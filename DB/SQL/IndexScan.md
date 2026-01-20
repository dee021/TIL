## 인덱스 스캔 방식
## Index Range Scan
- 인덱스 루트 블록에서 리프 블록까지 수직적으로 탐색한 후 리프 블록을 필요한 범위만 스캔하는 방식
- Index Range Scan이 가능하게 하려면 인덱스를 구성하는 선두 칼럼이 조건절에 사용되어야 한다.
- Index Range Scan 과정을 거쳐 생성된 결과 집합은 인덱스 칼럼 순으로 정렬된 상태
  - 이러한 특징을 잘 이용하면 sort order by 연산을 생략하거나 min/max 값이 빠르게 추출할 수 있다.
- 인덱스를 스캔하는 범위를 얼마만큼 줄일 수 있느냐, 테이블로 액세스하는 횟수를 얼마만큼 줄일 수 있느냐가 인덱스 튜닝의 핵심

## Index Ragne Scan Descending
- Index Range Scan과 동일한 스캔 방식
- 인덱스를 뒤에서부터 앞으로 스캔하여 내림차순 정렬 결과를 얻음

## Index Full Scan
- 수직적 탐색없이 인덱스 리프 블록을 처음부터 끝까지 수평적으로 탐색하는 방식
  - 실제로는 첫 번째 리프 블록에 접근하기 위한 수직적 탐색 발생
- 대개 데이터 검색을 위한 최적의 인덱스가 없을 때 차선으로 선택됨

`ex 1`

```sql
select * from emp where sal > 5000 order by ename;
```
`sal > 5000`과 같이 대부분의 레코드를 필터링하고 일부에 대해서만 테이블 액세스가 발생하는 경우라면 Table Full Scan보다 Index Full Scan이 효과를 발휘하는 전형적인 사례이다.<br>
그러나 Index Full Scan이 일어나는 경우는 적절한 인덱스가 없어 Index Range Scan 대신 차선책으로 선택된 경우이므로, 가능하다면 인덱스 구조를 조정하는 것이 좋다.

`ex 2`
```sql
select /*+ first_rows */ * from emp where sal > 1000 order by ename;
```

`sal > 1000`과 같이 대부분의 레코드가 조건을 충족하는 경우, 

I/O 관점
- Index Scan은 Single I/O 방식으로 잦은 테이블 액세스 발생
- Table Full Scan은 Multiblock I/O 방식

Case 1) `sal`이 선두 칼럼인 인덱스로 Index Range Scan 하는 경우
- 조건을 만족하는 대다수의 레코드에 대해 매번 테이블 액세스가 일어남
- range scan 종료 후 ename를 기준으로 sort 연산
- sort 연산 이후 첫 레코드 반환

Case 2) `ename`이 선두 칼럼인 인덱스로 Index Full Scan
- sort 연산을 고려하지 않음
- 옵티마이저 목적(first_rows)에 따라 조건을 만족하는 row 발견 후 즉시 반환하면 빠른 응답이 가능
- 결과를 바로 반환하지 않고 끝까지 fetch가 일어나면 대다수 레코드에 대해 테이블 액세스가 발생해서 오히려 성능 저하

Case 3) Table Full Scan
- Multiblock I/O로 데이터 연속 처리
- 조건을 충족하는 결과 집합에 대해 sort 연산 실행
- 결과 집합이 크고 전체 데이터를 끝까지 처리하는 경우 대용량 DB에 대해서 비교적 안정적인 성능을 보장

## Index Unique Scan
- 수직적 탐색만으로 데이터를 찾는 스캔 방식
- Unique Index를 동등 조건으로 탐색하는 경우에 작동

## Index Skip Scan
- Oracle 9i 버전부터 제공
- 조건절에 인덱스 선두 칼럼이 빠진 상황에서 인덱스를 활용하는 스캔 방식
- 루트 또는 브랜치 블록에서 읽은 칼럼 값 정보를 조건에 부합하는 레코드를 포함할 가능성이 있는 하위 블록만 골라서 액세스 하는 방식
- 인덱스 선두 칼럼의 Distinct Value 개수가 적고, 후행 칼럼의 Distinct Value가 많을 때 유용

`ex`

```sql
create table emp_test
as
select rownum empno,
decode(mod(rownum, 2), 0, 'M', 'F') gender,
mod(rownum*1100, 10000) sal
from dual
connect by level <= 100000;

create index idx_gender_sal_emp on emp_test(gender, sal);
```

```sql
-- 1
select * from emp_test where sal < 2000;

-- 2
select * from emp_test where gender in ('F', 'M') and sal < 2000;
```

1과 같이 인덱스 선두 칼럼이 조건절에 존재하지 않을때, 옵티마이저는 Table Full Scan 혹은 Index Skip Scan을 고려할 수 있다.

2와 같이 인덱스 선두 칼럼이 조건절에 명시되어 있을 때, `gender in ('F', 'M')`에 대하여 `sal < 2000` 조건에 대하여 Index Range Scan의 반복 수행(INLIST ITERATOR) 고려할 수 있다. 

<HR>

#### Index Skip Scan vs. In-List Range Scan<br>

Index Skip Scan
- 선두 칼럼의 Distinct Value 값 메타 정보를 참조
- 인덱스 액세스 계층에서 선두 칼럼 값마다 내부적으로 수직적 재탐색
- Cardinality 추정 불안정 

In-List Range Scan
- 브랜치 블록 재탐색은 Index Skip Scan와 동일하나 옵티마이저가 반복 구조를 명시적으로 생성
- Cardinality 추정 명확

## Index Fast Full Scan
- 인덱스 트리 구조를 무시하고 인덱스 세그먼트 전체를 Multiblock Read 방식으로 스캔

<b>Index Full Scan vs. Index Fast Full Scan </b><br>

1. 특징 비교 
<table>
<thead>
<th>Index Full Scan</th><th>Index Fast Full Scan</th>
</thead>
<tr>
<td>
<ul>
<li>인덱스 구조를 따라 스캔
<li>결과 집합 순서 보장
<li>Single Block I/O
<li>병렬스캔 불가(파티션 돼 있지 않다면)
<li>인덱스에 포함되지 않은 칼럼 조회 시에도 사용 가능 
</ul>
</td><td>
<ul>
<li>세그먼트 전체를 스캔
<li>결과 집합 순서 보장 안 됨
<li>Multiblock I/O
<li>병렬스캔 가능
<li>인덱스에 포함된 칼럼으로만 조회할 때 사용 가능
</ul></td>
</tr>
</table>

2. 사용 상황 비교
<table>
<thead>
<th>Index Full Scan</th><th>Index Fast Full Scan</th>
</thead>
<tr>
<td>
<ul>
<li>정렬이 필요할 때
<li>테이블 접근이 필요할 때(인덱스 칼럼만으로 조회 불가능)
<li>Top N 쿼리
</ul>
</td><td>
<ul>
<li>정렬 연산이 불필요
<li>테이블 접근 불필요(인덱스로 커버 가능한 상황)
</ul></td>
</tr>
</table>

<details>
<summary>쿼리 예시</summary>

- Top N 쿼리 + STOPKEY
```sql
SELECT *
FROM emp
ORDER BY ename
FETCH FIRST 10 ROWS ONLY;
```

- 정렬 연산 불필요
```SQL
SELECT COUNT(*)
FROM emp
WHERE sal > 1000;
```
</details>
