# header

ページやセクションの「見出し・導入部分」をまとめるための要素

## header タグとは

```html
<header>
  <h1>サイトタイトル</h1>
</header>
```

- ページやセクションの先頭に置く導入エリア
- ロゴ・タイトル・ナビゲーションなどをまとめる
- HTML5 で追加された意味を持つタグ（セマンティックタグ）

## 何ができるのか

1. サイトのタイトル表示

   ```html
   <header>
     <h1>My Blog</h1>
   </header>
   ```

1. ナビゲーションメニューを置く

   ```html
   <header>
     <nav>
       <a href="/">ホーム</a>
       <a href="/about">概要</a>
     </nav>
   </header>
   ```

1. 記事のヘッダーとして使う

   ```html
   <article>
     <header>
       <h2>記事タイトル</h2>
       <p>投稿日: 2026-04-14</p>
     </header>
     <p>本文...</p>
   </article>
   ```

## どこに使うのか

1. ページ全体のヘッダー

   ```html
   <body>
     <header>サイト全体のヘッダー</header>
   </body>
   ```

1. セクションごとのヘッダー

   ```html
   <section>
     <header>このセクションのタイトル</header>
   </section>
   ```

## 重要な考え方

- 「上にある見た目」ではなく「役割」で使う

  - 画面の上にあるから header → ✕
  - 見出し・導入部分だから header → 〇

- 複数使って OK

  ```html
  <header>サイト用</header>

  <article>
    <header>記事用</header>
  </article>
  ```

- div との違い
  - div ただの箱（意味なし）
  - header 見出し・導入部分という意味あり
