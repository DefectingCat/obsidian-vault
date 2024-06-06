Tailwind 的 [初始化 CSS](https://tailwindcss.com/docs/preflight) 中使用了属性选择器来对 `button` 对基础样式进行覆盖，但由于 antd v5 版本样式使用的是 `:where()` 选择器来应用到 DOM 上。从而导致了权重没有 tailwind 高到问题。

![[Pasted image 20230329173558.png]]