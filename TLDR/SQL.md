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