# オブジェクトのコピー

- 代入（=）はコピーではない

  - 参照（同じものを指す）

- 本当のコピーは別の方法が必要

## 基本（超重要）

```js
const a = { name: "山田" };
const b = a;

b.name = "田中";

console.log(a.name); // "田中"
```

a と b は同じオブジェクト

## なぜこうなる？

オブジェクトは「参照型」

```text
a → メモリ
b → 同じメモリ
```

## シャローコピー（浅いコピー）

1 階層だけコピー

### ● スプレッド構文

```js
const a = { name: "山田" };
const b = { ...a };
```

### ● Object.assign

```js
const b = Object.assign({}, a);
```

これで別オブジェクトになる

## シャローコピーの問題

```js
const a = {
  user: { name: "山田" },
};

const b = { ...a };

b.user.name = "田中";

console.log(a.user.name); // "田中"
```

ネストはコピーされない

## ディープコピー（深いコピー）

ネストも全部コピー

### ● JSON でコピー（簡易）

```js
const b = JSON.parse(JSON.stringify(a));
```

デメリット

- 関数消える
- undefined 消える
- Date 壊れる

### ● structuredClone（おすすめ）

```js
const b = structuredClone(a);
```

最新の安全な方法

## React での重要ポイント

```js
setState({ ...state, name: "田中" });
```

**必ずコピーしてから変更**

## なぜ重要？

直接変更するとバグる

```js
state.name = "田中"; // NG
```

React が変更を検知できない

## 重要な考え方

- オブジェクトは参照型
- コピーしないと同じものを触る
- ネストは特に注意
