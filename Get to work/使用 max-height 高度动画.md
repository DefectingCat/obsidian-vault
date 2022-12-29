`height` 不可以作用于 `transition` ，`max-height` 可以配合 `transition` 。

```tsx
${({ show, height }) => {
  if (show) {
    return css`
      padding: 0.1rem 0;
      max-height: ${(height ?? 0) + 0.2}rem;
    `;
  } else {
    return css`
      padding: 0;
      max-height: 0;
    `;
  }
}}
```