# 프로세스

<h3><b>Process</b></h3>

- 실행중인 프로그램
  - 프로그램을 설치하면 하드 디스크에 저장되고, 실행하면 메모리(보통 RAM)에 상주
  - 프로세스는 PID(Process Identity)가 할당되어 관리됨
- PCB(Process Control Block)을 지닌 프로그램
- 프로그램 카운터(Program Counter)를 지닌 프로그램
- 능동적 개체(entity)로, 순차적으로 수행하는 프로그램


## 프로세스의 생성
하나의 프로세스가 다른 프로세스를 실행하기 위한 시스템 호출 방법에는 `fork`와 `exec`가 있음

fork
- 새로운 프로세스를 위해 메모리를 할당받아 복사본 형태의 프로세스를 실행하는 형태
- 기존의 프로세스는 그대로 실행되어 있음
- 새로 생성된 프로세스는 원래의 프로세스와 똑같은 코드를 기반으로 실행
- 리눅스 부팅 완료 후 실행되는 프로세스들은 fork 방식으로 systemd(PID 1번)의 자식 프로세스가 됨

exec
- 원래의 프로세스를 새로운 프로세스로 대체하는 형태
- 호출한 프로세스의 메모리에 새로운 프로세스의 코드를 덮어씌움

## 멀티태스킹과 작업 전환

프로세스는 사용자의 입력에 관계없이 실행되는 `백그라운드 프로세스`와 명령 입력 후 수행종료까지 기다려야 하는 `포어그라운드 프로세스`가 있음

- 프로그라운드 프로세스 실행 명령 뒤에 '&'를 붙이면 백그라운드 프로세스로 실행됨

![backgroundProcess](https://github.com/dee021/TIL/blob/linuxmaster/Linux/img/bgps.png)
> [1]은 작업 번호(Job Number), 3497은 PID를 의미

포어그라운드 -> 백그라운드 전환
1) 작업 중인 프로세스를 일시 중지시킴
2) 'bg' 명령을 입력하여 전환

백그라운드 -> 포어그라운드 전환
1) 'jobs' 명령을 통해 대상 프로세스의 작업 번호 확인
2) `fg %작업번호` 또는 `fg 작업번호`를 입력하여 전환 

## 프로세스 관리
### 시그널(signal)

<table>
<thead><td>번호</td><td>이름</td><td>의미</td></thead>
<tr><td>1</td><td>SIGHUP(HUP)</td><td>(Hangup) 로그아웃과 같이 터미널에서 접속이 끊겼을 때 보내지는 신호<br>데몬 관련 환경 설정 파일을 변경시키고 변화된 내용을 적용하기 위해 재시작할 때 이 시그널이 사용됨</td></tr>
<tr><td>2</td><td>SIGINT(INT)</td><td>키보드로부터 오는 인터럽트 시그널<br> [Ctrl] + [c] 입력 시 보내지는 시그널</td></tr>
<tr><td>3</td><td>SIGQUIT(QUIT)</td><td>키보드로부터 오는 실행 중지 시그널<BR> [Ctrl] + [\] 입력 시 보내지는 시그널</td></tr>
<tr><td>9</td><td>SIGKILL(KILL)</td><td>프로세스를 강제로 종료시키는 시그널</td></tr>
<tr><td>15</td><td>SIGTERM(TERM)</td><td>(Terminate) 가능한 정상 종료시키는 시그널<br>kill명령의 기본 시그널</td></tr>
<tr><td>18</td><td>SIGCONT(CONT)</td><td>(Continue) 정지된 프로세스를 다시 실행시킬 때 사용</td></tr>
<tr><td>19</td><td>SIGSTOP(STOP)</td><td>터미널에서 입력된 정지 시그널</td></tr>
<tr><td>20</td><td>SIGTSTP(TSTP)</td><td>실행 정지 후 다시 실행을 계속하기 위해 대기시키는 시그널<BR>[Ctrl] + [z]를 입력했을 때 보내지는 시그널</td></tr>
</table>

### 데몬(deamon)
주기적이고 지속적인 서비스 요청을 처리하기 위해 계속 실행되는 프로세스

- 백그라운드로 실행됨
- 리눅스에서는 서버 역할을 하는 프로그램이 이에 해당

데몬 프로세스를 실행하는 방법에는 standalone 방식과 inetd 방식이 있음

<b>standalone 방식</b>

- 보통 부팅 시에 실행되어 해당 프로세스가 메모리에 계속 상주하면서 클라이언트의 서비스 요청을 처리하는 방식
- 웹, 메일 등과 같이 빈번한 요청이 들어오는 서비스인 경우

<b>inetd 방식</b>

- 클라이언트의 서비스 요청이 들어왔을 때 관련 프로세스를 실행시키고 접속 종료 후에는 자동으로 프로세스를 종료시키는 방식
- inetd 데몬이 메모리에 상주하면서 inetd 데몬이 관리하는 서비스에 대한 요청이 들어오면 관련 프로세스를 메모리에 상주시켜 요청을 처리
- 리눅스 커널 2.4 버전부터는 xinetd 데몬이 이 역할을 수행

#### 데몬 관련 유틸리티티

<b>ntsysv</b> : 텍스트 환경에서 커서를 이용하여 부팅 중 자동으로 실행되는 서비스를 설정하는 유틸리티

![ntsysv](https://github.com/dee021/TIL/blob/linuxmaster/Linux/img/ntsysv.png)

```
ntsysv [option]
```
option

<table><tr><td>--level 실행레벨</td><td>특정 실행레벨을 편집</td></tr></table>

<b>chkconfig</b> : 실행레벨에 따른 서비스의 on/off 설정 리스트를 설정하거나 출력

- System V 기반의 init 스크립트 시절에 사용하던 텍스트 기반의 명령형 프로그램
- systemd 기반으로 전환된 이후 System V 기반의 일부 서비스와 xinetd 기반 서비스만 제어 가능

```
chkconfig option [service]
```
option
<table>
<tr><td>--list</td><td>각 실행레벨에서의 서비스 설정 상태를 출력</td></tr>
<tr><td>--add 서비스</td><td>서비스를 추가한다</td></tr>
<tr><td>--del 서비스</td><td>서비스를 삭제한다</td></tr>
<tr><td>--level 레벨 서비스 on/off/reset</td><td>해당 레벨에서 특정 서비스의 상태를 설정</td></tr>
</table>


# 프로세스 관리
## 프로세스 제어
### 프로세스 우선순위

- 하나의 시스템에서 동시에 실행되는 프로세스들은 우선순위를 부여받아 관리됨
- 프로세스 우선순위는 `ps -l` 명령을 실행하여 PRI와 NI로 확인 가능

<b>PRI(priority)</b>

- 커널이 사용하는 우선순위 항목
- 값 : 0 ~ 139
  - 일반적으로 0 ~ 99는 시스템에 예약된 우선순위 값, 100 ~ 139는 사용자 공간 프로세스(user space process)에 할당됨
- 값이 작을수록 프로세스의 우선순위가 높음
- 인위적인 조작이 되지 않고, 시스템 상황에 따라 적절히 부여됨

<b>NI(nice)</b>

- root나 사용자가 조작하는 우선순위 값
- 값 : -20 ~ 19
- 값이 작을수록 우선순위가 높고, root만 값을 낮출 수 있음

### 프로세스와 /proc 디렉터리

<b>/proc</b>

- 일종의 가상 파일 시스템
- 시스템에 동작 중인 프로세스의 상태 정보와 기타 시스템 및 하드웨어 정보를 확인 할 수 있음

![proc](https://github.com/dee021/TIL/blob/linuxmaster/Linux/img/proc.png)

/proc 디렉터리의 주요 정보
<table>
<thead><td>파일 또는 디렉터리</td><td>설명</td></thead>
<tr><td>acpi</td><td>전원 관련 정보를 담고 있는 디렉터리이다.</td></tr>
<tr><td>bus</td><td>pci와 같은 bus 정보를 담고 있는 디렉터리이다.</td></tr>
<tr><td>cmdline</td><td>부팅 시에 실행되는 커널 관련 옵션에 대한 정보를 담고 있다.</td></tr>
<tr><td>cpuinfo</td><td>CPU에 관한 정보를 담고 있다.</td></tr>
<tr><td>devices</td><td>현재 커널에 설정된 디바이스 드라이버의 리스트 정보를 담고 있다.</td></tr>
<tr><td>dma</td><td>시스템에서 DMA 정보를 담고 있다.</td></tr>
<tr><td>filesystems</td><td>커널에 설정된 파일 시스템의 리스트 정보를 담고 있다.</td></tr>
<tr><td>interrupts</td><td>시스템에서 사용 중인 인터럽트(IRQ) 정보를 담고 있다.</td></tr>
<tr><td>iomem</td><td>메모리 번지별로 할당된 리스트 정보를 담고 있다.</td></tr>
<tr><td>ioports</td><td>시스템에서 사용 중인 I/O 주소를 담고 있다.</td></tr>
<tr><td>kallsyms</td><td>커널에 의해 공개된 심볼 정보를 담고 있다. 모듈이 참조할 수 있는 커널 내부의 함수나 변수의 심볼 정보를 가지고 있다.</td></tr>
<tr><td>kcore</td><td>시스템에 장착된 물리적 메모리의 이미지로 실제 하드 디스크의 용량을 차지하지는 않는다. 이 파일은 프로그램 실행 시 비정상 종료나 프로그램의 디버깅할 때에 유용하게 쓰인다.</td></tr>
<tr><td>kmsg</td><td>커널이 동작 중에 중요한 이벤트가 생기면 출력되는 메시지 정보를 담고 있다.</td></tr>
<tr><td>loadavg</td><td>최근 1분, 5분, 15분 동안의 평균 부하율을 기록하는 파일이다.</td></tr>
<tr><td>locks</td><td>flock() 및 fcntl() 등에 의한 현재 lock 파일에 대한 정보를 담고 있는 파일이다.</td></tr>
<tr><td>mdstat</td><td>RAID 사용 시에 관련 정보를 기록한다.</td></tr>
<tr><td>meminfo</td><td>물리적 메모리 및 스왑 메모리 정보가 들어 있는 파일이다.</td></tr>
<tr><td>misc</td><td>기타 장치에 대한 정보가 들어 있는 파일이다.</td></tr>
<tr><td>mounts</td><td>시스템에 마운트된 정보가 기록되어 있는 파일이다.</td></tr>
<tr><td>net</td><td>Network 관련 정보 파일이 들어 있는 디렉터리이다.</td></tr>
<tr><td>partitions</td><td>현재 활성화된 파티션 정보를 기록하고 있는 파일이다.</td></tr>
<tr><td>scsi</td><td>SCSI 관련 정보를 기록한 파일이 들어 있는 디렉터리이다.</td></tr>
<tr><td>self</td><td>/proc 디렉터리를 이용하고 있는 프로세스에 대한 정보를 담고 있는 디렉터리이다.</td></tr>
<tr><td>stat</td><td>커널과 시스템 상태 정보를 담고 있는 파일이다.</td></tr>
<tr><td>swaps</td><td>스왑 파티션 관련 정보를 기록한다.</td></tr>
<tr><td>sys</td><td>커널과 관련된 정보를 담고 있는 디렉터리로 sysctl 명령으로 제어한다.</td></tr>
<tr><td>sysvipc</td><td>SysV IPC 자원 정보인 Messages Queues, Semaphores, Shared Memory 관련 정보 파일이 들어 있는 디렉터리이다.</td></tr>
<tr><td>uptime</td><td>시스템 가동 시간에 대한 정보를 기록한다. uptime 명령어가 참조하는 파일이다.</td></tr>
<tr><td>version</td><td>커널 버전 정보를 기록한다.</td></tr>
</table>

<b>/proc/PID</b>

- 프로세스 생성 시 PID n번을 부여받은 프로세스의 정보는 /proc/n 디렉터리 안에 기록됨

/proc/PID 안의 주요 구성 요소
<table>
<thead><td>구성요소</td><td>설명</td></thead>
<tr><td>cmdline</td><td>명령행 옵션 정보가 들어있다.</td></tr>
<tr><td>cwd</td><td>작업 디렉터리를 가리킨다.</td></tr>
<tr><td>environ</td><td>프로세스의 환경에 대한 정보를 담고 있다.</td></tr>
<tr><td>exe</td><td>해당 프로세스를 실행시킨 명령어를 가리킨다.</td></tr>
<tr><td>fd</td><td>파일 지정자(file descriptor)에 대한 정보를 담고 있는 디렉터리이다.</td></tr>
<tr><td>maps</td><td>실행 명령과 라이브러리 파일의 메모리 맵 정보를 담고 있다.</td></tr>
<tr><td>mounts</td><td>시스템의 마운트 정보를 담고 있다.</td></tr>
<tr><td>root</td><td>해당 프로세스의 루트 디렉터리를 가리킨다.</td></tr>
<tr><td>stat</td><td>해당 프로세스의 상태를 나타낸다. 보통 ps 명령에 의해 사용된다.</td></tr>
<tr><td>statm</td><td>프로세스의 메모리 상태에 대한 정보를 담고 있다.<br>size(프로그램의 총 크기), resident(할당된 메모리의 크기), shared(공유된 페이지 수), trs(text의 페이지 수), drs(data/stack 페이지 수), lrs(library 페이지 수), dt(dirty 페이지 수)를 나타낸다.</td></tr>
<tr><td>status</td><td>프로세스의 상태 정보를 담고 있는 파일로 사람이 보기 쉬운 형태로 되어 있다.</td></tr>
</table>



### 프로세스 스케줄링(Scheduling)
- 특정한 시간에 특정한 작업을 수행하게 하는 것
- 리눅스에서는 `at`과 `cron`을 수행
  - 모두 데몬으로 실행 중에 있어야 함

#### at
- 지정한 시간에 원하는 명령이나 작업이 실행될 수 있도록 함
- 보통 한 번 실행되는 경우에 주로 사용
- atd 데몬에 의해 실행됨
- 지정한 작업은 qeueu에 저장되며, 저장된 작업들은 /var/spool/at 디렉터리 아래 파일로 저장됨

```
at [option] 시간
```

주요 옵션
<table>
<tr><td>-q 큐이름</td><td>작업의 대기 큐를 지정(기본큐로 a 사용)<br>사용 가능한 큐는 A-Z, a-z까지임</td></tr>
<tr><td>-c 작업</td><td>작업 정보 출력</td></tr>
<tr><td>-d</td><td>예약한 작업 삭제<br>atrm 명령과 동일</td></tr>
<tr><td>-l</td><td>큐에 있는 작업 출력<br>root인 경우 모든 작업 목록을 출력<br>atq 명령과 동일</td></tr>
<tr><td>-m</td><td>실행 결과를 메일로 통보</td></tr>
<tr><td>-f</td><td>지정한 파일로부터 작업을 읽어옮</td></tr>
</table>

<b>사용자 제한</b> : /etc/at.allow, /etc/at.deny 파일로 at 사용자를 제한

- /etc/at.allow 파일 존재 : /etc/at.deny 파일 존재 유무에 상관없이 /etc/at.allow 파일에 등록된 사용자만 가능
- /etc/at.deny 파일만 존재 : /etc/at.deny 파일에 존재하는 사용하는 사용자만 사용 불가
- 모두 존재하지 않은 경우 : root만 사용 가능


#### cron
- 주기적으로 프로세스 실행 시 사용
- 시스템 운영에 필요한 작업은 root 권한으로 /etc/crontab에 등록됨
- 사용자 필요에 의한 작업은 crontab 명령을 수행하여 등록 가능
  - 설정된 작업은 /var/spool/cron/사용자아이디 파일로 저장


crontab 파일 구성 필드

<table>
<thead><td>필드</td><td>설정 값 및 내용</td></thead>
<tr><td>minute</td><td>분. 0 ~ 59로 설정</td></tr>
<tr><td>hour</td><td>시. 0 ~ 23</td></tr>
<tr><td>day_of_month</td><td>날. 1 ~ 31</td></tr>
<tr><td>month</td><td>요일. 0(일요일) ~ 7(일요일) 혹은 sun, mon, ...으로 설정</td></tr>
<tr><td>day_of_week</td><td>사용자 이름. 일반적으로 생략해서 사용</td></tr>
<tr><td>user-name</td><td>실행할 명령어.</td></tr>
<tr><td>command</td><td></td></tr>
</table>

<b>crontab</b>

- 주기적인 작업을 등록하기 위해서 `crontab` 명령을 이용
- 파일명 지정 시 해당 파일에 설정된 내용을 불러와서 등록

```
crontab [option]
crontab [option] 파일명
```

주요 옵션
<table>
<tr><td>-l</td><td>crontab에 설정된 내용 출력</td></tr>
<tr><td>-e</td><td>crontab의 내용을 작성하거나 수정</td></tr>
<tr><td>-r</td><td>crontab의 내용이 저장된 파일을 삭제</td></tr>
<tr><td>-u</td><td>root 사용자가 특정 사용자의 crontab 파일을 다룰 때 사용</td></tr>
<tr><td>-i</td><td>-r 옵션으로 crontab 설정 파일을 삭제하기 전에 질의 진행</td></tr>
</table>

<b>cron 사용자 제한</b> : /etc/cron.allow, /etc/cron.deny 파일로 제한

- /etc/cron.allow 파일이 존재 : /etc/cron.deny 파일 존재와 상관없이 /etc/cron.allow 파일에 존재하는 사용자만 가능
- /etc/cron.deny 파일만 존재 : 해당 파일에 존재하는 사용자만 사용 불가능
- 모두 존재하지 않는 경우 : root만 사용 가능

## 프로세스 관련 명령어
#### ps(process status)
- 동작 중인 프로세스의 상태 출력
- System V, BSD, GNU에 따라 옵션 결과가 다르게 나타나고 표기법에 차이가 존재
  - System V 계열 : -(대시) 사용
  - BSD 계열 : - 미사용
  - GNU 계열 : -- 사용

```
ps [option]
```

주요 옵션
<table>
<tr><td>a</td><td>터미널과 연관된 프로세스를 출력(BSD 계열)<br>보통 x 옵션과 연계하여 모든 프로세스를 출력할 때 사용</td></tr>
<tr><td>u</td><td>프로세스의 소유자를 기준으로 출력</td></tr>
<tr><td>x</td><td>데몬 프로세스처럼 터미널에 종속되지 않는 프로세스 출력</td></tr>
<tr><td>l</td><td>프로세스의 정보를 길게 출력<br>우선순위와 관련된 PRI와 NI 값 확인 가능</td></tr>
<tr><td>e</td><td>해당 프로세스에 관련된 환경 변수 정보를 함께 출력</td></tr>
<tr><td>f</td><td>프로세스간 상속 관계를 트리 구조로 출력</td></tr>
<tr><td>p</td><td>특정 PID 지정(BSD 계열)</td></tr>
<tr><td>-A</td><td>모든 프로세스 출력(System V 계열)</td></tr>
<tr><td>-e</td><td>모든 프로세스 출력</td></tr>
<tr><td>-a</td><td>세션 리더(일반적으로 로그인 셸)을 제외하고 터미널에 종속되지 않은 모든 프로세스 출력</td></tr>
<tr><td>-f</td><td>유닉스 스타일로 출력하는 옵션<br>UID, PID, PPID 등이 함께 출력</td></tr>
<tr><td>-C 프로세스명</td><td>지정한 프로세스만 출력</td></tr>
<tr><td>-o 값</td><td>출력 포맷 지정<br>pid, tty, time, cmd 등 지정 가능</td></tr>
<tr><td>-p</td><td>특정 PID를 지정할 때 사용(System V 계열)</td></tr>
<tr><td>-u</td><td>특정 사용자의 프로세스 정보를 확인할 때 사용<br>미지정 시 현재 사용자 기준으로 정보 출력</td></tr>
</table>

![ps](https://github.com/dee021/TIL/blob/linuxmaster/Linux/img/ps.png)

ps 주요 항목
<table>
<tr><td>USER</td><td>BSD 계열에서의 프로세스 소유자 이름</td></tr>
<tr><td>UID</td><td>SYSTEM V 계열에서의 프로세스 소유자 이름</td></tr>
<tr><td>%CPU</td><td>CPU 사용비율의 추정치(BSD)</td></tr>
<tr><td>%MEM</td><td>메모리 사용비율의 추정치(BSD)</td></tr>
<tr><td>VSZ</td><td>K 단위 또는 페이지 단위의 가상 메모리 사용량</td></tr>
<tr><td>RSS</td><td>실제 메모리 사용량(Resident Set Size)</td></tr>
<tr><td>TTY</td><td>프로세스와 연관된 터미널</td></tr>
<tr><td>STAT</td><td>현재 프로세스의 상태 코드</td></tr>
<tr><td>START</td><td>프로세스 시작 시간 또는 날짜</td></tr>
<tr><td>TIME</td><td>총 CPU 사용 시간</td></tr>
<tr><td>COMMAND</td><td>프로세스의 실행 명령행</td></tr>
<tr><td>STIME</td><td>프로세스가 시작된 시간 혹은 날짜(SYS V)</td></tr>
<tr><td>C, CP</td><td>짧은 기간 동안의 CPU 사용률(C : SYS V, CP, BSD)</td></tr>
<tr><td>F</td><td>프로세스의 플래그

- 1 : exec 호출 없이 fork 된 경우
- 4 : 슈퍼유저 권한 사용자
</td></tr>
</table>

STAT 항목의 주요 값
<table>
<tr><td>R(Running)</td><td>실행 중 혹은 실행될 수 있는 상태(실행 큐 내에 존재)</td></tr>
<tr><td>S(Sleeping)</td><td>인터럽트에 의한 sleep 상태로 특정 이벤트가 끝나기를 기다리는 상태</td></tr>
<tr><td>D(Disk Wait)</td><td>디스크 I/O에 의해 대기하고 있는 상태</td></tr>
<tr><td>T(Traced or stopped)</td><td>정지된 상태(suspend)</td></tr>
<tr><td>W</td><td>paging 상태(2.6.xx 커널 이후 사용 안 함)</td></tr>
<tr><td>Z(Zombie)</td><td>좀비 프로세스. 작업이 종료되었으나 부모 프로세스로부터 회수되지 않아 메모리를 차지하고 있는 상태</td></tr>
<tr><td>X</td><td>죽어있는 상태. 상태값으로 볼 수 없음</td></tr>
<tr><td><</td><td>우선순위가 인위적으로 높아진 상태(사용자에 의해 nice된 것은 아님)</td></tr>
<tr><td>N(nice)</td><td>다른 사용자에 의해 인위적으로 우선순위가 낮아지 상태</td></tr>
<tr><td>L</td><td>메모리 안에서 페이지가 잠금된 상태(보통 real-time과 일반적 I/O에 의해 발생)</td></tr>
<tr><td>s</td><td>session Leader</td></tr>
<tr><td>|</td><td>멀티 쓰레드 상태(CLONE_THREAD)</td></tr>
<tr><td>+</td><td>포어그라운트 프로세스 그룹</td></tr>
</table>

#### PSTREE
- 프로세스의 상태를 트리 구조로 출력

```
pstree [option]
```

주요 옵션
<table>
<tr><td>-a</td><td>각 프로세스의 명령행 인자까지 출력</td></tr>
<tr><td>-h</td><td>현재 프로세스와 그것의 조상 프로세스를 하이라이트로 강조</td></tr>
<tr><td>-n</td><td>프로세스명 대신 PID로 정렬하여 출력</td></tr>
<tr><td>-p</td><td>PID 값을 함께 출력</td></tr>
</table>

#### top
- 동작중인 프로세스이 상태를 실시간으로 화면에 출력
- CPU, 메모리, 부하 상태 등도 확인 가능
- 명령 실행 상태에서 명령을 입력하여 프로세스 상태를 출력하거나 제어 가능

```
top [option]
```

주요 옵션
<table>
<tr><td>-d 갱신시간</td><td>갱신 시간 설정(단위: 초)</td></tr>
<tr><td>-p</td><td>특정 PID 값을 갖는 프로세스를 모니터링</td></tr>
<tr><td>-b</td><td>Batch mode로 실행하는 옵션<br>다른 프로그램이나 파일에 전송할 때 사용</td></tr>
<tr><td>-n 값</td><td>top 명령의 실행 횟수를 지정</td></tr>
</table>


![top](https://github.com/dee021/TIL/blob/linuxmaster/Linux/img/top.png)

top의 주요 항목

<table>
<tr><td>VIRT</td><td>작업에 의해 사용된 가상 메모리의 총 사용량</td></tr>
<tr><td>RES</td><td>프로세스가 사용하는 실제 메모리양(Resident size(kb))</td></tr>
<tr><td>SHR</td><td>프로세스가 사용하는 공유 메모리양</td></tr>
<tr><td>S</td><td>현재 프로세스의 상태</td></tr>
<tr><td>%CPU</td><td>CPU 사용량</td></tr>
<tr><td>%MEM</td><td>메모리 사용량</td></tr>
<tr><td>TIME+</td><td>프로세스가 시작하여 사용한 총 CPU 시간(1/100초 단위까지 표시)</td></tr>
<tr><td>COMMAND</td><td>프로세스를 실행한 명령</td></tr>
</table>

top 실행 상태에서의 명령
<table>
<tr><td>[Space]</td><td>화면을 갱신한다.</td></tr>
<tr><td>h, ?</td><td>도움말을 출력한다.</td></tr>
<tr><td>k</td><td>kill 명령을 내린다. PID 값을 입력하면 종료신호를 보낸다.</td></tr>
<tr><td>i</td><td>Zombie, idle 프로세스의 출력을 on/off한다.</td></tr>
<tr><td>n, #</td><td>출력하는 프로세스의 수를 지정한다.</td></tr>
<tr><td>q</td><td>top을 종료</td></tr>
<tr><td>r</td><td>Nice 값을 변경</td></tr>
<tr><td>s</td><td>화면을 갱신하는 시간 변경</td></tr>
<tr><td>F, f</td><td>보여줄 항목을 추가하거나 삭제</td></tr>
<tr><td>O, o</td><td>보여줄 항목의 순서를 바꿈</td></tr>
<tr><td>l</td><td>top의 맨 윗줄(uptime)을 on/off한다.</td></tr>
<tr><td>m</td><td>메모리의 과련된 항목을 on/off한다.</td></tr>
<tr><td>t</td><td>프로세스와 CPU 항목을 on/off한다.</td></tr>
<tr><td>c</td><td>Command line의 옵션을 on/off한다.</td></tr>
<tr><td>M</td><td>프로세스의 RSS 값을 정렬한다.</td></tr>
<tr><td>P</td><td>%CPU 값으로 정렬(기본값)</td></tr>
<tr><td>T</td><td>Time 값으로 정렬</td></tr>
<tr><td>W</td><td>바꾼 설정을 저장</td></tr>
</table>


#### kill
- 프로세스에 특정한 signal을 보내는 명령
- 옵션 생략 시 종료 신호(15, TERM, SIGTERM)을 보냄

```
kill [option] [signal] [PID 또는 %Job_number]
```

주요 옵션
<table>
<tr><td>-l</td><td>시그널의 종류를 출력</td></tr>
<tr><td>-s signal</td><td>시그널의 이름을 지정</td></tr>
</table>

#### killall
- 같은 데몬의 여러 프로세스를 한 번에 종료시킬 때 사용하는 명령
- 시그널을 지정하지 않으면 종료 시그널(TERM, SIGTERM)을 보냄

```
kilall [option] 프로세스명
```

주요 옵션
<table>
<tr><td>-l</td><td>시그널의 종류를 출력(--list)</td></tr>
<tr><td>-w</td><td>시그널을 받은 프로세스들이 종료될 때까지 대기(--wait)</td></tr>
<tr><td>-v</td><td>시그널이 전송된 결과 출력(--verbose)</td></tr>
<tr><td>-s signal</td><td>시그널의 이름을 지정(--signal)</td></tr>
<tr><td>-u 사용자명</td><td>특정 사용자의 프로세스를 지정할 때 사용(--user)</td></tr>
</table>

#### jobs
- 백그라운드로 실행중인 프로세스나 현재 중지된 프로세스의 목록 출력

```
jobs [option]
```

주요 옵션
<table>
<tr><td>-l</td><td>PID를 추가로 출력</td></tr>
</table>

#### fg
- 백그라운드 프로세스를 포어그라운드 프로세스로 전환

```
fg [%Job_number]
fs [Job_number]
```

#### bg
- 포그라운드 프로세스를 백그라운드 프로세스로 전환
- 포그라운드 프로세스를 [Ctrl] + [z]로 일시 중지시킨 후 전환 가능

```
bg
```

#### nice
- 프로세스의 우선순위를 변경하는 명령으로 NI 값을 설정할 때 사용
- 프로세스에 설정되어 있는 NI의 기본값은 0
- 지정 가능한 값의 범위는 -20 ~ 19이며, 값이 작을수록 우선순위가 높음
- 명령 실행 시 새로운 프로세스가 추가로 발생됨
- root만 NI 값을 낮출 수 있음

```
nice [option] 프로세스명
```

주요 옵션
<table>
<tr><td>-n 값, -값,<br>--adjustment=값</td><td>프로세스에 설정된 NI 값을 지정한 값만큼 증감</td></tr>
</table>

#### renice
- 실행중인 프로세스의 우선순위를 변경할 때 사용하는 명령
- PID, 사용자 이름, 프로세스의 그룹 ID를 이용
- 명령 실행 시 지정한 우선순위가 적용된 프로세스를 기존의 프로세스를 교체
- root만 NI 값을 낮출 수 있음

```
renice [option] NI값 PID
```

주요 옵션
<table>
<tr><td>-n</td><td>NI 값을 지정할 때 사용하는 옵션으로 생략 가능(--priority)</td></tr>
<tr><td>-p</td><td>PID를 지정하는 옵션(기본값)(--pid)</td></tr>
<tr><td>-u</td><td>사용자 이름을 지정하는 옵션(--user)</td></tr>
<tr><td>-g</td><td>프로세스의 그룹 ID를 지정하는 옵션(--pgrp)</td></tr>
<tr><td>-h</td><td>명령어의 간단한 사용법 출력(--help)</td></tr>
</table>

#### nohub
- 사용자가 로그아웃하거나 작업 중인 터미널창이 닫혀도 실행중인 프로세스를 백그라운드로 작업하는 명령
- 실행중인 프로세스의 표준 출력과 표준 에러는 'nohub.out' 파일을 생성하여 기록
  - 쓰기 작업이 불가능한 경우 '$HOME/nohub.out'을 생성하여 기록

```
nohub 명령
```

#### pgrep
- 프로세스 이름을 기반으로 PID를 검색하는 명령

```
pgrep [option] [pattern]
```

주요 옵션
<table>
<tr><td>-u/-g</td><td>특정 사용자/그룹이 실행시킨 프로세스 조회</td></tr>
<tr><td>-U/-G</td><td>특정 UID/GID를 갖는 사용자가 실행시킨 프로세스 조회</td></tr>
<tr><td>-t</td><td>특정 터미널에 실행 중인 프로세스 조회</td></tr>
<tr><td>-l</td><td>PID 외에 프로세스명도 같이 출력</td></tr>
</table>


#### pkill
- 프로세스명을 사용해서 특정 프로세스에 시그널을 보내는 명령
- 기본 시그널은 TERM 시그널
- 사용자 및 그릅명 등으로 지정 가능

```
pkill [option] [pattern]
```

주요 옵션
<table>
<tr><td>-u/-g</td><td>특정 사용자/그룹이 실행시킨 프로세스에 시그널 전송</td></tr>
<tr><td>-U/-G</td><td>특정 UID/GID를 갖는 사용자가 실행시킨 프로세스에 시그널 전송</td></tr>
<tr><td>-t</td><td>특정 터미널에 실행 중인 프로세스에 시그널 전송</td></tr>
<tr><td>-l</td><td>PID 외에 프로세스명도 같이 출력</td></tr>
</table>
