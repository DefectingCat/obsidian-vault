[wasm-bindgen](https://rustwasm.github.io/docs/wasm-bindgen/introduction.html)

Rust 在 WASM上主要使用 `wasm-pack` 与 `wasm-bindgen`，他们分别是：

- `wasm-pack`：类似于 `webpack` 的构建工具，帮助我们快速的实现 WASM 的构建与测试。
- `wasm-bindgen`：生成使用 WebAssembly 的 JavaScript API。

## 主要依赖

```toml
[dependencies]
wasm-bindgen = "0.2.92"
wasm-bindgen-futures = "0.4.42"
js-sys = "0.3.69"
web-sys = "0.3.69"
# The `console_error_panic_hook` crate provides better debugging of panics by
# logging them with `console.error`. This is great for development, but requires
# all the `std::fmt` and `std::panicking` infrastructure, so isn't great for
# code size when deploying.
console_error_panic_hook = { version = "0.1.7", optional = true }
```

`wasm-pack` 属于构建工具一类，直接使用 `cargo install wasm-pack` 作为二进制可执行文件安装到机器上即可。此外就是一些不可或缺的依赖项：

- `wasm-bindgen-futures`：异步 WASM function。
- `web-sys`：浏览器的一些 API。
- `js-sys`：JavaScript 的一些数据类型。

`crate-type` 是必不可少的一个选项，主要用于 WASM 最后构建的产物。

```toml
[lib] 
crate-type = ["cdylib"]
```

## 项目结构

`wasm-pack` 会将我们的 WASM 构建为一个符合 NPM 的目录结构，以便于直接在 Web 项目中应用。所以 Rust 的项目结构自然就是一个 `lib` 的结构。

```
.
├── CONTRIBUTING.md
├── Cargo.toml
├── README.md
├── examples
│   ├── node-example
│   │   ├── package.json
│   │   ├── pnpm-lock.yaml
│   │   ├── src
│   │   │   └── main.ts
│   │   └── tsconfig.json
│   └── wasm-exmaple
│       ├── README.md
│       ├── index.html
│       ├── package.json
│       ├── pnpm-lock.yaml
│       ├── postcss.config.js
│       ├── public
│       │   ├── test-file.txt
│       │   └── vite.svg
│       ├── src
│       │   ├── App.tsx
│       │   ├── assets
│       │   │   └── react.svg
│       │   ├── index.css
│       │   ├── main.tsx
│       │   └── vite-env.d.ts
│       ├── tailwind.config.js
│       ├── tsconfig.json
│       ├── tsconfig.node.json
│       └── vite.config.ts
├── src
│   ├── args.rs
│   ├── encryption.rs
│   ├── error.rs
│   ├── http.rs
│   ├── lib.rs
│   ├── main.rs
│   └── utils.rs
└── tests
    ├── node.rs
    └── test-file.txt
```

就和编写 Rust 库类似，其中 `lib.rs` 就是该库的主要入口，WASM 中的一些主要方法也就会放在这里定义。

```rust
pub mod encryption;
pub mod error;
pub mod http;
pub mod utils;

use encryption::{decrypt_str, encrypt_str, Passwd};
use error::into_js_value;
use http::fetch_file;
use js_sys::Uint8Array;
use wasm_bindgen::prelude::*;
use web_sys::console;

#[wasm_bindgen]
extern "C" {
    fn alert(s: &str);
}
```

## 体积优化

https://gist.github.com/DefectingCat/6b9ab77833f270dcf77197e0e52f729a

