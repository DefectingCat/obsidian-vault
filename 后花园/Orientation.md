在 `window` 上的 `screen` 属性值，它包含了当前屏幕的 orientation 详细信息，可以用它来判断当前屏幕的方向以及旋转水平。

[Orientation](https://developer.mozilla.org/en-US/docs/Web/API/Screen/orientation)

```ts
{
  angle: 0
  onchange: null
  type: "portrait-primary"
}
```

```js
switch (screen.orientation.type) {
  case "landscape-primary":
    console.log("That looks good.");
    break;
  case "landscape-secondary":
    console.log("Mmmh… the screen is upside down!");
    break;
  case "portrait-secondary":
  case "portrait-primary":
    console.log("Mmmh… you should rotate your device to landscape");
    break;
  default:
    console.log("The orientation API isn't supported in this browser :(");
}
```

同时可以在 `window` 上监听 `orientationchage` 来在旋转屏幕时做执行回调。

```ts
// 是否是竖屏
const [isPortrait, setIsPortrait] = useBoolean(false);
// 旋转时记录
useEffect(() => {
  const handler = (e: Event) => {
    const orientation = (e.target as Window).screen.orientation;
    if (orientation.type === 'portrait-primary') {
      setIsPortrait.setTrue();
    } else {
      setIsPortrait.setFalse();
    }
  };
  window.addEventListener('orientationchange', handler);
  return () => {
    window.removeEventListener('orientationchange', handler);
  };
  // eslint-disable-next-line react-hooks/exhaustive-deps
}, []);
```