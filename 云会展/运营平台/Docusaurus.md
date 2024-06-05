运营平台首页文档

## [](#%E6%96%87%E6%A1%A3%E7%9A%84%E6%9B%B4%E6%96%B0)文档的更新

```
git pull && pnpm i && pnpm start
```

1. 更新仓库

```
git pull
```

2. 安装依赖

```
pnpm i
```

3. 启动 dev server

```
pnpm start
```

## [](#%E9%A1%B5%E9%9D%A2%E7%9A%84%E6%9B%B4%E6%96%B0)页面的更新

一个 markdown 文件通常就是一个文档页面，在一个 markdown 文件中，可以在开头的部分添加对这个页面的描述信息。

```
---
slug: /
sidebar_position: 1
---
```

- `slug`: 这个页面在浏览器中的 url
- `sidebar_position`: 这个页面在侧边栏中的位置

### [](#%E6%96%87%E4%BB%B6%E6%A0%91)文件树

```
  docs
    tutorial-basics
    tutorial-extras
  │   img
  │ │  _category_.json
  │ │  manage-docs-versions.md
  │ └  translate-your-site.md
  └  intro.md
```

页面的路由在 docs 中由文件路由作为基准，当一个文件夹在侧边栏中作为父分类时，可以使用 `_category_.json` 来为其配置。

```
{
  "label": "Tutorial - Basics",
  "position": 2,
  "link": {
    "type": "generated-index",
    "description": "5 minutes to learn the most important Docusaurus concepts."
  }
}
```

## Coder

```
coder port-forward docs/showyun-docs --tcp 5000:3000
```