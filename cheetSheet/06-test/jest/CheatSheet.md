# CheatSheet

過去に使用したサンプルの備忘録

1. [入力確認](#入力確認)
1. [関数呼出](#関数呼出)

---

## 入力確認

```ts
test("入力できること", async () => {
  // ユーザー操作の準備
  const user = userEvent.setup();

  // コンポーネントのレンダリング
  render(<TestComponent onSearch={jest.fn()} />);

  // 要素の取得
  const input = screen.getByRole("textbox");

  // 入力
  await user.type(input, "テスト");

  // 同値チェック
  expect(input).toHaveValue("テスト");
});
```

## 関数呼出

```ts
test("検索ボタンをクリックすると onSearch が呼ばれること", async () => {
  // ユーザー操作の準備
  const user = userEvent.setup();

  // モック関数の作成
  const onSearch = jest.fn();

  // コンポーネントのレンダリング
  render(<TestComponent onSearch={onSearch} />);

  // 要素の取得
  const button = screen.getByRole("button");

  // ボタン押下
  await user.click(button);

  // 呼ばれたことの確認
  expect(onSearch).toHaveBeenCalledTimes(1);
});
```
