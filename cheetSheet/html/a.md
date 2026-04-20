# a

リンク（他のページや場所へ移動する）ための要素

## a タグとは

```html
<a href="https://example.com">リンク</a>
```

- クリックすると別のページや場所へ移動する
- `href`属性でリンク先を指定する
- Web ページをつなぐ最も重要なタグの 1 つ

## 何ができるのか（重要まとめ）

1. 別ページへ移動

   ```html
   <a href="https://example.com">サイトへ</a>
   ```

1. 同一ページ内リンク（アンカーリンク）

   ```html
   <a href="#section1">セクション1へ</a>

   <h2 id="section1">セクション1</h2>
   ```

1. メール送信リンク

   ```html
   <a href="mailto:test@example.com">メールする</a>
   ```

1. 電話リンク（スマホ向け）

   ```html
   <a href="tel:09012345678">電話する</a>
   ```

1. 新しいタブで開く

   ```html
   <a href="https://example.com" target="_blank">別タブで開く</a>
   ```

## どこに使うのか

- ナビゲーションメニュー

  ```html
  <nav>
    <a href="#">Home</a>
    <a href="#">About</a>
  </nav>
  ```

- 文章中のリンク

  ```html
  <p>詳しくは <a href="#">こちら</a></p>
  ```

## 重要な考え方（ここ大事）

- 「移動させるため」に使う

  - ボタンの見た目だけで使う ×
  - ページ遷移やリンクに使う ○

- href は必須

  ```html
  <a>リンク</a> ← NG
  ```

- ブロック要素も囲める（HTML5 以降）

  ```html
  <a href="#">
    <div>カード全体をクリック可能</div>
  </a>
  ```

## div との違い

タグ 意味

- div ただの箱
- a リンク（移動できる）

## button との違い

タグ 役割

- a ページ移動
- button 処理実行（JS など）

例：

```html
<a href="/next">次のページ</a> <button>クリック処理</button>
```

## よく使う属性

- href → リンク先（必須）
- target="\_blank" → 新しいタブで開く
- rel="noopener" → セキュリティ対策

例：

```html
<a href="https://example.com" target="_blank" rel="noopener"> 外部リンク </a>
```
