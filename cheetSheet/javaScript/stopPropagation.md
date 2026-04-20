# stopPropagation

イベントの伝播（バブリング）を止めるためのメソッド

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

child.addEventListener("click", (e) => {
  e.stopPropagation();
  console.log("子");
});
```

### 実行結果

```text
子
```

親には伝わらない

## stopPropagation がない場合

```text
子
親
```

イベントが上に伝わる

## なぜ起きる？

**イベントバブリング**

```text
子 → 親 → さらに親 → document
```

## イベントの流れ（重要）

```text
キャプチャ → ターゲット → バブリング
```

通常は

**バブリングで上に伝わる**

## stopPropagation の役割

```js
e.stopPropagation();
```

ここでストップ

```text
子（止まる）
```

## よくある使いどころ

### ● モーダル

```js
overlay.onClick = closeModal;

modal.onClick = (e) => {
  e.stopPropagation();
};
```

- 背景クリックで閉じる
- 中クリックでは閉じない

## React での例

```jsx
<div onClick={() => console.log("親")}>
  <button
    onClick={(e) => {
      e.stopPropagation();
      console.log("子");
    }}
  >
    クリック
  </button>
</div>
```

## preventDefault との違い

| メソッド        | 役割                   |
| --------------- | ---------------------- |
| stopPropagation | イベントの伝播を止める |
| preventDefault  | デフォルト動作を止める |

### ● preventDefault 例

```js
e.preventDefault();
```

リンク遷移・フォーム送信を止める

## よくあるミス

- stopPropagation で submit を止めようとする
  - 無理（preventDefault が必要）
