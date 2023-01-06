SVG `viewBox` 属性决定了其位置与尺寸。属性分别为`min-x`, `min-y`, `width` and `height` 。

要想一个 SVG 可以随着其父元素或 CSS 决定大小，需要设置 `viewBox` 的 `width` and `height` 为其本身的大小。

```jsx
<svg viewBox="0 0 38 38" fill="none" xmlns="http://www.w3.org/2000/svg">
  <rect width="38" height="38" rx="6" fill="#F22A3A" />
  <g clip-path="url(#prefix__prefix__clip0_0_1)">
    <path
      d="M29.931 10.775l-2.736-2.737-8.21 8.21-8.21-8.21-2.736 2.737 8.21 8.21-8.21 8.21 2.737 2.735 8.209-8.21 8.21 8.21 2.736-2.736-8.209-8.21 8.21-8.21z"
      fill="#fff" />
  </g>
  <defs>
    <clipPath id="prefix__prefix__clip0_0_1">
      <path fill="#fff" transform="translate(8.039 8.038)" d="M0 0h21.923v21.923H0z" />
    </clipPath>
  </defs>
</svg>
```

其中 `rect` 的大小为 `width="38" height="38"` ，当设置了 `viewBox="0 0 38 38"` 后，就可以设置对其设置大小。

![close.svg](close.svg)

<aside>
💡 当设置了 `viewBox` 后没有为 SVG 明确的设置大小时，它会 100% 的占用父元素。

</aside>