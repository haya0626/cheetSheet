# img

画像を表示するための要素

## img タグとは

```html
<img src="image.jpg" alt="画像の説明" />
```

- 画像を表示するタグ
- 終了タグがない（空タグ）
- `src`と`alt`が基本的に必須

## 何ができるのか（重要まとめ）

1. 画像を表示する

   ```html
   <img src="image.jpg" alt="サンプル画像" />
   ```

1. 外部画像を表示

   ```html
   <img src="https://example.com/image.jpg" alt="外部画像" />
   ```

1. サイズ指定

   ```html
   <img src="image.jpg" alt="画像" width="300" />
   ```

1. レスポンシブ対応（基本）

   ```html
   <img src="image.jpg" alt="画像" style="max-width: 100%;" />
   ```

1. リンクとして使う

   ```html
   <a href="#">
     <img src="image.jpg" alt="画像リンク" />
   </a>
   ```

## どこに使うのか

- 記事内の画像

  ```html
  <article>
    <img src="image.jpg" alt="説明" />
  </article>
  ```

- アイコン・ロゴ

  ```html
  <img src="logo.png" alt="ロゴ" />
  ```

## 重要な考え方

- alt は必須（超重要）

  ```html
  <img src="image.jpg" alt="猫の写真" />
  ```

  → 画像の説明を書く  
  → SEO・アクセシビリティに重要

- 画像はコンテンツか装飾かを考える

  - 意味がある → alt を書く
  - 装飾だけ → alt=""

- パスの理解が重要

  - 相対パス
  - 絶対パス

## div との違い

タグ 意味

- div ただの箱
- img 画像表示

## よく使う属性（超重要）

### ● src（必須）

```html
<img src="image.jpg" />
```

→ 画像の場所

### ● alt（必須）

```html
<img src="image.jpg" alt="説明" />
```

→ 画像の説明

### ● width / height

```html
<img src="image.jpg" width="300" height="200" />
```

→ サイズ指定

### ● loading（遅延読み込み）

```html
<img src="image.jpg" loading="lazy" />
```

→ パフォーマンス改善

### ● class

```html
<img src="image.jpg" class="thumbnail" />
```
