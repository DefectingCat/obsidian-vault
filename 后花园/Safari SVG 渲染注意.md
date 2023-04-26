在 Safari 中，如果仅仅只是父元素设置了宽高，而作为子元素的 SVG 没有设置，仅有 `viewBox` 的话，默认则不会给 SVG 任何宽高，导致直接不显示。

在 Chromium 内核的浏览器中，则会尝试最大化显示 SVG。

```ts
export const Button = styled.div`
  width: 0.34rem;
  height: 0.34rem;
  cursor: pointer;
  pointer-events: auto;
  transition: all 0.3s;
  margin-right: 0.2rem;
  position: relative;
  display: flex;

  & > .icon > svg {
    width: 0.34rem;
    height: 0.34rem;
  }

  &:last-child {
    margin-right: unset;
  }
  & > div {
    display: flex;
  }
`;
```