# 장치 관리
# 장치의 설치 및 관리
## Module
- 리눅스에서의 커널 모듈은 필요 시 커널 이미지에 합류하고 필요하지 않을 때에는 모듈 형태로 별도 존재
- 리눅스 커널 모듈은 C 컴파일러로 만들어진 오브젝트 파일로서 '*.ko' 형태로 생성됨
- 각 시스템에서 가능한 모듈은 '/lib/modules/커널버전/kernel' 디렉터리 안에 생성되어 있음

### 모듈 관련 명령어
#### lsmod
- 리눅스 커널에 적재된 모듈 정보를 출력하는 명령
- 모듈명, 크기, 다른 모듈에서 사용 중인 수, 사용 중인 모듈 순으로 출력

```
lsmod
```

#### insmod
- 커널에 모듈을 적재하는 명령
- 해당 모듈을 자동으로 검색되고 삽입됨
- <b>해당 모듈 파일이 존재하는 디렉터리에 이동해야만 함</b>
- 의존성 있는 모듈인 경우 적재 불가

```
insmod 모듈파일명
```

#### rmmod
- 커널에서 모듈을 제거하는 명령
- 다른 모듈에 의해 사용 중인 모듈은 제거 불가

```
rmmod 모듈
```

#### modprobe
- 리눅스 커널에 모듈을 적재하거나 제거하는 명령
- 최상위 모듈 디렉터리에 존재하는 modules.dep 파일을 참고해 모듈간의 의존성 해결
  - 단일모듈, 의존성이 있는 여러 모듈, 특정 디렉터리의 모든 모듈들 적재 가능
  - 다른 모듈에 의존하고 있을 경우 필요한 모듈을 먼저 적재
- '기호=값' 형식을 이용해 매개 변수 전달 가능

```
modprobe [option] 모듈 [기호=값]
```

주요 옵션
<table>
<tr><td>-r</td><td>모듈을 제거할 때 사용하는 옵션(--remove)<br>의존성이 있는 모듈들을 찾아서 사용되지 않으면 자동으로 제거</td></tr>
<tr><td>-c</td><td>모듈 관련 환경 설정 파일의 내용을 전부 출력(--showconfig)</td></tr>
</table>

#### modinfo
- 모듈 파일에 대한 정보를 출력하는 명령

```
modinfo [option] 모듈파일명
```

#### depmod
- 커널 모듈 간의 의존성을 관리하는 명령
- '/lib/modules/커널버전/modules.dep' 파일과 맵 파일을 생성

```
depmod [option] [파일명]
```

주요 옵션

<table>
<tr><td>-a</td><td>modules.dep와 맵 파일을 새롭게 생성(--all)</td></tr>
<tr><td>-A</td><td>새로 추가된 모듈이 있는 경우에만 갱신(--quick)</td></tr>
</table>

### 모듈 관련 파일
<b>모듈 관련 설정 파일</b><br>
- 리눅스 커널 2.4 버전 : 부팅 시 모듈을 커널에 적재할 때 /etc/modprobe.conf 파일에 등록하여 사용
- 리눅스 커널 2.6 버전 이후 : /etc/modprobe.d 디렉터리 안의 '*.conf' 파일을 인식
- CentOS 7 버전 이후 : /etc/modprobe.d 및 /lib/modprobe.d 디렉터리 안의 '*.conf' 파일을 인식

![modprobe](https://github.com/dee021/TIL/blob/linuxmaster/Linux/img/modprobe.png)

주요 항목
<table>
<tr><td>alias</td><td>특정 모듈에 별칭을 부여할 때 사용</td></tr>
<tr><td>options</td><td>특정 모듈에 특정한 값을 전달할 때 사용</td></tr>
<tr><td>install</td><td>모듈을 적재할 때 특정 명령을 지정할 때 사용<br>보통 첫 번째 모듈 적재 명령이 실패할 경우를 대비하여 두 번째 명령도 같이 지정할 때 사용</td></tr>
<tr><td>remove</td><td>특정 모듈을 제거할 때 여러 상황을 지정하여 사용</td></tr>
<tr><td>blacklist</td><td>특정 모듈 사용을 제한할 때 사용</td></tr>
</table>


<b>모듈 의존성 파일</b><br>
/lib/modules/커널버전/modules.dep

- 모듈 간의 의존성을 기록한 파일
- 각 라인마다 모듈 파일명이 기록되고, 콜론 이후 의존성 있는 모듈 파일명이 차례로 나열
- 모듈 간의 의존성이 변경되면 해당 파일 내용이 변경되어야 modprobe 명령이 정상적으로 작동
- `depmod` 명령을 통해 해당 파일을 갱신하고 관리

## Kernel
- 리눅스에서 커널은 시스템 자원을 소유하고 관리하는 역할을 담당
  - 하드웨어, 메모리, 프로세스 스케줄링 담당
- `uname -r` 명령으로 커널 버전 확인 가능

### 커널 컴파일
- 커널 소스를 다운로드하여 사용하는 시스템에 최적화된 커널을 만드는 과정
- C 컴파일러인 gcc, 어셈블러, 관련 라이브러리(ncurss, ncurses-devel 등), 링커, make 유틸리티 등의 개발 도구가 설치되어야 있어야 함

커널 컴파일 과정
> /usr/src/kernels 디렉터리에 리눅스 커널 버전 소스를 다운
> 
> → 커널 소스 파일 압축 해제
>
> → 커널 소스의 설정값 초기화 `make mrproper`
>
> → 커널 컴파일 옵션 설정 작업 `make menuconfig`
>> <b>설정 규칙</b></br> 
>> y(yes) : 선택하여 커널에 포함</br>
>> m(module) : 모듈로 생성</br>
>> n(no) : 선택하지 않음
>
> → 커널 이미지 파일 생성 작업 `make bzImage`
>
> → 커널 모듈 생성을 위한 컴파일 작업 `make modules`
>
> → 커널 모듈 설치 작업 `make moudles_install`
>
> → 커널 모듈 파일 복사, grub 환경설정 파일 수정 `make install`
>
> → 새로운 커널 사용을 위한 커널 재부팅

`make help` 명령으로 커널 컴파일 관련 도움말 확인 가능

Cleaning Targets
<table>
<thead><td>주요 도구</td><td>설명</td></thead>
<tr><td>make clean</td><td>configure 작업을 수행하여 생성된 오브젝트 파일을 제거하고 다시 configure 할 때 사용<br>환경 설정한 내용이 저장되는 .config 등은 지우지 못 함</td></tr>
<tr><td>make mrproper</td><td>configure 작업을 통해 생성된 오브젝트 파일과 config 파일, 다양한 백업 파일 등을 제거할 때 사용</td></tr>
<tr><td>make distclean</td><td>mrproper 작업과 더불어 편집된 백업 및 패치 파일도 모두 제거</td></tr>
</table>

커널 컴파일 주요 도구
<table>
<thead><td>주요 도구</td><td>설명</td></thead>
<tr><td>make config</td><td>텍스트 기반의 설정 도구로 터미널 환경에서 y, m, n으로 설정한다.
</td></tr>
<tr><td>make menuconfig</td><td>텍스트 기반의 컬러 메뉴를 제공하고, 커서를 이용해서 이동이 가능하다. 가장 보편적으로 사용하는 도구</td></tr>
<tr><td>make nconfig</td><td>텍스트 기반의 컬러 메뉴를 제공하고, 커서와 [F1]~[F9]까지의 기능키를 제공하는 도구이다.</td></tr>
<tr><td>make xconfig</td><td>X 윈도 환경의 Qt 기반의 설정 도구이다.</td></tr>
<tr><td>make gconfig</td><td>X 윈도 환경의 Gtk+ 기반의 설정 도구이다.</td></tr>
</table>

# 주변 장치 관리
## 주변 장치 설정
### 디스크 확장

디스크 확장 작업 과정

> 디스크 장착
> 
> → 디스크 인식 여부 확인 `fdisk -l`
>
> → 파티션 작업 `fdisk 장치명`
>
> → 시스템 재부팅 `partprobe` 또는 `reboot`
>
> → 파일 시스템 생성 `mkfs.fs_type 디바이스명`
>
> → 디렉터리(마운트 포인터) 생성 `mkdir /path`
>
> → 마운트 작업 `mount -t fs_type 디바이스명 /path`
>
> → 마운트 및 용량 확인 `mount; df -h`
>
> → 부팅 시에 자동 마운트 되도록 /etc/fstab 파일에 등록

### 프린터

리눅스 배포판에서 사용하는 프린팅 시스템 : LPRng, CUPS

<b>LPRng</b>

- Line Printer deamon protocol을 사용하여 프린터 스풀링(Printer spooling)과 네트워크 프린터 서버를 ㅣ줜
- BSD 계열의 프린터 명령(lpr, lpq, lprm 등)과 System V 계열 명령(lp, lpstat, cancel 등) 지원

<b>CUPS</b>

- 유닉스 계열 운영체제의 시스템을 프린터 서버로 사용할 수 있게 함
- HTTP 기반의 IPP(Internet Printing protocol) 사용
- SMB 프로토콜의 부분적 지원 
- 사용자 및 호스트 기반의 인증 제공

CUPS 관련 파일

- /etc/cups/cupsd.conf : CUPS 프린터 데몬의 혼경 설정 파일
  - 기본 문법이 아파치의 httpd.conf와 유사
- /etc/cups/printers.conf : 프린터 큐 관련 환경 설정 파일
  - lpadmin 명령을 이용하거나 웹을 통해 제어 가능
- /etc/cups/classes.conf : CUPS 프린터 데몬의 클래스 설정 파일

프린터 설정
- CentOS 6 버전 이후 : `system-config-printer` 명령 이용

#### 프린터 관련 명령어
#### lpr
- 프린터 작업을 요청하는 명령

```
lpr [option] [파일명]
```

주요 옵션
<table>
<tr><td>-# 값</td><td>인쇄할 매수(1-100)를 지정하는 옵션</td></tr>
<tr><td>-m</td><td>작업이 완료되면 관련 정보를 E-mail로 전송</td></tr>
<tr><td>-p 프린터명</td><td>기본 설정된 프린터 이외에 다른 프린터를 지정할 때 사용</td></tr>
<tr><td>-T</td><td>타이틀 페이지에 들어갈 타이틀명을 설정</td></tr>
<tr><td>-r</td><td>출력한 뒤에 지정한 파일을 삭제</td></tr>
<tr><td>-l</td><td>필터링 없이 직접 보냄</td></tr>
</table>

#### lpq
- 프린터 큐에 있는 작업의 목록 출력

```
lpq [option]
```

주요 옵션
<table>
<tr><td>-a</td><td>설정되어 있는 모든 프린터의 작업 정보 출력</td></tr>
<tr><td>-l</td><td>출력 결과를 자세하게(long format) 출력</td></tr>
<tr><td>-P 프린터명</td><td>특정 프린터를 지정할 때 사용</td></tr>
</table>

#### lprm
- 프린터 큐에 대기 중인 작업을 삭제하는 명령
- 취소할 프린트 작업의 번호를 입력하지 않으면 가장 마지막에 요청한 작업 취소

```
lprm [option] [파일명]
```

주요 옵션
<table>
<tr><td>-</td><td>프린터 큐에 있는 모든 작업 취소</td></tr>
<tr><td>-U 사용자명</td><td>지정한 사용자의 인쇄 작업을 취소</td></tr>
<tr><td>-P 프린터명</td><td>특정 프린터를 지정할 때 사용</td></tr>
<tr><td>-h 서버[:port]</td><td>지정한 서버의 인쇄 작업 취소</td></tr>
</table>

#### lpc
- Line Printer Control Program
- 프린터나 프린터 큐를 제어할 때 사용
- lpc 명령 실행 후 지정 명령어를 사용

주요 명령
<table>
<tr><td>disable</td><td>새로운 프린트 작업을 할 수 없도록 한다.</td></tr>
<tr><td>enable</td><td>프린트 작업을 가능하게 한다.</td></tr>
<tr><td>down</td><td>지정한 프린터를 사용할 수 없게 한다.</td></tr>
<tr><td>up</td><td>모든 환경을 활성화시키고, 관련 데몬을 새롭게 구동한다.</td></tr>
<tr><td>help, ?</td><td>사용 가능한 명령을 출력한다.</td></tr>
<tr><td>quit, exit</td><td>lpc 명령을 종료한다.</td></tr>
</table>

#### lp
- 인쇄 작업을 요청하는 명령

```
lp [option] [파일명]
```

주요 옵션
<table>
<tr><td>-d</td><td>다른 프린터를 지정</td></tr>
<tr><td>-n 값</td><td>인쇄할 매수 지정(1-100)</td></tr>
</table>

#### lpstat
- 프린터 큐의 상태 출력

```
lpstat [option]
```

주요 옵션
<table>
<tr><td>-p</td><td>프린터의 인쇄 가능 여부 출력</td></tr>
<tr><td>-t</td><td>프린터의 상태 정보 출력</td></tr>
<tr><td>-a</td><td>받아들이는 요청들의 상태 출력</td></tr>
</table>

#### cancel
- 프린트 작업을 취소하는 명령
- lpstat으로 Request-ID를 확인해야 함

```
cancel Rquest-ID
```

주요 옵션
<table>
<tr><td>-a</td><td>모든 인쇄 작업 취소</td></tr>
</table>

### 사운드 카드
#### 고급 리눅스 사운드 아키텍처(Advanced Linux Sound Arcitecture; ALSA)
- 사운드 카드용 장치 드라이버를 제공하기 위한 리눅스 커널의 요소
- 사운드 카드를 자동으로 구성하게 하고, 다수의 사운드 장치를 관리하는 것이 목적
- GPL 및 LGPL 라이선스 기반으로 배포되고 있음
- OSS(Open Sound System)의 지원을 받아 하드웨어 기반 미디 합성, 다중 채널 하드웨어 믹싱, 전이중 통신, 다중 프로세서와의 조화, 스레드 안전 장치 드라이버 등의 기능을 지원

#### 오픈 사운드 시스템(Open Sound System; OSS)
- 리눅스 및 유닉스 계열 운영체제에서 사운드를 만들고 캡처하는 인터페이스
- 표준 유닉스 장치 시스템 콜(POSIX read, write, ioctl 등)에 기반을 두고 있음

### 사운드 관련 명령어
#### alsactl
- ALSA 사운드 카드를 제어하는 명령어

```
alsactl [option] [command]
```

주요 옵션
<table>
<tr><td>-d</td><td>디버그 모드 사용(--debug)</td></tr>
<tr><td>-f</td><td>환경 설정 파일 선택<br>기본 파일은 /etc/asound.state</td></tr>
</table>

주요 command
<table>
<tr><td>store</td><td>사운드 카드에 대한 정보를 환경 설정 파일에 저장한다.</td></tr>
<tr><td>restore</td><td>환경 설정 파일로부터 선택된 사운드카드 정보를 다시 읽어 들인다.</td></tr>
<tr><td>init</td><td>사운드 장치를 초기화한다.</td></tr>
</table>

#### alsamixer
- 커서(ncurses) 라이브러리 기반의 ALSA 사운드 카드 오디오 믹서 프로그램

```
alsamixer
```

#### cdparanoia
- 오디오 CD에서 음악 파일을 추출할 때 사용하는 명령

```
cdparanoia [option]
```

주요 옵션
<table>
<tr><td>-w</td><td>wav 파일로 추출(기본 옵션)</td></tr>
<tr><td>-a</td><td>Apple AIFF-C 포맷으로 추출</td></tr>
<tr><td>-B</td><td>모든 트랙의 음악을 Cdda2wav 스타일로 추출<br>'track#.' 형태로 파일 이름 생성</td></tr>
</table>

사용 예
```
# cdparanoia -B
디스크 전체에서 각 트랙별로 추출해서 파일로 생성

# cdparanoia "1[:30.12]-1[1:10]"
트랙 1번의 시간 0:30.12 부터 1:10.00 까지 추출

# cdparanoia -- "-3"
트랙 3번부터 추출
```

### 스캐너
#### SANE(Scanner Access Now Easy)
- 이미지 관련 하드웨어(평판 스캐너, 핸드 스캐너, 비디오 캠 등)를 사용할 수 있도록 해주는 API
- GPL 라이선스
- 리눅스 및 유닉스 계열뿐만 아니라 os/2, Microsoft Windows 지원
- 패키지
  - sane-backends : 스캐너 관련 드라이버가 들어 있는 패키지
  - sane-frontends : 사용자 관련 명령이 들어있는 패키지

#### XSANE(X based interface for the SANE)
- SANE 스캐너 인터페이스를 이용하여 X-Window 기반으로 만든 프로그램
- SANE 관련 라이브러리를 이용하여 스캐너, 디지털 카메라, 디지털 캠 등 다양한 장치에서 사용 가능
- GTK+ 라이브러리로 만들어졌으며, 스캔 작업뿐만 아니라 캡처된 이미지도 수정 작업이 가능
- X-Window 터미널에서 xsane이라고 입력하면 실행 가능

### 스캐너 관련 명령어
#### sane-find-scanner
- USB 및 SCSI 스캐너와 관련 장치 파일을 찾아주는 명령
- 보통 SCSI 스캐너는 /dev/sg0, /dev/scanner로 인식하고 USB 스캐너는 /dev/usb/scanner, /dev/usbcsanner 등으로 사용

```
sane-find-scanner [option] [장치파일명]
```

주요 옵션
<table>
<tr><td>-q</td><td>스캐너 장치만 출력</td></tr>
<tr><td>-v</td><td>자세한 정보 출력</td></tr>
<tr><td>-p</td><td>병렬(Parallel) 포트에 연결된 스캐너만 찾음</td></tr>
</table>

#### scanimage
- 이미지를 스캔하는 명령

```
scanimage [option]
```

주요 옵션
<table>
<tr><td>-h</td><td>사용 가능한 옵션 목록 출력</td></tr>
<tr><td>-d</td><td>SANE의 장치 파일명을 적는 옵션(--device-name)</td></tr>
<tr><td>--format</td><td>이미지 형식을 지정하는 옵션<br>pnm(기본)과 tiff를 지정할 수 있음</td></tr>
<tr><td>-L</td><td>사용 가능한 스캐너 장치 목록 출력(--list-device)</td></tr>
</table>

> <b>lspci</b><br>
> 설치된 PCI 관련 장치의 목록을 확인할 수 있는 명령

## LVM
- CentOS 7 버전에서 LVM은 lvm2라는 패키지 이름으로 설치 되어 있음
- 리눅스 설치 시 자동 파티션을 선택하면 LVM 기반 파티션이 할당됨
- LVM 구성은 관련 명령어를 이용하여 PV, VG, LV 순으로 순차적으로 구성

### LVM 관련 명령어
#### pvscan
- 생성된 물리적 볼륨(PV; Physical Volume)의 정보를 출력하는 명령

```
pvscan
```

#### vgscan
- 생성된 볼륨 그룹(VG; Volume Group)의 정보를 출력하는 명령

```
vgscan
```

#### pvcreate
- 물리적 볼륨을 생성하는 명령
- fdisk를 이용하여 파티션을 분할하고 LVM 속성으로 지정한 뒤 해당 파티션을 PV로 만들 때 사용하는 명령

```
pvcreate [option] 디바이스명
```

주요 옵션
<table>
<tr><td>-f</td><td>강제로 생성할 때 사용하는 옵션(--force)<br>응급상황에서는 -ff 사용</td></tr>
</table>

#### vgcreate
- 볼륨 그룹을 생성하는 명령
- 생성된 PV를 지정한 볼륨 그룹에 포함시키면서 생성

```
vgcreate [option] VG명 PV명 PV명
```

주요 옵션
<table>
<tr><td>-s 사이즈</td><td>PE(Physical Extends)의 크기 지정<br>사용 가능한 단위 : [bBsSkKmMgGtTpPeE]</td></tr>
</table>

#### vgextend
- 미리 생성된 VG에 PV를 추가하여 VG를 확장할 때 사용

```
vgextend VG명 PV명 [PV명]
```

#### vgreduce
- VG에 있는 PV를 삭제하는 명령
- 해당 PV의 PE가 할당되어 있으면 제거되지 않음

```
vgreduce [option] 볼륨그룹명 PV명
```

주요 옵션
<table>
<tr><td>-a</td><td>특별한 PV명을 지정하지 않는 경우 비어있는 PV를 모두 제거(--all)</td></tr>
</table>

#### vgdisplay
- 볼륨 그룹의 속성과 정보를 보여주는 명령

```
vgdisplay [option] [볼륨그룹명]
```

주요 옵션
<table>
<tr><td>-v</td><td>관련 정보를 자세히 출력(--verbose)<br> 볼륨 그룹 이외에도 Logical Volume과 Physical Volume 정보도 함께 출력</td></tr>
</table>

#### lvcreate
- 볼륨 그룹 안에 LV을 생성하는 명령

```
lvcreate 옵션 볼륨그룹명
```

주요 옵션
<table>
<tr><td>-L</td><td>LV의 크기를 지정하는 옵션<br>사용 가능한 단위 : [bBsSkKmMgGtTpPeE]</td></tr>
<tr><td>-l</td><td>LV의 크기를 PE 개수로 지정하는 옵션<br>일반적으로 1PE에 4MB</td></tr>
<tr><td>-n</td><td>LV의 이름을 지정하는 옵션</td></tr>
<tr><td>-i</td><td>RAID-0과 같이 stripe 사용 시 개수를 지정하는 옵션</td></tr>
<tr><td>-I stripe_size</td><td>스트라이프되는 크기를 지정하는 옵션<br>기본 단위는 kilobyte이고 크기는 반드시 2의 배수(2^n : n 값은 2부터 9)로 지정</td></tr>
<tr><td>-s</td><td>유동적인 디렉터리 백업을 위해서 snapshot LV를 생성할 때 사용</td></tr>
</table>

#### lvscan
- 전체 디스크에 있는 LV을 찾아줌

```
lvscan [option]
```

주요 옵션
<table>
<tr><td>-v</td><td>관련 정보를 자세히 출력(--verbose)</td></tr>
</table>

#### lvdisplay
- LV의 속성 정보를 출력하는 명령

```
lvdisplay [option] [LV명]
```

주요 옵션
<table>
<tr><td>-v</td><td>관련 정보를 자세히 출력(--verbose)</td></tr>
</table>

#### lvextend
- LV의 용량을 확장하는 명령

```
lvextend 옵션 [볼륨그룹명] LV명
```

주요 옵션
<table>
<tr><td>-L +사이즈</td><td>확장할 LV의 크기를 지정하는 옵션(--size)<br>기본 단위는 MB이며, 사용 가능한 단위는 [bBsSkKmMgGtTpPeE]</td></tr>
<tr><td>-l +사이즈</td><td>확장할 LV의 크기를 PE 개수로 지정하는 옵션</td></tr>
</table>

#### lvreduce
- LV의 용량을 줄이는 명령
- 데이터가 파괴되므로 주의
  - 파괴된 경우 다시 mkfs하여 사용해야 함

```
lvreduce 옵션 [볼륨그룹명] LV명
```

주요 옵션
<table>
<tr><td>-L -사이즈</td><td>지정한 크기만큼 LV의 크기를 줄임(--size)<br>기본 단위는 MB이며, 사용 가능한 단위는 [bBsSkKmMgGtTpPeE]</td></tr>
<tr><td>-l -사이즈</td><td>지정한 PE 개수에 해당하는 만큼의 용량을 줄임</td></tr>
</table>

#### lvrename
- LV의 이름을 변경하는 명령

```
lvrename old_LV_절대경로 new_LV_절대경로
lvrename VG명 old_LV명 new_LV명
```

#### lvremove
- LV를 제거하는 명령
- 사용 중인 경우 제거하지 않으므로 미리 umount 해야 함

```
lvremove 논리적볼륨_절대경로
```

#### vgchange
- VG의 속성을 변경하는 명령
- VG 사용 가능 여부, 최대 LV 개수 등 변경 가능

```
vgchange 옵션 볼륨그룹명
```

주요 옵션
<table>
<tr><td>-a y|n</td><td>지정한 VG의 사용 여부 지정(--activate)</td></tr>
<tr><td>-l 개수</td><td>해당 VG에 생성할 수 있는 최대 LV 수 지정(--logicalvolume)</td></tr>
</table>

#### vgremove
- VG 제거 명령
- 해당 VG가 사용 중이면 제거가 되지 않으므로 umount 후에 속해있는 LV를 전부 제거해야 함

```
vgremove VG명
```

#### pvmove
- 물리적 확장인 PE를 이동시키는 명령

```
pvmove [option] 장치명 [장치명]
```

주요 옵션
<table>
<tr><td>-v</td><td>관련 정보를 자세히 출력(--verbose)</td></tr>
</table>

#### fsadm
- 특정 장치의 파일 시스템의 크기를 조정하고 점검하는 명령
- ext2, ext3, ext4, ReiserFS, XFS 파일 시스템 지원

```
fsadm [option] check 장치명
fsadm [option] resize 장치명 [new_size[BKMGTPE]]
```

주요 옵션
<table>
<tr><td>-v</td><td>관련 정보를 자세히 출력(--verbose)</td></tr>
<tr><td>-f</td><td>몇 가자ㅣ 검사를 무시하고 넘어갈 때 사용(--force)</td></tr>
<tr><td>-y</td><td>명령 실행 시 나오는 질의에 'yes'라고 답함(--yes)</td></tr>
</table>

### LVM 확장

LVM의 가장 큰 특징은 사용 중에도 데이터 이동 없이 용량 증가가 가능하다는 점

사용 중인 LV의 확장 절차

1. 새롭게 추가된 하드 디스크를 PV로 생성
2. 생성된 PV를 VG에 포함시켜 VG를 확장
3. LV 확장
4. 파일 시스템의 크기 증량
5. 증성된 용량 확인

## RAID
- md(Multiple Device) : Linux Software RAID
- CentOS 7 버전에서는 mdadm 패키지를 이용하여 Software RAID를 구성
- RAID 생성 후 관련 정보는 /proc/mdstat에서 확인 가능


#### mdadm
- md 장치를 관리하는 명령

```
mdadm options <raiddevice> <component-devices>
```

주요 옵션
<table>
<tr><td>-C</td><td>RAID 장치를 생성하는 옵션(--create)</td></tr>
<tr><td>-l</td><td>-C 옵션과 함께 사용되며 RAID 레벨값을 지정(--level=)<br>
레벨값 : linear, raid0, 0, stripe, raid1, 1, mirror, raid4, 4, raid5, 5, raid6, 6, multipath, mp, faulty, container
</td></tr>
<tr><td>-n</td><td>-C 옵션과 함께 사용되며 구성할 디바이스의 개수를 지정(--raid-devices=)</td></tr>
<tr><td>-D</td><td>지정한 RAID의 자세한 정보를 출력(--detail)</td></tr>
<tr><td>-S</td><td>구성된 RAID의 배열을 비활성화하여 모든 자원 해제(--stop)</td></tr>
<tr><td>-Q</td><td>지정한 레이드 장치의 간단한 정보 출력(--query)</td></tr>
<tr><td>-f</td><td>특정 장치에 오류를 발생시킬 때 사용(--fail)</td></tr>
<tr><td>-a</td><td>작동중인 RAID에 디바이스를 추가할 때 사용(--add)</td></tr>
<tr><td>-r</td><td>RAID에서 디바이스 제거(--remove)</td></tr>
</table>

사용 예
```
# mdadm --detail --scan
작동중인 장치를 찾아 관련 정보 출력
```
