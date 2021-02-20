# 스프링 입문 - 코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술
스프링에 대해서 이미 어느정도 공부를 진행했기에  
여기서는 몰랐던 내용들에 대해서만 **기록** 차원으로 글을 작성하고자 합니다.     

## 프로젝트 생성
**Spring Boot version**      
* version(SNAPSHOT) : 아직 개발 중인 버전          
* version(M1_Milestone) : 정식 릴리즈 전 사용자 테스트 버전               
* version : 정식 릴리즈 된 버전, 안정성이 높은 스프링 라이브러리를 제공한다.      
  
GA (General Availability)   
RC (Release Candidate)     

**Project Meatadata**
* Group : 도메인주소(기업 도메인,즉 부서 입력 전까지)
* Artifact : 빌드되어 나올 때 나오는 결과물의 이름 
* Name : 프로젝트의 이름 


**package(디렉터리) 분리하기**     
* 패키지에 다른 파일이 없으면 체이닝 형식으로 표기될 때가 많다.(me.kwj1270.sample 이 하나의 디렉터리로 표현)              
* 프로젝트 네비게이션의 톱니바퀴를 누르면 `[Compact Middle Packages]`가 있으니 이를 클릭해주면 디렉터리가 분리되어 표현된다.          

**실행속도 높이기**
* 최근 IntelliJ 버전은 Gradle을 통해서 실행 하는 것이 기본 설정이다. 이렇게 하면 실행속도가 느리다.
* 다음과 같이 변경하면 자바로 바로 실행해서 실행속도가 더 빠르다.

* Preferences -> Build, Execution, Deployment -> Build Tools -> Gradle
  * Build and run using: Gradle IntelliJ IDEA
  * Run tests using: Gradle IntelliJ IDEA
    
**IntelliJ에서 단축키를 확실하게 검색하는 방법**
1. File Settings에 들어간다.
2. keymap을 선택한다.
3. 검색창에 단축키 이름을 입력한다. 단축키 이름은 위 그림 처럼 영상 하단에 나온다.   

## 라이브러리 살펴보기

**gradle**         
* gradle 같은 경우 의존받은 라이브러리 + 의존받은 라이브러리가 의존하는 라이브러리 또한 같이 가져온다.             
* 즉, `spring-boot-starter` 밖에 의존 안했는데, 연관된 모든 라이브러리를 가져오기 때문에 `spring core`까지 가져온다.       
* 라이브러리 중복이 발생할 수 도 있는데, 이는 알아서 처리해주고 `*`이라는 표시로 표식을 남겨준다.     
      
**외장 톰캣과 내장 톰캣 차이**     
* 깊게 들어가서 설명은 하지 않겠다.     
* 외장 톰캣을 쓴다면, 웹 서버 프로그램을 실행시키고 자바 코드를 밀어 넣는 식이었다.         
* 요즘에는 소스 라이브러리에서 웹 서버를 들고 있다.(임베디드 서버)       
* 그렇기에 따로 설정할 필요 없이 실행만 하더라도 서버 실행 및 애플리케이션을 배포할 수 있다.        
    
**로그**          
* 현업에서는 로그를 주로 이용한다.           
* 로그로 남기면, 심각한 에러만 따로 모아놓을 수 있고 로그 파일들을 관리할 수 있다.        
* 이전에는 `slf4j`를 많이 썼는데, 요즘에는 `logback`으로 넘어가는 추세이다.    

## View 환경 설정   

**server-side-template**
* 서버 사이드 템플릿 엔진을 사용하고자 한다면 관련 dependency를 의존하도록 하자      
  * `implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'`
* 추가로 검색해서 알았는데, mustache 처럼 헤더 풋터를 만들어 레이앗처럼 사용하고자 한다면 아래와 같은 라이브러리도 의존 받는다.   
  * `implementation 'nz.net.ultraq.thymeleaf:thymeleaf-layout-dialect'`        
* 아직, 사용법은 모르지만 언젠가 쓸 일이 있다면 그 때 검색해보자     

**웹 동작 과정**   
* 클라이언트 -> 서버(tomcat)  
* 서버(tomcat)의 servlet이 controller 호출
* 비즈니스로직 처리
* controller -> 모델을 가지고 -> servlet
* servlet 에서 viewResolver 및 forward
  * `resources:template/`+vireName(Controller 리턴값)+`.html`    
* client는 model을 받음  

## 빌드하고 실행하기
빌드파일 : 하나의 애플리케이션, 관련 라이브러리를 다운받았기에 실행시키기만 하면 바로 애플리케이션이 실행된다.     
우리 같은 경우는 서버가 실행되기에, 도메인을 통해 웹 애플리케이션에 접근 가능하다.    
       
빌드를 진행하기전에 서버 실행한 것을 꺼야 된다.(필수!)(포트 충돌로 한 쪽 실행안됨)       
**빌드파일 생성**  
```gradle
./gradlew build
```   
  
**모든 빌드파일 삭제** 
```gradle
./gradlew clean build
```

## 정적 콘텐츠 
**정적 콘텐츠**   
    
* WAS기법이 아닌, 즉 모델이 없는 경우 `main/resources/static`에서 html을 찾는다.         
* server-side-template이나 어떤 프로그래밍을 하는 html은 작성할 수 없다.        
* 동작 기준-> Controller에 url 관련 매핑이 없을 경우, servlet에서  `main/resources/static`를 찾는다.          

## MVC와 템플릿 엔진 
**동적 콘텐츠**   
* `@RequestParam("name")` 필자가 알기론, 파라미터 이름이 같은 경우 자동으로 주입하지만,    
특별한 상황을 대비해 `@RequestParam("name")`을 통해 요청값을 명확히 가져오는 것이 좋다.     
* `@RequestParam("name")`의 멤버를 살펴보면 `required`라는 것이 존재한다.  
* `required`는 디폴트 값으로 true이고, true이기에 존재하기에 해당 파라미터를 넘겨줘야만 `Controller` 맵핑을 실행한다.    
* 반대로, 우리가 명시적으로 `required=false`한다면, 해당 파라미터가 오지 않아도 매핑을 실행해준다.   
       
참고: spring-boot-devtools 라이브러리를 추가하면, html 파일을 컴파일만 해주면 서버 재시작 없이 View 파일 변경이 가능하다.         
   
**thymeleaf**   
```html
<p th:text="'hello' + ${name}">hello! empty</p>
```    
`th:text=`로 서버로 넘어온 값이 출력될텐데 `hello! empty`가 왜 필요할까?       
`thymeleaf`는 서버사이드 템플릿 엔진으로 서버에서 와야 동작한다.     
하지만, 내용물을 넣어놓음으로써 서버를 실행시키지 않고도 해당 레이아웃을 확인하게끔 해주는 것이다.      
   
## API    
  
**@ResponseBody**  
* `@ResponseBody`를 사용하게되면 HttpMessage의 body 부분에 데이터를 직접 넣는다는 것이다.     
* 즉, 특정 resolve 작업을 거치지 않고, 리턴하는 값을 바로 메시지에 넣는 것을 의미한다.        
* 이런 데이터를 JSON으로 내려주면 FrontEnd에서 해당 데이터를 알아서 처리한다고 생각하면 된다.(데이터만 잘 넘기면 된다.)     
* Model과의 차이점은 서버사이드 템플릿은 우리가 HTML을 조작하는 것이고, API는 데이터만 내려주면 Front에서 HTML을 조작한다는 것이다.       
* 리턴 값을 String 이 아닌 다른 레퍼런스 값으로 넘긴다면 아래와 같은 데이터로 치환하여 보낸다. (단, **Getter/Setter** 있어야한다.)    
   
```java  
{변수명 : 값}    
// 클래스의 이름은 나오지 않는다.   
```
  
**동작 방식**      
* `@ResponseBody` 를 사용
  * HTTP의 BODY에 문자 내용을 직접 반환
  * **`viewResolver` 대신에 `HttpMessageConverter`가 동작**   
    * 기본 문자처리: StringHttpMessageConverter
    * 기본 객체처리: MappingJackson2HttpMessageConverter
    * byte 처리 등등 기타 여러 HttpMessageConverter가 기본으로 등록되어 있음
        
즉, 문자열 타입이 오면 문자열 그대로 JSON에 넣는다.             
레퍼런스 타입(객체)가 오면 이를 해석한 후 문자열로 치환하여 JSON에 넣는다. (Jackson 라이브러리 이용)          
  
```
참고: 클라이언트의 HTTP Accept 해더와 서버의 컨트롤러 반환 타입 정보 둘을 조합해서
HttpMessageConverter 가 선택된다. 더 자세한 내용은 스프링 MVC 강의에서 설명하겠다.

Accept: text/html, application/xhtml+xml, application/xml;q=0.9, */*;q=0.8
```   

## 비즈니스 요구사항 정리 
* 컨트롤러: 웹 MVC의 컨트롤러 역할
* 서비스: 핵심 비즈니스 로직 구현
* 리포지토리: 데이터베이스에 접근, 도메인 객체를 DB에 저장하고 관리
* 도메인: 비즈니스 도메인 객체, 예) 회원, 주문, 쿠폰 등등 주로 데이터베이스에 저장하고 관리됨   



