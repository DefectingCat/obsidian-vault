## HTTP 的处理

HTTP 是承载于 TCP 之上，想要从零构建一个 HTTP 服务就需要从监听一个 TCP 开始。

整个 HTTP 协议都是在 TCP 的 data 中。使用 `TcpListener` 监听后，我们的 `socket` 读到的就是 data 中的内容，所以直接对读取的数据进行 HTTP 序列化即可。

```rust
let listener = TcpListener::bind(format!("0.0.0.0:{}", self.port)).await?;
let (socket, addr) = listener.accept().await?;
```
## HTTP 头的处理

HTTP 发送过来的第一部分，也就是 headers。根据 HTTP 协议的规范，

```rust
/// Read bytes from reader to string
/// but not common headers, include first line like GET / HTTP/1.1
/// 13 10 13 10
/// \r \n \r \n
pub async fn read_headers<R>(mut reader: R) -> Result<(Bytes, R)>
where
    R: AsyncRead + Unpin,
{
    let mut buffer = BytesMut::with_capacity(512);
    loop {
        let mut buf = vec![0_u8; 1];
        let res = reader.read(&mut buf).await;
        match res {
            Ok(0) => {
                break;
            }
            Ok(_) => {
                buffer.put_u8(buf[0]);
                let len = buffer.len();
                if len < 4 {
                    continue;
                } else {
                    let last_four = &buffer[len - 4..len];
                    if last_four == b"\r\n\r\n" {
                        trace!("breaking read headers");
                        break;
                    }
                }
            }
            Err(e) if e.kind() == ErrorKind::UnexpectedEof => {
                return Ok((buffer.freeze(), reader));
            }
            Err(e) => {
                return Err(e.into());
            }
        }
    }
    let headers = buffer.freeze();
    Ok((headers.clone(), reader))
}
```