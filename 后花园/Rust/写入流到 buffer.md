测试的大部分写入器（`TcpStream`，`File`）等，在写入 buffer 时，都会从头开始写入。以 `TcpStream` 为例，在每个数据包到来之后，都会从 buffer 的开头写入数据。即使 buffer 中有上个数据包的数据，也会被覆盖。

```rust
let mut buffer = vec![0; 10];
loop {
    dbg!(&buffer);
    match socket.read(&mut buffer).await {
        Ok(0) => (),
        Ok(bytes) => if socket.write_all(&buffer[..bytes]).await.is_err() {},
        Err(_err) => (),
    }
}
```