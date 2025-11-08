## 계층형 질의
### `Oracle` 계층형 질의
<meta url="https://docs.oracle.com/html/E26088_02/queries003.htm">

DFS 방식으로 탐색한다.

[syntax]
```sql
SELECT ...
    FROM table1
    WHERE condition ...
START WITH condition AND condition... 
CONNECT BY [NOCYCLE] condition AND contidion ...
[ORDER SIBLINGS BY column, column, ...]
```

- START WITH절 : 계층 구조 전개의 시작 위치를 지정하는 구문으로, 루트 데이터를 지정한다(액세스).
  - CONNECT BY절에 기술하거나 생략이 가능하다.
- CONNECT BY절 : 다음에 전개될 자식 데이터를 지정하는 구문이다. 자식 데이터는 CONNECT BY절에 주어진 조건을 만족해야 한다(조인).
- PRIOR : CONNECT BY절에 사용되며, 현재 읽은 칼럼을 지정한다.
  - `(FK) = PRIOR (PK)` : 부모 데이터에서 자식 데이터 방향으로 전개하는 순방향 전개를 한다.
  - `(PK) = PRIOR (FK)` : 자식 데이터에서 부모 데이터 방향으로 전개하는 역방향 전개를 한다.
- NOCYCLE : 데이터를 전개하면서 Cycle이 발생한 데이터는 런타임 오류가 발생한다. NOCYCLE를 추가하면 오류를 발생시키지 않고 이후의 데이터를 전개하지 않는다.
- ORDER SIBLINGS BY : 형제 노트 사이에서 정렬을 수행한다.
- WHERE : 모든 전개를 수행한 후에 지정된 조건을 만족하는 데이터만 추출한다(필터링).


Oracle은 계층형 질의를 사용할 때 다음과 같은 Pseudo Column을 제공한다.


<table>
<thead><td>가상 칼럼</td><td>설명</td></thead>
<tr><td>LEVEL</td><td>루트 데이터이면 1부터 시작하여 계층 레벨을 의미</td></tr>
<tr><td>CONNECT_BY_ISLEAF</td><td>전재 과정에서 해당 데이터가 리프 데이터이면 1, 그렇지 않으면 0</td></tr>
<tr><td>CONNECT_BY_ISCYCLE</td><td>CYCLE 옵션을 사용했을 때만 사용할 수 있다. 전재 과정에서 자식을 갖는데, 해당 데이터가 조상으로 존재하면 1, 그렇지 않으면 0</td></tr>
</table>

Oracle은 계층형 질의를 사용할 때 사용자 편의성을 제공하기 위해 다음과 같은 함수를 제공한다.

<table>
<thead><td>함수</td><td>설명</td></thead>
<tr><td>SYS_CONNECT_BY_PATH</td><td>루트 데이터부터 현재 전개할 데이터까지의 경로를 표시<BR>

`SYNTAX.` SYS_CONNECT_BY_PATH(칼럼, 경로분리자)</td></tr>
<tr><td>CONNECT_BY_ROOT</td><td>현재 전개할 데이터의 루트 데이터를 표시한다.

`SYNTAX` CONNECT_BY_ROOT 칼럼
</td></tr>
</table>



[순방향 전개 vs 역방향 전개]
```SQL
SELECT LEVEL AS lv, empno, mgr,
    CONNECT_BY_ISLEAF AS isleaf
    FROM emp
START WITH mgr IS NULL
CONNECT BY mgr = PRIOR empno;
```

![alt text](/DB/SQL/img/o_hierarchical_query1.png)

- 'mgr = PRIOR empno'에 의해 관리자 -> 사원 방향(순방향) 전개

```sql
SELECT LEVEL AS LV, EMPNO, MGR,
    CONNECT_BY_ISLEAF AS ISLEAF
    FROM EMP
START WITH EMPNO = 7876
CONNECT BY EMPNO = PRIOR MGR;
```

- 'EMPNO = PRIOR MGR'에 의해 사원 -> 관리자(역방향) 전개

![alt text](/DB/SQL/img/o_hierarchical_query5.png)


[START WITH절 생략]

```sql
SELECT LEVEL AS lv, empno, mgr,
    CONNECT_BY_ISLEAF AS isleaf
    FROM emp
CONNECT BY mgr = PRIOR empno;
```

![alt text](/DB/SQL/img/o_hierarchical_query2.png)

모든 행을 읽으면서 해당 데이터를 루트로 하는 트리를 탐색한다.
- empno = 7902 부터 시작된 트리에서는 empno = 7369의 레벨은 2
- empno = 7566 부터 시작된 트리에서는 empno = 7369의 레벨은 3

즉, 모든 서브 트리를 탐색하며, emp 테이블의 데이터는 14건이나, 해당 결과 집합의 데이터 건수는 39건이다.

```sql
SELECT LEVEL AS lv, CONNECT_BY_ROOT EMPNO AS CONN_NO, empno, mgr,
    CONNECT_BY_ISLEAF AS isleaf
    FROM emp
CONNECT BY mgr = PRIOR empno;
```

![alt text](/DB/SQL/img/conn_by_root1.png)

- `CONNECT_BY_ROOT`는 해당 서브 트리의 루트 데이터의 칼럼을 출력


[ORDER SIBLINGS BY절 ]
```SQL
SELECT
    LEVEL AS LV, EMPNO, ENAME, MGR,
    CONNECT_BY_ISLEAF AS ISLEAF
    FROM EMP
START WITH MGR IS NULL
CONNECT BY MGR = PRIOR EMPNO;
```

![alt text](/DB/SQL/img/o_hierarchical_query3.png)


```SQL
SELECT LEVEL AS LV, EMPNO, ENAME, MGR,
    CONNECT_BY_ISLEAF AS ISLEAF
    FROM EMP
START WITH MGR IS NULL
CONNECT BY MGR = PRIOR EMPNO
ORDER SIBLINGS BY ENAME;
```

![alt text](/DB/SQL/img/o_hierarchical_query4.png)

'ORDER SIBLINGS BY ENAME' 조건에 의해 EMPNO = 7839의 자식 데이터 JONES, BLAKE, CLARK 데이터가 정렬됨

[SYS_CONNECT_BY_PATH]

```sql
SELECT CONNECT_BY_ROOT (empno) AS root_empno,
    SYS_CONNECT_BY_PATH (empno, '-') AS path,
    empno, mgr
    FROM emp
START WITH mgr IS NULL
CONNECT BY mgr = PRIOR empno;
```

![alt text](/DB/SQL/img/o_hierarchical_query6.png)

### `SQL Server` 계층형 질의

CTE(Common Table Expression)를 재귀 호출하여 계층 구조를 전개할 수 있다. BFS 방식 탐색한다.

[형식]
```sql
WITH CTE_NAME AS (
    -- Anchor Member
    SELECT ... FROM TABLE1 
    WHERE 최상위 데이터 조건

    UNION ALL

    -- Recursion Member
    SELECT ... 
    FROM TABLE1 T, CTE_NAME R
    WHERE 조인 조건
) SELECT .... FROM CTE_NAME;
```

재귀적 쿼리의 처리 과정은 다음과 같다.

1. CTE 식을 앵커 멤버와 재귀 멤버로 분할한다.
2. 앵커 멤버를 실행해 첫 번째 호출 또는 기본 결과 집합(= T<SUB>0</SUB>)을 생성한다.
3. T<SUB>i</SUB>는 입력으로 사용하고 T<SUB>i+1</SUB>은 출력으로 사용해 재귀 멤버를 실행한다.
4. 빈 집합이 반환될 때까지 3단계를 반복한다.
5. T<SUB>0</SUB>에서 T<SUB>n</SUB>까지의 UNION ALL 집합을 반환한다.


[예시]
```sql
WITH emp_anchor AS (
    -- Anchor Member
	SELECT ename, empno, mgr, 0 AS level
		FROM emp WHERE mgr IS NULL
	UNION ALL
    -- Recursion Member
	SELECT a.ename, a.empno, a.mgr, r.level + 1 AS level
		FROM emp a, emp_anchor r
		WHERE r.empno = a.mgr
) SELECT level, ename, empno, mgr FROM emp_anchor;
```

![alt text](/DB/SQL/img/m_hierarchial1.png)

DFS 방식 탐색 결과와 같이 정렬하려면 다음과 같이 가상 칼럼을 추가하여 정렬할 수 있다.

```sql
WITH emp_anchor AS (
    -- Anchor Member
	SELECT ename, empno, mgr, 0 AS level, CONVERT(VARCHAR(1000), empno) AS sort
		FROM emp WHERE mgr IS NULL
	UNION ALL
    -- Recursion Member
	SELECT a.ename, a.empno, a.mgr, r.level + 1 AS level, CONVERT(VARCHAR(1000), r.sort + '-' + CONVERT(VARCHAR(1000), a.empno)) AS sort
		FROM emp a, emp_anchor r
		WHERE r.empno = a.mgr
) SELECT level, ename, empno, mgr, sort 
    FROM emp_anchor
    ORDER BY sort;
```

![alt text](/DB/SQL/img/m_hierarchial2.png)
