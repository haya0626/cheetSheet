# useCallBack

### 関数のメモ化（キャッシュ）を行うためのフック

関数が変更されず、依存配列の値が変更されない限り、同じ関数インスタンスを返すことで、不要な再レンダリングを防ぐ。特に関数プロップを持つ子コンポーネントの再レンダリングを制御する際に有用である。

```typescript
const sample = useCallback(
  () => {
    // 関数本体
  },
  [dependency1, dependency2, ...]
);
```

## 実務レベルでの主な使用用途

### 1. React.memo された子に props として関数を渡すとき

子コンポーネントが React.memo で「props が同じなら再レンダーしない」設計のとき、関数 props が毎回新参照だと、子が毎回再レンダーする。

```typescript
const Child = React.memo(({ onSave }: { onSave: () => void }) => {
  /* ... */
  return <button onClick={onSave}>Save</button>;
});

function Parent() {
  const [count, setCount] = useState(0);

  // これだと Parent が更新されるたび Child も更新されやすい
  const onSave = useCallback(() => {
    /* 保存処理 */
  }, []);

  return (
    <>
      <Child onSave={onSave} />
      <button onClick={() => setCount((x) => x + 1)}>inc</button>
    </>
  );
}
```

✅ 基準：

- 子が React.memo / memo 相当で最適化されている
- 関数 props の参照安定が「再レンダー削減」に直結する
- 子の再レンダーが重い / 数が多い

### 2.関数が、後で何らかのフックの依存値として使用されるとき

JavaScript では関数は再定義されるたびに別物として扱われるため、useEffect の依存値に直接関数を渡すと、毎回エフェクトが発火してしまうことがある

```typescript
const fetchData = useCallback(() => {
  // データを取得する処理
}, []);

useEffect(() => {
  fetchData(); // fetchDataが変わらない限り再実行されない
}, [fetchData]);
```

関数に切り出さなくても、関数内で使われる dps を useEfect の dps に書けばそもそも不要である

1. 同じ処理を複数箇所で使う
1. 依存が多くなりすぎるのを避けたい
1. あえて effect の意味を読みやすくしたい

なら使用する価値はある。

結論

> **「この effect は、この“処理の定義”が変わったときに動く」  
> という設計意図の表現次第**

## アンチパターン

### アンチ 1：とりあえず全部 useCallback

- useCallback 自体にもコスト（依存比較・保持）がある
- 可読性が下がる
- 依存配列ミスが増える（バグ源）

✅ 対処：まず素直に書いて、必要になったら入れる

### アンチ 2：依存配列が毎回変わる（結局毎回新関数）

```typescript
const onSave = useCallback(() => doSave(form), [form]); // form が毎回新参照なら意味薄い
```

- deps が頻繁に変わるなら参照は安定しない

✅ 対処：状態の持ち方を見直す（不要なオブジェクト生成を減らす / 正規化 / useMemo / useRef など）

### アンチ 3：useCallback を “最適化” だと思って使う

- useCallback は「子のレンダーを減らす」には効くことがあるけど、関数本体の処理が速くなるわけではない
- メモリを消費するので必要なときに使うぐらいでよい

[When to useMemo and useCallback](https://kentcdodds.com/blog/usememo-and-usecallback) の著者である Kent C. Dodds より引用

> React is VERY fast and there are so many things I can think of for you to do with your time that would be better than optimizing things like this.
>
> ほとんどの場合、不要なレンダリングの最適化は気にしなくていいです。React は非常に速いし、このようなものを最適化するよりも、他に時間を割いてやるべきことはたくさんあります

PayPal で働いた 3 年間、そしてそれよりも長い React 歴の間もそいういう最適化が必要な瞬間はなかったらしい

要するに useCallback を使うべき瞬間はそんなに多くない
