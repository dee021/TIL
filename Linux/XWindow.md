# X 윈도
<b>XFree86</b> : IBM 호환 시스템을 사용하는 유닉스 계열 운영체제를 위한 X 윈도 프로젝트
- 2004년 초까지 가장 선도적인 X 윈도 시스템이었으나, GPL 라이선스와 호환되지 않으면서 X.org로 변경됨

<b>X.org</b> : X.org Foundation에서 관리되는 X 서버 패키지
- 현재 리눅스를 비롯하여 유닉스의 대부분이 이 프로젝트 기반의 X 윈도 시스템을 사용하고 있으며, freedesktop.org와 함께 지속적인 발전 중

<b>X 윈도</b>

1. 특징
  - 네트워크 프로토콜에 기반한 그래픽 사용자 인터페이스 환경
  - 디스플레이 장치에 의존적이지 않고 서로 다른 이 기종을 함께 사용할 수 있음

2. 구조

클라이언트/서버 구조로, 통신을 위해 X Protocol을 사용
  - X 서버 : 그래픽하게 보여주는 하나의 시스템
  - X 클라이언트 : X 서버 위에 동작하는 일종의 응용 프로그램

네트워크 기반 구조
  - 로컬의 사용자가 키보드나 마우스로 입력하면 TCP 포트 6000번을 통해 전달
  - 로컬 시스템뿐만 아니라 원격의 호스트 간 통신이 가능

3. Xlib

  - C언어로 구현된 클라이언트 라이브러리
  - X 서버와 대화를 해주는 역할
  - 저수준의 인터페이스로 키보드나 마우스에 대한 반응 등의 단순한 기능만 가지고 있음

Xlib의 기능을 포함하는 고수준의 라이브러리 : Xt, Xaw, Motif, FLTK, GTK+, Qt, Tk, SDL 등
- XCB(X protocol C-language Binding) : Xlib 대체 라이브러리로 향상된 쓰레딩 기능을 지원하고 확장성이 뛰어남


## X 윈도 설치와 실행
<b>설치</b>

```
관련 패키지 그룹 검색
yum group list

설치 가능한 데스크톱 목록 확인 후 설치
yum group install 'GNOME Desktop'
yum group install 'KDE Plasma Workspaces'
yum group install 'Server with GUI'
```

<b>실행</b>
```
부팅 모드 확인
systemctl get_defualt

부팅 모드 설정
systemctl set_default TARGET_NAME
```

![bootingMode](https://github.com/dee021/TIL/blob/linuxmaster/Linux/img/bootingMode.png)

> 부팅 모드가 multi-user.target으로 설정된 상태에서 X 윈도를 실행시키려면 
> 사용자 계정으로 로그인 후 'startx' 명령을 실행한다.

## X 윈도 활용
1. xhost : X 서버에 접근할 수 있는 클라이언트를 지정하거나 해제하는 명령
- 호스트 기반 인증
```
xhost [+ | -] [IP 주소 or 도메인명]
```

예시
```
xhost
현재 설정된 접근 목록 출력

xhost 192.168.12.22
192.168.12.22의 접속을 허가

xshot -
모든 클라이언트의 접속을 차단
```

2. DISPLAY
X 클라이언트 프로그램이 실행될 때 표시되는 창이 설정되어 있는 환경 변수
- X 클라이언트를 원격지로 전송하기 위해 환경 변수 DISPLAY의 값을 원격지의 주소로 변경

예시
```
export DISPLAY="203.247.51.100:0"
X 클라이언트 프로그램을 203.247.51.100으로 전송
```

3. xauth
- 사용자 기반 인증
- X 윈도를 실행시키면 생성되는 $HOME/.Xauthority 파일의 `MIT-MAGIC-COOKIE-1` 키 값으로 사용자를 인증


## 데스크톱 환경(Desktop Environment; DE)
데스크톱 환경 : GUI 환경을 이용하기 위해 사용자에게 제공되는 인터페이스 스타일
- Window Manager를 포함하여 파일 관리자, 아이콘, 창, 도구 모음, 폴더 배경화면 데스크톱 위젯 제공
- 리눅스에서 사용하는 대표 DE : GNOME, KDE, Xfce, LXDE 등

1. KDE(K Desktop Environment)
- 마티아스 에트리히가 Qt 라이브러리를 기반으로 만들기 시작
- 리눅스뿐만 아니라 FreeBSD, Solaris, Microsoft Windows, OS X 등도 지원

2. GNOME(GNU Network Object Model Environment)
- GPL 라이선스를 따르지 않는 Qt 라이브러리 기반의 KDE의 대안으로 시작
- GNU 프로젝트로 데스크톱 부분과 라이브러리는 LPGL을, 응용 프로그램은 GPL을 따름
- GTK+ 라이브러리 기반
- GNOME 초기 버전 : nautilus 파일 관리자를 이용하여 단순히 사용하는 면에 초점
- GNOME 2 이후 : metacity 윈도 매니저를 사용하고 윈도우, 아이콘, 파일 등의 메뉴를 이용
- GNOME 3 이후 : Mutter(GNOME Shell) 윈도 매니저 사용

## 윈도 매니저(Window Manager)
윈도 매니저 : X 윈도 환경에서 window의 배치와 표현을 담당하는 시스템 소프트웨어
- 창 열기/닫기, 최소화/최대화, 이동, 크기 조정, 태스크 바 등 다양한 유틸리티 제공

윈도 매니저 종류
- GNOME : Mutter, metacity
- KDE 환경 : KWin, KWM
- 그 외 Window Maker, Enlightenment 등
