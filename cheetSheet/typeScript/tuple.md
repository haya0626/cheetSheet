# Tuple（タプル）

「順番」と「型」が固定された配列

配列だけど、位置ごとに型が決まっている

## 基本

```ts
const data: [string, number] = ["山田", 20];
```

- 1 番目 = string
- 2 番目 = number

## 間違い

```ts
const data: [string, number] = [20, "山田"];
```

型エラー

## 要素数も固定

```ts
const data: [string, number] = ["山田"];
```

NG

## 普通の配列との違い

### ● 通常の配列

```ts
const arr: string[] = ["a", "b"];
```

どこも string

### ● tuple

```ts
const tuple: [string, number] = ["a", 1];
```

位置ごとに型が違う

## よくある使いどころ

### ● ペアデータ

```ts
const user: [string, number] = ["山田", 20];
```

### ● 関数の戻り値

```ts
function getUser(): [string, number] {
  return ["山田", 20];
}
```

### ● React（超重要）

```ts
const [count, setCount] = useState(0);
```

これ実は

```ts
[number, Dispatch<...>]
```

tuple

## 分解すると

```ts
const state: [number, Function] = useState(0);
```

配列じゃなくて tuple

## 名前付き tuple（分かりやすい）

```ts
type User = [name: string, age: number];
```

```ts
const user: User = ["山田", 20];
```

## 取り出し

```ts
const user: [string, number] = ["山田", 20];

const name = user[0]; // string
const age = user[1]; // number
```

## 分割代入（よく使う）

```ts
const [name, age] = user;
```

## 注意点

### push できる問題

```ts
const t: [string, number] = ["a", 1];
t.push("追加"); // 型的には許されることがある
```

実務ではあまり使わない

## Array との違い

| 項目 | Array  | Tuple          |
| ---- | ------ | -------------- |
| 型   | 同じ   | 位置ごとに違う |
| 長さ | 可変   | 基本固定       |
| 用途 | リスト | 構造データ     |
