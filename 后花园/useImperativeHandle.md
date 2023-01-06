子组件利用 `useImperativeHandle` 可以向父组件输出任意数据。

子组件可以利用 **`forwardRef`** 将父组件的 ref 接受，然后利用 `useImperativeHandle` 来为 ref 赋上指定值。之后父组件就可以访问 ref 中子组件提供的值。

```tsx
const Child = forwardRef<ImpertiveHandler, Props>(({ placeholder }, ref) => {
  const inputRef = useRef<HTMLInputElement>(null);

  useImperativeHandle(ref, () => ({
    focus: () => {
      inputRef.current?.focus();
    }
  }));

  return (
    <>
      <input
        ref={inputRef}
        className={css`
          border: gray solid 1px;
          border-radius: 6px;
          padding: 0.5rem;
          transition: all 0.3s ease-in-out;
          width: 250px;
        `}
        placeholder={placeholder}
        type="text"
      />
    </>
  );
});
```