redux 将会对 selector 的返回值进行 `===` 比较，从而判断是否渲染该组件。

如果 selector 返回一个对象，由于对象的特殊性，每次返回的对象字面量内存地址都不一样，就会导致该组件进行不必要的渲染。

```ts
/**
 * Compares two arbitrary values for shallow equality. Object values are compared based on their keys, i.e. they must
 * have the same keys and for each key the value must be equal according to the `Object.is()` algorithm. Non-object
 * values are also compared with the same algorithm as `Object.is()`.
 */
export function shallowEqual<T>(left: T, right: any): boolean;
```

```ts
export type AppDispatch = typeof store.dispatch;
export const useAppDispatch = () => useDispatch<AppDispatch>();
export const useAppSelector: TypedUseSelectorHook<RootState> = useSelector;
export const useShallowEqualSelector: TypedUseSelectorHook<RootState> = (
  selector
) => useAppSelector(selector, shallowEqual);
```