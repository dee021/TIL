# 집계(aggregate)
이미 저장되어 있는 정보에서 다른 정보를 합해서 출력하거나, 그룹화를 통해 다른 형태로 정보를 보여주는 것

## 집계 방법
1) 데이터베이스의 모든 정보를 불러와 애플리케이션 단계에서 집계
   - 자유도 : 좋음
   - 처리 속도 : 가장 나쁨
   - 램 사용량 : 매우 높음
   - 처리 위치 : 애플리케이션 내부
2) MongoDB의 맵-리듀스 기능을 이용
    - 자유도 : 좋음
    - 처리 속도 : 보통
    - 램 사용량 : 높음
    - 처리 위치 : 자바스크립트 엔진
3) MongoDB의 집계 파이프라인 기능을 이용
    - 자유도 : 나쁨
    - 처리 속도 : 가장 좋음
    - 램 사용량 : 낮음
    - 처리 위치 : MongoDB 내부

> MongoDB 5.0부터 mapReduce 지원 X

### 맵-리듀스(mapReduce)

구조

```
db.collection_name.mapReduce(
    <map>,
    <reduce>,
    {
        out : <collection>,
        query: <document>,
        sort: <docuemnt>,
        limit: <number>,
        finalize: <function>,
        scope: <docuemnt>,
        jsMode: <boolean>,
        verbose: <boolean> 
    }
)
```

<table>
<thead><td>필드</td><td>타입</td><td>설명</td></thead>
<tr><td>map</td><td>function</td><td>어떤 정보들끼리 서로 묶일 수 있을지 정하는 함수</td></tr>
<tr><td>reduce</td><td>function</td><td>묶인 정보들끼리 연산을 하는 함수</td></tr>
<tr><td>out</td><td>string | document</td>
<td>출력된 정보를 데이터베이스 내에 기록으로 남기도록 설정</td></tr>
<tr><td>query</td><td>document</td><td>(optional) map 과정을 실행하기 전에 먼저 필요한 정보를 쿼리로 걸러낸다.</td></tr>
<tr><td>sort</td><td>docuemnt</td><td>(optional) map 과정을 실행하기 전에 어떤 값을 기준으로 정렬할지 설정</td></tr>
<tr><td>limit</td><td>integer</td><td>(optional) map 과정을 실행하기 전에 함수에 넣을 도큐먼트의 수를 제한</td></tr>
<tr><td>finalize</td><td>function</td><td>(optional) reduce 과정이 끝난 후 실행할 함수를 설정</td></tr>
<tr><td>scope</td><td>docuemnt</td><td>
(optional) map, reduce, finalize 함수에서 사용 가능한 전역변수 설정</td></tr>
<tr><td>jsMode</td><td>boolean</td>
<td>(optional) map과 redece 사이의 정보를 자바스크립트의 형태로 남길지 설정

- false(default) : map 함수에서 내보낸 JavaScript 객체를 BSON 객체로 변환
  - 변환된 BSON 객체는 reduce 함수 호출 시 JavaScript객체로 다시 변환됨 
- true : map 함수에서 내보낸 JavaScript 객체는 JavaScript 객체로 남아 있음
  - 변환하지 않으므로 실행 속도 증가
  - emit() 함수에 대한 고유 key 인수가 500,000개 미만인 결과 세트에만 사용 가능
</td></tr>
<tr><td>verbose</td><td>boolean</td><td>(optional) 연산 처리에 걸린 시간을 보여줄지 설정<br>default - false</td></tr>
</table>

<b>작동 과정</b><br>
`Query 작업` -> `Sort 작업` -> `Limit 작업`<br>
-> `Map 함수` : 주어진 컬렉션에서 어떤 도큐먼트를 하나의 도큐먼트로 묶을지 정하는 과정. JS 엔진 속에서 실행되며, BSON 정보가 JS로 변환되는 과정을 거쳐야 함<br>
-> `같은 키값끼리 그룹핑` : 정해진 도큐먼트를 실제로 묶는 작업. JSMode가 false면 JS 엔진에서 MongoDB 내부로 정보를 보내는 과정을 거치고, true이면 JS 엔진 내에서 작업 수행<br>
-> `Reduce 함수` -> `Finalizer 함수` -> `Out 작업`

#### mapping function : emit을 사용하여 생성

map 함수
```
function() {
    ...
    emit(key, value);
}
```

- emit(key, value) : Map 함수에서 다음 단계로 도큐먼트를 넘겨주기 위해 사용
  - key : 그룹핑의 기준
  - value : 다음 단계에 넘겨줄 값

예
```
var mapper = function() {
    emit(this.rating, 1) // Map, Reduce, Finalize 함수에서 입력 받은 도큐먼트는 this로 표현
} // => 각 도큐먼트의 rating 필드를 키로, 값 1이 저장됨
```

#### reduce function

reduce 함수
```
function(key, values) {
    ...
    return result;
}
```

- mapping 함수로부터 key와 values를 넘겨 받음
    - key : 그룹핑된 기준값
    - values : 배열에 각각의 값이 담긴 형태가 들어있음
    - return 을 이용하여 다음 단계로 값을 넘김

예
```
var reducer = function(key, values) { // mapper에서 지정한 key별 value 배열을 입력 받음
    var counter = 0
    values.forEach(function(value) {
        counter += value
    })
    return counter
}
```

리듀서 함수는 멱등원적(Idempotent)이어야 함
- 처리할 데이터가 많은 경우 도큐먼트를 분할하여 맵-리듀스 과정을 거침
- 중간 저장값을 고려하여 함수를 정의하지 않으면 예상과 다른 결과값을 갖게 된다.

#### finalizer function

finalize 함수
```
function(key, reducedValue) {
    ...
    return modifiedObject;
}
```

- reduce 함수의 key와 reducedValue를 넘겨받음
  - reducedValue : reduce 함수에 따라 배열 형태일 수도 아닐수도 있음
- reduce function은 하나의 데이터에 여러 번 실행될 수 있지만, Finalize function은 각 데이터당 한 번밖에 수행되지 않음

예
```
var finalizer = function(key, value) {
    value.user_id_avg = value.user_id_sum / value.count
    return value
}
```

#### out
1) 콘솔에 찍을 때

```
out: {
    inline : true
}
```

2) 컬렉션에 저장
```
out : {
    <action> : <collectionsName>,
    db : <dbName>,              // (optional) 저장할 데이터베이스 이름
    sharded : <boolean>,        // (optional) true 값으로 설정되었다면 저장하는 컬렉션을 샤딩하면서 저장한다. 
                                    이미 샤딩되지 않은 컬렉션에 저장하면서 이 값이 true면 에러
    nonAtomic : <boolean>       // (optional) action 값이 merge나 reduce일 때만 사용됨. 기본값과 같은 false 값으로 설정되면 맵-리듀스의 결과로 저장할 때 해당 데이터베이스는 잠금 상태가 된다.
}
```
<b>action</b>

- replace(string) : 저장할 컬렉션 이름을 값으로 한다. 컬렉션의 도큐먼트를 출력된 도큐먼트로 대체한다.
- merge(string) : 저장할 컬렉션 이름을 값으로 한다. 컬렉션에 출력된 도큐먼트를 더한다. 같은 _id 값을 가진 도큐먼트가 있으면 덮어쓴다.
- reduce(string) : 저장할 컬렉션 이름을 값으로 한다. 컬렉션에 출력된 도큐먼트를 더한다. 같은 _id 값을 가진 도큐먼트가 있으면 reduce 과정을 두 도큐먼트에 적용한 후 결과값을 덮어쓴다.


#### 증분 맵-리듀스
- 기존의 맵-리듀스 결과와 새로 증가한 도큐먼트만 맵-리듀스를 수행하여 병합하는 방식
  - 과거의 데이터들은 수정/삭제되지 않아야 하는 제한 사항
- 기존의 맵-리듀스 결과를 컬렉션으로 저장하고, 새로 증가한 도큐먼트에 대하여 맵-리듀스를 실행하여 그 결과를 저장
  - 새로 증가한 도큐먼트는 options을 통해 지정

### 집계 파이프라인

<b>aggregate()</b>

```
db.collection_name.aggregate(stage1[, stage2, ...])
```

- 하나 이상의 stage로 구성
- 각 stage는 입력 도큐먼트에 대한 연산을 수행하고 다음 단계로 전달
- 이전 state에서 출력된 도큐먼트에서 해당 필드의 값을 이용하기 위해 "$필드명"의 형태로 사용
- 파이프라인에 $merge 혹은 $out 스테이지가 포함되지 않는 한 컬렉션의 도큐먼트를 수정하지 않음
  - $out : 기존 컬렉션이 있으면 덮어쓰며, 샤딩된 컬렉션에는 쓸 수 없음
  - $merge : 샤딩 여부에 관계없이 모든 데이터베이스와 컬렉션에 쓸 수 있음
- $bucket, $bucketAuto, $group 스테이지에서 `accumulator` 사용 가능


예
```
 db.rating.aggregate([
    {$match: {user_id:{$lte:6}}},
    {$group: {_id:"$rating", count:{$sum:1}}}
])
```

<b>기본 스테이지</b>
<table><thead><td>스테이지</td><td>설명</td></thead>
<tr><td>$project</td><td>기존 필드를 숨기거나 새 필드를 추가</td></tr>
<tr><td>$group</td><td>지정된 필드를 기준으로 그룹화</td></tr>
<tr><td>$match</td><td>지정 조건에 따라 도큐먼트를 필터링해서 반환</td></tr>
<tr><td>$unwind</td><td>입력 도큐먼트에서 배열 필드를 분해하여 각 요소에 대한 도큐먼트로 분리</td></tr>
<tr><td>$out</td><td>집계 파이프라인의 결과를 컬렉션에 기록</td></tr>
</table>

<b>고급 스테이지</b>
<table><thead><td>스테이지</td><td>설명</td></thead>
<tr><td>$bucket</td><td>도큐먼트를 범위에 따라 그룹화</td></tr>
<tr><td>$bucketAuto</td><td>도큐먼트를 자동적으로 범위에 따라 그룹화</td></tr>
<tr><td>$addFields</td><td>새로운 필드를 추가</td></tr>
<tr><td>$facet</td><td>각 필드에 대한 파이프라인을 수행해서 해당 필드에 파이프라인의 출력값을 배열의 형태로 저장</td></tr>
<tr><td>$lookup</td><td>같은 데이터베이스에 있으면서 샤딩되지 않은 외부 컬렉션에 대해서 현재 필드와 외부 컬렉션의 필드가 일치하면 배열로 외부 컬렉션 도큐먼트를 가져옴</td></tr>
<tr><td>$replaceRoot</td><td>주어진 도큐먼트를 newRoot 값으로 바꿈</td></tr>
<tr><td>$sample</td><td>주어진 size 값만큼 랜덤하게 도큐먼트를 가져옴</td></tr>
<tr><td>$sortByCount</td><td>주어진 필드의 값이 같은 도큐먼트끼리 그룹화 후, 도큐먼트의 숫자를 세어 표시하고 내림차순으로 정렬<br>$group과 $sort 작업을 한 것과 같은 의미</td></tr>
</table>



#### $project
- 결과 도큐먼트에 표시할 필드를 지정하거나 배제
- 새로운 필드를 추가하거나 기존 필드 값을 재설정
  - 명시하지 않은 필드는 결과 도큐먼트에서 배제됨
- _id는 필드는 0 또는 false로 지정하지 않으면 기본 포함됨

형식
```
{$project : { <field>: <boolean> } }

{$project : { <field>: <expression> } }
```

사용 예 : 중첩 필드 승격(promoting)
```q
> db.books.find().pretty()
{
        "_id" : ObjectId("67ef7aee533612b82155c08d"),
        "title" : "MongoDB: The Definitive Guide",
        "author" : [
                "Kristina Chodorow",
                "Mike Dirolf"
        ],
        "published_date" : ISODate("2010-09-24T00:00:00Z"),
        "pages" : 216,
        "language" : "English",
        "publisher" : {
                "name" : "O'Reilly Media",
                "founded" : 1980,
                "location" : "CA"
        }
}
{
        "_id" : ObjectId("67ef7aee533612b82155c08e"),
        "title" : "50 Tips and Tricks for MongoDB Developer",
        "author" : "Kristina Chodorow",
        "published_date" : ISODate("2011-05-06T00:00:00Z"),
        "pages" : 68,
        "language" : "English",
        "publisher" : {
                "name" : "O'Reilly Media",
                "founded" : 1980,
                "location" : "CA"
        }
}


> db.books.aggregate([
    {$project: {
        _id:0, 
        title:1, 
        author:1, 
        published_date:1, 
        publisher: "$publisher.name"
    }}
])

{
        "title" : "MongoDB: The Definitive Guide",
        "author" : [
                "Kristina Chodorow",
                "Mike Dirolf"
        ],
        "published_date" : ISODate("2010-09-24T00:00:00Z"),
        "publisher" : "O'Reilly Media"
}
{
        "title" : "50 Tips and Tricks for MongoDB Developer",
        "author" : "Kristina Chodorow",
        "published_date" : ISODate("2011-05-06T00:00:00Z"),
        "publisher" : "O'Reilly Media"
}
```


#### $group
- _id 값으로 지정된 내용이 같은 도큐먼트끼리 그룹화한다.
- SQL의 GROUP BY와 유사한 기능

형식
```
{$group : {
    <_id>: <expression>,
    <field1>: {<accumulator1>: <expression1>}, ...} }
```
- <_id> : 그룹화 키 지정
- < field> : (optional) accumulator 를 사용하여 계산
  - $first, $last, $min, $max, $avg, $sum, $push, $addToSet 등
  - 생략 시 그룹화 키의 종류만 확인하는 결과


#### $match
- 도큐먼트를 필터링해서 반환한다. find문과 비슷한 역할
- 파이프라인 최적화를 위해 가능한 한 앞부분에 배치

```
{$match: {<query> }}
```
- "\$필드명" 형식이 아니라 find() 명령에서의 query형식으로 작성
  
#### $unwind

- 입력 도큐먼트의 배열 필드를 분해하여 각 요소에 대한 도큐먼트로 분리하여 출력한다.

![unwind stage](https://github.com/dee021/TIL/blob/main/MongoDB/img/unwind.jpg)

형식
```
{$unwind: <field path>}

{$unwind: 
    {
        path : <field path>,
        includeArrayIndex: <string>,
        preserveNullAndEmptyArrays : <boolean> 
    }
}
```
- path : 분리 대상이 되는 배열 필드의 경로
- includeArrayIndex : (optional) 기존 배열에서의 인덱스를 저장할 필드의 이름
  - value type : NumberLong
- preserveNullAndEmptyArrays : (optional)
  - true : 빈 요소도 각각의 도큐먼트로 분리
  - false : (default) 해당 배열이 null이거나 빈 배열일 경우 출력하지 않음


#### $out
- 파이프라인의 결과를 컬렉션에 기록한다.
```
{$out: "<collection name>"}
{$out : {db : "<db name>", coll: "<collection name>"}}
```
- 존재하는 컬렉션이면 지우고 새로 작성
- 샤딩된 컬렉션이나 capped 컬렉션을 출력 결과로 지정할 수 없다.

#### $bucket
- 도큐먼트를 범위에 따라 그룹화한다.
- 그룹화 기준이 정수값이면 $group 스테이지를, 실수값이면 $bucket 스테이지를 사용
```
{$bucket : {
    groupBy : <expression>,
    boundaries: [ <lowerbound1>, <lowerbound2>, ... ], 
    default: <literal>, 
    output : {
        <output1> : { <$accumulator expression> },
        ...
        <outputN> : { <$accumulator expression> },
    } 
}}
```
- groupBy : (string)  해당 필드의 값에 따라 그룹화
- boundaries: 정해진 필드의 값의 범위를 정한다. 요소들은 동일한 자료형으로 오름차순으로 나열되어야 함
  - 숫자형끼리는 자료형 혼합 가능
  - [2,3,5] 일 때, [2, 3), [3, 5) 두 구간으로 그룹화하고, default 지정 시 이외 범위는 default명으로 들어감
    - 이 때의 _id는 2, 3
  - 범위의 값이 꼭 숫자가 아니어도 가능
  - 해당 범위에 속하는 도큐먼트가 없으면 해당 범위는 생성되지 않음
- default: (optional) 지정한 구간을 벗어나는 도큐먼트를 모을 필드명을 정한다.
  - 선택적이나, 범위 외의 도큐먼트가 있다면 Error
- output : (optional) 그룹화를 하고 난 후 출력 결과를 어떻게 표시할 지 결정
   - 생략 시 $count 결과를 출력, 생략하지 않을 때는 명시적으로 지정해야 함

#### $bucketAuto
- 도큐먼트를 자동적으로 범위에 따라 그룹화한다. 자동적으로 n등분

```
{$bucketAuto : {
    groupBy : <expression>,
    buckets: <number>, 
    output : {
        <output1> : { <$accumulator expression> },
        ...
        <outputN> : { <$accumulator expression> },
    }
    granularity: <string>
}}
```
- buckets:  도큐먼트를 몇 개의 그룹으로 나눌 지 설정
- output : (optional) 그룹화를 하고 난 후 출력 결과를 어떻게 표시할 지 결정
  - 생략 시 $count 결과를 출력, 생략하지 않을 때는 명시적으로 지정해야 함
- granularity: (optional) 어떤 기준으로 값을 분류할지 설정
  - 그룹화를 하는 기준이 되는 값들이 전부 숫자이고 NaN이 아니어야 함
  - granularity 값을 설정하면 출력되는 그룹 개수가 buckets 숫자보다 적은 상황에는 적게 그룹화한 상태로 결과를 출력

<table>
<thead><td>수열 이름</td><td>값</td><td>설명</td></thead>
<tr><td>레나드 수열</td><td>R5, R10, R20, R40, R80</td><td>m번째 레나드 수열의 n번째 요소는 10의 n/m 제곱근을 소수점 둘째자리에서 반올림한 값<br>

- ex) R5 값의 2번째 요소 : 10의 2/5 제곱근을 반올림한 2.5</td></tr>
<tr><td>E 수열</td><td>E6, E12, E24, E48, E96, E192</td><td>m번째 E 수열의 n번째 요소는 10의 n/m 제곱근을 소수점 둘째자리에서 반올림한 값</td></tr>
<tr><td>1-2-5 수열</td><td>1-2-5</td><td>0.1, 0.2, 0.5, 1, 2, 5, 10, 20, 50, ... 과 같이 이어지는 수열</td></tr>
<tr><td>공비가 2인 등비수열</td><td>POWERSOF2</td><td>n번째 요소: 2**(n-1)<br>
1, 2, 4, 8, 16, 32, ... 로 나열되는 수열</td></tr>
</table>

출력 도큐먼트 형식
```
{
    id: {min : <number>, max : <number>}, 
    <field1> : <value1>,
    ...
    <fieldN> : <valueN>
}
```

#### $addFields
- Alias : $set
- 새로운 필드를 추가
  - $project 스테이지에서 필드를 추가하는 기능만 있는 것과 같음
- 기존 필드를 지정하면 기존 필드의 내용을 변경

```
{$addFields : { 
    <newField> : <expression>,
     .... 
    }
}
```

#### $facet
- 각 `필드`에 대한 파이프라인을 수행해서 해당 필드에 파이프라인의 출력값을 배열의 형태로 저장한다.
- 정보들을 취합해서 통계를 내거나 요약을 하고자 할 때 사용하기 좋은 스테이지

```
{$facet : {
    <outputField1> : [ <stage1>, <stage2>, ... ] 
    <outputField2> : [ <stage1>, <stage2>, ... ]
    ...
    }
}
```

- 각 outputField는 지정된 파이프라인을 수행한 결과를 값으로 갖는다

> 예시 추가

#### $lookup
- 동일 데이터베이스의 외부 컬렉션에 대해서 현재 컬렉션의 필드와 외부 컬렉션의 필드가 일치하면 <b>배열</b>로 외부 컬렉션 도큐먼트를 가져온다.
  - MongoDB 5.1 이전 : 외부 컬렉션은 샤딩되어 있지 않아야 함
  - MongoDB 5.1부터 : 샤딩된 외부 컬렉션과 조인 가능

> 서로 다른 두 컬렉션을 결합하려면 `$unionWith` 사용

1) 두 필드만을 비교하여 연결할 수 있는 경우
   - 입력 컬렉션의 필드와 외부 컬렉션의 필드 간 equal join 수행하는 경우

```
{$lookup : {
    from : <collections to join>,
    localField : <field from the input documents>, 
    foreignField : <field from the document of the "from" collection>, 
    as : <output array field> 
}}
```
- from : 조인할 외부 컬렉션 지정
- localField : 입력 컬렉션 내 조인의 기준이 되는 필드를 지정
- foreignField : from에 지정된 외부 컬렉션 내 조인의 기준이 되는 필드를 지정
- as : 매칭된 도큐먼트를 담은 배열의 필드명을 지정
  - 기존 필드명을 지정하면 기존 필드를 덮어씀

2) 다양한 논리 식을 이용하여 두 컬렉션 사이를 연결하는 경우
   - 조인된 컬렉션에서 파이프라인을 실행하는 경우
   - 여러 조인 조건을 적용하는 경우
   - 

```
{$lookup : {
    from : <collection to join>,
    let : { <var_1>: <expression>, ..., <var_n>: <expression> },
    pipeline : [ <pipeline to execute on the collection to join> ],
    as : <output array field>
}} 
```

- let : (optional) 입력 도큐먼트의 값을 pipeline 단계에서 사용하기 위해 변수로 지정
    - 지정 변수는 pipeline 단계에서 "$$var"의 형태로 사용 가능
- pipeline : from에 지정한 외부 컬렉션을 해당 파이프라인에 통과시킨 뒤 출력되는 도큐먼트들을 as에서 정한 필드에 배열 요소로 연결
    - pipeline 단계 내의 $match 스테이지에서 let에 지정된 변수를 사용하기 위해서 $expr 연산자를 사용
    - $out, $merge 스테이지 사용 불가능 


#### $replaceRoot
- 주어진 도큐먼트를 newRoot 값으로 바꾼다.
- _id 필드를 포함하여 모든 기존 필드를 대체
- 서브 도큐먼트를 도큐먼트 전체의 내용으로 바꿀 때 유용

```
{$replaceRoot : {
    newRoot : <replacementDocument>
    }
}
```
- newRoot : 입력 도큐먼트를 지정한 도큐먼트로 대체
    - \<replacementDocument>가 유효한 문서가 아닐 경우 에러 발생


#### $sample
- 주어진 size 값만큼 랜덤하게 도큐먼트 추출

``` 
{$sample : {
    size : <positive integer>
    }
}
```
- 랜덤 정렬 후 sampling 하므로 allowDiskUsege 옵션이 true가 아니라면 100MB 이상의 도큐먼트를 처리할 수 없음

#### $sortByCount
- 주어진 필드의 값이 같은 도큐먼트끼리 그룹화 후, 도큐먼트의 숫자를 세어 표시하고 내림차순으로 정렬한다.

```
{$sortByCount: <expression> }
```
- \$group과 \$sort 작업을 한 것과 같은 의미

```
{$group: {_id:<expression>, count: {$sum:1}}},
{$sort: {count:-1}}
```


#### 입력 도큐먼트 제어 스테이지
<table>
<thead><td>스테이지</td><td>형식</td><td>설명</td></thead>
<tr><td>$limit</td><td>

```
{$limit : <number> }
```
</td><td>입력 받은 도큐먼트 중 지정된 숫자만큼 처음부터 출력</td></tr>
<tr><td>$skip</td><td>

```
{$skip : <number> }
```
</td><td>입력 받은 도큐먼트 중 지정된 숫자만큼 건너뛰고 출력</td></tr>
<tr><td>$sort</td><td>

```
{$sort : {<field> : <1 or -1> }}
```
</td><td>도큐먼트를 정렬한다. 1이면 오름차순, -1이면 내림차순

- 처리하는 도큐먼트의 총 크기가 100MB 이상이면 allowDiskUse 옵션을 true로 설정해야 한다.
    - ```db.collection_name.aggregate([ states ], {allowDiskUse: true})```

</td></tr>
</table>

### 집계 파이프라인 연산자

- 일부 집계 파이프라인 스테이지에서는 표현식을 허용
    - 예: $project, $addFields, $group

형식 : 피연산자가 들어있는 배열을 인수로 받음
```
{
    <operator> : [ <argument1>, <argument2>, ... ]
}
```

형식 : 단일 인수를 허용하는 경우 외부 배열 생략 가능
```
{
    <operator> : <argument>
}
```

#### 가변적 표현식(variable expression)
가변적 표현식을 사용하여 리터럴(literal), 날짜 값 구문 분석을 위한 식, 조건식을 사용

#### 산술 표현식(arithmetic expression)
- 숫자에 대한 수학적 연산 수행
- 일부 연산자는 날짜 산술 연산 지원


산술 연산자
<table>
<thead>
<tr><td>이름</td><td>설명</td><td>예시</td></tr>
</thead>
<tbody>
<tr><td>$add</td><td>배열에 있는 숫자를 더한다.<br>날짜 연산을 지원하며, 최대 하나의 표현식이 날짜로 해석됨<br>`날짜 + 숫자`인 경우 숫자는 밀리초 단위로 해석 
</td><td>

```
{$add:["$count", 1, 2, 3] }
{$add:["$hours", 3*24*60*60000] } // 3일 합산
```
</td></tr>
<tr><td>$divide</td><td>첫 번째 숫자를 두 번째 숫자로 나눈 몫 반환</td><td>{$divide: ["$hours",8]}</td></tr>
<tr><td>$mod</td><td>첫 번째 숫자를 두 번째 숫자로 나눈 나머지 반환</td><td>

```
{$mod: ["$hours",8 ]}
```
</td></tr>
<tr><td>$multiply</td><td>배열에 있는 숫자들을 곱한다.</td><td>

```
{$multiply:["$price", "$hours", 1]}
```
</td></tr>
<tr><td>$subtract</td><td>첫 번째 숫자에서 두 번째 숫자를 뺀다.</td><td>

```
{$subtract: ["$hours",8]}
```
</td></tr>
</tbody>
</table>

#### 비교 표현식(comparison expression)
- 다양한 유형의 범위 필터(range filter)를 표현할 수 있음
- `$cmp` 연산자를 제외하고 boolean 반환

비교 연산자
<table>
<thead>
<tr><td>이름</td><td>설명</td><td>예시</td></tr>
</thead>
<tbody>
<tr><td>$cmp</td><td>(compare) 두 값을 비교하여 첫 번째 값이 크면 1, 두 번째 값이 크면 -1, 같으면 0 반환</td><td>

```
{$cmp: ["$qty", 250]}
```
</td></tr>
<tr><td>$eq</td><td>(equals) 주어진 두 값이 서로 일치하면 참, 아니면 거짓 반환</td><td>

```
{$eq:["$qty", 250]}
```
</td></tr>
<tr><td>$ne</td><td>(not equals) 주어진 두 값이 일치하지 않으면 참, 일치하면 거짓 반환</td><td>

```
{$ne:["$qty", 250]}
```
</td></tr>
<tr><td>$gt[e]</td><td>(greater than [or equals]) 첫 번째 값이 두 번째 값보다 크[거나 같으]면 참, 아니면 거짓 반환</td><td>

```
{$gte:["$qty", 250]}
```
</td></tr>
<tr><td>$lt[e]</td><td>(less than [or equals]) 첫 번째 값이 두 번째 값보다 작[거나 같]으면 참, 아니면 거짓 반환</td><td>

```
{$lte:["$qty", 250]}
```
</td></tr>
<tr><td>$in</td><td>두 번째 값 안에 첫 번째 값이 속하면 참, 아니면 거짓 반환</td><td>

```
{$in:["bananas", "$in_stock"]}
```
</td></tr>
<tr><td>$nin</td><td>(not in) 두 번째 값 안에 첫 번째 값이 속하면 거짓, 아니면 참 반환환</td><td>

```
{$nin: ["bananas", "$in_stock"] }
```
</td></tr>
</tbody>
</table>


#### 문자열 표현식(string expression) 
- 연결(concatenate), 하위 문자열(substring) 검색, 대소문자 및 텍스트 검색과 관련된 작업을 수행할 수 있음

문자열 연산자
<table>
<thead>
<tr><td>이름</td><td>설명</td><td>예시</td>
</thead>
<tbody>
<tr><td>$concat</td><td>두 개 이상의 문자열을 하나의 문자열로 연결</td><td>

```
{$concat: ["$item", "-", "$description"] }
```
</td></tr>
<tr><td>$strcasecmp</td><td>두 개의 문자열의 대소를 비교하여 크면 1, 작으면 -1, 같으면 0 반환</td><td>

```
{$strcasecmp:["$quarter", "13q4"] }
```
</td></tr>
<tr><td>$substr</td><td>ASCII 문자열의 일부분을 자라서 반환<br>args: 1 - 문자열, 2 - 시작 문자 위치, 3 - 문자 길이</td><td>

```
{$substr:["$quarter",2,-1] }
```
</td></tr>
<tr><td>$substrCP</td><td>UTF-8 문자열의 일부분을 잘라서 반환<br>args: 1 - 문자열, 2 - 시작 문자 위치, 3 - 문자 길이</td><td>

```
{$substrCP:["$quarter",2,3] }
```
</td></tr>
<tr><td>$strLenCP</td><td>UTF-8 문자열의 길이를 반환</td><td>

```
{$strLenCP:"$name"}
```
</td></tr>
<tr><td>$toUpper / $toLower</td><td>문자열의 모든 문자를 대/소문자로 바꾼다</td><td>

```
{$toUpper:"$item"}
```
</td></tr>
</tbody>
</table>


#### 날짜, 시간 연산자
<table>
<thead>
<tr><td>이름</td><td>설명</td><td>예시</td></tr>
</thead>
<tbody>
<tr><td>$toDate</td><td>주어진 값을 Date 타입으로 변환</td><td>

```
{$toDate :"2001-10-02"}
```
</td></tr>
<tr><td>$dayOfYear</td><td>올해 1월 1일부터 지금까지의 일수(1~366)</td><td>

```
{$dayOfYear: new Date("2016-01-01")}
```
</td></tr>
<tr><td>$dayOfMonth</td><td>이번달 1일부터 지금까지의 일수(1~31)</td><td>

```
{$dayOfMonth: new Date("2016-01-01")}
```
</td></tr>
<tr><td>$dayOfWeek</td><td>이번주 월요일부터 지금까지의 일수. 1은 일요일(1~7)</td><td>

```
{$dayOfWeek: new Date("2016-01-01")}
```
</td></tr>
<tr><td>$year / $month</td><td>날짜의 년도/월</td><td>
```
{$year: new Date("2016-01-01T12:00:03Z")}
```
</td></tr>
<tr><td>$week</td><td>날짜의 연중 주(0~53)</td><td>

```
{$week: new Date("2016-01-01T12:00:03Z")}
```
</td></tr>
<tr><td>$hour</td><td>날짜의 시간(0~23)</td><td>

```
{$hour: new Date("2016-01-01T12:00:03Z")}
```
</td></tr>
<tr><td>$minute / $second</td><td>날짜의 분/초(0~59)</td><td>

```
{$second: new Date("2016-01-01T12:00:03Z")}
```
</td></tr>
<tr><td>$millisecond</td><td>날짜의 밀리초(0~999)</td><td>

```
{$millisecond: new Date("2016-01-01T12:00:03Z")}
```
</td></tr>
</tbody>
</table>

#### 불리언 표현식(boolean expression)

논리 연산자
<table>
<thead>
<tr><td>이름</td><td>설명</td><td>예시</td></tr>
</thead>
<tbody>
<tr><td>$not</td><td>배열 속 값이 참이면 거짓, 거짓이면 참 반환</td><td>

```
{$not : [{$gt:["$qty", 250]} ]}
```
</td></tr>
<tr><td>$or</td><td>주어진 조건 중 하나라도 만족하면 참을, 그렇지 않으면 거짓 반환</td><td>

```
{$or : [{$gt:["$qty", 250]}, {$lt:["$qty",200]} ]}
```
</td></tr>
<tr><td>$and</td><td>주어진 모든 조건을 만족하면 참을, 그렇지 않으면 거짓 반환<br>null, 0, undefined를 제외한 숫자 값과 배열을 포함한 모든 자료형을 true로 간주</td><td>

```
{$and : [{$gt:["$qty", 250]}, {$lt:["$qty",200]} ]}
```
</td></tr>
<tr><td>$nor</td><td>주어진 조건 중 하나라도 만족하지 않으면 참 값을, 그렇지 않으면 거짓 반환</td><td>

```
{$nor : [{$gt:["$qty", 250]}, {$lt:["$qty",200]} ]}
```
</td></tr>
</tbody>
</table>

#### 조건부 표현식 연산자
<table>
<thead>
<tr><td>이름</td><td>설명</td><td>예시</td></tr>
</thead>
<tbody>
<tr><td>$cond</td><td>if 자리에 조건을 쓰고, 참이면 then의 값을, 거짓이면 else의 값을 반환</td><td>

```
{$cond: { if : {$gte: ["$qty", 250]}, then: 30, else:20}}
```
</td></tr>
<tr><td>$ifNull</td><td>첫 번째 요소 값이 존재하지 않으면, 두 번째 값을 반환</td><td>

```
{$ifNull : ["$description", "Unspecified"]}
```
</td></tr>
<tr><td>$switch</td><td>조건에 해당하는 첫 번째 case에 지정된 then 구문을 실행</td><td>

```
{$switch: {
      branches: [
         { case: "this is true", then: "first case" },
         { case: false, then: "second case" }
      ],
      default: "Did not match"
}}
```
</td></tr>
</table>


<b>$switch</b>

```
$switch: {
   branches: [
      { case: <expression>, then: <expression> },
      { case: <expression>, then: <expression> },
      ...
   ],
   default: <expression>
}
```
- case : boolean으로 해석되는 유효한 표현식
    - 각 case는 상호배타적일 필요는 없으나 가장 처음 만족하는 조건의 행동을 취함
- then : 처음 만족되는 case의 경우 실행할 표현식
- default : (optional) 모든 case 표현식의 조건을 충족하지 못 하는 경우 취할 행동
    - 모든 조건이 충족되지 않고, default가 없는 경우 Error 반환


#### 배열 표현식(array expression)
- 배열 요소를 필터링하거나, 배열을 분할하거나, 특정 배열에서 값의 범위를 가져오는 등 배열 조작에 유용

배열 표현식 연산자
<table>
<thead>
<tr><td>이름</td><td>설명</td><td>예시</td></tr>
</thead>
<tbody>

<tr><td>$arrayElemAt</td>
<td>주어진 배열속의 정해진 순번의 요소를 반환, [0, n)</td>
<td>

```
{$arrayElemAt: [[true, false, 1], 1]}
```
</td></tr>


<tr><td>$filter</td><td>필터 조건과 일치하는 요소만 포함된 배열을 반환</td><td>

```
{
  $filter: {
     input: [ 1, "a", 2, null, 3.1, NumberLong(4), "5" ],
     as: "num",
     cond: { $isNumber: "$$num" }
  }
}
```
</td></tr>

<tr><td>$slice</td><td>배열의 부분 집합을 반환</td><td>

```
{ $slice: [ [ 1, 2, 3 ], 1, 1 ] }
```
</td></tr>
</table>


<b>$arrayElemAt</b>

- 지정된 배열 인덱스에 있는 요소 반환

형식
```
{ $arrayElemAt: [ <array>, <idx> ] }
```
- array : 배열로 해석되는 모든 유효한 표현식
- idx : 정수로 해석되는 모든 유효한 표현식
  - 해당 값이 0또는 양의 정수인 경우 : 배열의 처음부터 시작하여 위치에 있는 요소를 반환
  - 해당 값이 음의 정수인 경우 : 배열의 끝 요소부터 시작하여 위치에 있는 요소를 반환
  - idx가 배열 경계를 초과하는 경우 : null 반환

<b>$slice</b>

- 배열의 부분 집합을 반환

형식
```
{ $slice: [ <array>, <n> ] }

{ $slice: [ <array>, <position>, <n> ] }
```

- position : (optional) 정수로 해석되는 모든 유효한 표현식
  - 양수인 경우 : 반환하는 배열의 시작 위치를 해당 위치로 함
    - 배열의 길이보다 크면 빈 배열 반환
  - 음수인 경우 : 배열의 끝에서부터 계산하여 시작 위치를 정함
    - 절댓값이 배열의 길이보다 크면 0 위치부터 시작
- n : 정수로 해석되는 모든 유효한 표현식
  - 음수인 경우 : 배열의 끝에서부터 n개 반환. position 옵션 사용 불가

<b>$filter</b>

- 필터 조건과 일치하는 요소만 포함된 배열을 반환하려면 배열의 하위 집합을 선택

형식

```
{
    $filter: 
    {
        input : <array>,
        as : <string>,
        cond : <expression>,
        limit : <number expression>
    }
}
```
- input : 대상 배열 지정
- as : (optional) input 배열의 각 요소를 나타내는 변수의 이름
    - default : `this`
- cond : 출력 배열에 포함할지 여부를 결정하는 조건식
    `as`에 지정된 변수 이름을 사용하여 각 요소를 개별적으로 검사
- limit : (optional) 반환하는 배열 요소의 수를 제한하는 옵션으로 1보다 같거나 커야함

#### 집합 표현식(set expression)
- 배열을 집합으로 사용할 수 있음
- 배열의 중복 요소와 순서를 무시한 집합(Set)으로 취급하여 연산
- 반환되는 배열은 고유한 요소로 구성된 배열이며 요소 순서는 지정되지 않음 

집합 표현식 연산자

<table>
<thead>
<tr><td>이름</td><td>설명</td><td>예시</td></tr>
</thead>
<tbody>
<tr><td>$setEquals</td>
<td>두 배열이 같은 요소를 가지면 참, 아니면 거짓 반환</td>
<td>

```
{$setEquals: ["$A", "$B"]}
```
</td></tr>

<tr><td>$setIsSubset</td>
<td>두 번째 배열이 첫 번째 배열의 부분 집합이면 참, 아니면 거짓 반환</td>
<td>

```
{$setIsSubset: ["$A", "$B"]}
```
</td></tr>

<tr><td>$setIntersection</td>
<td>두 배열의 공통된 요소를 모아서 배열로 반환</td>
<td>

```
{$setIntersection: ["$A", "$B"]}
```
</td></tr>

<tr><td>$setDifference</td>
<td>첫 번째 배열에는 있고 두 번째 배열에 없는 요소를 모아서 배열로 반환 ( {x | X in A-B } )</td>
<td>

```
{$setDifference: ["$A", "$B"]}
```
</td></tr>

<tr><td>$setUnion</td>
<td>두 배열에 속한 모든 요소를 모아서 배열로 반환. 요소 간 중복이 없다</td>
<td>

```
{$setUnion: ["$A", "$B"]}
```
</td></tr>
</tbody>
</table>

#### accumulator expression

누산기 연산자

- $bucket, $bucketAuto, $group 스테이지에서 사용 가능
  - MongoDB 5.0부터 $setWindowFields 포함
- 집계 파이프라인을 진행하는 동안 상태를 유지
  - 각 도큐먼트에 대한 연산이 아닌, 컬렉션 단위의 연산이 가능

<table>
<thead><td>연산자</td><td>설명</td></thead>
<tr><td>$accumulator</td><td>사용자 정의 누산기 함수 결과를 반환</td></tr>
<tr><td>$addToSet</td><td>각 그룹에 대한 고유한 원소로 이루어진 배열을 반환<br>배열 요소의 순서는 지정되지 않음</td></tr>
<tr><td>$avg</td><td>숫자 값의 평균을 반환<br>숫자가 아닌 값은 무시됨</td></tr>
<tr><td>$sum</td><td>숫자 값의 합을 반환<br>숫자가 아닌 값은 무시됨</td></tr>
<tr><td>$first</td><td>그룹의 첫 번째 도큐먼트에 대한 표현식 결과를 반환<br>정렬된 컬렉션에 대해 의미를 가지며, null이 반환될 수 있음</td></tr>
<tr><td>$last</td><td>그룹의 마지막 도큐먼트에 대한 표현식 결과를 반환<br>정렬된 컬렉션에 대해 의미를 가지며, null이 반환될 수 있음</td></tr>
<tr><td>$max</td><td>각 그룹별 가장 높은 표현식 값을 반환<br>값과 데이터 유형 모두 비교</td></tr>
<tr><td>$min</td><td>각 그룹별 가장 낮은 표현식 값을 반환<br>값과 데이터 유형 모두 비교</td></tr>
<tr><td>$push</td><td>각 그룹 내 도큐먼트에 대한 표현식 값을 담은 배열을 반환<br>파이프파인으로 들어오는 순서에 따라 배열 원소의 순서가 정해짐<br></td></tr>
</table>
