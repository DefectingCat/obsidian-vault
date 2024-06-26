## HTTP 的处理

HTTP 是承载于 TCP 之上，想要从零构建一个 HTTP 服务就需要从监听一个 TCP 开始。

整个 HTTP 协议都是在 TCP 的 data 中。使用 `TcpListener` 监听后，我们的 `socket` 读到的就是 data 中的内容，所以直接对读取的数据进行 HTTP 序列化即可。

```rust
let listener = TcpListener::bind(format!("0.0.0.0:{}", self.port)).await?;
let (socket, addr) = listener.accept().await?;
```
## HTTP 头的处理

HTTP 发送过来的第一部分，也就是 headers。根据 [HTTP](https://developer.mozilla.org/en-US/docs/Web/HTTP/Overview) 协议的规范，headers 与 body 之间使用一行空行隔开，且换行符是 CRLF，也就是 `\r\n\r\n`。

```http
HTTP/1.1 200 OK
Date: Sat, 09 Oct 2010 14:28:02 GMT
Server: Apache
Last-Modified: Tue, 01 Dec 2009 20:18:22 GMT
ETag: "51142bc1-7449-479b075b2891b"
Accept-Ranges: bytes
Content-Length: 29769
Content-Type: text/html

<!DOCTYPE html>… (here come the 29769 bytes of the requested web page)
```

所以针对头的处理，只需要一直读取 TCP 数据流，直到在末尾遇到一个空行。

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

在 headers 中除了第一行 `HTTP/1.1 200 OK` 后续就全都是以键值对为组合的常规头部。所以读取了 headers 所有的字节后，还需要一些简单的处理

```rust
impl Request {
    /// Parse request from HTTP header's bytes that read from tcp.
    #[inline]
    pub fn parse_from_bytes(bytes: Bytes) -> Result<Self> {
        let mut req = Self::default();

        let collect_headers = |(i, l): (usize, &[u8])| {
            // the first line is route path
            // GET /v1/ HTTP/1.1
            if i == 0 {
                let route = l.split(|&b| b == b' ');
                let (method, path, version) = route.enumerate().try_fold(
                    (String::new(), PathBuf::new(), String::new()),
                    fold_first_line,
                )?;
                req.method = method;
                req.path = path;
                req.version = version;
                anyhow::Ok(())
                // the second line is headers until \r\n\r\n
            } else {
                let heads = std::str::from_utf8(l)?.split(": ");
                let (k, v) = heads
                    .enumerate()
                    .try_fold((String::new(), String::new()), fold_headers)?;
                req.headers.entry(k).or_insert(v);
                Ok(())
            }
        };

        // GET /v1/ HTTP/1.1\r\nUser-Agent: ua\r\n ..
        bytes
            .split(|&b| b == b'\n')
            .map(|line| line.strip_suffix(b"\r").unwrap_or(line))
            .filter(|l| !l.is_empty())
            .enumerate()
            .try_for_each(collect_headers)?;

        Ok(req)
    }
}
```

第一行主要有 3 个字段，分别是请求方法，路由和 HTTP 版本。