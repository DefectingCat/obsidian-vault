[wasm-bindgen](https://rustwasm.github.io/docs/wasm-bindgen/introduction.html)

Rust 在 WASM上主要使用 `wasm-pack` 与 `wasm-bindgen`，他们分别是：

- `wasm-pack`：类似于 `webpack` 的构建工具，帮助我们快速的实现 WASM 的构建与测试。
- `wasm-bindgen`：生成使用 WebAssembly 的 JavaScript API。

## 主要依赖

```toml
[dependencies]
wasm-bindgen = "0.2.87"
wasm-bindgen-futures = "0.4.37"
js-sys = "0.3.64"
# The `console_error_panic_hook` crate provides better debugging of panics by
# logging them with `console.error`. This is great for development, but requires
# all the `std::fmt` and `std::panicking` infrastructure, so isn't great for
# code size when deploying.
console_error_panic_hook = { version = "0.1.7", optional = true }
```

`wasm-pack` 属于构建工具一类，直接使用 `cargo install wasm-pack` 作为二进制可执行文件安装到机器上即可。此外就是一些不可或缺的依赖项：

- `wasm-bindgen-futures`：异步 WASM function。
- `js-sys`：JavaScript 的一些数据类型。

## 项目结构

`wasm-pack` 会将我们的 WASM 构建为一个符合 NPM 的目录结构，以便于直接在 Web 项目中应用。所以 Rust 的项目结构自然就是一个 `lib` 的结构。

