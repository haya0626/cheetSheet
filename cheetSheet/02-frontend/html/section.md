# section

コンテンツを「意味のあるまとまり」で区切るための要素

## section>タグとは

```html
<section>
  <h2>見出し</h2>
  <p>このセクションの内容</p>
</section>
```

- コンテンツをテーマごとに分ける
- 見出し（`h2`など）とセットで使うのが基本
- HTML5 のセマンティックタグ

## 何ができるのか（重要まとめ）

1. 内容をテーマごとに分割

   ```html
   <section>
     <h2>プロフィール</h2>
     <p>自己紹介...</p>
   </section>

   <section>
     <h2>スキル</h2>
     <p>HTML / CSS / JS</p>
   </section>
   ```

1. 長いページを読みやすくする

   ```html
   <main>
     <section>
       <h2>概要</h2>
       <p>説明...</p>
     </section>

     <section>
       <h2>詳細</h2>
       <p>詳しい内容...</p>
     </section>
   </main>
   ```

1. サイトの構造をはっきりさせる

   ```html
   <section>
     <h2>サービス一覧</h2>
     <div>サービスA</div>
     <div>サービスB</div>
   </section>
   ```

1. セクションごとに header や footer を持てる

   ```html
   <section>
     <header>
       <h2>ニュース</h2>
     </header>

     <p>内容...</p>

     <footer>
       <p>更新日: 2026</p>
     </footer>
   </section>
   ```

## どこに使うのか

- 基本は main の中で使う

  ```html
  <main>
    <section>セクション1</section>
    <section>セクション2</section>
  </main>
  ```

## 重要な考え方（ここ大事）

- 「意味のあるまとまり」に使う
  - 見た目のレイアウトだけで使う ×
  - 内容が変わるところで使う ○
- 見出しとセットが基本

```html
<section>
  <h2>タイトル</h2>
</section>
```

→ 見出しがないと、section の意味が弱くなる

## div との違い

タグ 意味

- div ただの箱
- section 内容のまとまり

## article との違い

タグ 役割

- section テーマごとの区切り
- article 単体で完結する内容

例：

```html
<section>
  → 「ニュース一覧」
  <article>→ 「1つのニュース記事」</article>
</section>
```
