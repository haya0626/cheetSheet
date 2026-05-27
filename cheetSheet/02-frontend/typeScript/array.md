# Array

配列の中に入る「型」を指定するための書き方（Generics）

## 基本の書き方

```ts
const numbers: Array<number> = [1, 2, 3];
```

number だけ入る配列

## 実はこれと同じ

```ts
const numbers: number[] = [1, 2, 3];
```

完全に同じ意味

## つまり

```text
Array<number> = number[]
```

## 何が起きてるのか

Array は実は

```ts
Array<T>;
```

Generics

## イメージ

```text
Array<T>
```

T = 中に入る型

```text
Array<string>
Array<number>
Array<User>
```

## 具体例

### ● 文字列配列

```ts
const names: Array<string> = ["山田", "田中"];
```

### ● 数値配列

```ts
const nums: Array<number> = [1, 2, 3];
```

### ● オブジェクト配列

```ts
type User = {
  name: string;
};

const users: Array<User> = [{ name: "山田" }, { name: "田中" }];
```

## 型チェック

```ts
const nums: Array<number> = [1, 2, 3];

nums.push(4); // OK
nums.push("a"); // NG
```

## Generics として見ると

```ts
Array<T>;
```

「型を受け取るクラス」

## 配列メソッドでも使われている

```ts
const arr = [1, 2, 3];

arr.map((n) => n * 2);
```

実は

```ts
map<T, U>();
```

Generics 使われてる

- map<T, U> =「T 型の配列を U 型の配列に変換する」関数

イメージ

```ts
map<T, U>(callback: (value: T) => U): U[]
```

## なぜ 2 つ書き方があるの？

### ● number[]

短くてよく使う

### Array<number>

Generics っぽく書きたいとき

## React での例

```ts
const [list, setList] = useState<Array<string>>([]);
```

```ts
const [list, setList] = useState<string[]>([]);
```

## よくあるミス

- 型を付けない
- any[]を使う
- 型を混ぜる
