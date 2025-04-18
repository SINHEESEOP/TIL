# 🌐 인터넷 통신과 네트워크 기초

## 📡 인터넷 통신

### 인터넷 통신이란?
인터넷 통신은 서로 다른 컴퓨터나 디바이스가 인터넷을 통해 데이터를 주고받는 과정입니다. 모든 인터넷 통신은 클라이언트와 서버 간의 요청과 응답을 기반으로 이루어집니다.

### 동작 원리
1. 클라이언트(사용자 컴퓨터)가 서버에 요청을 보냄
2. 서버가 요청을 처리하고 응답을 생성
3. 서버가 클라이언트에게 응답을 전송
4. 클라이언트가 응답을 받아 처리

### 📊 프로토콜 계층
인터넷 통신은 여러 계층의 프로토콜로 구성됩니다:
- 애플리케이션 계층 (HTTP, FTP, DNS 등)
- 전송 계층 (TCP, UDP)
- 인터넷 계층 (IP)
- 네트워크 인터페이스 계층

## 🔄 IP, TCP, UDP, PORT, DNS

### IP (Internet Protocol)
- IP는 인터넷 상에서 데이터 패킷의 전달을 책임지는 프로토콜입니다.
- 각 장치는 고유한 IP 주소를 가지며, 이를 통해 데이터가 올바른 목적지로 전달됩니다.
- 특징:
  - 비연결성: 패킷을 받을 대상이 없거나 서비스 불능 상태여도 패킷을 전송
  - 신뢰성 없음: 중간에 패킷이 사라지거나 순서가 바뀔 수 있음
  - 패킷 단위로 데이터 전송: 큰 데이터는 작은 패킷으로 나누어 전송

### TCP (Transmission Control Protocol)
- IP의 한계를 보완하는 프로토콜로, 신뢰성 있는 데이터 전송을 보장합니다.
- 특징:
  - 연결지향: 3-way handshake로 연결 수립
  - 데이터 전달 보증: 데이터 전송 여부 확인 가능
  - 순서 보장: 패킷이 순서대로 도착하도록 보장
  - 흐름 제어: 데이터 양 조절
  - 혼잡 제어: 네트워크 상황에 따른 데이터 전송 속도 조절

### UDP (User Datagram Protocol)
- TCP보다 단순하고 빠른 프로토콜입니다.
- 특징:
  - 비연결성: 연결 설정 없이 데이터 전송
  - 데이터 전달 및 순서 보장 없음
  - 단순하고 빠름
  - 적은 오버헤드
  - 실시간 스트리밍, 게임 등에 사용

### PORT
- 하나의 IP 내에서 여러 애플리케이션을 구분하는 번호입니다.
- IP가 아파트라면, 포트는 각 집의 호수입니다.
- 주요 포트 번호:
  - 0-1023: 잘 알려진 포트 (HTTP:80, HTTPS:443, FTP:21 등)
  - 1024-49151: 등록된 포트
  - 49152-65535: 동적 포트

### DNS (Domain Name System)
- IP 주소를 사람이 기억하기 쉬운 도메인 이름으로 변환해주는 시스템입니다.
- 작동 방식:
  1. 사용자가 도메인 이름 입력 (예: www.google.com)
  2. DNS 서버에 해당 도메인의 IP 주소 요청
  3. DNS 서버가 IP 주소 응답 (예: 142.250.196.110)
  4. 해당 IP 주소로 통신 