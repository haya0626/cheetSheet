# select

プルダウン（選択式入力）を作るための要素

## select タグとは

```html
<select name="fruit">
  <option value="apple">りんご</option>
  <option value="orange">みかん</option>
</select>
```

- 複数の選択肢から 1 つ（または複数）選べる入力
- 中に`option`タグを入れて使う
- フォーム送信時は`name`に対して選択値が送られる

## select タグでできること

1. プルダウン選択

   ```html
   <select name="fruit">
     <option value="apple">りんご</option>
     <option value="orange">みかん</option>
   </select>
   ```

1. 初期選択（selected）

   ```html
   <select name="fruit">
     <option value="apple" selected>りんご</option>
     <option value="orange">みかん</option>
   </select>
   ```

1. 複数選択（multiple）

   ```html
   <select name="fruits" multiple>
     <option value="apple">りんご</option>
     <option value="orange">みかん</option>
   </select>
   ```

1. 選択肢のグループ化（optgroup）

   ```html
   <select name="food">
     <optgroup label="果物">
       <option value="apple">りんご</option>
     </optgroup>
     <optgroup label="野菜">
       <option value="carrot">にんじん</option>
     </optgroup>
   </select>
   ```

1. プレースホルダー風（ダミー選択）

   ```html
   <select name="fruit">
     <option value="" disabled selected>選択してください</option>
     <option value="apple">りんご</option>
   </select>
   ```

## どこに使うのか

- フォームの選択入力

  ```html
  <form>
    <select name="country">
      <option value="jp">日本</option>
    </select>
  </form>
  ```

## 重要な考え方（ここ大事）

- option とセットで使う（必須）

  ```html
  <select>
    <option>OK</option>
  </select>
  ```

- value が送信される

  ```html
  <option value="apple">りんご</option>
  ```

  → サーバーには「apple」が送られる

- multiple のときは配列扱い

  ```html
  <select name="fruits" multiple></select>
  ```

## div との違い

タグ 意味

- div ただの箱
- select 選択入力

## よく使う属性（超重要）

### ● name

```html
<select name="fruit"></select>
```

### ● multiple

```html
<select multiple></select>
```

→ 複数選択可能

### ● size

```html
<select size="5"></select>
```

→ 表示行数

### ● disabled

```html
<select disabled></select>
```

### ● required

```html
<select required></select>
```

### ● class

```html
<select class="form-select"></select>
```

## option 側の重要属性

### ● value（最重要）

```html
<option value="apple">りんご</option>
```

### ● selected

```html
<option selected>選択済み</option>
```

### ● disabled

```html
<option disabled>選択不可</option>
```
