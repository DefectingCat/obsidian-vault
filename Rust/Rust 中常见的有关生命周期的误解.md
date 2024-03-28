## `T` 只包含所有权类型

`T`, `&T`, 和 `&mut T` 都是无限集，`T` 是 `&T` 和 `&mut T` 的超集。`&T` 和 `&mut T` 是不相交的集合. 下面有一些例子来验证这些概念：

```rust
trait Trait {}

impl<T> Trait for T {}

impl<T> Trait for &T {} // 编译错误

impl<T> Trait for &mut T {} // 编译错误
```

编译器会告诉我们问题出在了哪里：

```rust
error[E0119]: conflicting implementations of trait `Trait` for type `&_`:
 --> src/lib.rs:5:1
  |
3 | impl<T> Trait for T {}
  | ------------------- first implementation here
4 |
5 | impl<T> Trait for &T {}
  | ^^^^^^^^^^^^^^^^^^^^ conflicting implementation for `&_`

error[E0119]: conflicting implementations of trait `Trait` for type `&mut _`:
 --> src/lib.rs:7:1
  |
3 | impl<T> Trait for T {}
  | ------------------- first implementation here
...
7 | impl<T> Trait for &mut T {}
  | ^^^^^^^^^^^^^^^^^^^^^^^^ conflicting implementation for `&mut _`
```

编译器不允许我们为 `&T` 和 `&mut T` 实现 `Trait`，因为这与我们为 `T` 实现的 `Trait` 发生了冲突，而 `T` 已经包括了 `&T` 和 `&mut T`. 因为 `&T` 和 `&mut T` 是不相交的，所以下面的代码可以通过编译：

```rust
trait Trait {}

impl<T> Trait for &T {} // 编译通过

impl<T> Trait for &mut T {} // 编译通过
```

**关键点回顾**

- `T` 是 `&T` 和 `&mut T` 的超集
- `&T` 和 `&mut T` 是不相交的集合

## 如果 `T: 'static` 那么 `T` 直到程序结束为止都一定是有效的

`'static` 生命周期通常用来标注静态变量，关于静态变量，他们表示：

- 只能在编译期创建
- 应当是不可变的，修改静态变量是 unsafe 的
- 在整个程序运行期间有效

但 _持有_ `'static` 生命周期注解的类型和一个满足 `'static` _约束_ 的类型是不一样的。后者可以于运行时被动态分配，能被安全自由地修改，也可以被 drop, 还能存活任意的时长。

[common-rust-lifetime-misconceptions](https://github.com/pretzelhammer/rust-blog/blob/4ccb14209030cec02d02d8a103679d7c24bd50df/posts/translations/zh-hans/common-rust-lifetime-misconceptions.md)