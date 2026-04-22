# SetStateAction（React + TypeScript）

「値」か「更新関数」を受け取れる型

## 正体

```ts
type SetStateAction<T> = T | ((prev: T) => T);
```

## 分解

```ts
T;
```

新しい値をそのまま渡す

```ts
(prev: T) => T;
```

前の状態を使って更新する関数

## useState との関係

```ts
const [count, setCount] = useState<number>(0);
```

setCount の型

```ts
Dispatch<SetStateAction<number>>;
```

展開

```ts
(value: number | (prev: number) => number) => void
```

## 使い方 ①（値を直接渡す）

```ts
setCount(10);
```

## 使い方 ②（関数で更新）

```ts
setCount((prev) => prev + 1);
```

前の状態を使う

## なぜ関数があるのか？

**非同期でも安全に更新するため**

### 危険な例

```ts
setCount(count + 1);
setCount(count + 1);
```

同じ値を 2 回使う可能性

### 正しい

```ts
setCount((prev) => prev + 1);
setCount((prev) => prev + 1);
```

ちゃんと 2 回増える

## 超重要ポイント

**prev は「最新の state」**

## イメージ

```text
状態更新はすぐ反映されない（非同期）
```

だから

**確実な値を使うために関数を使う**

## オブジェクト更新

```ts
setUser((prev) => ({
  ...prev,
  name: "田中",
}));
```

イミュータブルと相性 ◎

## 値 vs 関数

| 方法 | 使う場面 |
||--|
| 値 | 単純に置き換え |
| 関数 | 前の値を使う |

## React 内部イメージ

```ts
dispatch(action);
```

action が

```ts
T または (prev) => T
```

これが SetStateAction

## 重要な考え方

- setState は「直接変更」ではない
- 更新は遅延・バッチ処理される
- だから関数が必要

## よくあるミス

### prev を使わない

```ts
setCount(count + 1);
```

同期っぽく書いてしまう

### オブジェクト直接変更

```ts
user.name = "田中"; // NG
```
