# FormEvent

フォーム全体のイベント（主に送信）の型

特に `onSubmit` で使う

## 基本の書き方

```tsx
import { FormEvent } from "react";

const handleSubmit = (e: FormEvent<HTMLFormElement>) => {
  e.preventDefault();
  console.log("送信");
};
```

```tsx
<form onSubmit={handleSubmit}>
  <button type="submit">送信</button>
</form>
```

## 何ができるのか（重要まとめ）

### 1. フォーム送信を受け取る

```tsx
const handleSubmit = (e: FormEvent<HTMLFormElement>) => {
  console.log("送信された");
};
```

### 2. リロードを止める

```tsx
e.preventDefault();
```

デフォルト動作（ページリロード）を防ぐ

### 3. イベント元を取得

```tsx
e.currentTarget;
```

`<form>` 要素

## 基本構造

```ts
FormEvent<T>;
```

T = イベントが発生した要素

## よく使う型

| 要素 | 型                         |
| ---- | -------------------------- |
| form | FormEvent<HTMLFormElement> |

## ChangeEvent との違い

| イベント    | 用途               |
| ----------- | ------------------ |
| ChangeEvent | 入力が変わったとき |
| FormEvent   | フォーム全体の操作 |

### ● ChangeEvent

```tsx
<input onChange={handleChange} />
```

入力ごとに発火

### ● FormEvent

```tsx
<form onSubmit={handleSubmit} />
```

送信時に発火

## 実務での基本パターン

```tsx
import { FormEvent, useState } from "react";

function App() {
  const [name, setName] = useState("");

  const handleSubmit = (e: FormEvent<HTMLFormElement>) => {
    e.preventDefault();
    console.log(name);
  };

  return (
    <form onSubmit={handleSubmit}>
      <input value={name} onChange={(e) => setName(e.target.value)} />
      <button type="submit">送信</button>
    </form>
  );
}
```

## 重要ポイント

FormEvent では

```tsx
e.target.value;
```

基本使わない

理由

フォーム全体のイベントだから

## 正しい考え方

- 入力値 → ChangeEvent で取得
- 送信 → FormEvent で処理

## よくあるミス

### ❌ FormEvent で value 取ろうとする

```tsx
e.target.value; // NG（型的に保証されない）
```

### ❌ preventDefault 忘れる

ページリロードする

## e.target vs e.currentTarget

```tsx
const handleSubmit = (e: FormEvent<HTMLFormElement>) => {
  console.log(e.target); // 子要素の可能性あり
  console.log(e.currentTarget); // form
};
```

FormEvent では `currentTarget` を使うことが多い

## React 特有ポイント

FormEvent は

**SyntheticEvent**

をラップしてる

```ts
FormEvent<T> extends SyntheticEvent<T>
```

## 型を書かない場合

```tsx
<form
  onSubmit={(e) => {
    e.preventDefault();
  }}
/>
```

推論されることが多い

## 重要な考え方

- FormEvent は「フォーム全体」
- ChangeEvent は「入力単体」
- 役割を分ける
