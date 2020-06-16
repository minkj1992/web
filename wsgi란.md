# WSGI
> Web Server Gateway Interface

## WSGI란
> 분류: 미들웨어, WAS

웹 서버와 웹어플리케이션이 어떤 방식으로 통신하는지에 대한 방식을 정의한 프로토콜(인터페이스)을 정의해주며, 해당 인터페이스를 돌려주는 프레임워크들과 비슷한 맥락에서 사용된다. 관련 용어(기능)로는 `어플리케이션 인터페이스`, `미들웨어 인터페이스`이 있다.

## WSGI 종류
- Bjoern
- uWSGI
- mod_wsgi
- Gunicorn
  - Unix용 Python WSGI HTTP Server이다


## uWSGI
WSGI 규격에 맞춘 코드를 실행해주는 프로그램의 일종이다.

- 기능 요약
  - WSGI 한 종류로 호스팅 서버를 구축할 수 있는 Full Stak 지원
  - 타 언어나 플랫폼을 다룰 수 있도록 `Pluggable architecture`지원 (C, C++ 등)


## 참고자료
- [uWSGI 제대로 배워보자](https://www.oss.kr/storage/app/public/oss/31/ce/[uWSGI]%20Solution%20Guide%20V0.96.pdf)