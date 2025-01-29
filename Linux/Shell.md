# 셸(Shell)
셸
- 커널과 사용자간의 다리역할
- 사용자로부터 명령을 받아 그것을 해석하고 프로그램을 실행하는 역할
- 사용자가 시스템에 로그인을 하면 각 사용자에게 설정된 셸이 부여되면서 다양한 명령을 수행할 수 있게됨

주요 셸의 특징
<table>
<tr><td>Bourne Shell</td><td>

- 1977년 AT&T 벨 연구소의 스티븐 본(Stephen Bourne)이 개발한 유닉스 버전 7의 기본 셸</td></tr>
<tr><td>C 셸</td><td>

- 1978년 빌 조이가 개발
- C 언어를 기반으로 만들어짐
- 히스토리 기능, Alias 기능, 작업 제어 등의 기능을 포함
- 명령행 편집 기능은 지원하지 않음
</td></tr>
<tr><td>tcsh</td><td>

- 1981년 C 셸과 명령어 완성 및 명령행 편집 기능을 통합하여 개발됨
</td></tr>
<tr><td>ksh</td><td>

- 1983년 AT&T사의 데이비드 콘(David Korn)이 개발
- 본 셸을 확장하여 만들어졌으며 C 셸의 기능을 추가함
- 작업 제어, Alias, 히스토리 기능, Vi 및 Emacs 스타일의 명령행 편집 기능, 명령행 완성 기능 등을 제공
</td></tr>
<tr><td>dash</td><td>

- 데비안 및 우분투 계열 리눅스의 기본 셸
- Bash와 비교하여 소스 크기도 매우 작고 처리 속도도 빠르나, history 명령 등은 지원하지 않음
</td></tr>
<tr><td>bash</td><td>

- 1989년 브라이언 폭스가 GNU 프로젝트를 위해 본 셸을 기반으로 개발
- 다양한 운영체제에서 사용 중이며 리눅스의 표준 셸
</td></tr>
</table>

### 셸 확인 및 변경
<b>셸 확인</b>
- 로그인 셸 관련 환경 변수 SEHLL을 통해 확인
```
echo $SHELL
```

변경 가능한 셸의 확인
- 'chsh -l' 명령 실행
- /etc/shells 파일 확인
  
![shells](https://github.com/dee021/TIL/blob/main/Linux/img/checkShell.png)

<b>셸 변경</b>

1. chsh 명령 실행 
   - 사용자 암호 입력 시 변경하려는 셸의 절대 경로 입력
   - 변경된 셸의 적용은 다음 로그인부터 유효함
2. /bin/csh 와 같이 셸 지정
   - 기본 bash 셸에서 포크(fork)되어 추가로 프로세스가 발생
   - exit 입력 시 기본 셸인 bash로 돌아감

<b>chsh</b>
```
chsh [option] [사용자명]
```
주요 옵션
<table>
<tr><td>-s</td><td>변경하고자 하는 셸을 명시할 때 사용(--shell)</td></tr>
<tr><td>-l</td><td>사용 가능한 셸의 목록 출력. /etc/shells 파일의 내용을 출력한다.(--list-shells)</td></tr>
</table>

## 셸 변수와 환경 변수
<B>셸 변수</B> : 특정한 셸에서만 적용되는 변수
- 명령행에서 '변수명=값' 형태로 지정하여 사용
- 'echo $변수명'의 형태로 변수값 출력
- set 명령으로 선언된 셸 변수 전부 확인 가능

<b>환경 변수</b> : 셸의 환경을 정의하는 중요한 역할을 수행하는 변수
- 미리 예약된 변수명을 사용
  - bash에서는 PATH, SHELL과 같이 대문자로 된 변수로 구성
- env 명령으로 설정된 전체 환경 변수 확인 가능


주요 환경 변수

<table>
<tr><td>HOME</td><td>사용자의 홈 디렉터리</td></tr>
<tr><td>PTAH</td><td>실행 파일을 찾는 디렉터리 경로</td></tr>
<tr><td>LANG</td><td>셸 사용 시 기본으로 지원되는 언어</td></tr>
<tr><td>TERM</td><td>로그인한 터미널 종류</td></tr>
<tr><td>PWD</td><td>사용자의 현재 작업 디렉터리</td></tr>
<tr><td>SHELL</td><td>사용자의 로그인 셸</td></tr>
<tr><td>USER</td><td>사용자의 이름</td></tr>
<tr><td>DISPLAY</td><td>X 윈도에서 프로그램 실행 시 출력되는 창</td></tr>
<tr><td>PS1</td><td>프롬프트(Prompt) 변수</td></tr>
<tr><td>PS2</td><td>2차 프롬프트 변수</td></tr>
<tr><td>HOSTNAME</td><td>시스템의 호스트명</td></tr>
<tr><td>MAIL</td><td>도착한 메일이 저장되는 경로</td></tr>
<tr><td>TMOUT</td><td>사용자가 로그인한 후 일정 시간 동안 작업을 하지 않을 경우 로그아웃시키는 시간(단위: 초)</td></tr>
<tr><td>UID</td><td>사용자의 UID</td></tr>
</table>

히스토리 관련 환경 변수
<table>
<tr><td>HISTFILE</td><td>히스토리(History) 파일의 절대 경로</td></tr>
<tr><td>HISTSIZE</td><td>히스토리 파일에 저장되는 명령어의 개수(줄 기준)</td></tr>
<tr><td>HISTFILESIZE</td><td>히스토리 파일의 파일 크기</td></tr>
<tr><td>HISTCONTROL</td><td>중복되는 명령에 대한 기록 유무를 지정하는 변수
<BR>CentOS 7에서는 'ignoredups'로 기본 적용되며, 연속적으로 중복된 명령은 저장하지 않는다.
</td></tr>
<tr><td>HISTTIMEFORMAT</td><td>history 명령 실행 시 출력되는 시간 형식을 지정</td></tr>
</table>

## bash의 주요 기능
1. 명령행 완성 기능
- 명령 입력 시 일부분만 입력하고 [TAB]키를 눌러 나머지 부분을 자동 완성 시키는 기능
- [TAB]키를 한 번 더 누르면 목록을 확인할 수 있음
- 보통 파일이나 디렉터리 입력 시 

2. 명령어 History 기능
- 히스토리 리스트 버퍼에 스택으로 저장된 명령들을 위/아래 방향키를 이용하여 검색 및 편집하여 사용 가능
- history 명령 입력 시 히스토리 리스트에 있는 명령어 출력
- 각 사용자가 실행한 명령은 사용자의 홈 디렉터리의 .bash_history 파일에 추가로 기록됨 
  - 사용자가 로그아웃 시 메모리에 기억된 명령의 목록을 파일에 저장

```
history
히스토리에 저장된 명령어 목록 출력

history 5
최근에 입력한 마지막 5개의 명령어 목록 출력
```
- history는 '!' 로 대체하여 사용 가능

<table>
<tr><td>!!</td><td>마지막에 사용한 명령을 실행</td></tr>
<tr><td>!n</td><td>n번째 사용한 명령을 실행</td></tr>
<tr><td>!-n</td><td>사용한 명령 목록을 역으로 세어 n번째 명령을 실행</td></tr>
<tr><td>!문자열</td><td>가장 최근에 사용한 명령 중 '문자열'로 시작하는 명령을 찾아 실행</td></tr>
<tr><td>!?문자열?</td><td>가장 최근에 사용한 명령 중 '문자열'을 포함하는 명령을 찾아 실행</td></tr>
<tr><td>^문자열1^문자열2</td><td>마지막에 사용한 명령문의 '문자열1'을 '문자열2'로 대체한 후 실행</td></tr>
</table>

3. alias 기능
- 어떠한 명령에 별명을 붙여 사용하는 기능

```
alias
alias가 설정된 목록 출력

alias 별칭='기존 명령어의 조합'
alias 지정

unalias 별칭
alias 해제
```

4. 명령행 편집 기능
- 명령행에서 단축키를 입력해 커서 이동을 빠르게 하거나 손쉽게 삭제할 수 있는 기능 등을 제공

5. 명령 대체(Command Substitution, 명령 치환) 기능
- 특정 명령의 결과를 다른 명령어의 인자값으로 사용하는 것
- \` `(Backquotes)나 $( )를 이용하여 대체 가능

![commandSubstitution](https://github.com/dee021/TIL/blob/main/Linux/img/commandSubstitution.png)

6. 그룹 명령 실행
- 하나의 명령 행에 여러 개의 명령어를 동시에 사용할 수 있음
  - ';' : 한 줄에 여러 명령을 나열하기 위해 사용. 입력한 순서대로 순차처리 함
  - || : 논리적 OR. 앞의 명령이 성공이면 결과를 출력하고, 그렇지 않으면 뒤의 명령을 실행하여 결과를 출력
  - && : 논리적 AND. 앞의 명령이 성공적으로 수행되어야만 뒤의 명령을 수행

7. 표준 입출력 제어 기능

8. 리다이렉션(redirection)
- 어떤 프로세스의 입/출력을 표준 입출력이 아닌 다른 입출력으로 변경할 때 사용

9. 파이프(pipe)
- 어떤 프로세스의 표준 출력이 다른 프로세스의 표준 입력으로 쓰이게 하는 것

관련 명령어

tee : 파이프 연결 출력을 두 갈래로 나눌 수 있다.
```
tee [option] [파일]
```
option

<table>
<tr><td>-i</td><td>인터럽트를 무시하도록 한다</td></tr>
<tr><td>-a</td><td>지정된 파일로 출력을 덮어쓰지 않고 파일 내용 뒤에 추가한다</td></tr>
</table>

xargs : 표준 입력으로부터 값을 받아 처리하는 명령
```
명령어 | xargs [option]
```
option
<table>
<tr><td>-n 값</td><td>한 번에 처리하는 최대 인자값을 지정하는 옵션(--max-args=값)</td></tr>
</table>

예시
```
ls -l | tee list.txt | more
파일의 목록을 list.txt에 기록함과 동시에 화면에 출력한다

echo a.txt | xargs ls -l
a.txt의 ls -l 결과를 출력
```

![xargs](https://github.com/dee021/TIL/blob/main/Linux/img/xargs.png)

10. 작업 제어 기능
- 작업을 백그라운드와 포그라운드에서 실행할 수 있으며, 이 작업을 서로 전환하여 실행할 수 있음

11. 산술 연산 기능
- 다음 형식을 이용하여 산술 연산이 가능
```
expr 값 연산자 값

echo $[값 연산자 값]
```

12. 프롬프트(prompt) 제어 기능
- 환경 변수 PS1을 이용하여 프롬프트를 원하는 대로 변경 지정이 가능

13. 확장된 내부 명령어 
