---------------------------------------타임리프------------------------------


1. URL 링크 표현식 - @{...},

th:href="@{/css/bootstrap.min.css}"
@{...} : 타임리프는 URL 링크를 사용하는 경우 @{...} 를 사용한다. 이것을 URL 링크 표현식이라 한다.
URL 링크 표현식을 사용하면 서블릿 컨텍스트를 자동으로 포함한다.

- 쿼리 파라미터도 생성한다.
th:href="@{/basic/items/{itemId}(itemId=${item.id}, query='test')}"
생성 링크: http://localhost:8080/basic/items/1?query=test

URL 링크 간단히
th:href="@{|/basic/items/${item.id}|}"

속성 변경 - th:action
th:action
HTML form에서 action 에 값이 없으면 현재 URL에 데이터를 전송한다.


2.리터럴 대체 - |...|
|...| :이렇게 사용한다.
타임리프에서 문자와 표현식 등은 분리되어 있기 때문에 더해서 사용해야 한다.
<span th:text="'Welcome to our application, ' + ${user.name} + '!'">
다음과 같이 리터럴 대체 문법을 사용하면, 더하기 없이 편리하게 사용할 수 있다.
<span th:text="|Welcome to our application, ${user.name}!|">