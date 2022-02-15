# CORS

10분 테코톡 나봄님의 `CORS` 영상을 보고 정리한 글입니다.

![image](https://user-images.githubusercontent.com/61595394/153692428-2eeed5ed-7718-4517-aa43-c875917c5617.png)

개발자라면 한번쯤은 위와 같은 에러를 마주했을 것이다.<br/>
여기서 지칭하는 CORS가 도대체 어떤 에러인지 오늘 한번 파헤쳐보고자 한다.

## SOP (Same Origin Policy)

먼저 CORS를 알기 이전에, SOP에 대해 알아보자.

SOP는 다른 출처의 리소스를 사용하는 것을 제한하는 보안 방식이다.

여기서 출처는 URL의 `Protocol`, `Host`, `Port`로 구분한다.

`http://github.com:80`를 예시로 들자면 **http** : 프로토콜, **github.com** : 호스트, **80** : 포트

그럼 **SOP**는 동일한 프로토콜, 호스트, 포트의 리소스만 허용한다는 것인데.. 가상의 시나리오를 들어보겠다.

```
1. 클라이언트가 페이스북에 로그인(토큰 발급)을 한다.
2. 해커가 클라이언트에게 `http://hacker.ck`라는 URL을 보냄.
3. 클라이언트가 해커가 보낸 링크가 흥미로워서 클릭함.
4. 해커가 보낸 페이지에는 클라이언트의 토큰을 이용하여 개인정보를 탈취하는 스크립트가 포함되어 있음.
```

생각해보자. 페이스북 서버가 토큰만으로 사용자를 판단한다면.. `클라이언트`와 `해커`의 요청을 구분하기 힘들 것이다.

반면에 요청 URL을 확인한다면 **클라이언트**의 출처는 `http://www.facebook.com/~`이고 **해커**의 출처는 `http://hacker.hc`일테니 서로 다른 출처라는 것을 확인하여 악의적인 요청을 방지할 수 있다.

위와 같은 상황, 요청의 출처가 다르다면 `Cross Origin` SOP에 위반이라고 한다.

하지만 외부 라이브러리도 사용해야 하는데 요청과 리소스를 매번 동일한 출처로만 받을 수는 없다.

이 때, 필요한 것이 `CORS`이다.

## CORS (Cross Origin Resource Sharing)
CORS는 다른 출처의 자원을 공유하는 것이다.

MOZILLA에서는 CORS를 아래와 같이 설명하고 있다.

```
교차 출처 리소스 공유는 추가 HTTP 헤더를 사용하여, 한 출처에서 실행 중인 웹 어플리케이션이 
다른 출처의 선택한 자원에 접근할 수 있는 권한을 부여하도록 브라우저에게 알려주는 체제이다.
```

CORS 접근제어에 사용되는 3가지의 시나리오가 있다.

- 단순 요청 (Simple Request)
- 사전 요청 (Preflight Request)
- 인증 요청 (Credentialed Request)

### 단순 요청 (Simple Request)
Preflight 요청 없이 바로 요청을 보낸다.
Simple Request는 아래와 같은 조건을 만족해야한다.

- 메서드 : GET, POST, HEAD
- Content-Type은 아래 셋 중 하나여야 한다.
  - application/x-www-form-urlencoded
  - multipart/form-data
  - text/plain
- 헤더 : Accept, Accept-Language, Content-Language, Content-Type 만 허용 한다.


### 사전 요청 (Preflight Request)
사전 요청은 OPTIONS 메서드를 통해 다른 도메인 리소스에 요청이 가능한지 확인하는 작업이다.
요청이 가능한 것을 확인하면 실제 요청을 보낸다.

### Preflight Request
- Origin : 요청 출처
- Access-Control-Request-Method : 실제 요청의 메서드
- Access-Control-Request-Headers : 실제 요청의 추가 헤더

### Preflight Response
- Access-Control-Allow-Origin : 허가 출처
- Access-Control-Allow-Methods : 허가 메서드
- Access-Control-Allow-Headers : 허가 헤더
- Access-Control-Max-Age : Preflight 응답 캐시 시간 

여기서 **Preflight Response**의 응답 코드는 200대여야하고 Body는 비어있는 것이 좋다.

## 인증 요청 (Credentialed Request)
인증 관련 헤더를 포함할 때 사용하는 요청이다.

### 클라이언트
쿠키 또는 JWT 토큰을 담아 보낼 경우 credentials : include 를 포함하여 보낸다.

### 서버
Access-Control-Allow-Credentials : true 해야 클라이언트의 인증 포함 요청에 허용이 가능하다.

## CORS 
CORS 해결 방법에는 세 가지가 있다.

- 프론트 프록시 서버 설정
  - 프론트 서버에서 백엔드 서버로 요청을 보낼 때, 대상의 URL을 변경한다.
- 직접 헤더 설정
  - 직접 헤더에 설정을 추가한다. 
- 스프링부트 설정
  - 설정 클래스를 만들고 `WebMvcConfigurer`을 구현하면 `addCorsMappings`란 메서드를 사용하여 CORS의 출처 및 설정 관리를 할 수 있다.


