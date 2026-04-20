# コールバック関数

関数を「引数として渡して、後で実行する仕組み」

## コールバック関数とは

```js
function greet(name, callback) {
  console.log("こんにちは " + name);
  callback();
}

greet("山田", () => {
  console.log("処理完了");
});
```

- 関数を別の関数に渡す
- 必要なタイミングで実行される
- 非同期処理でよく使われる

## 基本イメージ

```text
関数A → 関数Bを渡す → 後で関数Bを実行
```

## 何ができるのか

1. 処理のあとに別の処理を実行

   ```js
   function process(callback) {
     console.log("処理中...");
     callback();
   }

   process(() => {
     console.log("完了");
   });
   ```

1. 配列操作（超よく使う）

   ```js
   [1, 2, 3].map((num) => num * 2);
   ```

   `num => num * 2` がコールバック

1. イベント処理

   ```js
   button.addEventListener("click", () => {
     console.log("クリックされた");
   });
   ```

1. 非同期処理

   ```js
   setTimeout(() => {
     console.log("1秒後");
   }, 1000);
   ```

## 同期 vs 非同期

### ● 同期（順番通り）

```js
console.log("A");
console.log("B");
```

### ● 非同期（あとで実行）

```js
console.log("A");

setTimeout(() => {
  console.log("B");
}, 1000);
```

B は後で実行される

## コールバックが必要な理由

**「いつ終わるかわからない処理」に対応するため**

例：

- API 通信
- タイマー
- ユーザー操作

## React での例

```js
<button onClick={() => handleClick()}>
```

クリックされたときに実行される

## よくあるパターン

### ● 名前付き関数

```js
function done() {
  console.log("完了");
}

process(done);
```

### ● 無名関数（よく使う）

```js
process(() => {
  console.log("完了");
});
```

## 重要な考え方

- コールバックは「あとで実行される関数」
- 自分で呼ぶとは限らない
- 他の処理がタイミングを決める
