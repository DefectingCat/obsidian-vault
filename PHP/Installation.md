## Compile

Source code on GitHub: https://github.com/php/php-src/. Clone and chose a tag.

```shell
git fetch --depth 10 --tags
```

```shell
./buildconf --force
```

```shell
./configure --prefix=/home/xfy/pl/local/bin/php/7.2.34 \
	--enable-zip \
```

## `php.ini`

修改默认的位置

```shell
export PHP_INI_SCAN_DIR=/etc/php.d/
```