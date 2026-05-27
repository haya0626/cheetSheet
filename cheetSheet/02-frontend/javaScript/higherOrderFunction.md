# 高階関数

高階関数 = 関数を扱う関数

## 高階関数とは

```js
function run(callback) {
  callback();
}

run(() => {
  console.log("実行");
});
```

- 関数を引数として受け取る
- または関数を返す
- コールバック関数とセットで使われる

## ① 関数を引数に取る（コールバック）

```js
function greet(name, callback) {
  console.log("こんにちは " + name);
  callback();
}

greet("山田", () => {
  console.log("完了");
});
```

greet は高階関数

## ② 関数を返す

```js
function createMultiplier(x) {
  return function (y) {
    return x * y;
  };
}

const double = createMultiplier(2);
console.log(double(5)); // 10
```

関数を生成できる

## よく使う高階関数（超重要）

### ● map

```js
[1, 2, 3].map((n) => n * 2);
```

### ● filter

```js
[1, 2, 3].filter((n) => n > 1);
```

### ● forEach

```js
[1, 2, 3].forEach((n) => console.log(n));
```

全部高階関数

## コールバックとの関係

- コールバック → 渡す関数
- 高階関数 → 受け取る側

## React での例

```js
items.map((item) => <div>{item}</div>);
```

map（高階関数）＋ コールバック

## 何が嬉しいのか

### ● 処理を外から渡せる

```js
function process(fn) {
  fn();
}
```

柔軟性が上がる

### ● 再利用しやすい

同じ関数で違う処理を実行できる

### ● 抽象化できる

ロジックを共通化できる
