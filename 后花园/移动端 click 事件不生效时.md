在移动端 click 事件有可能会无法生效，这时可以使用  touchstart 事件代替。

在使用 https://github.com/react-grid-layout/react-draggable 时，子元素的 `onClick` 无法被正确触发到。使用 `onTouchStart` 即可正常触发。

```tsx
<Draggable onStop={handleClick}>
  <S.Wrapper>
      <S.VideoTools show={mobileHang}>
        <HangupIcon onClick={hangUp} onTouchStart={hangUp} />
      </S.VideoTools>
    </S.VideoWrapper>
  </S.Wrapper>
</Draggable>
```