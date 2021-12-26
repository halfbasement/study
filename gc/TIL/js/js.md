# JS

## this

- 일반 함수의 this

1. **함수** : 그냥 쓰거나 일반함수에서 쓰면 window(전역공간) 출력
2. **오브젝트**: 나를 포함한 오브젝트 출력 (그 함수의 주인 출력)
<br>
메소드실행시 메소드를 소유하고있는 객체 가르킴

3. 생성자에선 새롭게 만들어진 객체를 가르킴
```js
console.log(this) // {window}

funtion(){
    this; // {window} -> window.function()
}

obj = {
    name:'kim',
    fuc1 : funciton(){
        this  // obj -> {name:'kim', fuc1 :f }
    }
    data : {
       fuc2 : sayHello(){
            this // { data : f}
        }
    }
}

```

- 애로우 펑션 this

1. **오브젝트** : 오브젝트 안에서 써도 this값을 바로 상위에있던 this값을 사용
2. **이벤트리스터** : {window}가 나옴 (상위 값 사용했기때문) 

3. 메소드에선 에로우펑션 사용을 지양해야한다
```js
const person = { 
    name: 'Lee', 
    sayHi: () => console.log(`Hi ${this.name}`)
     }; 
person.sayHi(); // Hi undefined​ -> ()=>this가 전역객체를 가르킴(1번의 이유)




```