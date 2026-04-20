# Record<Keys, Type>

## 「決まったキー全部を持つオブジェクト型」を作る型

### サンプル

```ts
type Status = "idle" | "loading" | "success" | "error";

const messages: Record<Status, string> = {
  idle: "待機中",
  loading: "読み込み中",
  success: "成功",
  error: "失敗",
};
```

- キーのタイポミスを減らすことができる
- 指定したキーは全部必須
- キーを string にするのは微妙
- `Partial`でラップするのも微妙

## 値をユニオンにするべきではない理由

```ts
Record<"a" | "b", number | string>;
```

- typescript の型的にはキーも値もユニオンになるので、a は number、b は string にしたい → Record 不向き

## interface と Record の使い分け

### interface 的な思考

- interface =「このオブジェクトはこういう部品を持つ」
- interface =「形（構造）」

```ts
interface User {
  id: string;
  name: string;
  age: number;
}
```

**「1 人の User とは何か」**を表す

### Record 的な思考

- Record =「キー → 値 の対応表」
- Record =「表（対応表・辞書）」

### Q1. これは「1 つのモノ」？

```tsxt
ユーザー

設定

API レスポンス

フォームデータ
```

Yes → interface

### Q2. これは「キーで引く表」？

```tsxt
状態 → メッセージ

イベント → ハンドラ

ID → データ

enum → 設定値
```

Yes → Record
