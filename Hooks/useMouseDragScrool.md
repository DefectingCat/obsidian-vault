```tsx
import React, { useCallback, useEffect, useRef } from 'react';
import Scrollbars from 'react-custom-scrollbars-2';

const useMouseDragScrool = (ref?: React.RefObject<Scrollbars>) => {
  // ScrollBars
  const _scrollRef = useRef<Scrollbars>(null);
  const scrollRef = ref ?? _scrollRef;
  // ScrollBars 可滚动的 container
  const scrollWrapper = useRef<Element>();
  // 可滚动的最大距离
  const maxScrollWidth = useRef(0);

  useEffect(() => {
    if (scrollRef.current?.container.firstElementChild) {
      scrollWrapper.current = scrollRef.current?.container.firstElementChild;
    }
    if (
      scrollRef.current?.getScrollWidth &&
      scrollWrapper.current?.clientWidth != null
    ) {
      // scrollLeft 最大距离：可滚动的距离 - 元素宽度
      maxScrollWidth.current =
        scrollRef.current.getScrollWidth() - scrollWrapper.current.clientWidth;
    }
  });

  // 鼠标按下时的坐标
  const startPosition = useRef({
    x: -1,
    y: -1,
  });
  // 按下时已经滚动的距离
  const scrolledLeft = useRef(0);
  // 鼠标按下状态
  const isMouseDown = useRef(false);
  const handleMouseDown: React.MouseEventHandler<Scrollbars> = useCallback(
    (e) => {
      isMouseDown.current = true;
      startPosition.current = {
        x: e.clientX,
        y: e.clientY,
      };
      if (scrollWrapper.current?.scrollLeft == null) return;
      scrolledLeft.current = scrollWrapper.current.scrollLeft;
    },
    []
  );

  // 移动的距离
  const movePosition = useRef({
    x: -1,
    y: -1,
  });
  /**
   * x：负数鼠标向左移动，正数鼠标向右移动
   * @TODO 纵向移动
   */
  const handleMouseMove: React.MouseEventHandler<Scrollbars> = useCallback(
    (e) => {
      if (!isMouseDown.current) return;
      if (scrollWrapper.current?.scrollLeft == null) return;
      movePosition.current = {
        x: e.clientX - startPosition.current.x,
        y: e.clientY - startPosition.current.y,
      };
      const newLeft = scrolledLeft.current - movePosition.current.x;

      // 鼠标向左移动，element 向右滚动，限制到最大移动距离
      if (movePosition.current.x < 0) {
        if (newLeft > maxScrollWidth.current) {
          scrollWrapper.current.scrollLeft = maxScrollWidth.current;
        } else {
          scrollWrapper.current.scrollLeft = newLeft;
        }
      }
      // 鼠标向右移动，element 向左滚动，限制到 0
      if (movePosition.current.x > 0) {
        if (newLeft < 0) {
          scrollWrapper.current.scrollLeft = 0;
        } else {
          scrollWrapper.current.scrollLeft = newLeft;
        }
      }
    },
    []
  );

  const handleMouseUp = useCallback(() => {
    isMouseDown.current = false;
  }, []);

  useEffect(() => {
    document.body.addEventListener('mouseleave', handleMouseUp);
    return () => {
      document.body.removeEventListener('mouseleave', handleMouseUp);
    };
  }, [handleMouseUp]);

  return {
    scrollRef,
    handleMouseDown,
    handleMouseMove,
    handleMouseUp,
  };
};

export default useMouseDragScrool;
```