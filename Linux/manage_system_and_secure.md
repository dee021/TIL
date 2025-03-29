# 시스템 분석
## 시스템 로그 분석 및 관리

- 로그(log) : 시스템에서 일어나는 모든 사건이나 이벤트 등을 기록한 것

### 로그 기록 관련 리눅스 패키지
1. syslog(리눅스 초기)
- syslogd 데몬이 /etc/syslog.conf 설정 파일을 기반으로 서비스별 로그 파일을 /var/log 디렉터리에 생성
2. rsyslog(최근 배포판)
- rocket-fast system for log processing
- rsyslogd 데몬이 /etc/rsyslog.conf 설정 파일을 기반으로 서비스별 로그 파일을 /var/log 디렉터리에 생성
- syslog의 성능을 대폭 강화
  - 멀티스레드(multi-thread) 지원
  - TCP 지원
  - SSL 및 TLS 지원
  - RELP(Reliable Evnet Logging Protocol) 지원
  - MySQL, PostgreSQL, Oracle 등 데이터베이스 지원
  - 보내는 목록 제한
  - 메시지 일부 필터링
  - 출력 포맷 제어


### rsyslog
- rsyslogd 데몬이 동작하면서 로그를 기록, /etc/rsyslog.conf 파일을 통해 관련 환경 설정 제어

1. 주요 파일
<table>
<thead><td>파일명</td><td>설명</td></thead>
<tr><td>/etc/rsyslog.conf</td><td>rsyslogd 데몬의 환경 설정 파일</td></tr>
<tr><td>/etc/sysconfig/rsyslog</td><td>rsyslogd 데몬이 실행과 관련된 옵션이 설정되는 파일</td></tr>
<tr><td>/usr/sbin/rsyslogd</td><td>실제 rsyslogd 데몬 실행 명령</td></tr>
<tr><td>/usr/lib/systemd/system/rsyslog.service</td><td>systemctl 명령에 의해 제어되는 유닛 파일</td></tr>
</table>

2. /etc/rsyslog.conf 파일

![rsyslog.conf](https://github.com/dee021/TIL/blob/linuxmaster/Linux/img/rsyslogConf.png)

<b>기본 구성 형식</b>

```
facility.prioiry            action
```

<b>facility</b>

- 일종의 서비스를 의미하며, 메시지를 발생시키는 프로그램의 유형
- 콤마(,)를 사용하여 facility 나열


<table>
<thead><td>facility</td><td>설명</td></thead>
<tr><td>cron</td><td>cron, at과 같은 스케줄링 프로그램이 발생한 메시지</td></tr>
<tr><td>auth, security</td><td>login과 같이 인증 프로그램 유형이 발생한 메시지</td></tr>
<tr><td>authpriv</td><td>ssh와 같이 인증이 플요한 프로그램 유형이 발생한 메시지로 사용자 추가 시에도 메시지 발생</td></tr>
<tr><td>daemon</td><td>telnet, ftp 등과 같이 여러 데몬이 발생한 메시지</td></tr>
<tr><td>kern</td><td>커널이 발생한 메시지</td></tr>
<tr><td>lpr</td><td>프린트 유형의 프로그램이 발생한 메시지</td></tr>
<tr><td>mail</td><td>mail 시스템이 발생한 메시지</td></tr>
<tr><td>mark</td><td>syslogd에 의해 만들어지는 날짜 유형</td></tr>
<tr><td>news</td><td>유즈넷 뉴스 프로그램이 발생한 메시지</td></tr>
<tr><td>syslog</td><td>syslog 프로그램 유형이 발생한 메시지</td></tr>
<tr><td>user</td><td>사용자 프로세스</td></tr>
<tr><td>uucp</td><td>UUCP(UNIX to UNIX Copy Protocol) 시스템이 발생한 메시지</td></tr>
<tr><td>local0~local7</td><td>여분으로 남겨둔 유형</td></tr>
<tr><td>*</td><td>모든 facility를 의미</td></tr>
</table>


<b>priority</b>

- 위험의 정도
- 설정한 수준을 포함하여 높으면 메시지를 보냄
- priority 앞에 '='을 사용하면 해당 수준의 위험도와 같은 경우에만 메시지를 기록
- priority 앞에 '!='을 사용하면 해당 수준만 제외
- 세미콜론(;)을 사용하여 facility.prioiry의 조합을 나열

<table>
<thead><td>priority</td><td>설명</td></thead>
<tr><td>none</td><td>지정한 facility를 제외<br>보통 다른 facility에 대한 설정을 하고 ; 뒤에 특정 facility를 제외할 때 사용</td></tr>
<tr><td>debug</td><td>프로그램을 디버깅할 때 발생하는 메시지</td></tr>
<tr><td>info</td><td>통계, 기본 정보 메시지</td></tr>
<tr><td>notice</td><td>특정한 주의가 필요하나 에러는 아닌 메시지</td></tr>
<tr><td>warning, warn</td><td>주의가 필요한 메시지</td></tr>
<tr><td>error, err</td><td>에러가 발생하는 경우의 메시지</td></tr>
<tr><td>crit</td><td>크게 급하지는 않지만 시스템에 문제가 생기는 단계의 메시지</td></tr>
<tr><td>alert</td><td>즉각적인 조정을 해야 하는 상황</td></tr>
<tr><td>emerg, panic</td><td>모든 사용자에게 전달해야 할 위험한 상황</td></tr>
</table>

<b>action</b>

- 메시지를 보낼 목적지나 행동들에 관한 설정
- 보통 파일명이나 아이디 등을 기재

<table>
<thead><td>action</td><td>설명</td></thead>
<tr><td>file</td><td>지정한 파일에 로그를 기록<br>/로 시작해서 절대 경로를 기입</td></tr>
<tr><td>@host</td><td>지정한 호스트로 메시지를 UDP 기반으로 전달

```
예  *.*         @192.168.12.22
```
</td></tr>
<tr><td>@@host</td><td>지정한 호스트로 메시지를 TCP 기반으로 전달

```
예  *.*         @@192.168.12.22
```
</td></tr>
<tr><td>user</td><td>지정한 사용자가 로그인한 경우 해당 사용자의 터미널로 전달

```
예 ":omurmsg:root,user1,user2"
```
</td></tr>
<tr><td>*</td><td>현재 로그인되어 있는 모든 사용자의 화면으로 전달

```
예 ":omurmsg:*"
```
</td></tr>
<tr><td>콘솔 또는 터미널</td><td>지정한 터미널로 메시지 전달

```
예 /dev/tty2
```
</td></tr>
</table>


사용 예
```
*.=crit;kern.none       /var/log/critical
# 모든 facility가 발생하는 메시지 중 crit 수준의 메시지만 /var/log/critical 파일에 기록
# 커널이 발생하는 메시지는 제외

mail.*;mail.!=info      /var/log/maillog
# mail 관련 모든 정보는 /var/log/maillog에 기록
# info 수준의 로그는 제외

uucp,news.crit          /var/log/news
# uucp 및 news에서 발생하는 crit 수준 이상의 메시지는 /var/log/news에 기록
```

### logrotate
- 로그 파일을 여러 개로 분할해주는 프로그램
- 로그 파일의 자동 로테이션 기능, 압축 기능, 제거 등 지원
- 각각의 로그 파일은 하루, 일주일, 한 달, 연 단위로 로테이션 할 수 있고, 파일의 크기를 지정하여 로테이션 할 수 있음
- 시스템과 관련된 기본적인 로그 설정은 /etc/logrotate.conf에서 제어
- 응용 프로그램은 /etc/logrotate.d 디렉터리 내에 위치하여 로그 파일을 관리
- 현재 리눅스에서는 /etc/cron.daily 디렉터리에 등록되어 cron에 의해 스케줄링 되어 실행되고 있음
- 각 로그 파일별로 로테이션된 날짜는 /var/lib/logrotate.status 파일에 기록됨

```
logrotate [option] config_file
```

주요 옵션
<table>
<tr><td>-f</td><td>강제로 환경 설정 파일을 읽어 들여 실행(--force)</td></tr>
</table>


#### /etc/logrotate.conf의 주요 설정

![logrotate.conf](https://github.com/dee021/TIL/blob/linuxmaster/Linux/img/logrotateConf.png)

<b>weekly</b>

로그 파일을 일주일마다 로테이트한다. 가장 맨 위에 등록되어 있는 경우 특별히 명시하지 않은 로그 파일들은 이 파일에 적용을 받는다.

기간과 관련된 옵션으로는 daily, weekly, monthly, yearly를 사용할 수 있다.

<b>rotate 4</b>

최대 4번까지 rotate를 하는 설정으로 기본 logfile1, logfile.1, logfile.2, logfile.3, logfile.4 형태로 생성된다.

<b>create</b>

로테이트를 한 후에 비어 있는 로그 파일을 생성하도록 설정하는 항목이다.

<b>dateext</b>

로테이션으로 생성되는 로그 파일에 해당 날짜를 덧붙여서 생성하는 항목이다.

예를 들면 maillog라면 maillog-20250223 형태로 생성된다.

<b>compress</b>

로테이트 한 후에 생성된 로그 파일에 대해 압축할 때 사용하는 항목이다.

<b>include /etc/logrotate.d</b>

/etc/logrotate.d 디렉터리 안에 설정된 파일에 대해서 로테이트를 적용하는 설정이다.

<b>nomissingok</b>

로그 파일이 존재하지 않는 경우에 에러 메시지를 출력한다. 기본값으로 설정되어 있다.

<b>missingok</b>

로그 파일이 존재하지 않는 경우에 에러 메시지를 출력하지 않고 다음 파일로 이동한다.

<b>/var/log/wtmp {<br>
&emsp;monthly<br>
&emsp;create 0644 root utmp<br>
&emsp;&emsp;minsize&emsp;1M<br>
&emsp;rotate&emsp;1<br>}</b>

로그 파일명을 명기한 후 { }로 묶어서 관련 내용을 설정하면 운영이 가능하다.

/var/log/wtmp는 한 달마다 로테이트하지만, 파일 크기가 1MB가 되면 그 이전이라도 로테이트를 실행한다.

파일 생성 시 허가권 값은 0644, 소유자는 root, 소유 그룹은 utmp로 설정한다.

로테이션으로 생성되는 백로그 파일은 1개만 생성한다.

<br><br>
설정 예
```
/var/log/httpd/access.log {
    rotate 5
    mail fsdf@gmail.com
    size 100k
    missingok
    dateext
    postrotate
        /usr/bin/killall -HUP httpd
    endscript
}
```
/var/log/httpd/access.log 파일에 대한 로테이션 설정을 파일의 크기가 100k 일 때 설정한 내용이다.

size 옵션은 기간을 지정하는 daily, weekly, monthly, yearly와 함께 사용할 수 없다.

mail 항목은 로그 파일이 없을 때 지정한 주소로 메일을 보내도록 설정할 수 있다.

postrotate ~ endscript는 로그 파일이 로테이트된 후에 실행될 명령어를 작성할 때 사용한다.

## 로그 관련 파일

1. /var/log/messages
- 시스템에서 발생하는 표준 메시지가 기록되는 파일
- 대부분의 로그가 해당 파일에 쌓이고 root만 읽을 수 있도록 설정되어 있음
- 콜론(:)을 구분자로 날짜 및 시간, 메시지가 발생한 호스트명, 메시지를 발생한 내부 시스템이나 응용 프로그램의 이름, 발생된 메시지 순으로 기록됨


2. /var/log/secure
- 인증을 기반으로 접속과 관련된 로그가 기록되는 파일
- 보통 login(telnet 및 ssh), tcp_wrappers, xinted 관련 로그가 쌓임

3. /var/log/dmesg
- 시스템이 부팅할 때 출력되었던 로그가 기록되는 파일
- 보통 커널 부트 메시지 로그라고 함

4. /var/log/maillog
- sendmail, dovecot 등과 같이 메일 관련 작업이 기록되는 로그 파일

5. /var/log/xferlog
- FTP 접속과 관련된 작업이 기록되는 파일
- 로그 포맷은 총 14개의 항목으로 구성되어 있음

로그 포맷
<table>
<thead><td>항목</td><td>설명</td></thead>
<tr><td>current-time</td><td>접속한 현재 시간. "DDD MMM dd hh:mm:ss YYYY"형태로 출력

예 Tue Jul 14 15:46:58 2025
</td></tr>
<tr><td>transfer-time</td><td>전송된 총 시간. 초 단위로 표시된다.</td></tr>
<tr><td>remote-host</td><td>원격 호스트의 IP 주소</td></tr>
<tr><td>file-size</td><td>전송된 크기. 단위 : byte</td></tr>
<tr><td>filename</td><td>전송된 파일 이름(절대 경로)</td></tr>
<tr><td>transfer-type</td><td>전송 형태<br>a : ascii 전송, b : binary 전송</td></tr>
<tr><td>special-action-flag</td><td>특정한 action이 발생하는 것을 나타냄

- C : 파일이 압축되어진 경우
- U : 파일이 압축되어 있지 않은 경우
- T : 파일이 tar로 묶여진 경우
- _ : 어떠한 action이 발생하지 않은 경우
</td></tr>
<tr><td>direction</td><td>전송된 지시를 나타내는 것<br>o : (outgoing) 다운로드된 경우, i : (incoming) 업로드된 경우</td></tr>
<tr><td>access-mode</td><td>사용자가 어떤 형태로 login 했는지 나타냄<br>a : (anonymous) 익명 사용자, g : (guest) 패스워드를 소유한 손님 user, r : (real) 인증한 로컬 사용자</td></tr>
<tr><td>username</td><td>로컬 사용자의 ID</td></tr>
<tr><td>service-name</td><td>발생되어지는 service의 이름<br>보통 ftp라고 기록됨</td></tr>
<tr><td>authentication-method</td><td>인증에 사용되는 방법<br> 0 : none. 기본 인증, 1 : RFC931 Authentication</td></tr>
<tr><td>authentication-user-id</td><td>인증 방법에 의해 되돌려지는 사용자 계정<br>authenticatio-method가 0이면 보통 *로 표시됨</td></tr>
<tr><td>completion-status</td><td>전송 상태<br>c : (complete) 완전한 전송, i : (incomplete) 불완전한 전송</td></tr>
</table>

6. /var/log/cron
- cron 관련 정보가 기록되는 파일

7. /var/log/boot.log
- 부팅 시 발생되는 메시지가 기록되는 파일
- 보통 부팅 시 동작하는 데몬 관련 정보가 기록됨

8. /var/log/lastlog
- telnet이나 ssh을 이용해서 접속한 각 사용자의 마지막 정보가 기록되는 파일
- 바이너리 파일로 `lastlog` 명령으로 확인

9. /var/log/wtmp
- 콘솔, telnet, ftp 등을 이용하여 접속한 사용자 기록, 시스템을 재부팅한 기록 등의 로그가 쌓이는 파일
- 바이너리 파일로 `last` 명령으로 확인

10. /var/log/btmp
- wtmp와 반대되는 로그로 접속이 실패한 경우를 기록
- 바이너리 파일로 되어 있으며 `lastb` 명령으로 확인

### 로그 관련 명령어
#### last
- /var/log/wtmp 파일의 내용을 출력하는 명령
- 재부팅한 정보는 시스템 계정인 reboot를 지정
- 인자값으로 정수값을 주면 로컬의 특정 터미널로 로그인한 정보 확인 가능

```
last [option] [사용자명] [ttyn]
```

주요 옵션
<table>
<tr><td>-f 파일명</td><td>로그 로테이션이 설정된 경우, 기본 로그 파일 이외의 다른 로그 파일의 기록을 볼 경우 사용</td></tr>
<tr><td>-n 숫자</td><td>가장 최근부터 해당 숫자값 만큼만 출력(-'숫자)</td></tr>
<tr><td>-t YYYYMMDDHHMMSS</td><td>지정한 시간 이전에 로그인한 기록 출력</td></tr>
<tr><td>-R</td><td>IP 주소나 호스트명을 출력하지 않음</td></tr>
<tr><td>-a</td><td>호스트명이나 IP 주소 필드를 맨 마지막에 출력<br>일반적으로 -d 옵션과 함께 사용</td></tr>
<tr><td>-d</td><td>호스트 이름이 존재하는 경우, IP 주소를 호스트 이름으로 변환하여 출력</td></tr>
<tr><td>-F</td><td>로그인 및 로그아웃 시간 출력</td></tr>
<tr><td>-i</td><td>접속한 호스트의 IP 주소로만 출력</td></tr>
<tr><td>-w</td><td>사용자의 전체 이름이나 전체 도메인 이름을 전부 출력</td></tr>
</table>

사용 예
```
$ last -2
가장 최근 정보 2개 출력

$ last 2
/dev/tty2로 로그인한 정보 출력
```

#### lastlog
- 각각의 사용자가 마지막으로 로그인한 정보를 출력
- /var/log/lsatlog 내용을 출력

```
lastlog [option]
```

주요 옵션
<table>
<tr><td>-u 사용자명</td><td>특정 사용자에 대한 정보만 출력(--user)</td></tr>
<tr><td>-t 날짜수</td><td>오늘부터 지정한 날짜수 만큼 거슬러 올라가 그 이후에 로그인한 사용자의 정보 출력(--time)</td></tr>
<tr><td>-b 날짜수</td><td>오늘을 기준으로 지정한 날짜수 이전에 최종적으로 로그인한 사용자 정보 출력(--before)</td></tr>
</table>

#### lastb
- 로그인 실패 정보를 기록하는 /var/log/btmp 파일의 내용 출력
- last 명령과 동일한 사용법
- root만 사용 가능

```
lastb [option] [사용자명]
```

주요 옵션
<table>
<tr><td>-f 파일명</td><td>로그 로테이션 설정이 되어 있는 경우, 기본 로그 파일 이외의 다른 로그 파일의 기록을 볼 경우 사용</td></tr>
<tr><td>-n 숫자</td><td>가장 최근부터 해당 숫자값 만큼만 출력(-숫자)</td></tr>
<tr><td>-t YYYYMMDDHHMMSS</td><td>지정한 시간 이전에 로그인한 기록 출력</td></tr>
<tr><td>-R</td><td>IP 주소나 호스트명을 출력하지 않음</td></tr>
<tr><td>-a</td><td>호스트명이나 IP 주소 필드를 맨 마지막에 출력</td></tr>
<tr><td>-d</td><td>호스트 이름이 존재하는 경우 IP 주소를 호스트 이름으로 변환하여 출력</td></tr>
<tr><td>-F</td><td>로그인 및 로그아웃 시간 출력</td></tr>
<tr><td>-i</td><td>접속한 호스트의 IP 주소로만 출력</td></tr>
<tr><td>-w</td><td>사용자의 전체 이름이나 전체 도메인 이름을 전부 출력</td></tr>
</table>

#### dmesg
- 커널 링 버퍼(kernel ring buffer)의 내용을 출력하고 제어하는 명령

> <b>커널 링 버퍼</b><br>
> 커널의 동작과 관련된 메시지를 기록해주는 영역

```
dmesg [option]
```

주요 옵션
<table>
<tr><td>-c</td><td>커널 링 버퍼에 저장된 메시지를 출력한 후 비움</td></tr>
</table>

#### logger
- 명령행에서 로그 시스템에 메시지를 전송할 때 사용하는 명령
- 기본적으로는 /var/log/message 파일에 기록됨

```
logger [option] [message]
```

주요 옵션
<table>
<tr><td>-i</td><td>PID를 기록할 때 사용</td></tr>
<tr><td>-f</td><td>저장되는 파일명 지정</td></tr>
<tr><td>-t</td><td>지정한 태그(tag)를 함께 기록할 때 사용</td></tr>
<tr><td>-s</td><td>표준 출력(모니터)에도 메시지를 출력</td></tr>
</table>

# 시스템 보안 관리
## 시스템 보안 관리
### 리눅스 보안

1. 물리적 보안 설정
- 개별 시스템에 잠금장치를 하거나 잠금장치가 있는 랙(Rack)을 이용하는 등 불필요한 물리적 접근을 최대한 막아야 함
- BOIS(Basic Input Output System) 보안을 위한 암호 설정

2. 불필요한 서비스 제거
- 불필요한 응용 프로그램의 버그를 통한 해킹 등 위험요소 제거

3. 시스템 정보 감추기
- 로컬이나 외부에서 접속할 때 제공되는 /etc/issue, /etc/issue.net 파일에 설정된 내용을 변경하여 시스템 정보 노출 최소화

4. root 패스워드 변경 제한
- grub에 패스워드를 설정하여 root 패스워드를 임의로 변경하는 것을 제한

5. 사용자 관리
- UID가 0인 일반 계정 사용자
- 손쉬운 암호를 설정한 사용자
- 패스워드 없이 로그인이 가능한 사용자
- su와 같은 명령 제한을 위한 PAM(Pluggable Authentication Modules) 이용
- sudo(superuser do) 등 보안 프로그램 이용 

6. 보안이 강화된 서비스로 대체 이용
- 외부에서 서버로 원격 접속을 허용하는 경우 보안이 강화된 서비스를 이용
- telnet보다 ssh(secure shell) 이용

7. 파일 시스템 관리
- 불필요한 Set-UID, Set-GID, Sticky-Bit과 같은 특수 권한 사용 파일 및 디렉터리 제거
- 분할된 파티션인 경우, /etc/fstab 파일에 nosuid 옵션 사용

### 커널 매개 변수와 sysctl
#### 커널 매개 변수(kernel parameter)
리눅스는 커널 제어를 위한 매개변수들을 /proc/sys 디렉터리에 보관하여 관리한다.

- `cat` 명령을 통해 커널 매개 변수의 값을 확인할 수 있음
- 커널 매개 변수 값 변경은 `sysctl` 명령을 이용하거나 `echo` 명령과 > 기호를 이용하여 변경할 수 있음

![/proc/sys](https://github.com/dee021/TIL/blob/linuxmaster/Linux/img/procSys.png)

- fs : 커널에서 최대로 사용 가능한 파일의 수 지정과 같은 파일 시스템 관련 매개 변수가 들어 있는 디렉터리
- kernel : IPC(inter-process communication)와 관련된 Internal Message Queue, Semaphore, Shared Memory 등을 설정하는 매개 변수가 들어 있는 디렉터리
- net : TCP, ICMP 등 네트워크 관련 매개 변수가 들어 있는 디렉터리
- vm : swap, page 등 메모리 관련 변수가 들어 있는 디렉터리

#### 네트워크 주요 커널 매개 변수

네트워크 관련 커널 매개 변수는 /proc/sys/net 디렉터리에 위치한다.

![netKernelParam](https://github.com/dee021/TIL/blob/linuxmaster/Linux/img/kerParamNet.png)

주요 하위 디렉터리
<table>
<tr><td>core</td><td>네트워크의 기본 설정과 관련된 커널 매개 변수가 들어 있다.</td></tr>
<tr><td>ipv4</td><td>ipv4 관련 커널 매개 변수가 들어 있다.<br>하위 디렉터리인 conf에는 all, default, enp0s3 등과 같은 디렉터리가 존재하는데 모든 장치에 적용되게 하려면 all 디렉터리 내에 있는 매개 변수에 설정한다.</td></tr>
<tr><td>ipv6</td><td>ipv6</td>ipv6 관련 커널 매개 변수가 들어 있다. 기본적인 구조는 ipv4와 유사</tr>
<tr><td>netfilter</td><td>iptables 등과 같이 네트워크 필터링 관련 매개 변수가 들어 있다.</td></tr>
</table>

<b>ipv4 관련 주요 매개 변수</b>

수치형 매개 변수
<table>
<thead><td>커널 매개 변수</td><td>기본값</td><td>설명</td></thead>
<tr><td>ipv4/tcp_keepalive_time</td><td>7200</td><td>TCP 연결 상태를 계속 연결시키기 위한 시간 설정으로 초 단위이다. Traffic이 없는 상태에서의 Timeout 값이다.</td></tr>
<tr><td>ipv4/tcp_max_syn_backlog</td><td>128</td>
<td>TCP 프로토콜에서 처리되지 않은 대기 상태에 있는 큐의 길이 설정이다. 백로그 큐가 꽉 차면 이후 들어오는 SYN 요청은 무시된다.</td></tr>
<tr><td>ipv4/tcp_fin_timeout</td><td>60</td>
<td>TCP 세션 종료 후에 얼마나 세션 연결을 유지하고 있을지 초 단위로 설정한다.</td></tr>
<tr><td>ipv4/tcp_synact/retries</td><td>5</td>
<td>일정 시간 내에 IP 주소별로 보내고 받는 SYN 패킷의 재시도 횟수를 제한하는 값이다.</td></tr>
<tr><td>ipv4/tcp_max_tw_buckets</td><td>8192</td>
<td>TIME_WAIT을 담을 수 있는 소켓 수이다.</td></tr>
<tr><td>ipv4/tcp_syn_retries</td><td>6</td>
<td>활성화된 TCP 접속이 재전송을 시도하기 위한 최초 SYN 시간의 값이다.</td></tr>
<tr><td>ipv4/tcp_retries2</td><td>15</td><td>살아있는 TCP 연결을 끊기 전 확인하는 횟수이다.</td>
</table>

활성/비활성 매개 변수
<table>
<thead><td>커널 매개 변수</td><td>기본값</td><td>설명</td></thead>
<tr><td>ipv4/icmp_echo_ingore_all</td><td>0</td>
<td>ping과 같은 ICMP 패킷에 대한 응답 여부를 결정하는 매개 변수<br>0 : 응답, 1 : 응답하지 않음</td></tr>
<tr><td>ipv4/tcp_syncookies</td><td>1</td>
<td>SYN 패킷의 도착빈도가 일정한 횟수보다 많을 때 해당 요청을 허용하지 않을지 설정한다.<br>0 : 요청 허용, 1 : 허용하지 않음<br>SYN Flooding 공격을 막을 때 유용한 항목</td></tr>
<tr><td>ipv4/ip_forward</td><td>1</td>
<td>인터넷 공유나 IP 매스커레이드 등 하나의 서버에서 IP를 공유하여 포워딩을 가능하게 할 것인지 여부를 지정하는 파일<br>0 : 불가능, 1 : 가능</td></tr>
<tr><td>ipv4/icmp_echo_ignore_broadcasts</td><td>1</td>
<td>브로드캐스트 주소에 ping을 전달하여 공격하는 형태인 smurf 공격을 막을 때 사용하는 매개 변수로 값을 1로 설정한다.</td></tr>
<tr><td>ipv4/icmp_echo_ignore_bogus_error_reponses</td><td>1</td>
<td>Bad ICMP 패킷을 무시할 때 사용하는 매개 변수로 1로 설정한다.</td></tr>
<tr><td>ipv4/tcp_timestamps</td><td>1</td>
<td>외부에서 서버의 날짜 정보를 확인 가능하게 할 것인지를 지정하는 변수<br>0 : 날짜 정보 확인 불가능, 1 : 날짜 정보 확인 가능</td></tr>
<tr><td>ipv4/conf/*/accept_redirects</td><td>0</td>
<td>ICMP redirect 관련 패킷을 허가할 것인지를 결정하는 변수<br>0 : 불허, 1 : 허가<br>ICMP redirect 패킷은 서버의 routing 경로를 확인하는 역할을 하는데, 이러한 패킷을 차단할 수 있다.</td></tr>
<tr><td>ipv4/conf/*/send_redirects</td><td>1</td>
<td>서버에서 나가는 ICMP redirect 패킷을 허가할 것인지 결하는 파일<br>0 : 불허, 1 : 허가</td></tr>
<tr><td>ipv4/conf/*/secre_redirects</td><td>1</td>
<td>게이트웨이로 나열된 시스템에서의 경로 재지정을 무시하도록 구성<br>리다이렉션은 공격을 수행하는데, 사용될 수 있으므로 값을 1로 설정한다.</td></tr>
<tr><td>ipv4/conf/*/accept_source_route</td><td>0</td>
<td>라우팅과 라우팅 프로토콜은 몇 가지 문제점을 생성해 내는데 목적지의 경로에 대한 세부적인 내용을 담고 있는 IP 소스 라우팅은 목적지 호스트에서도 같은 경로를 따라 반응을 해야 한다는 문제점이 있다. 해커가 특정 네트워크에 소스 라우팅 패킷을 보낼 수 있다면 돌아오는 반응을 가로채서 상대방의 호스트와 신뢰받은 호스트처럼 속일 수 있게 된다. 이 기능을 사용하지 못 하게 하기 위해 값을 0으로 한다.</td></tr>
<tr><td>ipv4/conf/all/rp_filter</td><td>1</td>
<td>IPv4는 인증 매커니즘을 가지고 있지 않기 때문에 Source IP Address를 조작할 수 있다. rp_filter는 패킷이 들어오는 인터페이스와 나가는 인터페이스가 같은지 검사하려면 값을 1로 설정한다.</td></tr>
<tr><td>ipv4/conf/all/log_martians</td><td>0</td>
<td>스푸핑 패킷과 redirect된 패킷의 기록을 남길 지 여부를 지정하는 변수이다<br>0 : 기록을 남기지 않음, 1 : 패킷을 기록함</td></tr>
</table>

#### sysctl
- 커널 매개 변수의 값을 제어하여 시스템을 최적화할 수 있는 명령
- /proc/sys 디렉터리에 존재하는 매개 변수를 제어하는 명령
- 매개 변수 호출은 /proc/sys 디렉터리의 하위부터 .을 구분자로 기재
- 변경된 값은 재부팅 시 초기화됨
  - /etc/sysctl.conf 파일에 등록하면 영구 반영됨
    - 등록 후 즉시 반영은 `sysctl -p` 실행

```
sysctl [option] 변수
sysctl [option] 변수=값
```

주요 옵션
<table>
<tr><td>-a, -A</td><td>커널 매개 변수와 값을 모두 출력(--all)</td></tr>
<tr><td>-p [파일명]</td><td>환경 변수 파일에 설정된 값을 읽어 들여서 반영하고 출력(--load=파일명)<br>파일명을 지정하지 않으면 /etc/sysctl.conf 파일이 적용됨</td></tr>
<tr><td>-n</td><td>특정 매개 변수에 대한 값을 출력할 때 사용(--values)</td></tr>
<tr><td>-w 변수=값</td><td>매개 변수에 값 설정(--write)</td></tr>
</table>

## 시스템 보안 관련 명령어
### ssh(Secure Shell)
- 원격 시스템에 로그인하여 명령을 실행하는 프로그램
- telnet은 패킷 전송 시 일반 텍스트 형태로 패킷을 전달하지만, ssh는 암호화시키므로 안전하게 전송할 수 있음
- ssh2는 ssh1과 호환되지만 기본적으로 호환성을 포함하고 있는 것이 아니므로 ssh1을 지원하려면 ssh1 서버를 같이 운영해야 함
- 기본 설정 포트는 22번


<b>ssh의 특징</b>

- 패킷을 암호화하여 다른 원격 로그인 프로그램인 telnet이나 rlogin에 비해 안전하다.
- rlogin처럼 패스워드 입력 없이 로그인이 가능하다.
- rsh처럼 원격 셸을 지원한다.
- 원격 복사(scp)를 지원한다.
- 안전한 파일 전송(sftp)을 지원한다.

#### ssh 설치
- 리눅스에서는 ssh의 공개 버전인 openssh를 사용하며, SSH 서버와 SSH 클라이언트 프로그램으로 나뉘어져 있음
- 레드햇 계열 리눅스에서는 3개의 패키지로 구분하여 배포
<table>
<thead><td>패키지명</td><td>설명</td></thead>
<tr><td>openssh</td><td>openssh 서버와 openssh 클라이언트에 모두 필요한 핵심적인 파일들이 들어 있는 패키지<br>ssh-keygen 명령이 들어 있음</td></tr>
<tr><td>openssh-server</td><td>openssh 서버 관련 패키지<br>데몬인 sshd, sftp 서버 등이 들어 있음</td></tr>
<tr><td>openssh-clients</td><td>openssh 클라이언트 관려 패키지<br>ssh, scp, sftp 등의 명령어가 들어 있음</td></tr>
</table>

#### ssh 서버 설정

- /etc/ssh/sshd_config : ssh 서버의 환경 설정 파일
- systemctl start sshd.service : ssh 서버 실행 명령
- /usr/sbin/sshd : ssh 서버 실행 파일
- /etc/rc.d/init.d/sshd : sshd 실행을 위한 스크립트 파일


<b>/etc/ssh/sshd_config의 주요 항목</b>

```
Port 22
# ssh 서버가 사용하는 포트를 지정

#AddressFamily any
# 접속하는 IP 주소 버전을 지정하는 옵션
# any : IPv4와 IPv6 주소 체계 모두 가능
# inet : IPv4 주소만 접속 가능
# inet6 : IPv6 주소만 접속 가능

#ListenAddress 0.0.0.0
#ListenAddress ::
# sshd 데몬이 허가하는 주소를 설정하는 영역
# 여러 개의 네트워크 인터페이스가 있을 경우 IP대역과 포트를 지정할 수 있음
# 콜론(:)은 IPv6 주소체계에서 사용

Protocol 2
# ssh 프로토콜의 버전을 지정

HostKey /etc/ssh/ssh_host_key
# SSH1의 호스트키 위치를 지정

#HostKey /etc/ssh/ssh_host_rsa_key
#HostKey /etc/ssh/ssh_host_ecdsa_key
#HostKey /etc/ssh/ssh_host_ed25519_key
# SSH2의 암호화 방식인 RSA, DSA 등의 호스트키 위치를 지정

#KeyRegenerationInteval 1h
# 호스트의 세션에 있는 키를 캡처해서 암호를 해독하거나 훔친 키를 사용하지 못하도록 접속이 이루어진 뒤 서버의 키를 재생성
# 0이면 키를 재생성하지 않고, 숫자만 입력하면 초단위. m, h 단위 사용 가능

#ServerKeyBits 1024
# 서버 키의 비트수를 정의

# Ciphers and keying
#RekeyLimit default none

# Logging
#SyslogFacility AUTH
# syslog 관련하여 facility를 지정하는 항목
# 기본값은 AUTH, 그 외 DAEMON, USER, LOCAL0~7 사용 가능
# ssh를 통한 접속은 AUTHPRIV로 처리하여 기록

#LogLevel INFO
# 로그레벨을 지정하는 항목
# 기본값은 INFO, 그 외 QUIET(기록하지 않음), FATAL, ERROR, VERBOSE, DEBUGS 등

#LoginGraceTime 2m
# 유저가 로그인에 실패했을 경우 서버가 연결을 끊는 시간
# 0이면 제한 없음

#PermitRootLogin prohibit-password
# root 로그인 허용 여부를 결정하는 것
# yes/no : root 외부 접속 허용/비허용
# prohibit-password : Root 비밀번호 로그인은 막고 key파일 로그인만 허용

#StrictModes yes
# 로그인을 허용하기 전에 파일 모드, 사용자 홈 디렉터리 소유권, 원격 호스트의 파일들을 SSH 데몬이 체크할 수 있도록 하는 설정

#MaxAuthTries 6
# 접속할 때 재시도할 수 있는 최대 횟수를 지정하는 설정

#MaxSessions 10
# 네트워크 주소 당 최대 연결할 수 있는 세션 수를 지정

RSAAuthentication yes
# RSA 인증을 사용할 것인지 설정

#PubkeyAuthentication yes
# 공개키를 통한 인증을 허가할 것인지 설정

#AuthorizedKeysFile     .ssh/authorized_keys .ssh/authorized_keys2
# 클라이언트에서 생성한 공개키를 저장할 파일명을 설정

RhostsRSAAuthentication no
# 프로토콜 1 기반 RSA를 이용할 때 rhost나 /etc/hosts.equiv 파일이 있으면 이것을 사용해 인증을 할 수 있도록 하는 항목
# 기본값은 no

#HostbasedAuthentication no
# rhost나 /etc/hosts.equiv를 이용하여 접속할 때 공개키를 이용하여 접속을 허가할 것인지를 설정할 때 사용
# 기본값은 no

#IgnoreUserKnownHosts no
# RhostsRSAAuthentication나 HostbasedAuthentication를 사용한 인증 시 각 사용자의 홈 디렉터리에 있는 .ssh/known_hosts를 무시할 것인지를 설정
# 기본값은 no

#IgnoreRhosts yes
# RhostsRSAAuthentication나 HostbasedAuthentication를 사용한 인증 시 .rhosts 파일을 무시할 것인지 설정
# 기본값은 yes

# To disable tunneled clear text passwords, change to no here!
PasswordAuthentication yes
# 패스워드를 이용한 인증을 허용할 것인지 설정
# 기본값은 yes

#PermitEmptyPasswords no
# 패스워드 인즐 시 서버가 비어있는 패스워드를 인정할 지 설정
# 기본값은 no

ChallengeResponseAuthentication no
# Challenge-Response 관련 인증 설정

UsePAM yes
# PAM을 통해 제어할 것인지 설정

#AllowTcpForwarding yes
# TCP를 통한 포워딩을 허가할 것인지 설정

#GatewayPorts no
# 포워딩된 포트에 대하여 클라이언트의 접소을 허가할 것인지 설정

X11Forwarding yes
# 원격에서 X11 포워딩을 허용할 것인지 설정
# xhost보다 안전한 방법으로 원격에 있는 X 프로그램을 사용할 수 있음

#X11DisplayOffset 10
# X11 포워딩될 때 디스플레이 번호를 지정

#X11UseLocalhost yes
# X11 포워딩 서버에 접속할 때 localhost 관련 설정

#PermitTTY yes
PrintMotd yes
# SSH 로그인 시 /etc/motd 파일의 내용을 프린트되도록 설정

#PrintLastLog yes
# 로그인시 지난번 로그인 기록을 보여줄 것인지 설정

#TCPKeepAlive yes
# 클라이언트의 접속이 끊어졌는지를 체크하기 위해 일정 시간 메시지를 전달하도록 하는 설정

#UseLogin no
# 로그인 세션을 사용할 것인지 설정

#UsePrivilegeSeparation yes
# 네트워크 트래픽 발생 시 권한이 없는 자식 프로세스에 의해 생성된 권한을 분리할 것인지 설정

#PermitUserEnvironment no
# 사용자 홈 디렉터리의 .ssh/environment나 .ssh/authorized_keys안의 environment= 옵션을 사용하게 할 것인지 설정

#Compression delayed
# 압축을 허가할 것인지 설정

#ClientAliveInterval 0
# 클라이언트로부터 전송되는 데이터가 없을 경우 서버는 메시지를 보내는 시간 지정
# 0이면 메시지를 보내지 않음

#ClientAliveCountMax 3
# 클라이언트에게 전달하는 메시지 횟수 지정

#UseDNS yes
# 클라이언트의 호스트 주소를 IP 주소로 해석할 것인지 설정

#PidFile /var/run/sshd.pid
# ssh 데몬의 PID를 기록하는 파일

#Banner none
# 로그인 전에 보여주는 메시지 파일을 지정하는 부분

Subsystem       sftp    /usr/lib/openssh/sftp-server
# sftp 서버를 사용하기 위한 항목
# sftp는 프로토콜 버전2에서 사용되는 것으로 ssh와 같이 ftp의 보안을 강화하기 위해 사용되는 ftp 프로그램
```


#### ssh 클라이언트
리눅스에서는 openssh-clients 패키지를 설치하면 `ssh` 명령을 이용해서 접속 할 수 있다.

```
ssh [option] 호스트명_or_IP주소
ssh 계정이름@호스트네임
ssh 호스트네임 명령
```

주요 옵션
<table>
<tr><td>-l</td><td>다른 계정으로 저속할 때 사용<br>이 옵션 대신 서버 주소 앞에 @을 붙여 사용할 수 있음</td></tr>
<tr><td>-p</td><td>ssh 서버의 포트 번호가 22번이 아닌 경우, 바뀐 포트를 지정할 때 사용</td></tr>
</table>

사용 예
```
$ ssh 203.247.40.248
# 203.247.40.248 서버로 클라이언트의 계정과 같은 계정으로 접속 시도
```

`ssh-keygen` 명령을 이용하여 인증키로 접속 할 수 있음

  1. 명령으로 비밀키와 공개키 생성
  2. SSH 서버에 공개키 복사

```
ssh-keygen [option]
```

주요 옵션
<table>
<tr><td>-t</td><td>사용할 암호화 알고리즘을 지정하는 옵션<br>rsa, dsa 등 지정 가능하며, ssh2 버전에서는 rsa를 기본으로 사용</td></tr>
</table>

### PAM(Pluggable Authentication Module)
- 사용자를 인증하고 그 사용자의 서비스에 대한 접근을 제어하는 모듈화된 방법
- 응용 프로그램들에게 사용자 인증 방법을 선택할 수 있는 공유 라이브러리의 묶음을 제공

#### PAM 관련 디렉터리 및 파일

PAM 모듈 디렉터리

- 제공 라이브러리는 동적으로 로드 가능한 오브젝트 파일(.so) 형태
- /lib/security 또는 /lib64/security에 위치
- CentOS 7 : /usr/lib64/security(실제 디렉터리), /lib64/security(심볼링 링크)

PAM을 이용하는 서비스 설정 파일

- PAM을 이용하는 서비스들은 /etc/pam.d 디렉터리 안에 설정
- 특별히 지정되지 않은 서비스에 대한 인증은 /etc/pam.d/other에서 관리


![pamConfigFile](https://github.com/dee021/TIL/blob/linuxmaster/Linux/img/pamConf.png)

/etc/pam.d 안에 있는 서비스별 설정 파일은 크게 type, control, module-path, module-arguments 4가지 영역으로 구성됨

<b>type</b>

- PAM에 어떤 타입의 인증이 사용될 것인지 알려주는 항목
- 다중 요구사항을 만족하도록 요청할 수 있음

<table>
<thead><td>type</td><td>설명</td></thead>
<tr><td>account</td><td>사용자 계정을 확인하는 절차를 제공<br>사용자가 해당 서비스에 접근이 허용되는지 여부, 패스워드 기간 만료 여부를 검사</td></tr>
<tr><td>auth</td><td>사용자를 인증하고 자격 증명 절차를 제공<br>기본적으로 패스워드를 통해 인증하지만 스마트카드나 생체 인식 장치를 이용한 인증을 통해 사용자를 검사</td></tr>
<tr><td>password</td><td>인증 관련 작업의 업데이트 절차를 제공<br>사용자가 인증 방법을 변경할 때 관련 방법을 제공하는데, 기본적으로 패스워드를 사용</td></tr>
<tr><td>session</td><td>사용자가 서비스를 제공받기 전과 서비스를 제공 받은 후에 수행해야 할 작업을 지정할 때 사용<br>사용자의 홈 디렉터리 마운트 및 언마운트, 로그인/로그아웃 서비스 제한 등 포함</td></tr>
</table>

<b>control</b>

- 통제를 담당하는 부분으로 PAM에 무엇을 해야 할지 알려줌

<table>
<thead><td>control</td><td>설명</td></thead>
<tr><td>required</td><td>지정한 모듈을 통한 인증이 실패하면 인증을 거부<br>인증이 거부되기 전에 해당 서비스에 등록된 다른 모듈들을 호출한 후 거부</td></tr>
<tr><td>requisite</td><td>required와 유사하나 지정한 모듈이 오류를 반환할 경우 즉시 거부</td></tr>
<tr><td>sufficient</td><td>이전에 지정한 required 모듈이 실해파지 않고, sufficient로 지정한 모듈이 성공한 경우 즉시 인증을 승인</td></tr>
<tr><td>optional</td><td>서비스에 대한 응용 프로그램의 성공/실패가 중요하지 않다는 것을 의미<br>지정한 다른 모듈이 있는 경우 성공/실패 판단 시에는 무시됨<br>유일한 설정 모듈인 경우 이 모듈이 응용 프로그램에게 주는 결과로 결정</td></tr>
<tr><td>include</td><td>모듈명 대신 다른 PAM 관련 서비스를 지정하며, 해당 서비스 인증을 통과해야 가능하도록 설정할 때 사용<br>지정 모듈에서 실패하면 인증 거부</td></tr>
<tr><td>substack</td><td>모듈명 대신 다른 PAM 관련 서비스를 지정하며, 완료(done) 및 실패(die) 평가에서 전체 모듈 스택의 나머지 부분을 건너뛰지 않고 하위 부분만 건너뜀</td></tr>
</table>

<b>module-path</b>

- 사용하는 모듈명을 명기하는 부분
- 절대 경로로 입력하거나 /lib64/security에 있는 모듈명을 기입

<b>module-arguments</b>

- 지정한 모듈이 사용하는 인수를 기입(없으면 생략)
- 구분자는 공백(space), 인수에 공백을 포함하려면 [ ] 사용


#### PAM의 주요 모듈

1. pam_securetty.so
- 접속하는 계정이 root인 경우 /etc/securetty 파일에 기록된 터미널을 통하는 경우에만 허가
- root 외 사용자는 항상 인증에 성공한 것으로 처리
- /etc/par.d/login, /etc/pam.d/remote 파일에 설정되어 있음

2. pam_lisfile.so
- 임의의 파일에 대해 서비스를 허가하거나 거부하는 방법을 제공
- 레드햇 계열인 경우 /etc/pam.d/vsftpd 파일에 설정되어 ftp 사용자 거부 목록 파일로 이용되고 있음

3. pam_nologin.so
- /etc/nologin 파일이 존재하면 root만 로그인할 수 있고, 그 외 사용자는 에러 메시지와 함께 거부할 때 사용
- 로그인 관련 서비스인 login, remote, sshd 등에 설정되어 있음

4. pam_deny.so
- 접근을 무조건 거부할 때 사용
- 응용 프로그램에게 항상 실패를 리턴
- other, system-auth 등에 사용

5. pam_wheel.so
- root 권한을 얻을 수 있는 사용자를 wheel(또는 group-ID=0) 그룹으로 묶어서 사용하도록 지원하는 모듈
- su 명령과 관련된 /etc/pam.d/su에 사용하면 매우 유용

6. pam_rootok.so
- UID가 0인 사용자를 인증하는 모듈
- 보통 root가 암호 입력 없이 해당 서비스에 대한 접근을 허용할 때 사용
- 사용 서비스는 su, setup 등이 해당

### sudo(Superuser do)
- 특정 사용자 또는 특정 그룹에 root 사용자 권한을 가질 수 있도록 해주는 도구
- /etc/sudoers 파일에 설정된 사용자가 실행할 수 있음
- `visudo` 명령으로 sudo의 환경 설정 파일인 /etc/sudoers를 편집할 수 있음
  - '사용자 접속한_곳=명령어_경로' 형식으로 지정

### 파일 시스템 보안
#### lsattr, chattr
- 리눅스 파일 시스템 ext2부터 사용되는 명령
- 기본적인 파일 권한 이외에 13가지 속성을 부여하여 관리할 때 사용

<b>lsattr</b> 

- 파일에 설정된 속성을 확인할 때 사용하는 명령

```
lsattr [option] [파일명]
```

주요 옵션

<table>
<tr><td>-R</td><td>하위 디렉터리까지 한 번에 속성 확인</td></tr>
<tr><td>-a</td><td>숨김 파일과 디렉터리까지 확인</td></tr>
</table>

<b>chattr</b>

- 파일의 속성을 변경하는 명령
- root 사용자만 사용 가능

```
chattr [option] mode 파일명
```

주요 옵션
<table>
<tr><td>-R</td><td>하위 디렉터리까지 한 번에 속성 변경</td></tr>
</table>

mode : +(부여), -(해제), =(할당) 기호와 속성을 조합하여 사용

<table>
<thead><td>속성</td><td>설명</td></thead>
<tr><td>A</td><td>파일 수정 시에 atime은 수정하지 않는다.</td></tr>
<tr><td>a</td><td>해당 파일에 추가만 가능하도록 설정한다.</td></tr>
<tr><td>d</td><td>dump로 백업되지 않도록 설정한다.</td></tr>
<tr><td>i</td><td>해당 파일의 변경, 삭제, 이름 변경, 파일 추가, 링크 파일 생성 등을 불가능하게 한다.</td></tr>
<tr><td>S</td><td>파일이 변경될 경우 디스크 동기화가 일어나도록 할 때 사용한다.</td></tr>
<tr><td>e</td><td>디스크 블록에 매핑하기 위해 확장된 파일임을 표시하는 것<br>chattr 속성으로 없앨 수 없는 속성</td></tr>
</table>

#### ACL(Access Control List)
- 파일이나 디렉터리에 접근 권한을 제어할 수 없도록 만든 시스템
- ext3부터 지원
- `getfacl`, `setfacl` 명령으로 ACL의 확인 및 설정 가능

#### getfacl(get file access control lists)
- 파일이나 디렉터리에 설정된 접근 권한 리스트를 확인하는 명령

```
getfacl [option] [파일명]
```

주요 옵션
<table>
<tr><td>-d</td><td>접근 권한 리스트의 기본값 출력</td></tr>
</table>

#### setfacl(set file access control lists)
- 파일이나 디렉터리에 접근 권한 리스트를 설정하는 명령
- root만 사용 가능

```
setfacl [option] [파일명]
```

주요 옵션
<table>
<tr><td>-m</td><td>권한을 지정하거나 수정(--modify)</td></tr>
<tr><td>-x</td><td>권한 삭제(--remove)</td></tr>
<tr><td>-R</td><td>하위 디렉터리와 파일까지 권한 변경(--recursive)</td></tr>
<tr><td>-b</td><td>권한 및 mask 등 지정한 권한을 전부 제거(--remove-all)</td></tr>
</table>

사용 예
```
# setfacl -m u::rw test.txt
test.txt 파일에 소유자 권한에 읽기 및 쓰기 권한을 부여

# setfacl -m u:user1:rw test.txt
test.txt 파일에 user1 사용자의 읽기 및 쓰기 권한을 부여

# setfacl -x mask test.txt
test.txt 의 mask 값 삭제
사용자의 권한이 부여되어 있는 경우 mask 삭제가 불가

# setfacl -b test.txt
test.txt의 권한 및 mask 값 삭제

# setfacl -m d:u:user1:rw /data
/data 디렉터리에 생성되는 모든 파일들을 user1 사용자가 읽기 및 쓰기 권한이 되도록 설정
```

### 주요 보안 도구
#### nmap(network mapper)
- 네트워크 탐지 도구 및 보안 스캐너
- 시스템의 서비스 중인 포트를 스캔하여 관련 정보를 출력
- 불필요하게 대상 검색을 시도하면 상대방 시스템의 IDS에 감지되어 IP 차단 등의 불이익을 당할 수 도 있음
- 레드햇 계열에 설치되는 nmap 패키지에 포함되는 것
  - nmap
  - 소켓을 리다이렉트 시켜주는 ncat
  - nmap 스캔의 결과를 비교해주는 ndiff
  - ping과 유사하게 패킷을 생성하는 도구인 nping
- 동종 프로그램 : WPScan, Nikto 등 

사용 예
```
# nmap -O -p 1-35535 localhost
로컬 호스트의 운영체제와 포트번호 1~35535까지 열린 포트를 점검하여 결과 출력
```

#### tcpdump
- 명령행에서 사용하는 네트워크 트래픽 모니터링 도구
- 특정한 조건식을 설정하여 네트워크 인터페이스를 거치는 패킷들의 헤더 정보를 출력할 수 있음
- 외부 호스트로부터 들어오는 패킷들을 검사할 수 있음
- 동종 프로그램 : ethereal, wireshark 등

#### tripwire(무결성 검사 도구)
- 파일의 변조 여부를 검사하는 도구
- 모든 파일들에 대한 데이터베이스를 만들어 파일들의 변조 여부를 검사
- MD5, SHA, CRC-32 등 다양한 해시 함수를 제공

#### nessus
- 서버의 보안 취약점을 검사하는 도구
- 문제가 되는 서비스에 대해 정보를 알려주고 관련 사이트나 대처 방안 등을 제시하는 프로그램

#### GnuPG(GNU Privacy Guard)
- 공개키와 비밀키를 생성하여 암호화하는 OpenPGP를 공개 버전으로 구현한 것
- 데이터의 암호화와 디지털 서명 생성 등에 사용

#### John The Ripper
- Password crack tool
- 유닉스 계열, Windows 계열, DOS, BeOS, OpenVMS 등 다양한 운영체제 지원
- 텍스트 형태의 사전 파일을 이용해서 일일히 대입한 후 /etc/shadow와 비교하여 사용자의 패스워드를 알아내는 방식

### SELinux(Security Enhanced Linux)
- 오픈 소스인 리눅스 커널의 보안을 강화하기 위해 등장
- 특정 데몬의 버그를 통해 root 권한을 획득하더라도 다른 데몬이나 시스템에 접근이 불가능하도록 하여 시스템의 보안을 강화하는 것이 주목적
- 기본적인 적용 확인 및 변경은 /etc/selinux/config(또는 /etc/sysconfig/selinux)에서 vi 편집기 등을 이용
- 셸 상에서는 `getenforce`, `setenforce` 명령으로 확인 및 설정 가능
- X 윈도에서는 system-config-selinux 도구를 이용하여 설정 가능
  - 'yum install policycoreutils-gui' 명령으로 관련 패키지를 설치해야 함

# 시스템 백업
## 정책 수립
### Backup

<b>고려 사항</b>

1. 백업 대상 선택
- 쉽게 설치가 가능하거나 항상 자동으로 생성되는 데이터들은 제외

2. 백업 종류
- 전체 백업(Full Backup) : 특정 디스크나 파티션 등 전체를 백업
- 부분 백업(Partial Backup) : 선택한 파일들만 백업하는 것
  - 증분 백업(Increamental Backup) : 증가된 내용만 백업
  - 차등 백업(Differential Backup) : 바뀐 부분만 백업

3. 백업 주기
4. 매체(Media) 선택 여부
- 매체 : 플로피 디스크, 테이프, 하드디스크 등
- 고려사항 : 경제성, 효율성, 신뢰성, 편리성, 처리속도, 가용성 등

5. 백업 방법
- 어떠한 프로그램을 선택할 것인지 결정
- 리눅스에서 사용 가능한 백업 프로그램 : tar, dd, dupm, cpio, rsync 등

### Backup 및 Restore
#### tar
- 유닉스와 리눅스 시스템 모두 사용 가능한 유틸리티
- 파일이나 디렉터리들을 하나의 파일로 묶어주는 역할
- 소량의 파일 백업에 좋고 원본 파일을 남겨두므로 안전함

#### cpio(copy input to output)
- tar와 비슷한 유틸리티로 많은 양의 데이터에 대해 tar보다 빠름
- 장치 파일이나 네트워크 파일 등의 특수 파일도 백업이 가능
- 백업본에 손상된 부분이 있더라도 손상된 부분을 제외하고 나머지 부분을 복구
- <b>증분 백업은 지원하지 않음</b>


```
[ 셸명령어 | ] cpio option > 파일명
cpio option < 파일명
```

주요 옵션
<table>
<tr><td>-o</td><td>표준 출력으로 보내어 사용(--create)</td></tr>
<tr><td>-i</td><td>표준 입력으로 받을 때 사용(--extract)<br>백업한 자료를 불러올 때 사용</td></tr>
<tr><td>-v</td><td>과정을 상세히 출력(--verbose)</td></tr>
<tr><td>-c</td><td>아카이브 포맷 형식을 'new SVR4 portable format with no CRC'으로 지정<br>'-H newc' 옵션과 동일</td></tr>
<tr><td>-d</td><td>필요할 경우 디렉터리 생성(--make-directories)</td></tr>
<tr><td>-t</td><td>내용만 확인할 때 사용(--list)</td></tr>
<tr><td>-F</td><td>표준 입출력 전환 기호 대신 파일명을 지정할 때 사용(--file=파일명)</td></tr>
<tr><td>-B</td><td>입출력 블록 사이즈를 조절할 때 사용<br>기본 512byte, 최대 5120byte까지 가능</td></tr>
<tr><td>-H 포맷</td><td>아카이브 포맷 형식을 지정하는 옵션(--format 포맷)<br>'bin', 'newc', 'crc' 등 지정 가능</td></tr>
</table>

### dump 및 restore
- 파일 시스템 전체를 백업할 때 사용하는 유틸리티
- 보통 파티션 단위로 백업할 때 많이 사용
- 전체 백업과 증분 백업을 지원
  - 레벨 0 : 전체 백업
  - 레벨 1~9 : 증분 백업
- 백업 시 /etc/fstab 파일을 참조
- ext 파일 시스템 계열만 지원(XFS는 지원하지 않음)


#### dump
- 파티션 단위로 백업하는 명령

```
dump option 파일명 백업대상
```

주요 옵션
<table>
<tr><td>-0~9</td><td>레벨 지정</td></tr>
<tr><td>-f</td><td>백업할 매체나 파일명 기재</td></tr>
<tr><td>-u</td><td>dump 작업 후 /etc/dumpdates 파일에 관련 정보를 기록</td></tr>
</table>

#### restore
- dump로 백업한 내용을 복원할 때 사용하는 명령
- 파일 기반으로 백업한 경우, 복원하고자 하는 파티션 영역에 해당 파일을 복사한 후 실행


```
restore option 백업파일명
```

주요 옵션
<table>
<tr><td>-i</td><td>대화식으로 복구할 파일을 선택한 후 복원할 때 사용</td></tr>
<tr><td>-f</td><td>백업할 매체나 파일명 기재</td></tr>
<tr><td>-r</td><td>전체 복원 시 사용<br>파일 시스템이 미리 생성되어 있어야 하고, 마운트도 되어 있어야 함</td></tr>
</table>


### dd(data dumper)
- 파티션이나 디스크 단위로 백업할 때 사용하는 유틸리티
- 사용하기 쉬우나 많은 시간이 소요됨

### rsync(remote synchronous)
- 네트워크로 연결된 원격지의 파일들을 동기하는 유틸리티
- 제공 기능
  - 링크된 파일이나 디바이스 파일도 복사 가능하고, 그룹을 포함한 소유권 및 허가권도 유지하여 복사할 수 있다.
  - GNU tar와 유사한 옵션을 사용하며, 특정한 부분만 백업할 수 있다.
  - CVS의 exclude 모드처럼 같은 파일들은 제외할 수 있다.
  - ssh나 rsh을 이용하여 전송 가능하고 root 권한이 필요하지 않다.
  - 내부 파이프라인을 통하여 전송 기간을 줄인다.
  - 익명(anonymous) 사용자 및 인증된 사용자를 지원하여 미러링(mirroring)에 이상적이다.
  - 로컬 시스템의백업 시에는 별다른 서버 설정 없이 사용이 가능하다.

```
rsync [option] source destination
```

주요 옵션
<table>
<tr><td>-r</td><td>하위 디렉터리까지 실행(--recursive)</td></tr>
<tr><td>-l</td><td>심볼릭 링크를 그대로 보존(--links)</td></tr>
<tr><td>-L</td><td>심볼릭 링크가 참고하고 있는 파일을 복사(--copy-links)</td></tr>
<tr><td>-p</td><td>퍼미션을 그대로 보존(--perms)</td></tr>
<tr><td>-t</td><td>타임스탬프를 그대로 보존(--times)</td></tr>
<tr><td>-g</td><td>그룹 소유권을 그대로 보존(--group)</td></tr>
<tr><td>-o</td><td>소유권을 그대로 보존(root만 가능)(--owner)</td></tr>
<tr><td>-D</td><td>디바이스 파일을 그대로 보존(root만 가능)</td></tr>
<tr><td>-H</td><td>하드 링크를 그대로 보존(--hard-links)</td></tr>
<tr><td>-a</td><td>-rlptgoD를 한 번에 실행하는 옵션(--archive)</td></tr>
<tr><td>-v</td><td>진행 상황을 자세히 출력(--verbose)</td></tr>
<tr><td>-u</td><td>업데이트된 내용만 전송(--update)</td></tr>
<tr><td>-z</td><td>전송할 때 압축(--compress)</td></tr>
<tr><td>-b</td><td>백업할 때 동일한 파일이 존재하는 경우 ~를 붙여서 백업 파일을 생성(--backup)</td></tr>
<tr><td>-e</td><td>원격지에서 사용할 셸 명령을 기재(--rsh=COMMAND)</td></tr>
<tr><td>--progress</td><td>명령이 실행되는 동안의 전송 상황 정보를 출력</td></tr>
<tr><td>--delete</td><td>송신측에서 없는 파일이 수신측의 백업 디렉터리에 존재하면 삭제</td></tr>
</table>


