# redux_memo
Redux메모

### Redux Toolkit
`npm install @reduxjs/toolkit react-redux`

### configureStore
store파일 생성 (stroe.js)
```
import { configureStore } from '@reduxjs/toolkit'

export default configureStore({
  reducer: { }
}) 
```

index.js에 import
```
import { Provider } from "react-redux";
import store from './store.js'

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(
  <React.StrictMode>
    <Provider store={store}>
      <BrowserRouter>
        <App />
      </BrowserRouter>
    </Provider>
  </React.StrictMode>
); 
```

### createSlice
1. createSlice( ) 로 state 만들고
2. configureStore( ) 안에 등록
```
import { configureStore, createSlice } from '@reduxjs/toolkit'

let user = createSlice({
  name : 'user',
  initialState : 'kim'
})

export default configureStore({
  reducer: {
    작명 : user.reducer
  }
}) 
```

state 사용할 파일에서
```
import { useSelector } from "react-redux"

function 함수(){
  let a = useSelector((state) => { return state } )
  console.log(a)

  return (생략)
}
```

### combineReducers
https://velog.io/@postlist/REACT-Reducer-%EC%97%AC%EB%9F%AC-%EA%B0%9C-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0-combineReducer


###  dispatch, state 변경 방법
store 파일의 createSlice 변경 함수 작성
```
let user = createSlice({
  name : 'user',
  initialState : 'kim',
  reducers : {
    작명(state){
      return 'john ' + state
    }
  }
}) 
export let { 작명 } = user.actions //export해주
```

사용할 파일에서 dispatch 사용
```
import { useDispatch, useSelector } from "react-redux"
import { changeName } from "./../store.js"

const dispatch = useDispatch();
<button onClick={()=>{
  dispatch(작명())
}}>버튼</button> 
```

redux state가 array/object인 경우 변경하려면 
```
let user = createSlice({
  name : 'user',
  initialState : {name : 'kim', age : 20},
  reducers : {
    changeName(state){
      return {name : 'park', age : 20}
    }
  }
}) //이렇게 해도 되지만

let user = createSlice({
  name : 'user',
  initialState : {name : 'kim', age : 20},
  reducers : {
    changeName(state){
      state.name = 'park'
    }
  }
}) // 이것도 가능 (Immer.js 라이브러리가 state 사본을 하나 더 생성해준 덕분)
```

파라미터 전달 가능
```
let user = createSlice({
  name : 'user',
  initialState : {name : 'kim', age : 20},
  reducers : {
    increase(state, action){
      state.age += action.payload
      //action.type 하면 state변경함수 이름이 나오고
      //action.payload 하면 파라미터가 나옴 
    }
  }
}) 
```

호출시엔
`dispatch(increase(10)), dispatch(increase(100))` 이런식으로 호출


### ReactQuery
`npm install @tanstack/react-query `

index에
```
import { QueryClient, QueryClientProvider } from "react-query"  //1번
const queryClient = new QueryClient()   //2번

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(
  <QueryClientProvider client={queryClient}>  //3번
    <Provider store={store}>
      <BrowserRouter>
        <App />
      </BrowserRouter>
    </Provider>
  </QueryClientProvider>
); 
```

사용
```
function App(){
  let result = useQuery('작명', ()=>
    axios.get('주소')
    .then((a)=>{ return a.data })
  )

  return (
    <div>
      { result.isLoading && '로딩중' }
      { result.error && '에러' }
      { result.data && result.data.name }
    </div>
  )
}
```

특징

1. ajax 요청 성공/실패/로딩중 상태를 쉽게 파악
2. 틈만나면 알아서 ajax 재요청
3. 실패시 재시도 알아서 해줌
4. ajax로 가져온 결과는 state 공유 필요없음 
