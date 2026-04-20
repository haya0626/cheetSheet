# React.ReactNode

React.ReactNode は 「React で画面に表示できるもの全部」 を表す型

- React コンポーネントが子要素（children）として受け入れることができるデータ型を表す
- 文字列、数値、React コンポーネント、配列、null、undefined など、多岐にわたる型を包括する
- TypeScript で React コンポーネントを定義する際に、ReactNode は子要素の型定義としてよく使われる

## React.ReactNode に含まれるもの

```ts
type ReactNode =
  | JSX.Element // <div /> など
  | string // "Hello"
  | number // 123
  | boolean // true / false（実際には描画されない）
  | null
  | undefined
  | ReactNode[]; // 配列
```

## よくある使い方（children）

```ts
type Props = {
  children: React.ReactNode;
};

const Card = ({ children }: Props) => {
  return <div className="card">{children}</div>;
};
```

```html
<Card>
  <h1>タイトル</h1>
  <p>本文</p>
</Card>
```

- どんな中身が来ても受け取れるのが強み
