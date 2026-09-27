# label

入力欄（input など）に説明を付けるための要素

## label タグとは

```html
<label>名前</label> <input type="text" />
```

- 入力フィールドの説明（ラベル）を付けるタグ
- クリックすると対応する入力欄にフォーカスされる
- アクセシビリティに非常に重要

## label タグでできること

1. 入力欄の説明を表示

   ```html
   <label>名前</label> <input type="text" />
   ```

1. クリックで入力欄にフォーカス

   ```html
   <label for="name">名前</label> <input id="name" type="text" />
   ```

1. 入力欄を内包する（別書き不要）

   ```html
   <label>
     名前
     <input type="text" />
   </label>
   ```

1. チェックボックスと組み合わせる

   ```html
   <label>
     <input type="checkbox" />
     同意する
   </label>
   ```

1. ラジオボタンと組み合わせる

   ```html
   <label>
     <input type="radio" name="gender" />
     男性
   </label>
   ```

## どこに使うのか

- フォーム入力の説明

  ```html
  <label for="email">メール</label> <input id="email" type="email" />
  ```

## 重要な考え方（ここ大事）

- input と必ず関連付ける（超重要）

  ```html
  <label for="name">名前</label> <input id="name" />
  ```

- for と id をセットで使う

  ```html
  for="name" ← label id="name" ← input
  ```

- または内包する

  ```html
  <label>
    名前
    <input />
  </label>
  ```

- UX が大幅に良くなる

  - クリック範囲が広がる
  - 操作しやすくなる

## div との違い

タグ 意味

- div ただの箱
- label 入力の説明＋関連付け

## よく使う属性

### ● for

```html
<label for="name">名前</label>
```

→ 対応する input の id を指定

### ● class

```html
<label class="form-label">名前</label>
```

### ● id

```html
<label id="label-name">名前</label>
```
