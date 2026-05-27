# MouseEvent

マウス操作（クリック・ホバーなど）のイベントの型

## 基本の書き方

```tsx
import { MouseEvent } from "react";

const handleClick = (e: MouseEvent<HTMLButtonElement>) => {
  console.log("クリック");
};
```

```tsx
<button onClick={handleClick}>クリック</button>
```

## 基本構造

```ts
MouseEvent<T>;
```

T = イベントが発生した要素

## よく使う型

| 要素   | 型                            |
| ------ | ----------------------------- |
| button | MouseEvent<HTMLButtonElement> |
| div    | MouseEvent<HTMLDivElement>    |
| a      | MouseEvent<HTMLAnchorElement> |

## 何ができるのか

### 1. クリックイベント

```tsx
const handleClick = (e: MouseEvent<HTMLButtonElement>) => {
  console.log("クリックされた");
};
```

### 2. クリックされた要素取得

```tsx
const handleClick = (e: MouseEvent<HTMLDivElement>) => {
  console.log(e.target);
};
```

### 3. 位置情報

```tsx
const handleClick = (e: MouseEvent<HTMLDivElement>) => {
  console.log(e.clientX, e.clientY);
};
```

クリック位置が分かる

### 4. 修飾キー

```tsx
const handleClick = (e: MouseEvent<HTMLDivElement>) => {
  if (e.shiftKey) {
    console.log("Shift押されてる");
  }
};
```

## よく使うプロパティ

| プロパティ           | 意味                     |
| -------------------- | ------------------------ |
| e.target             | 実際にクリックされた要素 |
| e.currentTarget      | イベントを付けた要素     |
| e.clientX / clientY  | 画面上の座標             |
| e.pageX / pageY      | ページ全体の座標         |
| e.button             | 押されたボタン           |
| e.ctrlKey / shiftKey | 修飾キー                 |

## クリック以外のイベント

### ● onClick

```tsx
<button onClick={handleClick} />
```

### ● onMouseEnter

```tsx
<div onMouseEnter={handleMouse} />
```

### ● onMouseLeave

```tsx
<div onMouseLeave={handleMouse} />
```

### ● onMouseMove

```tsx
<div onMouseMove={handleMouse} />
```

全部 `MouseEvent`

## preventDefault / stopPropagation

```tsx
const handleClick = (e: MouseEvent<HTMLAnchorElement>) => {
  e.preventDefault();
  e.stopPropagation();
};
```

MouseEvent でも普通に使える

## React 特有ポイント

React のイベントは

**SyntheticEvent**

```ts
MouseEvent<T> extends SyntheticEvent<T>
```

DOM イベントをラップしている

## 型を書かない場合

```tsx
<button
  onClick={(e) => {
    console.log(e.target);
  }}
/>
```

多くの場合は推論される

## 明示した方がいい場面

- 関数を外に出すとき
- 型を明確にしたいとき
- 共通処理にするとき

## よくあるミス

### 型を書かない

```tsx
const handleClick = (e) => {};
```

any になる可能性

### 要素型ミス

```tsx
MouseEvent<HTMLDivElement>;
```

実際は button だった

## target vs currentTarget

```tsx
const handleClick = (e: MouseEvent<HTMLDivElement>) => {
  console.log(e.target); // 実際にクリックされた要素
  console.log(e.currentTarget); // div
};
```

## 重要な考え方

- MouseEvent は「マウス操作のイベント型」
- Generics で要素を指定する
