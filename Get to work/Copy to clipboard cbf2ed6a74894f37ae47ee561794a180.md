# Copy to clipboard

复制到剪贴板中有两个方法，分别是现代的 Clipboard API 和 已经被标记为 deprecated 的 `execCommand()` 方法。

其中 Clipboard API 需要 http 才能使用。

```tsx
import { useCallback, useState } from 'react';

type CopiedValue = string | null;
type CopyFn = (text: string) => Promise<boolean>; // Return success

/**
 * 使用 clipboard API writeText 写入字符到剪贴板
 * copy 方法为 memoized
 * @returns
 */
function useCopyToClipboard() {
  const [copiedText, setCopiedText] = useState<CopiedValue>(null);

  const copy: CopyFn = useCallback(async (text) => {
    const copyWithOldWay = () => {
      try {
        const el = document.createElement('textarea');
        el.value = text;
        el.setAttribute('readonly', '');
        el.style.position = 'absolute';
        el.style.left = '-9999px';
        document.body.appendChild(el);
        el.select();
        document.execCommand('copy');
        document.body.removeChild(el);
        return true;
      } catch (e) {
        console.warn('Copy failed', e);
        return false;
      }
    };

    const copyWithClipboardAPI = async () => {
      // Try to save to clipboard then save it in the state if worked
      try {
        await navigator.clipboard.writeText(text);
        setCopiedText(text);
        return true;
      } catch (error) {
        console.warn('Copy failed', error);
        setCopiedText(null);
        return false;
      }
    };

    if (!navigator?.clipboard) {
      console.warn('Clipboard not supported');
      return copyWithOldWay();
    } else {
      return await copyWithClipboardAPI();
    }
  }, []);

  return { copiedText, copy };
}

export default useCopyToClipboard;
```

 Clipboard API 在权限满足去情况下可以直接 copy。`document.execCommand()` 需要先创建一个 DOM（`input` 或 `textarea`），并将其实际渲染到 document 上。再使用其 `el.select()` 方法，选中需要复制的文字，最后使用 `document.execCommand()` 执行 copy 命令。