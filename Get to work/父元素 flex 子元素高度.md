当父元素位 flex 容器时，子元素设置 `flex: 1;` 时会默认占满父元素。

当子元素设置了 `height: 100%;` 时，就算有 `flex: 1;` 高度也是会不生效。除非当父元素设置了 `flex-flow: column;` ，子元素有 `flex: 1;` 才会占满父元素高度。 `height: 100%;` 可有可无

```css
.parent {
  background-color: aquamarine;
  margin-top: 2rem;
  border-radius: 1rem;
  overflow: hidden;
  min-height: 30rem;
  display: flex;
  flex-flow: column;
}

.child {
  background-color: blueviolet;
  position: relative;
  /* height: 100%; */
  flex: 1;
}
```