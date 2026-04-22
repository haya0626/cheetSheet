# unknown と Generics の違い・使い分け・選定基準

- `unknown` → **型が分からない値を一旦安全に受け取る**
- `Generics` → **型が分からないのではなく、あとから決まる型をそのまま保つ**

## まず一言で違い

### unknown

```ts
「この値が何型か分からない」
```

### Generics

```ts
「何型かは呼び出し側が決める。その型を保ちたい」
```

## イメージ比較

| 概念     | イメージ                     |
| -------- | ---------------------------- |
| unknown  | 正体不明の箱                 |
| Generics | 型を入れるためのテンプレート |

## unknown とは

```ts
let value: unknown;

value = 123;
value = "hello";
value = true;
```

何でも入れられる

### でもそのまま使えない

```ts
value.toUpperCase(); // NG
```

何型か分からないから

### 使うには型チェックが必要

```ts
if (typeof value === "string") {
  console.log(value.toUpperCase());
}
```

## Generics とは

```ts
function echo<T>(value: T): T {
  return value;
}
```

```ts
echo("hello"); // T = string
echo(123); // T = number
```

呼び出し時に型が決まる  
しかもその型を保ったまま返せる

## 本質的な違い

## unknown

```ts
function print(value: unknown) {
  // 型が分からない
}
```

関数の中では **value の型が分からない**

## Generics

```ts
function print<T>(value: T): T {
  return value;
}
```

関数の中では **value が T 型であることは分かっている**  
ただし、その T が具体的に何かは呼び出し側次第

## 超重要な違い

### unknown

```ts
「型情報を失っている」
```

### Generics

```ts
「型情報を保持している」
```

## 比較すると分かりやすい例

## 1. unknown の場合

```ts
function getValue(value: unknown) {
  return value;
}
```

戻り値も `unknown`

```ts
const result = getValue("hello");
```

`result` は `unknown`

### つまり

```ts
result.toUpperCase(); // NG
```

## 2. Generics の場合

```ts
function getValue<T>(value: T): T {
  return value;
}
```

```ts
const result = getValue("hello");
```

result は `string`

### つまり

```ts
result.toUpperCase(); // OK
```

## なぜこうなるのか

### unknown

TypeScript 視点では

```ts
「何型か本当に分からない」
```

### Generics

TypeScript 視点では

```ts
「具体的な型はまだ未確定だけど、同じ型であることは分かっている」
```

## unknown を使う場面

### 1. 外部から来たデータ

```ts
const data: unknown = JSON.parse(json);
```

- API レスポンス
- localStorage
- ユーザー入力
- 外部ライブラリの戻り値

### 2. 本当に型が分からない値を一旦受ける

```ts
function handle(value: unknown) {
  if (typeof value === "string") {
    console.log(value.toUpperCase());
  }
}
```

### 3. 安全にしたいとき

`any` を使いたくないが、まだ型を確定できないとき

## Generics を使う場面

### 1. 入力と出力の型を対応させたいとき

```ts
function identity<T>(value: T): T {
  return value;
}
```

### 2. 配列・Promise・useState など、型を後から決めたいとき

```ts
const list: Array<string> = ["a", "b"];
const p: Promise<number> = Promise.resolve(1);
const [count, setCount] = useState<number>(0);
```

### 3. 型を再利用したいとき

```ts
function first<T>(arr: T[]): T {
  return arr[0];
}
```

## 選定基準

ここが一番大事。

## unknown を選ぶ基準

### この値は本当に何型か分からないか？

YES → `unknown`

### 例

- API から来た生データ
- JSON.parse した直後
- catch の error
- ユーザーが何を入れるか分からない値

## Generics を選ぶ基準

### 型は分からないのではなく、呼び出し側によって決まるだけか？

YES → `Generics`

### 例

- 受け取った値をそのまま返す関数
- 配列の要素型に応じて戻り値も変わる関数
- 型を保ったまま処理したい関数

## 実務では

- **外部入力の入口** → `unknown`
- **アプリ内部の汎用処理** → `Generics`

  になりやすい

## まとめ

- `unknown` は「型不明な値を安全に受ける型」
- `Generics` は「型を保ったまま後から決める仕組み」

- `unknown` は型を失う
- `Generics` は型を保持する
