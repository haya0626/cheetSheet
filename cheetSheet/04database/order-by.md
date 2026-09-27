# ORDER BY

`ORDER BY` は、`SELECT` で取得したレコードの並び順を指定するために使用する。

## 基本文法

```sql
SELECT
    id,
    name,
    age
FROM
    users
ORDER BY
    age ASC;
```

| 指定   | 内容 |
| ------ | ---- |
| `ASC`  | 昇順 |
| `DESC` | 降順 |

`ASC` は省略可能。

```sql
ORDER BY age;
```

## 複数条件

複数のカラムを指定した場合、左側から順番に並び替え条件が適用される。

```sql
SELECT
    id,
    name,
    department_id,
    age
FROM
    users
ORDER BY
    department_id ASC,
    age DESC;
```

この場合、`department_id` の昇順で並び替えたあと、同じ `department_id` の中で `age` の降順に並び替える。
