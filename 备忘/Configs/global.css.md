```css
@tailwind base;
@tailwind components;
@tailwind utilities;

* {
  scroll-behavior: smooth;
}

*::selection {
  background: hsl(230, 1%, 90%);
  color: inherit;
}

.dark *::selection {
  background: hsl(220, 13%, 28%);
  color: inherit;
  text-shadow: none;
}

/* 
font-family: 'Aleo', serif;
font-family: 'Aref Ruqaa', serif;
font-family: 'Barlow', sans-serif;
font-family: 'JetBrains Mono', monospace;
font-family: 'Poppins', sans-serif;
*/

@layer base {
  body {
    @apply text-gray-600 bg-bluish-gray dark:bg-rua-gray-900 dark:text-gray-200;
    font-family: 'Poppins', -apple-system, BlinkMacSystemFont, 'Helvetica Neue',
      Helvetica, Segoe UI, Arial, Roboto, 'PingFang SC', miui,
      'Hiragino Sans GB', 'Microsoft Yahei', sans-serif;
  }

  img,
  iframe {
    @apply dark:brightness-75;
  }

  /* 滚动槽 */
  ::-webkit-scrollbar {
    width: 7px;
    height: 7px;
  }

  ::-webkit-scrollbar-track {
    border-radius: 3px;
    box-shadow: inset 0 0 5px rgba(0, 0, 0, 0.08);
  }

  ::-webkit-scrollbar-track-piece {
    background: 0 0;
  }

  /* 滚动条滑块 */
  ::-webkit-scrollbar-thumb {
    @apply bg-gray-400;
    border-radius: 3px;
    box-shadow: inset 0 0 10px rgba(185, 69, 69, 0.2);
  }
}
```