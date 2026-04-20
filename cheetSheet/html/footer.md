# footer

ページやセクションの「締めくくり（最後の情報）」をまとめるための要素

## footer タグとは

```html
<footer>
  <p>© 2026 My Website</p>
</footer>
```

- ページやセクションの一番下に置く情報エリア
- 著作権・連絡先・リンクなどをまとめる
- header と対になるセマンティックタグ

## 何ができるのか

1. 著作権表示（よくあるやつ）

   ```html
   <footer>
     <p>© 2026 My Company</p>
   </footer>
   ```

1. サイトの補助リンク

   ```html
   <footer>
     <a href="#">利用規約</a>
     <a href="#">プライバシーポリシー</a>
   </footer>
   ```

1. 連絡先情報

   ```html
   <footer>
     <p>Email: info@example.com</p>
   </footer>
   ```

1. SNS リンク

   ```html
   <footer>
     <a href="#">Twitter</a>
     <a href="#">Instagram</a>
   </footer>
   ```

1. 記事ごとのフッター

   ```html
   <article>
     <p>本文...</p>

     <footer>
       <p>著者: 山田</p>
     </footer>
   </article>
   ```

- ページだけでなく、記事やセクションごとにも使える

## どこに使うのか

- ページ全体のフッター

  ```html
  <body>
    <footer>サイト全体のフッター</footer>
  </body>
  ```

- セクションごとのフッター

  ```html
  <section>
    <p>内容...</p>
    <footer>補足情報</footer>
  </section>
  ```

## 重要な考え方

- 「下にあるから」ではなく「役割」で使う

  - 画面の下にあるから footer → ✕
  - 補足・締めの情報だから footer → 〇

- 複数使って OK
- ページ内に何個あっても OK

## div との違い

タグ 意味

- div 意味なしの箱
- footer 締め・補足情報という意味あり

→ SEO やアクセシビリティに有利

## よく一緒に使うタグ

```html
<p>
  → テキスト
  <a>
    → リンク
    <nav>→ フッター用メニュー <small> → 小さい注釈や著作権</small></nav></a
  >
</p>
```

実用例（よくある Web サイト）

```html
<footer>
  <p>© 2026 My Website</p>
  <nav>
    <a href="#">About</a>
    <a href="#">Privacy</a>
    <a href="#">Contact</a>
  </nav>
</footer>
```
