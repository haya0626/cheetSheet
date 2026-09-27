# GROUP BY

`GROUP BY` は、指定したカラムの値ごとにレコードをグループ化するために使用する。

主に `COUNT`、`SUM`、`AVG`、`MAX`、`MIN` などの集計関数と組み合わせて使用する。

## 基本文法

```sql
SELECT
    department_id,
    COUNT(*)
FROM
    users
GROUP BY
    department_id;
```

`department_id` ごとにレコードをグループ化し、それぞれの件数を取得する。

## 主な集計関数

| 関数    | 内容   |
| ------- | ------ |
| `COUNT` | 件数   |
| `SUM`   | 合計   |
| `AVG`   | 平均   |
| `MAX`   | 最大値 |
| `MIN`   | 最小値 |

```sql
SELECT
    department_id,
    AVG(age)
FROM
    users
GROUP BY
    department_id;
```

## HAVING

グループ化した結果に対して条件を指定する場合は `HAVING` を使用する。

```sql
SELECT
    department_id,
    COUNT(*)
FROM
    users
GROUP BY
    department_id
HAVING
    COUNT(*) >= 10;
```

`WHERE` はグループ化する前のレコードを絞り込み、`HAVING` はグループ化した後の結果を絞り込む。

```sql
SELECT
    department_id,
    COUNT(*)
FROM
    users
WHERE
    delete_flag = 0
GROUP BY
    department_id
HAVING
    COUNT(*) >= 10;
```
