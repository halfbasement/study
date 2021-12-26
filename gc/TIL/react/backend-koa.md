# Koa

express와 흡사함 / async,await 제공

- 미들웨어
```js
(ctx,next){
   //   ctx : 컨텍스트 , 웹 요청과 응답에 관한 정보
   //  next : 현재 처리중인 미들웨어의 다음 미들웨어를 호출하는 함수
}
```

- Koa router

```js
const Koa = require('koa');
const Router = require('koa-router');

const app = new Koa();
const router = new Router();

//기본형

router.get('/', (ctx) => {
  ctx.body = '홈';
}); 

//라우트 파라미터 : 통상적으로 카테고리를 받아오거나 고유한 값으로 특정 데이터 조회

router.get('/about/:name?', (ctx) => {

    console.log(ctx.params) // /about/밸류  -> {name : 밸류}
    const {name} = ctx.params;

    ctx.body = name? `${name}의 소개` : '그냥 소개';
}); 

//라우트 쿼리 : 통상적으로 옵션에 관련된 정보를 받아옴 

router.get('/posts',ctx=>{

    console.log(ctx.query) // /posts/?키=밸류 ->{ 키 : 밸류 }
    const {id} = ctx.query;

    ctx.body = id ? `포스트 #${id}` : '포스트 아이디가 없습니다';
})

//app인스턴스에 라우터 적용
app.use(router.routes()).use(router.allowedMethods());

app.listen(4000, () => {
  console.log('Listening to port 4000');
});
```

## REST API
<br>

 **DB** < -- > **REST API** < -- > **클라이언트**

1. 클라리언트가 DB에 직접 접속 하는 건 보안상 문제가 됨
2. PUT, POST , DELETE , PUT , PATCH (HTTP메서드 이용)
