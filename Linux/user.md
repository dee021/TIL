# 사용자 관리

## 사용자
root 사용자(Privileged User, Super User)

- 시스템운영에 있어서 모든 권한을 행사하는 계정
- UID 0 부여

일반 사용자(Normal user, Unprivileged user)

- 로그인이 가능한 사용자와 시스템의 필요에 의해 생성된 시스템 계정으로 나뉨
- 시스템에 대해 제한적인 권한을 행사
- UID 1부터 부여(CentOS는 사용자 생성 시 1000번부터 할당)

### root 계정 관리 / 주의사항

1. root 계정 이외의 다른 슈퍼유저가 존재하는지 점검한다. root 이외의 다른 사용자의 UID가 0인지 점검
2. PAM(Pluggable Authentication Modules)를 이용하여 root 계정으로 직접 로그인하는 것을 막는다. 특히 텔넷, ssh와 같은 원격 로그인 서비스를 통한 접근은 확실히 통제하도록 한다.
3. 관리자라고 하더라도 일반 계정으로 로그인하도록 하며, root 권한이 필요한 경우에 su 명령의 사용을 유도한다.
4. 환경 변수 TMOUT를 설정하여 무의미하게 장시간 로그인하는 것을 막는다.
5. 일반 사용자에게 특정 명령어 권한만 할당해줄 경우, su 보다 sudo 를 이용하도록 한다.

### 사용자 계정 관리
#### useradd

- 사용자의 ID를 생성하는 명령
- root 권한자가 root 이외의 사용자를 생성할 때 사용하는 명령
- addusre도 가능
```
useradd [option] 사용자계정이름
```

주요 옵션
<table>
<tr><td>-p</td><td>사용자 암호 지정(--password)<br>현재 리눅스에서는 암호화된 값을 사용하므로 암호화된 값으로 지정해야 함</td></tr>
<tr><td>-d</td><td>홈 디렉터리 지정(--home-dir)<br>최종 디렉터리만 생성하므로 중간 경로가 있는 경우에는 미리 생성해야 함</td></tr>
<tr><td>-g</td><td>그룹 지정(--gid)<br>지정할 그룹이 미리 생성되어 있어야 함<br>
별도로 지정하지 않으면 레드햇 계열에서는 아이디와 동일한 그룹에 포함시키고, 다른 배포판에서는 users에 포함시킴</td></tr>
<tr><td>-G</td><td>기본 그룹 이외에 추가 그룹 지정(--groups)</td></tr>
<tr><td>-c</td><td>사용자 생성 시 사용자에 대한 설명을 설정(--comment)</td></tr>
<tr><td>-s</td><td>사용자가 이용할 셸 지정(--shell)</td></tr>
<tr><td>-D</td><td>/etc/default/useradd에 설정된 유저추가와 관련된 기본 사항들을 보여줌(--defaults)</td></tr>
<tr><td>-m</td><td>사용자 생성 시 홈 디렉터리를 생성하는 옵션(--create-home)<br>레드햇 리눅스는 이 옵션을 사용하지 않아도 홈 디렉터리가 생성됨</td></tr>
<tr><td>-k</td><td>사용자 생성 시 제공되는 환경 파일들이 지정된 디렉터리 지정<br>
기본값은 /etc/skel</td></tr>
<tr><td>-f</td><td>사용자의 패스워드 만기일을 날짜수로 지정(--inactive)</td></tr>
<tr><td>-e</td><td>계정의 만기일을 YYYY-MM-DD 형식으로 지정(--expiredate)</td></tr>
<tr><td>-u</td><td>UID 값 지정(--uid)</td></tr>
</table>


![userDefault](https://github.com/dee021/TIL/blob/linuxmaster/Linux/img/useraddDefault.png)


```
useradd -D option
```
- /etc/default/useradd 파일의 설정을 변경한다

주요 옵션
<table>
<tr><td>-g</td><td>기본 그룹 지정<br>여기에서 지정하는 값은 /etc/group내에 있어야 한다<br>
LSB(Linux Standard Base)를 따라가는 리눅스 배포판인 경우 기본 그룹 지정이 가능하나, 레드햇 계열 리눅스에서는 /etc/default/useradd 파일에는 반영되나 실제 사용자 추가 시 적용되지 않음</td></tr>
<tr><td>-b</td><td>사용자 홈 디렉터리의 상위 디렉터리를 지정</td></tr>
<tr><td>-f</td><td>INACTIVE행과 관련된 옵션으로 패스워드 유효 기간이 만료된 후에 언제 이 계정을 사용할 수 없도록 할것인지 하루 단위로 설정</td></tr>
<tr><td>-e</td><td>사용자 계정 만료일을 'yyyy-mm-dd'형식으로 지정</td></tr>
<tr><td>-s</td><td>사용자의 기본 셸 지정</td></tr>
</table>



#### passwd

- 등록된 사용자의 암호를 지정하거나 변경하는 명령
- 리눅스에서는 계정 생성 후 암호를 지정하지 않으면 로그인이 되지 않음
- passwd만 입력하면 현재 로그인 사용자의 암호가 변경됨
- root만 다른 사용자의 암호 변경 가능

```
passwd [사용자명]
```

#### su(substitute user)

- 시스템에 로그인되어 있는 상태에서 다른 사용자의 권한으로 셸을 실행할 수 있도록 전환하는 명령

```
su [option] [사용자명]
```
- 사용자명 생략 시 root로 사용자 전환
- 원래 사용자로 돌아가려면 exit 입력

주요 옵션
<table>
<tr><td>-, -l</td><td>전환하려는 사용자가 실제 로그인한 것처럼 셸 이용(--login)</td></tr>
<tr><td>-c</td><td>사용자를 전환하지 않고 다른 사용자의 권한으로 명령을 일시적으로 수행(--command)</td></tr>
</table>

#### usermod

- 사용자의 셸, 홈 디렉터리, 그룹, UID, GID 등을 변경하는 명령

```
usermod [option] 사용자계정
```

주요 옵션
<table>
<tr><td>-d</td><td>사용자의 홈 디렉터리 변경(--home)<br>변경할 디렉터리는 미리 생성되어야 함</td></tr>
<tr><td>-m</td><td>사용자의 홈 디렉터리 변경 시 기존에 사용하던 파일 및 디렉터리를 옮겨주는 옵션(--move-home)<br>-d 옵션과 함께 쓰임</td></tr>
<tr><td>-g</td><td>사용자의 그룹 변경</td></tr>
<tr><td>-s</td><td>사용자의 셸 변경</td></tr>
<tr><td>-u</td><td>사용자의 UID 변경</td></tr>
<tr><td>-e</td><td>계정만기일 변경(--expiredate)<br>
'YYYY-MM-DD' 또는 'MM/DD/YY' 형식으로 지정</td></tr>
<tr><td>-f</td><td>패스워드 만기일이 지난 후 패스워드에 Lock을 설정할 유예 기간을 지정(--inactive)</td></tr>
<tr><td>-c</td><td>사용자의 간단한 정보를 입력하거나 변경(--comment)</td></tr>
<tr><td>-G</td><td>추가 그룹 지정(--groups)</td></tr>
<tr><td>-a</td><td>-G 옵션과 함께 사용하여 기존의 2차 그룹 이외에 추가로 2차 그룹을 지정(--append)</td></tr>
<tr><td>-p</td><td>암호화된 패스워드를 변경(--password)<br>암호화된 값으로 변경해야 함</td></tr>
<tr><td>-l</td><td>사용자의 아이디 변경(--login)</td></tr>
<tr><td>-L</td><td>사용자의 패스워드에 Lock을 걸어 로그인을 막는다(--lock)</td></tr>
<tr><td>-U</td><td>사용자 패스워드에 설정된 잠금을 푼다(--unlock)</td></tr>
</table>

#### userdel

- 사용자 계정을 삭제하는 명령

```
userdel [option] 사용자계정
```

주요 옵션
<table>
<tr><td>-r</td><td>사용자의 홈 디렉터리 및 메일 관련 파일까지 삭제한다(--remove)</td></tr>
</table>

#### passwd

- 패스워드를 부여하거나 변경

```
passwd [option] 사용자계정
```

주요 옵션
<table>
<tr><td>-S</td><td>사용자의 패스워드 정보 출력</td></tr>
<tr><td>-l</td><td>사용자 패스워드에 lock 설정(--lock)<br>/etc/shadow의 두 번째 필드 맨 앞에 '!!'를 넣어 로그인을 막는다.</td></tr>
<tr><td>-u</td><td>사용자 패스워드 잠금을 해제(--unlock)</td></tr>
<tr><td>-d</td><td>사용자의 패스워드를 제거<br>/etc/shadow의 두 번째 필드 내용이 삭제되며, 패스워드 없이 로그인이 가능하다</td></tr>
<tr><td>-n</td><td>패스워드 변경까지의 최소 날짜. 패스워드 변경 후 최소로 사용해야 하는 날짜수</td></tr>
<tr><td>-x</td><td>현재 패스워드의 유효 기간을 지정</td></tr>
<tr><td>-w</td><td> 패스워드 만료 전 경고 날짜를 지정</td></tr>
<tr><td>-i</td><td>패스워드 만료 후 실제 로그인이 불가능하게 되기까지의 유예 기간 설정</td></tr>
<tr><td>-e</td><td>다음 로그인 시 반드시 패스워드를 변경하도록 할 때 사용<br>/etc/shadow의 세 번째 필드값이 0으로 설정됨</td></tr>
</table>

![passwdS](https://github.com/dee021/TIL/blob/linuxmaster/Linux/img/passwdS.png)

> PS는 패스워드가 세팅되었다는 것을 의미하고 2023-08-04는 최근 패스워드 설정 혹은 변경일을 의미
> 이후 순서대로 패스워드 변경 후 최소 보유일(may), 패스워드 최대 보유일(must), 패스워드 만기일 전 경고일, 패스워드 만기 후 계정이 불가능하게 되기 전 유효 기간을 의미

#### chage

- 사용자의 패스워드에 대한 정보를 출력하고 설정
- /etc/shadow의 날짜 관련 필드 설정을 모두 할 수 있는 명령

```
chage [option] 사용자계정
```

주요 옵션
<table>
<tr><td>-l</td><td>사용자 패스워드에 대한 정보 출력(--list)</td></tr>
<tr><td>-d</td><td>최근 패스워드를 바꾼 날을 수정(--lastday)<br>'YYYY-MM-DD' 형식으로 지정해도 됨</td></tr>
<tr><td>-m</td><td>패스워드 변경의 최소 날짜를 지정(--mindays)</td></tr>
<tr><td>-M</td><td>패스워드 변경 없이 사용 가능한 최대 날짜 지정(--maxdays)</td></tr>
<tr><td>-I</td><td>패스워드 최대 사용 기간 만료 후 실제로 패스워드에 잠금을 설정하기까지의 유예 기간 설정(--inactive)</td></tr>
<tr><td>-E</td><td>계정이 만기되는 날 지정(--expiredate)<br>MM/DD/YY 또는 YYYY-MM-DD 형식으로 지정</td></tr>
<tr><td>-W</td><td>패스워드 만료 전 변경을 요구하는 경고 날짜 지정(--warndays)</td></tr>
</table>

#### chpasswd

- 사용자의 패스워드를 변경하는 명령
- 보통 리다이렉션으로 파일을 입력으로 주어, 여러 사용자들의 패스워드를 한 번에 변경할 때 사용
- 명령 실행 시 상호작용 모드로 들어가 '사용자명:암호' 형태로 지정하며 [ctrl] + [d]를 눌러 종료한다

```
chpasswd [option] [< 파일]
```

주요 옵션
<table>
<tr><td>-e</td><td>암호화된 패스워드 값을 사용할 경우 지정(--encrypted)</td></tr>
<tr><td>-c</td><td>패스워드에 사용될 암호화 알고리즘을 지정할 때 사용(--crypt-method)<br>DES, MD5, SHA256, SHA512 등 지정 가능</td></tr>
</table>


## 그룹

- 파일 공유를 원하는 사용자들을 같은 그룹으로 묶어 허가권(Permission) 설정을 통해 파일 및 디렉터리를 공유할 수 있음
- 리눅스에서 모든 사용자는 하나 이상의 그룹에 속한다
  - 사용자 추가 시 그룹 설정을 하지 않으면 GID 100번 그룹(users 그룹)에 속함
  - 레드햇 계열 리눅스는 사용자간의 불필요한 공유를 막기 위해 사용자 아이디와 동일한 이름의 그룹에 속함
- 리눅스에 등록된 그룹 목록은 `/etc/group`에서 확인 가능
- 그룹의 패스워드는 `/etc/gshadow`에서 확인 가능


### 그룹 계정 관리
#### groupadd
새로운 그룹을 생성하는 명령

- root 계정만 사용 가능한 명령

```
groupadd [option] 그룹명
```

주요 옵션
<table>
<tr><td>-g</td><td>생성과 동시에 GID를 부여

- GID 0~999는 시스템 레벨 계정을 위해 예약되었으므로 1000번 이상을 사용해야 함
</td></tr>
<tr><td>-r</td><td>생성하는 그룹을 시스템 그룹으로 생성할 때 사용<br>GID 0~999가 할당됨</td></tr>
</table>

#### groupmod

- 그룹명이나 GID를 변경하는 명령

```
groupmod [option] 그룹명
```

주요 옵션
<table>
<tr><td>-n</td><td>그룹의 이름 변경(--new-name)</td></tr>
<tr><td>-g</td><td>그룹의 GID 변경(--gid)</td></tr>
</table>

#### groupdel

- 생성된 그룹을 삭제하는 명령
- 주 그룹으로 속한 사용자가 없어야 함
- 2차 그룹으로 속한 사용자는 존재해도 무방

```
groupdel 그룹명
```

#### gpasswd

- 그룹의 패스워드를 설정하거나 그룹 관리자를 지정하는 명령
- 그룹 관리자는 해당 그룹에 속하지 않아도 지정 가능
- 그룹 관리자는 다른 사용자들을 그룹의 2차 그룹으로 속하게 하거나 그룹 패스워드를 설정할 수 있음
- 그룹 패스워드가 설정되면 해당 그룹에 속하지 않은 사용자들이 newgrp 명령을 이용하여 그룹 패스워드 입력 후 일시적으로 1차 그룹을 변경할 수 있음

```
gpasswd [option] group
```

주요 옵션
<table>
<tr><td>-A</td><td>root가 그룹 관리자를 지정할 때 사용</td></tr>
<tr><td>-M</td><td>root가 그룹 멤버를 지정할 때 사용</td></tr>
<tr><td>-a</td><td>그룹 관리자가 그룹에 사용자를 추가할 때 사용</td></tr>
<tr><td>-d</td><td>그룹 관리자가 그룹에서 사용자를 제외시킬 때 사용</td></tr>
<tr><td>-r</td><td>그룹 패스워드를 제거<br>그룹 패스워드가 제거되면 기존에 속해 있던 그룹 사용자만 newgrp 명령을 이용하여 1차 그룹을 변경할 수 있음</td></tr>
<tr><td>-R</td><td>그룹 패스워드의 사용을 비활성화시켜 해당 그룹 사용자만 newgrp 명령을 이용하여 1차 그룹을 변경할 수 있음</td></tr>

</table>

#### newgrp

- 사용자가 일시적으로 1차 그룹을 변경할 때 사용하는 명령
- 해당 그룹에 소속된 경우 패스워드 입력 없이 가능
- 해당 그룹에 소속되지 않은 경우 그룹 패스워드가 설정되어 있는 경우 패스워드 입력 후 가능
- 그룹 전환 후, 원래 그룹으로 돌아가려면 exit 입력

```
newgrp 그룹명
```

### 사용자 관리 명령
#### users

- 시스템에 로그인되어 있는 사용자의 아이디를 출력

```
users
```

#### who

- 시스템에 로그인되어 있는 사용자를 출력

```
who [option]
```

주요 옵션
<table>
<tr><td>-b</td><td>시스템의 부팅 시간 출력(--boot)</td></tr>
<tr><td>-d</td><td>죽은 프로세스 출력(--dead)</td></tr>
<tr><td>-H</td><td>출력되는 정보의 헤더 출력(--heading)</td></tr>
<tr><td>-l</td><td>시스템 로그인 프로세스 출력(--login)</td></tr>
<tr><td>-p</td><td>init 프로세스에 의해 발생되어 활성화된 프로세스 출력(--process)</td></tr>
<tr><td>-r</td><td>현재의 런레벨 출력(--runlevel)</td></tr>
<tr><td>-t</td><td>마지막으로 시스템 시간이 변경된 정보 출력(--time)</td></tr>
<tr><td>-T</td><td>사용자의 메시지 상태를 출력(-w, --megs)</td></tr>
<tr><td>-u</td><td>시스템에 로그인한 사용자의 목록 출력</td></tr>
<tr><td>-m</td><td>표준 입력과 연관된 호스트명과 사용자명 출력<br>'who am i'와 동일</td></tr>
</table>

#### whoami

- 실질적으로 사용 중인 권한자 출력
```
whoami
```

#### w
- 시스템에 로그인되어 있는 사용자와 사용자가 수행중인 작업 출력

```
w [option] [user]
```

주요 옵션
<table>
<tr><td>-h</td><td>header를 출력하지 않는다</td></tr>
<tr><td>-s</td><td>LOGIN@, JCPU, PCPU를 제외하고 간략히 출력</td></tr>
</table>

![w](https://github.com/dee021/TIL/blob/linuxmaster/Linux/img/w.png)

<table>
<thead><td>항목</td><td>의미</td></thead>
<tr><td>USER</td><td>시스템에 로그인한 사용자의 아이디</td></tr>
<tr><td>TTY</td><td>Terminal type

- 로컬 시스템의 콘솔로 접속한 경우 ttyn(n은 정수) 표기
- 외부에서 telnet 등으로 접속하거나 X-Window 터미널은 pts/n(n은 정수)로 표기
</td></tr>
<tr><td>FROM</td><td>접속한 위치

- 로컬 시스템의 콘솔로 접속한 경우 하이픈(-)
- X-Window로 로그인한 경우 ':0'
- X-Window의 터미널인 경우 ':0.0'
- 외부에서 접속한 경우 원격의 호스트 도메인명이나 IP 표기
</td></tr>
<tr><td>LOGIN@</td><td>시스템에 로그인한 시간</td></tr>
<tr><td>IDLE</td><td>최종 명령 수행 후 대기 시간</td></tr>
<tr><td>JCPU</td><td>(JOB CPU) 시스템에 로그인한 후에 CPU를 사용한 시간</td></tr>
<tr><td>PCPU</td><td>(Process CPU) WHAT 컬럼에 표시된 작업에 의해 사용된 프로세스 시간</td></tr>
<tr><td>WHAT</td><td>현재 사용 중인 셸이나 작업 등이 표시됨</td></tr>
</table>

#### logname
- 사용자의 로그인 계정 출력
- su 명령으로 사용자 전환했을 경우 최초의 로그인 계정 출력
```
logname
```

#### id
- 시스템에 등록된 아이디에 대한 정보를 출력하는 명령
- UID, GID, 속한 그룹 정보 출력
- SELinux를 사용하는 리눅스 배포판에서는 context를 추가로 출력

```
id [option] [user]
```

<table>
<tr><td>-g</td><td>주 그룹의 GID만 출력</td></tr>
<tr><td>-G</td><td>사용자가 속한 모든 그룹의 GID 출력</td></tr>
<tr><td>-u</td><td>사용자의 UID만 출력</td></tr>
<tr><td>-n</td><td>숫자값인 UID, GID 대신 이름으로 출력</td></tr>
</table>

#### groups
- 사용자가 속한 그룹명 출력

```
groups [user]
```
- user 생략 시 로그인한 사용자에 대한 정보를 출력

#### lslogins
- 시스템 전체 사용자의 정보를 출력하는 명령
- /etc/passwd, /etc/shadow, 로그 파일(wtmp, btmp)에서 관련 정보를 가져와 출력

```
lslogins [option]
```

주요 옵션
<table>
<tr><td>-G</td><td>각 사용자의 그룹 정보 출력</td></tr>
<tr><td>-L</td><td>각 사용자의 마지막 로그인 정보 출력(--last)</td></tr>
<tr><td>-u</td><td>사용자 계정 정보를 출력하는 옵션<br>일반적으로 시스템 계정을 제외하고 실제 로그인이 되는 계정 위주로 관련 정보를 출력</td></tr>
</table>

#### write
- 로그인한 다른 사용자에게 메시지를 보내는 명령

```
write 사용자계정 [ttyname]
```
- 명령 실행 후 메시지를 입력하고 [Ctrl] + [d]를 입력하면 메시지가 전송됨
- ttyname : 동일한 사용자가 여러 곳에서 로그인한 경우 특정 터미널에 로그인한 사용자에게 메시지를 전송할 때 사용

#### wall
- 로그인한 모든 사용자의 터미널에 메시지를 보내는 명령

```
wall [메시지]
```
- wall만 입력한 경우 메시지를 입력하고 [Ctrl] + [d]를 입력하면 메시지가 전송됨

#### mesg
- 사용자가 write를 사용해서 들어오는 메시지 수신 여부를 확인하고 제어하는 명령

```
mesg [n | y]
```
- 'mesg n'으로 메시지 수신 거부 상태로 설정하더라도 root가 보내는 메시지는 거부되지 않음
