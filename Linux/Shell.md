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
  
![shells](https://github.com/dee021/TIL/blob/linuxmaster/Linux/img/checkShell.png)

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

![commandSubstitution](https://github.com/dee021/TIL/blob/linuxmaster/Linux/img/commandSubstitution.png)

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

![xargs](https://github.com/dee021/TIL/blob/linuxmaster/Linux/img/xargs.png)

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


#### bash 관련 디렉터리
/etc/profile.d : 몇몇 응용 프로그램들이 시작할 때 필요한 스크립트가 위치하는 디렉터리

- 보통 /etc/profile에서 호출됨
- 일반 사용자의 alias 설정 등과 관련된 스크립트가 존재

bash 관련 파일
<table>
<tr><td>/etc/profile</td><td>시스템 전체(모든 사용자)에 적용되는 환경 변수와 시작 관련 프로그램을 설정</td></tr>
<tr><td>/etc/bashrc</td><td>시스템 전체(모든 사용자)에 적용되는 alias와 함수를 설정</td></tr>
<tr><td>~/.bash_profile</td><td>개인 사용자의 환경 설정과 시작 프로 그램 설정과 관련이 있는 파일<br>
사용자 로그인 시 읽어들임<br>
경로, 환경 변수 등의 설정이 들어있으며 PATH와 같은 환경 변수 수정 시 사용
</td></tr>
<tr><td>~/.bash_logout</td><td>개인 사용자가 로그아웃할 때 수행하는 설정을 지정하는 파일</td></tr>
</table>

#### 셸에서 사용되는 특수 문자 

<table>
<tr><td>~(tilde)</td><td>홈 디렉터리</td></tr>
<tr><td>.</td><td>

1) 현재 디렉터리
2) source : 명령행 맨 앞에서 셸 스크립트를 실행시킬 때 사용
  </td></tr>
<tr><td>' '</td><td>모든 문자를 일반 문자로 취급</td></tr>
<tr><td>" "</td><td>$, \`(backquotes), \(Backslash), !(history)를 제외한 모든 문자를 일반 문자로 취급</td></tr>
<tr><td>` `</td><td>명령 대체 기능을 수행</td></tr>
<tr><td>$</td><td>셸 변수 기호</td></tr>
<tr><td>*</td><td>0자 이상의 모든 문자</td></tr>
<tr><td>?</td><td>

1) 한 문자
2) 특정 명령에 대한 결과를 갖는 Return 변수. 셸 상에서 내린 명령의 에러 유무를 확인할 수 있음
   </td></tr>
<tr><td>( )</td><td>subshell. 하나의 셸 단위로 묶는 역할</td></tr>
<tr><td>[ ]</td><td>bracket. 안에 나열된 문자 중 하나를 의미</td></tr>
<tr><td>{ }</td><td>안에 제시된 문자열 중 하나로 대치</td></tr>
</table>

# Shell Programming

- 셸에서 사용되는 여러 명령어들을 모아 하나의 파일로 만드는 과정
  - Shell Script : 셸 프로그래밍을 통해 만들어진 파일 또는 프로그램
- 셸은 for문 및 case문 같은 구문, 변수, 함수 등과 같이 보통 언어가 가지고 있는 기본적인 특성을 가지고 있음

#### 셸 스크립트 작성 방법

1. vi 편집기 등을 사용하여 파일 생성

```
$ vi who2.sh
```

2. 첫 번째 줄에 사용할 셸 명시

```
#!/bin/bash
```

3. 이후 원하는 명령 및 구분 등 입력

```
#/bin/bash
echo "Login List"
who
```

4. 스크립트를 실행 가능한 파일로 만듦(소유권 변경)

```
$ chmod 755 who2.sh
```

5. 스크립트 실행

```
$ ./who2.sh
```

#### 셸 스크립트 실행
1. 계속적으로 사용할 경우 : 경로(PATH) 등록
- 경로에 등록되지 않은 스크립트 실행 시 './' 와 같이 경로를 붙이지 않고 파일명만 실행 시 'command not found' 메시지
- PATH에 해당 경로를 등록하거나 실행 파일을 PATH에 등록된 디렉터리로 옮겨야 함

방법 1. `make ~/bin` 또는 `make ~/.local/bin` 명령 실행

방법 2. `export PATH=$PATH:$HOME` 실행

2. 허가권 없이 셸 스크립트 실행

방법. 앞에 sh, source, . 붙이기

```
$ sh who2.sh
$ source who2.sh
$ . who2.sh
```

## 셸 프로그래밍 문법

1. 주석 : '#'을 이용
2. 변수
- 셸에서의 변수형은 string만 가능하며, 변수형 선언이 필요하지 않음

```
값 지정
var=value

변수 사용
$var
```

-  변수 지정 시 대입연산자와 연산자, 피연산자 사이 공백이 없어야 함
-  변수명은 '-'를 제외한 특수 문자와 숫자로 시작할 수 없음


기타 변수 대응법
<table>
<thead><td>종류</td><td>설명</td></thead>
<tr><td>${var}</td><td>var에 해당하는 변수값으로 치환</td></tr>
<tr><td>${var:=value}</td><td>var이 null이면 value로 할당 후 저장<br>var이 null이 아니면 var 값 사용</td></tr>
<tr><td>${var:+value}</td><td>var이 null이 아니면 value 사용(저장하지 않음)<br>var이 null이면 var 사용</td></tr>
<tr><td>${var:-value}</td><td>var 값이 있다면 var 값 사용<br>var 값이 없으면 value값 사용(저장하지 않음)</td></tr>
<tr><td>${var:?value}</td><td>var 값이 있다면 var 값 사용<br>var 값이 없다면 error 내면서 value를 보여줌</td></tr>
<tr><td>${#var}</td><td>var 값의 문자열 길이 보여줌</td></tr>
<tr><td>${var:offset}</td><td>var 값의 앞에서부터 offset만큼 삭제한 값으로 치환</td></tr>
<tr><td>${var:ofset:length}</td><td>var 값의 앞에서부터 offset만큼 삭제한 후 length만큼 센 값으로 치환</td></tr>
</table>

관련 환경 변수
<table>
<thead><td>종류</td><td>설명</td></thead>
<tr><td>$IFS</td><td>입력필드 구분자<br>셸 상에서 입력을 읽어 들일 때 글자를 구분하기 위한 목적으로 사용되는 문자 목록을 의미<br>보통은 스페이스, 탭, 개행 문자를 사용</td></tr>
</table>

Argument 변수

- 위치 매개 변수(positional parameter)
- 매개 변수를 불러올 때 스크립트의 명령행 인자를 담당
<table>
<thead><td>종류</td><td>설명</td></thead>
<tr><td>$0</td><td>실행된 셸 스크립트명</td></tr>
<tr><td>$n (n > 0)</td><td>스크립트에 넘겨진 n 번째 인자</td></tr>
<tr><td>$#</td><td>스크립트에 넘겨진 인자 개수</td></tr>
<tr><td>$$</td><td>셸 스크립트의 PID</td></tr>
<tr><td>$*</td><td>스크립트에 전달된 인자 전체를 IFS 변수의 첫 번째 문자로 구분</td></tr>
<tr><td>$@</td><td>$*와 동일(IFS 환경 변수를 사용하지 않음)</td></tr>
<tr><td>$?</td><td>실행한 뒤의 반환 값<br><b>참이면 0, 거짓이면 1 반환</b></td></tr>
<tr><td>$-</td><td>현재 Shell이 호출될 때 사용한 옵션들</td></tr>
</table>

Shell 변수 관련 명령어
<table>
<thead><td>명령</td><td>설명</td></thead>
<tr><td>set</td><td>셸 변수를 출력하는 명령<br>리눅스에서는 셸의 환경과 관련된 변수, 함수 등도 출력</td></tr>
<tr><td>env</td><td>환경 변수를 출력하는 명령<br>export된 변수는 이 명령으로 확인해야 하고 사용자가 선언한 셸 변수 정보도 함께 출력한다.</td></tr>
<tr><td>export</td><td>특정 변수의 범위를 환경 데이터 공간으로 전송하여 자식 프로세스에서도 특정 변수를 사용 가능하게 한다.</td></tr>
<tr><td>unset</td><td>선언된 변수를 제거한다.</td></tr>
</table>

![shell variable](https://github.com/dee021/TIL/blob/linuxmaster/Linux/img/shellVar.png)

- 셸 변수로 선언한 user1과 export로 선언한 user2는 set 명령으로 확인 가능
  - 셸 변수인지 export된 변수인지는 확인할 수 없음
- export로 선언한 user2는 env 명령으로 확인 가능

3. echo문과 escape 문자

- echo문은 라인을 제어하는 명령해석기이며 -e 옵션과 같이 \로 시작하는 escape 특수 문자를 사용할 수 있음

escape 특수 문자 종류

<table>
<tr><td>\f</td><td>Formfeed. 앞 문자열만큼 열을 밀어서 이동시킨다.</td></tr>
<tr><td>\n</td><td>New line. 새로운 줄로 바꾼다.</td></tr>
<tr><td>\r</td><td>Carriage Return. 앞 문자열의 앞부분부터 뒷 문자열만큼 대체하고 반환한다.</td></tr>
<tr><td>\t</td><td>탭만큼 띄운다.</td></tr>
<tr><td>\\</td><td>\를 표기한다.</td></tr>
</table>

![escape](https://github.com/dee021/TIL/blob/linuxmaster/Linux/img/escape.png)

4. 간단한 조건식
- 명령행에서 test 명령어를 이용하여 간단히 비교할 수 있음
- 셸 스크립트에서 보통 if문과 같이 사용됨
- 셸 스크립트에서 사용할 경우 test 명령어를 생략하고 [ ]를 이용
  - 표현식과 '[', ']' 사이에 반드시 공백 문자가 들어가야 함


```
test 표현식

[ 표현식 ]
```

문자열 비교 표현식
<table>
<thead><td>종류</td><td>true 조건</td></thead>
<tr><td>[ string ]</td><td>string이 빈 문자열이 아님</td></tr>
<tr><td>[ string1 = string2 ]</td><td>두 문자열이 같음</td></tr>
<tr><td>[ string1 != string2 ]</td><td>두 문자열이 다름</td></tr>
<tr><td>[ -n string ]</td><td>문자열이 null이 아님</td></tr>
<tr><td>[ -z string ]</td><td>문자열이 null</td></tr>
</table>

산술 비교 표현식
<table>
<thead><td>종류</td><td>true 조건</td></thead>
<tr><td>[ expr1 -eq expr2 ]</td><td>(equal) 두 표현식 값이 같음</td></tr>
<tr><td>[ expr1 -ne expr2 ]</td><td>(not equal)두 표현식 값이 다름</td></tr>
<tr><td>[ expr1 -gt expr2 ]</td><td>(greater than) expr1 > expr2</td></tr>
<tr><td>[ expr1 -ge expr2 ]</td><td>(greater equal) expr1 >= expr2</td></tr>
<tr><td>[ expr1 -lt expr2 ]</td><td>(less than) expr1 < expr2</td></tr>
<tr><td>[ expr1 -le expr2 ]</td><td>(less equal) expr1 <= expr2</td></tr>
<tr><td>[ ! expr ]</td><td>expr이 거짓(!뒤 공백 문자)</td></tr>
<tr><td>[ expr1 -a expr2 ]</td><td>(AND) expr1과 expr2가 모두 true</td></tr>
<tr><td>[ expr1 -o expr2 ]</td><td>(OR) expr1과 expr2 중 적어도 하나가 true</td></tr>
</table>

파일 조건 표현식
<table>
<thead><td>종류</td><td>true 조건</td></thead>
<tr><td>[ -b FILE ]</td><td>FILE이 블록 디바이스</td></tr>
<tr><td>[ -c FILE ]</td><td>FILE이 문자 디바이스</td></tr>
<tr><td>[ -d FILE ]</td><td>FILE이 디렉터리</td></tr>
<tr><td>[ -e FILE ]</td><td>FILE이 존재함</td></tr>
<tr><td>[ -f FILE ]</td><td>FILE이 존재하고 정규 파일임<br>(호환성 문제로 -e보다 -f를 주로 사용)</td></tr>
<tr><td>[ -u FILE ]</td><td>FILE에 SUID가 있음</td></tr>
<tr><td>[ -g FILE ]</td><td>FILE에 SGID가 있음</td></tr>
<tr><td>[ -k FILE ]</td><td>FILE에 sticky bit가 있음</td></tr>
<tr><td>[ -L FILE ]</td><td>FILE이 심볼릭 링크</td></tr>
<tr><td>[ -p FILE ]</td><td>FILE이 Named pipe</td></tr>
<tr><td>[ -r FILE ]</td><td>현재 사용자가 읽을 수 있는 파일임</td></tr>
<tr><td>[ -w FILE ]</td><td>현재 사용자가 쓸 수 있는 파일임</td></tr>
<tr><td>[ -x FILE ]</td><td>현재 사용자가 실행할 수 있는 파일임</td></tr>
<tr><td>[ -s FILE ]</td><td>파일이 비어있지 않음</td></tr>
<tr><td>[ -S FILE ]</td><td>파일이 소켓 디바이스</td></tr>
<tr><td>[ -O FILE ]</td><td>FILE의 소유자가 현재 사용자임</td></tr>
<tr><td>[ -G FILE ]</td><td>FILE의 그룹이 현재 사용자의 그룹과 같음</td></tr>
<tr><td>[ -t FD ]</td><td>FD(File Descriptor)가 열려진 터미널임</td></tr>
<tr><td>[ FILE1 -nt FILE2 ]</td><td>FILE1이 FILE2보다 새로운 파일(최근의 파일)임</td></tr>
<tr><td>[ FILE1 -ot FILE2 ]</td><td>FILE1이 FILE2보다 오래된 파일임</td></tr>
<tr><td>[ FILE1 -ef FILE2 ]</td><td>FILE1이 FILE2의 하드링크임(I-node 값이 같음)</td></tr>
</table>

![test](https://github.com/dee021/TIL/blob/linuxmaster/Linux/img/test.png)

- 참이면 0, 거짓이면 1


> <b>문자 디바이스</b><br>
> byte 단위로 데이터를 전송<br>
> I/O 전송속도가 다소 느릴수도 있으나, 어플리케이션 단에서 버퍼링을 제어하므로 성능에 차이가 있음 <br>
> 키보드, 마우스, 모니터, 프린터 등의 입출력장치가 해당

> <b>블록 디바이스</b><br>
> 보통 하드디스크나 CD/DVD, 플로피디스크 등 블록이나 섹터 등의 정해진 단위로 데이터를 전송<br>
> I/O 전송 속도 빠름<br>
> /dev/sha, /dev/hda, /dev/fdo 등

> <b>Named Pipe</b><br>
> 부모 프로세스나 자식 프로세스와 같이 연관된 프로세스간 통신을 지원하는 익명 파이프(pipe)의 확장<br>
> 파일 경로를 ID로 사용하며 연관 없는 서로 다른 프로세스들이 파이프의 이름만 알면 통신이 가능함

5. 조건문

<b>if 문</b>

```
if [조건1]
then
  실행문장1
elif [조건2]
then
  실행문장2
else
  실행문장n
fi
```

![ifTest](https://github.com/dee021/TIL/blob/linuxmaster/Linux/img/ifTest.png)

<b>case 문</b>

- 보통 맨 마지막에는 *)로 지정하여 default 명령 실행
```
case 문자열
in
  정규식1) 명령어1;;
  정규식2) 명령어2;;
  ...
  *) 명령어n
esac
```

<b>select 문</b>

- Korn Shell, Bash에만 존재
- in 다음에 나오는 값들이 항목으로 된 메뉴로 생성됨
  - 값 생략시 기본 값은 "$@"(스크립트에 전달된 인자)


```
select 변수 in 값1, 값2, ...
  do
    실행문장
  done
```

![selectTest](https://github.com/dee021/TIL/blob/linuxmaster/Linux/img/selectTest.png)

6. 반복문

<b>for 문</b>

```
for 변수 in 값1, 값2, ...
  do
    실행문장
  done
```

<b>while 문</b>

```
while 조건문
  do
    실행문장
  done
```

![whileTest](https://github.com/dee021/TIL/blob/linuxmaster/Linux/img/whileTest.png)

<b>until 문</b>

- while문과 동일하나 조건이 거짓일 동안 루프를 수행

```
until 조건문
  do
    실행문장
  done
```

7. 함수

```
함수이름()
{
  command
}

function 함수이름
{
  command
}
```

![funcTest](https://github.com/dee021/TIL/blob/linuxmaster/Linux/img/funcTest.png)

8. 패턴과 패턴비교

- 특정한 패턴(pattern)을 놓고 변수의 문자열 값이 일부분이라도 패턴과 일치하는지 검사
- 패턴에는 와일드카드 문자(*, , [ ]를 포함한 문자 세트)를 포함할 수 있음

<table>
<tr><td>${variable#pattern}</td><td>처음부터 pattern과 맞는 variable의 부분을 찾아 가장 작은 부분을 제거하고 나머지를 반환</td></tr>
<tr><td>${variable##pattern}</td><td>처음부터 pattern과 맞는 variable의 부분을 찾아 가장 큰 부분을 제거하고 나머지를 반환</td></tr>
<tr><td>${variable%pattern}</td><td>끝에서부터 pattern과 일치하는 variable의 최소 부분을 제거하고 나머지를 반환</td></tr>
<tr><td>${variable%%pattern}</td><td>끝에서부터 pattern과 일치하는 variable의 최대 부분을 제거하고 나머지를 반환</td></tr>
</table>

![ptnTest](https://github.com/dee021/TIL/blob/linuxmaster/Linux/img/ptnTest.png)

## 셸 프로그래밍 기타 사항
### exit code
- 리눅스에서 사용되는 종료 코드는 0~255까지 사용
- 0은 성공, 1 이상은 오류를 의미
- 명령 실행 후 $?를 이용해 확인 가능

주요 종료 코드
<table>
<tr><td>0</td><td>success</td></tr>
<tr><td>1</td><td>일반적인 오류(Catchall for general errors)</td></tr>
<tr><td>2</td><td>셸 내장 명령의 틀린 사용(Misuse of shell builtins)</td></tr>
<tr><td>126</td><td>파일이 실행 가능하지 않음(Command invoked cannot execute)</td></tr>
<tr><td>127</td><td>명령어를 찾을 수 없음(command not found)</td></tr>
<tr><td>128</td><td>종료할 때 잘못된 인수 적용(Invalid argument to exit)</td></tr>
<tr><td>128+n</td><td>치명적인 시그널 n 에러(Fatal error signal "n")</td></tr>
<tr><td>130</td><td>[Ctrl] + [c] 키 조합에 의한 종료(Script terminated by Control-C)</td></tr>
</table>

### 우선순위
1. alias
2. function, if, for와 같은 지정 키워드
3. 함수(function)
4. type이나 export 등과 같은 내장 명령어
5. 스크립트, 프로그램, PATH 환경 변수에 들어있는 디렉터리를 셸이 확인하여 실행

## 셸 프로그래밍 관련 명령어
#### read
- 임의의 값을 키보드로부터 입력받는 명령어
- 연속으로 값을 받을 수 있으며, 인자값은 차례대로 처리됨

```
read 변수명1 [변수명2, ....]
```

#### echo
- 해당 문자열을 표준 출력에 표시하고 행 바꿈
- 문자열이 없으면 행 바꿈만 실행

```
echo [option] [문자열]
```

주요 옵션
<table>
<tr><td>-n</td><td>출력 결과에서 행 바꿈을 하지 않음</td></tr>
<tr><td>-e</td><td>escape 문자를 사용할 수 있음</td></tr>
</table>

#### break
- for, while, until 등의 순환문에서 빠져나옴
- 숫자 지정 시 순환문의 n번째 루프에서 빠져나옴

```
break [n]
```

#### continue
- for, while, until 등의 순환문을 계속해서 실행
- 숫자 지정 시 순환문의 n번째 루프를 재개

```
continue [n]
```

#### exit

- 상태 코드 n을 반환하고 스크립트를 종료
- 상태 코드가 주어지지 않으면 가장 최근에 사용된 명령의 상태 코드를 사용

```
exit [n]
```

#### return

- 함수 정의에서 사용되며 함수가 종료될 때 상태 코드 n을 반환
- 상태 코드가 주어지지 않으면 가장 최근에 사용된 명령의 상태 코드를 사용

```
return [n]
```

#### function

- 함수를 정의
- 위치 매개 변수($1, $2, ..., ${11}, ...)를 명령어 부분에서 사용 가능함

```
function 함수명
{
  명령어
}
```

#### getopts

- 명령행 인자를 처리해 올바른지 점검
- 주로 셸 스크립트 순환문에서 사용되며 명령행 옵션이 표준 형식에 맞는지를 확인
- 문자열에는 옵션을 나타내는 문자들이 포함됨(single character option 지원)
- 스크립트가 실행될 때 getopts에 의해 처리함
- 적합한 옵션들은 하나씩 처리되어 지정된 변수명에 저장됨

```
getopts 문자열 변수명
```

#### set

- 셸에서 보통 변수와 값을 선언할 때 주로 사용
  - bash에서는 생략 가능하며, c-shell에서는 반드시 사용
- 특별한 옵션 없이 입력 시 셸 변수와 값 출력
- 셸에서 매개인자(argument)를 설정하고 다양한 셸 환경 설정 시에도 사용

```
set [option] [문자열]
```

<table>
<tr><td>-f</td><td>셸에서 *, ?와 같은 와일드카드 문자를 이용한 확장을 없앰<br>-o noglob와 같음</td></tr>
<tr><td>-C</td><td>리다이렉션 기호인 >를 이용한 파일의 덮어쓰기를 제한<br>-o noclobber와 같음</td></tr>
<tr><td>-o</td><td>bash의 환경 설정 관련 값을 확인하고 설정하는 옵션<br>noglob, noclobber 등이 있음</td></tr>
</table>

![setEx](https://github.com/dee021/TIL/blob/linuxmaster/Linux/img/setEx.png)


#### shift

- 위치 매개 변수를 이동할 때 사용하는 명령
- 지정 숫자 만큼 위치 매개 변수를 이동하며, 숫자 생략 시 1만큼 이동

```
shift [n]
```

#### source

- 지정한 텍스트 파일의 내용을 읽고 실행
- 콤마(.)와 같은 기능

```
source 파일 [인수]
. 파일 [인수]
```

#### eval
- 인자의 값을 구하는데 사용
- 만약 값이 실행 명령어와 같은 문자열인 경우 해당 명령을 실행

#### expr
- 인자로 사용되는 표현식의 값을 구할 때 사용하며 산술연산이 가능
- 셸 상태에서 * 등의 연산자 사용 시 \를 붙여 사용하고, 값과 연산자 사이는 반드시 한 칸 띄어씀

```
expr 값 연산자 값
```

#### printf
- 형식화된 출력을 할 때 사용
- C 언어의 printf()와 유사
- 최근에 등장한 shell에서만 사용 가능

```
printf "format string" parameter1 parameter2
```

주요 format string

- %d : 십진수 출력
- %c : 한 문자 출력
- %s : 문장 출력
- %% : % 문자 출력

사용 예
```
$ printf "%s\n" shell
shell
```

#### true
- 셸 상에서 참의 의미를 가지는 0을 반환
- 셸 스크립트 상에서 무한 루프를 만들 때 사용
- Bourne shell에서 콜론 문자(:)로 같은 효과를 얻을 수 있음

#### type
- 주어진 인자가 셸 스크립트, 함수, 앨리어스인지를 검사하여 출력
- 함수이면 정의된 내용까지 모두 출력하고 명령어도 검사 가능

```
type [option] 인자
```

주요 옵션
<table>
<tr><td>-all</td><td>지정한 인자 이름으로 정의된 모든 정보를 출력</td></tr>
<tr><td>-path</td><td>실행 파일이나 셸 스크립트에 한정하여 관련 정보를 출력</td></tr>
<tr><td>-type</td><td>인자 이름과 일치하는 키워드 하나만 출력</td></tr>
</table>

![type](https://github.com/dee021/TIL/blob/linuxmaster/Linux/img/type.png)



