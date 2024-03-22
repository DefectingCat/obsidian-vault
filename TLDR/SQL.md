## Delete statement

```sql
delete from users;
delete from users where email='i@rua.plus';
```

## Where contains

```sql
SELECT * FROM mytable
WHERE column1 LIKE '%word1%'
  AND column1 LIKE '%word2%'
  AND column1 LIKE '%word3%'
```

```sql
SELECT * FROM mytable
WHERE column1 LIKE '%word1%'
   OR column1 LIKE '%word2%'
   OR column1 LIKE '%word3%'
```

## Update row

```sql
update users set username='xfy_sonetto' where email='i@rua.plus';
```

Insert row

```sql
insert into users (username, email, password)
values ('test', 'test@test.com', 'test')
returning id, username, email, password;
```

## Alter column

convert column's data type

```sql
SELECT * FROM public.users
ORDER BY id ASC ;

alter table users
alter column gender type smallint USING (gender::smallint);

select * from users where gender = '';
update users set gender = null where gender = '';
```

add column

```sql
alter table users add column "uid" bigint not null default 100000;
```

## Contains

```go
	if userInfo.Username != "" {
		dbRes = user.Db.Model(&models.User{}).
			Find(&users, "(username ILIKE '%' || ? || '%') OR ( ? ILIKE '%' || username || '%')", userInfo.Username, userInfo.Username)
		return
	}
```