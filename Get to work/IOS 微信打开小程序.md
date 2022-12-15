在 IOS 微信中使用开放标签打开小程序时，不能将 `path` 设置为根路径。否则再多次打开后会提示 `relativePath is empt` 的错误。

```tsx
        {/* @ts-ignore */}
        <wx-open-launch-weapp
          ref={wxRef}
          id="launch-btn"
          username={appId}
          // path="/"
        >
          <script type="text/wxtag-template">
            <style>{`.btn-${id.current} {
  opacity: 0;
  border: none;
}`}</style>
            <button
              className={`btn-${id.current}`}
              style={{
                ...btnSize,
              }}
            ></button>
          </script>
          {/* @ts-ignore */}
        </wx-open-launch-weapp>
```

