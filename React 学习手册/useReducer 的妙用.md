在官方文档中的 `useReducer` 主要是类似于 Redux 中的用法。虽说 Redux 文档也提到过其灵感来源自数组原型上的 `reduce` 方法，但配合 `action` 的使用一直感觉和 `reduce` 不是特别相似。

```jsx
import { useReducer } from 'react';

function reducer(state, action) {
  if (action.type === 'incremented_age') {
    return {
      age: state.age + 1
    };
  }
  throw Error('Unknown action.');
}

export default function Counter() {
  const [state, dispatch] = useReducer(reducer, { age: 42 });

  return (
    <>
      <button onClick={() => {
        dispatch({ type: 'incremented_age' })
      }}>
        Increment age
      </button>
      <p>Hello! You are {state.age}.</p>
    </>
  );
}
```

这第一眼看上去就是在使用 Redux。另一个使用方式就是并不是非要模仿 Redux 的方法， `reducer`  函数的第二个参数是接受任何值作为参数的。