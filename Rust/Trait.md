Trait 和接口类似，特征定义了一个可以被共享的行为，只要实现了特征，你就能使用该行为。

## 定义与实现

```rust
pub trait Summary {
    fn summarize(&self) -> String;
}

pub struct Post {
    pub title: String,
    pub author: String,
    pub content: String,
}

impl Summary for Post {
    fn summarize(&self) -> String {
        format!("文章 {}，作者是 {}", self.title, self.author)
    }
}
```

与实现方法不同的是，实现 Trait 需要在 `impl` 后添加一个 `for` 关键字：

```rust
impl Post {
    fn new(title: String, author: String, content: String) -> Self {
        Self {
            title,
            author,
            content,
        }
    }
}
```

### 特征定义与实现的位置(孤儿规则)

关于特征实现与定义的位置，有一条非常重要的原则：如果你想要为类型 A 实现特征 T，那么 A 或者 T 至少有一个是在当前作用域中定义的！

该规则被称为孤儿规则，可以确保其它人编写的代码不会破坏你的代码，也确保了你不会莫名其妙就破坏了风马牛不相及的代码。

### 默认实现

特征中定义具有默认实现的方法，这样其它类型无需再实现该方法，或者也可以选择重载该方法：

```rust
pub trait Summary {
    fn summarize(&self) -> String {
        String::from("(Read more...)")
    }
}
```

如果在实现该 Trait 的时候再次定义了该方法，则会重载该方法。

## 作为函数参数

```rust
pub fn notify(item: &impl Summary) -> String {
    format!("Breaking news {}", item.summarize())
}
```

它的意思是 实现了Summary特征 的 item 参数。只要任何实现了 `Summary` Trait