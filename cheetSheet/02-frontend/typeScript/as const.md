# as const

### 値を readonly にした上で、リテラル型にする型アサーション

## as const の役割

```ts
const status = "success" as const;
```

```ts
const status: "success";
```

効果まとめ

- 型の widening（拡張）を防ぐ
- リテラル型を保持
- readonly 化

as const がないと string 型になってしまう。

status の型は文字列"success"に固定したい → `as const`

## as const が行う 3 つのこと

### 1.リテラル型にする

```ts
const n = 1 as const;
// type: 1
```

### 2.readonly にする

```ts
const obj = { x: 1 } as const;
// { readonly x: 1 }
```

### 3.配列をタプルにする

```ts
const arr = [1, 2, 3] as const;
// readonly [1, 2, 3]
```

## 応用

### API レスポンスの型固定(関数の戻り値 × as const )

```ts
function createStatus() {
  return {
    ok: true,
    code: 200,
  } as const;
}
```

```ts
{
  readonly ok: true;
  readonly code: 200;
}
```

### enum の代替

```ts
const ROLE = {
  ADMIN: "admin",
  USER: "user",
  GUEST: "guest",
} as const;

type Role = (typeof ROLE)[keyof typeof ROLE];
```

```ts
// "admin" | "user" | "guest"
```

`keyof typeof ROLE`で、オブジェクトのキーで構成されたユニオンを作成し、そこから`typeof`で、value 値の型を作成する。
