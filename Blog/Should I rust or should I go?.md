
## Basic CRUD

对于多数语言来说，一个需求多数情况下都有多个解决方案，而选择稳定稳定可维护的才是长久之计。对于 Golang 来说，早已有较为成熟的解决方案。所以选择的是 Gin 与 Gorm。

基础功能：

1. 创建新用户
2. 根据邮箱查询用户
3. 用户登录并返回 JWT token
4. JWT 认证
5. 更新用户信息
6. 删除用户，注销账户

## JSON

```rust
let data = r#"
{
    "name": "xfy",
    "age": 14,
    "test": null
}
"#;
let unamed: Value = serde_json::from_str(data)?;
println!("access unamed {:?}", unamed["name"].as_str());
let named: Test = serde_json::from_str(data)?;
println!("access named {:?}", named.name);

let named = Test::default();
let unamed = json!({
    "name": "xfy",
    "age": 14,
    "test": null
});
println!("unamed json string {}", serde_json::to_string(&unamed)?);
println!("named json string {}", serde_json::to_string(&named)?);
```

```go
	data := `
        {
            "name": "xfy",
            "age": 14,
            "test": null
        }
        `
	var unamed map[string]any
	if err := json.Unmarshal([]byte(data), &unamed); err != nil {
		log.Fatal(err)
	}
	log.Printf("access unamed %v", unamed["name"])
	var named Test
	if err := json.Unmarshal([]byte(data), &named); err != nil {
		log.Fatal(err)
	}
	log.Printf("access named %v", named.Name)

	{
		named := Test{
			Name: "xfy",
			Age:  14,
			Test: nil,
		}
		unamed := map[string]any{
			"name": "xfy",
			"age":  14,
			"test": nil,
		}
		named_str, err := json.Marshal(named)
		if err != nil {
			log.Fatal(err)
		}
		unamed_str, err := json.Marshal(unamed)
		if err != nil {
			log.Fatal(err)
		}
		log.Printf("unamed json string %v", string(unamed_str))
		log.Printf("named json string %v", string(named_str))
	}
```

## Basic http server

### Golang

Golang std 自带了 http 包，不仅可以用来发送 http 请求还可以用来快速的启动一个 http server。

不过 dotenv file 的支持还需要一个额外的第三方包 [godotenv](https://github.com/joho/godotenv) 的支持。

```bash
go get github.com/joho/godotenv
```

至于 Log 的话，std 自带的 `log` 包就非常的优秀，对于简单的需求可以直接满足。

```go
// main.go
package main

import (
	"log"

	"github.com/joho/godotenv"

	"rua.plus/gymo/server"
)

func main() {
	err := godotenv.Load()
	if err != nil {
		log.Println("env file not found")
	}

	server.InitServer()
}
```

对于 http server 的使用也是非常的令人舒心，使用 `HandleFunc` 就像是注册路由一般，直接注册到对应的 controller 即可。而对应的 http method 则需要根据 headers 在 handler 中手动判断。

```go
// server/server.go
package server

import (
	"log"
	"net/http"
	"os"
)

func InitServer() {
	log.Println("init server")
	port := os.Getenv("PORT")
	if port == "" {
		port = "4000"
	}

	root := new(controllers.Root)
	http.HandleFunc("/", root.GetRoot)

	log.Println("server listening on", port)
	err := http.ListenAndServe(":"+port, nil)
	if err != nil {
		log.Fatal(err)
	}
}
```

对于 handler 来说，它会接受到俩参数，一个是 writer 另一个是客户端当前请求的指针。剩下的处理就和常用的框架类似了。

```go
// controllers/root.go
package controllers

import (
	"io"
	"log"
	"net/http"
)

type Root struct{}

func (root Root) GetRoot(w http.ResponseWriter, r *http.Request) {
	log.Printf("%v / request", r.Method)
	io.WriteString(w, "Hello Gymo!")
}
```

### Rust

与 Golang 的定位不同，Rust std 中并没有自带 http 包，如果需要一个简单的 http 服务器则有两种选择。

- 监听 TCP 连接，手动实现 http 服务器
- 使用其他第三方包，例如 axum

同样与 Golang 自带协程不同的是，Rust std 中的多线程是系统线程，如果选择手动实现，则还有几种选择。

- 使用系统线程，手动维护一个线程池
- 使用异步，手动实现一个异步 runtime
- 使用第三方异步 runtime，或者单纯的线程池

这里选择的是使用 `tokio` 作为异步 runtime，同时使用其 `TcpListener` 来监听 TCP 连接从而手动实现一个 http 服务器。