SVG 支持改变宽高动画，普通元素需要设置 `max-width` 或 `max-height`  才能支持改变宽高的动画

```tsx
const TabTitle = styled.div`
  display: flex;
  align-items: center;

  svg {
    width: 0;
    height: 1.3em;
    fill: currentColor;
    margin-right: 4px;
    opacity: 0;
    transition: all 0.3s;
  }

  &:hover svg {
    width: 1.3em;
    opacity: 1;
  }
`;
```