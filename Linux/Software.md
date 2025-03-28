# 소프트웨어 설치 및 관리
## 패키지를 통한 소프트웨어 설치
- 리눅스에 사용되는 프로그램은 C 언어로 된 소스 파일을 tar로 묶은 후에 압축하여 배포됨
- 소스로 제공되는 프로그램을 설치하기 위해 먼저 압축을 풀고 디렉터리 이동 후 configure, make, make install의 작업을 거쳐야 함
- 리눅스 배포판에서는 쉬운 프로그램의 설치, 삭제, 관리 등을 위해 독자적인 패키지 관리기법을 개발

배포판별 패키지 관리 기법
<table>
<thead><td>배포판</td><td>기본 패키지 관리 기법</td><td>온라인 패키지 관리 기법</td></thead>
<tr><td>레드햇 계열</td><td>RPM</td><td>YUM</td></tr>
<tr><td>데비안 계열</td><td>Dpkg</td><td>apt-get</td></tr>
<tr><td>수세 계열</td><td>YaST</td><td>zypper</td></tr>
</table>

### RPM(Red Hat Package Manager)

배포되는 rpm 파일 형식
```
패키지이름-버전-릴리즈.리눅스버전.아키텍처.rpm
```

rpm 파일 예
```
sendmail-8.12.8-6.ppc64.rpm 
패키지 이름 : sendmail
패키지 버전 : 8.12.8
패키지 릴리즈 : 6
패키지 아키텍처 : ppc64(PowerPC 64 bit용 패키지) 
```

rpm 명령의 사용법은 설치 및 갱신, 제거, 질의, 검증 모드와 소스 rpm(.src.rpm)의 리빌드 모드로 나눌 수 있음

```
rpm [option] [패키지_파일명]
```

#### 설치 및 갱신(install & upgrade) 모드

주요 옵션
<table>
<tr><td>-i</td><td>새로운 패키지를 설치할 때 사용(--install)<br>이전 버전의 같은 패키지가 있으면 설치 되지 않음</td></tr>
<tr><td>-U</td><td>기존 패키지를 새로운 버전의 패키지로 업그레이드할 때 사용(--upgrade)<br>설치된 패키지가 없을 시 -i 옵션과 같음</td></tr>
<tr><td>-F</td><td>이전 버전이 설치되어 있는 경우에만 설치(--freshen)</td></tr>
<tr><td>-v</td><td>메시지를 자세히 출력(--verbose)</td></tr>
<tr><td>-h</td><td>설치 상황을 '#' 기호 표시(--hash)</td></tr>
<tr><td>--force</td><td>강제로 설치<br>--oldpackages, --replacefiles, --replacepkgs와 같이 3개의 옵션을 포함

> --oldpackages : 새로운 패키지를 지우고 더 예전 패키지로 교체
>
> --replacefiles : 이미 설치된 다른 패키지의 파일을 덮어씀
>
> --replacepkgs : 패키지가 이미 설치되어 있어도 다시 설치
</td></tr>
<tr><td>--nodeps</td><td>의존성 관계를 무시하고 설치<br>의존성 관계에 있는 패키지가 존재하지 않아도 강제 설치</td></tr>
<tr><td>--vv</td><td>메시지를 아주 자세히 출력</td></tr>
<tr><td>--test</td><td>실제로 파일에 기록하는 작업을 제외한 대부분을 테스트할 때 사용<br>패키지가 제대로 설치되는지 테스트해 볼 경우 사용</td></tr>
<tr><td>--rebuilddb</td><td>rpm 데이터베이스 업데이트<br>특정한 패키지 설치 후 rpm 패키지를 검색했으나 나타나지 않을 때 사용</td></tr>
</table>

#### 제거(erase) 모드
`-e` 옵션을 붙여 명령한다.

주요 옵션
<table>
<tr><td>-e</td><td>설치된 패키지 삭제(--erase)<br>의존성을 갖는 패키지가 존재하는 경우 삭제되지 않음</td></tr>
<tr><td>--nodeps</td><td>의존성을 갖는 패키지가 존재하는 경우에도 설치된 패키지 삭제</td></tr>
<tr><td>--test</td><td>패키지 제거 테스트 수행</td></tr>
<tr><td>--allmatches</td><td>동일한 이름을 갖는 패키지가 중복 설치되어 있는 경우 모두 제거</td></tr>
</table>

#### 질의(query) 모드
`-q` 옵션을 붙여 명령한다.

주요 옵션
<table>
<tr><td>-q</td><td>질의 시 사용하는 옵션(--query)<br>패키지를 찾으면 패키지 이름과 버전만 표시</td></tr>
<tr><td>-i</td><td>설치된 패키지의 정보 출력(--info)<br>-p 옵션과 함께 사용하면 rpm 패키지 파일에 대한 정보 조회</td></tr>
<tr><td>-l</td><td>패키지에 설치한 모든 파일 정보를 출력(--list)</td></tr>
<tr><td>-a</td><td>시스템에 설치된 모든 패키지 목록 출력(--all)</td></tr>
<tr><td>-p 패키지_파일명</td><td>rpm 패키지 파일에 대한 정보 출력(--package)<br>패키지 파일의 정확한 이름을 입력해야 함</td></tr>
<tr><td>-f 파일명</td><td>지정한 파일을 설치한 패키지 이름 출력(--file)</td></tr>
<tr><td>-c</td><td>해당 패키지의 설정 파일이나 스크립트 파일 출력(--configfiles)</td></tr>
<tr><td>-d</td><td>해당 패키지의 문서 파일 출력(--docfiles)</td></tr>
<tr><td>-R</td><td>해당 패키지가 설치되거나 동작 시 필요한 패키지 목록 출력(--requires)</td></tr>
<tr><td>--changelog</td><td>특정 패키지의 바뀐 내역을 최근부터 연대순으로 출력</td></tr>
<tr><td>--scripts</td><td>설치 및 제거 관련 스크립트 출력</td></tr>
<tr><td>--filesbypkg</td><td>rpm 패키지가 많을 경우 목록으로 보이는 파일 앞에 패키지명을 붙임</td></tr>
<tr><td>--queryformat</td><td>질의 결과의 포맷 지정(--qf)</td></tr>
</table>

#### 검증(verify) 모드
- rpm 데이터베이스에 저장되어 있는 패키지의 메타데이터 정보를 이용하여 변경된 정보를 찾아내는 모드
- 파일의 크기, 허가권, 소유권, 파일 형식 등에 대한 변경 정보 출력
- `-V(--verify)` 옵션을 사용하여 명령

주요 옵션
<table>
<tr><td>-a</td><td>모든 패키지를 검사할 때 사용</td></tr>
</table>


![rpm-Va](https://github.com/dee021/TIL/blob/linuxmaster/Linux/img/rpmVa.png)


검증 코드
<table>
<tr><td>S</td><td>파일 크기 변경</td></tr>
<tr><td>M</td><td>파일 모드(Permission & File Type) 변경</td></tr>
<tr><td>5</td><td>메시지 다이제스트(Message Digest) 변경<br>보통 MD5 값 변경</td></tr>
<tr><td>D</td><td>장치 파일의 메이저 및 마이너 번호 불일치</td></tr>
<tr><td>L</td><td>링크 파일 경로 불일치</td></tr>
<tr><td>U</td><td>소유자 변경</td></tr>
<tr><td>G</td><td>그룹소유권 변경</td></tr>
<tr><td>T</td><td>수정 시간(Modify Time) 변경</td></tr>
<tr><td>P</td><td>권한(Capavility) 변경</td></tr>
<tr><td>.</td><td>테스트 통과</td></tr>
<tr><td>?</td><td>테스트를 수행하지 못 함(허가권 거부 등)</td></tr>
</table>

#### 리빌드(rebuild) 모드
- rpm 소스 파일인 .src.rpm 파일을 패키지 파일로 만드는 모드
- `rpmbuild` 명령어 사용

```
rpmbuild [option] 소스패키지
```

주요 옵션
<table>
<tr><td>--rebuild</td><td>소스 rpm 파일을 이용해서 rpm 패키지를 생성할 때 사용</td></tr>
</table>

### yum(Yellowdog Updater, Modified)
- rpm 기반 시스템에서 패키지를 손쉽게 설치해주고 자동으로 업데이트를 수행하는 명령행 기반의 유틸리티
- 소프트웨어 저장소(repository)에 관련 패키지들을 모아두고, 네트워크를 통해 의존성을 검사하여 설치 및 업데이트 등 수행

<b>yum 관련 파일 및 디렉터리</b>

- /etc/yum.conf : 환경 설정 파일
- /etc/yum.repos.d 디렉터리 : 설치 및 업데이트를 위한 저장소 관련 파일들 저장
  - CentOS-Base.repo : 네트워크 작업용 저장소 파일
  - CentOS-Media.repo : CD-ROM, DVD-ROM 작업용 저장소 파일

CentOS-Base.repo 주요 항목
<table>
<tr><td>[base]</td><td>yum 패키지 서버의 기본 경로를 설정하는 항목</td></tr>
<tr><td>[updates]</td><td>업데이트된 패키지를 위한 경로를 설정하는 항목</td></tr>
<tr><td>[extras]</td><td>유용하게 쓸 수 있는 추가 패키지 경로를 설정하는 항목</td></tr>
<tr><td>[centosplus]</td><td>존재하는 패키지들의 기능적 확장과 관련 있는 패키지 경로를 설정하는 항목</td></tr>
<tr><td>[contrib]</td><td>CentOS 사용자들에 의해 제장된 패키지 경로를 설정하는 항목</td></tr>
</table>

- /var/cache/yum/x86_64/7/base 디렉터리 : 작업 관련 정보
- /var/log/yum.log : yum 관련 작업 로그 파일


```
yum [option] [command] [패키지_파일명]
```

주요 옵션
<table>
<tr><td>-y</td><td>모든 질의에 yes라고 답(--assumeyes)</td></tr>
<tr><td>-v</td><td>자세한 정보 출력(--verbose)</td></tr>
</table>

주요 command
<table>
<tr><td>list [항목]</td><td>전체 패키지에 대한 정보 출력

출력
- 설치가 되어 있는 경우 installed
- 업데이트가 가능한 항목 updates

항목 : all(기본값), installed, updates 등
</td></tr>
<tr><td>info [패키지명]</td><td>패키지에 대한 정보 출력</td></tr>
<tr><td>check-update</td><td>업데이트가 필요한 패키지 출력<br> 

`yum list updates` 와 같음</td></tr>
<tr><td>update [패키지명]</td><td>패키지 업데이트</td></tr>
<tr><td>install [패키지명]</td><td>패키지 설치<br>의존성 관련 패키지도 자동 설치</td></tr>
<tr><td>search [문자열..]</td><td>문자열이 포함된 패키지 출력</td></tr>
<tr><td>remove/erase 패키지명</td><td>패키지 삭제</td></tr>
<tr><td>group list</td><td>패키지 그룹 정보 출력</td></tr>
<tr><td>group info 패키지그룹명</td><td>해당 패키지 그룹명과 관련된 패키지 정보 출력</td></tr>
<tr><td>group install/update/remove 패키지명</td><td>지정한 그룹의 패키지를 설치/업데이트/제거</td></tr>
<tr><td>whatprovides</td><td>특정한 파일이나 기능과 관련된 패키지 정보 검색</td></tr>
<tr><td>clean [값]</td><td>yum 관련해서 저장된 정보 삭제<br> 값 : all, packages, rpmdb, dbcache 등</td></tr>
<tr><td>history [sub-command]</td><td>yum 명령을 사용한 작업 이력 정보 출력<br> sub-command : list(기본값), info, summary 등</td></tr>
</table>


### 데비안 패키지 관리(Debial Package Management)

파일 형식
```
패키지이름_버전-릴리즈-아키텍처.deb
```

#### dpkg

```
dpkg [option] [패키지명] [패키지_파일명]
```

주요 옵션
<table>
<tr><td>-i 패키지_파일명</td><td>패키지 설치(--install)</td></tr>
<tr><td>-R 디렉터리명</td><td>-i 옵션과 함께 사용하여 지정 디렉터리 안에 있는 패키지 설치</td></tr>
<tr><td>-l</td><td>설치되어 있는 패키지 출력(--list)</td></tr>
<tr><td>-I 패키지_파일명</td><td>패키지 파일에 대한 정보 출력(--info)</td></tr>
<tr><td>-c 패키지_파일명</td><td>패키지 파일에 포함된 파일 정보 출력(--contents)</td></tr>
<tr><td>-L 패키지명</td><td>패키지가 설치한 파일 목록 출력(--listfiles)</td></tr>
<tr><td>-r 패키지명</td><td>환경 설정 파일을 남기고 패키지 제거(--remove)</td></tr>
<tr><td>-P 패키지명</td><td>환경 설정 파일을 포함하여 패키지 제거(--purge)</td></tr>
<tr><td>-S 파일명</td><td>해당 파일을 설치한 패키지 이름 출력(--search)</td></tr>
<tr><td>-C</td><td>완전히 설치되지 않은 패키지 검사(--audit)</td></tr>
<tr><td>--unpack 패키지_파일명</td><td>환경 설정은 하지 않고 패키지를 풀기만 함</td></tr>
<tr><td>--configure 패키지명</td><td>--unpack 옵션으로 풀린 패키지의 환경 설정 수행</td></tr>
<tr><td>-a</td><td>--unpack 옵션으로 풀린 패키지들의 환경 설정 수행</td></tr>
<tr><td>-s 패키지명</td><td>패키지에 대한 상태 출력(--status)</td></tr>
</table>

#### apt-get
- 데비안 리눅스 배포판에서 패키지 관리를 쉽게 하기 위해 제공하는 명령행 기반의 유틸리티
- `/etc/apt/sources.list` 파일에 패키지 관련 정보를 관리하여 의존성 충돌 문제를 해결

```
apt-get [option] [command] [패키지명]
```

주요 옵션
<table>
<tr><td>-y</td><td>모든 질의에 yes라고 대답(--yes, --assume-yes)</td></tr>
<tr><td>--purge</td><td>remove 명령 수행 시 환경 설정도 함께 제거</td></tr>
</table>

주요 command
<table>
<tr><td>update</td><td>패키지 목록 갱신<br>'/etc/apt/sources.list'에서 관련 정보를 가져옴</td></tr>
<tr><td>upgrade</td><td>모든 패키지를 최신 버전으로 갱신<br>update 후 사용</td></tr>
<tr><td>install 패키지명</td><td>패키지 설치<br>'/var/cache/apt/archive'에 .deb 파일이 생성됨</td></tr>
<tr><td>remove 패키지명</td><td>패키지 삭제</td></tr>
<tr><td>clean</td><td>/var/cache/apt/archive에 생성된 파일 삭제</td></tr>
</table>



## 소스 코드 컴파일
### 소스 설치법
소스 프로그램 설치 과정

1. 압출 풀기
2. 디렉터리 이동
3. configure
- 사용자들이 자신의 환경에 맞도록 손쉽게 수정하도록 제공되는 소스 프로그램 환경 설정 스크립트
- 성공적인 컴파일을 위해 필요한 컴파일러, 라이브러리, 유틸리티, 기타 정보를 시스템에서 찾아냄
- 사용자 시스템에 적상한 소프트웨어 패키지가 생성되도록 Makefile 생성
4. make
- 대상 프로그램을 파생시키는 방법을 지정하는 Makefile 파일을 읽어 소스 코드에서 실행 가능한 프로그램과 라이브러리를 자동으로 빌드하는 자동화 도구
- configure에 의해 변경된 내용을 반영하고 target과 의존성 관련 작업을 한 후 최종적으로 실행 파일 생성
5. make install
- 컴파일된 실행 파일을 지정한 속성으로 지정된 디렉터리에 설치

사용 예
```
[root@www httpd-2.4.29]# ./configure --help
configure로 지원되는 옵션 정보 출력

[root@www httpd-2.4.29]# ./configure --prefix=/usr/local/apache
설치 디렉터리 지정

[root@www httpd-2.4.29]# make clean
configure 작업으로 생성된 다양한 파일 제거
```

### cmake(cross platform make system)
- 소스 컴파일 시 사용되는 Make의 대체 프로그램
- 멀티플랫폼을 지원하기 위한 목적으로 등장한 오픈소스 프로젝트
- make 과정을 수행하지 않고 지정한 운영체제에 맞는 make 파일의 생성을 목적으로 함

<b>cmake 특징</b>

- 소프트웨어 빌드에 특화된 언어로 독자적인 설정 스크립트를 이용한다.
- 유닉스 계열 운영체제, Mac OS X, 윈도우 계열 등 다양한 플랫폼을 지원한다.
- C, C++, Java, Fortran에 대해서는 자체적으로 의존 관계를 분석할 수 있다.
- SWIG, Qt, FLTK 등 지원한다.
- Visutal Studio .Net 및 Visual Studio 지원
- 이클립스용 빌드 파일을 생성할 수 있다.
- 타임스탬프를 통해 파일 내용의 변화를 알 수 있다.
- 평행 빌드를 지원한다.
- 크로스 컴파일을 할 수 있다.

### 압축 관련 유틸리티
#### tar(tape archive)
- 여러 파일들을 하나의 파일로 묶어주는 명령
- 디렉터리 지정 시 하위 디렉터리 및 파일을 함께 묶음
- 파일의 속성, 하드링크, 심볼릭링크 등 보존
- 옵션을 이용하여 압축 관련 작업을 동시 진행 가능

```
tar [option] [파일명]
```

주요 옵션
<table>
<tr><td>-c</td><td>지정한 파일이나 디렉터리를 하나로 묶어 새로운 tar 파일 생성(--create)</td></tr>
<tr><td>-x</td><td>생성된 tar 파일을 푼다(--extract, --get)</td></tr>
<tr><td>-v</td><td>명령을 실행할 때 대상이 되고 있는 파일들을 출력(--verbose)</td></tr>
<tr><td>-f 파일명</td><td>작업 대상이 되는 tar 파일의 이름 지정(--file=파일명)<br>파일명 대신 '-'를 쓰면 표준 입출력</td></tr>
<tr><td>-r</td><td>기존의 tar 파일 뒤에 파일 추가(--append)</td></tr>
<tr><td>-h</td><td>심볼릭 링크가 가리키고 있는 원본 파일 저장(--dereference)</td></tr>
<tr><td>-C</td><td>디렉터리 변경(--directory=디렉터리명)</td></tr>
<tr><td>-p</td><td>파일의 생성되었을 때의 권한을 그대로 유지(--same-premissions)<br>root 사용자는 기본적으로 적용되는 옵션</td></tr>
<tr><td>-Z</td><td>compress 관련 옵션으로 예전 UNIX 계열 표준 압축 파일인 tar.Z에 사용(--compress, --uncompress)</td></tr>
<tr><td>-z</td><td>gzip 관련 옵션으로 압축 파일인 tar.gz에 사용(--gzip)</td></tr>
<tr><td>-j</td><td>bzip2관련 옵션으로 압축 파일인 tar.bz2에 사용(--bzip2)</td></tr>
<tr><td>-J</td><td>xz 관련 옵션으로 압축 파일인 tar.xz에 사용(--xz)</td></tr>
<tr><td>--delete</td><td>생성된 tar 파일에서 특정 파일 삭제</td></tr>
</table>

#### compress, uncompress
- 유닉스에서 사용했던 압축 프로그램이나, 리눅스에서는 압축률이 낮아 거의 쓰이지 않음
- 옵션 없이 압축 시 원본 파일은 사라지고 '파일명.Z'이 생성됨
- 레드햇 계열 리눅스인 경우 ncompress 패키지 설치 시 사용 가능

```
compress [option] 파일명
uncompress [option] 파일명
```

주요 옵션
<table>
<tr><td>-c</td><td>표준 출력으로 지정하는 옵션<br>보통 생략하나, tar 등과 병행해서 사용 시 반드시 표기</td></tr>
<tr><td>-v</td><td>압축 관련 정보 출력</td></tr>
</table>

#### gzip(GNU zip), gunzip
- GNU에서 만든 압축 프로그램
- 압축 파일은 .gz

```
gzip [option] 파일명
gunzip [option] 파일명
```

주요 옵션
<table>
<tr><td>-d</td><td>압축을 풀 때 사용하는 옵션(--decompress, --uncompress)</td></tr>
<tr><td>-n</td><td>1~9 사이의 값을 지정해 압축 속도 지정

- 6 : 기본값
- 1 : 파일의 압축 시간을 줄임(--fase). 압축률은 떨어짐
- 9 : 파일을 최대로 압축(--best). 시간이 많이 걸림</td></tr>
<tr><td>-c</td><td>결과를 표준 출력으로 보낼 때 사용<br>tar와 병행해서 작업할 때 사용</td></tr>
<tr><td>-l</td><td>압축 파일에 대한 정보 출력(--list)</td></tr>
<tr><td>-r</td><td>대상이 디렉터리인 경우 하위 디렉터리까지 찾아 처리(--recursive)</td></tr>
<tr><td>-v</td><td>진행 과정을 이름 및 퍼센트와 함께 자세히 보여줌(--verbose)</td></tr>
</table>

사용 예
```
$ gzip -l test.tar.gz
압축 되기전 파일명과 크기, 압축된 크기, 압축률에 대한 정보 출력

$ gzip -cd test.tar.gz | tar xvf -
test.tar.gz 파일의 압축 해제 후 tar를 사용하여 현재 디렉터리에 푼다.
```

#### bzip2, bunzip2
- 블록 정렬 알고리즘 버로우즈-휠러 변환(Burrows-Wheeler transform)과 허브만 부호화(Huffman coding)을 사용하여 만든 압출 프로그램
- gzip보다 압축률은 좋지만 압축 시간이 더 걸림
- 압축 파일은 .bz2

```
bzip2 [option] 파일명
bunzip2 [option] 파일명
```

주요 옵션
<table>
<tr><td>-d</td><td>압축 해제(--decompress)</td></tr>
<tr><td>-n</td><td>1~9를 지정하여 압축 시 블록 크기를 n00 K로 지정

- -1 : 압축은 빠르지만 압축률은 떨어짐(--fast)
- -9 : 압축률은 좋으나 압축 시간이 많이 걸림(--best)
</td></tr>
<tr><td>-c</td><td>결과를 표준 출력으로 보낼 때 사용<br>tar와 병행해서 작업할 때 사용</td></tr>
<tr><td>-f</td><td>덮어쓰기(--force)<br>이 옵션을 사용하지 않으면 덮어쓰지 않음</td></tr>
</table>

#### xz, unxz
- LZMA2(Lempel-Ziv-Markov chain algorithm)을 이용하여 만든 데이터 무손실 압축 프로그램
- gzip, bzip2와 비교하여 매우 높은 압축률
- 압축 파일은 .xz

```
xz [option] 파일명
unxz [option] 파일명
```

주요 옵션
<table>
<tr><td>-z</td><td>압축할 때 사용하는 옵션(기본 지정)</td></tr>
<tr><td>-d</td><td>압축을 풀 때 사용하는 옵션(--decomporess, --uncompress)</td></tr>
</table>

#### zip, unzip
- DOS/Windows 계열 운영체제에서 많이 사용되던 압축 프로그램
- 압축 파일은 .zip

```
zip [option] 압축파일명 파일명
unzip 압축파일명
```

주요 옵션
<table>
<tr><td>-r</td><td>압축 대상이 디렉터리인 경우 하위 디렉터리를 포함하여 압축</td></tr>
</table>

### gcc(GNU Compiler Collection)
- GNU 프로젝트에 의해 만들어진 일종의 컴파일러 모음
- C, C++, Objective-C, Fortran, Java, Ada, Go 등 다양한 프로그래밍 언어 지원
- 리눅스 기반에서 가장 손쉽게 사용할 수 있는 C 컴파일러 도구
  - C언어로 작성한 파일을 특별한 옵션 없이 gcc로 컴파일하면 a.out라는 실행 파일이 생성됨

```
gcc [option] 파일명
```

주요 옵션
<table>
<tr><td>-o</td><td>컴파일할 때 실행 파일의 이름을 지정하는 옵션</td></tr>
<tr><td>-c</td><td>링크는 하지 않고 목적 파일만 생성할 때 사용하는 옵션<br>.o 파일이 생성됨</td></tr>
</table>

### make 유틸리티 활용
#### make
- GNU 프로젝트에 의해 만들어진 프로그램으로 컴파일을 자동화해주는 도구
- 프로그램의 소스 파일에서 프로그램의 실행 파일 및 기타 비소스 파일의 생성을 제어
- Makefile에서 관련 정보를 가져와 빌드(Build)함

```
make [option]
```

주요 옵션
<table>
<tr><td>-f</td><td>Makefile 이외의 다른 파일에서 관련 정보를 참조할 때 사용(--file=파일명, --makefile=파일명)</td></tr>
</table>


#### Makefile
- 비소스 파일을 나열하고, 다른 파일을 참고하는 방법에 대한 정보가 기재된 파일

기본 구조
```
목표파일명 : 의존성파일1 의존성파일2
            명령행
```
- 목표 파일(target)을 지정하고 콜론으로 구분한 후 의존성 있는 파일들을 나열
  - 의존성 있는 파일들은 목표 파일의 생성에 관계되는 모든 파일들을 의미
- 그 다음 행에 [Tab]키 후에 관련 명령을 기입


생성 예1
```
lin : lin.c
  gcc -o lin lin.c
```
- `make lin` 실행 lin.c를 컴파일하여 실행 파일로 생성

생성 예2
```
TARGET := lin
OBJECT := lin.o
SOURCE := lin.c
$(TARGET) : $(SOURCE)
  gcc -o $(TARGET) $(SOURCE)

clean :
  rm -f $(TARGET) $(SOURCE)
```
- `make clean`을 실행하면 lin, lin.o 파일 삭제

## 라이브러리(Library) 관리
### 라이브러리
- 프로그램에서 특정한 기능을 하는 루틴(Routine)들을 모아 놓은 것
  - 하나 이상의 서브루틴, 함수, 클래스로 만듦
- 동일한 기능을 여러 곳에 사용할 경우, 불필요한 코드 작성을 없애기 위해 재사용이 가능한 형태로 만든 것

링크 방식에 따라 정적 링크 라이브러리와 동적 링크 라이브러리로 분류

- <b>Static Link Library</b> : 프로그램을 컴파일 할 때 실행 파일에 포함(복사)되어 배포되는 방식
- <b>Dynamic Link Library</b> : 실행 프로그램에 항상 라이브러리를 포함하지 않고 필요할 때만 메모리에 라이브러리를 적재하는 방식

### 공유 라이브러리(Shared Library)
- 다수의 명령어(응용 프로그램)가 같은 루틴을 공유하는 것
- 많은 응용 프로그램에 동적으로 링크되어 공유되는 라이브러리

공유 라이브러리 특징
1. 의존성
- 동적으로 링크된 프로그램은 적어도 하나 이상의 공유 라이브러리가 필요하다.
- 필요한 라이브러리가 존재하지 않거나 찾을 수 없으면 프로그램은 실행되지 않는다.

2. 링크
- 동적으로 링크된 실행 파일은 실행 시에 공유 오브젝트에 대한 동적 링커인 ld.so에 의해 검사된다.
  - ld.so는 실행 파일을 점검하고 시스템의 공유 라이브러리에 대한 의존성 문제를 해결
- ld.so가 지정된 라이브러리를 찾지 못하면 파일은 실행되지 않는다.

### 공유 라이브러리 관린 디렉터리 및 파일
- 리눅스 운영체제에서 사용되는 공유 라이브러리는 파일명 뒤에 .so(shared object)라고 덧붙여져 있다.
- 공유 라이브러리 파일들의 위치는 `/lib`와 `/usr/lib` 디렉터리에 나뉘어져 있음
  - /lib : ls, mv 등과 같은 기본 명령어 및 시스템과 연관된 라이브러리가 위치
  - /usr/lib : 주로 응용 프로그램과 관련된 라이브러리가 위치
- CentOS 7(RHEL 7) 버전부터 대부분 공유 라이브러리 파일들을 /lib64 디렉터리 안에 두고 관리

<b>/etc/ld.so.conf</b>

- 기본적으로 사용되는 공유 라이브러리 디렉터리인 /lib, /usr/lib, /lib64 이외에 추가로 다른 디렉터리를 등록할 때 사용하는 환경 설정 파일
- 추가로 등록할 공유 라이브러리 디렉터리를 한 줄에 하나씩 명기한 후 `ldconfig` 명령을 수행해서 관련 정보를 갱신시켜야 함

<b>/etc/ld.so.cache</b>

- /lib, /usr/lib 디렉터리뿐만 아니라 /etc/ld.so.conf 파일에 명시한 디렉터리에서 찾을 수 있는 라이브러리 파일 목록 정보를 담고 있는 파일

### 공유 라이브러리 관련 명령어
#### ldconfig
- 공유 라이브러리 관련 정보를 갱신하고 설정하는 명령
- 주로 변경된 /etc/ld.so.conf 파일의 내용을 갱신할 때 사용

```
ldconfig [option] [라이브러리]
```

주요 옵션
<table>
<tr><td>-p</td><td>현재 캐시에 저장되어 있는 공유 라이브러리 목록 정보를 출력</td></tr>
<tr><td>-v</td><td>명령 실행과 관련된 자세한 정보를 출력</td></tr>
<tr><td>-f</td><td>/etc/ld.so.conf 파일 대신 다른 환경 설정 파일을 지정할 때 사용</td></tr>
</table>

### ldd
- 특정 파일이나 프로그램이 의존하고 있는 공유 라이브러리 정보를 출력하는 명령

```
ldd [option] 파일명
```

주요 옵션
<table>
<tr><td>-v</td><td>관련 라이브러리 정보를 버전 등과 함께 자세히 출력(--verbose)</td></tr>
<tr><td>-f</td><td>/etc/ld.so.conf 파일 대신 다른 환경 설정 파일을 지정할 때 사용</td></tr>
</table>

### 공유 라이브러리 관련 환경 변수
LD_LIBRARY_PATH

- 라이브러리 로드 경로
- 특정 프로그램 실행 시 라이브러리 등록이 필요하면 이 변수에 디렉터리 경로를 등록(`export LD_LIBRARY_PATH=/path`)

