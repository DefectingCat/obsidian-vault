# encodeURI

`encodeURIComponent()` 与 `decodeURIComponent()` 分别是将 URL 中的特殊字符来 UTF-8 编码的转义序列替换。

这样在携带的 query 中有多个包含 query 的字符（`?` 、`&` ）等会被编码为特定的格式，以方便在 query  中解析出正确的参数。

```tsx
let url =
    getViewerBaseURL() +
    `/?from_organ=1&organ_url=${encodeURIComponent(organUrl)}&booth_id=${id}`;

const organUrl = Common.getQuery('organ_url');
if (organUrl) window.open(decodeURIComponent(organUrl), '_blank');
```