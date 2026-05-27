# form

ユーザーから入力されたデータを送信するための要素

## form タグとは

```html
<form action="/send" method="post">
  <input type="text" name="username" />
</form>
```

- 入力データをまとめて送信するためのタグ
- input や textarea などを内包する
- action と method で送信先と方法を指定

## form タグでできること

1. データをサーバーに送信

   ```html
   <form action="/send" method="post">
     <input type="text" name="name" />
     <button type="submit">送信</button>
   </form>
   ```

1. 複数の入力をまとめる

   ```html
   <form>
     <input type="text" name="name" />
     <input type="email" name="email" />
   </form>
   ```

1. GET 送信（URL に付く）

   ```html
   <form method="get">
     <input type="text" name="keyword" />
   </form>
   ```

1. POST 送信（データを隠す）

   ```html
   <form method="post">
     <input type="text" name="password" />
   </form>
   ```

1. ファイル送信

   ```html
   <form enctype="multipart/form-data">
     <input type="file" />
   </form>
   ```

## どこに使うのか

- ログインフォーム

  ```html
  <form>
    <input type="text" placeholder="ユーザー名" />
    <input type="password" placeholder="パスワード" />
  </form>
  ```

- 検索フォーム

  ```html
  <form>
    <input type="text" name="q" />
  </form>
  ```

## 重要な考え方

- 入力は form の中に入れる

  ```html
  <form>
    <input />
  </form>
  ```

- name がないと送信されない（超重要）

  ```html
  <input name="username" />
  ```

- 送信は submit で行う

  ```html
  <button type="submit">送信</button>
  ```

## よく使う属性（超重要）

### ● action（送信先）

```html
<form action="/api/send"></form>
```

→ データを送る URL

### ● method（送信方法）

```html
<form method="post"></form>
```

- get → URL に付く（検索など）
- post → 本文で送信（ログインなど）

### ● enctype（データ形式）

```html
<form enctype="multipart/form-data"></form>
```

→ ファイル送信時に必要

### ● target（開き方）

```html
<form target="_blank"></form>
```

→ 新しいタブで送信結果表示

### ● autocomplete（入力補助）

```html
<form autocomplete="on"></form>
```

## よく使うタグ（セットで理解）

- input → 入力欄
- textarea → 複数行入力
- select → プルダウン
- button → ボタン
- label → ラベル

例：

```html
<form>
  <label>名前</label>
  <input type="text" name="name" />

  <label>コメント</label>
  <textarea name="comment"></textarea>

  <button type="submit">送信</button>
</form>
```
