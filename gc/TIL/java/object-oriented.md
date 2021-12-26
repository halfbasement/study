# 객체 지향

## 상속 (extends)

1. 생성자와 초기화 블럭은 상속 X
2. 자손 클래스의 인스턴스를 생성하면
자손+조상의 인스턴스로 생성
3. 포함관계(객체생성) : ~은 ~을 가지고있다 / 상속관계 : ~은 ~이다
4. 다중상속 X

- ## 오버라이딩 
    ( 조상클래스로부터 상속받은 메서드의 내용을 자손이 변경하는 것 )

    -조건-

1. 이름 , 매개변수 , 반환타입이 같아야함
2. 접근 제어자를 조상 클래스의 메서드보다 좁은 범위로 변경 불가능 

- ## super
    ( 조상의멤버들을 호출할때 사용 )

1. super( ) : 조상 클래스의 멤버변수는 조상의 생성자에 의해 초기화 되도록 해야 한다

## package와 import

- ## pakage 
1. 하나의 소스파일에는 첫 번째 문장으로 단 한 번의 패키지만을 허용
2. 모든 클래스는 반드시 하나의 패키지에 속해야 함
3. 패키지는 물리적으로 클래스 파일을 포함하는 하나의 디렉토리이다

## 접근 제어자

 public > protected (같은패키지 & 자손클래스) > default > private
1. 생성자가 private인 클래스는 다른 클래스의 조상이 될 수 없다.

 
 - ## static
    
    인스턴스 멤버가 선언되지 않은 메서드는 static 메서드로 정의하는게 더 좋다

 - ## final

    final이 붙은 변수는 상수이므로 일반적으로 선언과 초기화를 동시에 하지만 , 인스턴스 변수의 경우 생성자에서 초기화 되도록 할 수 있다.


## 다형성

1. 조상 클래스 타입의 참조변수로 자손 클래스의 인스턴스를 참조 할 수 있도록 하였다.
```java
    //조상
    class Tv{
        boolean power;
        int channel;

        void power(){ power = !power; }
        void channelUp() {++channel;}
        void channelDown() { --channel; }
    }

    //자손 
    class CaptionTv extends Tv{
        String text;
        void caption()
    }

    
    
    //조상 클래스 타입의 참조변수로 자손 클래스의 인스턴스 참조
    
    Tv t = new CaptionTv() ; //생략 됐지만 형변환이 이루어짐
    // Tv클래스의 멤버들만 사용가능 (CaptionTV의 멤버 사용 불가능)

   
   //자손 클래스 타입의 참조변수로 조상 클래스 인스턴스 참조 
   
    CaptionTv c = new Tv(); //에러
    //참조 변수가 사용 할 수 있는 멤버의 개수는 인스턴트의 멤버 개수보다 많거나 같아야함

```
<br>

- ## 참조 변의 형변환

```java
 조상타입 = <- 자손타입; // 형변한 생략 , 업 캐스팅

 자손타입 = <- (인스턴스)조상타입 // 형변환 생략불가 , 다운 캐스팅

public class CastingTest2 {
    public static void main(String[] args) {
        Car car = new Car();
        Car car2 = null;
        FireEngine fe = null;

        car.drive();
        fe = (FireEngine)car; //에러 -> 자손타입의 참조변수로 조상타입의 인스턴스를 참조하려고 했기 때문에 
        fe.drive();
        car2 = fe;
        car2.drive();
    }
}

```
<br>

- ## instanceof 연산자

```java
FireEngine fe = new FireEngine();

if(fe instanceof FireEngine){ // FireEngine 타입의 참조변수 fe가 FireEngine인스턴스를 참조 하고 있는가? 
             sout("true")
}
```
<br>

- ## 참조변수와 인스턴스의 연결

```java
public class BindingTest {
    public static void main(String[] args) {
        Parent p = new Child();
        Child c = new Child();

        System.out.println("p.x = " + p.x); //100
        System.out.println("c.x = " + c.x); //200
        p.method();  //자식 메소드
        c.method();  //자식 메소드

        // 참조변수 타입에 따라 멤버변수의 값은 바뀌지만 메서드는 오버라이딩한 자식의 메서드가 호출 됨
        // 자식이 오버라이딩 하지 않았으면 부모의 메서드가 호출 됨
    }
}

class Parent {
    int x = 100;

    void method() {
        System.out.println("부모 메소드");
    }
}

class Child extends Parent {
    int x = 200;

    @Override
    void method() {
        System.out.println("자식 메소드");
    }

}
```



- ## 매개변수의 다형성

```java

public class PolyArgumentTest {

    public static void main(String[] args) {
        Buyer b = new Buyer();

//매개변수가 Product타입이기 때문에 자손타입의 참조변수면 어느것이나 들어갈수 있다 (자손은 조상의 모든걸 갖고있기 때문)
        b.buy(new Tv());
        b.buy(new Computer());

        System.out.println("현재 남은 돈 :"+ b.money); //700
        System.out.println("현재 보너스 점수"+b.bonusPoint); //30
    }
}

class Product {
    int price;
    int bonusPoint;

    Product(int price) {
        this.price = price;
        bonusPoint = (int) (price / 10.0);
    }
}

class Tv extends Product {
    Tv() {
        // 조상 클래스의 생성자 Product(int price) 호출
        super(100); //Tv 의 가격을 100만원으로 한다
    }

    public String toString() {
        return "Tv";
    }
}

class Computer extends Product{
    Computer(){
        super(200);
    }
    public String toString() {
        return "computer";
    }
}

class Buyer {
    int money = 1000;
    int bonusPoint = 0;

    void buy(Product p){
        if(money < p.price){
            System.out.println("잔액부족");
            return;
        }

        money -= p.price;
        bonusPoint += p.bonusPoint;
        System.out.println(p+"를 구매했습니다");
    }


}

```

## 추상 클래스

1. 미완성 메서드를 포함하고 있다는 의미
2. 추상클래스로 인스턴스 생성 불가능 , 상속을 통해 자손 클래스에서 완성 가능
3. 새로운 클래스를 작성하는데 있어서 바탕이 되는 조상클래스로서 중요한 의미를 가짐
4. 자손 클래스에서 추상 메서드를 반드시 구현하도록 강요하기 위함

## 인터페이스

1. 일종의 추상 클래스
2. 오직 추상 메서드와 상수만을 멤버로 가질 수 있음

```java

interface TestInterface {
    public static final 타입 상수이름 = 값;
    public abstract 메서드이름;

    // 모든 멤버변수는 public static final 이여야 함 , 생략가능
    // 기본적으로 모든 메서드는 추상메서드이지만 static메서드와 디폴트 메서드도 허용

}
```

- 인터페이스의 상속

    인터페이스끼리 상속 받을 수 있으며 다중 상속도 허용

```java
class Fighter extends Unit implements Fightable{
    public void move(int x,int y){ //접근제어자를 public으로 한 이유
    //오버라이딩 할때는 조상의 메서드보다 넓거나 같은 접근제어자를 지정해야한다
    //public abstract가 생략 된 것 이기 때문에  public으로 해줘야한다

    }
    public void attack(Unit u){

    }

}

class Unit{
    int currentHP;
    int x;
    int y;
}
interface Fightable extends Movable,Attackable{}
interface Movable { void move(int x,int y); }
interface Attackable { void attack(Unit u); }


```

- 인터페이스를 이용한 다형성
1. 해당 인터페이스 타입의 참조변수로 이를 구현한 클래스의 인스턴스를 참조 할 수 있다
2. 인터페이스 타입으로 형변환도 가능
3. 인터페이스 타입의 매개변수가 갖는 의미는 구현클래스의 인스턴스를 매개변수로 제공해야한다는 것

```java
class Fighter extends Unit implements Fightable{
    public void move(int x, int y){}
    public void attack(Fightable f){} 
    //매개변수로 Fighter 클래스의 멤버를 사용
    // attack(new Fighter()) 가능
}
```
4. 리턴타입이 인터페이스라는 것은 메서드가 해당 인터페이스를구현한 클래스의 인스턴스를 반환한다는것을 의미


- 인터페이스 장점
1. 개발시간 단축 
2. 표준화 가능
3. 서로 관계없는 클래스들에게 관계를 맺어 줄 수 있음
4. 독립적인 프로그래밍 가능


- 인터페이스의 이해

```java
package ch7;

public class InterfaceTest2 {
    public static void main(String[] args) {
        A a = new A();
        a.autoPlay(new B()); //B클래스
        a.autoPlay(new C()); //C클래스

    }
}

interface I {
    public abstract void play();
}


class A{
    void autoPlay(I i){
        i.play();
    }
}

class B implements I{
    @Override
    public void play() {
        System.out.println("B 클래스");
    }
}

class C implements  I{
    @Override
    public void play() {
        System.out.println("C 클래스");
    }
}
```

- 디폴트 메서드와 스태틱 메서드

1. 아무리 설계도 잘해도 언젠간 변경은 발생함
2. 추상메서드가 아니기 때문에 디폴트 메서드가 추가되어도 구현 클래스들을 변경하지 않아도 됨.


# 핵심

1. 조상 타입의 인스턴스를 자손타입으로 변경하는건 허용하지않음

2. 매개변수로 인터페이스를 넣었을때 그 인터페이스를 구현한 클래스 또는 그 자손의 인스턴스가 매개변수로 가능하다