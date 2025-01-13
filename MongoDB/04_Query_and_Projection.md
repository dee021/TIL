findOne() / find()
컬렉션에서 하나 혹은 다수의 도큐먼트를 조회한다.

형식
```
db.collection_name.findOne(
    <query>,
    <projection>,
    <options>
)

db.collection_name.find(
    <query>,
    <projection>,
    <options>
)
```
- query: 조회할 도큐먼트를 필터링
- projection: 반환할 필드를 지정
- options
  - .ReadConcern() : find 함수는 읽기 작업을 하므로 ReadConcern 옵션을 설정할 수 있음

# 쿼리
1) 값으로 쿼리하기
- 쿼리 도큐먼트에 키-값 쌍을 추가해 검색을 제한할 수 있음

쿼리 형식
```
{<field>: <value>, ... }
```

예시
```
db.student.find({class: 1})
db.student.find({class: 1, score:100})
```

2) 연산자를 이용하여 쿼리
- 연산자를 사용하여 검색을 제한할 수 있음

쿼리 형식
```
{<field>: {<operator>: <value>, ... }, ... }
```

예시
```
db.student.find({height:{$gte: 160} })
db.student.find({height:{$gte: 160, &lt:170} })
db.student.find({height:{$gte: 160}, weight:{$gte:60} })
```


<b>쿼리 조건</b>
1) 쿼리의 가장 바깥쪽 키 값으로 필드 또는 $and, $or, $nor 세 가지 연산자만을 갖는다
```
{$or: [ {status:'A'}, {qty: {$lt: 30}}] }
```
2) 만약 바깥쪽 키 값으로 필드를 적었으면 그 내부의 값으로는 꼭 연산자가 나와야 한다
```
{name : {first : 'Karoid', last:'Jeong'}} // (X)
{"name.first":'Karoid', "name.last":'Jeong'} // (O)
```

## 연산자

### 비교 연산자
<table>
<thead><td>연산자</td><td>설명</td></thead>
<tr><td>$eq</td><td>(equals) 주어진 값과 일치하는 값</td></tr>
<tr><td>$ne</td><td>(not equals) 주어진 값과 일치하지 않는 값</td></tr>
<tr><td>$gt</td><td>(greater than) 주어진 값보다 큰 값</td></tr>
<tr><td>$gte</td><td>(greater than or equals) 주어진 값보다 크거나 같은 값</td></tr>
<tr><td>$lt</td><td>(less than) 주어진 값보다 작은 값</td></tr>
<tr><td>$lte</td><td>(less than or equals) 주어진 값보다 작거나 같은 값</td></tr>
<tr><td>$in</td><td>주어진 배열 안에 속하는 값</td></tr>
<tr><td>$nin</td><td>(not in) 주어진 배열 안에 속하지 않는 값</td></tr>
</table>

- $lt, $lte, $gt, $gte
    - 숫자, 문자열(사전순), 배열, 오브젝트(sorted Key, value 순), 시간 비교 가능
    - 서로 다른 데이터 타입 사이의 크기 비교 가능
- $in, $nin
    - $in, $nin 연산자는 배열을 값으로 갖는 필드에 주로 사용
    - 해당 연산자 뒤의 값이 모두 포함/미포함되는 도큐먼트 반환
    - 배열의 요소로 정규 표현식을 넣을 수 있음

예시
```
db.users.find({"age" : {"$gte" : 18, "$lte":30}})

start = new Date("01/01/2007")
db.users.find({"registered" : {"$lt":start}}) // 2007.01.01 이전 날짜들 조회

db.users.find({"user_id": {"$in" : [12345, "joe"] }})
```

### 논리 연산자
AND, OR, NOR 연산자는 사용되는 위치가 다르며, 값으로 배열을 가짐

형식 
```
{ <field>: {$not: <operator-expression> }}
{ $and | $or | $nor : [{<field1>:<value1>}, {<field2>:<value2>}, ... ] }
```

<table>
<thead><td>연산자</td><td>설명</td></thead>
<tr><td>$and</td><td>주어진 조건을 모두 만족하는 도큐먼트 반환</td></tr>
<tr><td>$not</td><td>주어진 조건을 만족하지 않는 도큐먼트 반환</td></tr>
<tr><td>$nor</td><td>주어진 조건 중 하나라도 만족하지 않는 도큐먼트 반환</td></tr>
<tr><td>$or</td><td>주어진 조건을 하나라도 만족하는 도큐먼트 반환</td></tr>
</table>

$and와 $or
- 일반적인 AND 쿼리에는 최소한의 인수로 반환 도큐먼트의 범위를 추리는 것이 효율적이나, OR 쿼리는 첫 번째 인수가 일치하는 도큐먼트가 많을 수록 효율적

$or와 $in
 - $in는 하나의 키를 다양한 값과 비교하고 $or는 여러 키를 주어진 값과 비교하는 쿼리에 사용

$not
- 메타 조건절이며 어떤 조건에도 적용이 가능
- 정규 표현식과 함께 사용해 주어진 패턴과 일치하지 않는 도큐먼트를 찾을 때 특히 유용함


### null

null은 쿼리에 따라 다르게 사용할 수 있음

1) 필드 값이 null인 도큐먼트와 해당 필드를 갖지 않는 도큐먼트를 함께 반환
```
{<field>: null }
{<field> : {"$eq":null} }
```

2) 해당 필드가 존재하며 값이 null이 아닌 도큐먼트 반환
```
{<field> : {"$ne":null} }
```

3) 해당 필드가 존재하며 값이 null인 도큐먼트 반환
```
{<field> : {"$eq":null, "$exists":true}}
```

### 문자열 연산자
<table>
<thead><td>연산자</td><td>설명</td></thead>
<tr><td>$text</td><td>문자열 검색의 기능을 수행</td></tr>
<tr><td>$regex</td><td>값이 정규 표현식과 맞는 도큐먼트 반환</td></tr>
</table>

<b>$text</b>

형식
```
{ $text:
    {
        $search : <string>,
        $language: <string>,
        $caseSensitive: <boolean>, 
        $diacriticSensitive: <boolean> 
    }
}
```
- $search: 검색하려는 내용을 담는다.
- $language: optional. 검색하는 언어를 설정. MongoDB가 지원하는 언어를 설정하며, 설정되지 않으면 인덱스에 설정된 내용을 따른다.
- $caseSensitive: optional. 문자의 대소문자를 구분할지 정한다.
- $diacriticSensitive: optional. 알파벳에 붙는 성조를 무시할지 정한다. false 값을 기본값으로 갖는다.

특징
- 문자열 인덱스가 존재하는 필드에만 사용
- 옵션으로 지정하지 않으면 대소문자를 구분하지 않으며, 지원 언어에 대하여 해당 단어의 원형을 이용하여 검색 가능
    - shopped 검색 -> shop, shopping, shopped 등 원형이 같은 대상을 모두 검색
    - 한글은 공식 지원하지 않는다.
- 띄어쓰기는 OR 연산 대상이 된다.
  - \\" 사이에 문구를 넣으면 띄어쓰기 자체도 검색 대상으로 봄

예시
```
{$text: {$search: "coffee shop" }} // "coffee" 혹은 "shop"을 포함하는 도큐먼트 반환
{$text: {$search: "\"coffee shop"\" }} // "coffee shop"을 포함하는 도큐먼트 반환
```

<b>$regex</b>

패턴 일치 문자열(pattern match string)을 위한 정규식 기능을 제공

형식
```
{ <field> : {$regex : /pattern/, $options: '<options>'} }
{ <field> : {$regex : 'pattern', $options: '<options>'} }
{ <field> : {$regex : /pattern/<options>} }
```
- options : 정규 표현식 플래그는 사용할 수 있지만 꼭 필요하지는 않다.
    - i : Ignore case. 대소문자 무시
    - m : Multi Line. 앵커(^) 사용시 \n 무력화
    - x : 정규식 내의 공백 무시
    - s : .을 사용할 때 \n 포함해서 매치
    - g : global. 문자열 내의 모든 패턴 검색
    - u : unicode. 유니코드 전체를 지원
    - y : sticky. 문자 내 특정 위치에서 검색을 진행하는 sticky 모드 활성화

예시
```
{city: {$regex: /시$/} } 
// 시로 끝나는 지역명 검색

{city: {$regex: /시$/, $gte:"아", $lt:"자"} } 
// 시로 끝나며 초성이 'ㅇ'으로 시작하는 지역명 검색
```
