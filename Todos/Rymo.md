## HTTP 的处理

HTTP 是承载于 TCP 之上，想要从零构建一个 HTTP 服务就需要从监听一个 TCP 开始。

整个 HTTP 协议都是在 TCP 的 data 中。使用 `TcpListener` 监听后，我们的 `socket` 读到的就是 data 中的内容，所以直接对读取的数据进行 HTTP 序列化即可。

```rust
let listener = TcpListener::bind(format!("0.0.0.0:{}", self.port)).await?;
let (socket, addr) = listener.accept().await?;
```
## HTTP 头的处理

