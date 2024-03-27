
在 Rust 中，所有的类型可以分为两类：

* 类型的值可以在内存中安全的移动，例如数值、字符串、布尔值、结构体、枚举等；
* 自引用值，保存对自身的引用的值；

## 自引用

Pin 就是为了解决一个主要的问题：自引用类型。例如一个结构体保存了一个引用类型，同时还有个指针指向这个引用类型。这是个非常常见的数据结构，在很多时候都很有用。但是它不是内存安全的，加上 Rust 会经常在内存中移动值，例如将这个结构体传给一个函数，在函数中它可能就被移动到了新的地址。或者使用了 `Box<T>` 将其放在了堆上。又或者该结构体被保存在了 `Vec<T>` 中，随着 Vec 的 capacity 增长，里面的值可能会被移动到一个新的地址（buffer）。

当我们移动结构体时，它只是地址被移动了，而结构体内的值不会被改变。


```rust
#[derive(Debug)]
struct SelfRef {
    value: String,
    pointer: *const String,
}
```
## 参考

- [Pin, Unpin, and why Rust needs them](https://blog.cloudflare.com/pin-and-unpin-in-rust)