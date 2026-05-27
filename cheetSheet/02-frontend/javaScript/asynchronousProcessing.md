# 非同期

「時間がかかる処理は“あとでやる”。その間に他のことを進める」

## まず前提

非同期処理には大きく 3 種類ある

| 種類             | 例             |
| ---------------- | -------------- |
| 単発             | fetch, DB 取得 |
| 複数（並列）     | 複数 API       |
| 連続（依存あり） | A→B→C          |

## Promise 系メソッド一覧

| メソッド           | 一言                                 |
| ------------------ | ------------------------------------ |
| Promise.all        | 全部成功したら OK                    |
| Promise.allSettled | 全部終われば OK（成功/失敗問わない） |
| Promise.race       | 一番早いやつ                         |
| Promise.any        | 一つでも成功すれば OK                |

## 1. Promise.all

### 特性

- 全部成功 → OK
- 1 つでも失敗 → 即エラー
- 並列で動く（速い）

### 例

```js
let results = await Promise.all([fetchA(), fetchB(), fetchC()]);
```

動き

- A, B, C 同時スタート
- 全部成功 → 配列で返る
- 1 つでも失敗 → 全体が reject

使うべき場面 : `「全部必要」`

例：ユーザー情報 + 投稿 + 設定

NG ケース
一部失敗してもいい場合

## 2. Promise.allSettled

特性

- 全部終わるまで待つ
- 成功・失敗どっちも返す
- エラーで止まらない

例

```js
let results = await Promise.allSettled([fetchA(), fetchB(), fetchC()]);
```

結果

```js
[
{ status: "fulfilled", value: ... },
{ status: "rejected", reason: ... },
]
```

使う場面 : 「全部の結果を知りたい」

例：バッチ処理、ログ収集

## 3. Promise.race

特性

- 一番早く終わったやつだけ
- 成功でも失敗でも終了

例

```js
let result = await Promise.race([fetchA(), fetchB()]);
```

使う場面 : `「早い者勝ち」`

例：タイムアウト処理

```js
await Promise.race([fetchData(), timeout(1000)]);
```

## 4. Promise.any

特性

- 最初に成功したやつ
- 全部失敗 → エラー

例

```js
let result = await Promise.any([fetchA(), fetchB()]);
```

使う場面 : `「どれでもいいから成功してほしい」`

## async/await での考え方

1. 依存関係あり（順番必要）

   ```ts
   let a = await A();
   let b = await B(a);
   ```

→ 直列

2. 依存なし（並列できる）

   ```js
   let [a, b] = await Promise.all([A(), B()]);
   ```
