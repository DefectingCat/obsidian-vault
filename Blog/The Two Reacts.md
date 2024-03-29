> 俩 React！

假设我想在你的屏幕上显示些内容，无论是类似这篇文章一样的网页，或是一个可交互的网页 APP，甚至是一个需要从应用商店下载的原生 APP，都至少需要两台设备。

你的设备和我的设备。

> 客户端与服务端。

这都要从我的设备上的代码和数据开始。例如，我从我的笔记本上编辑的这篇文章的文件，如果你要在你的屏幕上看到，那它就已经从我的设备上传输到你的设备上了。换句话说，我的代码和数据变成了 HTML 和 JavaScript 来决定如何在你的设备上渲染。

所以这和 React 有啥关系？React 是一个 UI 编程范式（programming paradigm），能够使我将一些称之为组件的部分组合到一起，然后决定来显示什么东西
（一篇博客文章，一个注册表单，或一整个 APP），就像乐高的积木一样。这里假设你已经了解且喜欢组件，[react.dev](https://react.dev/) 上有更多关于组件的信息。
组件即是代码，这些代码得在某个计算机上运行。但是，该由谁的机器来运行？是应该跑在你的机器上，还是应该在我的机器上？

来点栗子。

首先，我们决定组件应该运行在你的（客户）机器上。