由于 React 的合成事件最终会在 window 绑定并触发，如果有其他组件在 window 上监听了同样的事件，则有可能也会被触发。即使该组件已经卸载，且尝试移除了该事件。

例如，antd 的 `onPressEnter` 事件会触发其他组件的 window 事件。即使其他组件已经移除事件，同时也有 `stopPropagation`

```tsx
            <Input
              placeholder="请输入密码"
              size="large"
              prefix={<LockOutlined />}
              value={passwd}
              onChange={handleChange}
              type="password"
              onPressEnter={(e) => {
                if (e.key !== 'Enter') return;
                e.preventDefault();
                e.stopPropagation();
                e.nativeEvent.stopPropagation();
                e.nativeEvent.preventDefault();
                handleClick();
              }}
            />

```

```ts
  // 回车进入展台
  const handleEnter = useCallback((e: KeyboardEvent) => {
    console.log(e);
    if (e.key !== 'Enter') return;
    onClickEnter();
    // eslint-disable-next-line react-hooks/exhaustive-deps
  }, []);
  useEffect(() => {
    window.addEventListener('keyup', handleEnter);
    return () => {
      window.removeEventListener('keyup', handleEnter);
    };
  }, [handleEnter, onClickEnter]);
```