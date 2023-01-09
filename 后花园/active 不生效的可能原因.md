对于某些元素来说，CSS `:active` 需要在 `:hover` 之后才能生效。

```tsx
<div
    className={css`
      width: 64px;
      height: 64px;
      cursor: pointer;
    `}
  >
    <Heart
      className={css`
        transition: all 0.1s ease-in-out;
        &:hover {
          transform: scale(1.1);
        }
        &:active {
          transform: scale(0.9);
        }
      `}
    />
  </div>
```