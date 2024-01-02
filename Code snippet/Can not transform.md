# Can not transform

Background clip and linear gradient can make text gradient.

```tsx
background: linear-gradient(
  to bottom,
  rgba(255, 255, 255, 1) 0%,
  rgba(125, 185, 232, 1) 100%
);
background-clip: text;
-webkit-text-fill-color: transparent;
```

But `-webkit-text-fill-color` will be inherit to the children. And can not use transform to it.