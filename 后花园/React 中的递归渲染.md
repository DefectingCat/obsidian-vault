数据结构大概是这样的：

```tsx
export default [
  {
    id: 18,
    pid: 0,
    typename: "分类1",
    e_id: 26,
    createtime: " ",
    path: "0",
    kid_type: [
      {
        id: 19,
        pid: 18,
        typename: "分类1-1",
        createtime: " ",
        child: [
          {
            id: 22,
            pid: 19,
            typename: "test",
            createtime: "2022-07-08 14:16:55",
            child: [
              {
                id: 23,
                pid: 22,
                typename: "abc",
                createtime: "2022-07-08 14:17:03",
                child: []
              }
            ]
          },
          {
            id: 24,
            pid: 19,
            typename: "test23",
            createtime: "2022-07-08 14:17:08",
            child: []
          }
        ]
      }
    ]
  },
  {
    id: 20,
    pid: 0,
    typename: "分类2",
    e_id: 26,
    createtime: "2022-05-05 14:01:53",
    path: "0",
    kid_type: [
      {
        id: 21,
        pid: 20,
        typename: "分类2-2",
        createtime: "2022-05-05 14:02:03",
        child: []
      }
    ]
  }
];
```

其中第一层数据的子属性表示不同，是 `kid_type` 。除此之外，所有的子属性都是 `child` 。

所以需要单独渲染第一层与第二层数据。

```tsx
			<div className="category">
        <div
          className={`item ${id === -1 && "active"}`}
          onClick={() => handleParentClick(-1)}
        >
          全部分类
        </div>

        {data.map((item) => (
          <div
            className={`item ${id === item.id && "active"}`}
            onClick={() => handleParentClick(item.id)}
            key={item.id}
          >
            {item.typename}
          </div>
        ))}
      </div>

			{cate?.kid_type.map((item) => (
        <div className="category">
          <div
            className={`item ${id === item.id && "active"}`}
            onClick={() => handleChildClick(item.id)}
            key={item.id}
          >
            {item.typename}
          </div>
        </div>
      ))}
```

剩下的所有数据子标签，数量不定嵌套，但所有属性都为 `child` 。所以可以利用函数来递归创建 DOM。

```tsx
	const generateCategory = useCallback(
    (item: ExhibitTypesKidType) => {
      return (
        <div className="category">
          {item.child.map((kid) => (
            <div
              key={kid.id}
              className={`item ${id === kid.id && "active"}`}
              onClick={() => handleChildClick(kid.id)}
            >
              {kid.typename}
            </div>
          ))}
        </div>
      );
    },
    [handleChildClick, id]
  );
  const categoryWalker = useCallback(() => {
    const tempList: JSX.Element[] = [];
    const pushList = (kid: ExhibitTypesKidType) => {
      kid.child.map((item) =>
        !!item.child.length
          ? pushList(item)
          : tempList.unshift(generateCategory(kid))
      );
    };
    cate?.kid_type.forEach((kid) => pushList(kid));
    return tempList;
  }, [cate?.kid_type, generateCategory]);
```

![Untitled](后花园/React%20中的递归渲染%208fdae296a40748c7bb480170b6eaba89/Untitled.png)