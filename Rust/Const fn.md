https://www.youtube.com/watch?v=Vw8BFScm0K0&t=1335s

与平时接触的不同的是，Rust 中的 const 不仅仅只是一个表示不可变的变量，同时还是表示它仅在编译期初始化的值。这就是为什么在 Rust 中初始化一个全局变量如此困难，如果一个方法不是 `const fn` 那么它将无法为 `const` 关键字所用。

如上所述，