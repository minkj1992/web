# websocket 이란
> [websocket 이란](https://duckdevelope.tistory.com/19)

`WebSocket`이란 HTML5에서 도입된 Transport protocol의 일종입니다. 쉽게 이야기하면 WEB 버전의 TCP 또는 Socket입니다.


## 특징

- 양방향 통신 가능 (Duplex)
- Stateful
  - 서버와 클라이언트 간에 Socket Connection을 유지
  - 이와 달리 HTTP는 Request/Response 기반의 Stateless Protocol이며, 단방향 통신(클->서버)이다.
  - TCP 연결을 지속하기 때문에, HTTP와 TCP 연결에 의한 트래픽을 피할 수 있다.
- `refresh`없이도 클라이언트 측 데이터 변경 가능(PUSH)
- HTTP와 같은 80포트를 사용하기 때문에, 방화벽을 재설정 하지 않아도 된다.
- Header가 상당히 작아 Overhead가 적다.
- 장시간 접속을 전제로 하기 때문에 접속한 상태라면 클라/서버에 데이터 송/수신 가능하다.
- **송/수신 각각 커넥션을 맺을 필요없이 하나의 커넥션으로 기능을 수행한다.**
- 통신시 지정되는 URL은 `http://www.sample.com/`같은 형식이 아닌 `ws://www.sample.com/`같은 형식이 된다.

## 연결 과정

서버와 클라이언트 간의 WebSocket 연결은 최초 HTTP 프로토콜을 통해 이루어집니다. Established 되면 이후 통신 과정은 WebSocket의 독자적인 프로토콜을 사용합니다. 

## Usage


1. 실시간 양방향 데이터 통신이 필요한 경우.

2. 많은 수의 동시 접속자를 수용해야 하는 경우.

3. 브라우저에서 TCP 기반의 통신으로 확장해야 하는 경우.

4. 개발자에게 사용하기 쉬운 API가 필요할 경우.

5. 클라우드 환경이나 웹을 넘어 SOA(Service Oriented Architecture) 로 확장해야 하는 경우


## TCP vs WebSocket
> 아직은 정확하게 이해가 되지는 않는다.  [출처](https://www.it-swarm.dev/ko/sockets/tcp-%EC%86%8C%EC%BC%93%EA%B3%BC-%EC%9B%B9-%EC%86%8C%EC%BC%93%EC%9D%98-%EC%B0%A8%EC%9D%B4%EC%A0%90/1073172973/)

WebSocket은 기본적으로 응용 프로그램 프로토콜 (ISO/OSI 네트워크 스택 참조)이며 메시지 지향적이며, TCP 를 전송 계층으로 사용합니다.

WebSocket 프로토콜의 기본 개념은 클라이언트와 서버간에 설정된 TCP 연결을 재사용하는 것입니다. HTTP 핸드 셰이크 후 클라이언트와 서버는 WebSocket 엔벨로프를 교환하여 WebSocket 프로토콜 말하기를 시작합니다. HTTP 핸드 쉐이킹은 일부 서비스를 제공하는 클라이언트와 서버 간의 장벽 (예 : 방화벽)을 극복하는 데 사용됩니다 (보통 포트 80은 어느 곳에서나 누구나 액세스 할 수 있음). 클라이언트와 서버는 언제든지 TCP 연결 (해제되지 않은)을 사용하여 말하기 HTTP를 전환 할 수 있습니다.

무대 뒤에서 WebSocket은 일관된 봉투/메시지로 TCP 프레임을 다시 작성합니다. 전이중 채널은 비동기 방식으로 클라이언트에 대한 Server to Push 업데이트에서 사용됩니다. 채널이 열려 있고 클라이언트가 미래를 호출 할 수 있습니다/callbacks/prompes는 비동기 WebSocket 수신 메시지를 관리합니다.

간단히 말해서 WebSocket은 TCP (프레임 당 신뢰할 수있는 전송 계층)에 구축 된 높은 수준의 프로토콜 (HTTP 자체와 같은)로 JS Client를 사용하여 효과적인 실시간 응용 프로그램을 구축 할 수 있습니다 (이전 혜성과 롱 폴링 기술을 사용하여 WebSocket을 구현하기 전에 서버에서 업데이트 풀을 사용했습니다. 스택 오버플로 게시물 : 웹 소켓과 턴 기반 게임 서버의 긴 폴링의 차이점 ).

