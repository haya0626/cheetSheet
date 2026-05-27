# Dispatch

関数の型（「命令を受け取る関数」の型）

## 一番よく見る形

```ts
Dispatch<Action>;
```

これは

Action 型を受け取る関数

## 正体（実はこう）

```ts
type Dispatch<A> = (value: A) => void;
```

分解すると

```text
A を受け取る関数
戻り値はなし
```

## つまり

```ts
Dispatch<Action>;
```

こうなる

```ts
(action: Action) => void
```

## useState の例

```ts
const [count, setCount] = useState<number>(0);
```

setCount の型

```ts
Dispatch<SetStateAction<number>>;
```

## さらに分解

```ts
type SetStateAction<T> = T | ((prev: T) => T);
```

つまり

```ts
setCount: (value: number | (prev: number) => number) => void
```

## 実際の使い方

```ts
setCount(10);
setCount((prev) => prev + 1);
```

両方 OK
