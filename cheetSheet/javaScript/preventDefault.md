# preventDefault

ブラウザが本来やる動作を止めるのメソッド

## 基本例

```html
<a href="https://example.com">リンク</a>
```

```js
document.querySelector("a").addEventListener("click", (e) => {
  e.preventDefault();
  console.log("遷移しない");
});
```

### 実行結果

ページ遷移しない

## デフォルト動作とは？

要素が元々持っている動作

例：

- a タグ → ページ遷移
- form → 送信（リロード）
- checkbox → チェック状態変更

## よくある使いどころ

### ● フォーム送信を止める

```html
<form>
  <button type="submit">送信</button>
</form>
```

```js
form.addEventListener("submit", (e) => {
  e.preventDefault();
  console.log("リロードしない");
});
```

### ● React での例（超重要）

```jsx
<form
  onSubmit={(e) => {
    e.preventDefault();
    console.log("送信処理");
  }}
>
  <button type="submit">送信</button>
</form>
```

リロードを防ぐ

### ● リンクの制御

```js
e.preventDefault();
```

SPA で画面遷移を JS で制御

## stopPropagation との違い

| メソッド        | 役割                   |
| --------------- | ---------------------- |
| preventDefault  | デフォルト動作を止める |
| stopPropagation | イベント伝播を止める   |

### ● 例で比較

```js
e.preventDefault(); // 送信しない
e.stopPropagation(); // 親に伝えない
```
