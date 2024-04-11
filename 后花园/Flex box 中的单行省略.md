当一个 `div` 设置为了 flex box，且设置了 `justify-content: center;` 来让其中的文本居中时。此时再设置 CSS 的单行省略就会导致过长的文本直接从中间的部分裁断，而不是靠左进行省略。

这种问题需要在 `div` 中添加一个 `p` 元素作为子元素。当 `p` 元素未被文本撑满时，其尊重 flex center 的居中效果。当被撑满时，则由 `p` 进行单行文本省略。

```css
.name {
  height: 20px;
  background: rgba(0, 0, 0, 0.25);
  display: flex;
  justify-content: center;
  align-items: center;
  position: absolute;
  bottom: 0;
  width: 100%;
  color: #fff;
  font-size: 12px;
  padding: 0 4px;

  p {
    margin: unset;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
  }
}
```