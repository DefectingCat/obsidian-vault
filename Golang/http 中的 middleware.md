自从 Golang 的 1.22 之后，标准库中的 `net/http` 对路由的支持更加完善了。对于一些简单的项目可以直接使用 `net/http` 来实现，从而省的再使用第三方库。

## 在标准库中的中间件

Golang 标准库 `net/http` 中的中间件其实非常简单，我们使用一个接受 `http.Handler` 为参数同时返回一个 `http.Handler` 做为接下来的路由响应。

函数的签名类似于这样：

```go
func(http.Handler) http.Handler
```

而实际的一个中间件看上去也非常的简单：

```go
// Logging
func LogginMiddleware(next http.Handler) http.Handler {
	return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
		method := r.Method
		path := r.URL
		host := r.Host

		start := time.Now()
		/* slog.Info(fmt.Sprintf("%v %v %v", host, method, path)) */
		next.ServeHTTP(w, r)
		slog.Info(fmt.Sprintf("%v %v %v %v", host, method, path, time.Since(start)))
	})
}
```

如果执行下一个对当前路由处理的 handler 直接使用参数中的 `http.Handler` 即可。

```go
next.ServeHTTP(w, r)
```

而我们中间件所返回的 handler，既可以成为下一个中间件的参数，也可以是最后一个用来处理请求的 handler。