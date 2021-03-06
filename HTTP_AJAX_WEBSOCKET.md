# HTTP, AJAX, WEBSOCKET 
> [참고자료](https://medium.com/@chullino/http%EC%97%90%EC%84%9C%EB%B6%80%ED%84%B0-websocket%EA%B9%8C%EC%A7%80-94df91988788)

## TL; DR
- HTTP는 통신 제약이 있는 약속입니다.
- AJAX로 HTTP의 통신 제약으로부터 조금 벗어날 수 있었습니다.
- Websocket은 HTTP의 통신 제약을 해결한 새로운 약속입니다.

## 기존의 HTTP (Naive HTTP)

HTTP에서 Transfet Protocol의 대 전제는 "URL및 부가정보를 통해 사용자가 원하는 페이지를 서버에 요청하고 서버는 해당 요청에 응답한다." 입니다. 이는 기존에 브라우저가 웹서버에 무언가를 요청하기 위해서는 URL을 통해서 페이지를 이동했어야만 한다는 것을 뜻합니다.

이러한 HTTP 규약을 뛰어넘기 위해 `AJAX`를 사용하기 시작합니다.
HTTP가 protocol이라면 AJAX는 효과적으로 서버와 소통하기 위한 기술입니다.

기존에는 요청이 들어왔다면 이 요청에 대해 서버에서 새롭게 HTML을 생성하여 전송해주어 client에서는 페이지가 이동한것 처럼 보이게 만들었다면, Ajax기술은 동일한 웹페이지 내에서 DOM을 변경하게 됩니다. 즉 client 측에서 새로운 값을 작성했다면 JS에서 해당 이름과 내용이 쓰여진 DOM을 읽어 들이고, XMLHttpRequest 객체를 통해 웹서버에 변경된 내용과 tag를 전송합니다. 이후 웹서버는 요청을 처리하고 XML, Text, JSON을 통해 XMLHttpRequest 객체에 전송합니다. 그러면 JS가 해당 응답 정보를 DOM에 rewrite 함으로써 결과 페이지가 만들어집니다. **즉 AJAX를 사용하면 HTML 페이지 전체를 바꿔주는 것이 아닌, 부분적인 DOM만 바꿀 수 있게 되는 것입니다.**

참고로 XMLHttpRequest는 서버와의 연결을 잡아둡니다. 그렇기 때문에 필요한 데이터를 정확한 위치로 서버가 클라이언트로 제공할 수 있습니다.

## Naive HTTP vs AJAX

1. 페이지 전체를 변경?
   - Naive HTTP: 전체 변경
   - AJAX: 부분 변경 (with json, xml, ...)
2. request 담당자
   - HTTP: 웹브라우저가 서버에 request
   - AJAX: XMLHttpRequest 객체가 서버에 request
3. 페이지 Update시, 페이지 이동 여부
   - HTTP: 항상 새롭게 페이지 생성
   - AJAX: 조그마한 변경시, 현재 페이지 내에서 변경 가능


## AJAX의 한계와 Web Socket의 등장
AJAX또한 `요청->응답`의 HTTP 틀에서 벗어나지 못하였기 때문에, **클라이언트의 요청이 없음에도, 서버로부터 응답을 받는 상황** 즉 **서버는 응답밖에 할 수 없었다.**(양방향 통신이 아닌 단방향 통신)이라는 한계점이 존재하였습니다.

이를 해결하기 위해 `Comet` (a.k.a `Long Polling`)이 등장하였고 이를 통해 "클라이언트의 요청이 없음에도 서버로부터 응답을 받는 상황"을 만들어 낼 수 있었지만 `PUSH`방식이 아닌, 클라이언트가 서버에게 요청하는 `Polling`방식이었습니다.

이런 애로사항은 HTML5 개발 과정에 녹아들었고, HTML5은 순수 웹 환경에서 실시간 양방향 통신이 가능해지도록 `Web Socket`을 도입하였습니다.

## Web Socket
> 웹소켓은 브라우저와 서버가 양방향 통신을 할 수 있도록 지원하는 프로토콜입니다.

웹소켓에서는 서버와 브라우저 사이에 양방향 소통이 가능합니다.

   - 브라우저는 서버가 PUSH해주는 데이터를 받을 수 있습니다.
   - 웹사이트로 이동하지 않아도, 최신 데이터가 적용된 웹을 볼 수 있습니다.
   - **기존의 `새로고침`하거나, 다른 주소로 이동을 통해서만 새로운 데이터를 제공하는 웹서비스 환경의 본질적인 문제를 풀어준 방법**

