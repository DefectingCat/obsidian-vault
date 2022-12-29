在 unicode 引用下，如果有多个项目，则需要引入多个对应的 CSS 文件。而这些 CSS 文件中对字体名称的定义是相同的。

```tsx
import '@/assets/css/iconfot-viewer/iconfont.css';
import '@/assets/css/iconfot/iconfont.css';
```

所以在多个项目时需要修改某个的名称。

```css
.iconfont {
  **font-family: "iconfont" !important;**
  font-size: 16px;
  font-style: normal;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
```