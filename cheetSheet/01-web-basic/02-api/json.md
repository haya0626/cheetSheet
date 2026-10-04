# JSON

データをやり取りするための軽量なデータ形式

## JSON とは

```json
{
  "name": "山田",
  "age": 20
}
```

- JavaScript Object Notation の略
- データを「キーと値」で表現する形式
- API 通信・設定ファイルなどで使われる
- 言語に依存しない（どの言語でも使える）

## 基本構造

### ● オブジェクト（{ }）

```json
{
  "key": "value"
}
```

### ● 配列（[ ]）

```json
["りんご", "みかん", "バナナ"]
```

## データ型

JSON で使える型

### ● 文字列（string）

```json
"name": "山田"
```

※ダブルクォート必須（重要）

### ● 数値（number）

```json
"age": 20
```

### ● 真偽値（boolean）

```json
"isAdmin": true
```

### ● null

```json
"data": null
```

### ● 配列（array）

```json
"fruits": ["りんご", "みかん"]
```

### ● オブジェクト（object）

```json
"user": {
  "name": "山田",
  "age": 20
}
```

## ネスト（入れ子）

```json
{
  "user": {
    "name": "山田",
    "hobbies": ["ゲーム", "読書"]
  }
}
```

## JavaScript との違い（重要）

### JS オブジェクト

```js
const user = {
  name: "山田",
};
```

### JSON

```json
{
  "name": "山田"
}
```

## よくある使い方

### ● API レスポンス

```json
{
  "id": 1,
  "title": "記事タイトル",
  "content": "本文"
}
```

### ● 設定ファイル

```json
{
  "theme": "dark",
  "language": "ja"
}
```

### ● データ一覧

```json
[
  { "id": 1, "name": "A" },
  { "id": 2, "name": "B" }
]
```

## JavaScript での扱い

### ● JSON → JS

```js
const obj = JSON.parse('{"name":"山田"}');
```

### ● JS → JSON

```js
const json = JSON.stringify({ name: "山田" });
```

## 重要なルール（超重要）

- キーは必ずダブルクォート

```json
{ "name": "OK" }
```

```json
{ "name": "NG" }
```

- 末尾カンマ NG

```json
{
  "name": "山田"
}
```

- コメント NG
