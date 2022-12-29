styled-components 添加 `@font-face` 需要添加到全局样式中，也就是必须要使用 `createGlobalStyle` 方法。

```ts
// 为加载按钮设置的字体
export const ButtonGlobal = createGlobalStyle<{ fontUrl?: string }>`
  @font-face {
    font-family: 'Loading Font';
     src: ${({ fontUrl }) => `url(${fontUrl}) format('woff2')`} ;
  }
`;
```

此外，使用 `createGlobalStyle` 方法创建的 CSS 会和在整个项目中其他任何地方调用 `createGlobalStyle` 合并到一起，成为一整个全局样式。