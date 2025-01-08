## Collection
몽고DB의 일반적인 컬렉션은 동적으로 생성되고 추가적인 데이터에 맞춰 크기가 자동으로 늘어난다.

### 컬렉션 상태 조회
- db.collection_name.isCapped() : 컬렉션이 Capped 컬렉션이면 true 반환
- db.collection_name.latencyStats() : 컬렉션의 지연 시간 통계를 보여준다.
- db.collection_name.stats() : 데이터베이스의 stats와 같이 컬렉션의 크기, 도큐먼트 개수 및스토리지 엔진의 통계를 제공
- db.collection_name.storageSize() : 컬렉션 스토리지의 크기를 반환
- db.collection_name.totalIndexSize() : 컬렉션의 인덱스 크기를 반환
- db.collection_name.totalSize() : 컬렉션의 스토리지와 인덱스 크기의 합을 반환

### 컬렉션 조작
<b>생성</b> : 도큐먼트 삽입 시 생성됨<br>
<b>수정</b>
- db.collection_name.renameCollection(new_name) : 컬렉션 이름 변경


<b>삭제</b>
- db.Collection_name.drop() : 컬렉션을 비우고 인덱스를 재생성

## 제한 컬렉션 
: 미리 생성되어 크기가 고정된 컬렉션
- circular queue와 같이 동작(FIFO)
- 도큐먼트는 삭제할 수 없으며 크기가 커지는 갱신이 불가능
  - 제한 컬렉션은 샤딩될 수 없음
- 회전 디스크에서 쓰기를 다소 빠르게 수행
- 로그 데이터나 일정 시간 동안만 보관하는 통계 데이터를 보관할 때 유용
- 실제 물리적인 저장장치에 저장된 값(storageSize)는 실제 size보다 더 클 수 있음(컬렉션에 대한 정보를 함께 저장하므로)

<b>생성</b>
```
db.createCollection(
  "collection_name",
  {
    capped: true, size: number(byte)[, max: number]
  }
)
```
<table>
<tr>
<td>size</td>
<td>컬렉션의 크기를 제한<br>
값이 4096 이하이면 4096, 이상이면 256k (k는 정수)<br>
최대 크기는 5,242,880byte
</td>
</tr>
<td>max</td>
<td>최대 도큐먼트 수를 제한
</td>
</tr>
</table>
  

<b>일반 컬렉션을 제한 컬렉션으로 변환</b>
```
db.runCommand({
  "converToCapped":"collection_name",
  "size":size
});
```
