# 예외처리

런타임시 발생하는 예외에 대해서 처리함

## Exception

exception 클래스는 크게 RuntimeException 클래스와 나머지 Exception클래스들로 나뉜다

- RunTimeExcetion

    주로 개발자의 실수에 의해서 발생 ( 배열 범위 벗어 남 , 널 값 반환 등) 

- 그 외 Execption 

    주로 외부의 영향으로 발생, 프로그램 사용자들의 동작에 의해서 발생 (입력 데이터 형식이 잘못 됨)

```java
public class ExceptionEx5 {
    public static void main(String[] args) {
        System.out.println(1);
        System.out.println(2);
        try{
            System.out.println(3);
            System.out.println(0/0);//오류 -> catch
            System.out.println(4); //출력 x
        }catch (ArithmeticException ae){
            System.out.println(5);
            System.out.println(ae);
        }
        System.out.println(6);
    }
}

/*출력

1
2
3
5
java.lang.ArithmeticException: / by zero
6

*/
```

```java
public class ExceptionEx7 {
    public static void main(String[] args) {
        System.out.println(1);
        System.out.println(2);
        try{
            System.out.println(3);
            System.out.println(0/0);
            System.out.println(4);
        }catch (ArithmeticException ae){ 
            //여기서 에러가 잡히면 아래 catch문 실행 x
            if(ae instanceof ArithmeticException)
                System.out.println(true);
            System.out.println("ArithmeticException");
        }catch (Exception e){
            System.out.println("Exception");
        } //마지막 catch 최상위 Exception클래스의 참조변수를 호출해서 어떤종류의 에러라도 잡을 수 있음
        System.out.println(6);
    }
}

/*출력

1
2
3
true
ArithmeticException
6



*/
```

## 예외 발생 시키기 (throw) 
**메서드 예외인 thorws와 다름**
1. 연산자 new로 예외 클래스 객체 생성
2. thorw 이용해서 예외 발생

```java
public class ExceptionEx9 {
    public static void main(String[] args) {
        try{
            //Exception 인스턴스를 생성할때 생성자에 String 넣으면 getMessage()로 조회 가능
            Exception e = new Exception("고의로 발생"); 
            throw e; //예외발생
        } catch (Exception e){
            System.out.println("에러메시지 :" + e.getMessage());
            e.printStackTrace();
        }
    }
}
/* 출력

에러메시지 :고의로 발생
java.lang.Exception: 고의로 발생
	at ch8.ExceptionEx9.main(ExceptionEx9.java:6)

*/
```

## 메서드에 예외 선언(throws)

1. 일반적으로 RuntimeException은 적지 않는다
2. 보통 명시해놓고 메서드를 사용하는 쪽에서는 이에 대한 처리를 강요하게 한다
```java
void method() throws Exception1 , Exception2 , ... {

}

public class ChainedExceptionEx {
    public static void main(String[] args) {
        try{
            method();
        }catch (Exception1 e){
            
        }catch (Exception2 e2){

        }catch (...){
            
        }

    }

```

## finally 블럭

예외에 관계없이 실행

1. 예외발생 try -> catch -> finally
2. 안발생 try -> finally

## 사용자 정의 예외

예외 클래스를 상속받아서 만듦

```java
class MyException extends Exception {
    MyException(String msg){
        super(msg); //조상인  Exception의 생성자 호출
    }
}
```

## 예외 되던지기
```java
public class ExceptionEx17 {
    public static void main(String[] args) {
        try {
            method1();
        }catch (Exception e){
            System.out.println("main메서드에서 예외가 처리되었습니다.");
        }
    }

    static void method1() throws Exception {
        try{
            throw new Exception(); //예외강제발생
        } catch (Exception e){
            System.out.println("method1메서드에서 예외가 처리되었습니다.");
            throw e; //다시 예외 발생
        }
    }
}
/* 출력
method1메서드에서 예외가 처리되었습니다.
main메서드에서 예외가 처리되었습니다.
*/

//method1()과 main메서드 양쪽에서 catch블럭이 모두 수행 됨

```


## 연습문제
1. 오버라이딩 을 할 때 (overriding) , 조상 클래스의 메서드보다 많은수의 예외를 선언할 수 없다. (더 큰)
