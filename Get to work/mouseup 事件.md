在 `mousemove` 移除元素本身后，`mouseup` 事件会无法触发。

可以通过在 document 上监听 `mouseup` 来解决。