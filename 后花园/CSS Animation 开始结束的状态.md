https://developer.mozilla.org/en-US/docs/Web/CSS/animation-fill-mode

有些时候利用 CSS 动画来改变一个元素的状态时，根据动画播放的状态（开始，结束）需要元素展示不同的状态。简单来说就是动画结束后需要元素保持在结束的那段 CSS 上。

```css
@keyframes grow {
  0% {
    font-size: 0;
  }
  100% {
    font-size: 40px;
  }
}

animation-fill-mode: none;
animation-fill-mode: forwards;
animation-fill-mode: backwards;
animation-fill-mode: both;
```