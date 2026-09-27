# SELECT

`SELECT` は、テーブルからデータを取得するための SQL 文。

CRUD の **Read（参照）** に該当する。

## 基本文法

```sql
SELECT
    column_name
FROM
    table_name;
```

複数のカラムを取得する場合はカンマで区切る。

```sql
SELECT
    id,
    name,
    age
FROM
    users;
```

すべてのカラムを取得する場合は `*` を指定する。

```sql
SELECT *
FROM users;
```

実務では、取得するデータを明確にするため、必要なカラムを明示することが推奨される。

## WHERE

取得対象を絞り込む場合は `WHERE` を使用する。

```sql
SELECT
    id,
    name
FROM
    users
WHERE
    age >= 20;
```

複数条件を指定する場合は `AND` や `OR` を使用する。

```sql
SELECT
    id,
    name
FROM
    users
WHERE
    age >= 20
    AND status = 'ACTIVE';
```
