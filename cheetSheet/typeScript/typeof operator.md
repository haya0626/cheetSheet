# typeof 演算子

### 値から型を生成するための演算子

## 基本構文

```typescript
type T = typeof value;
```

- 値 → 型を取り出す
- 実行時コードには一切影響しない

## 基本例：値から型を作る

```typescript
const user = {
  id: 1,
  name: "Alice",
};

type User = typeof user;
```

展開結果

```typescript
type User = {
  id: number;
  name: string;
};
```

## 関数 × typeof

```typescript
function add(a: number, b: number) {
  return a + b;
}

type AddFn = typeof add;
```

展開結果

```typescript
type AddFn = (a: number, b: number) => number;
```

※戻り値型だけ欲しい場合は`ReturnType`

```typescript
type AddReturn = ReturnType<typeof add>;
// number
```

## 配列・タプル × typeof

```typescript
const colors = ["red", "green", "blue"] as const;
type Colors = typeof colors;
```

```typescript
type Colors = readonly ["red", "green", "blue"];
```

### 要素型だけ欲しい場合

```typescript
type Color = (typeof colors)[number];
// "red" | "green" | "blue"
```

## typeof + keyof

```typescript
const routes = {
  home: "/",
  login: "/login",
  profile: "/profile",
};

type RouteKey = keyof typeof routes;
// "home" | "login" | "profile"
```
