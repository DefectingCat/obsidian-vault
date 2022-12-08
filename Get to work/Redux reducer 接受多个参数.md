`createSlice`  使我们能够添加一个“prepare callback”的回调函数来处理一个 reducer 接受多个参数的场景。

将 reducer 创建为一个对象，分别拥有 `prepare` 和 `reducer` 两个值。

```js
    todoColorSelected: {
      reducer(state, action) {
        const { color, todoId } = action.payload
        state.entities[todoId].color = color
      },
      prepare(todoId, color) {
        return {
          payload: { todoId, color },
        }
      },
    },
```

在使用时可以像这样的给 reducer 传递多个参数：

```jsx
  const handleColorChanged = (e) => {
    const color = e.target.value
    dispatch(todoColorSelected(todo.id, color))
  }
```

