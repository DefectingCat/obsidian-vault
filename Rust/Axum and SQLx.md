一个 Web APP 该有的基本素养就是少不了数据库。对比了各种 ORM 之后，选择了 SQLx。它在目前阶段中比较热门的一个 sql driver。它的一个亮点就是可以使用宏来在编译期构建 SQL 语句。

```rust
#[serde_with::serde_as]
#[derive(Serialize)]
#[serde(rename_all = "camelCase")]
pub struct LastPost {
    pub id: i32,
    pub per_page: i32,
    pub current_page: i32,
    pub last_page: i32,
    // `OffsetDateTime`'s default serialization format is not standard.
    #[serde_as(as = "Rfc3339")]
    pub create_at: OffsetDateTime,
}

pub async fn fetch_last_post() -> Result<Option<LastPost>> {
    let pool = access_db().await;
    let last_post = sqlx::query_as!(
        LastPost,
        // language=PostgreSQL
        r#"
SELECT id, per_page, current_page, last_page, create_at
FROM last_post
ORDER BY create_at DESC
LIMIT 1;
            "#
    )
    .fetch_optional(pool)
    .await?;

    Ok(last_post)
}
```

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