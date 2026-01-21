# type

### 型を“計算・合成・変換した結果”に名前をつける仕組み

- 単なる別名ではない
- 型の演算結果を保持する箱
- 一度決めたら 固定
- 型エイリアスともいう

型エイリアスとは

> 「既存の型（または型の計算結果）に
> 読みやすい名前を付けただけのもの」

## 1. type が生まれた理由（思想）

TypeScript には 2 種類のニーズがある

形（shape）を宣言したい → `interface`

型を組み合わせて作りたい → `type`

```ts
type Result = A | B;
```

これは `「A または B という型の集合」`

## 2. type は「名前付き型計算」

```ts
type User = {
  id: number;
  name: string;
};
```

これは一見 interface と同じだが、本質は

`「このオブジェクト型という“結果”に User という名前を付けた」`
→ 型の式を書いて、その出来上がった型に名前を付ける構文

`interface`
→ 「こういう形のものを User と呼ぶ」

`type`
→ 「この型を User という名前で使う」

## 3. type の最大の特徴：Union（超重要）

```ts
type Status = "idle" | "loading" | "success" | "error";
```

## 4. Intersection（型の合成）

```ts
type A = { a: number };
type B = { b: number };

type C = A & B;
```

結果

```ts
{
  a: number;
  b: number;
}
```

## 5. type は「固定」される

```ts
type User = {
  name: string;
};

type User = {
  age: number;
};
// ❌ Duplicate identifier
```

再宣言不可

## 6. type が向いている場面（実務）

- Union / Intersection
- enum 代替
- 型演算
- 条件付きロジック
- 内部実装用の型
- 一時的な型
