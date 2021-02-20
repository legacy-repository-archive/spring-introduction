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

```
devtools(Developer Tools)는 스프링 부트에서 제공하는 개발 편의를 위한 모듈이다. 
주로 변경된 코드를 서버 또는 화면에 신속하게 반영해 결과를 확인하기 위해서 사용한다.

dev tools 는 일반적으로 서버를 내렸다 올리는 식의 재시작 방법보다 더 빠르게 재시작할 수 있다.     
Devtools는 classpath에 변경이 생기면 이를 감지해 재시작 여부를 판단하게 된다.      
프로그램이 실행되면 polling을 통해 주기적으로 classpath의 변경여부를 확인하고 있기 때문에 가능한 부분이다     
```

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

## 테스트 케이스 작성  
* 테스트 메서드의 순서는 보장되지 않는다.  
* 그렇기 때문에, 공용된 의존성이 있다면 서로 다른 테스트 메서드에 영향을 줄 수 있다.  
* 예를 들면, 어떤 객체를 저장하고 주소값으로 확인한다 가정한다  
* 하지만, 이미 다른 값이 다른 테스트에 있기 때문에 저장이 안되거나 조회를 했을 때 다른 객체가 나와 비교에 실패한다.(Map 활용시)   
* 그렇기에 `@AfterEach`를 통해 공용으로 의존하는 개체에 저장된 값은 `clear` 해주는 것이 좋다.  
* 이전 스프링부트 aws에서도 레포지토리를 계속 클리어했던것으로 기억난다.   

지금 강의에서는 먼저 클래스르 만들고 테스트를 진행했다.    
하지만, 원래는 테스트를 먼저 만들고 클래스를 만들어야 한다.   
테스트 클래스를 만들때 어떤 **행동**을 수행할 것인가를 관점으로 기능을 만들면 된다.  
자바는 객체지향 언어이고 정적인 상태인 변수와 동적인 형태인 메서드가 있다.  
그렇기에 동적인 형태 -> 행동을 만들어 테스트 케이스를 작성하고 이에 맞게끔 클래스를 설계하자    
  
그리고 이러한 방식을 우리는 TDD라고 부른다.    
요즘 대세이지만 꼭 TDD 방식으로 진행하지 않아도 된다    
클래스 설계가 처음부터 완벽하게 된다면 일반적인 방법으로 하면 된다.     
하지만, 반대로 클래스 설계를 하기 힘들다거나 아니면 설계가 있는데 추가적인 기능을 넣어야 할 것 같거나      
아니면 만들 클래스의 라인이 예를들어 몇백줄이라 치면, 차라리 테스트를 만들고 클래스를 구현하는 것이 더 좋을 수 있다.   
  
`./gradlew test`로 테스트 할 수도 있다.      
전체 테스트 클래스를 테스트하고 싶다면 테스트 디렉터리에서 런하면 된다.   
   
**테스트는 맞는 로직만 검사하는 것이 아니다.**    
엣지케이스나 실패케이스를 넣어서 실패하는지도 확인해야한다.      
일부러 예외를 발생시키고, 예외가 발생했는지 테스팅을 하면 된다.   
이 부분은 정말 중요하다고 생각한다.
예외사항이 우리가 원하는 상황에 발생하는지, 그리고 맞는 테스트케이스만 짜는게 아니라 실패하는 테스트케이스를 짜는 생각이 넓어진다.       
   
## 서비스 개발 

```java
public Long join(Member member){
    
    Optional<Member> result = memberRepository.findByName(member.getName());
    result.ifPresent(m -> {
        throw new IllegalStateException("이미 존재하는 회원입니다.");   
    });
    
    memberRepository.save(member);
    return member.getId();
}
```
이 코드에서 변경을 할 필요가 있는 권장사항이 있다.     
`Optional<E> instance` 를 사용하는 것은 별로 권장하지 않는다.  
우선 이쁘지 않으며 가독성을 떨어뜨린다.   

그렇기에 참조 변수를 만들어 할당하기 보다, 바로 사용하는 것이 좋다.  
예를 들면 아래와 같이 코딩을 해야한다.  

```java
public Long join(Member member){
    
    memberRepository.findByName(member.getName())
      .ifPresent(m -> {
          throw new IllegalStateException("이미 존재하는 회원입니다.");   
      });
    
    memberRepository.save(member);
    return member.getId();
}
```    
그리고 위 메서드에서 있는지 없는지 검증하는 역할은 따로 메서드로 뽑을 수 있다.       
(저장, 조회 2가지 역할을 메서드에서 하고 있으니 이를 각각 하나의 기능만 수행하도록 메서드로 뽑는다.)       
맥에서는 범위만큼 드래그 한 후 `control + T`를 누르고 `[extract method]`눌러 따로 메서드로 뽑아 분리할 수 있다.       
더 쉽게 하는 방법은 `command + option + m`이 있다.   

## 





추가로 `Optional 인스턴스`에서 값을 꺼낼때는 `get()`보단, `orElseGet()`을 사용하여   
값이 있을 때 꺼내고, 값이 없을 때 꺼내지 않는 방식의 코드를 짜는 것이 훨씬좋다.     

## 

