# undefined と null の違い

「値がない」ように見える 2 つの値だが、意味が違う

- `undefined` → **まだ値が設定されていない**
- `null` → **意図的に値がない**

## ざっくりイメージ

### ● undefined

```js
let name;
console.log(name); // undefined
```

- 変数はある
- でも値が入っていない

### ● null

```js
const user = null;
```

- わざと「値なし」にしている

## 基本の違い

| 値        | 意味              |
| --------- | ----------------- |
| undefined | 未定義 / 未設定   |
| null      | 空 / 意図的にない |

## undefined とは

```js
let x;
console.log(x); // undefined
```

- 変数を宣言したが値を入れていない
- 存在するが中身が未設定
- JavaScript が自動で入れることが多い

## undefined が出る場面

### 1. 変数に値を入れていない

```js
let x;
console.log(x); // undefined
```

### 2. オブジェクトに存在しないプロパティ

```js
const user = { name: "山田" };

console.log(user.age); // undefined
```

### 3. 関数の戻り値がない

```js
function test() {}

console.log(test()); // undefined
```

### 4. 配列の存在しない要素

```js
const arr = [1, 2];

console.log(arr[10]); // undefined
```

## null とは

```js
const selectedUser = null;
```

- 値がないことを自分で明示する
- 「空です」と意図的に表現する値

## null が使われる場面

### 1. 選択中のデータがない

```js
const selectedUser = null;
```

- まだ誰も選ばれていない

### 2. DOM 要素が見つからない可能性

```js
const el = document.querySelector(".box");
```

型的には `Element | null`

### 3. API 結果が空

```js
{
  "user": null
}
```

値はあるが中身が空

## 実務での感覚的な違い

### undefined

- まだ決まってない
- まだ入ってない
- 存在しない

### null

- 空です
- 何もありません
- 意図的にないです

## typeof の罠（超重要）

```js
typeof undefined; // "undefined"
typeof null; // "object"
```

- `null`なのに `"object"`
- これは JavaScript の古い仕様による有名な罠

## == と === の違い

### ● 緩い比較

```js
null == undefined; // true
```

同じ扱いされる

### ● 厳密比較

```js
null === undefined; // false
```

別物

## 実務では基本これ

```js
value == null;
```

`null` と `undefined` をまとめて判定できる

### 例

```js
if (value == null) {
  console.log("null か undefined");
}
```

## TypeScript での違い

### ● 型としての undefined

```ts
let value: string | undefined;
```

値がまだ入っていない可能性

### ● 型としての null

```ts
let value: string | null;
```

意図的に空の可能性

### ● 両方含む

```ts
let value: string | null | undefined;
```

## React でよくある使い分け

### ● undefined

```ts
const [data, setData] = useState<string | undefined>();
```

初期値未設定

### ● null

```ts
const [selectedUser, setSelectedUser] = useState<User | null>(null);
```

「今は選択中のユーザーなし」

## 重要な考え方（ここ大事）

- `undefined` は自然発生しやすい
- `null` は自分で使うことが多い
- 実務では「どちらを使うか」を決めるのが大事

## よくあるミス

- `null` と `undefined` を同じものと思う
- `===` と `==` の違いを意識しない
- TypeScript で片方しか許容していないのに両方来ると思う

## 実務でのおすすめ

- 未設定 → `undefined`
- 意図的な空 → `null`
