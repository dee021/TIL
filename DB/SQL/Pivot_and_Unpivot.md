## PIVOT절과 UNPIVOT 절

PIVOT 절은 행을 열로 회전시키고, UNPIVOT 절은 열을 행으로 회전시킨다.

FROM 절과 ORDER BY 절 사이에 기술한다.

### PIVOT 절
`PIVOT`은 집계를 수행하고 가능한 여러 행을 단일 행으로 병합한다.


```sql
PIVOT [XML]
(
    <aggregation function> ( <expr> )
FOR <column that contains the values that become column headers>
    IN ( <first column>, <second column>, ... <last column> )
) AS <alias for the pivot table>
[ ORDER BY ... ];
```
- \<aggregation function> : 집계할 열을 지정
- FOR 절 : PIVOT할 열을 지정
- IN 절 : PIVOT할 열의 값을 지정한다.
  - `SQL Server`
    - 숫자형은 " ", [ ] 안에 넣어 필드명을 명시한다
    - 문자열은 " ", [ ] 안에 넣거나 그냥 명시한다.
  - `Oracle`
    - 숫자형은 ' ' 안에 넣거나 그냥 명시한다.
    - 문자열은 ' ' 안에 넣어 명시한다.
- `SQL Server` : PIVOT TABLE에 대한 ALIAS를 필수로 지정해야 한다. / `Oracle` : PIVOT TABLE에 대한 ALIAS를 붙이는 것이 syntax error


[EX]
```SQL
SELECT * FROM (SELECT job, deptno, sal FROM emp)
	PIVOT (SUM (sal) FOR deptno IN (10,20,30))
	ORDER BY 1;
```
![alt text](/DB/SQL/img/pivot1.png)


[`SQL Server` EX]
```sql
SELECT * FROM (SELECT job, deptno, sal FROM emp) e
	PIVOT (SUM (sal) FOR deptno IN ([10],[20],[30])) AS sal_pivot
	ORDER BY 1;


SELECT * FROM (SELECT job, deptno, sal FROM emp) e
	PIVOT (SUM (sal) FOR deptno IN ("10", "20", "30")) AS sal_pivot
	ORDER BY 1;
```
PIVOT 절은 집계함수와 FOR 절에 지정되지 않은 열을 기준으로 집계된다.

```SQL
SELECT * FROM (SELECT DATEPART(YEAR, hiredate) AS yyyy, job, deptno, sal FROM emp) e
	PIVOT (SUM (sal) FOR deptno IN ("10", "20", "30")) AS sal_pivot
	ORDER BY 2;
```
![alt text](/DB/SQL/img/pivot2.png)

<hr>

`Oracle` 집계함수와 IN 절에 ALIAS를 부여할 수 있다.

```SQL
SELECT * FROM (SELECT job, deptno, sal FROM emp)
	PIVOT (SUM (sal) as sal 
        FOR deptno IN (10,20,30,50))
	ORDER BY 1;
```
![alt text](/DB/SQL/img/pivotAlias1.png)

```sql
SELECT job, d20_sal FROM (SELECT job, deptno, sal FROM emp)
	PIVOT (SUM (sal) as sal FOR deptno IN (10 as d10,20 as d20,30 as d30,50 as d50))
    WHERE d20_sal > 2500
	ORDER BY 1;
```

![alt text](/DB/SQL/img/pivotAlias2.png)

`SQL Server`는 다음과 같이 컬럼에 별칭을 붙일 수 있다.

```sql
SELECT job, [10] AS d10, [20] AS d20, [30] AS d30
	FROM (SELECT job, deptno, sal FROM emp) e
	PIVOT (SUM (sal) FOR deptno IN ([10], [20], [30])) AS sal_pivot
	ORDER BY 1;
```

![alt text](/DB/SQL/img/pivotAlias3.png)

```sql
SELECT job, [10] AS d10, [20] AS d20
	FROM (SELECT job, deptno, sal FROM emp) e
	PIVOT (SUM (sal) FOR deptno IN ([10], [20], [30])) AS sal_pivot
	where [20] > 2500
	ORDER BY 1;
```

![alt text](/DB/SQL/img/pivotAlias4.png)

<hr>

`Oracle` PIVOT 절은 다수의 집계함수를 지원한다.

```sql
SELECT * FROM (SELECT job, deptno, sal FROM emp) e
	PIVOT (SUM (sal) as sal, COUNT(*) as cnt FOR deptno IN (10, 20))
	ORDER BY 1;
```

![alt text](/DB/SQL/img/pivot3.png)

FOR 절에도 다수의 열을 기술할 수 있다.

```SQL
SELECT * FROM (SELECT TO_CHAR (hiredate, 'YYYY') AS yyyy, job, deptno, sal FROM emp)
    PIVOT (SUM (sal) AS sal, COUNT (*) AS cnt 
    FOR (deptno, job) IN ((10, 'ANALYST') AS d10a, (10, 'CLERK') AS d10c,
                          (20, 'ANALYST') AS d20a, (20, 'CLERK') AS d20c))
    ORDER BY 1;
```

![alt text](/DB/SQL/img/pivot4.png)

<hr>
PIVOT 절을 사용할 수 없는 경우 집계함수와 CASE 표현식으로 PIVOT을 수행할 수 있다.

```SQL
SELECT job,
    SUM (CASE deptno WHEN 10 THEN sal END) AS d10_sal,
    SUM (CASE deptno WHEN 20 THEN sal END) AS d20_sal,
    SUM (CASE deptno WHEN 30 THEN sal END) AS d30_sal
    FROM emp
    GROUP BY job
    ORDER BY job;
```

![alt text](/DB/SQL/img/pivot5.png)

```sql
SELECT job,
    SUM (CASE deptno WHEN 10 THEN sal END) AS d10_sal,
	COUNT (CASE deptno WHEN 10 THEN job END) AS d10_cnt,
    SUM (CASE deptno WHEN 20 THEN sal END) AS d20_sal,
	COUNT (CASE deptno WHEN 20 THEN job END) AS d20_cnt
    FROM emp 
	WHERE job IN ('ANALYST', 'CLERK')
	GROUP BY job
    ORDER BY job;
```

![alt text](/DB/SQL/img/pivot6.png)

### UNPIVOT 절

[Syntax]

```
UNPIVOT [ { INCLUDE | EXCLUDE } NULLS ]
(
    <new output column name>
    FOR <column for names of the pivot columns>
    IN ( <first column>, <second column>
         , ... <last column> )
)
[ ORDER BY ... ];
```

- SQL Server의 경우 PIVOT절과 마찬가지로 UNPIVOT TABLE에 대한 ALIAS를 지정해야 한다.

<hr>

UNPIVOT 절 사용을 위한 테이블 T1 생성

[Oracle]
```sql
CREATE TABLE T1 AS
SELECT job, d10_sal, d20_sal, d10_cnt, d20_cnt
    FROM (SELECT job, deptno, sal FROM emp WHERE job IN ('ANALYST', 'CLERK'))
    PIVOT (SUM (sal) AS sal, COUNT (*) AS cnt FOR deptno IN (10 AS d10, 20 AS d20));

SELECT * FROM t1;
```

[SQL Server]

```sql
SELECT job,
    SUM (CASE deptno WHEN 10 THEN sal END) AS d10_sal,
	COUNT (CASE deptno WHEN 10 THEN job END) AS d10_cnt,
    SUM (CASE deptno WHEN 20 THEN sal END) AS d20_sal,
	COUNT (CASE deptno WHEN 20 THEN job END) AS d20_cnt
	INTO t1
    FROM emp 
	WHERE job IN ('ANALYST', 'CLERK')
	GROUP BY job
	ORDER BY job;

    SELECT * FROM t1;
```

![alt text](/DB/SQL/img/tableT1.png)

[Oracle]
```SQL
SELECT job, deptno, sal FROM t1
    UNPIVOT (sal FOR deptno IN (d10_sal, d20_sal))
    ORDER BY 1,2;
```

[SQL Server]
```sql
SELECT job, deptno, sal FROM t1
	UNPIVOT (sal FOR deptno IN (d10_sal, d20_sal)) AS unpvt
	ORDER BY 1,2;
```

![alt text](/DB/SQL/img/unpivot1.png)

기존의 d10_sal, d20_sal 컬럼을 deptno라는 컬럼명으로 묶고 sal이라는 컬럼명으로 컬럼값을 행으로 전치했다.


<hr>

`Oracle` `INCLUDE NULLS` 키워드를 이용하면 열의 값이 NULL인 행도 결과에 포함된다.

```SQL
SELECT job, deptno, sal FROM t1
    UNPIVOT INCLUDE NULLS (sal FOR deptno IN (d10_sal, d20_sal))
    ORDER BY 1,2;
```

![alt text](/DB/SQL/img/unpivotIncludeNull1.png)



<hr>


`Oracle` FOR절에 다수의 열과 IN 절에 Alias를 지정할 수 있다.

- IN절에 ALIAS 지정

```sql
SELECT job, deptno, sal FROM t1
    UNPIVOT (sal FOR deptno IN (d10_sal AS 10, d20_sal AS 20))
    ORDER BY 1,2;
```

![alt text](/DB/SQL/img/unpivot2.png)

- IN 절에 다수의 값에 단일 ALIAS를 지정하여 병합

```SQL
SELECT * FROM t1 
    UNPIVOT ((sal, cnt) FOR deptno IN ((d10_sal, d10_cnt) AS 10, (d20_sal, d20_cnt) AS 20))
    ORDER BY 1,2;
```

![alt text](/DB/SQL/img/unpivot3.png)

- FOR 절에 다수의 열, IN 절에 다수의 ALIAS 지정

```sql
SELECT job, deptno, dname, sal FROM t1 
    UNPIVOT (sal FOR (deptno, dname) IN ((d10_sal) AS (10, 'ACCOUNT'), (d20_sal) AS (20, 'RESEARCH'))) 
    ORDER BY 1,2;
```

![alt text](/DB/SQL/img/unpivot5.png)


```SQL
SELECT * FROM t1 
    UNPIVOT ((sal, cnt) FOR (deptno, dname) IN ((d10_sal, d10_cnt) AS (10, 'ACCOUNT'), (d20_sal, d20_cnt) AS (20, 'RESEARCH'))) 
    ORDER BY 1,2;
```

![alt text](/DB/SQL/img/unpivot4.png)


`SQL Server`의 경우 아래와 같이 CASE 문을 이용하여 값을 변경할 수 있다.

```sql
SELECT job, 
		CASE unpvt.deptno WHEN 'd10_sal' THEN 10 ELSE 20 END AS deptno,
		sal FROM t1
    UNPIVOT (sal FOR deptno IN (d10_sal, d20_sal))  AS unpvt
    ORDER BY 1,2;
```


<hr>

UNPIVOT 절을 사용할 수 없는 경우 카티션 곱을 이용하여 필요한 만큼 행을 늘려 CASE 문으로 값을 지정할 수 있다.

[SQL Server]

```SQL
SELECT a.job,
	CASE b.lv WHEN 1 THEN 10		 WHEN 2 THEN 20		   END AS deptno,
	CASE b.lv WHEN 1 THEN a.d10_sal WHEN 2 THEN a.d20_sal END AS sal,
	CASE b.lv WHEN 1 THEN a.d10_cnt WHEN 2 THEN a.d20_cnt END AS cnt
	FROM t1 a, (SELECT 1 AS LV UNION SELECT 2 AS lv) b
	ORDER BY 1, 2;
```

![alt text](/DB/SQL/img/unpivot6.png)

[Oracle]

```sql
SELECT a.job, 
    CASE b.lv WHEN 1 THEN 10        WHEN 2 THEN 20  END AS deptno,
    CASE b.lv WHEN 1 THEN a.d10_sal WHEN 2 THEN a.d20_sal END AS sal,
    CASE b.lv WHEN 1 THEN a.d10_cnt WHEN 2 THEN a.d20_cnt END AS cnt
    FROM t1 a, (SELECT LEVEL AS lv FROM DUAL CONNECT BY LEVEL <= 2) b 
    ORDER BY 1,2;
```

![alt text](/DB/SQL/img/unpivot7.png)
