# Initial redux in getServerSideProps

每个页面的 `getServerSideProps` 所传递的 `props` 都能被 `_app` 的参数接收中 `pageProps` 到。

所以在 `index.tsx` 中检测 cookie 中的用户信息，可以将其传递给 `_app` 。

```tsx
export const getServerSideProps: GetServerSideProps = async ({ req }) => {
  const cookies = nookies.get({ req });
  const token = cookies.token;
  if (!token)
    return {
      props: {},
    };

  const { payloadObj }: { payloadObj: any } = decodeJWT(token);
  if (!payloadObj)
    return {
      props: {},
    };

  return {
    props: {
      initialState: {
        users: {
          logined: true,
          ...payloadObj.data,
        },
      },
    },
  };
};
```

其次还需要对 `store.ts` 进行一些小小的修改，使其可以接受一个初始化的状态值。并导出一个方法，在 `_app` 中初始化 redux store。

```tsx
import { configureStore, ConfigureStoreOptions } from '@reduxjs/toolkit';
import usersReducer, { UserState } from './features/user/userSlice';

export let store: null | ReturnType<typeof configureStore<{
  users: UserState;
}>> = null

export const createStore = (
  preloadedState?: ConfigureStoreOptions['preloadedState']
) => {
  if (typeof window === 'undefined') {
    return configureStore({
      reducer: { users: usersReducer },
      preloadedState,
    });
  }

  if (!store) {
    store = configureStore({
      reducer: { users: usersReducer },
      preloadedState,
    });
  }

  return store;
};
```

根据 store 中的修改，可以让在 `index.tsx` 中传递的初始状态在 `_app` 创建带有初始值的 redux。

```tsx
function MyApp({ Component, pageProps }: AppPropsWithLayout) {
  const getLayout = Component.getLayout ?? ((page) => page);

  const { initialState } = pageProps;
  const store = createStore(initialState);

  return (
    <>
      <Provider store={store}>
        {getLayout(<Component {...pageProps} />)}
      </Provider>
    </>
  );
}
```