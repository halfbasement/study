# 리덕스

## **흐름**
<br>
 <span style="color:yellow">액션 -> 미들웨어 -> 리듀서 -> 스토어 -> 뷰</span>
<br>
<br>

## **액션**
- 상태에 변화를 주는 객체<br> ( type 필드를 반드시 가지고 있다 , 그 외의 값은 상태 업데이트 시 참고해야 할 값 )
```js
{
    tpye:'ACTION',
    data:{
        id:1,
        text:'text'
    }
}
```
```js
//액션 타입 정의
const INCREASE = 'counter/INCREASE'; // 모듈 이름 / 액션 이름


```
## **액션 생성 함수**

- 액션 객체를 만들어주는 함수
```js
{
    const fuc = text =>({
        type:'ACTION',
        text:text
    })
}
```

```js


export const increase = () => ({type:INCREASE});

//파라미터 추가 ( 액션 객체 안에 추가 필드로 들어감 )

export const changeInput = input => ({
    type: CHANGE_INPUT,
    input:input
})

//redux-actions적용
export const = createAction(INCREASE)

```

## **리듀서**
- 변화를 일으키는 함수
<br>

1. 액션을 만들어서 발생 -> 리듀서가 현재 상태와 전달받은 액션객체를 파라미터로 받아옴
2. 두 값을 참고하여 새로운 상태로 만들어서 반환

```js
const initialstate = {
    counter:1
}

function reducer(state=initialstatae , action){
    switch(action.type){
        case INCREMENT:
          return{
              counter: state.counter +1
          }
        default:
            return{
                state;
            }
    }
}

//react-acions적용

const counter = handleActions(
 {
     [INCREASE] : (state,action) => ({ number : state.number +1 }),
 },
 initialstate,
)
```


## **스토어**
1. 리덕스를 적용하기 위함
2. 한개의 프로젝트에는 단 하나의 스토어 
3. 현재 애플리케이션의 상태와 리듀서 , 그 외 내장함수 ( 디스패치 , 구독 ) 를 지님
<br>
<br>

- 디스패치 : 액션을 발생시키는 것
1. 액션 객체를 파라미터로 넣어서 호출  `dispatch(action)` 
2. 이 함수가 호출되면 스토어는 리듀서 함수를 실행시켜 새로운 상태 생성

<br>
<br>
<br>

# 리덕스 미들웨어 비동기 처리

## **redux-thunk**

- thunk는 특정 작업을 나중에 할 수 있도록 미루기 위해 함수 형태로 감싼 것

```js
const sampleThunk = () => (dispatch,getState) => {
    //새 액션을 디스패치 하거나
    //현재 상태 참조 가능
}
```

```js
// 요청 성공시 요청한 값을 담아서 리듀서에게 전달 
export const getPost = id => async dispatch => {
    dispatch({ type: GET_POST });
    try{
        const res = await api.getPost(id); 
        dispatch({
            type:GET_POST_SUCCESS,
            payload: res.data
        });
    } catch(e){

     dispatch({
        type:GET_POST_FAILURE,
        payload:e,
        error:true
     });
     thorw e;
    }
}

```
<br>
<br>

## **redux-saga**
- 처리가 유연함(제너레이터 함수)

https://velog.io/@kyjun/redux-saga-%ED%9D%90%EB%A6%84-2v59xa3d

```js
 fuction* getPostSaga(action){
     yield put(startLoading(GET_POST));
     
     try{
         //call을 사용하면 Promise반환 함수 호출
         //첫 번째는 파라미터는 함수 , 나머지는 함수에 넣을 인수
        const post = yield call(api.getPost , action.payload); 
        //=api.getPost(action.payload)

        yield put({
            type:GET_POST_SUCESS,
            payload:post.data
        });
     } catch(e) {
         yield put({
             type:GET_POST_FAILURE,
             payload:e,
             error:true
         })

     }
     yield put(finishLoading(GET_POST));
 }
```