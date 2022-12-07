在 `useEffect` 中监听事件时，对应的回调函数需要 `useCallback` 否则当组件再次渲染时，函数会被重新创建，对应的 `removeEventListener` 则无法移除监听器。

```tsx
  // 回车进入展台
  const handleEnter = useCallback((e: KeyboardEvent) => {
    if (e.key !== 'Enter') return;
    onClickEnter();
  }, []);
  useEffect(() => {
    window.addEventListener('keyup', handleEnter);
    return () => window.removeEventListener('keyup', handleEnter);
  }, []);
```