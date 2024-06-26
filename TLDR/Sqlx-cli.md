https://github.com/launchbadge/sqlx/blob/main/sqlx-cli/README.md

### With Rust toolchain

```shell
# supports all databases supported by SQLx
$ cargo install sqlx-cli

# only for postgres
$ cargo install sqlx-cli --no-default-features --features native-tls,postgres

# use vendored OpenSSL (build from source)
$ cargo install sqlx-cli --features openssl-vendored

# use Rustls rather than OpenSSL (be sure to add the features for the databases you intend to use!)
$ cargo install sqlx-cli --no-default-features --features rustls
```

## export DATABASE_URL

For compile-time check

```shell
export DATABASE_URL=postgres://xfy:qwer1234@192.168.31.193/limos
```

build query cache

```shell
cargo sqlx prepare
```

## Model

```rust
use serde::Serialize;
use serde_with::serde_as;
use sqlx::{types::time::OffsetDateTime, Pool, Postgres};
use time::format_description::well_known::Rfc3339;

use crate::{
    error::AppResult,
    models::menus::{Menu, MenuWithChildren},
};

#[serde_as]
#[derive(Serialize, Debug, Clone)]
#[serde(rename_all = "camelCase")]
/// 菜单
pub struct UserToMenu {
    pub id: i32,
    // 用户
    pub uid: i64,
    // 菜单
    pub mid: i32,
    #[serde_as(as = "Rfc3339")]
    pub created_at: OffsetDateTime,
    #[serde_as(as = "Rfc3339")]
    pub updated_at: OffsetDateTime,
    #[serde_as(as = "Option<Rfc3339>")]
    pub deleted_at: Option<OffsetDateTime>,
}

impl UserToMenu {
    /// Query all menus belonging the user that given uid
    pub async fn get(pool: &Pool<Postgres>, uid: i64) -> AppResult<Vec<MenuWithChildren>> {
        // get all current user's menus
        let menus = sqlx::query_as!(
            Menu,
            // language=PostgreSQL
            r#"
with user_menus as (
select
        id,
        mid
from
    user_to_menus
where
    uid = $1
)
select
    m.id,
    name,
    pid,
    created_at,
    updated_at,
    deleted_at
from
    user_menus as um
join menus as m on
    um.mid = m.id;
            "#,
            uid
        )
        .fetch_all(pool)
        .await?;

        // covert all menus with children
        let parents = menus.iter().filter(|m| m.pid.is_none()).collect::<Vec<_>>();
        let children = menus.iter().filter(|m| m.pid.is_some()).collect::<Vec<_>>();
        let mut parents = parents
            .into_iter()
            .map(MenuWithChildren::new_from_menu)
            .collect::<Vec<_>>();
        let children = children
            .into_iter()
            .map(MenuWithChildren::new_from_menu)
            .collect::<Vec<_>>();

        for child in children {
            let pid = child.pid.unwrap();
            let parent = parents.iter_mut().find(|p| p.id == pid);
            if let Some(parent) = parent {
                parent.children.push(child);
            }
        }

        Ok(parents)
    }
}
```