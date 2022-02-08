# 웹서버 VS WAS
10분 테코톡  희봉님의 `웹서버 VS WAS` 영상을 참고하여 정리한 글입니다.

[[10분 테코톡] 👩‍🦰희봉의 웹서버 vs WAS](https://www.youtube.com/watch?v=NyhbNtOq0Bc&ab_channel=%EC%9A%B0%EC%95%84%ED%95%9CTech)

## Web
- 인터넷을 기반으로 하여 정보를 공유하고 검색할 수 있는 서비스
- URL(주소), HTTP(통신 규칙), HTML(내용)

## Web Server

웹 브라우저(Client)로부터 HTTP 요청을 받아 HTML 문서와 같은 **정적 컨텐츠**를 제공하는 서버

> 어떠한 경우에도 달라지지 않는 컨텐츠(html, css, image)를 **정적 컨텐츠**라고 한다.


## WAS(Web Application Server)
### 특징
- 클라이언트의 HTTP 요청을 받을 수 있음. (대부분의 WAS는 Web Server 내장)
- 요청의 요구사항에 따라 정적 컨텐츠, 동적 컨텐츠 제공이 가능하다.
- DB 접속이나 비즈니스 로직 처리를 통한 **동적 컨텐츠** 제공이 가능하다.
- WAS는 Web Server와 Web Container로 구성되어 있다.

> Client의 요청에 따라 변할 수 있는 컨텐츠를 **동적 컨텐츠**라고 한다.

### 동작 과정

![](https://images.velog.io/images/frankle97/post/67b7fe15-b3e6-443b-9d5d-63099fb23e7e/image.png)

1. Client가 WAS로 HTTP 요청을 보냄
2. WAS에 내장된 Web Server에서 요청에 대해 정적인지 동적인지 파악함.
3. 동적 페이지 요청이라면, Web Container로 전송
4. Servlet 실행 및 필요 시 프로그래밍 된 동적 컨텐츠 생성
5. 가공된 컨텐츠를 Web Server에게 전달
6. 최종적으로 Web Server는 가공된 정보를 Client에게 제공

# Web Server, WAS 차이

**Web Server** : 오로지 **정적 컨텐츠**만 제공이 가능하다.

**WAS** : 요청에 따라 변할 수 있는 정보, 즉 **동적 컨텐츠** 제공이 가능하다.


# 그럼 WAS만 사용해도 되는가?
WAS만으로도 웹 서비스는 가능하지만 효율적인 방법은 아니다.
오로지 WAS만 사용하게 된다면, 정적/동적 컨텐츠 요청을 함께 처리하게 되면서 부하가 커지고 동적 컨텐츠 처리가 지연이 된다면 수행 속도가 느려지고 이로 인해 페이지 노출 시간이 늘어나는 문제가 발생할 수 있고 **효율성이 떨어지게 된다.**

WAS는 DB 조회 및 다양한 로직을 처리하는 데 집중해야 한다.
단순 정적 컨텐츠는 Web Server로 동적 컨텐츠는 WAS로 **책임을 분할하여 서버 부하를 방지**하는 것이 좋다.


> Web Server를 Front에 두고 필요한 WAS들을 Web Server에 플러그인 형태로 구조하면 효율적인 분산처리가 가능하다.
로드밸런싱을 통해 여러 WAS 동시 운영이 가능하며, 필요 시 Sacle-out도 편하다.


