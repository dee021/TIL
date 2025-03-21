# 네트워크(Network)

- 컴퓨터를 상호 연결하여 정보 교환 및 처리, 자료 및 하드웨어 공유 등을 위해 구성한 통신망

## 네트워크와 통신망
### LAN(Local Area Network)

집, 사무실, 학교 등의 건물과 같이 가까운 지역을 네트워크 매체를 이용하여 하나로 묶는 근거리 통신망

#### 구성 방식

1. 스타(Star)형
- 중앙에 위치한 중앙 컴퓨터가 각각의 컴퓨터와 통신하는 방식
- 중앙의 제어기(허브 또는 교환기)를 중심으로 모든 기기는 Point-to-Point 방식으로 연결
- 고속의 대규모 네트워크에 이용
- 일부 장애가 발생해도 전체 네트워크에 영향을 주지 않으나, 중앙 컴퓨터 고장 시 전체 네트워크 중단

2. 버스(Bus)형
- 하나의 통신 회선(Bus)에 여러 컴퓨터를 연결해서 전송하는 방법
- 모든 장치들은 동등한 조건으로 경쟁함
- 한 번에 한 컴퓨터만 전송 가능
  - 연결된 컴퓨터 수에 따라 성능이 좌우됨
- 신호 반사에 의한 상호 간섭을 막기 위해 종단에는 Terminator가 존재
- 장애 발생 시 발생지의 위치 추적이 어렵고, 버스의 연결 부위나 종단 장치에 문제가 발생하면 전체 네트워크가 중단됨
- 전송 방식 : CSMA/CD와 토큰 패싱(Token Passing)

3. 링(Ring) 형
- 원형의 통신 회선에 컴퓨터와 단말기를 연결하는 형태
- 앞의 컴퓨터로부터 수신한 내용을 다음 컴퓨터에 재전송하는 방법으로 동작
- 토큰 패싱을 사용하여 데이터를 전송
- 네트워크 전송상의 충돌이 없음
- 노드의 숫자가 늘더라도 전체적인 성능 저하가 적으나, 비교적 노드의 추가가 어렵고, 노드에 문제 발생 시 전체 네트워크가 중단될 수 있음

4. 망(Mesh) 형
- 스타형과 링형의 혼합된 형태
- 각 네트워크 장비가 여러 개의 인터페이스를 갖추고, 상호간에 그물 형태로 연결하는 방식
- LAN과 LAN을 연결하거나 Backborn Network를 구성할 때 주로 사용
- 장애 발생 시 다른 시스템에 영향이 적고, 우회할 수 있는 방법이 존재
- 설치비용이 많이 들고 장애 발생 시 고장 지점을 찾기가 쉽지 않음
 
#### 전송 방식

1. 이더넷과 CSMA/CD

이더넷(Ethernet)
- 각 기기들이 48비트 길이의 고유한 MAC(Media Access Control) 주소를 기반으로 상호간에 데이터를 주고받을 수 있도록 만들어짐
- 전송매체로 동축(BNC), UTP, STP 케이블을 이용
- 각각의 기기를 상호 연결하기 위해 허브, 스위치, 리피터 등의 장치를 이용
- CSMA/CD 방식을 이용

CSMA/CD(Carrier Sense Multiple Access with Collision Detection; 반송파 감지 다중 접속 및 충돌 탐지)
- 단말기가 전송로의 신호유무를 조사하고, 다른 단말기가 신호를 송출하고 있는지 확인
- 토큰 링 방식에 비해 효율성을 떨어지나 가격이 저렴


2. 토큰링(Token Ring)
- 토큰링 네트워크는 여러 스테이션(컴퓨터)을 하나의 링에 이어져 형성
- 데이터는 한쪽 방향으로 흐르도록 하여 한 컴퓨터에서 다음 컴퓨터 순으로 순서대로 전달
- 데이터 전송을 위한 네트워크 채널을 사용하기 위해 토큰(Token)을 획득해야 함

3. FDDI(Fiber Distributed Data Interface)
- 전송 매체를 광섬유 케이블을 사용하여 설계된 링 구조의 통신망
- 네트워크 액세스를 제어하기 위해 토큰 패싱 방법을 사용

### MAN(Metropolitan Area Network)
LAN과 WAN의 중간 형태로 위성 도시 등을 연결한 형태
- 국제 표준안에는 DQDB(Distributed Queue Dual Bus)가 IEEE 802.6으로 규정됨

### WAN(Wide Area Network)
국가, 대륙 등과 같은 넓은 지역을 연결하는 네트워크
- 거리상 제약이 없음
- 다양한 경로를 경유하여 도달하므로 속도가 느리고 전송 에러율이 높은 편

#### 구성 방법
1. 전용 회선 방식
- 회선을 제공하는 통신사업자가 계약 체결한 송수신 사용자끼리만 전용 통신 선로로 연결하여 데이터를 교환하는 방식
- 안전성이 우수하나 높은 비용

2. 교환 회선 방식
- 공중망을 이용해 전송하는 방식
- 다수의 사용자가 선로를 공유해서 사용하므로 데이터 전송 속도가 상대적으로 느림
- 교환 회선 방식
  1. 회선 교환 방식(Circuit Switching)
    - 송신 호스트와 수신 호스트가 데이터를 전송하기 전에 연결 경로를 설정하여 물리적 전용선을 연결하는 방식
    - 고정된 대역폭을 할당 받아 전송되고, 안정적인 데이터 전송률을 지원
  2. 패킷 교환 방식(Packet Switching)
    - 패킷(데이터 블록)을 이용하여 전송하는 방식
    - 전송 대역폭을 동적인 방식으로 공유
    - 이론상 호스트의 무제한 수용이 가능하며 패킷별로 우선 순위 부여가 가능
    - `데이터그램(Datagram)` 방식과 `가상 회선(Virtual Circuit)` 방식으로 나뉨

데이터그램 방식 
  - 비연결형 서비스를 이용해 전달되는 각 패킷들이 독립적으로 전송됨
  - 목적지에 도착하는 순서를 예측할 수 없음
  - 전송할 정보의 양이 적거나, 상대적으로 신뢰성이 중요하지 않은 환경에서 사용

가상 회선 방식
  - 송수신 호스트 사이에 설정된 가상의 단일 파이프를 통해 패킷을 송수신
  - 모든 패킷이 동일한 경로로 순서대로 도착함

#### 관련 프로토콜
1. X.25
- 패킷망에서 DCE(회선 종단 장치)와 DTE(데이터 단말 장치) 사이에 이루어지는 상호 작용을 규정한 프로토콜
- 네트워크 계층의 대표적인 프로토콜
- 가변 길이 프레임 전송을 지원하는 프레임 릴레이(Frame Relay)의 근간을 이룸
2. 프레임 릴레이(Frame Relay)
- LAN 또는 WAN 상에서 단말 지점간의 효율적인 데이터 전송을 위해 고안된 방법
- 패킷 대신 프레임에 데이터를 넣고 재전송과 같은 오류정정기능은 단말 지점에 맡기는 방식
3. 셀 릴레이(Cell Relay) : ATM(Asynchronous Transfer Mode)
- 고정 길이(53Byte) 패킷을 이용하여 순서대로 자료를 전송하는 방식
- 가상 채널(Virtual Channel) 기반의 연결형 서비스
- 양 끝 단말간의 오류 제어와 흐름 제어


## 네트워크 장비
#### LAN 구성 장비
1. 네트워크 카드(Network Card)
- Network Adaptor, Network Interface Card, Ethernet Card, LAN Card
- 네트워크 안에서 컴퓨터끼리 통신하는데 쓰이는 하드웨어
- 고유한 주소 할당 시스템인 MAC 주소를 이용하여 물리 계층과 데이터 링크 계층의 장치를 사용
2. 케이블
- 신호를 전송하는 물리적인 전송 매체
  1. BNC 케이블, 동축케이블
  - 잡음에 강하며, 넓은 대역폭으로 여러 채널을 동시에 수용할 수 있음
  - UTP처럼 허브 없이 여러 대의 컴퓨터를 연결할 수 있으나, 한 컴퓨터에 문제가 생기면 전체 네트워크에 영향을 줄 수 있음
  2. Twisted Pair Cable
   - 저렴하나 잡음에 약함
   - STP, UTP가 이에 해당
   - 허브를 통해 각 컴퓨터마다 하나의 선으로 연결되므로 한 곳에 연결 문제가 발생하더라도 다른 컴퓨터는 지장을 받지 않음
  3. 광케이블
   - 빛을 이용해 정보 전달이 이루어지므로 전기적인 간섭을 받지 않고 오류 발생도 매우 적음
3. 허브
- 이더넷 네트워크에서 여러 대의 컴퓨터 및 네트워크 장비를 연결하는 장치
- 같은 허브에 연결된 컴퓨터와 네트워크 장비는 모두 상호 간에 통신할 수 있음

#### 인터네트워킹(Internetworking) 장비
1. Repeater
- 물리 계층에서 동작
- 입력된 신호를 증폭하여 중계하는 장치
- 디지털 방식의 통신 선로에서 전송 신호를 재생하여 전달하는 통신 장치
- LAN 선로의 길이가 물리적 한계에 부딪쳤을 때 물리적, 논리적 확장

2. Bridge
- 데이터 링크 계층에 있는 여러 개의 네트워크 세그먼트를 연결
- 물리 계층에 있는 리피터나 허브의 기능
- 특정 네트워크로부터 오는 통신량을 조정하여 트래픽을 관리

3. Router
- 물리 계층, 데이터 링크 계층, 네트워크 계층의 기능을 지원
- 네트워크 계층에 대한 경로 선택 기능을 제공
- 네트워크에 들어온 데이터를 어느 네트워크로 전달할지 판단
- 어떤 경로를 이용해야 빠르게 전송할 수 있는지 판단
- Routing Table에 연결된 네트워크 및 호스트 정보를 보관/유지/관리

4. Gateway
- 서로 다른 네트워크 간을 연결하여 통신이 가능하게 해주는 장치
- OSI 모델 전 계층을 포함하여 동작
- 완전히 다른 두 개의 네트워크 사이에 데이터 형식을 변환

# 프로토콜(Protocol)
컴퓨터 상호간에 통신을 위해 미리 정해놓은 특별한 통신규칙, 통신규약

#### 프로토콜의 구성요소
1. 구문(Syntax)
- 데이터의 구조나 형식으로 부호화, 신호 레벨 등을 규정
2. 의미(Semantics)
- 전송의 조작이나 오류 제어를 위한 제어 정보에 대한 규정
3. 순서(Timing)
- 접속되어 있는 개체 간의 통신 속도의 조정이나 메시지의 순서 제어

#### 프로토콜의 기능
1. 주소 지정(Addressing)
2. 순서 지정(Sequencing)
3. 단편화와 재조합(Fragmentation and Reassembly)
4. 흐름 제어(Flow Control) : 'Stop and Wait'와 'Sliding Window'
5. 오류 제어(Error Control) : 'Checksum'
6. 연결 제어(Connetion Control)
7. 동기화(Synchronization)
8. 멀티플렉싱(Multiplexing)
9. 전송 서비스(Transmission Service)
10. 캡슐화(Encapsulation)

#### 프로토콜 제정 기관
1. ISO(International Organization for Standardization) : OSI 참조 모델 제정
2. IEEE(Institute of Electrical and Electonics Engineers) : LAN 및 WAN 관련 표준 IEEE 802
3. ANSI(American National Stadards Institue)
4. EIA(Electronic Industries Alliance) : RS-232C, UTP 케이블 표준 규격 제정
5. ITU-T(International Telecommunication Union Telecommunication Standardization Sector)

## TCP/IP
컴퓨터 기종에 관계없이 정보 교환이 가능하게 해주는 통신 프로토콜

### TCP/IP의 구조

TCP/IP 계층과 관련 프로토콜
<table>
<thead><td>계층</td><td>프로토콜</td></thead>
<tr><td>응용 계층</td><td>HTTP, SMTP, POP3, IMAP, DNS, NFS, FTP, TELNET, SSH 등</td></tr>
<tr><td>전송 계층</td><td>TCP, UTP 등</td></tr>
<tr><td>인터넷 계층</td><td>IPv4, IPv6, ICMP, ARP 등</td></tr>
<tr><td>네트워크 인터페이스 계층</td><td>Ethernet, Token Ring, FDDI 등</td></tr>
</table>

1. Network Interface Layer
- 이더넷 카드 등으로 연결된 물리적인 네트워크
- 프레임을 물리적인 회선에 올리거나 내려 받는 역할
2. Internet Layer
- 전송 계층에서 받은 패킷을 목적지까지 효율적으로 전달하는 역할
  <table>
  <tr><td>IP<br>(Internet Protocol)</td><td>
  
  - OSI 네트워크 계층에서 호스트의 주소 지정과 패킷 분할 및 조립 기능을 담당
  - 데이터 세그먼트를 패킷으로 만들어 전송
  - 비신뢰성(Unreliability), 비연결형(connectionless)</td></tr>
  <tr><td>ICMP<br>(Internet Control Message Protocol)</td><td>

  - 메시지에 대한 오류 보고와 이에 대한 피드백을 원래 호스트에 보고하는 역할
  - ICMP 제어 메시지는 IP 패킷의 형태로 전달되고 ping 명령이 사용하는 프로토콜
  </td></tr>
  <tr><td>ARP<br>(Address Resolution Protocol)</td><td>

  - 하드웨어 주소(MAC 주소)와 논리적인 할당에 의한 IP 주소를 맵핑하기 위한 프로토콜
  - 동적으로 특정 프로토콜에 의해 필요할 때마다 목적지 호스트의 하드웨어 주소(MAC 주소)를 찾는 역할
  </td></tr>
  </table>
3. Transport Layer
- 응용 계층으로부터 받은 데이터를 세그먼트(Segment) 형태로 만들어 네트워크를 통해 수신자에게 전달하는 역할
- 연결 지향 데이터 스트림 지원, 신뢰성, 흐름 제어, 다중화와 같은 서비스 제공
<table>
<tr><td>TCP<br>(Transmission Control Protocol)</td><td>

- 연결지향(Connection Oriented) 전송 프로토콜
- 세그먼트가 수신자에게 제대로 전달되었는지 응답(Ack)을 주고받음으로서 점검
- IP에 의해 전달되는 패킷의 오류를 검사하고 재전송을 요구하는 등의 정교한 제어를 담당
- 패킷을 전송할 두 지점을 네트워크상 최단 경로로 찾아서 1:1로 직접 연결
</td></tr>
<tr><td>UDP<br>(User Datagram Protocol)</td><td>

- 비연결(Connectionless) 전송 프로토콜
- 세그먼트를 보내기만하고 응답(Ack)을 주고받지 않음
</td></tr>
</table>

4. Application Layer
- 네트워크로 연결된 프로세스간의 통신 접속을 위해 설계된 통신 프로토콜과 방식을 위해 만들어진 추상 계층
- 사용자 프로그램에서 구현됨
- 전송 계층 프로토콜을 사용하여 호스트간의 연결을 확립하는 역할

### OSI 7-Layer
- ISO(Interneational Standardization Organization)에서 개발
- 컴퓨터 네트워크 프로토콜 디자인과 통신을 7계층으로 나누어 정의
- 각 계층은 서로 독립적으로 구성

1. Physical Layer
- 실제 장치들을 연결하기 위한 케이블 및 연결 장치 등 기계적인 항목과 전압, 신호 방식 등의 전기적인 항목에 대한 특성을 규정
- 상위 계층에서 전송된 데이터를 물리적인 전송 매체를 bit 형태로 전송
- 네트워크 장치에 허브나 리피터 등이 있음

2. Data Link Layer
- 네트워크 계층에서 받은 데이터를 논리적 단위인 frame으로 구성하고 필요한 정보를 덧붙여 물리 계층으로 전달
- 전송 제어, 오류 제어, 흐름 제어와 같은 정보를 필요에 따라 추가시켜 전송
- 브리지, 스위치 등

3. Network Layer
- 데이터를 패킷 단위로 분할하여 전송하며 데이터 전송과 경로 선택에 관한 서비스 제공
- 라우팅, 혼잡 제어, 패킷의 분할과 병합, 인터네트워킹 등의 역할을 수행
- 라우터 

4. Transport Layer
- 송신 측과 수신 측 사이의 실제적인 연결 설정 및 유지, 오류 복구와 흐름 제어
- 전체 메시지의 전속을 책임짐
- 송신 프로세스와 수신 프로세스간의 연결 기능을 제공하고 안전한 데이터 전송을 지원

5. Session Layer
- 응용 프로그램 간의 통신을 관리하기 위한 방법과 동기화를 유지하는 서비스를 제공
- 응용 프로그램 사이의 접속 설정 및 유지, 종료에 관한 세션 관리 기능, 데이터의 전송 순서 및 동기점의 위치를 제공

6. Presentation Layer
- 전송하는 데이터의 표현 방식에 관한 서비스를 제공
- 송신자와 수신자가 서로 다른 표현 방식을 사용하는 경우 코드와 문자 등을 번역하여 전송 데이터를 서로 이해할 수 있도록 하는 기능
- 데이터의 암호화와 해독, 압축과 압축 해제를 수행

7. Appliction Layer
- 응용 프로그램과 연계하여 사용자에게 편리한 환경을 제공하는 역할

### 프로토콜 데이터 단위
1. PUD(rotocol Data Unit)
- 같은 계층에 존재하는 두 통신 개체(Peer-to-Peer)간에 서로 주고받는 헤더(제어 정보) 및 데이터(SDU, Payload)가 합쳐진 캡슐화된 운반체
- 프레임, 패킷, 세그먼트 등이 이에 해당
- PCI(Protocol Coltro Unit)와 SDU(Service Data Unit)으로 구성
  - PCI는 헤더 정보를 의미하는 발신지 주소, 수신지 주소, 순서 번호, 오류 검출용 FCS(Frame Check Sequence) 등을 포함
  - SDU는 실제 사용자의 정보

2. SDU
- 상/하향 두 통신 계층 간에 전달되는 실제 정보로써 교환되는 데이터 단위량

OSI 모델의 전송 단위 및 역할
<table>
<thead><td>계층</td><td>전송 단위</td><td>역할</td></thead>
<tr><td>응용 계층</td><td>data</td><td>사용자에게 직접 제공하는 서비스 및 응용 프로그램 간의 정보 교환</td></tr>
<tr><td>표현 계층</td><td>data</td><td>송수신할 정보의 표현에 차이가 있는 응용 프로그램들에게 연결 제공</td></tr>
<tr><td>세션 계층</td><td>data</td><td>응용 프로그램 간의 통신을 관리하기 위한 방법과 동기화를 유지하는 서비스 제공</td></tr>
<tr><td>전송 계층</td><td>segment</td><td>정보의 신뢰성 있고 투명한 데이터 전송에 관한 서비스 제공</td></tr>
<tr><td>네트워크 계층</td><td>packet</td><td>정보의 전송을 위한 경로 선택</td></tr>
<tr><td>데이터링크 계층</td><td>frame</td><td>물리적인 링크를 통하여 신뢰성 있는 정보의 전송을 위한 통신로 설정</td></tr>
<tr><td>물리 계층</td><td>bit</td><td>전송 매체를 통해 전송</td></tr>
</table>

OSI 계층과 관련 프로토콜
<table>
<thead><td>계층</td><td>프로토콜</td></thead>
<tr><td>응용 계층</td><td>HTTP, SMTP, POP3, IMAP, DNS, NFS, FTP, TELNET, SSH 등</td></tr>
<tr><td>표현 계층</td><td>SMB, AFP, ASN.1 등</td></tr>
<tr><td>세션 계층</td><td>SSL, TLS, NetBIOS 등</td></tr>
<tr><td>전송 계층</td><td>TCP, UDP, RTP 등</td></tr>
<tr><td>네트워크 계층</td><td>IP, ICMP, ARP, RARP, IGMP, BGP, RIP, IPX 등</td></tr>
<tr><td>데이터링크 계층</td><td>Ethernet, Token Ring, FDDI, ATM 등</td></tr>
<tr><td>물리 계층</td><td>RS-232, 10BASE-T, DSL 등</td></tr>
</table>

#### Protocol Number
- 시스템이 데이터를 처리하기 위해서는 전송 프로토콜에 대한 인식이 필요
- 관련 프로토콜 정보는 번호를 사용하여 데이터그램 헤더에 담겨 전송됨
- 응용 프로그램을 구분하기 위해 포트 번호를 추가로 사용
- 리눅스 및 유닉스에서는 '/etc/protocols' 파일에서 프로토콜 번호를 확인할 수 있음

 ![protocols](https://github.com/dee021/TIL/blob/linuxmaster/Linux/img/protocols.png)


#### Port Number
- IP 프로토콜에 의해 전달된 데이터는 TCP 및 UDP 등에서 서비스 다중화를 위해 Port로 관리됨
- 각각의 응용 프로그램은 할당된 포트를 이용해 통신할 데이터를 주고 받음
- 포트는 번호로 관리되며, 0번부터 65535(= 2**16 -1)번까지 사용됨
  - [0, 1023]번은 Well-Known Port : HTTP, SMTP, TELNET과 같은 주요 프로토콜이 할당받아 사용
- 리눅스 및 유닉스에서는 '/etc/services' 파일에서 주요 포트 번호를 확인할 수 있음

# IP 주소와 도메인
## IP 주소(Internet Protocol Address)
- 인터넷에서 다른 컴퓨터와 서로 인식하고 통신하기 위해 사용되는 특수한 번호
- 각 컴퓨터마다 고유한 값으로 제공됨

#### IPv4
- 32비트의 이진 숫자로 되어 있으며, 8비트씩 4부분으로 나누어 십진수로 표현
- 첫 8비트 영역의 값에 따라 A~E 클래스로 나움
<table>
<thead><td>클래스</td><td>주소 범위</td><td>설명</td></thead>
<tr><td>A 클래스</td><td>0.0.0.0 ~ 127.255.255.255</td><td>

- 첫 번째 영역을 네트워크 ID로 사용, 나머지 영역을 Host ID로 사용
- 네트워크 ID의 1bit가 '0'
- 네트워크 ID 0, 127은 자체 네트워크 용도와 Loopback 네트워크 주소를 나타내는 특수 용도
</td></tr>
<tr><td>B 클래스</td><td>128.0.0.0 ~ 191.255.255.255</td><td>

- 앞의 두 영역을 네트워크 ID로 사용, 나머지 영역을 Host ID로 사용
- 네트워크 ID의 2bit가 '10'
</td></tr>
<tr><td>C 클래스</td><td>192.0.0.0 ~ 223.255.255.255</td><td>

- 앞의 세 영역을 네트워크 ID로 사용, 나머지 영역을 Host ID로 사용
- 네트워크 ID의 3bit가 '110'</td></tr>
<tr><td>D 클래스</td><td>224.0.0.0 ~ 239.255.255.255</td><td>

- 첫 4bit가 '1110'
- 다중 방송 통신용으로 예약되어 있음
</td></tr>
<tr><td>E 클래스</td><td>240.0.0.0 ~ 255.255.255.255</td><td>

- 첫 4bit가 '1111'
- 연구용으로 사용
</td></tr>
</table>



#### IPv6(Internet Protocol version 6) 
- IPv4의 주소 고갈 문제 해결을 위해 제안된 차세대 인터넷 프로토콜 
 - 128비트를 16비트 단위로 하여 8자리로 주소 표현하며 각 자리는 :(콜론)으로 구분 
 - 16진수 값으로 표기 

<b>특징</b>

- IP 주소의 확장
- 패킷 크기의 확장
- 호스트 주소 자동 설정
- 헤더 구조 단순화
- 흐름 제어 기능 지원
- 인증 및 보안기능
- 이동성 : 네트워크의 물리적 위치에 제한받지 않고 같은 주소를 유지하면서 자유롭게 이동할 수 있음 

## 도메인
#### 도메인 네임(Domain Name)
 
- IP 주소를 알기 쉽게 영문으로 표기한 것
- 일반적으로 '컴퓨터이름.기관이름.기관종류.국가이름'으로 구성

#### DNS(Domain Name System)
- 인터넷상에서 컴퓨터 이름에 대한 주소 매핑 서비스를 제공하기 위해 개발됨
- 기존 유닉스 및 리눅스에서는 '/etc/hosts' 파일을 이용

#### 도메인 체계
- 도메인은 Root('.') 도메인 아래에 역트리 형태의 계층적 구조로 구성되어 있음
- 최상위 도메인(루트 도메인 바로 아래 1단계 도메인) : 일반 최상위 도메인 / 국가 코드 최상위 도메인

# 네트워크 환경
## 환경 설정
#### 리눅스 네트워크
리눅스에서 네트워크 서비스를 사용하려면 네트워크 하드웨어, 네트워크 프로토콜, 네트워크 서비스 프로그램을 순서대로 설치해야 함
- 하드웨어 설치 : Ethernet Card 모뎀, 시리얼/패러럴 케이블 등의 네트워크 하드웨어를 설치
- 프로토콜 설치 : SLIP, PPP, TCP/IP 등과 같이 네트워크 프로토콜을 설치
  - 리눅스는 네트워크를 기반으로 하는 운영체제로서 대부분의 프로토콜을 기본적으로 제공
- 서비스 프로그램 설치 : 웹 서비스를 위한 웹 서버, 메일 서버, FTP 서버 등 실제 사용할 네트워크 서비스 프로그램을 설치

리눅스에서 지원하는 네트워크 하드웨어
<TABLE>
<THEAD><TD>장치명</TD><TD>설명</TD></THEAD>
<TR><TD>lo</TD><TD>(Local Loopback) 물리적으로 존재하는 인터페이스가 아니라 가상으로 만들어진 네트워크 인터페이스 장치들은

- IP 주소는 127.0.0.1이 설정되고 자체적인 PING 테스트 등 네트워크 디버깅에 사용</TD></TR>
<TR><TD>ethx</TD><TD>CentOS 6 이전 버전에서 사용하는 이더넷 카드 인터페이스 장치(eth0, eth1 등)</TD></TR>
<TR><TD>enpx</TD><TD>CentOS 7 부터 사용하는 이더넷 카드 인터페이스 장치(enp0s3, enp1s0f0 등)</TD></TR>
<TR><TD>pppx</TD><TD>모뎀을 사용해서 네트워크를 사용할 경우 설정되는 PPP 장치(ppp0, ppp1 등)</TD></TR>
<TR><TD>trx</TD><TD>Token Ring을 사용할 경우 설정되는 네트워크 장치</TD></TR>
<TR><TD>fddix</TD><TD>FDDI 인터페이스를 사용할 경우 설정되는 네트워크 장치</TD></TR>
<TR><TD>virbr0</TD><TD>서버 가상화를 사용할 경우 가상 머신의 NAT 설정에 사용되는 네트워크 장치</TD></TR>
<TR><TD>xenbr0</TD><TD>xen 기반 서버 가상화를 사용할 경우 설정되는 네트워크 장치</TD></TR>
<TR><TD>docker0</TD><TD>Docker 기반의 서버 가상화를 사용할 경우 설정되는 네트워크 장치</TD></TR>
</TABLE>

#### 인터넷 접속을 위한 설정
인터넷에 접속하기 위해서는 IP 주소 설정이 필요<br>
네트워크 주소를 설정하기 위해서는 IP 주소, 넷마스크 주소, 게이트웨이 주소가 필수적으로 요구되며, 추가적으로 호스트 이름과 도메인 이름을 알아야 함

## 네트워크 관련 명령어

#### ip
이더넷 장치, IP 주소, 라우팅 정보 등의 설정 정보를 출력하거나 변경하는 명령

```
ip [options] 대상 [command]
```

주요 대상
<table>
<tr><td>address, addr, a, ad</td><td>IPv4 또는 IPv6 주소</td></tr>
<tr><td>route</td><td>라우팅 테이블 목록</td></tr>
<tr><td>link</td><td>네트워크 장치</td></tr>
</table>

주요 command
<table>
<tr><td>show, list</td><td>대상에 대한 정보 출력. 생략 시 기본값</td></tr>
<tr><td>add</td><td>대상에 대한 특정 정보를 추가하거나 설정</td></tr>
<tr><td>delete, del</td><td>대상에 대한 특정 정보를 삭제</td></tr>
</table>


```
# ip addr
IP 주소 정보를 출력

# ip link set eth1 up
네트워크 인터페이스를 활성화시킨다

# ip route del default via 192.168.5.1
게이트웨이 주소값을 삭제
```

#### ficonfig(interface config)
네트워크 인터페이스를 설정하거나 확인하는 명령<br>
IP 주소, 넷마스크 주소, MAC 주소 등을 설정하고 확인할 수 있음

```
ifconfig [interface] [address] [option]
```
예시
```
# ifconfig enp0s1 down
enp0s1 네트워크 장치의 작동을 중지시킨다

# ifconfig enp0s1 192.168.0.2 netmask 255.255.255.0 broadcast 192.168.0.255 up
enp0s1에 IP 주소는 192.168.0.2, netmask는 255.255.255.0, broadcast는 192.168.0.255를 부여하고 활성화시킨다

```

![ifconfig](https://github.com/dee021/TIL/blob/linuxmaster/Linux/img/ifconfig.png)

ifconfig의 주요 항목
<table>
<tr><td>flags</td><td>네트워크 인터페이스의 상태 정보

- UP : 인터페이스가 활성화되어 있음
- BROADCAST : 브로드캐스트를 사용함
- RUNNING : 인터페이스가 동작중
- MULTICAST : 멀티캐스트를 사용
</td></tr>
<tr><td>mtu</td><td>(Maximum Transmission Unit) 한 번에 전송할 수 있는 최대 패킷의 크기</td></tr>
<tr><td>inet/inet6</td><td>IPv4/IPv6 주소</td></tr>
<tr><td>netmask</td><td>넷마스크 주소값</td></tr>
<tr><td>broadcast</td><td>브로드캐스트 주소</td></tr>
<tr><td>ether</td><td>MAC 주소</td></tr>
<tr><td>RX/TX</td><td>수신 패킷수와 전송 패킷 관련 수</td></tr>
<tr><td>collisions</td><td>충돌이 발생한 패킷 수수</td></tr>
</table>

#### route
라우팅 테이블의 정보를 출력하거나 관리하는 명령<br>
네트워크 주소, 게이트웨이 주소를 확인하거나 설정할 때 사용

```
route [add|del] [-destination] [netmask 값] [gw 값] [dev 인터페이스]
```
![route](https://github.com/dee021/TIL/blob/linuxmaster/Linux/img/route.png)

route 주요 항목
<table>
<tr><td>Destination</td><td>목적지 네트워크 주소

- defualt : 목적지 네트워크로 등록되지 않은 다른 모든 네트워크 주소를 처리하는 곳
</td></tr>
<tr><td>Gateway</td><td>목적지 주소로 보내기 위한 게이트웨이 주소<br>보통 목적지가 자신의 네트워크 인터페이스와 직접 연결되어 있는 것이면 '*'로 표시되고 그러지 않으면 게이트웨이의 IP 주소가 입력됨</td></tr>
<tr><td>Genmask</td><td>목적지의 네트워크 주소를 알아내기 위한 넷마스크<br>IP 주소에 Netmask를 AND 연산자로 계산하면 목적지의 네트워크 주소를 알 수 있다.</td></tr>
<tr><td>Flags</td><td>목적지 네트워크와의 연결 상태

- U : 인터페이스가 사용되고 있음
- G : 라우트가 게이트웨이로 사용되고 있음
- H : 오직 단독 호스트만이 라우트를 거쳐서 접근할 수 있다. 이러한 경우의 루프백 엔트리는 127.0.0.1이 된다
- D : 테이블 엔트리가 설정된 경우, ICMP 리다이렉트 메시지로 운영된다
- M : 테이블 엔트리가 설정된 경우, ICMP 리다이렉트 메시지에 의해 수정되고 있음
</td></tr>
<tr><td>Metric</td><td>목적지 네트워크와의 거리(Hop count)<br>직접 연결되어 있으면 0</td></tr>
<tr><td>Ref</td><td>다른 라우터가 목적지로 가기 위해 참조한 횟수</td></tr>
<tr><td>Use</td><td>다른 라우터가 목적지로 가기 위해 사용한 횟수</td></tr>
<tr><td>Iface</td><td>목적지 네트워크와 연결된 인터페이스</td></tr>
</table>

#### netstat
네트워크의 연결 상태를 출력하는 명령<br>
네트워크 연결 상태 이외에도 라우팅 테이블 정보, 네트워크 인터페이스 상태, masquerade 연결 상태, 멀티캐스트 멤버 등의 정보를 출력

```
netstat [option] [address_family_option]
```

주요 옵션
<table>
<tr><td>-a</td><td>모든 소켓 정보 출력</td></tr>
<tr><td>-n</td><td>기호화된 호스트명이나 포트명 대신 숫자값으로 표시</td></tr>
<tr><td>-p</td><td>소켓에 대한 PID와 프로그램명을 출력</td></tr>
<tr><td>-r</td><td>라우팅 테이블 정보 출력</td></tr>
<tr><td>-l</td><td>대기하고 있는 Listen 포트를 출력</td></tr>
<tr><td>-i</td><td>네트워크 인터페이스 테이블 정보 출력</td></tr>
<tr><td>-s</td><td>네트워크 프로토콜에 대한 통계 정보 출력</td></tr>
<tr><td>-c</td><td>네트워크 정보를 계속적으로 출력</td></tr>
<tr><td>-t</td><td>TCP 프로토콜 기반으로 접속한 목록 출력</td></tr>
<tr><td>-u</td><td>UDP 프로토콜 기반으로 접속한 목록 출력</td></tr>
<tr><td>-g</td><td>멀티캐스트 그룹 멤버의 정보 출력</td></tr>
</table>

address family option
<table>
<tr><td>--protocol=값</td><td>inet, unix, ipx, ax25 등 특정 프로토콜과 연관된 정보 출력</td></tr>
<tr><td>--inet, --ip</td><td>IP 주소 기반으로 연결된 정보 출력. '--protocol=inet'과 같은 결과</td></tr>
<tr><td>--unix</td><td>UNIX Domain Socket에 대한 정보 출력. '--protocol=unix'</td></tr>
</table>

> <b>UNIX Domain Socket</b><br>
> 운영체제에서 실행되는 프로세스간의 데이터 교환을 하기 위해 사용되는 통신 연결포인트

netstat의 State 결과
<table>
<thead><td>값</td><td>내용</td></thead>
<tr><td>LISTEN</td><td>서버에서 클라이언트로부터 들어오는 패킷을 위해 소켓을 열고 기다리는 상태</td></tr>
<tr><td>SYS-SENT</td><td>로컬 시스템의 클라이언트 애플리케이션이 원격 호스트에 연결을 연결을 요청한 상태</td></tr>
<tr><td>SYN_RECEIVED</td><td>서버 시스템이 원격 클라이언트로부터 접속 요구를 받아 클라이언트에게 응답을 하였지만, 아직 클라이언트에게 확인 메시지를 받지 못한 상태</td></tr>
<tr><td>ESTABLISHED</td><td>3Way-Handskaking이 완료된 후 서버와 클라이언트가 서로 연결된 상태</td></tr>
<tr><td>FIN-WAIT1</td><td>소켓이 닫히고 연결이 종료되는 상태</td></tr>
<tr><td>FIN-WAIT2</td><td>연결은 종료된 상태이고 소켓이 원격 호스트로부터 종료되었다는 정보를 기다리고 있는 상태</td></tr>
<tr><td>CLOSE-WAIT</td><td>원격 호스트는 종료된 상태이고 소켓을 종료하기 위해 기다리는 상태</td></tr>
<tr><td>CLOSING</td><td>소켓 연결을 닫는 중. 주로 확인 메시지가 전송 도중 분실된 상태</td></tr>
<tr><td>TIME-WAIT</td><td>패킷 처리는 끝났지만 분실되었을지 모를 느린 세그먼트를 위해 당분간 소켓을 열어둔 상태</td></tr>
<tr><td>CLOSED</td><td>소켓 연결이 종료된 상태</td></tr>
<tr><td>LAST_ACK</td><td>원격 호스트가 종료되고 소켓도 닫힌 상태에서 마지막 ACK 패킷을 기다리는 상태</td></tr>
<tr><td>UNKNOWN</td><td>소켓 상태를 확인할 수 없는 상태</td></tr>
</table>

#### ss(socket statistics)
소켓 상태를 출력하는 명령

- 다른 도구에 비해 더 많은 TCP 및 상태 정보를 제공

```
ss [option] [필터]

# ss
소켓에 연결된 정보만 출력
```
필터 : 'state TCP-상태값 표현식' 형태

주요 option
<table>
<tr><td>-a</td><td>소켓의 모든 정보를 출력(--all)</td></tr>
<tr><td>-n</td><td>서비스명으로 resolving 하지 안고 숫자값으로 표시(--numeric)</td></tr>
<tr><td>-r</td><td>숫자값 형태의 주소나 포트 정보를 호스트명이나 서비스명으로 리졸빙해서 출력(--resolve)</td></tr>
<tr><td>-l</td><td>서비스 연결을 위해 대기 중인 소켓 정보를 출력(--listening)</td></tr>
<tr><td>-o</td><td>시간 관련 정보를 출력(--options)</td></tr>
<tr><td>-e</td><td>소켓 정보를 확장해서 자세히 출력(--extend)</td></tr>
<tr><td>-p</td><td>소켓을 사용 중인 프로세스 정보를 출력(--processes)</td></tr>
<tr><td>-i</td><td>시스템 내부의 TCP 정보를 출력(--info)</td></tr>
<tr><td>-s</td><td>소켓 관련 상태 정보를 요약해서 출력(--summary)</td></tr>
<tr><td>-4/-6</td><td>IPv4/IPv6 관련 정보만 출력(--ipv4/--ipv6)</td></tr>
<tr><td>-0</td><td>packet 소켓 정보를 출력(--packet)</td></tr>
<tr><td>-t/-u</td><td>TCP/UDP 관련 소켓 정보만 출력(--tcp/--udp)</td></tr>
<tr><td></td><td></td></tr>

</table>

#### arp
ARP(Address Resolution Protocol) 캐시를 관리하는 명령

ARP 캐시

- 현재 들어와 있는 호스트에 대한 정보를 담고 있음
- 15분 정도 쓰이지 않은 MAC 주소는 제거 

```
arp [option]
```

주요 옵션
<table>
<tr><td>-a</td><td>ARP 캐시에 기록된 정보 중 특정 호스트에 대한 정보를 출력</td></tr>
<tr><td>-d</td><td>ARP 캐시에서 해당 호스트 정보를 제거</td></tr>
<tr><td>-n</td><td>출력 정보를 도메인 주소 대신 IP 주소로 출력</td></tr>
<tr><td>-v</td><td>관련 정보를 자세히 출력</td></tr>
</table>

#### ethtool
이더넷 카드 설정 정보를 출력하거나 변경하는 명령

```
ethtool 네트워크_장치명
```

#### ping(packet internet groper)
ICMP을 이용하여 네트워크 연결을 확인하는 명령

- 원격의 호스트와 서로 연결이 되어 있는지 확인
- 패킷을 보내고 받는 시간을 출력하여 네트워크 장애 여부 확인

```
ping [option] Hostname_or_IPAddress
```

주요 옵션
<table>
<tr><td>-c 개수</td><td>몇 개의 패킷을 보낼 것인지 설정</td></tr>
<tr><td>-i 초</td><td>몇 초 간격으로 패킷을 보낼 것인지 설정. 기본값은 1초</td></tr>
<tr><td>-s 패킷크기</td><td>전송하는 패킷의 크기를 byte 단위로 설정. 기본값은 56bytes(header 8bytes 제외)</td></tr>
<tr><td>-w 초</td><td>패킷을 보내고 몇 초 후에 실행을 멈출 것인지 설정<br>
-c 옵션과 동시에 사용하면 둘 중 먼저 끝나는 값을 반영</td></tr>
</table>

#### traceroute
패킷이 특정 호스트까지 라우팅되는 과정을 출력

```
traceroute Hostname_or_IPAddress
```

#### nslookup
DNS를 이용하여 도메인이나 IP를 조회하는 명령

```
nslookup [option] Hostname_or_IPAddress
```

주요 옵션
<table>
<tr><td>-type='서버타입'</td><td>mx(메일 서버), ns(네임 서버) 등 조회 가능</td></tr>
</table>

![nslookup](https://github.com/dee021/TIL/blob/linuxmaster/Linux/img/nslookup.png)

> `nslookup` 명령만 입력하면 대화형 모드로 접속
