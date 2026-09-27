# INNER JOIN

`INNER JOIN` は、**両方のテーブルに結合条件を満たすレコードが存在する場合のみ取得する**ために使用する。

どちらか一方にしか存在しないレコードは、取得結果に含まれない。

## 基本文法

```sql
SELECT
    u.id,
    u.name,
    d.name AS department_name
FROM
    users u
INNER JOIN
    departments d
    ON u.department_id = d.id;
```

`ON` には、テーブル同士を関連付ける条件を指定する。

```text
users.department_id
        ↓
departments.id
```

## 結合の考え方

例えば以下のデータがある。

### users

```text
id | name | department_id
---|------|--------------
1  | 田中 | 10
2  | 山田 | 20
3  | 佐藤 | NULL
```

### departments

```text
id | name
---|------
10 | 営業部
20 | 開発部
30 | 人事部
```

以下の SQL を実行する。

```sql
SELECT
    u.id,
    u.name,
    d.name AS department_name
FROM
    users u
INNER JOIN
    departments d
    ON u.department_id = d.id;
```

結果は以下となる。

```text
id | name | department_name
---|------|----------------
1  | 田中 | 営業部
2  | 山田 | 開発部
```

佐藤は対応する部署が存在しないため取得されない。

人事部も対応するユーザーが存在しないため取得されない。

つまり、

```text
INNER JOIN
=
両方に対応するデータが存在するものだけ残す
```

と考える。

## 実務で INNER JOIN を使用するケース

### 関連データが存在することが前提の場合

例えば、

```text
社員
↓
必ず部署に所属している
```

という業務ルールの場合。

```sql
SELECT
    e.id,
    e.name,
    d.name AS department_name
FROM
    employees e
INNER JOIN
    departments d
    ON e.department_id = d.id;
```

部署情報が存在しない社員を取得する必要がないため、`INNER JOIN` を使用する。

---

### 関連データが存在するものだけ取得したい場合

例えば、

```text
注文実績があるユーザーだけ取得したい
```

場合。

```sql
SELECT
    u.id,
    u.name
FROM
    users u
INNER JOIN
    orders o
    ON u.id = o.user_id;
```

注文が存在しないユーザーは取得されない。

---

### マスタとの紐付きが必須の場合

例えば商品一覧を取得するとき、

```text
商品
↓
有効なカテゴリに紐づいているものだけ取得したい
```

場合。

```sql
SELECT
    p.id,
    p.name,
    c.name AS category_name
FROM
    products p
INNER JOIN
    categories c
    ON p.category_id = c.id;
```

対応するカテゴリが存在しない商品は取得されない。

## INNER JOIN を選ぶ判断基準

実務では、

```text
関連先が存在しないデータを
結果に残す必要があるか？
```

で判断すると分かりやすい。

---

### 残さなくてよい

```text
INNER JOIN
```

例えば、

- 部署に所属している社員だけ取得したい
- 注文実績のあるユーザーだけ取得したい
- 有効なマスタに紐づくデータだけ取得したい
- 親データと子データの両方が存在するものだけ取得したい

---

### 残したい

```text
LEFT JOIN
```

例えば、

- 部署未所属の社員も表示したい
- 注文履歴がないユーザーも表示したい
- 任意登録の付加情報がなくても本体データを表示したい

## LEFT JOIN との違い

例えば以下のユーザーが存在する。

```text
id | name | department_id
---|------|--------------
1  | 田中 | 10
2  | 佐藤 | NULL
```

---

### INNER JOIN

```sql
FROM users u
INNER JOIN departments d
    ON u.department_id = d.id
```

結果：

```text
田中 | 営業部
```

佐藤は取得されない。

---

### LEFT JOIN

```sql
FROM users u
LEFT JOIN departments d
    ON u.department_id = d.id
```

結果：

```text
田中 | 営業部
佐藤 | NULL
```

佐藤も取得される。

違いは、

```text
関連データが存在しない場合に
元データを残すかどうか
```

となる。

## INNER JOIN と WHERE の違い

例えば以下の SQL。

```sql
SELECT
    u.id,
    u.name,
    d.name
FROM
    users u
INNER JOIN
    departments d
    ON u.department_id = d.id
WHERE
    u.active_flag = 1;
```

役割はそれぞれ異なる。

```text
ON
↓
usersとdepartmentsを
どの条件で結合するか

WHERE
↓
結合された結果から
どのレコードを取得するか
```

基本的には、

```text
ON
= テーブル同士の関係

WHERE
= 検索条件
```

と考える。

## 1 対多の INNER JOIN に注意

例えば、

### users

```text
id | name
---|------
1  | 田中
```

### orders

```text
id  | user_id
----|--------
100 | 1
101 | 1
102 | 1
```

以下の SQL を実行する。

```sql
SELECT
    u.id,
    u.name,
    o.id AS order_id
FROM
    users u
INNER JOIN
    orders o
    ON u.id = o.user_id;
```

結果は 3 件となる。

```text
user_id | name | order_id
--------|------|---------
1       | 田中 | 100
1       | 田中 | 101
1       | 田中 | 102
```

INNER JOIN だから 1 件になるわけではない。

JOIN 後の件数は、

```text
テーブル同士が
1対1なのか
1対多なのか
多対多なのか
```

によって変わる。

JOIN 後に件数が想定より増えた場合は、結合条件とカーディナリティを確認する。
