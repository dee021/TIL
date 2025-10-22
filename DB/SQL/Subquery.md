## Subquery

서브 쿼리는 하나의 SQL 문안에 포함돼 있는 또 다른 SQL 문이다.<br>메인 쿼리가 서브 쿼리를 포함하는 종속적인 관계이다.

서브 쿼리는 메인 쿼리의 칼럼을 모두 사용할 수 있지만, 메인 쿼리는 서브 쿼리의 칼럼을 사용할 수 없다. 질의 결과에 서브 쿼리 칼럼을 표시해야 한다면 조인 방식으로 변환하거나 함수, 스칼라 서브 쿼리(Scalar Subquery) 등을 사용해야 한다.

서브 쿼리는 서브 쿼리 레벨과는 상관없이 항상 메인 쿼리 레벨로 결과 집합이 생성된다. <br>
SQL 문에서 버스 쿼리 방식을 사용해야 할 못 잘못 판단해 조인 방식을 사용하는 경우가 있다. <br>
`ex.` 결과는 조직(1)레벨이고 사원(M) 테이블에서 체크해야 할 조건이 존재할 때, 조인을 사용하면 M(= 1 * M)레벨의 결과 집합이 생성되어, SQL문에 DISTINCT를 추가해 결과를 다시 조직 레벨로 만들어야 한다.

서브 쿼리를 사용할 때 주의사항
- 서브 쿼리는 괄호로 감싸서 기술한다.
- 서브 쿼리는 단일 행(Single row) 또는 복수 행(Multiple Row) 비교 연산자와 함께 사용 가능하다. 단일 행 비교 연산자는 서브 쿼리의 결과가 반드시 1건 이하이어야 하고, 복수 행 비교 연산자는 서브 쿼리의 결과 건수와 상관 없다.
- 중첩 서브 쿼리 및 스칼라 서브 쿼리에서는 ORDER BY를 사용하지 못한다.

[동작하는 방식에 따른 서브 쿼리 분류]
<table>
<tr><td>Un-Correlated 서브 쿼리</td><td>서브 쿼리가 메인 쿼리 칼럼을 갖고 있지 않는 형태의 서브 쿼리다. 메인 쿼리에 값(서브 쿼리가 실행된 결과)을 제공하기 위한 목적으로 주로 사용한다.</td></tr>
<tr><td>Collelated 서브 쿼리</td><td>서브 쿼리가 메인 쿼리 칼럼을 갖고 있는 형태의 서브 쿼리다. 일반적으로 메인 쿼리가 먼저 수행돼 읽혀진 데이터를 서브 쿼리에서 조건이 맞는지 확인하고자 할 때 주로 사용한다.</td></tr>
</table>

[반환되는 데이터의 형태에 따른 서브 쿼리 분류]
<table>
<tr><td>Single Row 서브 쿼리</td><td>서브 쿼리의 실행 결과가 항상 1건 이하인 서브 쿼리를 의미한다.</td></tr>
<tr><td>Multi Row 서브 쿼리</td><td>서브 쿼리의 실행 결과가 여러 건인 서브 쿼리의 의미한다.</td></tr>
<tr><td>Multi Column 서브 쿼리</td><td>서브 쿼리의 실행 결과로 여러 칼럼을 반환한다. 메인 쿼리의 조건절에 여러 칼럼을 동시에 비교할 수 있다. 서브 쿼리와 메인 쿼리에서 비교하고자 하는 칼럼 개수와 칼럼의 위치가 동일해야 한다.</td></tr>
</table>

### Single Row Subquery
단일 행 서브 쿼리는 단일 행 비교 연산자(=, <, >, <=, >=, <> 등)와 함께 사용되며, 서브 쿼리의 결과 건수는 반드시 1건 이하이어야 한다. 서브 쿼리의 결과 건수가 2건 이상을 반환하면 SQL 문은 Run Time Error가 발생한다. 

```sql
SELECT player_name, position, back_no FROM player
WHERE team_id = (SELECT tem_id FROM player WHERE plyer_name = '정남일')
ORDER BY player_name;
```

서브 쿼리가 먼저 수행돼 선수의 소속팀ID가 반환된다. 메인 쿼리는 서브 쿼리에서 반환된 결과를 이용해서 조건을 만족하는 선수들의 정보를 출력한다. 만약 선수와 같은 이름을 가진 동명이인이 있다면 서브 쿼리의 결과가 2건 이상의 결과가 반환돼 SQL 문에 오류가 발생한다.

<hr>
테이블 전체에 대해 집계함수를 적용할 때는 그 결과 값이 1건만 생성되기 때문에 단일 행 서브 쿼리로서 사용할 수 있다.

```sql
SELECT player_name, position, back_no FROM player
WHERE height <= (SELECT AVG(height) FROM player)
ORDER BY player_name;
```

### Multi Row Subquery
다중 행 서브 쿼리는 다중 행 비교 연산자(IN, ALL, ANY, SOME, EXITSTS)와 함께 사용된다.

<table>
<thead><td>다중 행 연산자</td><td>설명</td></thead>
<tr><td>IN(서브 쿼리)</td><td>서브 쿼리의 결과에 존재하는 임의의 값과 동일한 조건을 의미한다.</td></tr>
<tr><td>비교연산자 ALL(서브 쿼리)</td><td>서브 쿼리의 결과에 존재하는 모든 값을 만족하는 조건을 의미한다.<br>비교 연산자로 '>'를 사용했다면 메인 쿼리는 서브 쿼리의 모든 결과 값을 만족해야 하므로, 서브 쿼리 결과의 최댓값보다 큰 모든 건이 조건을 만족한다.</td></tr>
<tr><td>비교연산자 ANY/SOME(서브 쿼리)</td><td>서브 쿼리의 결과에 존재하는 어느 하나의 값이라도 만족하는 조건을 의미한다.<br>비교 연산자로 '>'를 사용했다면 메인 쿼리는 서브 쿼리의 값들 중 어떤 값이라도 만족하면 되므로, 서브 쿼리 결과의 최솟값보다 큰 모든 건이 조건을 만족한다.</td></tr>
<tr><td>EXIST(서브 쿼리)</td><td>서브 쿼리의 결과를 만족하는 값이 존재하는지 여부를 확인하는 조건을 의미한다. 조건을 만족하는 건이 여러 건이더라도 1건만 찾으면 더이상 검색하지 않는다.</td></tr>
</table>

```sql
SELECT region_name, team_name, e_team_name FROM team
WHERE team_id IN (SELECT team_id FROM player WHERE palyer_name = '정현수')
ORDER BY team_name;
```

### Multi Column Subquery
다중 칼럼 서브 쿼리는 서브 쿼리의 결과로 여러 개의 칼럼이 반환돼 메인 쿼리의 조건과 동시에 비교되는 것을 의미한다. <br>SQL Server에서는 지원되지 않는 문법이다.

```sql
SELECT team_id, player_name, position, back_no, height FROM player
WHERE (team_id, height) IN 
(
    SELECT team_id, MIN(height) 
    FROM player 
    GROUP BY team_id
)
ORDER BY team_id, player_name;
```
서브 쿼리와 메인 쿼리에서 비교하고자 하는 칼럼 개수와 칼럼의 위치가 동일해야 한다.

### Correlated Subquery
연관 서브 쿼리는 서브 쿼리 내에 메인 쿼리 칼럼이 사용된 서브 쿼리다.

```sql
SELECT b.team_name, a.player_name, a.position, a.back_no, a.height FROM player a, team b
WHERE a.height < 
(
    SELECT AVG(x.height) 
    FROM player x
    WHERE x.team_id = a.team_id
    GROUP BY x.team_id
)
AND b.team.id = a.team_id
ORDER BY player_name;
```

서브 쿼리는 메인 쿼리에 존재하는 모든 행에 대하여 반복 수행된다.

EXISTS 서브 쿼리는 항상 연관 서브 쿼리로 사용된다. 또한 EXISTS 서브 쿼리는 아무리 조건을 만족하는 건이 여러 건이더라도 조건을 만족하는 1건만 찾으면 추가적인 검색을 진행하지 않는다.

```sql
SELECT a.stadium_id, a.stadium_name FROM stadium a
WHERE EXISTS 
(
    SELECT 1 FROM schedule x -- 메인 쿼리의 결과 집합 중 첫번째 칼럼
    WHERE x.stadium_id = a.stadium_id
    AND x.sche_date BETWEEN '20120501' AND '20120502'
);
```

### Scalar Subquery
스칼라 서브 쿼리는 1 Row 1Column 만을 반환하는 서브 쿼리로, SELECT 절에서 사용한다.

```sql
SELECT a.player_name, a.height, ROUND(
    (
        SELECT AVG(x.height) FROM player x
        WHERE x.team_id = a.team_id
    ), 3)
FROM player a;
```

### Inline View
FROM 절에서사용하는 서브 쿼리를 말하며, 서브 쿼리의 결과를 테이블처럼 사용할 수 있다.<br>
SELECT 문을 객체로 저장해 테이블처럼 사용하는 View와 달리 쿼리 내에서 즉시 처리된다.

```sql
SELECT b.team_name, a.player_name, a.back_no
FROM 
(
    SELECT team_id, player_name, back_no
    FROM player
    WHERE position = 'MF'
) a, team b
WHERE b.team_id = a.team_id
ORDER BY 2;
```

인라인 뷰에서는 ORDER BY 절을 사용할 수 있다.

### HAVING 절에서 서브 쿼리 사용
HAVING 절은 집계함수와 함께 사용될 때 그룹핑 된 결과에 대해 부가적인 조건을 주기 위해 사용한다.


```SQL
SELECT a.team_id, b_team_name, ROUND(AVG(a.height), 3) AS avg_height
FROM player a, team b
WHERE b.team_id = a.team_id
GROUP BY a.team_id, b.team_name
HAVING avg(a.height) < (
    SELECT AVG(x.height) FROM player x
    WHERE x.team_id in (
        SELECT team_id FROM team
        WHERE team_name = '삼성블루윙즈'
    )
);
```

### View
뷰는 실제 데이터를 갖지 않고 뷰 정의만을 갖고 있다. 질의에서 뷰가 사용되면 뷰 정의를 참조하여 DBMS 내부적으로 질의를 재작성해 질의를 수행한다.

뷰 사용의 장점
- 독립성 : 테이블 구조가 변경돼도 뷰를 사용하는 응용 프로그램은 변경하지 않아도 된다.
- 편리성 : 복잡한 질의를 뷰로 생성함으로써 관련 질의를 단순하게 작성할 수 있다. 또한 해당 형태의 SQL 문을 자주 사용할 때 뷰를 이용하면 편리하게 사용할 수 있다.
- 보안성 : 숨기고 싶은 정보가 있다면, 뷰를 생성할 때 해상 칼럼을 빼고 생성함으로써 사용자에게 정보를 감출 수 있다.


**뷰 생성**<BR>

테이블 참조 생성
```SQL
CREATE VIEW v_player_team AS
SELECT a.player_name, a.position, a.back_no, b.team_id, b.team_name
FROM player a, team b
WHERE b.team_id = a.team_id;
```

뷰 참조 생성
```sql
CREATE VIEW v_player_team_filter AS
SELECT player_name, position, back_no, team_name
FROM v_player_team
WHERE position IN ('GK', 'MF);
```

**뷰 사용**<BR>
```SQL
SELECT player_name, position, back_no, team_id, team_name
FROM v_player_team
WHERE player_name LIKE '황%';
```

위의 질의는 DBMS가 내부에서 다음과 같이 재작성(rewrite)한다.

```SQL
SELECT player_name, position, back_no, team_id, team_name
FROM 
(
    SELECT a.player_name, a.position, a.back_no, b.team_id, b.team_name
    FROM player a, team b
    WHERE b.team_id = a.team_id;   
)
WHERE player_name LIKE '황%';
```
