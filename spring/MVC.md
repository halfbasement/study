여기서 form의 action을 보면 절대 경로(로 시작)이 아니라 상대경로(로 시작X)하는 것을 확인할 수 있다.
이렇게 상대경로를 사용하면 폼 전송시 현재 URL이 속한 계층 경로 + save가 호출된다.
현재 계층 경로: /servlet-mvc/members/
결과: /servlet-mvc/members/save

정리 : 중복되는 부분들이 너무 많다 이걸 해결한게 프론트 컨트롤러 패턴( 스프링 )



--mvc 프레임워크 만들기

v1 - 프론트 컨트롤러 도입
v2 - view 분류 
1. 단순 반속 되는 뷰 로직 분리
v3 - Model추가
- 서블릿 종속성 제거
- 뷰 이름 중복 제거
v4- 단순하고 실용적인 컨트롤러
-v3 와 거의 비슷
-구현 입장에서 ModelAndView를 직접 생성해


@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
----스프링 mvc 구조 이해

dispatcher서블릿 ( 서블릿을 상속 받음)  이 doDispatch 호출한다

1. handlerMapping : 핸들러 매핑에서 이 컨트롤러를 찾을 수 있어야 한다 ( 1순위 @RequestMapping (애노테이션 ) , 2 순위 빈이름 )
2.  hanlderAdapter : 핸들러 매핑을 통해서 찾은 핸들러를 실행 할ㅇ 수 있는 핸들러 어댑터 ( 1순위 @RequestMapping(애노테이션)
 // v5 마지막 보자(dispatcher Servlet)

---로깅-----
 
sout 대신 로그쓰는이유 ( 레벨별로 로그를 남길 수 있어서) 
테스트
로그가 출력되는 포멧 확인
시간, 로그 레벨, 프로세스 ID, 쓰레드 명, 클래스명, 로그 메시지
로그 레벨 설정을 변경해서 출력 결과를 보자.
LEVEL: TRACE > DEBUG > INFO > WARN > ERROR
개발 서버는 debug 출력
운영 서버는 info 출력
@Slf4j 로 변경

올바른 로그 사용법
log.debug("data="+data)
로그 출력 레벨을 info로 설정해도 해당 코드에 있는 "data="+data가 실제 실행이 되어 버린다.
결과적으로 문자 더하기 연산이 발생한다.
log.debug("data={}", data)
로그 출력 레벨을 info로 설정하면 아무일도 발생하지 않는다. 따라서 앞과 같은 의미없는 연산이
발생하지 않는다.

로그는 파일로 남길 수도 있다
성능도 좋다

--스프링 팁 --

컨트롤러면서 반환값이 string이면 뷰리졸버를 찾게된다 

-롬복 @Data
1. 객체를 바로 찍어도 toString덕분에 값이 나온다
2. Require

-프로퍼티
객체에 getUsername() , setUsername() 메서드가 있으면, 이 객체는 username 이라는 프로퍼티를
가지고 있다.

-@RequestParam
1.   매개변수 값 생략가능 , 심지어 @RequestParam도 생략가능
2.  쿼리스트링 아무것도 안넣으면 null이 아니라 빈문자("")로 들어와서 처리 해줘야함 ( 기본형 타입도 조심)
3. defaultValue로 null이나 ""처리가능

-@ModelAttribute
1. 쿼리스트링 대응 값 객체 setter찾아서 넣어줌
2. 얘도 생략가능

기본형 같이 간단한 것 들은 @RequestParam , 지정된 객체 제외하고 나머지 객체들은 ModelAttribute

