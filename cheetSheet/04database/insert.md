# INSERT

`INSERT` は、テーブルに新しいレコードを登録するための SQL 文。

CRUD の **Create（登録）** に該当する。

## 基本文法

```sql
INSERT INTO table_name (
    column_name1,
    column_name2
)
VALUES (
    value1,
    value2
);
```

```sql
INSERT INTO users (
    id,
    name,
    age
)
VALUES (
    1,
    '田中太郎',
    25
);
```

指定したカラムと `VALUES` の値は、同じ順番で対応する。

## 複数レコードの登録

```sql
INSERT INTO users (
    id,
    name,
    age
)
VALUES
    (1, '田中太郎', 25),
    (2, '山田太郎', 30);
```
