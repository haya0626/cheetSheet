# option

select の中で選択肢を定義する要素

## option タグとは

```html
<select name="fruit">
  <option value="apple">りんご</option>
</select>
```

- `select`タグの中で使う選択肢
- 表示テキストと送信値を持つ
- 単体では使えない（重要）

## option タグでできること

1. 選択肢を作る

   ```html
   <select>
     <option value="apple">りんご</option>
     <option value="orange">みかん</option>
   </select>
   ```

1. 初期選択（selected）

   ```html
   <select>
     <option value="apple" selected>りんご</option>
   </select>
   ```

1. 選択不可（disabled）

   ```html
   <select>
     <option disabled>選べません</option>
   </select>
   ```

1. プレースホルダー的な使い方

   ```html
   <select>
     <option value="" disabled selected>選択してください</option>
     <option value="apple">りんご</option>
   </select>
   ```

1. グループ内で使用（optgroup）

   ```html
   <select>
     <optgroup label="果物">
       <option value="apple">りんご</option>
     </optgroup>
   </select>
   ```

## どこに使うのか

- select の中で使う（必須）

  ```html
  <select>
    <option>項目</option>
  </select>
  ```

## 重要な考え方（ここ大事）

- 単体では使えない

  ```html
  <option>NG</option>
  ```

- value が送信される（超重要）

  ```html
  <option value="apple">りんご</option>
  ```

  → サーバーには「apple」が送られる

- 表示文字と値は別

  ```html
  表示：りんご 値：apple
  ```

## select との関係

タグ 役割

- select 入力全体
- option 選択肢

## よく使う属性（超重要）

### ● value（最重要）

```html
<option value="apple">りんご</option>
```

### ● selected

```html
<option selected>選択状態</option>
```

### ● disabled

```html
<option disabled>選択不可</option>
```

### ● label（補助表示）

```html
<option value="apple" label="りんご"></option>
```
