
## `T` 只包含所有权类型

`T`, `&T`, 和 `&mut T` 都是无限集，`T` 是 `&T` 和 `&mut T` 的超集。`&T` 和 `&mut T` 是不相交的集合. 下面有一些例子来验证这些概念：

```rust
trait Trait {}

impl<T> Trait for T {}

impl<T> Trait for &T {} // 编译错误

impl<T> Trait for &mut T {} // 编译错误
```

[common-rust-lifetime-misconceptions](https://github.com/pretzelhammer/rust-blog/blob/4ccb14209030cec02d02d8a103679d7c24bd50df/posts/translations/zh-hans/common-rust-lifetime-misconceptions.md)