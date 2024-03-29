
| 短语                        | 意义                                            |
| ------------------------- | --------------------------------------------- |
| `T`                       | 1) 所有可能类型的集合 _或_  <br>2) 上述集合中的某一个具体类型        |
| 所有权类型                     | 某些非引用类型，其自身拥有所有权 例如 `i32`, `String`, `Vec` 等等 |
| 1) 借用类型 _或_  <br>2) 引用类型  | 引用类型，不考虑可变性 例如 `&i32`, `&mut i32` 等等          |
| 1) 可变引用 _或_  <br>2) 独占引用  | 独占可变引用, 即 `&mut T`                            |
| 1) 不可变引用 _或_  <br>2) 共享引用 | 可共享不可变引用, 即 `&T`                              |

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

区分 `&'static T` 和 `T: 'static` 是非常重要的一点。

`&'static T` 是一个指向 `T` 的不可变引用，其中 `T` 可以被安全地无期限地持有，甚至可以直到程序结束。这只有在 `T` 自身不可变且保证 _在引用创建后_ 不会被 move 时才有可能。`T` 并不需要在编译时创建。 我们可以以内存泄漏为代价，在运行时动态创建随机数据，并返回其 `'static` 引用，比如：

```rust
use std::fmt::Display;

fn main() {
    let rand_string = leak_static();
    test_static(rand_string);
}

fn leak_static() -> &'static str {
    let rand_string = rand::random::<u64>().to_string();
    Box::leak(rand_string.into_boxed_str())
}

fn test_static<T: 'static + Display>(rand_string: T) {
    println!("random leak string {}", rand_string);
}
```

`T: 'static` 是指 `T` 可以被安全地无期限地持有，甚至可以直到程序结束。 `T: 'static` 在包括了全部 `&'static T` 的同时，还包括了全部所有权类型， 比如 `String`, `Vec` 等等。 数据的所有者保证，只要自身还持有数据的所有权，数据就不会失效，因此所有者能够安全地无期限地持有其数据，甚至可以直到程序结束。`T: 'static` 应当视为 _“`T` 满足 `'static` 生命周期约束”_ 而非 _“`T` 有着 `'static` 生命周期”_。 一个程序可以帮助阐述这些概念：

```rust
use rand;

fn drop_static<T: 'static>(t: T) {
    std::mem::drop(t);
}

fn main() {
    let mut strings: Vec<String> = Vec::new();
    for _ in 0..10 {
        if rand::random() {
            // 所有字符串都是随机生成的
            // 并且在运行时动态分配
            let string = rand::random::<u64>().to_string();
            strings.push(string);
        }
    }

    // 这些字符串是所有权类型，所以他们满足 'static 生命周期约束
    for mut string in strings {
        // 这些字符串是可变的
        string.push_str("a mutation");
        // 这些字符串都可以被 drop
        drop_static(string); // 编译通过
    }

    // 这些字符串在程序结束之前就已经全部失效了
    println!("i am the end of the program");
}
```

```rust
use rand::random;
use std::fmt::Display;

fn main() {
    let mut strings = Vec::new();
    for _ in 0..10 {
        if random() {
            let string = leak_static();
            strings.push(string)
        }
    }

    strings.iter().for_each(test_borrow);
    strings.into_iter().for_each(test_static);
    // strings.into_iter().for_each(drop_static);

    println!("end of the program");
}

fn leak_static() -> &'static str {
    let rand_string = rand::random::<u64>().to_string();
    Box::leak(rand_string.into_boxed_str())
}

fn test_borrow<T: Display + ?Sized>(rand_string: &T) {
    println!("random leak string {}", rand_string);
}

fn test_static<T: Display + ?Sized>(rand_string: &'static T) {
    println!("random leak string {}", rand_string);
}

fn drop_static<T: 'static>(target: T) {
    std::mem::drop(target);
}
```

总的来说就是

- `&'static T` 是一个指向 `T` 的不可变引用，其中 `T` 可以被安全地无期限地持有，甚至可以直到程序结束。
- `T: 'static` 是指 `T` 可以被安全地无期限地持有，甚至可以直到程序结束。 `T: 'static` 在包括了全部 `&'static T` 的同时，还包括了全部所有权类型。
- 和 `T` `&T` 类似的是，`T: 'static` 也包括了 `&'static T`。

**关键点回顾**

- `T: 'static` 应当视为 _“`T` 满足 `'static` 生命周期约束”_
- 若 `T: 'static` 则 `T` 可以是一个有 `'static` 生命周期的引用类型 _或_ 是一个所有权类型
- 因为 `T: 'static` 包括了所有权类型，所以 `T`
    - 可以在运行时动态分配
    - 不需要在整个程序运行期间都有效
    - 可以安全，自由地修改
    - 可以在运行时被动态的 drop
    - 可以有不同长度的生命周期

[common-rust-lifetime-misconceptions](https://github.com/pretzelhammer/rust-blog/blob/4ccb14209030cec02d02d8a103679d7c24bd50df/posts/translations/zh-hans/common-rust-lifetime-misconceptions.md)