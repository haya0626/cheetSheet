# イベントバブリング

イベントが「子要素から親要素へ順番に伝わっていく仕組み」

## 基本例

```html
<div id="parent">
  <button id="child">クリック</button>
</div>
```

```js
parent.addEventListener("click", () => {
  console.log("親");
});

child.addEventListener("click", () => {
  console.log("子");
});
```

### 実行結果

```text
子
親
```

子から親へイベントが伝わる

## イメージ

```text
ボタン（子）
   ↓
div（親）
   ↓
body
   ↓
document
```

下から上へ伝播

## なぜ起きるのか

- DOM は「入れ子構造」

- イベントもそれに沿って伝わる

## イベントの流れ（重要）

```text
① キャプチャ（上 → 下）
② ターゲット（クリックされた要素）
③ バブリング（下 → 上）
```

### 通常は

バブリングだけ意識すれば OK

## React での例

```jsx
<div onClick={() => console.log("親")}>
  <button onClick={() => console.log("子")}>クリック</button>
</div>
```

### 実行結果

```text
子
親
```

React でも同じ

## よくあるバグ

- ボタン押したのに親の処理も動く
- モーダルが勝手に閉じる
- 二重処理になる

## stopPropagation との関係

```js
e.stopPropagation();
```

ここでバブリング停止

## 重要な考え方

- イベントは「勝手に上に伝わる」
- 意識しないとバグになる
- 必要なときだけ止める
