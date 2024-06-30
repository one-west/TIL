# 네트워크-DMZ, NAT, VPN의 이해

> Kisa 아카데미 [정보보호제품군 실습훈련 (기초)] 수강 후 내용 정리

## 목록

- [DMZ(Demilitarized Zone)](#dmz-demilitarized-zone))
- [NAT(Network Address Translation)](#nat-network-address-translation)
- [VPN(Virtual Private Networking)](#vpn-virtual-private-networking)

## DMZ(Demilitarized Zone)

### DMZ란?

- 네트워크에서 내부 네트워크와 외부 네트워크 간의 중간 지점에 위치한 보안 구역을 의미

- DMZ는 외부 네트워크에서 내부 네트워크로 접근을 제한

- 내부 네트워크에서 DMZ로의 접근을 통제

- DMZ는 외부에서 접근 가능한 서비스, 즉 웹서버들을 두고 외부와의 통신을 처리

- 방화벽은 내부 네트워크와 DMZ, DMZ와 뵈부 네트워크 간의 통신을 제어

    <img src="https://mblogthumb-phinf.pstatic.net/MjAyMTAyMTdfMjgx/MDAxNjEzNTQ3OTM5MDg5.WAcu5v6oiPKyxBkkUGPINLPU_8Pz3B1XM5Ubo3XRfy4g.RNle822nYqBRCzQHI6qIynQv5q6PEMxTKw1PTXOu5Ccg.PNG.innoviss/image.png?type=w800">

### DMZ 네트워크 망

- 외부 인터넷에서 들어오는 트래픽은 기본적으로 방화벽으로 향하며, 이 트래픽은 방화벽으로 보호되고 있는 내부망 혹은 DMZ에 위치한 시스템들로 라우팅 됨

- DMZ의 시스템들과 내부망 시스템들의 네트워크 또한 방화벽을 지나며, 특별히, 웹방화벽(WAF)를 추가적으로 두어 웹서버를 보호함

    <img src="https://w7.pngwing.com/pngs/32/151/png-transparent-dmz-computer-network-virtual-desktop-infrastructure-computer-servers-system-citrics-computer-network-angle-text-thumbnail.png" width="600">

### DMZ (Demilitarized Zone) 구성

- DMZ에 외부에서 접근 가능한 서비스를 결정해야 함 (웹서버, 메일 서버, FTP 서버 등)

- DMZ 서버들은 내부 네트워크와도 물리적으로 분리

- DMZ 서버들은 내부 네트워크와도 물리적으로 분리

- DMZ와 내부 네트워크 간의 데이터 통신을 제어하는 방화벽 등의 보안 장비 사용

- 보안 장비를 사용하여 취약점 진단을 수행하고, 보안 대응 실시

- DMZ 내 보안 정책 및 규칙 설정

- DMZ 내 서버들의 O/S, 어플리케이션 등의 보안 패치와 업그레이드. 패스워드 변경 등의 주기적인 관리

- DMZ 내부에 있는 서버 및 장비들을 실시간 모니터링하고, 고장 발생 시 즉각 조치가 가능하도록 계획 수립

- DMZ 내 서버들의 로그를 수집하고, 보안 이벤트를 모니터링하고 분석

- DMZ에 위치한 서버들을 고가용성 (High Availability)을 보장해야 하며, 이를 위해 부하 분산 장비 (L4 스위치 등) 등을 이용하여 서비스 가용성 유지

- DMZ 내부에서는 서비스 제공에 영향을 미칠 수 있는 문제를 예방하기 위해 백업 및 복원 계획을 수립하고, 주기적으로 백업을 수행

- DMZ 내부에서는 서버들의 무단 접근, 서비스 장애 등의 문제가 발생할 경우 신속하게 대처하기 위한 대응 계획을 수립하고, 이를 주기적으로 검토하고 개선

- DMZ 내부에서는 보안 강화를 위해 웹방화벽, IPS(침입 차단 시스템), IDS(침입 차단 시스템) 등의 보안 장비를 활용하여 보안을 강화

- DMZ 보안을 유지하기 위해서는 보안 전문가들의 지속적인 교육과 훈련이 필요하며, 보안 인식 제고를 위한 보안 교육이나 행사 등을 진행


### DMZ 사용 목적

- **보안 강화**

    - DMZ는 외부에서 내부로의 직접적인 접근을 차단하고, 내부 네트워크를 보호

- **서비스 분리**

    - DMZ에는 외부에서 접근 가능한 서비스들이 위치하므로, 내부 네트워크와 외부 네트워크 간의 서비스를 분리 시키고, 보안을 강화
 
    - 특히, 외부에서 접근 가능한 웹 서버는 DMZ에 위치시키고, 웹 서비스 통신은 DMZ를 통해 서비스 제공
 
- **외부 공격 대응**

    - DMZ에는 외부에서 내부로 직접 접근할 수 없게 하여, 외부 공격에 대한 대응 능력 강화
   
    - 웹방화벽(WAF), IPS/IDS(침입 차단 / 탐지 시스템) 등의 보안 장비 설치
 

## NAT (Network Address Translation)

### NAT (네트워크 주소 변환) 란?

- 내부망의 사설 IP 주소를 외부망의 공인 IP 주소로 바꿔주는 기술

- 사설 네트워크에 존재하는 여러 개의 호스트가 하나의 공인 IP 주소를 사용하여 인터넷에 접속하기 위해 사용

    <img src="https://images.spiceworks.com/wp-content/uploads/2023/04/04094918/Network-Address-Translation.jpg" width="">

### NAT의 특징

- 인터넷의 공인 IP 주고는 한정되어 있기 때문에 가급적 이를 공유할 수 있도록 하는 것이 필요

- NAT를 이용하면 사설 IP 주소를 사용하면서 이를 공인 IP 주소와 상호 변환할 수 있도록 하여, 공인 IP주소를 다수의 호스트가 함께 사용할 수 있음

- 외부 공격으로부터 내부 네트워크망을 보호하기 위해서 공개된 인터넷과 사설망 사이에 방화벽을 설치하여 활용 가능

- 인터넷말과 연결하는 장비인 라우터에 NAT를 설정할 경우 라우터는 자신에게 할당된 공인 IP 주소로만 외부로 알려지게 하고, 내부의 사설 IP주소를 사용하는 호스트를 보호함

- 외부 침입자가 공격하기 위해서는 사설망의 내부 사설 IP주소를 알아야 하기 때문에 공격이 어렵게 내부 네트워크를 보호할 수 있는 효과가 있음

### NAT의 장점

- IP 주소 부족 문제 해결

- 보안 강화

- 호환성 유지

- 효율적인 네트워크 관리

### NAT의 단점

- 애플리케이션 호환성 문제

- 통신 시간 지연

### NAT 핵심 기능

- IP Masquerading : 외부 인터넷 자원을 공유할 수 있는 기능

- Port Forwarding : 네트워크 연결이 들어오면 해당 연결을 내부 네트워크의 특정 서버의 포트로 연결 시켜주는 기능

- Load Balancing : 트래픽을 여러 대의 서버로 분산 처리 하여 부하를 해결하는 기술

### NAT의 종류

- Static NAT = 1 : 1

- Dynamoc NAT = m : n

- PAT (Port Adaress Translation) = 1 : n
  
- LS NAT (Load Sharing NAT) = 1 : n

## VPN (Virtual Private Networking)

### VPN (가상사설망) 이란?

- 가상 사설망을 의미하여, 이는 인터넷 트래픽을 암호화하여 통신하는 방식

- 온라인 상의 안전한 통신 채널을 생성

- 외부에서 내부로 접속하는 것을 막아 데이터를 보호

### VPN의 특징

- 인터넷에서 웹사이트에 접근하려고 하면 ISP (인터넷 서비스 공급자)가 요청을 수신하고 목적지로 리디렉션 (재설정) 함

- ISP가 인터넷에서 접근하려는 사용자에 대한 정보 조회가 가능하기에 보편적으로 이를 방지하기 위해 VPN을 사용

- VPN은 특별히 구성된 VPN 서버를 통해, 사용자의 인터넷 트래픽을 리디렉션하고, IP 주소를 숨기고, 보내거나 받는 모든 데이터를 암호화 함

- 암호화된 데이터는 중간에 읽을 수 없게 되므로 사용자의 정보를 보호 할 수 있게 됨

- VPN은 인터넷 상에서 암호화된 '터널'을 생성하여 온라인 뱅킹 계좌, 동영상 공유 웹사이트, 검색 엔진에 이르기까지 사용자와 사용자의 인터넷 목적지 사이에 이동하는 모든 데이터를 보호

### VPN 기능

- 보안성 향상

- 지리적 제약 극복

- 외부 공격 차단

- 비용 절감

- 이동성 제공

- 사용자 인증 및 접근 제어

- 다양한 프로토콜 지원

- IP 추적 차단

- 차단된 웹 사이트 접근

### VPN 터널링 프로토콜 종류 

- OpenVPN

- IPSec/IKEv2

- WireGuard

- SSTP

- PPTP

- L2TP/IPSec

### VPN 분할 터널링 (Split Turnneling)

- VPN에 연결하거나 연결하지 않을 앱을 지정할 수 있는 고급 VPN 기능

- 보안을 확보하고 싶은 앱은 VPN 연결을 설정해 보안 터널로 안전하게 보호

- 빠른 연결 속도가 필요한 앱은 VPN에 연결하지 않도록 지정할 수 있음

    <img src="https://nordvpn.com/wp-content/uploads/JdsorK6E.png">
