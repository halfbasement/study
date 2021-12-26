# 스프링

EJB의 지옥에서 벗어나서 만든 POJO를 살린 프레임 워크 

<br>
<br>
 

## 1 . SOLID

1. 단일 책임 원칙(SRP) - 변경이 있을 때 파급효과가 적어야 함 ( 1클래스 1책임 )
2. ***개방 폐쇄 원칙(OCP)*** - 확장에는 열려있고 변화엔 닫혀 있어야 함<br>
인터페이스를 이용한 다형성을 사용 
3. 리스코프 치환 원칙(LSP) - 단순히 컴파일 성공 유무를 넘어서 기능정인 구현까지 지켜져야함
4. 인터페이스 분리 원칙(ISP) - 인터페이스를 최대한 쪼개자(모듈 단위로)
5.  ***의존관계 역전 원칙(DIP)*** - 인터페이스에 집중해야한다 ( 추상화에 의존한다 ) 

- 즉 인터페이스에 의존하자 

OCP와 DIP를 위반하는 경우는 스프링이 해결해준다.

<br>
<br>

## 2. ocp와 dip 해결 (관심사의 분리)

- AppConfig파일로 의존성을 주입해준다 ( 클라이언트 코드는 건들 필요가 없다.)
- 클라이언트 코드 <---> AppConfig(구현체 주입)

<br>
<br>

## 3. spring 컨테이너 적용

- 클라이언트 코드 < --- > 스프링 컨테이너 (  AppConfig  )
- 스프링 컨테이너가 빈들을 관리하면서 얻는 이점이 매우 많다.

<br>
<br>


## 4. BeanFactory와 ApplicationContext

```java
BeanFactory(interface) // 빈 관리 & 조회 기능  getBean
        ↑ 상속
ApplicationContext(interface) // 빈 관리 & 조회 + 수 많은 부가 기능 제공 
        ↑ 구현
구현1-- AnnotationContextApplicationContext(java) -reader(설정 정보 읽기)-> .java파일 -> beanDefinition 제공
구현2-- GenericXmlApplicationContext(XML) -reader(설정 정보 읽기)-> .xml파일 -> beanDefinition 제공
구현3-- xxxxAplicationContext(xxx) ...
```

- 결론 : 스프링이 다양한 형태의 설정 정보를 BeanDefinition으로 추상화 해서 사용한다.

<br>
<br>

## 5. 싱글톤

1. 객체 참조 변수를 static으로 해서 클래스 내에서 객체 생성
2. private 으로 생성자 막기
3. getInstance()메서드를 이용해서 리턴 값으로 1번에서 선언한 객체 참조 변수를 넣어줌

- 주의점
1. 무상태로 설계 해야 함 ( 리턴을 멤버변수로 해주기 보단 지역변수나 파라미터를 사용하여 일회성으로 리턴해줌 )

<br>
<br>

## 6. 스프링 컨테이너

기본적으로 스프링은 빈들을 싱글톤으로 관리해줌

### 빈을 참조 할 때 객체를 여러개 생성해야 할 때가 있는데 그 때는 싱글톤을 어떻게 보장 해 줄 것인가?

1. 스프링이 아무리 기능이 좋아도 직접 생성한 자바코드 자체를 조작하는건 힘들기 때문에 ***@Configuration***이 붙은 클래스는 직접 사용하는게 아니고 CGLIB라는 바이트 조작코드 라이브러리를 사용해서 @Configuration(빈이 등록 된 클래스) 를 상속받은 클래스를 임의로 생성해서 사용한다 

2. 1에서 생성한 클래스는 객체가 있으면 기존 객체를 사용하고 없으면 생성시켜준다 ( singleton 보장 )

<br>
<br>


## 7. 컴포넌트 스캔

- class에 @Component를 붙혀주면 자동으로 빈 등록이 된다.

### @ComponentScan

1. 기본적으로 빈 이름은 @Component가 붙은 클래스의 앞글자만 소문자로 바꿔서 등록된다
2. basePackage를 지정해서 해당 패키지안을 스캔한다 (default는 @ComponentScan이 적용 된 클래스 패키지)
3. 권장하는 방법은 default를 쓰되 @ComponetnScan이 적용 된 클래스를 클라이언트 코드 최상단에 둬서 다른 코드들을 스캔하지 않도록 한다 ( springboot는 main최상단 클래스 부터 스캔)

### @Component종류

- @Controller : 스프링 MVC 컨트롤러로 인식
- @Repository : 데이터 접근계층으로 인식 , 데이터 예외를 스프링 예외로 변환
- @Service : 서비스로 인식 , 별 기능 없음


### filter

```java
  @ComponentScan(includeFilters = @Filter(type = FilterType.ANNOTATION, classes = MyIncludeComponent.class),
            excludeFilters = @Filter(type = FilterType.ANNOTATION, classes = MyExcludeComponent.class) //등록 안됨
    ) //나만의 컴포넌트를 스캔 할 수 있는 기능 지정
    @Configuration
    class ComponentFilterAppConfig {

    }
```

<br>
<br>


## 8. 의존관계 자동 주입

- **생성자 주입** ( 생성자 호출과 동시에 빈 주입 )
1. 파라미터로 들어오는 변수들의 **타입으로** 빈을 스캔해서 넣어준다.
2. 생성자 호출 시점에 1번만 호출( 불변 , 필수 )
3. 생성자가 단일이면 @Autowired를 생략해도 무관

- setter 
1. 선택적 변경 가능

- 필드주입
1. 권장 x 외부변경 불가능 ( 테스트가 힘듦 ) // di기능이 없으면 뭘 할 수가 없음

- 일반 메서드 주입
1. 임의의 메서드에 @Autowired로 생성자처럼 주입 ( 잘 안씀 )

### 옵션처리

스프링 빈이 없이도 동작해야 할 때가 있다.

```java
public class AutowiredTest {

    @Test
    void AutowiredOption() {
        ApplicationContext ac = new AnnotationConfigApplicationContext(TestBean.class);

    }

    static class TestBean {

        @Autowired(required = false)  // 메서드 자체가 호출이 안됨 (빈이 없기 때문에)
        public void setNoBean1(Member noBean1){//빈이 아님 Member는
            System.out.println("noBean1 = " + noBean1);
        }
        
        @Autowired
        public void setNoBean2(@Nullable Member noBean2){ //호출은 되는데 널로 들어옴
            System.out.println("noBean2 = " + noBean2);
        }

        @Autowired
        public void setNoBean3(Optional<Member> noBean3){ //호출은 되는데 Optional.empty로 들어옴
            System.out.println("noBean3 = " + noBean3);
        }

    }
}

```

<br>
<br>

### 생성자 주입을 선택하라

1. 불변하고 final키워드를 사용해서 값이 주입 되지 않거나 변경되지 않게 만듦(컴파일오류)
2. 순수 자바의 특성을 잘 살릴 수 있음
3. 테스트 코드에서 유연하게 사용 가능( 가짜 객체 주입 등)


### @Autowired 조회 빈이 2개 이상 일 때

- 빈 조회 시 **타입**으로 조회 하기 때문에 구현체가 여러개면 중복 된다

```java
             DiscountPolicy
            ↗          ↖
        구현              구현
    @Coponent            @Coponent
    FixDiscountpolicy    RateDiscountPolicy
``` 

- 해결법
1. 타입이 여러개면 필드명(구현 클래스)으로 구분
2. @qualifier(추가 구분자) -> 빈 이름 변경 X 추가로 등록 됨
3. @Primary로 우선순위 지정

### 조회 한 빈이 모두 필요 할 때

- Map<String,타입> -> 빈 이름 , 빈 객체

- List<타입> -> 빈 객체

- 모두 뽑아내면 다형성을 이용하여 동적으로 빈 사용이 가능하다

### 자동 빈 주입 VS 수동 빈 주입

- 자동 빈 주입 -> 업무 로직 빈(@Controller,@Repository,@Service 등 )
- 수동 빈 주입 -> 직접 등록한 기술지원 객체 ( ex: common )

<br>
<br>


## 9. 빈 생명주기 콜백 

### 스프링 컨테이너 생성 -> 스프링 빈 생성(생성자 주입은 의존관계 주입 생략) -> 의존 관계 주입 <br>
### -> ***초기화 콜백 @PostConstructor*** -> 사용 -> ***소멸 전 콜백 @PreDestroy*** -> 종료 

- 스프링 빈은 객체를 생성하고 의존 관계를 주입하고 난 후에 데이터를 사용 할 수 있다.
- 그럼 그냥 생성자할때 생성자에 다 때려 박으면 되지 않나요?  <br>
-> 안됨,  객체의 생성과 초기화는 분리해야한다 ( 생성시엔 필수 데이터만 넣고 초기화로(set) 그 외의 데이터를 넣어줘야 한다)

- 방법
1. 인터페이스 ( 거의 안씀 , 장점 X)
2. @Bean(initMethod , destroyMethod) -> 빈이 스프링에 의존 x 외부라이브러리에 적용하고 싶을 때 사용
3. @PostConstructor , @PreDestory -> 메서드 위에 선언 , 이게 정석

<br>
<br>


##  10. 빈 스코프 (@Scope) (파일 참고)
- 빈 스코프 : 빈이 존재 할 수 있는 범위

- 종류
1. 싱글톤 스코프 : 스프링컨테이너가 defalut 빈 시작 ~ 종료까지 관리
2. 프로토타입 스코프 : 생성 & 주입 까지 관리하고 이후엔 관리 x
- ***요청마다 새로운 인스턴스 반환***
- 주입 이후 관리가 안되니 @PreDestory같은 종료 메서드 호출 x
- 싱글톤 빈 안에서 프로토타입 빈 사용시 싱글톤 처럼 사용 됨 
- ObjectProvider<타입>으로 주입해서 getObject()로 사용
3. 웹 스코프 :
웹 스코프는 웹 환경에서만 동작한다.
- 웹 스코프는 프로토타입과 다르게 스프링이 해당 스코프의 종료시점까지 관리한다. 따라서 종료 메서드가
호출된다

- 프록시 :사실 Provider를 사용하든, 프록시를 사용하든 핵심 아이디어는 진짜 객체 조회를 꼭 필요한 시점까지
지연처리 한다는 점이다.
단지 애노테이션 설정 변경만으로 원본 객체를 프록시 객체로 대체할 수 있다. 이것이 바로 다형성과 DI 
컨테이너가 가진 큰 강점이다.
꼭 웹 스코프가 아니어도 프록시는 사용할 수 있다