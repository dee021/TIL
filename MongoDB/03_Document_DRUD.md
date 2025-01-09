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
