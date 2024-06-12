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