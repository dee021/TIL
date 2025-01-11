# Document 조작
## Document 삽입
insertOne, insertMany 명령으로 컬렉션에 도큐먼트 삽입
- _id 속성 미지정 시 자동으로 추가됨


<b>insertOne</b> : 하나의 도큐먼트를 컬렉션에 추가
```
db.collection_name.insertOne(
    <document>[, {writeConcern: <document>}]    
)
```
- writeConcern : 도큐먼트 생성 시 해당당 컬렉션이 쓰기(생성, 수정, 삭제) 작업 중이라면 어떤 영향을 줄지(concern)에 대한 것을 설정하는 옵션

반환
```
{
    "acknowledged": true,
    "insertedId": <ObjectId>
}
```

예시
```
db.products.insertOne(
    { _id: 10, "item" : "packing peanuts", "qty" : 200 }
)
```

<b>insertMany</b> : 단일 컬렉션에 여러 도큐먼트를 컬렉션에 추가
- 데이터베이스로 왕복하지 않고 한 번에 도큐먼트를 대량 삽입하여 속도가 빠름 

형식
```
db.collection_name.insertMany(
    [ <document 1>, <document 2>, ... ] 
    [,{ writeConcern: <document>,
        ordered: <boolean> } ]
)
```
- {ordered: true } : 기본값. 순서가 지정된 삽입을 수행. error가 발생하면 이후 데이터는 생성되지 않음.
- {ordered: false } : 몽고DB가 성능 개선을 위한 삽입 재배열. error가 발생하면 해당 건만 무시되고 나머지 도큐먼트는 생성됨.

반환
```
{
    "acknowledged": true,
    "insertedIds": [<ObjectId>, <ObjectId>, ... ]
}
```

예시
```
db.products.insertMany( [
    { item: "card", qty: 15 },
    { item: "envelope", qty: 20 },
    { item: "stamps" , qty: 30 }
] )
```

## Document 읽기
find, findOne 명령으로 도큐먼트를 확인
- .pretty()를 붙여 데이터를 보기 좋은 형태로 확인할 수 있음.

<b>findOne</b> : 하나의 도큐먼트를 확인

<b>find</b> : 다수의 도큐먼트를 확인

예시
```
db.movies.find().pretty()
```

## Document 삭제
deleteOne, deleteMany 명령을 통해 하나 혹은 다수의 도큐먼트 제거

```
db.collection_name.deleteOne(
    <filter>
    [, {
        writeConcern: <document>,
        collation: <document>,
        hint: <document|string>
    }]
)
```
- collation: 악센트나 대소문자 관계에 대한 언어별 규칙을 지정
- hint: 사용할 인덱스를 지정하는 도큐먼트 혹은 문자열

```
db.collection_name.deleteMany(
    <filter>
    [, {
        writeConcer: <document>,
        collation: <document>
    }]
)
```
- filter : { } 지정 시 모든 도큐먼트 제거 (이 경우 컬렉션 제거가 더 빠름)

반환
```
{
    "acknowledged": <boolean>,
    "deleteCount": <integer>
}
```

예시
```
db.orders.deleteOne( 
    { _id: ObjectId("563237a41a4d68582c2509da") }
)

db.orders.deleteMany( 
    { "stock" : "Brent Crude Futures", 
      "limit" : { $gt : 48.88 } } 
)
```

## Document 수정
### 도큐먼트 치환
<b>replaceOne</b> : 쿼리 결과에 맞는 첫 번째 도큐먼트를 지정한 새 도큐먼트로 교체
- _id 값은 바뀌지 않는다.

형식
```
db.collection_name.replaceOne(
    <filter>,
    <repalcement>
    [, {
        upsert: <boolean>,
        writeConcern: <document>,
        collation: <document>,
        hint: <document | string>
    } ]
)
```
- upsert : true 설정 시 쿼리한 도큐먼트가 없을 경우 새로운 도큐먼트를 추가. 기본값은 false.
  - 새로운 도큐먼트가 생성되면 upsertedId 값이 반환됨
- writeConcern : 복제와 샤딩이 된 상황에서 어디까지 변경점을 체크할지 설정값
    
반환
```
{ 
    "acknowledged" : <boolean>, 
    "matchedCount" : <number>, 
    "modifiedCount" : <number> 
}
```

### 도큐먼트 갱신 
- 키 변경/추가/제거, 값 변경, 배열과 내장 도큐먼트 조작
- 갱신 도큐먼트는 갱신 연산자를 포함해야 한다
  - 포함하지 않으면 에러
- 갱신은 원자적으로 이루어진다. 
- 다수의 갱신 요청 발생 시 서버에 도착한 순서대로 요청 처리
  - 동일 도큐먼트의 같은 필드에 상이한 갱신이 있다면 결과적으로 마지막 요청이 반영된다

<b>updateOne</b> : 조건에 맞는 첫 번째 도큐먼트를 찾아 update 내용을 갱신

```
db.collection_name.updateOne(
    <filter>, 
    <update>
    [, {
        upsert: <boolean>,
        writeConcern: <document>,
        collation: <document>,
        arrayFilters: [<filterdocuemnt1>, ...],
        hint: <document | string>,
        let: <document>,
        sort: <document>
    } ]
)
```
- update : 도큐먼트 또는 파이프라인으로 지정된 수정 사항
- arrayFilters : 배열 필드의 업데이트 작업을 위해 수정할 배열 요소를 결정하는 필터 문서 배열
- let : 변수 목록을 지정하며 지정된 변수는 "$$변수명"의 형식으로 사용
- sort : 지정된 조건 결과에 여러 도큐먼트가 다수인 경우, 정렬 결과의 첫 번째 도큐먼트를 갱신

<b>updateMany</b> :  조건에 맞는 모든 도큐먼트를 찾아 update 내용을 갱신

형식
```
db.collection_name.updateMany(<filter>, 
    <update>
    [, {
        upsert: <boolean>,
        writeConcern: <document>,
        collation: <document>,
        arrayFilters: [<filterdocuemnt1>, ...],
        hint: <document | string>,
        let: <document>
    } ]
)
```

예시
```
db.movies.updateOne(
    {title:"Star Wars: Episode IV - A New Hope"},
    {$set : {reviews: [] } }
)

db.cakeFlavors.updateOne(
   {
      $expr: { $eq: [ "$flavor", "$$targetFlavor" ] }
   },
   [
      { $set: { flavor: "$$newFlavor" } }
   ],
   { let: { targetFlavor: "cherry", newFlavor: "orange" } }
)

```

#### 갱신 연산자
갱신 도큐먼트는 갱신 연산자를 포함하지 않으면 Error 발생
```
> db.numbers.updateOne(
    {}, 
    {"test":"test"}
)

uncaught exception: Error: the update operation document must contain atomic operators
```

도큐먼트 수정 연산자
<table>
<thead><td>파라미터</td><td>설명</td><td>형식</td></thead>
<tr><td>$currentDate</td><td>필드의 값으로 현재 timestamp나 date 값으로 설정</td>
<td>

```
$currentDate : {<field>: <typeSpecification1>, ...}
```
</td></tr>
<tr><td>$inc</td><td>이미 존재하는 필드의 값을 증감하거나 없는 필드라면 생성<br>
숫자 타입에만 사용 가능</td><td>

```
$inc: {<field>: <증가시킬 값>, ... }
```
</td></tr>
<tr><td>$min</td><td> 필드의 값이 지정값보다 크면 지정값으로 설정</td><td>

```
$min: {<field>: <value>}
```
</td></tr>
<tr><td>$max</td><td>필드의 값이 지정값보다 작으면 지정값으로 설정</td><td>

```
$max: {<field>: <value>}
```
</td></tr>
<tr><td>$mul</td><td>기존 필드의 값과 지정된 값을 곱한 값으로 수정</td><td>

```
$mul: {<field>: <value>}
```
</td></tr>
<tr><td>$rename</td><td>필드의 이름을 새이름으로 바꾼다.</td><td>

```
$rename: {<field>: <새이름>}
```
</td></tr>
<tr><td>$set</td><td>필드값 설정(추가, (데이터 형)변경)</td><td>

```
$set: {<field>: <value>, ... }
```
</td></tr>
<tr><td>$setOnInsert</td><td>Upsert 값이 true이면서 문서를 생성할 때만 사용되는 연산자<br>쿼리와 $set 연산자에 나온 필드와 값 이외의 필드와 값을 함께 설정</td><td>

```
$setOnInsert: {<field>: <value>, ... }
```
</td></tr>
<tr><td>$unset</td><td>필드값 제거</td><td>

```
$unset : {<field>: "", ...}
```
연산자 안에서 value는 의미 없음</td></tr>
</table>

#### 도큐먼트 수정 배열 연산자
<b>배열 필터를 이용한 갱신</b> : arrayFilters 이용하여 조건에 맞는 배열 요소 변경

실행 순서
```
db.collection_name.updateMany(
    {<query conditions>}, // (1) 도큐먼트 검색
    {<update operator> : { "<array>.$[<identifier>]" : value } }, // (3) 배열 요소 수정
    {arrayFilters : [{ <identifier> : <condition> } ] } // (2) 배열 요소 검색
)
```
예시
```
db.blog.updateOne(
    {"post":post_id}, 
    {"$set": {"comments.$[elem].hidden": true} },
    {arrayFilters: [{"elem.votes": {$lte: -5}}] } // -5 이하인 도큐먼트에 대하여
)
```
<br>
<b>배열의 위치 기반 변경</b>

1) 위치를 이용하거나
   - 배열 인덱스([0, n-1])이용
   - ex) column_name.index.inner_column_name : comments.0.votes
2) 위치 연산자($) 사용
   - 위치를 모를 때 index = $로 지정
   - 첫 번째로 일치하는 요소'만' 변경

<br>
사용 예시

```
db.students.insertMany( [
   { "_id" : 1, "grades" : [ 95, 92, 90 ] },
   { "_id" : 2, "grades" : [ 98, 100, 102 ] },
   { "_id" : 3, "grades" : [ 95, 110, 110 ] }
] )

// case1. arrayFilters를 이용
db.students.updateMany(
    {},
    {$set: {"grades.$[scoreElem]":100}},
    {arrayFilters: [{scoreElem: {$gt: 100} }] }
)

// 100보다 큰 각 요소를 100으로 수정
{ "_id" : 1, "grades" : [ 95, 92, 90 ] }
{ "_id" : 2, "grades" : [ 98, 100, 100 ] }
{ "_id" : 3, "grades" : [ 95, 100, 100 ] }


// case2. 위치 연산자 이용
db.students.updateMany(
    {grades: {$gt: 100}},
    {$set: {"grades.$":100} }
)

// 100보다 큰 요소 중 첫 번째 요소만 수정
{ "_id" : 1, "grades" : [ 95, 92, 90 ] }
{ "_id" : 2, "grades" : [ 98, 100, 100 ] }
{ "_id" : 3, "grades" : [ 95, 100, 110 ] }

// 전체 위치 연산자($[]) 사용
db.students.updateMany(
    {grades: {$gt: 100}},
    {$set: {"grades.$[]":100}}
)

// 100보다 큰 요소를 가진 도큐먼트의 모든 배열 요소를 수정
{ "_id" : 1, "grades" : [ 95, 92, 90 ] }
{ "_id" : 2, "grades" : [ 100, 100, 100 ] }
{ "_id" : 3, "grades" : [ 100, 100, 100 ] }
```

배열 관련 연산자
<table>
<thead><td>파라미터</td><td>설명</td><td>형식</td></thead>
<tr><td>$addToSet</td><td>배열 안에 해당 값이 없다면 추가하고, 있다면 추가하지 않음</td><td>

```
{$addToSet: {<field1>:<value1>, ...} }
```
</td></tr>
<tr><td>$pop</td><td>배열의 첫 번째(-1) 또는 마지막(1) 요소를 제거</td><td>

```
{$pop: {<field>: <-1 | 1>, ...} }
```
</td></tr>
<tr><td>$pull</td><td>지정된 쿼리와 일치하는 모든 배열 요소를 제거</td><td>

```
{$pull: {<field>: <value | condition>, ...} }
```
<tr><td>$pullAll</td><td>배열에서 나열된 값과 일치하는 모든 값을 제거</td><td>

```
{$pullAll : {<field1>: [ <value1>, <value2>, ... ], ...} }
```
</td></tr>
<tr><td>$push</td><td>
배열이 이미 존재하면 배열 끝에 요소를 추가하고, 존재하지 않으면 새로운 배열을 생성</td><td>

```
{$push: {<field1>: <value1>, ...}}
```
</td></tr>
</table>


$push 연산자 Modifiers

<table>
<thead><td>파라미터</td><td>설명</td><td>형식</td></thead>
<tr><td>$each</td><td>$addToSet 또는 $push와 함께 사용하여 다수 요소를 추가</td><td>

```
{$addToSet: {
    <field>: {
        $each: [<value1>, <value2>, ...]
        }
    }
}

{$push: {
    <field>: {
        $each: [<value1>, <value2>, ...]
        }
    }
}
```
</td></tr>
<tr><td>$position</td><td>$push 연산자가 삽입하는 요소의 위치를 지정

- value >= 0 : 배열의 시작 부분부터 나타냄
- value < 0 : 배열의 끝 부분부터 나타냄

$each 수정자와 함께 사용

</td><td>

```
{$push: {
        <field>: {
            $each: [<value1>, <value2>, ...],
            $position: <number>
        }
    }
}
```
</td></tr>
<tr><td>$slice</td><td>$push와 함께 사용하여 배열의 길이를 제한

- value = 0 : 배열을 빈 배열로 업데이트
- value < 0 : 마지막 n개의 요소만 포함
- value > 0 : 처음 n개의 요소만 포함


$each 수정자와 함께 사용</td><td>

```
{$push:{
        <field>: {
            $each: [<value1>, <value2>, ...],
            $slice: <number>
        }
    }
}
```
</td></tr>
<tr><td>$sort</td><td>배열 요소의 순서를 재정렬<br>key가 여러 개이면 앞선 키가 우선</td><td>

```
{$push: {
        <field>: {
            $each: [<value1>, <value2>, ...],
            $sort: <1 | -1>
        }
    }
}

{$push: {
        <field>: {
            $each: [<value1>, <value2>, ...],
            $sort: {<inner_field>: <1 | -1>, ...}
        }
    }
}

```
</td></tr>
</table>
