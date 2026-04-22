# unknown

「型が分からないけど、安全に扱うための型」

## 基本

```ts
let value: unknown;

value = 123;
value = "hello";
value = true;
```

何でも代入できる

## でもそのまま使えない

```ts
value.toUpperCase(); // エラー
```

型が分からないから使えない

## ここが重要

**使う前に型チェックが必要**

## 型ガード（基本）

```ts
if (typeof value === "string") {
  console.log(value.toUpperCase());
}
```

string と分かって初めて使える

## any との違い

### ● any

```ts
let value: any;

value = "hello";
value.toUpperCase(); // OK（危険）
```

何でもできる（型チェックなし）

### ● unknown

```ts
let value: unknown;

value = "hello";
value.toUpperCase(); // NG
```

安全（チェック必須）

## 一言で違い

any = 何でも OK（危険）

unknown = 何でも入るけど使う前に確認

## 型アサーション

```ts
(value as string).toUpperCase();
```

強制的に型を決める

ただし安全ではない（自己責任）

## 関数での例

```ts
function print(value: unknown) {
  if (typeof value === "number") {
    console.log(value.toFixed(2));
  }
}
```

安全に扱える

## JSON の例（実務でよく使う）

```ts
const data: unknown = JSON.parse(json);
```

型が分からないので unknown

```ts
if (typeof data === "object" && data !== null) {
  console.log("オブジェクト");
}
```

## 使いどころ

- 外部データ（API / JSON）
- 型が分からない値
- 一旦受け取るとき
