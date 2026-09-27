# UPDATE

`UPDATE` は、既存のレコードを更新するための SQL 文。

CRUD の **Update（更新）** に該当する。

## 基本文法

```sql
UPDATE table_name
SET
    column_name = value
WHERE
    condition;
```

```sql
UPDATE users
SET
    name = '田中太郎',
    age = 26
WHERE
    id = 1;
```

複数カラムを更新する場合はカンマで区切る。

## WHERE の指定

`WHERE` を指定しない場合、対象テーブルのすべてのレコードが更新される。

```sql
UPDATE users
SET
    status = 'ACTIVE';
```

意図しない全件更新を防ぐため、実行前に同じ条件で `SELECT` を実行し、対象レコードを確認する。

```sql
SELECT *
FROM users
WHERE id = 1;
```
