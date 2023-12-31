https://www.youtube.com/watch?v=Vw8BFScm0K0&t=1335s

与平时接触的不同的是，Rust 中的 const 不仅仅只是一个表示不可变的变量，同时还是表示它仅在编译期初始化的值。这就是为什么在 Rust 中初始化一个全局变量如此困难，如果一个方法不是 `const fn` 那么它将无法为 `const` 关键字所用。

如上所述，`const fn` 即为编译器在编译时期就可以执行并产出结果的方法。

```rust
const fn five_numbers() -> [u32; 5] {
    let mut numbers = [0u32; 5];

    let mut i = 0;
    while i < 5 {
        numbers[i] = i as u32 + 1;
        i += 1;
    }

    numbers
}
```

这使得我们可以构建一些可以在编译期就执行的方法，从而节省宝贵的运行时间。

> 目前的 `const fn` 不支持迭代器，同样也无法支持 `for loop`。

除此之外，`const fn` 还可以是范型的，同时可以接受一个 `const generic`