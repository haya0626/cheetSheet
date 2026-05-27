# addEventListener

要素にイベント（クリック・入力など）を登録するためのメソッド

## 基本の書き方

```js
element.addEventListener("イベント名", 関数);
```

## 基本例

```html
<button id="btn">クリック</button>
```

```js
const btn = document.querySelector("#btn");

btn.addEventListener("click", () => {
  console.log("クリックされた");
});
```

## よく使うイベント

| イベント  | 説明           |
| --------- | -------------- |
| click     | クリック       |
| input     | 入力された     |
| change    | 値が確定       |
| submit    | フォーム送信   |
| keydown   | キー押下       |
| mouseover | マウス乗った   |
| resize    | 画面サイズ変更 |

## イベントオブジェクト（e）

```js
btn.addEventListener("click", (e) => {
  console.log(e);
});
```

イベントの情報が入っている

---

### よく使うプロパティ

```js
e.target; // 実際にクリックされた要素
e.currentTarget; // イベントを付けた要素
```

## 何ができるのか

### 1. クリック処理

```js
btn.addEventListener("click", () => {
  alert("クリック");
});
```

### 2. 入力監視

```js
input.addEventListener("input", (e) => {
  console.log(e.target.value);
});
```

### 3. フォーム送信

```js
form.addEventListener("submit", (e) => {
  e.preventDefault();
});
```

### 4. イベント委譲

```js
ul.addEventListener("click", (e) => {
  if (e.target.tagName === "LI") {
    console.log("クリックされた");
  }
});
```

## removeEventListener（セットで重要）

```js
function handler() {
  console.log("クリック");
}

btn.addEventListener("click", handler);

btn.removeEventListener("click", handler);
```

同じ関数参照じゃないと消せない

## よくあるミス

### 関数を実行してしまう

```js
btn.addEventListener("click", handler()); // NG
```

すぐ実行される

---

### 正しい

```js
btn.addEventListener("click", handler);
```

### removeEventListener が効かない

```js
btn.addEventListener("click", () => {});
btn.removeEventListener("click", () => {});
```

## React との違い

### ● JS

```js
btn.addEventListener("click", handler);
```

---

### ● React

```jsx
<button onClick={handler}>
```

React は内部で管理している
