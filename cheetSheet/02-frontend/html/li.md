# li

リストの各項目を表す要素

## li タグとは

```html
<ul>
  <li>項目1</li>
  <li>項目2</li>
</ul>
```

- リストの「1 つ 1 つの項目」を表すタグ
- `ul`や`ol`の中で使う
- 単体では使えない（重要）

## 何ができるのか（重要まとめ）

1. 箇条書きの項目を作る

   ```html
   <ul>
     <li>りんご</li>
     <li>みかん</li>
   </ul>
   ```

1. 番号付きリストの項目

   ```html
   <ol>
     <li>手順1</li>
     <li>手順2</li>
   </ol>
   ```

1. リンクと組み合わせる（ナビ）

   ```html
   <ul>
     <li><a href="#">Home</a></li>
     <li><a href="#">About</a></li>
   </ul>
   ```

1. 入れ子（ネスト）構造

   ```html
   <ul>
     <li>
       親
       <ul>
         <li>子</li>
       </ul>
     </li>
   </ul>
   ```

1. カードや UI のベース

   ```html
   <ul>
     <li>商品A</li>
     <li>商品B</li>
   </ul>
   ```

## どこに使うのか

- ul / ol の中で使う（必須）

  ```html
  <ul>
    <li>項目</li>
  </ul>
  ```

## 重要な考え方

- 単体では使えない

  ```html
  <li>NG</li>
  ```

- ul / ol の中で使う

  ```html
  <ul>
    <li>OK</li>
  </ul>
  ```

- 「項目」という意味で使う
  - レイアウト目的だけで使う ×
  - リストの 1 要素として使う ○

## ul / ol との関係

タグ 役割

- ul / ol リスト全体
- li 各項目

## div との違い

タグ 意味

- div ただの箱
- li リストの項目

## よく使う属性

### ● class（最重要）

```html
<li class="active">現在ページ</li>
```

### ● value（ol で使用）

```html
<ol>
  <li value="5">5番目から</li>
</ol>
```

### ● id

```html
<li id="item1">項目</li>
```
