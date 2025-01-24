## 부팅(Booting)
컴퓨터의 전원이 켜진 후 운영체제가 가동되어 사용자가 사용할 수 있도록 만들어주는 과정
1. 컴퓨터 전원을 켜면 바이오스(BIOS; Basic Input Output System)는 컴퓨터에 장착된 하드웨어를 점검한다.
2. 바이오스는 하드웨어 검사가 끝나면 CMOS에 설정된 첫 번째 부팅 하드 디스크를 확인한다.
3. 첫 번째 하드 디스크의 MBR 영역에 있는 부트 매니저 프로그램을 실행한다.
4. 부트 매니저 프로그램은 관련 환경 설정 파일을 참고하여 운영체제 부팅을 시작한다.

 > MBR(Master Boot Record) : 디스크의 첫 번째 섹터에 해당하며 512byte. 부트 매니저 프로그램과 파티션 정보가 기록됨.
### 부트 매니저(Boot Manager)
- 부팅을 도와주는 역할을 하는 프로그램
- 하드 디스크의 맨 앞쪽 영역 MBR에 설치됨

<b>GRUB(Grand Unified BootLoader)</b>
- 환경 설정 파일 : /boot/grub2/grub.cfg, /etc/grub2.cfg(심볼릭 링크 파일)
  - /etc/grub.d 디렉터리 안의 파일과 /etc/default/grub 파일을 참고하여 동작함
  - 셸 스크립트 형태
- 환경 설정 파일 수정 후 grub2-mkconfig 명령 실행

![grub_file](https://github.com/dee021/TIL/blob/main/Linux/img/grub.png)
- GRUB_TIMEOUT=5 : GRUB 부트 화면에서 대기하는 시간(s)
- GRUB_DEFAULT=saved : 부팅할 모드를 선택하는 항목
  - 0 ~ N : 부트 화면에 제시된 목록 중 i+1번째 항목으로 부팅 
  - saved : 기본 메뉴 목록이 'GRUB_SAFEDEFAULT' 또는 'grub-set-default'에 의해 저장됨
> GRUB_DEFAULT=1 : 메뉴 선택 없을 시 2번째 항목의 운영체제로 부팅
- root 패스워드 분실 시 응급 복구 모드로 부팅하여 패스워드 재설정
  1) GRUB 부트 메뉴 화면에서 [e]를 눌러 편집 상태로 진입
  2) 커널 인자값 'ro rhgb quite LANG=ko_KR.UTF-8'를 지우고 'rw init=/bin/sh' 입력
  3) [Ctrl] + [x] 입력하여 부팅 진행
  4) 사용자 확인 후 passwd 명령으로 패스워드 변경
  5) 시스템 재부팅

```
grub2-mkconfig [-o 파일명]
```
- GRUB 환경 설정 파일을 생성하는 명령
- -o : GRUB의 환경 설정 파일을 생성할 때 지정(--output=파일명)

### systemd(System and Service Manager)
CentOS 7 부터 사용되는 시스템 부팅 및 서비스를 관리해주는 프로그램
- CentOS 6 이전 버전까지 init 사용

<b>systemd의 구조</b>
1. 유닛(unit)
<br>주요 유닛
   <table>
   <tr><td>service</td><td>서버에서 제공하는 서비스(demon)<br>관련 파일명이 '.service'로 끝난다.</td></tr>
   <tr><td>target</td><td>부팅 레벨, 특정 동기화 지점과 같이 유닛을 그룹화할 때 사용<br>관련 파일명이 '.target'으로 끝난다.</td></tr>
   <tr><td>socket</td><td>프로세스 간 통신에 사용되는 IPC(Inter-process communication) 소켓을 의미<br>
   systemd에 의해 제어되면서 소켓 기반으로 동작하는 파일 시스템 FIFO, 네트워크 소켓, IPC 등이 해당.
   <br>관련 파일명이 '.socket'으로 끝난다.</td></tr>
   <tr><td>path</td><td>특정 파일 시스템이 변경될 때까지 서비스의 활성화를 지연시킬 때 사용된다.<br>프린팅 시스템의 스풀 디렉터리를 사용하는 서비스에 사용된다.<br>관련 파일명이 '.path'로 끝난다.</td></tr>
   </table>
2. 서비스(service)
<br>시스템에서 제공하는 서비스. 웹 서버나 메일 서버 같은 데몬을 의미.
```
systemctl status '서비스명' // 서비스 관련 상태 확인
```

서비스 상태에 대한 주요값
<table>
<tr><td>loaded</td><td>프로세스에 로드되는 유닛의 환경 설정 파일을 나타낸다.</td></tr>
<tr><td>enabled</td><td>부팅 시에 활성화됨을 나타낸다.</td></tr>
<tr><td>disabled</td><td>부팅 시에 비활성화됨을 나타낸다.</td></tr>
<tr><td>active(running)</td><td>프로세스가 하나 이상의 프로세스에 의해 동작중임을 나타낸다.</td></tr>
<tr><td>active(exited)</td><td>일회성 프로세스를 성공적으로 실행한 경우에 나타난다.</td></tr>
<tr><td>active(waiting)</td><td>동작 중인 상태이나 특정 이벤트에 의해 대기 중인 상태를 나타낸다.</td></tr>
<tr><td>inactive(dead)</td><td>프로세스가 종료된 상태를 나타낸다.</td></tr>
<tr><td>static</td><td>활성화되는 다른 유닛에 의해 활성화가 가능한 상태를 나타낸다.</td></tr>
</table>
3. 타깃(target)
<br>부팅 레벨, 특정 동기화 지점과 같이 유닛을 그룹화할 때 사용

부팅과 관련된 주요 타깃
<table>
<tr><td>poweroff.target</td><td>시스템을 종료시키는 타깃</td></tr>
<tr><td>rescue.target</td><td>응급복구 모드(init 체제의 runlevel 1)로 전환하는 타킷</td></tr>
<tr><td>emergency.target</td><td>rescue.target과 유사하고 /(root)를 읽기 전용으로 마운트</td></tr>
<tr><td>multi-user.target</td><td>콘솔 모드(init 체제의 runlevel 3)로 전환하는 타깃. 텍스트 기반 로그인만 지원</td></tr>
<tr><td>graphical.target</td><td>X Window(init 체제의 runlevel 5) 모드로 전환하는 타깃. 텍스트 기반 로그인 이외의 X 기반의 로그인 지원</td></tr>
<tr><td>reboot.target</td><td>재부팅시키는 타깃</td></tr>
</table>

/lib/systemd/system에서 확인 가능<br>
![runlevel](https://github.com/dee021/TIL/blob/main/Linux/img/runlevel.png)

<B>관련 명령어</B>
1. systemctl
systemd 기반의 시스템 및 서비스 관리를 제어하는 명령
```
systemctl [option] 명령 [서비스명]
```
option
<table>
<tr><td>-l, --full</td><td>유닛 관련 정보를 출력할 때 긴 이름도 약어로 출력하지 않고 전체 출력한다.</td></tr>
<tr><td>-t, --type=</td><td>유닛의 유형을 지정. 주요 유형에는 service, target, socket 등이 있다.</td></tr>
<tr><td>-a, --all</td><td>유닛 정보를 출력할 때 모든 유닛을 지정한다.</td></tr>
<tr><td>--failed</td><td>실패한 유닛 정보만 출력한다.</td></tr>
</table>

명령
<table>
<tr><td colspan=2> 
  
- 런레벨 관련 주요 명령</td></tr>
<tr><td>get-default</td><td>현재 시스템에 설정된 런레벨 target 정보를 출력</td></tr>
<tr><td>set-default 타깃명</td><td>시스템의 런레벨을 지정한 target으로 변경</td></tr>
<tr><td>isolate 타깃명</td><td>지정한 타깃의 런레벨로 즉시 변경</td></tr>
<tr><td>rescue</td><td>응급 복구 모드로 즉시 전환</td></tr>
<tr><td>poweroff</td><td>시스템을 즉시 종료. 'halt'와 동일</td></tr>
<tr><td>reboot</td><td>시스템을 즉시 재부팅</td></tr>
<tr><td colspan=2>
  
- 상태 정보 관련 주요 명령</td></tr>
<tr><td>list-untis</td><td>유닛 관련 정보 출력. 옵션이 없을 시 기본 적용되며 긴 이름은 약어로 표기</td></tr>
<tr><td>list-unit-files</td><td>설치된 유닛 파이의 목록 및 상태 정보 출력</td></tr>
<tr><td>list-socket</td><td>소켓 관련 유닛 정보 출력</td></tr>
<tr><td>list-dependencies</td><td>명시된 유닛의 의존성 관련 있는 유닛 정보를 출력<br>명시된 유닛이 없으면 기본 타깃(defualt.target)의 의존성 정보를 출력</td></tr>
<tr><td colspan=2>
  
- 서비스 제어 관련 주요 명령</td></tr>
<tr><td>enable 서비스명</td><td>부팅 시 특정 서비스를 구동시킨다.</td></tr>
<tr><td>disable</td><td>부팅 시 특정 서비스를 구동시키지 않는다.</td></tr>
<tr><td>is-enabled</td><td>부팅 시 특정 서비스가 구동되는지 여부를 확인</td></tr>
<tr><td>start/stop 서비스명</td><td>특정 서비스를 즉시 시작/종료</td></tr>
<tr><td>restart 서비스명</td><td>특정 서비스를 재시작</td></tr>
<tr><td>reload 서비스명</td><td>특정 서비스의 환경 설정만 다시 읽어 들인다.</td></tr>
<tr><td>mask 서비스명</td><td>특정 서비스의 유닛 파일을 /dev/null로 링크시켜서 시작되는 것을 막을 때 사용</td></tr>
<tr><td>unmask 서비스명</td><td>mask 설정된 서비스를 해제</td></tr>
<tr><td>is-active 데몬명</td><td>특정 데몬이 활성화되어 있는 지 여부를 검사</td></tr>
<tr><td>status 데몬명</td><td>특정 데몬에 대한 상태 정보를 출력</td></tr>
<tr><td>kill 데몬명</td><td>특정 데몬의 프로세스를 종료</td></tr>
<tr><td>deamon-reload</td><td>systemd 매니저 관련 환경 설정을 다시 읽어들인다.</td></tr>
</table>

2. systemd-analyze
시스템 부팅과 관련된 성능을 분석해주는 명령
```
systemd-analyze [argument]
```
argument
<table>
<tr><td>time</td><td>부팅 시에 소요된 시간 정보를 출력<br>
크게 커널(kernel), 램 디스크(initrd), 서비스(userspace) 영역 3가지 정보로 출력</td></tr>
<tr><td>blame</td><td>서비스별로 부팅 시에 소요된 시간을 출력</td></tr>
<tr><td>critical-chain</td><td>각 유닛의 시간을 트리 형태로 출력<br>
@second : 유닛이 시작 또는 활성화되기까지 소요된 시간<br>
[+second] : 유닛을 시작하는데 소요되는 시간</td></tr>
<tr><td>plot</td><td>관련 정보를 SVG(Scalable Vector Graphic)이미지 파일로 생성</td></tr>
</table>

3. journalctl
systemd 관련 로그는 systemd-journald가 생성하고 관리한다.
- 로그인 관련 정보를 커널로부터 받고, 사용자 프로세스 관련 정보는 syslog로부터 받음
- 관련 정보는 메타 데이터로 /run/log/journal 디렉터리에 저장됨
- 재부팅 시 관련 정보는 사라짐
systemd-journald에 의해 생성된 관련 정보를 질의할 때 사용하는 명령
```
journalctl [option] [항목]
```
option
<table>
<tr><td>-l, --full</td><td>출력 가능한 모든 필드의 정보를 출력</td></tr>
<tr><td>-r, --reverse</td><td>역순(가장 최근 정보부터)으로 출력</td></tr>
<tr><td>-p, --priority=</td><td>syslog에 사용하는 로그 레벨을 지정하는 옵션</td></tr>
<tr><td>--since=</td><td>특정 날짜 이후의 정보만 출력<br>
2025-01-23 21:13:00 형식으로 지정</td></tr>
<tr><td>--until=</td><td>특정 날짜까지의 정보만 출력<br>
2025-01-23 21:13:00 형식으로 지정</td></tr>
</table>

예시
```
journalctl -p err
error 수준 이상의 로그 정보를 출력

journalctl /sbin/sshd
sshd 관련 로그 정보만 출력

journalctl /dev/sda
장치 파일인 /dev/sda의 로그 정보를 출력

```

#### 시스템 설정 주요 명령어
1. timedatectl
시스템의 날짜 및 시간을 확인하거나 설정하는 명령
```
timedatectl [command]
```
주요 command
<table>
<tr><td>status</td><td>시스템의 시간 및 RCT(Real Time Clock)의 시간 정보를 출력</td></tr>
<tr><td>set-time</td><td>시스템의 시간을 설정하는 명령<br>
"2025-01-23 21:05:13" 형식으로 지정</td></tr>
<tr><td>set-timezone</td><td>타임존을 설정하는 명령<br>
대한민국인 경우 'Asia/Seoul'로 지정</td></tr>
<tr><td>set-ntp 값</td><td>NTP 사용 여부를 지정하는 명령<br>
1로 설정하면 사용, 0이면 미사용</td></tr>
</table>

> NTP(Network Time Protocol) : 네트워크의 모든 디바이스에서 시간을 동기화하는데 사용되는 프로토콜

2. hostnamectl
시스템에 설정된 호스트명 정보를 출력하거나 설정하는 명령
```
hostnamectl [command] [설정값]
```
주요 command
<table>
<tr><td>status</td><td>시스템에 설정된 호스트명과 관련된 정보를 출력<br>
command 입력이 없을 경우 기본값</td></tr>
<tr><td>set-hostname</td><td>호스트명을 설정할 때 사용</td></tr>
</table>

## 셧다운(Shutdown)
셧다운 관련 명령<br>
1. shutdown<br>
시스템을 재시작하거나 전원을 종료하는 명령어
- root 권한자만 가능
- 해당 옵션에 따라 적당한 실행레벨을 인식하고 init 프로세스를 호출하여 요청
```
shutdown [option] 시간 [경고메시지]
```
주요 옵션
<table>
<tr><td>-r</td><td>시스템을 재부팅할 때 사용(=reboot)</td></tr>
<tr><td>-h</td><td>시스템을 종료할 때 사용(=halt)</td></tr>
<tr><td>-c</td><td>예약된 셧다운 명령을 취소</td></tr>
<tr><td>-k</td><td>실제로 셧다운 하지 않고 경고 메시지만을 접속한 사용자들에게 전송</td></tr>
</table>

예시
```
shutdown -r now
시스템을 즉시 재부팅한다

shutdown -h 10
시스템을 10분 뒤 종료한다

shutdown -h 23:59
시스템을 23시 59분에 종료한다
```

2. reboot<br>
시스템을 재부팅하는 명령
```
reboot [option]
```

주요 옵션
<table>
<tr><td>-w</td><td>시스템을 재부팅하지 않고 /var/log/wtmp에 셧다운한 기록만 저장한다</td></tr>
<tr><td>-f</td><td>init을 호출하지 않고 즉시 시스템을 재부팅한다</td></tr>
</table>

3. half<br>
시스템을 종료하는 명령
```
half [option]
```
<table>
<tr><td>-p</td><td>시스템을 종료하고 전원까지 끄는 경우 사용(--poweroff)</td></tr>
</table>

4. poweroff<br>
시스템 종료 및 전원을 끄는 명령<br>
5. init, telinit<br>
init 프로세스에 직접 요청하여 실행레벨을 변경하는 명령
- 빠르게 실행되지만, 실행중인 프로세스를 무조건적으로 종료
