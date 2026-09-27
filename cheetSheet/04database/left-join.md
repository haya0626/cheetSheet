# LEFT JOIN

`LEFT JOIN` は、**左側のテーブルを基準として、右側のテーブルを結合する**ために使用する。

右側に対応するデータが存在しない場合でも、左側のレコードは取得される。

対応するデータが存在しない右側のカラムには `NULL` が設定される。

## 基本文法

```sql
SELECT
    u.id,
    u.name,
    d.name AS department_name
FROM
    users u
LEFT JOIN
    departments d
    ON u.department_id = d.id;
```

この場合、

```text
左側
users

右側
departments
```

となる。

`users` のレコードは、対応する `departments` が存在しなくても取得される。

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
```

以下の SQL を実行する。

```sql
SELECT
    u.id,
    u.name,
    d.name AS department_name
FROM
    users u
LEFT JOIN
    departments d
    ON u.department_id = d.id;
```

結果は以下となる。

```text
id | name | department_name
---|------|----------------
1  | 田中 | 営業部
2  | 山田 | 開発部
3  | 佐藤 | NULL
```

佐藤には対応する部署が存在しないが、`users` が左側のためレコードは残る。

つまり、

```text
LEFT JOIN = 左側のテーブルは必ず残す
```

と考える。

## 実務で LEFT JOIN を使用するケース

### 基準となるデータをすべて取得したい場合

例えば社員一覧を表示する場合。

```text
社員
↓
部署情報があれば表示する
↓
部署未所属でも社員自体は表示したい
```

この場合は `LEFT JOIN` を使用する。

```sql
SELECT
    e.id,
    e.name,
    d.name AS department_name
FROM
    employees e
LEFT JOIN
    departments d
    ON e.department_id = d.id;
```

社員が基準となるため、社員データを左側に配置する。

### 任意項目の情報を付加したい場合

例えばユーザーに住所情報が存在する場合だけ住所を表示する。

```text
ユーザー
↓
住所登録あり
    → 住所を表示

住所登録なし
    → ユーザー自体は表示
```

```sql
SELECT
    u.id,
    u.name,
    a.address
FROM
    users u
LEFT JOIN
    addresses a
    ON u.id = a.user_id;
```

このように、

```text
メインとなるデータ
+
存在すれば追加したいデータ
```

という場合に `LEFT JOIN` を使用する。

## INNER JOIN との使い分け

判断基準は、

```text
右側のデータが存在しない場合に、
左側のデータを残したいか
```

で考える。

## 残したい

```text
LEFT JOIN
```

例：

- 部署未所属の社員も一覧表示したい
- 注文履歴がないユーザーも表示したい
- 任意登録のプロフィール情報がなくてもユーザーを表示したい

## 残さなくてよい

```text
INNER JOIN
```

例：

- 部署に所属している社員だけ取得したい
- 注文実績が存在するユーザーだけ取得したい
- 対応するマスタが存在するデータだけ取得したい

## LEFT JOIN の考え方

以下のように考えると判断しやすい。

```text
この一覧で絶対に表示したいデータは何か？
                ↓
        そのテーブルを左側にする
```

例えば、

```text
社員一覧
↓
社員は必ず表示
↓
employees LEFT JOIN departments
```

となる。

## LEFT JOIN と WHERE の注意点

LEFT JOIN した右側テーブルのカラムを `WHERE` で絞り込む場合は注意する。

例えば、

```sql
SELECT
    u.id,
    u.name,
    d.name
FROM
    users u
LEFT JOIN
    departments d
    ON u.department_id = d.id
WHERE
    d.active_flag = 1;
```

右側に部署が存在しない場合、

```text
d.active_flag = NULL
```

となる。

`NULL = 1` は成立しないため、そのレコードは除外される。

そのため、

```text
LEFT JOINしたのに
右側のデータがないレコードが消える
```

ことがある。

JOIN 対象となる右側のデータだけに条件を付けたい場合は、

```sql
SELECT
    u.id,
    u.name,
    d.name
FROM
    users u
LEFT JOIN
    departments d
    ON u.department_id = d.id
    AND d.active_flag = 1;
```

のように `ON` に条件を書く場合がある。

`ON` と `WHERE` のどちらに条件を書くかで取得結果が変わるため、要件に応じて使い分ける。
