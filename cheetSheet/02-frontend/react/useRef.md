# useRef

### 参照を保持するための hook

```ts
// 変数名 = useRef(初期値)
const ref = useRef(initialValue);
```

**useRef**はとてもシンプルな設計で、

Ref オブジェクト(`{current: initialValue}`) を生成して、その値をメモ化しているだけです。

値の取り出し、変更は下記のように書きます。

```ts
// 設定した値を取り出す
const value = ref.current;

// 値を変更する
const ref.current = 2;
```

`ref.current`の値を変更させているだけなので

**再レンダリングが走らない**。

これが、`useRef`の最大の特徴。

DOM を参照して、要素の幅を調べることも可能

参考元 : https://qiita.com/cheez921/items/9a5659f4b94662b4fd1e
