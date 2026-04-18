# Input

ユーザーからデータを入力してもらうための基本パーツ

## input タグとは

```html
<input type="text" name="username" />
```

- 単体で使う「空タグ（終了タグなし）」
- type 属性で何を入力させるかを決める
- name でサーバーに送るデータ名を指定

## input タグでできること

1. テキスト入力 名前・住所・コメントなど

   ```html
   <input type="text" />
   ```

1. パスワード入力（伏せ字）

   ```html
   <input type="password" />
   ```

1. 数値入力（範囲制限も可能）

   ```html
   <input type="number" min="0" max="100" />
   ```

1. チェックボックス（複数選択）

   ```html
   <input type="checkbox" name="hobby" value="game" />
   ```

1. ラジオボタン（1 つだけ選択）

   ```html
   <input type="radio" name="gender" value="male" />
   ```

1. 日付・時間入力

   ```html
   <input type="date" /> <input type="time" />
   ```

1. ファイルアップロード

   ```html
   <input type="file" />
   ```

1. ボタン（送信など）

   ```html
   <input type="submit" value="送信" /> <input type="button" value="クリック" />
   ```

1. スライダー（範囲選択）

   ```html
   <input type="range" min="0" max="100" />
   ```

1. 色選択

   ```html
   <input type="color" />
   ```

## よく使う属性

● `placeholder（ヒント表示）`

```html
<input type="text" placeholder="名前を入力" />
```

● `required（必須入力）`

```html
<input type="text" required />
```

● `value（初期値）`

```html
<input type="text" value="山田" />
```

● `disabled（無効化）`

```html
<input type="text" disabled />
```

● `readonly（編集不可）`

```html
<input type="text" readonly />
```

● `maxlength（文字数制限）`

```html
<input type="text" maxlength="10" />
```
