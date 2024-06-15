## With query

```sql
with user_menus as (
select
        utm.id,
        utm.mid
from
    user_to_menus utm
where
    uid = 2
)
select
    *
from
    user_menus as um
join menus as m on
    um.mid = m.id;
```

## PSQL recursive query

```sql
with recursive test as (
    select
        id,
        name,
        pid
    from menus where id = 2
    union
    select
        p.id,
        p.name,
        p.pid
    from menus as p
    inner join test as c
        on p.id = c.pid
)

select * from test;
```

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