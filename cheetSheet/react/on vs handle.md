# on vs handle

## on と handle の使い分け（イベントハンドラ）

| 名前            | 役割                               | 使う場所                     |
| --------------- | ---------------------------------- | ---------------------------- |
| **`onXxx`**     | **「イベントが起きた」ことを表す** | **props / イベントの入口**   |
| **`handleXxx`** | **イベントを処理する関数**         | **コンポーネント内部の関数** |

## ① `on~` は「イベントの通知名」

### 基本ルール

- **「何が起きたか」だけを表す**
- 処理の中身は想像させない
- **親から渡される callback** に使う

```tsx
function Button({ onClick }) {
  return <button onClick={onClick}>Click</button>;
}
```

```tsx
<Button onClick={handleClick} />
```

## ② `handle~` は「処理をする関数名」

### 基本ルール

- **「どう処理するか」を表す**
- コンポーネントの中で定義する
- 副作用・状態更新が入ることが多い

```tsx
function Form() {
  const handleSubmit = () => {
    // バリデーション
    // API呼び出し
    // state更新
  };

  return <form onSubmit={handleSubmit} />;
}
```

✅ 正解例

```tsx
function Parent() {
  const handleSave = () => {
    console.log("保存する");
  };

  return <Child onSave={handleSave} />;
}

function Child({ onSave }) {
  return <button onClick={onSave}>Save</button>;
}
```

役割がはっきり

- `onSave` → イベント名
- `handleSave` → 処理内容
- 子は 「何が起きたか」しか知らない
- どう処理するかは知らない / 知る必要がない

👉 責務の分離ができる

❌ 悪い例

```tsx
<Child handleSave={handleSave} />
```

- 子が「処理を持ってる感」が出る
- 親の事情が props 名に漏れている

→ Child から見ると

「これはイベント？処理？どっち？」となる

## まとめ

- 親：`handle~` を定義
- 子：`on~` として受け取る
- **「on = 合図」「handle = 処理」**
- 迷ったら props は `on~`
