# DELETE

`DELETE` は、テーブルからレコードを削除するための SQL 文。

CRUD の **Delete（削除）** に該当する。

## 基本文法

```sql
DELETE FROM table_name
WHERE
    condition;
```

```sql
DELETE FROM users
WHERE
    id = 1;
```

## WHERE の指定

`WHERE` を指定しない場合、テーブル内のすべてのレコードが削除される。

```sql
DELETE FROM users;
```

実行前に同じ条件で `SELECT` を実行し、削除対象を確認する。

## 論理削除

実務ではレコード自体を削除せず、削除フラグなどを更新する場合がある。

```sql
UPDATE users
SET
    delete_flag = 1
WHERE
    id = 1;
```

レコードを実際に削除する方法を **物理削除**、削除状態をカラムで管理する方法を **論理削除** という。
