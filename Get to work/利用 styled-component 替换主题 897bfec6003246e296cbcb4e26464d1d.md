# 利用 styled-component 替换主题

定义主题格式，以及存储在全局状态：

```tsx
export interface Theme {
  /**
   * 主要颜色
   */
  mainColor?: string;

  /**
   * 场景列表按钮
   */
  sceneListBtn?: {
    background: string;
    fontSize: number;
  };

  /**
   * 右下角按钮
   */
  rightBtn?: {
    background: string;
  };

  /**
   * 场景列表按钮
   */
  sceneTabs?: {
    parentTab: {
      // tab bar 背景
      tab: string;
      tabActive: string;
      tabColor: string;
      tabActiveColor: string;
    };
  };

  /**
   * 右下角弹出按钮
   */
  popupBtns?: Partial<BottomButton>[];
}
export interface ThemeState extends Theme {
  defaultTheme: boolean;
}

const initialState: ThemeState = {
  defaultTheme: !isDev,
  ...libraryTheme,
};

export const themeSlice = createSlice({
  name: 'theme',
  initialState,
  reducers: {},
});

export default themeSlice.reducer;
```

实际的主题格式，并加载到全局状态中：

```tsx
const libraryTheme: Theme = {
  mainColor,
  sceneListBtn: {
    background: sceneList,
    fontSize: 14,
  },
  rightBtn: {
    background: rightBtn,
  },
  sceneTabs: {
    parentTab: {
      tab: tabs,
      tabActive: tabsActive,
      tabColor: mainColor,
      tabActiveColor: '#fff',
    },
  },
  popupBtns: [
    {
      name: 'INTELLIGENT_QA',
      id: 'im',
      src: qa,
      visible: false,
    },
    {
      name: 'LEAVE_MESSAGE',
      id: 'leavemsg',
      src: leaveMsg,
      visible: false,
    },
    {
      name: 'VIP_MEETING',
      id: 'videochat',
      src: videoChat,
      visible: false,
    },
    {
      name: 'ONLINE_LIVE',
      id: 'live',
      src: live,
      visible: true,
    },
    {
      name: 'BUSINESS_CONTACT',
      id: 'business',
      src: business,
      visible: false,
    },
    {
      name: 'INTERNET_MESSAGE',
      id: 'message',
      src: message,
      visible: true,
    },
    {
      name: 'PROFILE',
      id: 'profile',
      src: profile,
      visible: true,
    },
    {
      name: 'EXHIBIT_LIST',
      id: 'exhibit',
      src: exhibit,
      visible: true,
    },
    {
      name: 'BOOK_SEARCH',
      id: 'book',
      src: book,
      visible: isDev,
    },
  ],
};

export default libraryTheme;
```

在 `styled-component` 中访问全局状态，并根据条件决定是否加载主题：

```tsx
const theme = store.getState().theme;

export const BtnItem = styled.div`
  margin: 0.12rem 0;
  width: 2.01rem;
  height: 0.62rem;
  background-size: 2.01rem 0.62rem;
  padding: 0 0 0 0.63rem;
  color: royalblue;

  ${() => {
    if (theme.defaultTheme) return;
    return css`
      color: ${theme.mainColor};
    `;
  }}
}
```