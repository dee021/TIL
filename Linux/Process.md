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

![backgroundProcess](https://github.com/dee021/TIL/blob/main/Linux/img/bgps.png)
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

![ntsysv](https://github.com/dee021/TIL/blob/main/Linux/img/ntsysv.png)

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
