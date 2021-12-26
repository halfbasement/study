# mongoDB (NoSQL)

- 특징
1. RDB와 다르게 스키마가 유동적이고 분산처리가 용이 
2. 반대로 ACID의 특성을 위한다면 RDB가 더 유리 할 수 있음
3. RDB는 테이블들을 조인해서 사용해야 하지만 NoSQL에서는 문서하나에 넣을 수 있다

# mongoose (ODM)

- 특징 
1. 데이터베이스 문서들을 자바스크립트 객체처럼 사용 할 수 있게 해줌
2. 스키마를 몽구스 모델로 생성해주면 쉽게 CRUD작업이 가능 ( 흡사 JPA와 비슷하다고 느꼈다.)
<br >
<br>

## 데이터베이스의 스키마와 모델
<br>

**스키마** ---mongoose.model()--->**모델**-----CRUD -----> **데이터베이스**

<BR>
<br>
<br>

- 스키마 : 컬렉션에 들어가는 문서 내부의 각 필드가 어떤 형식으로 되어 있는지 정의하는 객체

- 모델: 스키마를 사용해서 만든 인스턴스 ( db에서 작업을 처리할수있는 함수 가지고있음 ex: find(),findID())
```js
//스키마 & 모델 생성

import mongoose from 'mongoose';

const { Schema } = mongoose;

//스키마 생성
const PostSchema  = new Schema({
    title :String,
    body :String,
    tags : [String],// 문자열로 이루어진 배열
    publisheDate : {
        type:Date,
        default: Date.now //현재 날짜가 디폴트 값
    }
})

//모델생성
const Post =mongoose.model('Post',PostSchema); // 스키마이름 , 스키마객체
export default Post;

```

- CRUD 

```js
//ex read

export const read = async ctx => {
    const { id } = ctx.params;
    try{
        const post = await Post.findById(id).exec();
        if (!post){
            ctx.status = 404; //not found;
            return;
        }
        ctx.body = post;
    }catch(e){
        ctx.throw(500,e);
    }
};

// 요청 파라미터를 id로 가져와서 id에 적합한 리스트 하나만 출력  

```
## 검증

- ObjectId 검증

   클라이언트 오류를 서버 오류로 인식해 500에러를 띄울 수 있으니 미들웨어를 생성해서 검증을 해줘야 함
   <br>
    ( ex: read API를 실행 할 때 id형식을 다르게 입력한것은 클라이언트 오류이나 500 에러가 뜬다) 
```js
//검증 미들웨어
const {ObjectId} = mongoose.Types;

export const checkObjectId = (ctx,next) =>{ //유효성 검증
    const {id} = ctx.params;
    if(!ObjectId.isValid(id)){ //오브젝트 아이디 타입이 아니면 400 
        ctx.status = 400;
        return;
    }
    return next();
} 


//쿼리를 쓰는 api에만 ObjectId 검증 미들웨어 적용
posts.get('/',postsCtrl.list);
posts.post('/',postsCtrl.write);
posts.get('/:id',postsCtrl.checkObjectId,postsCtrl.read);
posts.delete('/:id',postsCtrl.checkObjectId,postsCtrl.remove);
posts.patch('/:id',postsCtrl.checkObjectId,postsCtrl.update);


```

- Request Body 검증

   write,update API의 경우 전달받은 요청값을 삽입하거나 수정해야하는 작업인데 검증 없이 수행하면 빈 값도 들어간다
   <BR>
   if문으로 비교하는 방법이 있지만 joi 라이브러리로 손쉽게 사용

```js



import Post from '../../models/post';
import mongoose from 'mongoose';
import Joi from 'joi';

const {ObjectId} = mongoose.Types;

// write api ( update API도 동일(required제외))
export const write = async ctx => {

    const schema = Joi.object().keys({
        //객체가 다음 필드를 가지고 있음을 검증함
        title: Joi.string().required(), //required() -> 필수항목
        body:Joi.string().required() ,
        tags:Joi.array().items(Joi.string()).required(), // 문자열로 이루어진 배열
    })


    const result = schema.validate(ctx.request.body);

    //검증하고 나서 검증 실패인 경우 처리
    if(result.error){
        ctx.status = 400; //Bad Request
        ctx.body = result.error;
        return;
    }


    //시작

    const { title,body,tags} = ctx.request.body;
    const post = new Post({
        title,
        body,
        tags,
    });

    try{
        await post.save();
        ctx.body = post;
    }catch (e){
        ctx.throw(500,e);
    }
};

```

## 페이지네이션 구현


```js
export const list = async ctx => {
    //query는 문자열이기 때문에 숫자로 변환해줘야함.
    //값이 주어지지 않았다면 1을 기본으로 사용

    const page = parseInt(ctx.query.page || '1', 10);

    if(page<1){
        ctx.status = 400;
        return;
    }

    try{
        const posts = await Post.find()
        .sort({_id:-1}) // 포스트를 역순으로 불러옴
        .limit(10) // 한번에 보이는 개수 
        .skip((page-1)*10) // 안에 넣은 파라미터(page-1)*10개를 넘기고 그 다음 데이터를 불러옴
        .lean() //JSON형태로 조회
        .exec(); 
      

        //페이지 마지막 번호 알려주기
        
        const postCount = await Post.countDocuments().exec();
        ctx.set('Last-page' , Math.ceil(postCount/10)); //last-page라는 커스텀 http 헤더 설정  Math.cell ()올림함수 , 43개의 글이 있으니 43/10 -> 올림 5 -> 5개의 페이지 

        //내용 길이 제한
        ctx.body = posts
        .map(post=>({
            ...post,
            body:
            post.body.length < 200 ? post.body : `${post.body.slice(0,200)}...`, //200글자 이상이면 자르고 뒤에 ... 붙임

        }))

    } catch (e){
        ctx.throw(500,e);
    }
};
```
