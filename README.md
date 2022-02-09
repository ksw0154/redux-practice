# Redux Practice

Learning Vanilla-Redux and React-Redux

## Vanilla Redux

Redux는 React를 위한 것이 아니다.

### Redux 설치

```javascript
npm install redux
```

<br>

### import Redux

```javascript
import { store } from "redux";
```

<br>

### Create Store

```javascript
const store = createStore(reducer);
```

<br>

### Create Reducer

```javascript
const reducer = (state = [], action) => {
  switch (action.type) {
    case ADD_TODO:
      return [...state, { text: action.text, id: action.id }];
    case DELETE_TODO:
      return state.filter((todo) => todo.id !== action.id);
    default:
      return state;
  }
};
```

`createStore()`를 하기 위해서는 `reducer`가 필요하다.  
`reducer`는 `state`와 `action`을 매개변수로 받을 수 있다.

`reducer`는 `state`를 `return`한다.

<br>

### Dispatch

```javascript
store.dispatch({ type: ADD_TODO, text: todo, id: Date.now() });
```

`reducer`의 `action`을 일으키는 함수이다.  
`dispatch`의 매개변수는 `object` 형식으로 작성해야 한다.

<br>

### Subscribe

```javascript
store.subscribe(paintToDos);
```

`state`의 변화를 감지하는 함수이다.  
`state`의 변화가 일어났을 때 `()` 안에 있는 함수를 실행한다.

---

## React Redux

npm install react-redux
npm install react-router-dom

store.js 생성
VanillaJS와 동일하게 store를 생성하고, reducer를 작성한다.
export default를 이용해서 다른 component에서 부를수 있도록 한다.

store 연결
`index.js`

```javascript
import { Provider } from "react-redux";
import store from "./store";

<Provider store={store}>
    <App />
</Provider>,
```

Provider를 통해서 store를 제공한다.

component에서 state 접근 (mapStateToProps)

`Home.js`

```javascript
import { connect } from "react-redux";
const Home = ({ toDos }) => {
  console.log(toDos);
};

const mapStateToProps = (state, ownProps?) => {
  return { toDos: state };
};

export default connect(mapStateToProps)(Home);
```

connect()를 이용해서 getState, dispatch()를 할 수 있다.
connect()는 2개의 매개변수를 가진다. (mapStateToProps, mapDispatchToProps)

```javascript
const Home = ({ toDos, addToDo }) => {};

const mapDispatchToProps = (dispatch) => {
  return {
    addToDo: (text) => dispatch(actionCreators.addToDo(text)),
  };
};

export default connect(mapStateToProps, mapDispatchToProps)(Home);
```

actionCreators.addToDo(text)는
{type: ADD, text: text}가 들어간다.

redux toolkit
기존의 redux 코드보다 조금 더 효율적으로 redux 코드를 작성할 수 있는 toolkit
npm install @reduxjs/toolkit@1.2.5

createAction

기존 코드

```javascript
import { createAction } from "@reduxjs/toolkit";
const ADD = "ADD";

const addToDo = (text) => {
  return {
    type: ADD,
    text,
  };
};

const reducer = (state = [], action) => {
  switch (action.type) {
    case ADD:
      return [{ text: action.text, id: Date.now() }, ...state];
    default:
      return state;
  }
};
```

toolkit 코드

```javascript
const addToDo = createAction("ADD");
// addToDo.type === "ADD"

const reducer = (state = [], action) => {
  switch (action.type) {
    case addToDo.type:
      return [{ text: action.payload, id: Date.now() }, ...state];
    default:
      return state;
  }
};
```

> addToDo.type을 통해서 case에 입력해야 하는 string을 선언해주지 않아도 된다.  
> action.text 형식으로 내보내던 값은 payload를 이용해서 내보낼 수 있다.  
> payload는 toolkit에서 제공하는 변수이다.

createReducer()

기존 코드

```javascript
const reducer = (state = [], action) => {
  switch (action.type) {
    case addToDo.type:
      // payload는 toolkit에서 제공하는 것
      return [{ text: action.payload, id: Date.now() }, ...state];
    case deleteTodo.type:
      return state.filter((todo) => todo.id !== parseInt(action.payload));
    default:
      return state;
  }
};
```

toolkit 코드

```javascript
const reducer = createReducer([], {
  [addToDo]: (state, action) => {
    state.push({ text: action.payload, id: Date.now() });
  },
  [deleteTodo]: (state, action) => {
    return state.filter((todo) => todo.id !== parseInt(action.payload));
  },
});
```

createReducer([])를 통해서 state를 초기화 할 수 있다.
switch, case를 사용하지 않아도 된다.
제일 큰 차이점은 state를 mutate해도 된다.
-> Redux toolkit은 immer를 통해서 기존의 방식처럼 작동하게 만든다.
코드상으로는 state를 mutate한 모습이지만, `return [{ text: action.payload, id: Date.now() }, ...state];` 이 방식으로 작동한다.
state를 mutate를 하게 되면 return하지 않지만, 기존 방식처럼 새로운 state를 가지게 된다면 return을 작성해줘야한다.
push는 state를 mutate하고 filter는 state를 mutate하지 않는다.

// argument: init(여기에서는 [] === state = []), {switch문이 들어가는 내용}
// switch, case를 안쓸수 있고, state를 mutate해도 된다.
// state를 내보낼때 2가지를 선택할 수 있다.
// 1. state를 mutate하거나 (redux tollkit이 immer 위에서 작동하기 때문에 immer가 mutate된 state를 새로운 state로 return 한다.) (immer를 통해서 기존의 방식처럼 작동한다.)
// 2. 기존의 방식처럼 새로운 state를 return하거나

configureStore()
Redux Developer Tools를 이용할 수 있다.

기존 코드

```javascript
const store = createStore(reducer);
```

toolkit 코드

```javascript
const store = configureStore({ reducer });
```

createSlice()

기존 코드

```javascript
const addToDo = createAction("ADD");
// addToDo.type === "ADD"
const deleteTodo = createAction("DELETE");

const reducer = createReducer([], {
  [addToDo]: (state, action) => {
    state.push({ text: action.payload, id: Date.now() });
  },
  [deleteTodo]: (state, action) => {
    return state.filter((todo) => todo.id !== parseInt(action.payload));
  },
});

export const actionCreators = {
  addToDo,
  deleteTodo,
};
```

toolkit 코드

```javascript
const toDos = createSlice({
  name: "toDosReducer",
  initialState: [],
  reducers: {
    add: (state, action) => {
      state.push({ text: action.payload, id: Date.now() });
    },
    remove: (state, action) => {
      return state.filter((toDo) => toDo.id !== action.payload);
    },
  },
});

const store = configureStore({ reducer: toDos.reducer });
export const { add, remove } = toDos.actions;
```
