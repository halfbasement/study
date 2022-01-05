# spring mvc

- HttpServletRequest 역할

HttpServletRequest를 사용하면 다음과 같은 HTTP 요청 메시지를 편리하게 조회할 수 있다.

## HTTP 요청 데이터

1. GET - 쿼리 파라미터 

- 클라이언트에서 서버로 데이터를 전달할 때는 HTTP 메시지 바디를 사용하지 않기 때문에 content-type이 없다

2. POST - HTML Form

3. 공통 애노테이션

- **@RequestParam**

- @RequestParam의 name(value) 속성이 파라미터 이름으로 사용
1. @RequestParam("username") String memberName 
2. @RequestParam String username // 생략 가능 
3.  완전 생략도 가능 But , 너무 과하다

4. 주의! - 파라미터 이름만 사용
/request-param?username=
파라미터 이름만 있고 값이 없는 경우 빈문자로 통과
5. 주의! - 기본형(primitive)에 null 입력
/request-param 요청
@RequestParam(required = false) int age
null 을 int 에 입력하는 것은 불가능(500 예외 발생)
따라서 null 을 받을 수 있는 Integer 로 변경하거나, 또는 다음에 나오는 defaultValue 사용

-  **@ModelAttribute**

1. @ModelAttribute 는 생략할 수 있다.

2.  모델(Model)에 @ModelAttribute 로
지정한 객체를 자동으로 넣어준다.

3. @ModelAttribute 의 이름을 생략하면 모델에 저장될 때 클래스명을 사용한다. 이때 클래스의 첫글자만
소문자로 변경해서 등록한다

4. 스프링은 해당 생략시 다음과 같은 규칙을 적용한다.
String , int , Integer 같은 단순 타입 = @RequestParam
나머지 = @ModelAttribute (argument resolver 로 지정해둔 타입 외)



- **프로퍼티**
1. 객체에 getUsername() , setUsername() 메서드가 있으면, 이 객체는 username 이라는 프로퍼티를
가지고 있다.
2. username 프로퍼티의 값을 변경하면 setUsername() 이 호출되고, 조회하면 getUsername() 이
호출된다


- **RedirectAttributes**

1. 리다이렉트할때 쓸 데이터 가져감 ( addAttribute , 사용안하면 쿼리파라미터로 넘어감)
2. RedirectAttributes 를 사용하면 URL 인코딩도 해주고, pathVarible , 쿼리 파라미터까지 처리해준다.


- **HTTP message body**



- @RequestBody
1. @RequestBody 를 사용하면 HTTP 메시지 바디 정보를 편리하게 조회할 수 있다. 참고로 헤더 정보가
필요하다면 HttpEntity 를 사용하거나 @RequestHeader 를 사용하면 된다.



- @RequestBody 요청
1. JSON 요청 - >HTTP 메시지 컨버터 -> 객체
@ResponseBody 응답
2. 객체  -> HTTP 메시지 컨버터  ->JSON 응답



- **HTTP 메시지 컨버터**
1. 뷰 템플릿으로 HTML을 생성해서 응답하는 것이 아니라, HTTP API처럼 JSON 데이터를 HTTP 메시지
바디에서 직접 읽거나 쓰는 경우 HTTP 메시지 컨버터를 사용하면 편리하다.

2. @ResponseBody 를 사용
HTTP의 BODY에 문자 내용을 직접 반환
viewResolver 대신에 HttpMessageConverter 가 동작

3. 요청 매핑 헨들러 어뎁터 구조
```
                                   
                                 HTTP 메시지 컨버터
                                                 ↑
                               ↙    ArgumentResolver    ↘

서블릿 < ----------->      어댑터   <----------------- >   핸들러

                               ↖   ReturnValueHandler   ↗
```
- HttpServletRequest , Model 은 물론이고, @RequestParam , @ModelAttribute 같은 애노테이션
그리고 @RequestBody , HttpEntity 같은 HTTP 메시지를 처리하는 부분까지 매우 큰 유연함을
보여주었다.
- 이렇게 파라미터를 유연하게 처리할 수 있는 이유가 바로 ArgumentResolver 덕분이다.






## HTTP 응답 - 정적 리소스, 뷰 템플릿


1. 정적 리소스 경로
src/main/resources/static
뒤에 .html 로 URL바로 접근가능


2. 뷰 템플릿
뷰 템플릿을 거쳐서 HTML이 생성되고, 뷰가 응답을 만들어서 전달한다.
일반적으로 HTML을 동적으로 생성하는 용도로 사용하지만, 다른 것들도 가능하다. 뷰 템플릿이 만들 수
있는 것이라면 뭐든지 가능하다.



### HttpServletResponse  역할
- HTTP 응답 메시지 생성
- HTTP 응답코드 지정
- 헤더 생성
- 바디 생성
- 편의 기능 제공
- Content-Type, 쿠키, Redirect



## 서블릿, JSP, MVC 패턴




- redirect vs forward
1. 리다이렉트는 실제 클라이언트(웹 브라우저)에 응답이 나갔다가, 클라이언트가 redirect 경로로 다시
요청한다. 따라서 클라이언트가 인지할 수 있고, URL 경로도 실제로 변경된다. 반면에 포워드는 서버
내부에서 일어나는 호출이기 때문에 클라이언트가 전혀 인지하지 못한다.

2. form의 action을 보면 절대 경로시작이 아니라 상대경로시작하는 것을 확인할 수 있다.
이렇게 상대경로를 사용하면 폼 전송시 현재 URL이 속한 계층 경로 + save가 호출된다.
ex 현재 계층 경로: /servlet-mvc/members/  결과: /servlet-mvc/members/save



## 동작 순서

1. 핸들러 조회: 핸들러 매핑을 통해 요청 URL에 매핑된 핸들러(컨트롤러)를 조회한다.
2. 핸들러 어댑터 조회: 핸들러를 실행할 수 있는 핸들러 어댑터를 조회한다.
3. 핸들러 어댑터 실행: 핸들러 어댑터를 실행한다.
4. 핸들러 실행: 핸들러 어댑터가 실제 핸들러를 실행한다.
5. ModelAndView 반환: 핸들러 어댑터는 핸들러가 반환하는 정보를 ModelAndView로 변환해서
반환한다.
6. viewResolver 호출: 뷰 리졸버를 찾고 실행한다.
JSP의 경우: InternalResourceViewResolver 가 자동 등록되고, 사용된다.
7. View 반환: 뷰 리졸버는 뷰의 논리 이름을 물리 이름으로 바꾸고, 렌더링 역할을 담당하는 뷰 객체를
반환한다.
JSP의 경우 InternalResourceView(JstlView) 를 반환하는데, 내부에 forward() 로직이 있다.
8. 뷰 렌더링: 뷰를 통해서 뷰를 렌더링 한다.











