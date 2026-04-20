# document.querySelector

CSS セレクタを使って、HTML 要素を 1 つ取得するためのメソッド

## 基本の使い方

```js
const el = document.querySelector("p");
```

最初に見つかった `<p>` を取得

## 何ができるのか（重要まとめ）

### 1. タグで取得

```js
document.querySelector("div");
```

### 2. クラスで取得

```js
document.querySelector(".box");
```

### 3. id で取得

```js
document.querySelector("#app");
```

### 4. ネスト指定

```js
document.querySelector("ul li");
```

ul の中の li

### 5. 属性指定

```js
document.querySelector('input[name="username"]');
```

## querySelectorAll との違い

### ● querySelector

```js
document.querySelector(".item");
```

最初の 1 つだけ

### ● querySelectorAll

```js
document.querySelectorAll(".item");
```

全部取得（NodeList）

## 戻り値

```js
const el = document.querySelector(".box");
```

- 見つかる → 要素
- 見つからない → null

## よくあるミス

### null エラー

```js
const el = document.querySelector(".box");
el.textContent = "変更"; // エラーの可能性
```

### 安全な書き方

```js
const el = document.querySelector(".box");

if (el) {
  el.textContent = "変更";
}
```

## 要素操作の例

```js
const el = document.querySelector("#title");

el.textContent = "変更";
el.style.color = "red";
```

## addEventListener と組み合わせ

```js
const btn = document.querySelector("button");

btn.addEventListener("click", () => {
  console.log("クリック");
});
```

## getElementById との違い

| メソッド       | 特徴             |
| -------------- | ---------------- |
| querySelector  | CSS で柔軟に指定 |
| getElementById | id 専用・高速    |

## React との関係

基本は使わない

### React では

```js
useRef();
```

これを使う
