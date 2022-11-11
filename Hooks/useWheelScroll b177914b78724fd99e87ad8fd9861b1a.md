# useWheelScroll

```tsx
import { useEffect, useRef } from 'react';
import Scrollbars from 'react-custom-scrollbars-2';

const useWheelScroll = () => {
  //  innerWidth - aside - margin - padding
  const editorWidth = window.innerWidth - 378 - 48;
  // ScrollBars
  const scrollRef = useRef<Scrollbars>(null);
  // 滚动的距离
  const scrollLeft = useRef(0);
  // ScrollBars 可滚动的 container
  const scrollWrapper = useRef<Element>();
  // 可滚动的最大距离
  const maxScrollWidth = useRef(0);

  /**
   * 保存 ScrollBars 可滚动的 container 到 ref 中
   * 再监听滚动事件时，横向滚动 container
   */
  useEffect(() => {
    if (scrollRef.current?.container.firstElementChild) {
      scrollWrapper.current = scrollRef.current?.container.firstElementChild;
    }
    if (
      scrollRef.current?.getScrollWidth &&
      scrollWrapper.current?.clientWidth != null
    ) {
      maxScrollWidth.current =
        scrollRef.current.getScrollWidth() - scrollWrapper.current.clientWidth;
    }
  });

  const handleWheel: React.WheelEventHandler<Scrollbars> = (e) => {
    const direction = Math.sign(e.deltaY);
    const newLeft = scrollLeft.current + direction * 30;

    //  1 滚轮向右 -1 滚轮向左
    if (scrollWrapper.current?.scrollLeft == null) return;
    if (direction === 1) {
      newLeft > 0 ? (scrollLeft.current = newLeft) : (scrollLeft.current = 0);
    }
    if (direction === -1) {
      newLeft > maxScrollWidth.current + direction * 20
        ? (scrollLeft.current = maxScrollWidth.current)
        : (scrollLeft.current = newLeft);
    }

    scrollWrapper.current.scrollLeft = scrollLeft.current;
  };

  return {
    editorWidth,
    scrollRef,
    scrollLeft,
    scrollWrapper,
    maxScrollWidth,
    handleWheel,
  };
};

export default useWheelScroll;
```