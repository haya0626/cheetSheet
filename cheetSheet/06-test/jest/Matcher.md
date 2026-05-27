# Matcher とは

expect で受け取った値を検証する関数のこと

# 目次

1. [ユーザー操作（クリック、入力など）の準備を行う](#ユーザー操作の準備を行う)
1. [DOM 要素を取得する](#dom-要素を取得する)
1. [現在の DOM に存在するかを検証する](#現在の-dom-に存在するかを検証する)
1. [フォーム要素の value を検証する](#フォーム要素の-value-を検証する)

# ユーザー操作の準備を行う

ユーザー操作を実行するための「ユーザーインスタンス」を作るコード

```ts
const user = userEvent.setup();
```

## userEvent メソッド

1. click

   ```ts
   await user.click(screen.getByRole("button", { name: "Submit" }));
   ```

1. type

   ```ts
   await user.type(input, "hello");
   ```

1. keyboard
   ```ts
   await user.keyboard("{Enter}");
   ```
1. clear
   ```ts
   await user.clear(toInput);
   ```

# DOM 要素を取得する

render された画面（DOM）から要素を検索するための API

## screen の取得メソッド

1.  getByRole

    ```ts
    screen.getByRole("button");
    ```

    | role     | 対応     |
    | -------- | -------- |
    | button   | button   |
    | textbox  | input    |
    | checkbox | checkbox |
    | link     | a タグ   |

1.  getByText

    ```ts
    screen.getByText("ログイン");
    ```

    - 見出し
    - メッセージ
    - ボタン文字

1.  getByLabelText

    ```ts
    // ラベル付きのinputを取得
    screen.getByLabelText("Email");
    ```

    ```html
    <label>Email</label> <input />
    ```

1.  getByPlaceholderText

    ```ts
    screen.getByPlaceholderText("Email");
    ```

    ```html
    <input placeholder="Email" />
    ```

文字を取得するイベントの使い分け

- **getByText**...要素がないと`TestingLibraryElementError`がでる
- **queryByText**...要素がないと`null`がでる
- **findByText**...API 通信で後から表示される場合に使う

# 現在の DOM に存在するかを検証する

要素が DOM 内に存在するかを確認する Matcher

## toBeInTheDocument

```ts
render(<Login />);

expect(screen.getByText("Login")).toBeInTheDocument();
```

`getByText`は要素がないと エラーになるため、要素が存在しないことを確認したい場合は`queryByText`を使う

```ts
expect(screen.queryByText("Error")).not.toBeInTheDocument();
```

# フォーム要素の value を検証する

フォーム要素の value 値を検証する

## toHaveValue

```ts
test("入力できる", async () => {
  const user = userEvent.setup();

  render(<Login />);

  const input = screen.getByRole("textbox");

  await user.type(input, "test@test.com");

  expect(input).toHaveValue("test@test.com");
});
```

対応している要素

- input
- textarea
- select

## checkbox / radio の場合

これらは value 値ではなく、 checked を見るので`toBeChecked()`を使う

```ts
expect(screen.getByRole("checkbox")).toBeChecked();
```
