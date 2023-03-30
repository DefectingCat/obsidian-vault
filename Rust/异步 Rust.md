`async` 是 Rust 选择的异步模型。

并发在现代社会非常重要，多数主流语言都有对自己并发模型的设计以及取舍。

-   **OS 线程**, 它最简单，也无需改变任何编程模型(业务/代码逻辑)，因此非常适合作为语言的原生并发模型，在[多线程章节](https://course.rs/advance/concurrency-with-threads/concurrency-parallelism.html)也提到过，Rust 就选择了原生支持线程级的并发编程。但是，这种模型也有缺点，例如线程间的同步将变得更加困难，线程间的上下文切换损耗较大。使用线程池在一定程度上可以提升性能，但是对于 IO 密集的场景来说，线程池还是不够。
-   **事件驱动(Event driven)**, 这个名词你可能比较陌生，如果说事件驱动常常跟回调( Callback )一起使用，相信大家就恍然大悟了。这种模型性能相当的好，但最大的问题就是存在回调地狱的风险：非线性的控制流和结果处理导致了数据流向和错误传播变得难以掌控，还会导致代码可维护性和可读性的大幅降低，大名鼎鼎的 `JavaScript` 曾经就存在回调地狱。
-   **协程(Coroutines)** 可能是目前最火的并发模型，`Go` 语言的协程设计就非常优秀，这也是 `Go` 语言能够迅速火遍全球的杀手锏之一。协程跟线程类似，无需改变编程模型，同时，它也跟 `async` 类似，可以支持大量的任务并发运行。但协程抽象层次过高，导致用户无法接触到底层的细节，这对于系统编程语言和自定义异步运行时是难以接受的
-   **actor 模型**是 erlang 的杀手锏之一，它将所有并发计算分割成一个一个单元，这些单元被称为 `actor` , 单元之间通过消息传递的方式进行通信和数据传递，跟分布式系统的设计理念非常相像。由于 `actor` 模型跟现实很贴近，因此它相对来说更容易实现，但是一旦遇到流控制、失败重试等场景时，就会变得不太好用
-   **async/await**， 该模型性能高，还能支持底层编程，同时又像线程和协程那样无需过多的改变编程模型，但有得必有失，`async` 模型的问题就是内部实现机制过于复杂，对于用户来说，理解和使用起来也没有线程和协程简单，好在前者的复杂性开发者们已经帮我们封装好，而理解和使用起来不够简单，正是本章试图解决的问题。

Rust 选择的取舍是，同时支持系统 OS 线程和 async 异步：

* 系统线程通过标准库实现，应对不是非常的并发，而需要并行计算时。优点是线程内的代码执行效率更高、实现更直观更简单。
* 异步通过语言特性 + 标准库 + 三方库的方式实现。并发性能会更好，但也需要额外的运行时支持。

Rust 中异步的特点：

-   **Future 在 Rust 中是惰性的**，只有在被轮询(`poll`)时才会运行， 因此丢弃一个 `future` 会阻止它未来再被运行, 你可以将`Future`理解为一个在未来某个时间点被调度执行的任务。
-   **Async 在 Rust 中使用开销是零**， 意味着只有你能看到的代码(自己的代码)才有性能损耗，你看不到的(`async` 内部实现)都没有性能损耗，例如，你可以无需分配任何堆内存、也无需任何动态分发来使用 `async` ，这对于热点路径的性能有非常大的好处，正是得益于此，Rust 的异步编程性能才会这么高。
-   **Rust 没有内置异步调用所必需的运行时**，但是无需担心，Rust 社区生态中已经提供了非常优异的运行时实现，例如大明星 [`tokio`](https://tokio.rs/)。
-   **运行时同时支持单线程和多线程**。

## 异步函数

一个普通函数会在调用时会立即执行，而一个异步函数的行为并非如此。

```rust
fn main() {
    normal_fool();  // print right now.
}

fn normal_fool() {
    println!("normal_fool");
}
```

异步函数的情况

## 与 JavaScript 相比

与老朋友 JavaScript 不同的时，**Future 在 Rust 中是惰性的**。在 JavaScript 中，创建一个异步的任务，无论是任务队列，还是 Promise，它都会立即执行。而且 JavaScript 自带异步运行时。

* JavaScript 中的异步任务会立即被添加到任务队列中；
* JavaScript 自带异步运行时；
* JavaScript 中 `async/await` 语法只是 Promise 到语法糖；

在 JavaScript 中，一个异步任务会被立即添加到任务队列中的末尾。如果不做任何处理，则该异步任务会立即脱离当前环境。

```js
const sleep = async (duration) => {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve();
    }, duration);
  });
};

const main = async () => {
  console.log("hello");
  sleep(1000);
  console.log("world");
};

main();
```

此处的 `sleep` 函数则会在之前到当前行时立即被添加到任务队列的末尾。等到 `main` 函数其他同步代码都执行完成后，才会在等待 1000ms 并结束 `main` 函数。这是 JavaScript 最基本的异步运行方式。

## Future

但 Rust 首当其冲不同的就是，Future 是惰性的。一个 async 函数会返回一个 `Future`，若直接调用该函数，不会发生任何事，因为 Future 还没有被执行。

Future 人如其名，可以理解为它是一个定义在未来被执行的任务，若不触发它则不会执行。使用一个 Future 需要使用一个执行器。

`futures` 包为我们提供了一个最简单粗暴的执行器：`block_on`。它会阻塞当前线程以等待任务完成。

```rust
use futures::executor::block_on;

fn main() {
    let future = sleep();

    block_on(future);
}

async fn sleep() {
    println!("Try to sleep.")
}
```

### await

既然一个函数被标记为了 async 函数，那肯定少不了它的搭档 await。在一个 async 函数执行另一个 async 函数的方法就是使用 await。不过与 JavaScript 或 C# 等语言不同的是，在 Rust 中使用 await 看上去更像是一个方法的调用：

```rust
use futures::executor::block_on;

fn main() {
    let future = sleep();

    block_on(future);
}

async fn sleep() {
    hello_cat().await;
    println!("Try to sleep.")
}

async fn hello_cat() {
    println!("Hello kitty.")
}
```

如果移除 await 字段，则编译器会给我们 `futures do nothing unless you .await or poll them` 的提示。出了 await 的方式运行 async 函数，还有个就是使用轮询。

与 `block_on` 不同的时，await 不会阻塞当前的线程。而是异步的等待 Future 的完成，在等待的过程中，该线程还可以继续执行其他的任务，从而实现了并发处理的效果。

```rust
#![feature(future_join)]
use async_std::task::sleep;
use futures::executor::block_on;
use std::future::join;
use std::time::Duration;

fn main() {
    let f1 = try_sleep();
    let f2 = hello_dog();

    block_on(join!(f1, f2));
}

async fn try_sleep() {
    hello_cat().await;
    sleep(Duration::from_secs(3)).await;
    println!("Try to sleep.")
}

async fn hello_cat() {
    println!("Hello kitty.")
}

async fn hello_dog() {
    println!("Hello puppy");
}
```

`join!` 宏可以并发的处理和等待多个 Future，当 `try_sleep` 被阻塞时，则 `hello_dog` 可以拿过线程的所有权继续执行。通过这种方式实现了**使用同步的代码顺序实现了异步的执行效果**。

## Future 特征

与 JavaScript 将异步任务放到任务队列尾端不同的时，Rust 的 Future 特征恰恰是异步函数的返回值和被执行的关键。

```rust
trait SimpleFuture {
    type Output;
    fn poll(&mut self, wake: fn()) -> Poll<Self::Output>;
}

#[derive(Debug)]
enum Poll<T> {
    Ready(T),
    Pending,
}
```

这里模拟真正的 Future 实现一个简易版的 `SimpleFuture`。一个 Future 需要被执行器 `poll` 轮询后才能执行，通过调用该方法，可以推进 Future 的进一步执行，直到被阻塞将运行权转移给其他 Future。

若在当前 `poll` 中，Future 可以被完成，则会返回 `Poll::Ready(result)`，反之则返回 `Poll::Pending`。并安排一个 `wake` 函数，当未来 Future 准备好被切回来继续执行时，该函数就会被调用。然后继续刚开始的步骤，该 Future 的执行器会再次调用 `poll` 方法，此时 Future 就可以继续执行了。

再来模拟一个简单的 Socket，并从一个 socket 中异步读取数据。当该 socket 中有数据时，就直接读取数据并返回 `Poll::Ready(data)`；如果数据还没准备好，Future 会被阻塞且不再继续执行，并注册一个 `wake` 函数，当 socket 数据准备好的时候，该函数将被调用以通知执行器继续执行。

```rust
use std::{thread::sleep, time::Duration};

pub struct Socket {
    pub test: u8,
}

impl Socket {
    pub fn has_data_to_read(&self) -> bool {
        return false;
    }

    pub fn read_buf(&self) -> Vec<u8> {
        return vec![];
    }

    pub fn set_readable_callback(&self, wake: fn()) {
        sleep(Duration::from_secs(3));
        wake();
    }
}
```

```rust
pub struct SocketRead<'a> {
    socket: &'a Socket,
}

impl SimpleFuture for SocketRead<'_> {
    type Output = Vec<u8>;

    fn poll(&mut self, wake: fn()) -> Poll<Self::Output> {
        if self.socket.has_data_to_read() {
            // socket 有数据，写入 buffer 并返回
            Poll::Ready(self.socket.read_buf())
        } else {
            // socket 中还没数据
            //
            // 注册一个 `wake` 函数，当数据可用时，该函数会被调用，
            // 然后当前 Future 的执行器会再次调用 `poll` 方法，此时就可以读取到数据。
            self.socket.set_readable_callback(wake);
            Poll::Pending
        }
    }
}
```

## 构建自己的执行器