# article

「それ単体で完結するコンテンツ」を表す要素

## article タグとは

```html
<article>
  <h2>記事タイトル</h2>
  <p>本文...</p>
</article>
```

- 独立して成立するコンテンツを表す
- ブログ記事・ニュース・投稿などに最適
- HTML5 のセマンティックタグ

## 何ができるのか

1. ブログ記事を表現

   ```html
   <article>
     <h2>今日の出来事</h2>
     <p>本文...</p>
   </article>
   ```

1. ニュース記事

   ```html
   <article>
     <h2>新サービス発表</h2>
     <p>詳細...</p>
   </article>
   ```

1. SNS 投稿・コメント

   ```html
   <article>
     <p>この投稿いいね！</p>
   </article>
   ```

1. 複数記事の一覧

   ```html
   <section>
     <h2>ブログ一覧</h2>

     <article>
       <h3>記事1</h3>
     </article>

     <article>
       <h3>記事2</h3>
     </article>
   </section>
   ```

1. 記事ごとのヘッダー・フッターも持てる

   ```html
   <article>
     <header>
       <h2>記事タイトル</h2>
       <p>投稿日: 2026-04-20</p>
     </header>

     <p>本文...</p>

     <footer>
       <p>著者: 山田</p>
     </footer>
   </article>
   ```

## どこに使うのか

基本は main や section の中

```html
<main>
  <article>記事</article>
</main>
```

または

```html
<section>
  <article>記事1</article>
  <article>記事2</article>
</section>
```

## 重要な考え方

- 「単体で成立するか？」が判断基準
  - OK：ブログ記事・ニュース・投稿
  - NG：ただのレイアウトや装飾

## section との違い

タグ 意味

- section テーマごとのまとまり
- article 独立したコンテンツ

例：

```html
<section>
  <h2>ニュース</h2>

  <article>ニュース1</article>
  <article>ニュース2</article>
</section>
```

## div との違い

タグ 意味

- div ただの箱
- article 独立したコンテンツ
- ネスト（入れ子）もできる

  ```html
  <article>
    <h2>ブログ記事</h2>

    <article>
      <p>コメント</p>
    </article>
  </article>
  ```

  記事の中に「コメント記事」なども表現できる
