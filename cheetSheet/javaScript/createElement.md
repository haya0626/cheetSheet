# document.createElement

新しい HTML 要素を JavaScript で作るためのメソッド

## 基本の使い方

```js
const div = document.createElement("div");
```

`<div>` をメモリ上に作成

## 画面に表示するには

```js
document.body.appendChild(div);
```

初めて DOM に追加される

## 何ができるのか

### 1. 要素を作る

```js
const p = document.createElement("p");
```

### 2. テキストを入れる

```js
p.textContent = "こんにちは";
```

### 3. クラスを付ける

```js
p.classList.add("text");
```

### 4. 属性を設定

```js
p.setAttribute("id", "title");
```

### 5. DOM に追加

```js
document.body.appendChild(p);
```

## 完成例

```js
const p = document.createElement("p");
p.textContent = "Hello";
p.classList.add("text");

document.body.appendChild(p);
```

`<p class="text">Hello</p>` が追加される

## よく使う追加方法

### ● appendChild

```js
parent.appendChild(child);
```

1 つだけ追加

---

### ● append（おすすめ）

```js
parent.append(child);
```

複数 OK・文字も OK

## 複数要素の作成

```js
const ul = document.createElement("ul");

for (let i = 0; i < 3; i++) {
  const li = document.createElement("li");
  li.textContent = `Item ${i}`;
  ul.appendChild(li);
}

document.body.appendChild(ul);
```

## innerHTML との違い

### ● createElement

```js
const p = document.createElement("p");
p.textContent = "OK";
```

安全・構造的

---

### ● innerHTML

```js
container.innerHTML = "<p>OK</p>";
```

速いが危険（XSS）

---

## 比較

| 方法          | 特徴             |
| ------------- | ---------------- |
| createElement | 安全・細かく制御 |
| innerHTML     | 簡単・一括       |

## React との違い

React では使わない

### 理由

- 仮想 DOM で管理されている
- 直接 DOM 操作するとズレる

---

### React の代替

```jsx
return <div>Hello</div>;
```

JSX で要素生成
