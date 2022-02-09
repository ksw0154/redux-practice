# Vanilla Redux

Learning Vanilla-Redux and React-Redux

## Vanilla Redux

Redux는 React를 위한 것이 아니다.

### Redux 설치

```
npm install redux
```

<br>

### import Redux

```
import { store } from 'redux';
```

<br>

### Create Store

```
const store = createStore(reducer);
```

<br>

### Create Reducer

```
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

```
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
