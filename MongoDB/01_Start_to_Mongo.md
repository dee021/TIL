## 접속
<b>Windows</b><br>
1) 데몬 실행
```
> mongod
```
2) 새 터미널로 접속
```
> mongosh
```

<b>Ubuntu</b><br>
1) 데몬 실행
```
$ service mongodb start
```
2) 접속
```
$ mongo [-u username -p password]
```

## 기본 명령어
- db : 현재 연결된 데이터베이스 확인
- show dbs : 데이터베이스 목록 확인
- show collections : 현재 연결된 데이터베이스에 존재하는 컬렉션 목록 확인

## 예약된 데이터베이스
- admin : 인증(authentication)과 권한 부여(authorization) 역할
- local : 단일 서버에 대한 데이터 저장
- config : 샤딩(sharding)된 몽고DB 클러스터는 config 데이터베이스를 사용해 각 샤드(shard)의 정보를 저장

## .mongorc.js 파일
- 자주 로드되는 스크립트를 정의
- 전역 변수 설정, alias 정의, 내장 함수 재정의(orverride)
- --norc 옵션을 사용하여 셸 시작 시 .mongorc.js 로딩을 비활성화할 수 있음

사용 예시
```
var no = function() {
        print("Not on my watch.");
};

// 데이터베이스 삭제 방지
db.dropDatabase = DB.prototype.dropDatabase = no;

// 컬렉션 삭제 방지
DBCollection.prototype.drop = no;

// 인덱스 삭제 방지
DBCollection.prototype.dropIndex = no;

// 인덱스 삭제 방지
DBCollection.prototype.dropIndexs = no;
```

## 몽고DB
- ordered insert가 기본값
- 삽입된 데이터에 최소한의 검사를 수행
    - "_id" 필드 존재 여부를 확인하여 없으면 추가
    - 모든 도큐먼트는 16MB보다 작아야 함(최대 크기는 나중에 커질 수 있음)
- 요청 반환 결과가 32MB 이상인 경우 sort() 지정을 거부하는 오류를 내보냄

### 데이터형 비교 순서
 : 동일한 키에 대하여 데이터형이 섞여 있는 경우 우선순위는 아래와 같음
- 최솟값
- null
- 숫자
- 문자열
- 객체/도큐먼트
- 배열
- 이진 데이터
- 객체 ID : ObjectId 타입으로 _id 자동 생성 ()
 - ObjectId('6746b718c7f88bce1f0d8190')
    - 6746b718(유닉스 시간 4byte) c7f88b(기기id 3byte) ce1f(프로세스id 2byte) 0d8190 (카운터 3byte)
    - ObjectId.getTimestamp() : return ISODate
- 불리언
- 날짜 : Date() : ISODate('2024-11-27T06:02:33.292Z')(ISODate Object)
- 타임스탬프 : Timestamp(유닉스 시간, 같은 유닉스 시간내에서 몇번째인지) : 하나의 mongod 인스턴스 내에서 값이 항상 유일하게 설계됨
- 정규 표현식
- 최댓값
