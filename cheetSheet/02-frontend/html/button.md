# button

クリック操作を行うための要素

## button タグとは

```html
<button>クリック</button>
```

- クリックできるボタンを作るタグ
- フォーム送信や処理実行に使う
- 開始タグと終了タグが必要

## button タグでできること

1. クリックボタン

   ```html
   <button>クリック</button>
   ```

1. フォーム送信

   ```html
   <form>
     <button type="submit">送信</button>
   </form>
   ```

1. JavaScript 処理の実行

   ```html
   <button onclick="alert('クリック')">実行</button>
   ```

1. リセットボタン

   ```html
   <button type="reset">リセット</button>
   ```

1. 中に HTML を入れられる

   ```html
   <button>
     <img src="icon.png" alt="" />
     送信
   </button>
   ```

## どこに使うのか

- フォーム送信

  ```html
  <form>
    <button type="submit">送信</button>
  </form>
  ```

- UI 操作（JS）

  ```html
  <button>開く</button>
  ```

## 重要な考え方

- デフォルトは submit（超重要）

  ```html
  <button>送信される</button>
  ```

  → form 内だと自動で送信される

- 明示的に type を書く

  ```html
  <button type="button">通常ボタン</button>
  ```

- a タグとの違い

  - button → 処理実行
  - a → ページ移動

## よく使う属性

### ● type

```html
<button type="submit">送信</button>
```

- submit → 送信（デフォルト）
- button → 通常ボタン
- reset → リセット

### ● disabled

```html
<button disabled>無効</button>
```

### ● name / value

```html
<button name="action" value="save">保存</button>
```

### ● class

```html
<button class="btn">ボタン</button>
```

## input[type=button] との違い

タグ 特徴

- button 中に HTML を入れられる
- input 制限あり（テキストのみ）

例：

```html
<button><strong>OK</strong></button> <input type="button" value="OK" />
```
