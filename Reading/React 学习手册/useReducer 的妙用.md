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

也就是说，只需要一点小小的手段，就不用再单独为复杂的状态写个 `update` 函数了。

```tsx
export default function App() {
  const [user, setUser] = useReducer(
    (state: User, newState: Partial<User>) => ({ ...state, ...newState }),
    defaultUser
  );

  return (
    <div className="App">
      <h1>Hello CodeSandbox</h1>

      <div>
        <span>Update name: </span>
        <input
          type="text"
          value={user.name}
          onChange={(e) => setUser({ name: e.target.value })}
        />
      </div>

      <pre>{JSON.stringify(user, null, 2)}</pre>
    </div>
  );
}
```

没错，这就和以前版本类式组件中的 `setState` 类似。

```tsx
class User extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      name: "xfy",
      age: 18
    };
  }

  render() {
    return <button onSubmit={() => this.setState({ name: "" })}></button>;
  }
}
```