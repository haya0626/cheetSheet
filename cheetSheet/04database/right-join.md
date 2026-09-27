# RIGHT JOIN

`RIGHT JOIN` は、**右側のテーブルを基準として、左側のテーブルを結合する**ために使用する。

左側に対応するデータが存在しない場合でも、右側のレコードは取得される。

対応するデータが存在しない左側のカラムには `NULL` が設定される。

## 基本文法

```sql
SELECT
    u.id,
    u.name,
    d.name AS department_name
FROM
    users u
RIGHT JOIN
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

`departments` のレコードは、対応する `users` が存在しなくても取得される。

## 結合の考え方

例えば以下のデータがある。

### users

```text
id | name | department_id
---|------|--------------
1  | 田中 | 10
2  | 山田 | 20
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
    u.name,
    d.name AS department_name
FROM
    users u
RIGHT JOIN
    departments d
    ON u.department_id = d.id;
```

結果は以下となる。

```text
name | department_name
-----|----------------
田中 | 営業部
山田 | 開発部
NULL | 人事部
```

人事部には所属ユーザーが存在しないが、

```text
departments
```

が右側のためレコードは残る。

つまり、

```text
RIGHT JOIN
=
右側のテーブルは必ず残す
```

と考える。

## 実務で RIGHT JOIN を使用するケース

考え方としては、

```text
右側のテーブルをすべて取得したい
```

場合に使用する。

例えば、

```text
全部署を表示したい
↓
所属社員がいない部署も表示したい
```

場合。

```sql
SELECT
    d.id,
    d.name,
    u.name AS user_name
FROM
    users u
RIGHT JOIN
    departments d
    ON u.department_id = d.id;
```

この場合、社員が存在しない部署も取得される。

## LEFT JOIN との関係

`RIGHT JOIN` は、基本的にテーブルの左右を入れ替えることで `LEFT JOIN` と同じ結果を表現できる。

例えば、

```sql
SELECT
    u.name,
    d.name
FROM
    users u
RIGHT JOIN
    departments d
    ON u.department_id = d.id;
```

は、

```sql
SELECT
    u.name,
    d.name
FROM
    departments d
LEFT JOIN
    users u
    ON u.department_id = d.id;
```

とほぼ同じ意味になる。

そのため実務では、

```text
LEFT JOINに統一する
```

チームも多い。

理由としては、

```text
FROMに基準テーブルを書く
↓
LEFT JOINで追加情報を結合する
```

という読み方の方が理解しやすいため。

## 実務での RIGHT JOIN の位置付け

RIGHT JOIN 自体は正しい SQL 文法だが、

```text
必ずRIGHT JOINを使わなければならない
```

ケースはほとんどない。

例えば、

```sql
users
RIGHT JOIN departments
```

と書くより、

```sql
departments
LEFT JOIN users
```

と書いた方が、

```text
departmentsが基準
```

という意図が分かりやすい。

そのため、実務では LEFT JOIN の方が使用頻度が高い。

## INNER JOIN との使い分け

### INNER JOIN

```text
両方に対応するデータが存在する場合だけ取得したい
```

場合。

例：

```text
社員が所属している部署だけ取得したい
```

---

### RIGHT JOIN

```text
右側のデータはすべて取得したい
```

場合。

例：

```text
所属社員が存在しない部署も含めて
全部署を取得したい
```

## 判断方法

RIGHT JOIN を考える場合も、本質は同じ。

```text
どのテーブルのレコードを必ず残したいか？
```

で判断する。

例えば、

```text
部署一覧を作成する
↓
社員が存在しなくても部署は表示したい
↓
departmentsを必ず残す
```

この場合、

```sql
users
RIGHT JOIN departments
```

でも実現できる。

ただし実務では、

```sql
departments
LEFT JOIN users
```

と書く方が分かりやすい。
