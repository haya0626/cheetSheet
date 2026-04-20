# textarea

複数行のテキスト入力を行うための要素

## textarea タグとは

```html
<textarea name="message"></textarea>
```

- 複数行のテキストを入力できる
- input（text）と違い改行が可能
- 開始タグと終了タグが必要（空タグではない）

## textarea タグでできること

1. 複数行入力

   ```html
   <textarea></textarea>
   ```

1. 初期値を設定

   ```html
   <textarea>初期テキスト</textarea>
   ```

1. サイズ指定（行・列）

   ```html
   <textarea rows="5" cols="30"></textarea>
   ```

1. プレースホルダー表示

   ```html
   <textarea placeholder="入力してください"></textarea>
   ```

1. フォームと組み合わせる

   ```html
   <form>
     <textarea name="comment"></textarea>
   </form>
   ```

## どこに使うのか

- コメント欄

  ```html
  <textarea name="comment"></textarea>
  ```

- お問い合わせフォーム

  ```html
  <textarea name="message"></textarea>
  ```

## 重要な考え方

- input との違い（超重要）

  - input → 1 行入力
  - textarea → 複数行入力

- value 属性は使わない

  ```html
  <textarea>ここに書く</textarea>
  ```

  → 中身が値になる

- name がないと送信されない

  ```html
  <textarea name="message"></textarea>
  ```

## よく使う属性

### ● name

```html
<textarea name="message"></textarea>
```

### ● placeholder

```html
<textarea placeholder="入力してください"></textarea>
```

### ● rows / cols

```html
<textarea rows="5" cols="30"></textarea>
```

### ● maxlength

```html
<textarea maxlength="100"></textarea>
```

### ● required

```html
<textarea required></textarea>
```

### ● disabled

```html
<textarea disabled></textarea>
```

### ● readonly

```html
<textarea readonly></textarea>
```
