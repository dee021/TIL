## 파일 시스템
File System : 운영체제가 파티션이나 디스크에 데이터를 저장하고 읽고 쓰고 찾기 위해 구성하는 일련의 체계

<b>파일 시스템의 기능</b>
- 사용자가 파일을 생성(Create), 수정(Modify), 삭제(Delete)할 수 있도록 제공
- 사용자가 파일을 사용하기 적합한 형태의 구조로 구성하고, 다양한 추가 정보 제공
- 다른 사용자와의 파일을 공동으로 사용할 수 있는 적절한 제어 방법 제공
- 파일 공유를 위하여 판독 접근, 기록 접근, 수행 접근 등의 다양한 접근 제어 방법 제공
- 정보 손실이나 파괴를 방지하기 위하여 백업(Backup)이나 복구(Recovery)를 위한 기능 준비
- 사용자와 장치 간의 독립성(device independence)을 유지하기 위해, 물리적인 장치 이름 대신 적절한 이름 제공
- 정보가 안전하게 보호되고 비밀이 보장될 수 있도록 정보의 암호화(encryption) 및 복호화(decryption) 기능 제공
- 사용자가 파일이나 디렉터리에 접근하기 쉬운 인터페이스 및 명령어 제공

### 리눅스 파일 시스템
<table>
<tr><td>minix</td><td>
<ui><li>Minix 운영체제에 사용된 파일 시스템으로, 초기 리눅스 파일 시스템의 모태<li>파티션 사이즈 64MB 제한, 파일 이름 14까지 지원<li>단일스탬프(Timestamp) 체제</ui>
</td></tr>
<tr><td>ext</td><td><ui><li>유닉스 파일 시스템 UFS 구조를 기반으로 Minix를 개선<li>최대 파티션 크기 2GB와 파일명 255자까지 가능<li>파일 접근에 대한 타임스탬프, 아이노드 수정 등은 지원하지 않음</ui></td></tr>
<tr><td>ext2</td><td><ui><li>ext의 아이노드 불변성과 타임스탬프 수정 문제를 해결<li>이론적으로 1K 블록을 사용했을 경우 최대 16GB의 단일 파일 생성, 최대 4TB의 파일 시스템으로 구성할 수 있으나, 응용 프로그램과의 호환성 문제로 2GB까지의 단일 파일생성만 지원</ui></td></tr>
<tr><td>ext3</td><td><ul><li>4K 블록을 사용했을경우 최대 2TB의 단일 파일 생성, 16TB의 파일 시스템 구성이 가능<li>저널링 지원<li>ACL(Access Control List)를 통한 접근 제어 지원<li>ext2와 대부분 호환 가능</ul></td></tr>
<tr><td>ext4</td><td><ul><li>ext2, ext3와 호환성이 있는 확장 버전
<li>대형 파일 시스템 관련 기능이 강화됨
  
  - 64비트 기억 공간 제한을 없애고, 최대 1EB의 디스크 볼륨과 16TB의 파일을 지원</ul>
</td></tr>
</table>

#### 저널링 파일 시스템
파일 시스템에 대한 변경사항을 반영하기 전에 저널이라 부르는 로그에 변경사항을 저장하여 추적이 가능하게 만든 파일 시스템
- 시스템에 충돌현상이 발생하거나 전원 문제가 발생된 경우 데이터 복구 확률을 높임
- 파일을 실제로 수정하기 전에 로그에 수정된 내용을 저장해서 비정상적으로 동작이 멈추었더라도 시스템 복구를 위해 단지 로그만 검사하면 됨
  - 로그 정보를 바탕으로 수정 내용을 적용하므로 속도와 복구의 안정성면에서 뛰어난 성능
- ext3, ext4, XFS, JFS, ReiserFS 등이 이에 해당

#### ext(Extended File System) 파일 시스템 구조

일반적으로 디스크 드라이브 이용 시 파티션을 분할하고 포맷을 통해 파일 시스템을 생성

![mkfs](https://github.com/dee021/TIL/blob/linuxmaster/Linux/img/mkfs.jpg)

저장되는 정보는 Directory Blocks and Data Blocks 영역에 저장됨
- 디렉터리 블록에는 아이노드 번호와 파일명이 저장됨
- 데이터 블록에는 파일이 보관해야할 정보를 저장됨

![dataBlocks](https://github.com/dee021/TIL/blob/linuxmaster/Linux/img/datablocks.jpg)

> <b>아이노드(Inode)</b>
> 전통적인 유닉스 계열 시스템에서 사용하는 일종의 자료 구조<br>
> 각각의 파일은 하나의 아이노드를 할당받아 관리된다.
>
> 아이노드 넘버(Inode Number), 접근 모드(읽기, 쓰기, 실행 권한), 파일 형식(파일, 디렉터리, 블록 및 캐릭터 디바이스 등), 소유자 정보(소유자와 그룹에 대한 식별자), 파일 크기, 타임스탬프 등의 정보를 저장

<b>etx2 파일 시스템</b>

![ext2](https://github.com/dee021/TIL/blob/linuxmaster/Linux/img/ext2.jpg)
- 슈퍼 블록(Super Block) : 파일 시스템에 대한 전체적인 정보를 담고 있음.
  - 매직 넘버(Magic Number) : 특정 파일 시스템임을 알리는 정보. ext 파일 시스템인 경우 0xEF53
  - 마운트 정보, 전체 아이노드 수 및 남은 수, 전체 블록 수 및 남은 수, 블록 그룹 번호, 블록 크기, 그룹 당 블록 수
- 그룹 기술자(Group Descriptor) : 각각의 블록 그룹을 기술하는 자료 구조
  - 블록 비트맵, 아이노드 비트맵, 아이노드 테이블(아이노드 블록)
- 블록 비트맵(Block Bitmap) : 블록의 사용 현황을 Bit로 표현
  - 각 디스크 블록 당 하나의 비트가 할당되어 관리됨
  - 해당 블록이 자유 공간이면 1, 할당되어 있으면 0으로 표기
- 아이노드 비트맵(Inode Bitmap) : 하이노드 할당 상태를 Bit로 표현 
- 아이노드 테이블(Inode Table, 아이노드 블록) : 아이노드에 대한 정보가 들어있는 영역
  - 'ls -l' 명령 실행 시나타내는 정보인 파일 유형, 접근 권한, 하드링크 수, 소유자, 소유 그룹명, 파일 크기, 날짜 관련 정보, 파일명 등을 저장
- 데이터 블록 : 파일이 보관해야 하는 정보를 저장하는 영역으로 파일의 데이터가 존재
- 간접 블록(Indirection Block) : 추가적인 데이터 블록을 위한 포인터들이 사용할 동적으로 할당되는 공간
- 홀(Hole) : 아이노드나 간접 블록 안의 데이터 블록의 주소로 특별한 값을 저장
  - 파일 시스템에 의해 파일 안에 자리하나, 실질적으로 디스크 상에 공간을 할당되지 않음

#### XFS
주요 특징
- B+ 트리 구조를 사용하는 파일 시스템
- 빠른 복구를 위해 메타데이터 저널링을 지원한다.
- 마운트되어 활성화된 상태로 조각 모음 및 확장 지원한다.
- 데이터 읽기/쓰기 트랜잭션으로 인한 성능 저하를 최소화한다. XFS의 저널링 구조와 알고리즘은 트랜잭션에 대한 로그 기록을 신속하게 할 수 있도록 최적화되어 있다.
- raw I/O 성능에 가까운 성능을 낼 수 있는 뛰어난 처리량을 보인다.
- 완전한 64비트 파일 시스템이기 때문에 높은 확장성을 제공한다.

- 최대 16EB의 디스크볼륨과 8EB의 파일 지원
- 레드햇 계열인 RHEL 7의 기본 파일시스템으로 제공 

<b>XFS의 할당 체계</b><br>
XFS는 익스텐트(extent) 기반 할당을 사용하여 지연 할당 및 명시적인 사전 할당과 같은 여러 할당 체계를 갖고 있음

- 지연 할당은 파일이 연속적인 블록 그룹에 기록될 가능성을 높이는 것으로 단편화를 줄이고 성능을 향상시킴
- 사전 할당은 애플리케이션이 사전에 기록해야 할 데이터양을 알고 있는 경우 완전히 조각화하는 것을 방지하는 데 사용

<b>XFS 데몬</b><br>
XFS는 기본적으로 xfssyncd, xfsbufd, xfddatad, xfslogd 4 종류의 데몬을 이용

- xfssyncd : 로그 정보와 메타 데이터 정보들을 기록하는 역할 수행
- xfsbufd : I/O요청 처리
- xfsdatad, xfslogd : 작업 큐에 수행할 작업이 있는 경우 관련 함수를 호출하여 수행하는 역할


# 파일 시스템 관리
## 파일 및 디렉터리 관리

#### 소유권(Ownership)과 허가권(Permission)

여러 사용자가 동일한 저장 공간인 하드 디스크를 공유하여 사용하는 구조는 각 사용자들이 생성한 파일이나 디렉터리가 다른 사용자에게 노출될 수 있음

리눅스는 소유권과 허가권으로 접근 제어를 하도록 설계됨

- 소유권(Ownership) : 어떠한 파일이나 디렉터리를 소유하여 지배하는 권리
  - 사용자 소유권과 그룹 소유권으로 나누어 설정
- 허가권(Permission) : 파일이나 디렉터리에 접근 권한을 설정하는 권리
  - 사용자(user), 그룹(group), 다른 사용자(other)로 나누어 설정
- 일반적으로 소유권을 가진 사용자가 허가권을 설정
- 권한의 설정은 `ls -l` 명령으로 확인 가능

![ls-l](https://github.com/dee021/TIL/blob/linuxmaster/Linux/img/lsL.jpg)

> 1 -rw-rw-r--. : 허가권<br>
> - 파일의 타입(1자리) + user 권한(3자리) + group 권한(3자리) + other 권한(3자리)<br>
> 
> 2 master : 소유권을 가진 사용자<br>
> 3 master : 그룹 소유권<br>

<table>
<thead><td>권한</td><td>파일</td><td>디렉터리</td></thead>
<tr><td>r<br>(read)</td><td>파일의 내용을 볼 수 있는 권한</td><td>디렉터리 내부의 내용을 볼 수 있는 권한</td></tr>
<tr><td>w<br>(write)</td><td>파일의 내용을 수정할 수 있는 권한</td><td>디렉터리 내부에 파일을 생성 또는 삭제할 수 있는 권한</td></tr>
<tr><td>x<br>(execute)</td><td>실행 파일을 실행시킬 수 있는 권한</td><td>디렉터리 내부로 접근할 수 있는 권한</td></tr>
</table>

### 특수 권한
일부의 권한 허용 등을 위한 특별한 권한을 제공

1. Set-UID
   - 보통 실행 파일에 사용됨
     - 소유자 권한 부분의 x자리에 s로 표기
     - 실행 권한이 없는 파일에 부여하면 대문자 S 표기
   - Set-UID가 부여된 파일을 실행 시, 해당 파일을 실행하는 동안에는 파일의 소유자 권한으로 인식

2. Set-GID
   - 파일에 설정되어 있을 경우, 해당 파일을 소유한 그룹 권한으로 인식
   - 디렉터리에 설정된 경우, 하위 디렉터리 및 파일 생성 시 사용자가 속한 그룹에 상관없이 디렉터리 소유 그룹 권한으로 생성됨
   - 그룹 소유권 부분에서 x자리에 s로 표기
   - 실행 권한이 없는 경우 X로 표기

3. Sticky-Bit
   - 디렉터리에 설정되는 특수 권한으로 일종의 공유 디렉터리로 사용됨
   - 리눅스에서는 /tmp 디렉터리에 기본적으로 설정되어 있음
   - 파일 생성에는 제한이 없지만, 파일 삭제는 본인이 생성한 파일 이외에는 불가능
   - Group과 Other 계층 사용자에게 사용할 수 있으나 두 계층 함께 사용은 불가능
   - 권한 설정은 other 계층에 설정하도록 되어있음
   - other 계층 권한 부분의 x자리에 t로 표기
   - other 계층에 실행 권한이 없는 경우(그룹의 공유 모드로 사용한 경우) T로 표기
>   - 특정 응용 프로그램이 다른 응용 프로그램에서 생성한 파일을 삭제하지 못하도록 하는 권한 설정 

<b>특수 권한 설정</b>

- chmod 명령을 사용
- 문자 모드 : Set-UID, Set-GID는 s, Sticky-bit은 t를 사용
- 숫자 모드 : 천의 자리를 사용하며 Set-UID는 4, Set-GID는 2, Sticky-bit은 1을 사용


#### 권한 관련 명령어

<b>chmod(change mode)</b>

- 파일이나 디렉터리에 접근할 수 있는 허가권을 설정하는 명령
- 알파벳 기호를 사용하는 문자 모드(Symbolic mode)
  - 권한 : r, w, x
  - 사용자 : u(user), g(group), o(other), a(all)
  - 설정 : +(권한 부여), -(권한 해제), =(특정 권한만 지정)
- 8진수의 값을 사용하는 숫자 모드(Numeric mode)로 지정
  - 4(읽기 권한), 2(쓰기 권한), 1(실행 권한), 0을 조합하여 8진수 값을 만들어 권한 설정
    
```
chmod [option] mode file(s)
```

주요 옵션
<table>
<tr><td>-R</td><td>하위 디렉터리를 포함하여 디렉터리 내부의 모든 파일의 접근 권한을 변경(--recursive)</td></tr>
<tr><td>-c</td><td>변경된 정보 출력(--changes)</td></tr>
<tr><td>-f</td><td>중요한 오류 메시지가 아니면 출력하지 않음(--silent, --quite)</td></tr>
<tr><td>-v</td><td>명령 진행 결과에 대한 정보를 자세히 출력(--verbose)</td></tr>
</table>

사용 예
```
모든 사용자에게 모든 권한 부여
$ chmod ugo+rwx aa.txt
$ chmod a+rwx aa.txt
$ chmod 777 aa.txt

현재 디렉터리 안의 모든 파일을 다른 사용자의 읽기 및 쓰기 권한을 해제
$ chmod o-r, o-w *
$ chmod o-rw *

사용자와 그룹은 기존의 권한을 유지하고 다른 사용자는 읽기 권한만 허용
$ chmod o=r /etc/fstab

모든 사용자에게 어떠한 권한도 허용하지 않음
$ chmod a= a.txt
$ chmod 0 a.txt
```

<b>chown(change owner)</b>

- 파일이나 디렉터리의 소유권 및 그룹 소유권을 변경하는 명령

```
chown [option] owner[:group] file(s)
```

주요 옵션
<table>
<tr><td>-R</td><td>하위 디렉터리를 포함하여 내부의 모든 파일의 소유 권한을 변경(--recursive)</td></tr>
<tr><td>-c</td><td>벼경된 정보를 출력(--changes)</td></tr>
<tr><td>-f</td><td>중요한 오류 메시지가 아니면 출력하지 않음(--silent, --quite)</td></tr>
<tr><td>-v</td><td>명령 진행 결과에 대한 정보를 자세히 출력(--verbose)</td></tr>
</table>

사용 예
```
하위 디렉터리를 포함하여 현재 디렉터리의 모든 파일의 소유자를 alin으로 변경
# chown -R alin *

a.txt 파일의 소유자는 alin, 소유 그룹은 yuloje로 설정
# chown alin:yuloje a.txt
# chown alin.yuloje a.txt

파일의 소유자를 UID가 1004인 사용자로 설정
# chown 1004 abc.txt
```

<b>chgrp(change group)</b>

- 파일이나 디렉터리의 소유 그룹을 바꾸는 명령
- 특정 사용자가 여러 그룹에 속한 경우 본인 소유의 파일을 본인이 속한 그룹 내에서 소유권 변경이 가능

```
chgrp [option] group file(s)
```

주요 옵션
<table>
<tr><td>-R</td><td>하위 디렉터리를 포함하여 디렉터리 내부의 모든 파일의 접근 권한을 변경(--recursive)</td></tr>
<tr><td>-c</td><td>변경된 정보를 출력(--changes)</td></tr>
<tr><td>-f</td><td>중요한 오류 메시지가 아니면 출력하지 않음(--silent, --quite)</td></tr>
<tr><td>-v</td><td>명령 진행 결과에 대한 정보를 자세히 출력(--verbose)</td></tr>
<tr><td>-h</td><td>심볼릭 링크(Symbolic Link) 파일의 그룹 소유권 변경(--no-dereference)<br>해당 옵션이 없으면 원본 파일의 그룹 소유권이 변경된다.</td></tr>
</table>

<b>umask</b>

- 파일이나 디렉터리 생성 시 부여되는 기본 허가권 값을 지정하는 명령
- 파일 생성 시에는 (666 - 설정된 umask 값)을 기본 허가권으로 지정
- 디렉터리인 경우에는 (777 - 설정된 umask 값)을 기본 허가권으로 지정

```
umask [option] [값]
```

사용 예
```
설정된 umask 값을 확인. -S옵션을 사용하면 문자로 출력
$ umask
$ umask -S

umask 값을 011로 변경
$ umask 011
$ umask u=rwx,g=rw,o=rw
```

### 파일 링크(link)

- 리눅스에서 파일이나 디렉터리는 부여된 I-node(Index node) 번호를 기준으로 관리됨
- 파일명이 다르더라도 I-node 번호가 같으면 내부적으로 같은 파일로 인식
- <b>Link : </b>하나의 파일을 여러 개의 이름으로 관리하거나 디렉터리의 접근 경로를 단축하는 형태
- 링크는 크게 하드 링크(Hard Link)와 심볼릭 링크(Symbolic Link, Soft Link)로 나뉨


<b>하드 링크</b>

- 하나의 동일한 파일을 디스크의 다른 곳에 배치하여 여러 이름으로 사용되는 형식
- 'ls -i'명령으로 확인하는 원본과 링크 파일의 I-node 번호가 같음
- 링크 파일 생성 시 마다 'ls -l' 명령 시 출력되는 I-node 번호가 1씩 증가
- 원본이나 링크 파일 중 어떠한 파일을 수정해도 같이 반영됨
- 원본 파일을 삭제해도 링크 파일은 아무런 영향을 받지 않음
- 하드 링크는 파일만 설정 가능하고, 동일한 파일 시스템에서만 사용 가능
- 파티션이나 디스크 드라이브를 가로질러 사용할 수 없음

<b>심볼릭 링크</b>

- 하나의 파일을 여러 이름으로가리키게 하는 것
- 원본과 생성된 링크 파일은 완전히 다른 파일로 관리됨
- 'ls -i'로 확인하는 원본과 링크 파일의 I-node 번호가 다름
- 생성된 링크 파일의 크기가 매우 작음
- 원본이나 링크 파일 중 어떠한 파일을 수정해도 같이 반영
- 원본 파일을 삭제하면 링크 파일은 아무런 구실을 하지 못함
- 디렉터리에 링크 파일을 생성하면 윈도우의 바로가기나 단축아이콘의 기능과 같음
- 생성되는 링크 파일의 퍼미션 값이 777로 표시되나, 원본 파일의 퍼미션과는 무관함

<b>ln</b>

- 하드 링크 또는 심볼릭 링크를 생성하는 명령
- 옵션 없이 실행하면 하드 링크를, -s 옵션을 사용하면 심볼릭 링크를 생성

```
ln [option] 원본 대상파일명
```

주요 옵션
<table>
<tr><td>-s</td><td>심볼릭 링크 생성(--symbolic)</td></tr>
<tr><td>-v</td><td>링크 만드는 정보를 자세히 출력(--verbose)</td></tr>
<tr><td>-f</td><td>링크 파일 존재 시 삭제하고 생성(--force)</td></tr>
</table>

## 파일 시스템 관리 및 복구
### 파일 시스템 생성
파일 시스템 생성 작업 순서
> 디스크 장착 
> 
> → 디스크 인식 여부 확인 `fdisk -l`
> 
> → 파티션 작업 `fdisk 장치명`
> 
> → 시스템 재부팅 `reboot` 또는 `partprobe`
> 
> → 파일 시스템 생성 `mkfs -t type 장치명`
> 
> → 디렉터리(마운트 포인트) 생성 `mkdir 디렉터리명`
> 
> → 마운트 작업 `mount -t type 장치명 마운트디렉터리`
> 
> → 마운트 `mount` 및 용량 확인 `df -h`
> 
> → 부팅 시에 자동 마운트 되도록 /etc/fstab 파일에 등록
> `vi /etc/fstab`<br>
> editor : `장치명 디렉터리 fs유형 defaults 1 1`

#### fdisk
- 디스크 파티션을 확인하고 추가/삭제하는 명령
- 설정 후에는 반드시 재부팅을 해야 함

![fdiskL](https://github.com/dee021/TIL/blob/linuxmaster/Linux/img/fdiskL.png)

```
fdisk [option] [장치파일명]
```

주요 옵션
<table>
<tr><td>-l [장치파일명]</td><td>지정한 장치 파일의 파티션 테이블 정보를 출력<br>
장치 파일명을 명시하지 않으면 /proc/partitions의 정보를 기반으로 장착된 디스크의 파티션 테이블 정보를 출력</td></tr>
<tr><td>-s partition</td><td>특정 파티션의 크기를 출력(단위: block)</td></tr>
</table>


![fdisk](https://github.com/dee021/TIL/blob/linuxmaster/Linux/img/fdisk.png)

fdisk 명령을 실행하면 텍스트 기반의 특정 명령어를 입력해야 파티션을 설정할 수 있음

fdisk 실행 시 주요 명령
<table>
<tr><td>m</td><td>사용 가능한 명령의 목록 출력</td></tr>
<tr><td>d</td><td>파티션 삭제</td></tr>
<tr><td>n</td><td>파티션을 새롭게 생성(추가)</td></tr>
<tr><td>t</td><td>파티션의 속성 변경<br>
주요 코드 : 82(Swap), 83(Linux), 8e(Linux LVM), fd(Raid) 등</td></tr>
<tr><td>w</td><td>변경된 파티션의 정보를 저장하고 종료</td></tr>
<tr><td>q</td><td>변경된 파티션의 정보를 저장하지 않고 종료</td></tr>
</table>

#### mkfs(make filesystem)
- 새로운 파일 시스템을 만드는 명령으로 root만 사용 가능
- 파일 시스템 유형을 지정하지 않으면 ext2로 생성

```
mkfs [-t fs_type] [option] 장치명
```

주요 옵션
<table>
<tr><td>-t fs_type</td><td>파일 시스템의 유형 지정<br>리눅스 파일 시스템인 ext3, ext4, xfs 등을 지정하거나 지정하지 않고 ext2로 생성할 수 있음</td></tr>
<tr><td>-c</td><td>bad block을 체크한 후 파일 시스템을 구축한다</td></tr>
<tr><td>-v</td><td>결과를 상세히 출력</td></tr>
</table>

> 최근 리눅스 배포판에서는 파일 시스템 생성 시 명령어 사용법을 간소화할 수 있는 mkfs.ext2, mkfs.ext3, mkfs.ext4 등이 추가로 제공된다.

#### mke2fs
- ext2, ext3, ext4 파일 시스템을 만드는 명령
- 최근 리눅스 배포판에서 mkfs 명령 실행 시 실제 사용되는 명령어
- 파일 시스템의 유형을 지정하지 않으면 ext2로 생성

```
mke2fs [option] 장치명
```

주요 옵션
<table>
<tr><td>-j</td><td>저널링(journaling) 파일 시스템인 ext3으로 생성</td></tr>
<tr><td>-t fs_type</td><td>파일 시스템의 유형 지정</td></tr>
<tr><td>-b block_size</td><td>블록 사이즈를 지정하는 옵션<br>1024, 2048, 4096으로 지정</td></tr>
<tr><td>-R raid_options</td><td>RAID 관련 옵션<br>-R 다음에 argument=값 형태로 지정</td></tr>
<tr><td>-T usage_type</td><td>I-node 크기 지정<br>RHEL 4까지는 news(4k), largefile(1M), largefile4(4M) 지정 가능<br>RHEL 5에서는 /etc/mke2fs.conf 크기를 지정할 수 있음</td></tr>
</table>

#### mkfs.xfs
- XFS 파일 시스템을 만드는 명령
- mkfs 명령으로 -t xfs를 지정하면 이 명령이 실행됨

```
mkfs.xfs [option] 장치명
```

주요 옵션
<table>
<tr><td>-b block_size</td><td>블록 사이즈를 지정하는 옵션<br>최소 512(2^9)에서 최대 65536(2^16) 바이트로 지정 가능<br>기본값은 4096 바이트</td></tr>
<tr><td>-f</td><td>지정한 디바이스에 파일 시스템이 존재하는 경우 강제로 수행</td></tr>
</table>

#### mount
- 보조 기억장치(HDD, FDD, CD-ROM 등)나 파일 시스템이 다른 디스크를 /의 하위 디렉터리로 연결하여 사용 가능하게 하는 명령
- 특정 옵션 없이 mount 명령 시 현재 마운트된 장치나 디스크를 확인

```
mount [option] [device] [directory]
```

주요 옵션
<table>
<tr><td>-a</td><td>/etc/fstab에 명시된 파일 시스템을 마운트할 때 쓰이는 옵션</td></tr>
<tr><td>-t fs_type</td><td>파일 시스템의 유형을 지정하는 옵션<br>지정하지 않으면 /etc/fstab 파일을 참조</td></tr>
<tr><td>-o 항목</td><td>마운트할 때 추가적인 설정을 적용할 때 사용하는 옵션<br>다수의 조건을 적용할 때는 콤마(,)로 구분</td></tr>
</table>

주요 파일 시스템의 유형
<table>
<tr><td>msdos</td><td>MS-DOS의 파일 시스템은 FAT-16을 마운트할 때 지정</td></tr>
<tr><td>vfat</td><td>마이크로소프트사의 파일 시스템인 FAT-32를 마운트할 때 지정</td></tr>
<tr><td>ntfs</td><td>마이크로소프트사의 윈도우 NT, 2000, XP 이후 버전에서 사용하는 파일 시스템을 마운트할 때 지정</td></tr>
<tr><td>ext2, ext3, ext4</td><td>ext2, ext3, ext4 파일 시스템을 마운트할 때 지정</td></tr>
<tr><td>xfs</td><td>xfs 파일 시스템을 마운트할 때 지정</td></tr>
<tr><td>iso9660</td><td>CD-ROM이나 DVD를 마운트할 때 지정</td></tr>
<tr><td>smbfs</td><td>네트워크 파일 시스템인 삼바 파일 시스템을 마운트할 때 지정</td></tr>
<tr><td>cifs</td><td>삼바 파일 시스템이 확장된 파일 시스템</td></tr>
<tr><td>nfs</td><td>네트워크 파일 시스템인 NFS로 공유된 영역을 마운트할 때 지정</td></tr>
<tr><td>udf</td><td>DVD 파일 시스템으로 대부분의 배포판 리눅스에서 iso9660으로 지정해도 마운트할 수 있음</td></tr>
</table>

-o의 주요 항목
<table>
<tr><td>ro</td><td>읽기전용으로 마운트</td></tr>
<tr><td>rw</td><td>읽기/쓰기 모드로 마운트하며 기본값으로 설정되어 있음</td></tr>
<tr><td>remount</td><td>해당 파티션을 다시 마운트하며, 파티션 정보를 바꾸었을 때 사용</td></tr>
<tr><td>loop</td><td>loop 디바이스로 마운트할 때 지정<br>CD-ROM 이미지 파일인 iso를 마운트하여 사용할 때 씀</td></tr>
<tr><td>noatime</td><td>파일이 변경되기 전까지 Access Time을 변경하지 않음<br>이 옵션을 사용하면 시스템의 작업을 줄여 성능이 향상되고, 노트북에서 사용하면 디스크 접근에 따른 배터리 시간을 늘림 </td></tr>
<tr><td>username=사용자명</td><td>삼바처럼 사용자 계정이 필요한 경우 사용자명을 입력</td></tr>
<tr><td>acl</td><td>ext3 파일 시스템에서 지원하는 접근 제어 리스트(Access Control List)를 사용 가능 하도록 마운트할 때 사용</td></tr>
</table>

주요 디바이스 파일명
<table>
<tr><td>FDD</td><td>/dev/fd0, /dev/fd1 등</td></tr>
<tr><td>CD-ROM 및 DVD</td><td>/dev/cdrom, /dev/dvd, /dev/sr0, /dev/hdc, /dev/sdb 등</td></tr>
<tr><td>IDE HDD</td><td>/dev/hda1, /dev/hdb1 등</td></tr>
<tr><td>USB Memory, SCSI HDD, S-ATA HDD</td><td>/dev/sda, /dev/sdb1 등</td></tr>
</table>

사용 예
```
# mount -t ext4 -o ro /dev/sdb1 /mnt
파일 시스템이 ext4인 /dev/sdb1을 /mnt 디렉터리에 읽기 전용으로 마운트

# mount -t iso9660 -o ro,loop /root/CentOS-7-x86_64-DVD-2009.iso /media
CentOS-7-x86_64-DVD-2009.iso라는 CD 이미지 파일을 읽기전용 및 루프백 장치로 /media 디렉터리로 마운트
```

#### umount
- (unmount) 마운트된 파일 시스템을 해제시켜주는 명령
- 특히 CD-ROM이나 DVD-ROM이 마운트된 상태에서 이 명령을 사용하지 않으면 안에 들어있는 디스크를 꺼낼 수 없음
- Floppy, USB 메모리 등의 보조기억장치 사용 후 반드시 이 명령을 사용한 뒤에 제거해야 데이터의 손실이 없음

```
umount [option] 디바이스명
umount [option] 마운트된_디바이스명
```

주요 옵션
<table>
<tr><td>-a</td><td>/etc/mtab에 명시된 파일 시스템을 언마운트할 때 쓰이는 옵션</td></tr>
<tr><td>-t fs_type</td><td>언마운트할 파일 시스템을 지정할 때 사용하는 옵션</td></tr>
</table>

사용 예
```
# umount /media
/media에 마운트된 장치를 언마운트

# umount /dev/sdb1
/dev/sdb1 장치를 언마운트

# umount -a -t iso9660
파일 시스템이 iso9660으로 마운트된 것들을 모두 언마운트
```

#### eject
- CD-ROM, DVD 등과 같이 이동식 보조기억장치의 미디어를 꺼낼 때 사용
- 자동으로 언마운트 작업을 수행

```
eject [장치명 or 마운트된_디렉터리명]
```

사용 예
```
# eject
기본 DVD 또는 CD-ROM 장치를 언마운트한 후 디스크 트레이(Tray)를 연다

# eject /media
/media에 마운트된 장치를 언마운트한 후에 디스크 트레이를 연다
```

### 파일 시스템 점검 및 관리
#### fsck(filesystem check)
- ext 계열 리눅스 파일 시스템을 검사하고 수리하는 명령
- CentOS 6 이전 버전에서는 손상된 디렉터리나 파일을 수정할 때 임시로 /lost+found 디렉터리에서 작업을 수행하고 복구를 진행하였음

```
fsck [option] 장치명
```

주요 옵션
<table>
<tr><td>-a</td><td>명령 수행에 대한 확인 질문 없이 무조건 수행</td></tr>
<tr><td>-r</td><td>명령 수행에 대한 확인 질문을 한다. 여러 개의 fsck가 병렬 모드 형태로 작동하고 있을 때 유용</td></tr>
<tr><td>-A</td><td>/etc/fstab에 정의되어 있는 모든 파일 시스템을 체크</td></tr>
<tr><td>-P</td><td>-A 옵션을 사용할 때, 루트 파일 시스템을 다른 파일 시스템과 병렬로 함께 체크</td></tr>
<tr><td>-R</td><td>-A 옵션을 사용할 때, 루트 파일 시스템은 체크하지 않고 건너뜀</td></tr>
<tr><td>-N</td><td>실행을 하지 않고 단지 어떤 것이 실행되어지는지만 보여줌</td></tr>
<tr><td>-T</td><td>검사를 시작할 때 제목을 보여주지 않음</td></tr>
<tr><td>-s</td><td>fsck 동작을 시리얼화한다. 대화형 모드에서 여러 파일 시스템을 점검할 때 유용</td></tr>
<tr><td>-V</td><td>실행되는 각 파일 시스템용 명령을 포함해 자세한 출력을 수행</td></tr>
<tr><td>-t fs_type</td><td>점검할 파일 시스템의 유형을 지정<br>

- 파일 시스템 앞에 no를 붙이면 지정한 파일 시스템을 제외한 나머지를 검사
- -A 옵션이 설정되어 있는 경우 /etc/fstab에서 파일 시스템이 맞는 것만 검사</td></tr>
</table>

#### e2fsck
- 리눅스 파일 시스템인 ext2, ext3, ext4를 검사하고 수리하는 명령
- 현재 리눅스 배포판에서 fsck 명령 실행 시 실제 사용되는 명령

```
e2fsck [option] 장치명
```

주요 옵션
<table>
<tr><td>-n</td><td>특정 상황에 대한 물음에 'no'라고 인식하여 처리</td></tr>
<tr><td>-y</td><td>특정 상황에 대한 물음에 'yes'라고 인식하여 처리</td></tr>
<tr><td>-c</td><td>Bad Block을 체크</td></tr>
<tr><td>-f</td><td>깨끗한 파일 시스템까지 강제적으로 체크</td></tr>
</table>

> fsck.ext2, fsck.ext3, fsck.xfs 등이 추가로 제공됨

#### xfs_repair
- XFS 파일 시스템을 점검 및 복구할 때 사용하는 명령

```
xfs_repair [option] 장치명
```

주요 옵션
<table>
<tr><td>-n</td><td>파일 시스템을 점검만 하고, 복구 작업은 수행하지 않음</td></tr>
<tr><td>-L</td><td>관련 장치의 로그를 비운다(Zeroing). 데이터 손실이 있을 수 있으므로 최후의 수단으로만 사용</td></tr>
<tr><td>-v</td><td>점검 및 복구와 관련된 시간 정보를 추가로 출력</td></tr>
</table>

#### df(disk free)
- 현재 마운트된 디스크의 크기, 사용량, 남아있는 용량 등에 대한 정보를 출력

```
df [option]
```

주요 옵션
<table>
<tr><td>-h</td><td>용량의 단위 표시(KB, MB, GB)를 해준다(--human-readable)</td></tr>
<tr><td>-k</td><td>킬로바이트 단위로 보여준다(기본값)</td></tr>
<tr><td>-m</td><td>메가바이트 단위로 보여준다</td></tr>
<tr><td>-T</td><td>각 파티션에 대한 파일 시스템의 유형을 보여준다</td></tr>
<tr><td>-i</td><td>아이노드의 사용량을 보여준다<br>'No space left on device'라는 메시지가 떴을 때 대부분 I-nodes를 다 써버린 경우임</td></tr>
</table>


![diskfree](https://github.com/dee021/TIL/blob/linuxmaster/Linux/img/diskfree.png)

#### du(disk usage)
- 파일이나 디렉터리들이 디스크에서 차지하고 있는 크기를 출력

```
du [option] [FILS(s)]
```

주요 옵션
<table>
<tr><td>-h</td><td>용량의 단위 표시(KB, MB, GB)를 해준다(--human-readable)</td></tr>
<tr><td>-b</td><td>바이트 단위로 보여준다</td></tr>
<tr><td>-k</td><td>킬로바이트 단위로 보여준다(기본값)</td></tr>
<tr><td>-m</td><td>메가바이트 단위로 보여준다</td></tr>
<tr><td>-a</td><td>디렉터리에 존재하는 모든 파일에 대해 각각의 크기를 보여준다</td></tr>
<tr><td>-s</td><td>파일들의 전체 크기를 합한 값만 보여준다(--summarize)</td></tr>
</table>

#### dd(Data Dumper)
- 디스크를 이미지 형태로 백업하거나 파일의 포맷을 바꾸는 명령
- 텍스트 파일의 대소문자변환과 부팅 디스크를 만들 때도 쓰임
- 스왑 파일을 만들거나 디바이스 초기화시킬 때도 사용

```
dd if=입력파일 [conv=conversion type] of=출력파일 [bs=] [count=] [skip=]
```

주요 항목
<table>
<tr><td>if=</td><td>입력 파일 지정. 장치명이나 이미지 파일도 지정 가능</td></tr>
<tr><td>of=</td><td>출력 파일 지정. 장치명이나 이미지 파일도 지정 가능</td></tr>
<tr><td>conv=</td><td>변환하는 데이터 유형 지정<br>예: lcase, ucase<br>대소문자 변환 시 if와 of의 파일명을 달리해야 함</td></tr>
<tr><td>bs=</td><td>출력되는 블록 사이즈 지정. 한 번에 변환하는 블록 사이즈를 지정하는 항목</td></tr>
<tr><td>count=</td><td>변환되는 블록의 수 지정</td></tr>
<tr><td>skip=</td><td>입력 파일의 시작되는 블록 번호를 지정. 보통 데이터를 나누어 이미지를 만들 때 사용</td></tr>
</table>

사용 예
```
$ dd if=a.txt conv=ucase of=b.txt
a.txt 파일의 모든 글자를 대문자로 변환하여 b.txt 파일을 생성

# dd if=/dev/sda of=/dev/sdb bs=1M
/dev/sda의 내용을 /dev/sdb로 디스크 백업하고 블록 사이즈는 1MB로 한다

# dd if=/dev/sda of=/dev/sdb
/dev/sda의 내용을 /dev/sdb에 그대로 옮긴다. 파티션의 내용 및 LABEL도 그대로 옮겨진다.
```

<b>dd 명령을 사용하는 경우</b><br>

1. 텍스트 파일의 문자들을 대/소문자로 전환할 때 사용한다.
2. 부팅 디스크 또는 설치 디스크를 만들 때 사용한다.(RHEL 4 버전 이후에는 사용되지 않음)
3. 디스크나 파티션 단위로 백업할 때 사용한다.
4. 스왑 파일을 만들 때 사용한다.
5. 디스크를 초기화시킬 때 사용한다. RAID나 LVM 구성 시 오류가 발생한 경우에 사용하면 유용하다.


#### partprobe
- 변경된 파티션 정보를 반영시킬 때 사용하는 명령
- 이 명령을 실행하면 재부팅하지 않고 바로 적용됨

> 일부 시스템에서는 반영되지 않는 경우가 있는데 반영 여부는 /proc/partitions에서 확인하고, 반영되지 않았다면 시스템 재부팅을 실행해야 한다.


```
partprobe [options]
```

주요 옵션
<table>
<tr><td>-d</td><td>실제 적용되지는 않고 실행 가능 여부만 점검(--dry-run)</td></tr>
<tr><td>-s</td><td>파티션 관련 정보를 간단히 출력(--summary)</td></tr>
</table>

#### stat
- 파일 또는 파일 시스템 관련 정보를 출력하는 명령

```
stat [options] 파일명
```

주요 옵션
<table>
<tr><td>-f</td><td>파일 대신 파일이 속한 파일 시스템 관련 정보를 출력(--file-system)</td></tr>
<tr><td>-L</td><td>심볼릭 링크 파일인 경우 원본 파일의 정보를 출력(--dereference)</td></tr>
<tr><td>--print=형식</td><td>%n(파일 이름), %U(소유자 이름), %G(소유자 그룹), %C(SELinux 관련 정보), %z(마지막 변경된 시간 정보) 등을 사용해서 형식을 지정.</td></tr>
</table>

![stat](https://github.com/dee021/TIL/blob/linuxmaster/Linux/img/stat.png)

### 스왑(Swap) 생성

<b>스왑</b> : 하드 디스크의 일부를 마치 메모리처럼 사용하게 해주는 기술

- 일반적으로 리눅스 설치 시 설정
- 스왑 영역이 부족한 경우 디스크의 남은 공간이나 파티션을 이용하여 추가 설정 할 수 있음 


<b>스왑 파일 생성하기</b><br>

> dd 명령을 이용하여 원하는 파일의 크기만큼 스왑 파일을 생성한다.
> `dd if=/dev/zero of=swap-file bs=1k count=1024000`
>
> 스왑 파일 생성
> `mkswap /swap-file`
>
> 스왑 파일 활성화
> `swapon /swap-file`
>
> 시스템 부팅 시마다 사용할 경우 /etc/fstab 파일에 등록
> `vi /etc/fstab`<br>
> > /swap-file  swap  swap defualts 0 0


<b>스왑 파티션 생성</b><br>

> fdisk 명령을 이용하여 스왑 파티션 생성
> `fdisk /dev/sdb`
> 
> 스왑 파티션 생성
> `mkswap -c /dev/sdb2`
>
> 스왑 파티션 활성화
> `swapon /dev/sdb2`
>
> 시스템 부팅 시마다 사용할 경우 /etc/fstab 파일에 등록
> `vi /etc/fstab`
> > /dev/sdb2 swap  swap  defaults 0 0


#### mkswap
- 스왑 파티션이나 스왑 파일을 생성하는 명령

```
mkswap [option] 스왑_파일 [size]
mkswap [option] 스왑_파티션
```

주요 옵션
<table>
<tr><td>-c</td><td>스왑파티션을 생성하기 전에 Bad Block을 검사하는 옵션</td></tr>
</table>

#### swapon
- 스왑 파티션이나 스왑 파일을 활성화시키는 명령
- 스왑의 상태 확인도 가능

```
swapon [option] 스왑_파일
swapon [option] 스왑_파티션
```

주요 옵션
<table>
<tr><td>-a</td><td>/etc/fstab 파일에 등록된 스왑 영역을 전부 활성화시킴<br>noauto 옵션이 설정된 경우는 제외</td></tr>
<tr><td>-s</td><td>스왑 영역의 상태 출력</td></tr>
</table>


#### swapoff
- 활성화된 스왑 파티션이나 스왑 파일을 중지시킴

```
swapoff [option] 스왑_파일
swapoff [option] 스왑_파티션
```

주요 옵션
<table>
<tr><td>-a</td><td>모든 스왑 영역을 중지시킴</td></tr>
</table>


#### free
- 현재 사용 중인 메모리의 상태를 출력하는 명령
- 기본적인 정보는 /proc/memifno 파일에서 가져옴


```
free [option]
```

주요 옵션
<table>
<tr><td>-m</td><td>메모리의 상태를 MB 단위로 출력(--mege)</td></tr>
<tr><td>-k</td><td>메모리의 상태를 KB 단위로 출력(--kilo)</td></tr>
<tr><td>-h</td><td>메모리의 상태를 B(bytes), K(kilos), M(megas), G(gigas), T(teras)와 같은 단위를 붙여 출력(--human)</td></tr>
</table>

![free](https://github.com/dee021/TIL/blob/linuxmaster/Linux/img/free.png)

<table>
<thead><td></td><td>Mem</td><td>Swap</td></thead>
<tr><td></td><td>물리적 메모리의 상태를 나타내는 부분</td><td>스왑 영역의 메모리 상태를 나타내는 부분</td></tr>
<tr><td>total</td><td>전체 메모리 용량</td><td>전체 용량</td></tr>
<tr><td>used</td><td>사용 중인 메모리양</td><td>사용 중인 스왑 메모리의 양</td></tr>
<tr><td>free</td><td>사용되지 않고 있는 메모리양</td><td>사용되지 않고 있는 스왑 메모리양</td></tr>
<tr><td>shared</td><td>프로세스간 공유되고 있는 메모리양</td><td></td></tr>
<tr><td>buff/cache</td><td>버퍼 및 캐시 메모리로 사용되는 양</td><td></td></tr>
<tr><td>available</td><td>스왑을 이용하지 않고 새로운 응용 프로그램을 실행할 수 있는 메모리 양</td><td></td></tr>
</table>

> free 항목의 값과 buff/cache 항목의 값을 더해 실제 유휴 메모리를 계산할 수 있다.

### Disk Quota
- 사용자 및 그룹의 디스크 사용량과 생성할 수 있는 파일의 개수(I-node의 수)를 제한할 수 있음
- 제한 값 설정과 관련된 항목으로 소프트(Soft)와 하드(Hard)로 구분
  - 소프트 : 보통 사용자에게 공지되는 제한 용량
  - 하드 : 실제 사용자가 최대로 사용할 수 있는 값

#### 사용자 쿼터(Quota) 설정하기
1. /etc/fstab 설정<br>
/etc/fstab에서 /home의 4번째 필드에 사용자쿼터 설정 옵션인 uquota(또는 usrquota)를 추가로 기입

1. /home 영역 다시 마운팅
```
# umount /home; mount -a
```

1. 적용 확인
```
# mount | grep /home
```

1. 사용자에 대한 쿼터 설정

2. 쿼터 설정 확인
```
# repquota /home
```

#### 그룹 쿼터(Quota) 설정하기
1. /etc/fstab 설정<br>
/etc/fstab에서 /home의 4번째 필드에 사용자쿼터 설정 옵션인 gquota(또는 qrpquota)를 추가 기입

2. /home 영역 다시 마운팅
```
# umount /home; mount -a
```

3. 적용 확인
```
# mount | grep /home
```

4. 그룹에 대한 쿼터 설정

5. 쿼터 설정 확인
```
# repquota -g /home
```


#### xfs_quota
- XFS 파일 시스템에의 쿼터(quota)를 관리해주는 명령어

```
xfs_quota [option]
```

주요 옵션
<table>
<tr><td>-x</td><td>전문가(expert) 모드를 활성화시키는 옵션<br>관리자 명령을 실행할 때 기본적으로 사용해야 하는 옵션</td></tr>
<tr><td>-c 명령</td><td>쿼터 관련 명령을 지정할 때 사용하는 옵션</td></tr>
</table>

사용 예
```
# xfs_quota -x -c 'limit bsoft=100m bhard=110m joon' /home
joon 사용자의 소프트 용량 제한 100MB, 하드 용량 제한은 110MB로 설정
```

#### edquota
- 사용자나 그룹에 쿼터를 설정할 때 사용하는 명령
- 명령을 실행시키면 vi 편집기가 실행되고, KB 단위로 입력하여 설정

```
edquota [option]
```

주요 옵션
<table>
<tr><td>-u</td><td>사용자에 대한 쿼터를 설정할 때 사용하는 옵션(기본 옵션)(--user)</td></tr>
<tr><td>-g</td><td>그룹에 대한 쿼터를 설정할 때 사용하는 옵션(--group)</td></tr>
<tr><td>-t</td><td>Soft limit을 초과한 후부터 적용되는 시간 제한(grace period)을 설정하는 옵션(--edit-period)</td></tr>
<tr><td>-p</td><td>특정 사용자의 쿼터를 다른 사용자에게 동일한 설정으로 적용할 때 사용하는 옵션(--prototype=name)</td></tr>
</table>

<!-- 추가 -->
```
user2 사용자의 쿼터를 할당
# edquota user2
```

edquota 항목

- Filesystem : Quota가 설정되어 있는 파티션을 나타낸다.
- Blocks : 현재 사용 중인 용량(KB)을 나타낸다.
- soft : 보통의 경우 사용자가 사용할 수 있는 최대 용량(KB)을 나타내는데, 여기에 지정한 용량이 hard에 지정한 값보다 작게 설정하였다면 hard 용량이 지정한 유예 기간(grace period) 동안은 초과할 수 있다. 유예 기간 내에 soft에 지정한 용량 이하로 줄이지 않으면 파일의 생성이 불가능해진다.
- hard : 실제 사용자가 최대로 사용할 수 있는 용량(KB)을 의미한다. soft는 초과하여 사용 가능하나 hard에 설정한 용량은 초과할 수 없다.
- inodes : 현재 사용 중인 I-node 수(파일이나 디렉터리의 개수)를 나타낸다.
- soft : 일반적으로 사용자에게 지정하는 inodes 값으로 파일이나 디렉터리를 생성할 수 있는 개수를 나타낸다. 유예 기간의 적용 등은 blocks 항목과 같다.
- hard : 절대 초과할 수 없는 inode 값으로 사용자가 생성할 수 있는 파일이나 디렉터리의 최댓값이다.


edquota -t의 항목

- Filesystem : Quota가 설정된 파일 시스템을 나타낸다.
- Block grace period : Block의 soft 제한을 초과했을 경우에 유예 기간이다.
- Inode grace period : Inode의 soft 제한을 초과했을 경우의 유예 기간이다.

#### repquota
- 파일 시스템에 설정된 쿼터 정보를 출력하는 명령

```
repquota [option] [디렉터리명]
```

주요 옵션
<table>
<tr><td>-a</td><td>쿼터가 설정되어 있는 모든 파티션의 정보를 출력<br>이 옵션을 사용하면 디렉터리명을 지정할 필요가 없음</td></tr>
<tr><td>-u</td><td>사용자 쿼터 정보를 출력(기본 옵션)</td></tr>
<tr><td>-g</td><td>그룹 쿼터 정보를 출력</td></tr>
</table>

#### quota
- 사용자나 그룹 단위로 쿼터 설정 정보를 출력

```
quota
```

주요 옵션
<table>
<tr><td>-u</td><td>사용자 쿼터 정보를 출력(--user)</td></tr>
<tr><td>-g</td><td>그룹 쿼터 정보를 출력(--group)</td></tr>
</table>

#### setquota
- 쿼터를 설정하는 명령
- vi 편집기를 이용하는 대신에 직정 명령행에서 설정 가능
- 기본 단위 : KB

```
setquota [option] 사용자명 block_soft block_hard inode_soft inode_hard 파일 시스템
setquota -t block_grace inode_grace 파일 시스템
```

주요 옵션
<table>
<tr><td>-u</td><td>사용자 쿼터 설정할 때 사용(기본 옵션)</td></tr>
<tr><td>-g</td><td>그룹 쿼터를 설정할 때 사용</td></tr>
<tr><td>-t</td><td>유예 기간(Grace Time)을 설정할 대 사용(단위: 초)</td></tr>
</table>

사용 예
```
# setquota -u yuloje 10000 11000 0 0 /home
yuloje 사용자의 용량 제한은 Soft 10MB, Hard 11MB로 설정하고, I-node에 대한 제한은 설정하지 않음

# setquota -t 86400 28800 /home
블록의 유예 기간은 24시간으로 설정하고, I-node는 8시간으로 설정
```

### 파일 시스템 관련 명령어
#### pwd(print working directory)
- 현재 위치한 작업 디렉터리의 경로를 출력

#### cd(change directory)
- 디렉터리를 이동하는 명령
- 절대 경로와 상대 경로를 지정하여 현재 작업 중인 디렉터리를 변경할 수 있음
- 디렉터리 생략 시 사용자의 홈 디렉터리로 이동

```
cd [directory_name]
```

#### mkdir
- 디렉터리를 생성하는 명령

```
mkdir [option] directory_name(s)
```

주요 옵션
<table>
<tr><td>-p</td><td>경로로 지정된 서브 디렉터리가 존재하지 않은 경우 함께 생성(--parents)</td></tr>
<tr><td>-m</td><td>디렉터리 생성과 동시에 Permission을 설정(--mode)</td></tr>
<tr><td>-v</td><td>생성된 디렉터리를 메시지로 출력(--verbose)</td></tr>
</table>

#### rmdir
- 디렉터리를 삭제하는 명령
- 빈 디렉터리가 아니면 삭제되지 않음
```
rmdir [option] directory_name(s)
```

주요 옵션
<table>
<tr><td>-p</td><td>삭제하려는 디렉터리를 제거한 후, 그 결과로 부모 디렉터리도 빈 디렉터리가 되면 부모 디렉터리도 삭제(--parents)</td></tr>
</table>

#### tree
- 특정 디렉터리의 목록 정보를 트리 구조 형태로 출력
- `yum install tree`를 실행하여 설치 후 실행 가능

```
tree [option] [디렉터리명]
```

주요 옵션
<table>
<tr><td>-l</td><td>심볼릭 링크가 있는 경우 실제 디렉터리에 대한 정보도 함께 출력</td></tr>
<tr><td>-a</td><td>숨겨진 파일이나 디렉터리 정보도 함께 출력</td></tr>
<tr><td>-d</td><td>디렉터리 정보만 출력</td></tr>
<tr><td>-f</td><td>파일인 경우 지정한 디렉터리를 기준으로 전체 경로 형식으로 출력</td></tr>
<tr><td>-p</td><td>permission 정보도 함께 출력</td></tr>
<tr><td>-s</td><td>파일의 크기도 함께 출력(단위: byte)</td></tr>
<tr><td>-h</td><td>파일의 크기 정보를 사람이 읽기 쉬운 형태의 단위를 붙여 출력<br>
K, M, G, T, P, E를 사용</td></tr>
</table>

### 파일 관련 명령어
#### ls(list)
- 디렉터리 안의 내용을 출력하는 명령

```
ls [option] [파일 또는 디렉터리명]
```

주요 옵션

<table>
<tr><td>-a</td><td>숨겨진 파일이나 디렉터리도 출력(--all)<br>
'.'으로 시작하는 파일은 숨겨진 파일을 의미</td></tr>
<tr><td>-A</td><td>.(현재 디렉터리) 및 ..(부모 디렉터리) 기호를 제외하고 숨겨진 파일이나 디렉터리를 함께 출력(--almost-all)</td></tr>
<tr><td>-F</td><td>파일의 형태에 따라 관련 정보를 출력

- 실행 가능한 파일은 파일 이름 뒤에 '*'(asterisk) 문자 표시
- 디렉터리는 이름 뒤에 './' 표시
- 심볼릭 링크는 뒤에 '@'를 표시
</td></tr>
<tr><td>-l</td><td>파일이나 디렉터리 정보를 한 줄에 하나씩 길게 출력(--long)<br>
출력되는 정보는 7개의 필드로 구성

- permission,  링크의 수(파일) 또는 이동 가능한 디렉터리 개수, 소유자, 그룹, 파일크기(byte), 최종 수정 날짜 및 시간, 파일 또는 디렉터리명

</td></tr>
<tr><td>-d</td><td>대상이 디렉터리인 경우, 디렉터리 안의 내용 대신 디렉터리에 대한 정보를 출력(--directory)</td></tr>
<tr><td>-t</td><td>출력 정보가 알파벳순이 아니라 최근 수정된 파일(또는 디렉터리)부터 시간 순으로 출력</td></tr>
<tr><td>-u</td><td>파일 및 디렉터리의 최종 수정된 시간 대신 사용된 시간(최종 접근 시간)으로 출력</td></tr>
<tr><td>-i</td><td>i-node 번호를 파일 또는 디렉터리명 앞에 출력</td></tr>
<tr><td>-r</td><td>파일 및 디렉터리 정보를 알파벳 역순으로 출력(--reverse)</td></tr>
<tr><td>-R</td><td>존재하는 하위 디렉터리별로 관련 내용을 재귀적으로 전부 출력(--recursive)</td></tr>
<tr><td>-S</td><td>파일 크기가 큰 것부터 출력</td></tr>
<tr><td>--color</td><td>파일의 종류에 따라 색을 보여줌<br>
--color=auto가 설정되어 있는데, 파일의 종류에 따라 자동으로 색상이 표시됨</td></tr>
<tr><td>-1</td><td>한 줄에 한 파일씩 출력</td></tr>
<tr><td>-h</td><td>보통 -l 옵션과 함께 사용되며, 보기 쉽게 단위(K, G 등)를 표시(--human-readable)</td></tr>
<tr><td>-Z</td><td>SELinux 관련된 보안문맥(security context)을 출력(--context)</td></tr>
</table>

사용 예
```
$ ls -alF
현재 디렉터리의 내용을 숨겨진 파일을 포함해서 모두 출력하고, 관련 정보 및 파일의 형태도 표시

$ ls -ld /home
/home 디렉터리의 파일 속성, 권한, 소유자 등을 보여줌
```

#### cp(copy)
- 파일이나 디렉터리를 복사하는 명령

```
cp [options] source(s) destination
```

주요 옵션
<table>
<tr><td>-r, -R</td><td>복사하려는 대상이 디렉터리인 경우, 하위 디렉터리까지 같이 복사(--recursive)</td></tr>
<tr><td>-i</td><td>복사를 통해 생성할 파일이 존재할 경우 덮어 쓸 것인지 질의(--interactive)</td></tr>
<tr><td>-f</td><td>같은 파일의 이름을 존재하는 경우 강제로 덮어씀(--force)</td></tr>
<tr><td>-b</td><td>덮어쓸 때 백업본의 파일을 생성<br>이 옵션을 사용하면 원래 파일명 뒤에 ~가 붙는 파일이 생성됨</td></tr>
<tr><td>-s</td><td>복사하는 대신 심볼릭 링크를 생성</td></tr>
<tr><td>-p</td><td>소유권, 그룹, 허가권 등 모든 정보를 복사<br>이 옵션이 없을 경ㅇ 현재 시간과 복사하는 사용자의 기본 허가권, 소유권, 그룹 정보로 파일 복사</td></tr>
<tr><td>-v</td><td>파일을 복사하기 전에 각각 파일명을 표시(--verbose)</td></tr>
<tr><td>-d</td><td>심볼릭 파일인 경우 그 심보릭 정보와 함께 링크 파일 그대로 복사할 때 사용<br>
이 옵션이 없을 경우, 원본 파일을 찾아 복사</td></tr>
<tr><td>-a</td><td>-dpR 옵션과 같음<br>
특정 파일의 속성, 링크 정보들을 그대로 유지하고 그 하위 디렉터리까지 전부 복사</td></tr>
<tr><td>-l</td><td>복사 대신 하드 링크 파일 생성</td></tr>
<tr><td>-u</td><td>복사되는 대상이 최신 파일이면 복사하지 않음(--update)</td></tr>
</table>

#### rm(remove)
- 파일을 삭제하는 명령
- 옵션에 따라 디렉터리도 삭제

```
rm [option] fils(s) 또는 directory
```

주요 옵션

<table>
<tr><td>-i</td><td>파일 삭제 시 질의 사용(--interactive)</td></tr>
<tr><td>-f</td><td>존재 유무나 어떠한 메시지 없이 강제로 삭제(--force)</td></tr>
<tr><td>-r, -R</td><td>하위 디렉터리를 포함하여 디렉터리 안의 모든 내용을 삭제(--recursive)</td></tr>
<tr><td>--</td><td>파일의 이름이 '-'로 시작하는 파일을 지울 때 사용</td></tr>
</table>

#### mv(move)
- 파일이나 디렉터리의 이름을 변경하거나 이동하는 명령

```
mv [options] source(s) destination
```

주요 옵션
<table>
<tr><td>-i</td><td>이동할 디렉터리에 동일한 파일명이 존재할 경우 덮어 쓸 것인지 질의(--interactive)</td></tr>
<tr><td>-f</td><td>같은 파일의 이름을 존재하는 경우에 강제로 덮어씀(--force)</td></tr>
<tr><td>-b</td><td>덮어쓸 때 백업본의 파일 생성<br>
이 옵션을 사용하면 원래 파일명 뒤에 ~가 붙는 파일이 생성됨</td></tr>
<tr><td>-v</td><td>파일을 이동하는 내용을 출력(--verbose)</td></tr>
<tr><td>-u</td><td>이동할 디렉터리에 동일한 파일명이 있는 경우 검사하여 해당 파일이 최신 파일이면 이동하지 않음</td></tr>
</table>

#### touch
- 파일 또는 디렉터리의 최종 접근 시간, 수정 시간 등 타임스탬프를 변경하거나 파일의 크기가 0인 빈 파일을 생성하는 명령

```
touch [option] 파일명
```

주요 옵션
<table>
<tr><td>-a</td><td>접근 시간(access time) 변경</td></tr>
<tr><td>-m</td><td>수정 시간(modification time) 변경</td></tr>
<tr><td>-t</td><td>현재 시간 대신 [[CC]YY]MMDDhhmm[.ss] 형식으로 지정된 타임스탬프로 변경<br>
리눅스 배포판에 따라 타임스탬프 형식이 다를 수 있으니 man 명령으로 확인해야 함</td></tr>
<tr><td>-r</td><td>지정된 파일의 Access time 및 Modify time으로 특정 파일의 시간을 변경(--reference=파일명)</td></tr>
</table>

#### file
- 파일의 종류를 출력하는 명령
- 관련 정보는 /usr/share/magic 파일을 참조한다.

```
file [option] 파일명
```

주요 옵션
<table>
<tr><td>-b</td><td>정보 출력 시 파일 이름은 출력하지 않음(--brief)</td></tr>
<tr><td>-i</td><td>특정 파일의 문자 집합(charset) 정보를 출력<br>
예 : ASCII, UTF-8 등</td></tr>
</table>

#### locate
- 시스템 전체에서 찾고자 하는 패턴(pattern)과 맞는 파일이나 디렉터리를 경로와 함께 찾아 출력
- 파일이나 디렉터리를 데이터베이스화하여 사용하므로 매우 빠름
- 파일이나 디렉터리 생성 후 데이터베이스 정보를 갱신해야 함 (`updatedb` 혹은 `cron`)
- 실제적으로 접근해서 볼 수 있는 것들만 찾아서 보여주므로 find 명령에 비해 강력하지는 않음

```
locate [option] pattern
```

주요 옵션
<table>
<tr><td>-c</td><td>관련 정보의 경로 출력 대신 개수를 출력(--count)</td></tr>
<tr><td>-i</td><td>대소문자를 구분하지 않음(--ignore-case)</td></tr>
</table>

#### find
- 현재 디렉터리에서부터 하위 디렉터리까지 주어진 여러 조건을 가지고 파일 및 디렉터리를 찾아 그 경로를 출력하는 명령
- root에서 접근 권한을 제한한 파일이나 디렉터리에 관해서는 "Permission Denied" 메시지 출력

```
find [path] options expression
```

주요 옵션
<table>
<tr><td>-name</td><td>주어진 이름의 파일이나 디렉터리를 찾음</td></tr>
<tr><td>-user</td><td>지정한 사용자의 파일이나 디렉터리를 찾음</td></tr>
<tr><td>-group</td><td>지정한 그룹 소유의 파일이나 디렉터리를 찾음</td></tr>
<tr><td>-uid/-gid</td><td>지정한 UID/GID 소유의 파일이나 디렉터리를 찾음</td></tr>
<tr><td>-perm</td><td>지정한 permission 값을 갖는 파일이나 디렉터리를 찾음
<br>예

```
-perm 755 : 퍼미션 값이 755로 설정된 파일(디렉터리)
-perm -6000 : Set-UID와 Set-GID를 동시에 갖는 파일(디렉터리)
-perm +6000 : Set-UID 또는 Set-GID 둘 중 하나 이상을 갖는 파일(디렉터리)
```
</td></tr>
<tr><td>-type</td><td>지정한 파일 유형을 찾음<br>
(d : 디렉터리, l : 링크 파일, b : 블록 디바이스, c : 캐릭터 디바이스, p : 파이프 파일, s : 소켓 파일)
</td></tr>
<tr><td>-atime n</td><td>n일 이전에 접근한 파일을 찾음

- +n : n일 이전에 액세스한 파일을 찾음
- -n : n일 내에 액세스한 파일을 찾음
</td></tr>
<tr><td>-ctime n / -mtime n</td><td>Change Time/Modify Time 기준으로 찾음</td></tr>
<tr><td>-exec</td><td>찾아진 파일 등과 연계하여 어떠한 명령을 내릴 수 있는 옵션<br>
결과값은 { } 기호로 표시하고 '\;'로 끝나야 함</td></tr>
<tr><td>-ok</td><td>확인 메시지를 출력하여 명령 실행 시 'y'나 'n'을 입력받는 것을 제외하고 -exec와 같음</td></tr>
<tr><td>-fprint</td><td>출력 결과를 파일로 저장할 때 사용하는 옵션<br>리다이렉션(>)로 대체 가능</td></tr>
<tr><td>-ls</td><td>맨 뒤에 이 옵션을 사용하면 상세 출력하는 `ls -l`과 같은 역할</td></tr>
<tr><td>-size n</td><td>n 블록 길이의 파일읖 찾음

- +n : n 블록보다 큰 파일
- -n : n 블록보다 작은 파일
- nc : n문자 길이의 파일
</td></tr>
<tr><td>-inum n</td><td>node 번호가 n인 파일을 찾음</td></tr>
<tr><td>-iname</td><td>대소문자를 구별하지 않고 이름으로 찾을 때 사용</td></tr>
<tr><td>maxdepth n</td><td>검색 시 주어진 정수값 n만큼의 섭스 디렉터리만 검색</td></tr>
<tr><td>-prune</td><td>찾고자하는 대상이 디렉터리인 경우 하위디렉터리의 검색을 하지 않음</td></tr>
<tr><td>-empty</td><td>파일의 크기가 0이거나 비어있는 디렉터리를 찾음</td></tr>
<tr><td>-newer</td><td>지정한 파일보다 뒤에 수정된 파일을 찾음(mtime)</td></tr>
<tr><td>-cnewer</td><td>지정한 파일보다 뒤에 변화된 파일을 찾음(ctime)</td></tr>
<tr><td>2>/dev/null</td><td>(옵션 아님) 맨 뒤에 붙이면 접근 권한이 없는 파일이나 디렉터리에 접근 시 "permission Denied" 관련 리스트는 출력하지 않음</td></tr>
</table>

### 텍스트 파일 관련 명령어
#### cat
- 표준 입력으로 받는 값을 표준 출력으로 이어주는 명령
- 기본적으로는 텍스트 파일의 내용을 출력
- redirection 기호와 함께 사용해서 파일을 생성하거나 여러 개의 텍스트 파일을 합치는 기능도 수행

```
cat [option] [filename(s)]
```

주요 옵션

<table>
<tr><td>-b</td><td>텍스트 파일 출력 시 행 번호를 붙임(공백만 있는 줄 제외)</td></tr>
<tr><td>-n</td><td>텍스트 파일 출력 시 행 번호를 붙임(공백만 있는 줄 포함)</td></tr>
<tr><td>-E</td><td>각 라인의 맨 끝에 $ 표시(개행 문자)를 붙여 출력</td></tr>
<tr><td>-T</td><td>탭 문자를 ^I로 표시하여 출력</td></tr>
<tr><td>-v</td><td>인쇄가 불가능한 문자를 식별할 수 있도록 출력</td></tr>
<tr><td>-A</td><td>-vET 옵션을 통합한 옵션으로 일반적으로 출력되지 않는 모든 문자를 출력</td></tr>
<tr><td>-s</td><td>인접한 여러 공백 줄을 하나의 공백 줄로 출력</td></tr>
</table>

사용 예
```
$ cat
[Ctrl] + [c]를 입력받기 전까지 키보드로부터 받은 입력받는 내용을 모니터에 출력

$ cat > a.txt
[Ctrl]+[d]를 입력받기 전까지 키보드로부터 입력받은 내용을 a.txt라는 파일에 저장

$ cat < a.txt > b.txt
cp a.txt b.txt와 같음

$ cat a.txt b.txt > c.txt
a.txt 내용과 b.txt 내용을 합해서 c.txt 파일로 저장


$ cat << end
end라는 문자열이 입력될 때까지 기다렸다가 대기되었던 내용을 한 번에 표준 출력으로 내보냄
```

#### head
- 텍스트 파일의 첫 부분을 보여주는 명령
- 기본 10줄을 출력

```
head [option] filename(s)
```

주요 옵션
<table>
<tr><td>-n 행수</td><td>파일의 앞에서부터 지정된 수만큼 출력<br>
-n 5와 -5는 동일</td></tr>
<tr><td>-c n</td><td>n 바이트까지 출력<br>
n뒤에 k나 m을 쓰면 KB, MB를 나타냄</td></tr>
<tr><td>-q</td><td>여러 개의 파일이 처리될 때 파일 이름 헤더를 출력하지 않음</td></tr>
</table>

#### tail
- 텍스트 파일의 끝 부분을 보여주는 명령
- 기본 10줄 출력

```
tail [option] filename(s)
```

주요 옵션
<table>
<tr><td>-n 행수</td><td>파일의 마지막 줄에서부터 지정된 수만큼 출력<br>
-n 5와 -5는 동일</td></tr>
<tr><td>-c n</td><td>n 바이트까지 출력<br>
n뒤에 k나 m을 쓰면 KB, MB를 나타냄</td></tr>
<tr><td>-f</td><td>특정 파일의 끝부분에 새로운 행이 추가될 경우 실시간으로 출력(--follow)<br>
로그 파일을 모니터링할 때 유용<br>[Ctrl] + [c] 입력하면 중단됨</td></tr>
<tr><td>-q</td><td>여러 개의 파일이 처리될 때 파일 이름 헤더를 출력하지 않음</td></tr>
</table>

#### more
- 텍스트 파일의 내용이 긴 경우 화면(page)단위로 출력

```
more [option] filename
```

주요 옵션
<table>
<tr><td>-num</td><td>한 페이지를 num의 줄 수로 지정</td></tr>
</table>

more 실행 상태에서 사용하는 명령
<table>
<tr><td>[h]</td><td>more에 관한 도움말을 보여준다.</td></tr>
<tr><td>[Space] 또는 [z]</td><td>다음 페이지를 보여준다.</td></tr>
<tr><td>[d] 또는 [Ctrl] + [d]</td><td>다음 반 페이지를 보여준다.</td></tr>
<tr><td>[Enter]</td><td>한 줄씩 보여준다.</td></tr>
<tr><td>[q]</td><td>more 명령을 종료한다.</td></tr>
<tr><td>[b] 또는 [Ctrl] + [b]</td><td>이전 페이지(back)를 보여준다.</td></tr>
<tr><td>[f]</td><td>한 페이지를 skip한 후 다음 페이지(forward)를 보여준다.</td></tr>
<tr><td>/패턴</td><td>지정한 '패턴'을 검색한다.</td></tr>
<tr><td>=</td><td>현재 줄 번호를 보여준다.</td></tr>
<tr><td>[Ctrl] + [l]</td><td>화면을 다시 출력한다.</td></tr>
<tr><td>:f</td><td>현재 파일명과 줄 번호를 보여준다.</td></tr>
<tr><td>!</td><td>다른 명령을 입력할 수 있는 상태로 전환해준다.</td></tr>

</table>

#### less
- more 명령과 유사하게 한 화면 단위로 출력하는 명령
- more 명령은 실행 결과를 화면에 직접 출력하는 형식, less 명령은 버퍼 메모리를 사용해서 출력하는 방식
- 커서(Cursor)키를 이용한 상하좌우 이동이 가능하고, vi에서 사용하는 명령 사용 가능

```
less [option] filename
```

주요 옵션
<table>
<tr><td>-?</td><td>less 실행 시 사용하는 명령들에 대한 도움말 출력</td></tr>
<tr><td>-c</td><td>화면에 출력하기 전 화면을 정리하여 맨 처음에 위치되도록 함</td></tr>
<tr><td>-s</td><td>인접한 여러 공백 줄을 하나의 공백 줄로 처리하여 화면에 출력</td></tr>
<tr><td>-e</td><td>less 실행 후 맨 끝줄에 도달한 뒤 [Enter]나 [Space] 입력이 들어오면 자동으로 명령 종료</td></tr>
<tr><td>-N</td><td>줄 번호 출력</td></tr>
<tr><td>+행번호</td><td>지정한 행 번호부터 1page씩 출력</td></tr>
</table>



less 실행 상태에서 사용하는 명령
<table>
<tr><td>[h]</td><td>less에 대한 도움말을 보여준다.</td></tr>
<tr><td>[space], [f], [Ctrl] + [f]</td><td>다음 페이지를 보여준다. (forward)</td></tr>
<tr><td>[Enter], [e]</td><td>한 줄씩 보여준다.</td></tr>
<tr><td>[y]</td><td>이전 줄로 이동한다.</td></tr>
<tr><td>[q]</td><td>less 명령을 종료한다.</td></tr>
<tr><td>[b], [Ctrl] + [b]</td><td>이전 페이지를 보여준다.(back)</td></tr>
<tr><td>[d], [Ctrl] + [d]</td><td>다음 반 페이지를 보여준다.</td></tr>
<tr><td>[u], [Ctrl] + [u]</td><td>이전 반 페이지를 보여준다.</td></tr>
<tr><td>/패턴</td><td>지정한 패턴을 아래 방향으로 검색</td></tr>
<tr><td>?패턴</td><td>지정한 패턴을 위 방향으로 검색</td></tr>
<tr><td>[n]</td><td>패턴 검색 시 정방향으로 다음 문자열을 찾아 화면의 맨 첫 줄에 위치시킨다.</td></tr>
<tr><td>[N]</td><td>패턴 검색 시 역방향으로 다음 문자열을 찾아 화면의 맨 첫 줄에 위치시킨다.</td></tr>
</table>

#### grep(Global Regular Expression Print)
- 텍스트 파일에서 특정 패턴을 갖는 줄을 찾아 출력하는 명령

```
grep [option] pattern file(s)
```

주요 옵션
<table>
<tr><td>-b</td><td>일치하는 패턴이 있는 줄의 시작점을 출력(--byte-offset)</td></tr>
<tr><td>-c</td><td>일치하는 패턴이 있는 줄의 개수 출력(--count)</td></tr>
<tr><td>-h</td><td>여러 개의 파일 검색 시 출력하는 파일의 이름이 붙는 것을 방지</td></tr>
<tr><td>-i</td><td>검색 시 대소문자를 구분하지 않음(--ignore-case) (-y)</td></tr>
<tr><td>-n</td><td>일치하는 패턴이 있는 줄의 번호와 내용을 함께 출력(--line-number)</td></tr>
<tr><td>-v</td><td>일치하는 패턴이 없는 줄을 출력(--invert-match)</td></tr>
<tr><td>-w</td><td>패턴과 한 단어로 일치해야 출력(--word-regexp)</td></tr>
<tr><td>-x</td><td>패턴과 한 줄로 일치해야 출력(--line-regexp)</td></tr>
<tr><td>-l</td><td>주어진 패턴과 일치하는 패턴이 있는 파일이 이름만 출력(--file-with-matches)</td></tr>
<tr><td>-r</td><td>하위 디렉터리까지 주어진 패턴 검색(--recursive)</td></tr>
<tr><td>-o</td><td>지정한 패턴과 매칭되는 것만 출력(--only-matching)</td></tr>
<tr><td>-E</td><td>|(pipe)와 연계하여 여러 패턴 검색<br>egrep과 동일</td></tr>
<tr><td>-F</td><td>특수문자를 포함하여 지정한 문자를 그대로 인식하여 출력<br>fgrep 명령과 동일 </td></tr>
<tr><td>--color=auto</td><td>검색하는 패턴과 매칭되는 문자열을 색으로 표시하여 강조</td></tr>
</table>

사용 예
```
$ grep "[0-9]" readme
readme 파일에서 숫자가 존재하는 모든 줄 출력

$ grep -v ^# /etc/vsftpd/vsftpd.conf
/etc/vsftpd/vsftpd.conf 파일에서 #으로 시작하지 않는 줄만 출력
```
<b>정규표현식(Regular Expression)</b>

<table>
<thead><td>문자</td><td>설명</td></thead>
<tr><td>.</td><td>매칭되는 하나의 문자</td></tr>
<tr><td>.*</td><td>실질적인 전부를 의미. 0개 이상의 문자와 매칭</td></tr>
<tr><td>a*</td><td>아무것도 없거나 a라는 문자열이 하나 이상인 문자열을 의미<br>예. ba*r 는 br, bar, baar, ...을 의미</td></tr>
<tr><td>[abc]</td><td>[ ]는 문자 리스트 중의 한 문자를 의미. a, b, c 중 하나의 문자를 의미</td></tr>
<tr><td>[0-9]</td><td>숫자 하나 의미</td></tr>
<tr><td>[^1-3]</td><td>1, 2, 3을 제외한 모든 문자</td></tr>
<tr><td>'^pattern'</td><td>주어진 pattern으로 줄이 시작되는 경우</td></tr>
<tr><td>'pattern$'</td><td>주어진 pattern으로 줄이 끝나는 경우</td></tr>
<tr><td> '\< pattern'</td><td>주어진 pattern으로 단어가 시작할 경우</td></tr>
<tr><td>'pattern\>'</td><td>주어진 pattern으로 단어가 끝나는 경우</td></tr>
</table>


#### wc(word count)
- 텍스트 파일의 행(line) 수, 단어(word) 수, 문자(byte) 수를 출력하는 명령

```
wc [option] file
```

주요 옵션
<table>
<tr><td>-l</td><td>행 수만 출력(--lines)</td></tr>
<tr><td>-w</td><td>단어 수만 출력(--words)</td></tr>
<tr><td>-c</td><td>문자 수만 출력(--bytes)</td></tr>
<tr><td>-L</td><td>가장 긴 라인의 길이 출력(--max-line-length)</td></tr>
</table>

#### sort
- 텍스트 파일의 내용을 행 단위 정렬
- 옵션을 지정하지 않으면 ASCII 코드순으로 정렬
  - 공백 < 특수 문자 < 숫자 < 대문자 < 소문자
  - 숫자는 대소가 아닌 숫자 형태의 문자로 취급

```
sort [option] file
```

주요 옵션
<table>
<tr><td>-b</td><td>선행하는 공백 문자를 무시(공백 라인은 제외)</td></tr>
<tr><td>-d</td><td>공백과 알파벳, 숫자만으로 정렬(--dictionary-order)</td></tr>
<tr><td>-f</td><td>대소문자 무시(--ignore-case)</td></tr>
<tr><td>-r</td><td>정렬 순서를 반대로 하여 내림차순으로 정렬(ASCII 코드표의 역순)</td></tr>
<tr><td>-o</td><td>정렬 결과를 파일명으로 저장</td></tr>
<tr><td>-c</td><td>파일이 정렬되었는지 검사<br>
정렬되었으면 아무런 출력도 하지 않고 정렬되지 않았다면 정렬이 되지 않았다는 메시지 출력</td></tr>
<tr><td>-n</td><td>숫자를 문자가 아닌 숫자값으로 취급해서 수의 크기대로 정렬</td></tr>
<tr><td>-u</td><td>중복되는 줄은 한 줄만 출력(--unique)</td></tr>
<tr><td>-M</td><td>월 표시 문자로 정렬할 때 사용(--month-sort)

- unknown < JAN < ... < DEC
</td></tr>
<tr><td>-t</td><td>필드 구분자를 지정할 때 사용<br>기본 필드 구분은 공백</td></tr>
<tr><td>-k n[,m]</td><td>정렬할 위치를 지정하는 옵션으로 n번째 필드를 기준으로 정렬<br>m이 지정되었으면 n필드에서 시작해서 m필드에서 끝남</td></tr>
</table>

#### cut
- 데이터의 열(column)을 추출할 때 사용
- 추출 데이터는 파일 각 줄의 글자(character), 바이트(byte) 또는 필드(field)

```
cut option file
```

주요 옵션
<table>
<tr><td>-c</td><td>문자 수를 기준으로 추출(--characters)</td></tr>
<tr><td>-f</td><td>파일의 필드를 기준으로 추출(--fields)</td></tr>
<tr>-d<td></td><td>필드 구분자를 지정(--delimeter)<br> 기본 필드 구분은 [tab]</td></tr>
</table>

#### split
- 하나의 파일을 여러 개의 작은 파일로 분리하는 명령
- 옵션을 지정하지 않으면 기본 값이 1000줄 단위로 파일을 분리
- 별도의 파일명을 지정하지 않으면 nameaa, nameab, nameac 등으로 알파벳 순서로 생성됨

```
split [option] file [file_name]
```

주요 옵션
<table>
<tr><td>-b 값</td><td>파일을 주어진 값의 바이트 크기로 분리(--bytes=값)<br> 값은 단위로 K, M 등을 붙여서 사용 가능</td></tr>
<tr><td>-l 값 | -값</td><td>파일을 주어진 값의 줄 크기로 분리(--lines=값)<br></td></tr>
</table>

#### tr
- 텍스트 파일 안에 있는 임의의 문자(열)를 원하는 문자로 바꾸거나 제거할 때 사용하는 명령
- 원본의 파일 내용이 바뀌지 않는 스트리밍 명령어
- 명령행에서 사용하는 경우 반드시 < 기호를 이용하여 입력을 지정

```
tr [option] 문자1 문자2 < 파일명
```

주요 옵션
<table>
<tr><td>-d</td><td>주어진 문자 삭제(--delete)</td></tr>
<tr><td>-s</td><td>문자가 중복된 경우 하나만 남기고 모두 삭제(--squeeze-repeats)</td></tr>
</table>

사용 예
```
$ tr '[a-z]' '[A-Z]' < tr.txt > tr2.txt
tr.txt 내용을 대문자로 바꾸고 tr2.txt로 저장

$ tr -d '\r' < windows.txt
windows.txt에서 CR(carrige return) 제거
```


#### sed
- 스트림 편집기(stream editor)로 셸 스크립트나 다른 명령의 출력을 여과하는 파이프라인을 사용
- 파일의 내용을 변경 없이 화면상 출력만 필터링
- 라인 단위 처리로, 버퍼를 사용하지 않고 출력
  - 큰 파일 처리에 적합

```
# 텍스트 변경 or 삽입 시
sed 'coomand\
>문자열' 텍스트파일명

# 텍스트 삭제 시
sed [option] 'command' 텍스트파일명
```

주요 옵션
<table>
<tr><td>-n</td><td>특정한 행을 지정할 때 사용(--quiet, --silent)</td></tr>
<tr><td>-f 파일명</td><td>특정한 스크립트가 들어있는 파일로부터 읽어 들여 처리(--file=파일명)</td></tr>
<tr><td>-i 표현식</td><td>치환구문과 함께 사용하며, 치환된 내용을 출력하지 않고 파일을 직접 수정(--in-place)</td></tr>
</table>


command
<table>
<tr><td>na\ text</td><td>n행 밑에 문자열을 추가하여 출력<br>n 생략 시 매 행마다 text를 덧붙여 출력</td></tr>
<tr><td>ni\ text</td><td>n행 앞에 문자열을 추가하여 출력<br>n 생략 시 매 행 앞에 text를 덧붙여 출력</td></tr>
<tr><td>nd</td><td>지정한 n행을 삭제하고 출력<br>n 생략 시 아무것도 출력하지 않음<br>'1,4d'와 같이 범위 지정 가능</td></tr>
<tr><td>np</td><td>n행을 한 번 더 출력<br>n 생략 시 매 행을 두 번씩 출력<br>'/문자열/p' : 해당 문자열이 있는 행 두 번 출력</td></tr>
<tr><td>nq</td><td>n행까지 출력 후 종료<br>n 생략 시 첫 줄만 출력</td></tr>
<tr><td>r 파일명</td><td>파일을 읽어서 출력</td></tr>
<tr><td>s/string1/string2</td><td>각 행의 첫 번째 string1을 string2로 바꿈</td></tr>
<tr><td>s/string1/string2/g</td><td>모든 string1을 string2로 바꿈<br>/대신 \ 사용 가능</td></tr>
</table>

![sed](https://github.com/dee021/TIL/blob/linuxmaster/Linux/img/sed.png)

```
$ sed -n '21,30p' /etc/passwd > b.txt
/etc/passwd의 21번째 행부터 30번째 행까지 b.txt로 저장
```

#### awk
- 일종의 프로그래밍 언어로서 주로 패턴 검색과 조작 시에 주로 사용됨
- 다른 프로그램들과 연계해서 이용되는 경우가 대부분이며, 리눅스 셸에서는 보통 grep 명령으로 넘어온 데이터들의 값을 처리하거나 sed과 함께 정규화된 양식을 처리하는 경우 많이 사용


```
awk [options] 'pattern{action}' filename
```

주요 옵션
<table>
<tr><td>-F 필드구분자</td><td>필드구분자를 지정할 때 사용(--field-separator)<br>기본 필드구분자는 공백</td></tr>
<tr><td>-f 파일명</td><td>특정 스크립트 파일을 지정할 때 사용(--file)</td></tr>
</table>

주요 패턴
<table>
<tr><td>BEGIN</td><td>입력 파일을 읽어 들이기 전에 제시되는 문자를 실행시킬 때 지정</td></tr>
<tr><td>END</td><td>awk가 모든 입력을 처리한 후, 옆에 제시되는 문자를 실행시키도록 할 때 지정</td></tr>
<tr><td>/문자열/</td><td>문자열과 일치하는 라인을 찾아 action을 실행</td></tr>
</table>

주요 액션
<table>
<tr><td>print</td><td>제시된 표현식을 표준 출력</td></tr>
</table>

![awk1](https://github.com/dee021/TIL/blob/linuxmaster/Linux/img/awk1.png)
![awk2](https://github.com/dee021/TIL/blob/linuxmaster/Linux/img/awk2.png)
