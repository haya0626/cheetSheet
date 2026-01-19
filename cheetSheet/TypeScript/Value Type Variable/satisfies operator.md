# satisfies 演算子

### 値が指定された型を満たすことを確認しながら、元の型推論を保持する演算子

satisfies は「型を“押し付けない”が、“満たしているか”は厳密に検証する」演算子

- 型チェックはする
- 型推論は壊さない
- リテラル型を保持する

## satisfies の基本構文

```typescript
const value = expression satisfies Type;
```

- チェック専用
- 型は上書きされない
- 実行時には存在しない

## 実務での利用例

### オブジェクトのプロパティ型保持

```typescript
type Colors = "red" | "green" | "blue";

// 従来の型注釈：プロパティの型が広くなってしまう
const palette1: Record<string, Colors> = {
  primary: "red", // Colors型
  secondary: "blue", // Colors型
};

// satisfiesを使用：具体的な値の型を保持
const palette2 = {
  primary: "red", // 'red'型（リテラル型）
  secondary: "blue", // 'blue'型（リテラル型）
} satisfies Record<string, Colors>;

// palette2.primaryは'red'型なので、より厳密な型チェックが可能
if (palette2.primary === "red") {
  // この分岐では型が確定している
}
```

### 設定オブジェクトの検証

```typescript
type Config = {
  apiUrl: string;
  timeout: number;
  retries?: number;
  features: {
    enableLogging: boolean;
    enableCache: boolean;
  };
};

const config = {
  apiUrl: "https://api.example.com",
  timeout: 5000,
  features: {
    enableLogging: true,
    enableCache: false,
  },
} satisfies Config;

// configの型は推論された型のまま
```

## 「型推論・型注釈・as const・satisfies」の 役割の違いと使い分け

| 手段         | 役割   | 型を固定 | 嘘防止 | 推論保持 |
| ------------ | ------ | -------- | ------ | -------- |
| 型推論       | 任せる | ❌       | △      | ✅       |
| 型注釈 `: T` | 固定   | ✅       | ✅     | ❌       |
| `as const`   | 最小化 | ✅       | △      | ❌       |
| `satisfies`  | 検証   | ❌       | ✅     | ✅       |

- 型推論は「任せる」
- 型注釈は「縛る」
- as const は「固める」
- satisfies は「検査する」

### 型注釈と satisfies の違い

```typescript
type Config = {
  mode: "dev" | "prod";
  debug: boolean;
};
```

① 型注釈の場合

```typescript
const config: Config = {
  mode: "prod",
  debug: false,
};

// config の型
{
  mode: "dev" | "prod";
  debug: boolean;
}
```

② satisfies の場合

```typescript
const config = {
  mode: "prod",
  debug: false,
} satisfies Config;

// config の型
{
  mode: "prod";
  debug: false;
}
```

また、vscode でホバーした時に定義元にジャンプしなくても型を確認できるメリットもある。

値を保持した状態で、キーが不足してないか検査するので`as const`と相性が良い気がした

```typescript
type Routes = "/" | "/login" | "/profile";

const ROUTES = {
  home: "/",
  login: "/login",
  profile: "/profile",
} as const satisfies Record<string, Routes>;

type Route = (typeof ROUTES)[keyof typeof ROUTES];

function navigate(to: Route) {}

navigate("/login"); // ✅
navigate("hogehoge"); // ❌
```
