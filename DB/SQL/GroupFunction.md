## Group Function
### 데이터 분석 개요
ANSI/ISO SQL 표준은 데이터 분석을 위해 다음 세 가지 함수를 정의하고 있다.

- (GROUP) AGGREGATE FUNCTION : COUNT, MIN, MAX, SUM, AVG 외 각종 집계 함수가 포함돼 있다.
- GROUP FUNCTION
- WINDOW FUNCTION : ANALYTIC FUNCTION나 RANK FUNCTION로도 알려졌다.

**GROUP FUNCTION**<BR>
결산 개념의 업무를 가지는 시스템의 경우 소계, 중계, 합계, 총합계 등 여러 레벨의 결산 보고서를 만드는 것이 중요 업무 중 하나이다. 그룹 함수를 사용하면 하나의 SQL로 테이블을 한 번만 읽어서 빠르게 원하는 리포트를 작성할 수 있다. 추가로 GROUPING 함수와 CASE 함수를 이용하면 원하는 포맷의 보고서 작성도 가능하다.

그룹 함수로는 집계함수를 제외하고, 소그룹 간의 소계를 계산하는 ROLLUP 함수, GROUP BY 항목 간 다차원적인 소계를 계산할 수 있는 CUBE 함수, 특정 항목에 대한 소계를 계산하는 GROUPING SETS 함수가 있다.<BR>
ROLLUP, CUBE, GRUOPING SETS 결과에 대한 정렬이 필요한 경우는 ORDER BY 절에 정렬 칼럼을 명시해야 한다.

### ROLLUP 함수
ROLLUP은 소그룹 간의 소계를 계산하는 함수로, GROUP BY의 확장된 형태로 사용하기 쉬우며 병렬로 수행할 수 있어 매우 효과적일 뿐 아니라 시간 및 지역처럼 계층적 분류를 포함하고 있는 데이터의 집계에 적합하다.

ROLLUP에 지정된 Grouping Columns의 List는 Subtotal을 생성하기 위해 사용되며, Grouping Columns의 수가 N일 때, N+1 Level의 Subtotal이 생성된다.

ROLLUP의 인수는 계층 구조이므로 인수 순서가 바뀌면 수행 결과도 바뀐다.

[GROUP BY 절을 사용한 소계]
```sql
SELECT b.dname, a.job, 
    COUNT (*) AS emp_cnt,
    SUM (a.sal) AS sal_sum 
FROM emp a, dept b
WHERE b.deptno = a.deptno
GROUP BY b.dname, a.job
ORDER BY b.dname, a.job;
```

![alt text](/DB/SQL/img/rollup1.png)

[ROLLUP 함수를 이용한 소계]
```sql
SELECT b.dname, a.job,
    COUNT (*) AS emp_cnt,
    SUM (a.sal) AS sal_sum
FROM emp a, dept b
WHERE b.deptno = a.deptno
GROUP BY ROLLUP (b.dname, a.job)
ORDER BY b.dname, a.job; -- SQL Server의 경우 NULL이 우선
```

![alt text](/DB/SQL/img/rollup2.png)

실행 결과에 추가 LEVEL의 집계가 생성된 것을 볼 수 있다.

L1 - GROUP BY 수행 시 생성되는 표준 집계(9건)<br>
L2 - DNAME 별 모든 JOB의 SUBTOTAL(3건)<br>
L3 - GRAND TOTAL(1건)

> <B>ORDER BY</B><br>
> ROLLUP의 경우 계층 간 집계에 대해서 LEVEL별 순서를 정렬하지만 계층 내 GROUP BY 수행 시 생성되는 표준 집계에는 별도의 정렬을 지원하지 않는다.


[GROUPING 함수 사용]
```SQL
SELECT b.dname, GROUPING (b.dname) AS dname_grp,
    a.job, GROUPING (a.job) AS job_grp,
    COUNT (*) AS emp_cnt, SUM (a.sal) AS sal_sum
FROM emp a, dept b
WHERE b.deptno = a.deptno
GROUP BY ROLLUP (b.dname, a.job)
ORDER BY b.dname, a.job;
```

![alt text](/DB/SQL/img/rollup3.png)

**GROUPING 함수**<BR>
<UL>
<LI>ROLLUP이나 CUBE에 의한 소계가 계산된 결과에는 GROUPING(EXPR) = 1이 표시된다.
<LI>그 외의 결과에는 GRUOPING(EXPR) = 0이 표시된다.
</UL>

GROUPING 함수와 CASE/`Oracle`DECODE를 이용해 소계를 나타내는 필드에 원하는 문자열을 지정할 수 있다.

[GROUPING 함수 + CASE 이용]

```SQL
SELECT CASE GROUPING (b.dname) WHEN 1 THEN 'All Departments' ELSE b.dname END AS DNAME,
    CASE GROUPING (a.job) WHEN 1 THEN 'All Jobs' ELSE a.job END AS JOB,
    COUNT (*) AS EMP_CNT, SUM (a.sal) AS SAL_SUM
FROM emp a, dept b
WHERE b.deptno = a.deptno
GROUP BY ROLLUP (b.dname, a.job)
ORDER BY b.dname, a.job;
```

![alt text](/DB/SQL/img/rollup4.png)

<details>
<summary>[GROUPING 함수 + DECODE 이용]</summary>

```SQL
SELECT DECODE (GROUPING (b.dname), 1, 'All Departments', b.dname) AS DNAME,
    DECODE (GROUPING (a.job), 1, 'All Jobs', a.job) AS JOB,
    COUNT (*) AS EMP_CNT, SUM (a.sal) AS SAL_SUM
FROM emp a, dept b
WHERE b.deptno = a.deptno
GROUP BY ROLLUP (b.dname, a.job)
ORDER BY b.dname, a.job;
```

</details>

### CUBE 함수

CUBE는 결합 가능한 모든 값에 대해 다차원적인 집계를 생성한다. CUBE를 사용할 때는 내부적으로 <sup>1</sup>Grouping Columns의 순서를 바꾸어서 또 한 번의 쿼리를 추가 수행해야 한다. <sup>2</sup>Grand Total은 양쪽의 쿼리에서 모두 생성되므로 한 번의 쿼리에서는 제거해야 한다. ROLLUP에 비해 다양한 데이터를 얻는 장점이 있는 반면, 시스템에 부하를 많이 주는 단점이 있다.

CUBE 함수의 경우 표시된 인수들에 대한 계층별 집계를 구할 수 있으며, Grouping Columns의 수가 N일 때, 2의 N승 LEVEL의 Subtotal을 생성한다. 표시된 인수는 평등 관계이므로 정렬 순서는 바뀔 수 있어도 데이터 결과는 같다.

[CUBE 함수]

```sql
SELECT CASE GROUPING (b.dname) WHEN 1 THEN 'All Departments' ELSE b.dname END AS DNAME,
    CASE GROUPING (a.job) WHEN 1 THEN 'All Jobs' ELSE a.job END AS JOB,
    COUNT (*) AS EMP_CNT, SUM (a.sal) AS SAL_SUM
FROM emp a, dept b
WHERE b.deptno = a.deptno
GROUP BY CUBE (b.dname, a.job)
ORDER BY b.dname, a.job;
```

![alt text](/DB/SQL/img/cube1.png)


[UNION ALL 사용]

```SQL
SELECT dname, job, COUNT (*) AS emp_cnt, SUM (sal) AS sal_sum FROM emp a, dept b 
WHERE b.deptno = a.deptno GROUP BY dname, job
UNION ALL
SELECT dname, 'All Jobs' AS job, COUNT (*) AS emp_cnt, SUM (sal) AS sal_sum FROM emp a, dept b
WHERE b.deptno = a.deptno GROUP BY dname
UNION ALL
SELECT 'All Departments' AS dname, job, COUNT(*) AS emp_cnt, SUM (sal) AS sal_sum FROM emp a, dept b
WHERE b.deptno = a.deptno GROUP BY job
UNION ALL
SELECT 'All Departments' AS dname, 'All Jobs' AS job, COUNT (*) emp_cnt, SUM (sal) AS sal_sum FROM emp a, dept b
WHERE b.deptno = a.deptno;
```

![alt text](/DB/SQL/img/cube2.png)

정렬 순서는 다르나 같은 결과 데이터를 도출한다.

CUBE 함수를 사용하면서 가장 크게 개선되는 부분은 EMP, DEPT 테이블을 반복 액세스하는 부분을 한 번으로 줄일 수 있는 부분이다. 결과적으로 수행속도 및 자원 사용률을 개선할 수 있으며, SQL 문장의 가독성도 높아졌다. 

### GROUPING SETS

GROUPING SETS는 원하는 부분의 소계만 손쉽게 추출할 수 있다. GROUPING SETS에 표시된 인수들에 대한 개별 집계를 구할 수 있으며, 이때 표시된 인수는 평등 관계이므로 인수의 순서가 바뀌어도 결과는 같다.

[일반 그룹 함수 이용]

```SQL
SELECT dname, 'All Jobs' AS job, COUNT (*) AS emp_cnt, SUM (sal) AS sal_sum FROM emp a, dept b
WHERE b.deptno = a.deptno GROUP BY dname
UNION ALL
SELECT 'All Departments' AS dname, job, COUNT (*) AS emp_cnt, SUM (sal) AS sal_sum FROM emp a, dept b
WHERE b.deptno = a.deptno GROUP BY job;
```

![alt text](/DB/SQL/img/groupingSets1.png)

[GROUPING SETS 사용]
```SQL
SELECT CASE GROUPING (b.dname) WHEN 1 THEN 'All Departments' ELSE b.dname END AS dname,
    CASE GROUPING (a.job) WHEN 1 THEN 'All Jobs' ELSE a.job END AS job,
    COUNT (*) AS emp_cnt, SUM (a.sal) AS sal_sum
FROM emp a, dept b
WHERE b.deptno = a.deptno
GROUP BY GROUPING SETS (b.dname, a.job);
```

![alt text](/DB/SQL/img/groupingSets2.png)

정렬 순서는 다르나 같은 결과 집합을 도출한다.
