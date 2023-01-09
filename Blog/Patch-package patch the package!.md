Antd-mobile 使用的是旧版本 `2.3.4` ，但其 `Carousel` 组件需要进行一些定制上的修改。组件在很多地方有使用，不方便升级版本。

而 ant-mobile `Carousel` 组件用的是 `"rmc-nuka-carousel": "~3.0.0",`，所以只需要 patch `rmc-nuka-carousel` 使其在移动端竖屏的状态下交换 touch 事件的 x、y 轴即可。

```js
/**
 * Swap page x and y when screen orientation is portrait.
 * Method: 
 * pageX = pageY
 * pageY = -pageX
 * @param e
 * @returns {[{pageY, pageX}]}
 */
getTouches(e) {
  const isPortrait = window.matchMedia('(orientation: portrait)').matches;
  const touches = [
    {
      pageX: e.touches[0].pageX,
      pageY: e.touches[0].pageY,
    },
  ];
  if (isPortrait) {
    [touches[0].pageX, touches[0].pageY] = [
      touches[0].pageY,
      -touches[0].pageX,
    ];
  }
  return touches;
},
```

```
yarn add patch-package -D
```

Patch 指定的包名，名称千万不能指定错了。在 patch 之后会在项目根目录生成一个 patches 目录，目录内就是刚刚修改的包内文件。其记录方式就是利用 git diff 来对比差异，从而进行每次的修改。

```
npx patch-package rmc-nuka-carousel
```

```
diff --git a/node_modules/rmc-nuka-carousel/es/carousel.js b/node_modules/rmc-nuka-carousel/es/carousel.js
index aef49f2..699b093 100644
--- a/node_modules/rmc-nuka-carousel/es/carousel.js
+++ b/node_modules/rmc-nuka-carousel/es/carousel.js
```

除此之外，在 `scripts` 中添加 `postinstall` 以实现每次都 patch

```
  "scripts": {
    "test": "craco test",
    "eject": "react-scripts eject",
    "postinstall": "patch-package"
  },
```