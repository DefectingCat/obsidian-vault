一个 Web APP 该有的基本素养就是少不了数据库。对比了各种 ORM 之后，选择了 SQLx。它在目前阶段中比较热门的一个 sql driver。它的一个亮点就是可以使用宏来在编译期构建 SQL 语句，从而实现

[sqlx_pool.rs](https://gist.github.com/DefectingCat/b482858c8ea148fe4c01f73c1c772628)

```rust
static DB: OnceCell<Pool<Postgres>> = OnceCell::const_new();
async fn access_db() -> &'static Pool<Postgres> {
    let initer = || async {
        let db_url = env::var("SPIO_DB").expect("db host url must be set");
        let pool = PgPoolOptions::new()
            .max_connections(20)
            .acquire_timeout(Duration::from_secs(3))
            .connect(&db_url)
            .await;
        match pool {
            Ok(pool) => pool,
            Err(err) => {
                panic!("connect to postgres failed {}", err)
            }
        }
    };
    DB.get_or_init(initer).await
}
```