
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

## `&'a T` 和 `T: 'a` 是一回事

`&'a T` 要求并隐含了 `T: 'a` ，因为如果 `T` 本身不能在 `'a` 范围内保证有效，那么其引用也不能在 `'a` 范围内保证有效。例如，Rust 编译器不会运行构造一个 `&'static Ref<'a, T>`，因为如果 `Ref` 只在 `'a` 范围内有效，我们就不能给它 `'static` 生命周期。

`T: 'a` 包括了全体 `&'a T`，但反之不成立。

```rust
// 只接受带有 'a 生命周期注解的引用类型
fn t_ref<'a, T: 'a>(t: &'a T) {}

// 接受满足 'a 生命周期约束的任何类型
fn t_bound<'a, T: 'a>(t: T) {}

// 内部含有引用的所有权类型
struct Ref<'a, T: 'a>(&'a T);

fn main() {
    let string = String::from("string");

    t_bound(&string); // 编译通过
    t_bound(Ref(&string)); // 编译通过
    t_bound(&Ref(&string)); // 编译通过

    t_ref(&string); // 编译通过
    t_ref(Ref(&string)); // 编译失败，期望得到引用，实际得到 struct
    t_ref(&Ref(&string)); // 编译通过

    // 满足 'static 约束的字符串变量可以转换为 'a 约束
    t_bound(string); // 编译通过
}
```

**关键点回顾**

- `T: 'a` 比 `&'a T` 更泛化，更灵活
- `T: 'a` 接受所有权类型，内部含有引用的所有权类型，和引用
- `&'a T` 只接受引用
- 若 `T: 'static` 则 `T: 'a` 因为对于所有 `'a` 都有 `'static` >= `'a`

## 我的代码里不含泛型也不含生命周期注解

**错误的推论**

- 避免使用泛型和生命周期注解是可能的

这个让人爽到的误解之所以能存在，要得益于 Rust 的生命周期省略规则，这个规则能允许你在函数定义以及 `impl` 块中省略掉显式的生命周期注解，而由借用检查器来根据以下规则对生命周期进行隐式推导。

- 第一条规则是每一个是引用的参数都有它自己的生命周期参数
- 第二条规则是如果只有一个输入生命周期参数，那么它被赋予所有输出生命周期参数
- 第三条规则是如果是有多个输入生命周期参数的方法，而其中一个参数是 `&self` 或 `&mut self`, 那么所有输出生命周期参数被赋予 `self` 的生命周期。
- 其他情况下，生命周期必须有明确的注解

这里有不少值得讲的东西，让我们来看一些例子：

```rust
// 展开前
fn print(s: &str);

// 展开后
fn print<'a>(s: &'a str);

// 展开前
fn trim(s: &str) -> &str;

// 展开后
fn trim<'a>(s: &'a str) -> &'a str;

// 非法，没有输入，不能确定返回值的生命周期
fn get_str() -> &str;

// 显式标注的方案
fn get_str<'a>() -> &'a str; // 泛型版本
fn get_str() -> &'static str; // 'static 版本

// 非法，多个输入，不能确定返回值的生命周期
fn overlap(s: &str, t: &str) -> &str;

// 显式标注（但仍有部分标注被省略）的方案
fn overlap<'a>(s: &'a str, t: &str) -> &'a str; // 返回值的生命周期不长于 s
fn overlap<'a>(s: &str, t: &'a str) -> &'a str; // 返回值的生命周期不长于 t
fn overlap<'a>(s: &'a str, t: &'a str) -> &'a str; // 返回值的生命周期不长于 s 且不长于 t
fn overlap(s: &str, t: &str) -> &'static str; // 返回值的生命周期可以长于 s 或者 t
fn overlap<'a>(s: &str, t: &str) -> &'a str; // 返回值的生命周期与输入无关

// 展开后
fn overlap<'a, 'b>(s: &'a str, t: &'b str) -> &'a str;
fn overlap<'a, 'b>(s: &'a str, t: &'b str) -> &'b str;
fn overlap<'a>(s: &'a str, t: &'a str) -> &'a str;
fn overlap<'a, 'b>(s: &'a str, t: &'b str) -> &'static str;
fn overlap<'a, 'b, 'c>(s: &'a str, t: &'b str) -> &'c str;

// 展开前
fn compare(&self, s: &str) -> &str;

// 展开后
fn compare<'a, 'b>(&'a self, &'b str) -> &'a str;
```

如果你写过

- 结构体方法
- 接收参数中有引用的函数
- 返回值是引用的函数
- 泛型函数
- trait object(后面将讨论)
- 闭包（后面将讨论）

那么对于上面这些，你的代码中都有被省略的泛型生命周期注解。

**关键点回顾**

- 几乎所有的 Rust 代码都是泛型代码，并且到处都带有被省略掉的泛型生命周期注解

## 如果编译通过了，那么我标注的生命周期就是正确的

Rust 编译器为我们省略的生命周期不总是正确的，这里指的更多的是语义上的。编译器只会保证检查时我们的代码是内存安全的，其他它就不会去尝试负责了。

```rust
struct ByteIter<'a> {
    remainder: &'a [u8],
}

impl<'a> ByteIter<'a> {
    fn next(&mut self) -> Option<&u8> {
        if self.remainder.is_empty() {
            None
        } else {
            let byte = &self.remainder[0];
            self.remainder = &self.remainder[1..];
            Some(byte)
        }
    }
}

fn main() {
    let mut bytes = ByteIter {
        remainder: b"hello world",
    };
    assert_eq!(Some(&b'h'), bytes.next());
}
```

`ByteIter` 是一个在 `&[u8]` 上的迭代器，每次获取一个字节并返回。这里只用一个 `<'a>` 的生命周期来表示数组的生命周期。在 `next()` 方法的实现中，剩下的生命周期交给编译器给我们推断。

这样直接运行好像没有什么问题，但是当需要获取更多的字节时，生命周期的问题就会暴露出来了。

```rust
fn main() {
    let mut bytes = ByteIter {
        remainder: b"hello world",
    };
    assert_eq!(Some(&b'h'), bytes.next());
    let byte1 = bytes.next();
    let byte2 = bytes.next();
    if byte1 == byte2 {}
}
```

```rust
error[E0499]: cannot borrow `bytes` as mutable more than once at a time
  --> examples/byte_iter.rs:23:17
   |
22 |     let byte1 = bytes.next();
   |                 ----- first mutable borrow occurs here
23 |     let byte2 = bytes.next();
   |                 ^^^^^ second mutable borrow occurs here
24 |     if byte1 == byte2 {}
   |        ----- first borrow later used here
```

发生这个问题的主要原因就是编译器为我们推断的被省略的生命周期，将其简单的展开下就不难发现，`next()` 方法返回的字节与 `self` 是具有相同的生命周期的。这就会直接导致了我们不能多次的调用位于 `&mut self` 上的 `next()` 方法，原因编译器也给我们解释的很清楚了：cannot borrow `bytes` as mutable more than once at a time.

```rust
struct ByteIter<'a> {
    remainder: &'a [u8]
}

impl<'a> ByteIter<'a> {
    fn next<'b>(&'b mut self) -> Option<&'b u8> {
        if self.remainder.is_empty() {
            None
        } else {
            let byte = &self.remainder[0];
            self.remainder = &self.remainder[1..];
            Some(byte)
        }
    }
}
```

了解了原因之后解决也非常的简单，我们返回的字节是从 `remainder` 上摘下来的引用，返回之后就和 `self` 一点关系也没有了。所以我们需要将其生命周期分开。

```rust
impl<'remainder> Iterator for ByteIter<'remainder> {
    type Item = &'remainder u8;
    fn next(&mut self) -> Option<Self::Item> {
        if self.remainder.is_empty() {
            None
        } else {
            let byte = &self.remainder[0];
            self.remainder = &self.remainder[1..];
            Some(byte)
        }
    }
}
```

将返回的字节生命周期和 `self` 区分开来，返回的字节是属于声明周期为 `remainder` 的字节数组，而后续在调用 `mut self` 也不会有影响。我们通过

[common-rust-lifetime-misconceptions](https://github.com/pretzelhammer/rust-blog/blob/4ccb14209030cec02d02d8a103679d7c24bd50df/posts/translations/zh-hans/common-rust-lifetime-misconceptions.md)