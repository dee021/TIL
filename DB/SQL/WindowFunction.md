## Window Function
기존 관계형 데이터베이스는 행과 행간의 관계를 정의하거나 행과 행간을 비교/연산하는 것을 하나의 SQL 문으로 처리하는 것은 매우 어려운 문제였다. 절차형 프로그램을 작성하거나 INLINE VIEW를 이용해 복잡한 SQL 문을 작성해야 하던 것을 부분적이나마 행과 행간의 관계를 쉽게 정의하기 위해 만든 함수가 Window Function이다.

Analytic Function, Rank Function으로도 알려진 Window Function은 데이터 웨어하우스에서 발전한 기능이다. 

Window Function은 기존에 사용하던 집계함수도 있고, 새로이 윈도우 함수 전용으로 만들어진 기능도 있다. 윈도우 함수는 중첩(NEST)해서 사용하지는 못하지만, 서브 쿼리에서는 사용할 수 있다.

**윈도우 함수 종류**<br>
윈도우 함수의 종류는 크게 5개의 그룹으로 분류할 수 있는데 벤더별로 지원하는 함수에 차이가 있다.

1. 그룹 내 순위(RANK) 관련 함수 : RANK, DENSE_RANK, ROW_NUMBER가 있다. ANSI/ISO SQL 표준과 Oracle, SQL Server 등 대부분의 DBMS에서 지원한다.
2. 그룹 내 집계(AGGREGATE) 관련 함수 : 일반적으로 많이 사용하는 SUM, MAX, MIN, AVG, COUNT가 있다. ANSI/ISO SQL 표준과 Oracle, SQL Server 등 대부분의 DBMS에서 지원하고 있다. SQL Server의 경우 OVER 절 내의 ORDER BY 구문을 지원하지 않는다.
3. 그룹 내 행 순서 관련 함수 : FIRST_VALUE, LAST_VALUE, LAG, LEAD가 있다. Oracle에서만 지원하는 함수이지만, FIRST_VALUE, LAST_VALUE 함수는 MAX, MIN 함수와 비슷한 결과를 얻을 수 있다. 
4. 그룹 내 비율 관련 함수 : CUME_DIST, PERCENT_RANK, NTILE, RATIO_TO_REPORT가 있다. CUME_DIST, PERCENT_rANK 함수는 ANSI/ISO SQL 표준과 Oracle, SQL Server에서 지원한다. NTILE 함수는 ANSI/ISO 표준에는 없지만 Oracle과 SQL Server에서 지원한다. RATIO_TO_REPORT 함수는 Oracle에서만 지원한다.
5. 선형 분석을 포함한 통계 분석 관련 함수가 있다.

### 윈도우 함수 문법
윈도우 함수에는 OVER 문구가 키워드로 필수 포함된다.

```SQL
SELECT WINDOW_FUNCTION (ARGUMENTS) OVER ([PARTITION BY 칼럼] [ORDER BY 절] [WINDOWING 절]) FROM 테이블명;
```

- WINDOW_FUNCTION : 기존에 사용하던 함수도 있고, 새롭게 WINDOW 함수용으로 추가된 함수도 있다.
- ARGUMENTS : 함수에 따라 0 ~ N 개의 인수가 지정될 수 있다.
- PARTITION BY 절 : 전체 집합을 기준에 의해 소그룹으로 나눌 수 있다.
- ORDER BY 절 : 어떤 항목에 대해 순위를 지정할지 ORDER BY 절을 기술한다.
- WINDOWING 절 : WINDOWING 절은 함수의 대상이 되는 행 기준의 범위를 강력하게 지정할 수 있다. <del>SQL Server에서는 지원하지 않는다.</del> SQL Server 2012(11.x)이상 버전에서 제한적으로 지원 `range 범위 지정 제한 있음`

[WINDOWING 절]
```
[BETWEEN 사용 타입]
ROWS | RANGE BETWEEN UNBOUNDED PRECEDING | CURRENT ROW | VALUE_EXPR PRECEDING/FOLLOWING
                AND UNBOUNDED FOLLOWING | CURRENT ROW | VALUE_EXPR PRECEDING/FOLLOWING

[BETWEEN 미사용 타입]
ROWS | RANGE UNBOUNDED PRECEDING | CURRENT ROW | VALUE_EXPR PRECEDING
```

**WINDOWING 절**<BR>

[BETWEEN 사용 타입]

```
ROWS | RANGE BETWEEN UNBOUNDED PRECEDING | CURRENT ROW | VALUE_EXPR PRECEDING/FOLLOWING
                AND UNBOUNDED FOLLOWING | CURRENT ROW | VALUE_EXPR PRECEDING/FOLLOWING
```

[BETWEEN 미사용 타입]
```
ROWS | RANGE UNBOUNDED PRECEDING | CURRENT ROW | VALUE_EXPR PRECEDING
```

- ROWS | RANGE : 파티션 내의 시작점 및 끝점을 지정하여 파티션 내의 행을 제한
  - ROWS 절은 현재 행 이전 또는 다음의 고정 행 수를 지정하여 파티션 내의 행 수를 제한
  - RANGE 절은 현재 행의 값을 기준으로 행 범위를 지정하여 파티션 내의 행 수를 제한
- UNBOUNDED PRECEDING : 창이 파티션의 첫 번째 행에서 시작하도록 정의
- CURRENT ROW : 창이 현재 행 또는 현재 값에서 시작되거나 끝나도록 지정
- UNBOUNDED FOLLOWING : 창이 파티션의 마지막 행에서 끝나도록 지정

예시
```sql
SUM (sal) OVER (ROWS UNBOUNDED PRECEDING) -- 첫 행부터 현재 행까지의 sal 누적 합

SUM (sal) OVER (RANGE BETWEEN 100 PRECEDING AND 200 FOLLWING) -- 현재 행의 sal 값을 기준으로 sal - 100 ~ sal + 200인 값들의 합 
```

### 그룹 내 순위 함수
#### RANK FUNCTION
- RANK는 ORDER BY를 포함한 쿼리문에서 특정 칼럼에 대한 순위를 구하는 함수
- 전체 데이터 또는 특정 범위(PARTITION) 내에서 순위를 구할 수 있다.
- 동일한 값에 대하여 같은 순위를 부여한다.

```SQL
SELECT job, ename, sal,
    RANK () OVER (ORDER BY sal DESC) AS all_rank,
    RANK () OVER (PARTITION BY job ORDER BY sal DESC) AS job_rank
FROM emp ORDER BY 4;
```

![alt text](/DB/SQL/img/rank1.png)

#### DENSE_RANK FUNCTION
- RANK 함수와 비슷하나 동일한 순위를 하나의 건수로 취급한다.

```SQL
SELECT job, ename, sal,
    RANK () OVER (ORDER BY sal DESC) AS rank1,
    DENSE_RANK () OVER (ORDER BY sal DESC) AS dense
FROM emp;
```

![alt text](/DB/SQL/img/dense_rank.png)

#### ROW_NUMBER FUNCTION
- 동일한 값들을 포함하여 고유한 순위를 부여한다.
  - 동일한 값에 대한 순서를 지정하려면 OVER 절 내의 ORDER BY에 추가 칼럼을 지정해야 한다.

```sql
SELECT job, ename, sal,
    RANK () OVER (ORDER BY sal DESC) AS rank1,
    ROW_NUMBER () OVER (ORDER BY sal DESC) AS row_number
FROM emp;
```

![alt text](/DB/SQL/img/row_number.png)

### 일반 집계함수
**SUM**<BR>

```SQL
SELECT mgr, ename, sal,
    SUM (sal) OVER (PARTITION BY mgr) AS sal_sum -- mgr 칼럼을 기준으로 sal 칼럼 합계
FROM emp; 
```

```SQL
SELECT mgr, ename, sal,
    SUM (sal) OVER (PARTITION BY mgr ORDER BY sal RANGE UNBOUNDED PRECEDING) AS sal_sum -- mgr 칼럼을 내에서의 누적합
FROM emp; 
```

![alt text](/DB/SQL/img/sum1.png)

- mgr = 7839는 정상적으로 누적합이 출력되었으나 mgr = 7698의 경우 같은 순위로 취급하여 3450(= 1250 * 2)가 출력됨

<hr>

**MAX/MIN**<BR>
MAX/MIN 함수를 이용하여 파티션별 윈도우의 최댓값/최소값을 구할 수 있다.

```SQL
SELECT mgr, ename, sal,
    MAX (sal) OVER (PARTITION BY mgr) AS max_sal
FROM EMP;
```

INLINE VIEW를 이용해서 파티션별 최댓값인 행만 추출할 수 있다.

```sql
SELECT mgr, ename, sal
FROM 
(
	SELECT mgr, ename, sal,
		MAX (sal) OVER (PARTITION BY mgr) AS max_sal
	FROM emp
)  -- nest roop 
WHERE sal = max_sal; 


SELECT mgr, ename, sal
FROM 
(
	SELECT mgr, ename, sal,
		RANK () OVER (PARTITION BY mgr ORDER BY sal DESC) AS sal_rk
	FROM emp
) a
WHERE sal_rk = 1;
```

[Inline View에 MAX 이용]
![alt text](/DB/SQL/img/window_inline1.png)

[Inline View에 RANK 이용]
![alt text](/DB/SQL/img/window_inline2.png)


**AVG**<br>
AVG 함수와 파티션별 ROWS 윈도우를 이용해 원하는 조건에 맞는 데이터에 대한 통곗값을 구할 수 있다.

```sql
SELECT mgr, ename, hiredate, sal,
    ROUND (AVG (sal) OVER (PARTITION BY mgr ORDER BY hiredate 
    ROWS BETWEEN 1 PRECEDING AND 1 FOLLOWING), 1) AS avg_sal
FROM emp;
```

![alt text](/DB/SQL/img/window_avg.png)


**COUNT**<BR>
COUNT 함수와 파티션별 ROWS 윈도우를 이용해 원하는 조건에 맞는 데이터에 대한 통곗값을 구할 수 있다.

```SQL
SELECT ename, sal,
    COUNT (*) OVER (ORDER BY sal 
    RANGE BETWEEN 50 PRECEDING AND 150 FOLLOWING) AS emp_cnt
FROM emp;
```

> SQL Server의 RANGE는 UNBOUNDED, CURRENT ROW만 지원

### 그룹 내 행 순서 함수
**FIRST_VALUE/LAST_VALUE**<br>
FIRST_VALUE/LAST_vALUE 함수를 이용해 파티션별 윈도우에서 가장 먼저/나중에 나온 값을 구한다.

```SQL
SELECT deptno, ename, sal,
    FIRST_VALUE (ename) OVER (PARTITION BY deptno ORDER BY sal DESC
    ROWS UNBOUNDED PRECEDING) AS ename_fv
FROM emp;
```

![alt text](/DB/SQL/img/firstvalue.png)

같은 부서의 같은 임금을 받는 FORD와 SCOTT 중 무엇이 결과로 나올지는 SQL 문만 갖고 판단할 수 없다. FIRST_vALUE는 공동 등수를 인정하지 않고 처음 나온 행만 처리한다.

**LAG**<br>
LAG 함수를 이용해 파티션별 윈도우에서 이전 몇 번째 행의 값을 가져올 수 있다.

[syntax]
```sql
LAG (scalar_expression [ , offset ] [ , default ] ) [ IGNORE NULLS | RESPECT NULLS ]
    OVER ( [ partition_by_clause ] order_by_clause )
```
- offset : (default value = 1) 몇 번째 이전행에서 데이터를 가져올지 지정
- defualt : offset이 유효한 범위를 벗어날 때의 반환값


```SQL
SELECT ename, hiredate, sal,
    LAG (sal) OVER (ORDER BY hiredate) AS lag_sal
FROM emp WHERE job = 'SALSEMAN';
```

![alt text](/DB/SQL/img/lag1.png)

```sql
SELECT ename, hiredate, sal,
    LAG (sal, 2, 0) OVER (ORDER BY hiredate) AS lag_sal
FROM emp WHERE job = 'SALESMAN';
``` 

![alt text](/DB/SQL/img/lag2.png)

**LEAD**<BR>
LEAD 함수를 이용해 파티션별 윈도우에서 이후 몇 번째 행의 값을 가져올 수 있다.


```SQL
SELECT ename, hiredate,
    LEAD (hiredate, 1) OVER (ORDER BY hiredate) AS lead_hiredate
FROM emp WHERE job = 'SALESMAN';
```

### 그룹 내 비율 함수
#### RATIO_TO_REPORT
RATIO_TO_REPORT 함수를 이용해 파티션 내 전체 SUM (칼럼) 값에 대한 행별 칼럼 값의 백분율을 소수점으로 구할 수 있다.<br>
결과값은 구간 (0, 1]의 범위를 가지며, 개별 RATIO의 합을 구하면 1이 된다.

[Oracle]
```sql
SELECT ename, sal,
	ROUND ( RATIO_TO_REPORT (sal) OVER (), 2) AS sal_ratio
FROM emp WHERE job = 'SALESMAN';
```

SQL Server에서는 지원하지 않는다. 아래와 같이 구할 수 있다.

```sql
SELECT ename, sal,
	ROUND ( sal * 100 / SUM ( sal ) OVER (), 2) AS sal_ratio
FROM emp WHERE job = 'SALESMAN';
```

#### PERCENT_RANK
PERCENT_RANK 함수를 이용해 함수별 윈도우에서 제일 먼저 나오는 것을 0으로, 제일 늦게 나오는 것을 1로 해, 행의 순서별 백분율을 구한다.<BR>
결과 값은 폐구간 [0, 1]의 범위를 가진다.

```SQL
SELECT deptno, ename, sal,
    PERCENT_RANK () OVER (PARTITION BY deptno ORDER BY sal DESC) AS prank
FROM emp;
```

![alt text](/DB/SQL/img/percentRank1.png)

- detpno = 10인 경우 3건이므로 구간은 2개다.
  - 0과 1 사이를 2개의 구간으로 나누면 0, 0.5, 1이다.
- detpno = 20인 경우 5건이므로 구간은 4개다.
  - 0과 1 사이를 4개의 구간으로 나누면 0, 0.25, 0.5, 0.75, 1이다.
  - FORD와 SCOTT의 경우 `ORDER BY sal DESC` 구문에 의해 급여가 같으므로 같은 ORDER로 취급한다.
- detpno = 30인 경우 6건이므로 구간은 5개다.
  - 0과 1 사이를 5개의 구간으로 나누면 0, 0.2, 0.4, 0.6, 0.8, 1이다.
  - MARTIN과 WARD의 경우 `ORDER BY sal DESC` 구문에 의해 급여가 같으므로 같은 ORDER로 취급한다.

#### CUME_DIST
CUME_DIST 함수를 이용해 파티션별 윈도우의 전체 건수에서 현재 행보다 작거나 같은 건수에 대한 누적백분율을 구한다.<BR>
결과 값은 구간 (0, 1]의 범위를 가진다.


```SQL
SELECT deptno, ename, sal,
    CUME_DIST () OVER (PARTITION BY deptno ORDER BY sal DESC) AS cd
FROM emp;
```

![alt text](/DB/SQL/img/cumeDist1.png)


- detpno = 10인 경우 3건이므로 0.3333 단위의 간격을 가진다.
- detpno = 20인 경우 5건이므로 0.2000 단위의 간격을 가진다.
- detpno = 30인 경우 6건이므로 0.1667 단위의 간격을 가진다.
- FORD와 SCOTT, MARTIN과 WARD의 경우 `ORDER BY sal DESC` 구문에 의해 급여가 같으므로 같은 ORDER로 취급한다.
  - 다른 WINDOW 함수의 경우 동일 순서이면 앞 행의 함수 결과 값을 따른다. CUME_DIST의 경우는 동일 순서면 뒤 행의 함수 결과 값을 기준으로 한다.

#### NTILE
NTILE 함수를 이용해 파티션별 전체 건수를 argument 값으로 N 등분한 결과를 구할 수 있다.

[syntax]
```sql
NTILE (argument) OVER ( [ <partition_by_clause> ] <order_by_clause> )
```

```sql
SELECT ename, sal,
    NTILE (4) OVER (ORDER BY sal DESC) AS nt
FROM emp;
```

![alt text](/DB/SQL/img/ntile1.png)

- 결과 행 수(=14)를 4등분하므로 4 + 4 + 3 + 3건씩 그룹핑한다. 2(= 14 % 4)건은 앞부터 할당한다.
