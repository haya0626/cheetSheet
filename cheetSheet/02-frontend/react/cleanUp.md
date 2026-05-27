# useEffect でクリーンアップが必要な理由

`useEffect`の中で登録したものは、**コンポーネントが消えるとき**や**effect が再実行される前**に片付けないと、前の処理が残り続けるから。

つまり cleanup は、

- 古いイベントを消す
- 古いタイマーを止める
- 古い通信や購読を解除する

ために必要。

## 1. addEventListener の具体例

### NG

```jsx
useEffect(() => {
  const onResize = () => {
    console.log("resize");
  };

  window.addEventListener("resize", onResize);
}, []);
```

### 何が起きる？

このコンポーネントを表示  
→ `resize`イベント登録

その後コンポーネントを消す  
→ でも `resize`イベントは残る

もう一度表示  
→ また `resize`イベント登録

結果：

```text
resize
resize
resize
```

1 回しかリサイズしてないのに、過去の登録分まで全部動く。

---

### OK

```jsx
useEffect(() => {
  const onResize = () => {
    console.log("resize");
  };

  window.addEventListener("resize", onResize);

  return () => {
    window.removeEventListener("resize", onResize);
  };
}, []);
```

### cleanup がやっていること

- 最初に表示されたとき → 登録
- 消えるとき → 削除

だから増殖しない。

## 2. setInterval の具体例

### NG

```jsx
useEffect(() => {
  setInterval(() => {
    console.log("1秒ごと");
  }, 1000);
}, []);
```

### 何が起きる？

コンポーネントを閉じても、`setInterval`は止まらない。

つまり画面にはいないのに裏でずっと動く。

再表示するとさらにもう 1 本増える。

結果：

```text
1秒ごと
1秒ごと
1秒ごと
```

どんどん本数が増える。

### OK

```jsx
useEffect(() => {
  const intervalId = setInterval(() => {
    console.log("1秒ごと");
  }, 1000);

  return () => {
    clearInterval(intervalId);
  };
}, []);
```

### cleanup がやっていること

- タイマー開始
- コンポーネントが消えるときにタイマー停止

これをしないと、見えない場所で永遠に動く。

## 3. setTimeout の具体例

### NG

```jsx
useEffect(() => {
  setTimeout(() => {
    console.log("3秒後に実行");
  }, 3000);
}, []);
```

### 何が起きる？

3 秒経つ前に画面が閉じても、timeout は残る。

つまり、もう存在しない画面のための処理が後から動く。

### OK

```jsx
useEffect(() => {
  const timeoutId = setTimeout(() => {
    console.log("3秒後に実行");
  }, 3000);

  return () => {
    clearTimeout(timeoutId);
  };
}, []);
```

## 4. fetch の具体例

### NG

```jsx
useEffect(() => {
  fetch("/api/user")
    .then((res) => res.json())
    .then((data) => {
      setUser(data);
    });
}, []);
```

---

### 何が起きる？

通信中にコンポーネントが消えることがある。

でも通信完了後に `setUser(data)` が走ると、

- もう消えたコンポーネントに対して
- state 更新しようとする

という気持ち悪い状態になる。

### OK（AbortController）

```jsx
useEffect(() => {
  const controller = new AbortController();

  fetch("/api/user", { signal: controller.signal })
    .then((res) => res.json())
    .then((data) => {
      setUser(data);
    })
    .catch((error) => {
      if (error.name !== "AbortError") {
        console.error(error);
      }
    });

  return () => {
    controller.abort();
  };
}, []);
```

### cleanup がやっていること

- まだ終わってない通信を中断する
- 不要になった通信結果で更新しないようにする

## 5. 依存配列ありのときも cleanup が必要

### 例

```jsx
useEffect(() => {
  const onResize = () => {
    console.log("現在のuserId:", userId);
  };

  window.addEventListener("resize", onResize);

  return () => {
    window.removeEventListener("resize", onResize);
  };
}, [userId]);
```

---

### なぜ必要？

`userId`が変わるたびに effect は再実行される。

このとき cleanup がないと、

- 古い `userId` 用のイベント
- 新しい `userId` 用のイベント

が両方残る。

cleanup があると、

1. 古い effect を片付ける
2. 新しい effect を登録する

という順番になる。

## React での cleanup のタイミング

cleanup は主にこの 2 つで呼ばれる。

### 1. コンポーネントがアンマウントされるとき

画面から消えるときに片付ける。

### 2. effect が再実行される前

依存配列の値が変わったとき、前回分を先に片付けてから新しい effect を動かす。

## cleanup が必要なもの / 不要なもの

### 必要になりやすい

- addEventListener
- setInterval
- setTimeout
- fetch（中断したい場合）
- WebSocket
- 外部ライブラリの購読
- subscribe / unsubscribe 系

### 不要なことが多い

```jsx
useEffect(() => {
  document.title = "マイページ";
}, []);
```

これは「何かを登録して残す」処理ではないので cleanup は普通いらない。

## 感覚的な理解

`useEffect` は「何かを外の世界に登録する場所」。

だから登録したら、最後に解除が必要。

- イベント登録した → イベント解除
- タイマー開始した → タイマー停止
- 接続した → 切断
- 購読した → 購読解除

## まとめ

cleanup が必要なのは、effect の中で作ったものが**勝手に残り続ける**から。

放置すると、

- 多重登録
- メモリリーク
- 画面が消えた後も処理が動く
- 同じ処理が何回も走る

みたいなバグになる。

だから `useEffect` では

**「作ったら片付ける」**

をセットで考える。
