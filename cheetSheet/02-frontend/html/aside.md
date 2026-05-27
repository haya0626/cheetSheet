# aside

メインコンテンツの補足・関連情報をまとめるための要素

## aside>タグとは

```html
<aside>
  <p>関連記事はこちら</p>
</aside>
```

- メイン内容の補助・関連情報を表示する
- サイドバーに使われることが多い
- HTML5 のセマンティックタグ

## 何ができるのか

aside タグでできること

1. サイドバー（定番）

   ```html
   <aside>
     <h3>人気記事</h3>
     <ul>
       <li>記事1</li>
       <li>記事2</li>
     </ul>
   </aside>
   ```

1. 関連記事リンク

   ```html
   <aside>
     <h3>関連記事</h3>
     <a href="#">おすすめ記事</a>
   </aside>
   ```

1. 補足説明（記事の中でも OK）

   ```html
   <article>
     <p>本文...</p>

     <aside>
       <p>※補足：この用語は〜</p>
     </aside>
   </article>
   ```

## どこに使うのか

- メインの横（よくある）

  ```html
  <main>
    <article>記事</article>
  </main>

  <aside>サイドバー</aside>
  ```

- main の中

  ```html
  <main>
    <article>本文</article>
    <aside>関連情報</aside>
  </main>
  ```

- 記事の中の補足

  ```html
  <article>
    <p>内容...</p>
    <aside>補足</aside>
  </article>
  ```

## 重要な考え方

- 「主役ではない情報」に使う

  - 関連記事・広告・補足
  - 本文そのもの

- メインとの関係がある内容
- 完全に無関係な内容は NG

## section との違い

タグ 意味

- section メイン内容の区切り
- aside 補足・サイド情報

## div との違い

タグ 意味

- div ただの箱
- aside 補足情報
