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

如果 selector 是从 store 中返回的对象，而不是从 selector 中创建的，由于 store 中的对象不会每次地址都不一样，则不会导致不必要的渲染。

```ts
// Memoized Selectors
// https://redux.js.org/usage/deriving-data-selectors#createselector-overview
export const sceneTabsSelector = (state: RootState) =>
  state.core.proj.data.scenesTabs;
export const currentSelector = (state: RootState) => state.core.current;

/**
 * 获取当前标签页下的场景列表 selector
 */
const emptyArray: never[] = [];
export const getTabScenes = createSelector(
  [sceneTabsSelector, currentSelector],
  (scenesTabs, current) => {
    if (current.parentIdx > -1 && current.childIdx > -1 && scenesTabs?.tabs) {
      return scenesTabs.tabs[current.parentIdx].content[current.childIdx]
        .content;
    }
    return emptyArray;
  }
);

/**
 * 获取当前单个场景 selector
 */
export const getCurrentScene = createSelector(
  [sceneTabsSelector, currentSelector],
  (scenesTabs, current) => {
    if (
      current.parentIdx > -1 &&
      current.childIdx > -1 &&
      current.currentIdx > -1 &&
      scenesTabs?.tabs
    ) {
      return scenesTabs.tabs[current.parentIdx].content[current.childIdx]
        .content[current.currentIdx];
    }
    return null;
  }
);

export const depthSelector = (state: RootState) =>
  state.core.proj.data.config?.depth;
/**
 * 获取 config.depth.embeds 或当前场景下的 currentScene.embeds
 */
export const getEmbeds = createSelector(
  depthSelector,
  getCurrentScene,
  (depth, currentScene) => {
    return (depth ? depth.embeds : currentScene?.embeds) || emptyArray;
  }
);
```