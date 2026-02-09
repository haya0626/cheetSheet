# null 合体演算子

### 初期値が null or undefined の時に初期値を割り振る

## 基本構文

```ts
let result = a ?? b;
```

a が null or undefined なら b を返す

それ以外は a を返すそれ以外（つまり、`0`, `''`, `false`など）はそのまま `a`

### 使用例イメージ

代入時にもし null なら何か代入させたい

```ts
const params = new URL(document.location).searchParams;
document.title = params.get("username") ?? "Default Title";
```

参考 : https://qiita.com/azukiazusa/items/b91046e9f1d618c67d7f
