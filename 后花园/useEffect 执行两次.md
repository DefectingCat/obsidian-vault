在 React 的严格模式下， `useEffect` 将会执行两次。一次用于运行组件用于检查潜在的问题，另一次用于真正的渲染组件。

```tsx
<React.StrictMode>
  <App />
</React.StrictMode>
```

当在组件中 `useEffect` 利用回调函数或异步的添加事件监听器后清除时，可能会导致无法正确的全部清除所有的监听器。或者无法正确的添加监听器。