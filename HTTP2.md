# HTTP/2
> [google docs](https://developers.google.com/web/fundamentals/performance/http2?hl=ko#spdy_%EB%B0%8F_http2%EC%9D%98_%EA%B0%84%EB%9E%B5%ED%95%9C_%EC%97%AD%EC%82%AC)

HTTP/2의 변화과정에 대해서 공부하도록 하겠습니다.

HTTP/1에서 버전 /2로 넘어오면서 크게 바뀐 점은 3가지 정도가 있습니다.

1. HTTP 헤더 압축

2. Multiplexing

3. Server push


## HTTP/1.2는 왜 안되나요?
> 들어가기 앞서 버전을 올린 이유에 대해서 구글측의 설명

HTTP Working Group이 규정한 성능 목표를 실현하기 위해 HTTP/2에서는 새 바이너리 프레이밍 계층을 도입합니다. 이 계층은 이전의 HTTP/1.x 서버 및 클라이언트와 호환되지 않으며 따라서 주 프로토콜 버전은 HTTP/2로 올라갑니다.

## 바이너리 프레이밍 계층
> HTTP/2의 모든 성능 향상 중 핵심은 `Binary Framing layer`입니다.

해당 계층을 통해 HTTP 메시지가 캡슐화 되어 client-server 사이에서 선송되는 방식을 규정합니다.

![](./img/binary_framing_layer001.svg)

해당 계층은 Application에 노출되는 Socket interface와 더 상위 HTTP API 간에 최적의 Encoding 메커니즘을 도입하기 위해 도입되었습니다.

일반 텍스트로 동작하더 HTTP/1.x 프로토콜은 줄바꿈을 통해 구분이 되었던 반면, HTTP/2 통신은 더 작은 메시지와 프레임으로 분할한 뒤, 이를 바이너리 형식으로 인코딩 시킵니다.

다행히 모든 프레이밍 작업을 브라우저가 수행해주며 서버 또한 대부분 엔진에서 처리해 줍니다.

## 용어 정리 / 규칙

새 바이너리 프레이밍 메커니즘이 도입됨에 따라 클라이언트와 서버 간에 데이터 교환 방식이 바뀌었습니다. 이 과정을 설명하기 위해 HTTP/2 용어를 먼저 익혀보겠습니다.

- 스트림
  - 구성된 연결 내에서 전달되는 바이트의 양방향 흐름이며, 하나 이상의 메시지가 전달될 수 있습니다.
- 메시지
  - 논리적 요청 또는 응답 메시지에 매핑되는 프레임의 전체 시퀀스입니다.
- 프레임
  - HTTP/2에서 통신의 최소 단위이며 각 최소 단위에는 하나의 프레임 헤더가 포함됩니다. 이 프레임 헤더는 최소한으로 프레임이 속하는 스트림을 식별합니다.

해당 용어들간의 관계를 설명드리겠습니다.

1. 모든 통신은 단일 TCP 연결을 통해 수행되며 전달될 수 있는 양방향 스트림의 수는 제한이 없습니다.
2. 각 스트림에는 양방향 메시지 전달에 사용되는 고유 식별자와 우선순위 정보(선택 사항)가 있습니다.
3. 각 메시지는 하나의 논리적 HTTP 메시지(예: 요청 또는 응답)이며 하나 이상의 프레임으로 구성됩니다.
4. 프레임은 통신의 최소 단위이며 특정 유형의 데이터(예: HTTP 헤더, 메시지 페이로드 등)를 전달합니다. 다른 스트림들의 프레임을 인터리빙한 다음, 각 프레임의 헤더에 삽입된 스트림 식별자를 통해 이 프레임을 다시 조립할 수 있습니다.

![](./img/streams_messages_frames01.svg)

![](./img/multiplexing01.svg)


## Request and response `multiplexing`

HTTP 메시지가 많은 개별 프레임으로 분할될 수 있고 여러 스트림의 프레임을 다중화하는 것이 가능해짐에 따라, 프레임이 클라이언트와 서버에 의해 인터리빙되고 전달되는 순서가 중요한 성능 고려사항이 되었습니다. 이를 용이하게 하기 위해 HTTP/2 표준에서는 각 스트림이 연관된 가중치와 종속성을 갖도록 허용합니다.

In short, HTTP/2 breaks down the HTTP protocol communication into an exchange of binary-encoded frames, which are then mapped to messages that belong to a particular stream, all of which are multiplexed within a single TCP connection. This is the foundation that enables all other features and performance optimizations provided by the HTTP/2 protocol.



## One connection per origin
> 출처당 하나의 연결

With the new binary framing mechanism in place, HTTP/2 no longer needs multiple TCP connections to multiplex streams in parallel

새 바이너리 프레이밍 메커니즘이 배치되면, 스트림을 병렬로 다중화하는 여러 개의 TCP 연결이 더 이상 HTTP/2에 필요 없습니다. 각 스트림은 여러 프레임으로 분할되며 각 프레임이 인터리빙되고 우선순위가 지정될 수 있습니다. 

As a result, all HTTP/2 connections are persistent, and only one connection per origin is required, which offers numerous performance benefits.

따라서, 모든 HTTP/2 연결은 영구적이고 출처당 하나의 연결만 필요하며 이 경우 성능상의 수많은 이점이 있습니다.

대부분의 HTTP 전송은 수명이 짧고 폭주하는 반면 TCP는 수명이 긴 대량 데이터 전송에 최적화되어 있습니다. HTTP/2에서는 동일한 연결을 재사용하여 각 TCP 연결을 더 효율적으로 사용할 수 있으며 또한 전반적인 프로토콜 오버헤드를 대폭 줄일 수 있습니다. 또한 더 적은 연결을 사용하므로 전체 연결 경로(즉, 클라이언트, 중개 장치 및 원본 서버)에서 메모리와 처리량이 줄어듭니다. 그 결과 전체 운영 비용이 절감되고 네트워크 활용도와 용량이 개선됩니다. 따라서 HTTP/2로 전환하면 네트워크 지연 시간이 줄어들 뿐만 아니라 처리량이 개선되고 운영 비용이 줄어듭니다.

참고: 연결 수가 적다는 것은 HTTPS 배포의 성능을 개선하는데 특히 중요합니다. 연결 수가 적으면 값비싼 TLS 핸드셰이크가 줄어들고, 세션 재사용이 더 향상되며, 필요한 클라이언트 및 서버 리소스가 감소합니다.

