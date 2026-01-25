# Yup と Zod の違い

> Yup と Zod の違いは
>
> 「何を中心に設計しているか」の違い

|        | Yup                  | Zod                       |
| ------ | -------------------- | ------------------------- |
| 中心   | フォーム入力         | TypeScript の型           |
| 主役   | 実行時バリデーション | 型 + 実行時バリデーション |
| 得意   | 条件付きフォーム     | API / 型自動化            |
| 自動化 | 苦手                 | 得意                      |

## 違い

### Yup

> 「入力された値が正しいか」をチェックするための道具

### Zod

> 「この形が正しい」という型を定義し、実行時にもそれを強制する道具

## 同じことを両方で書くとどう違うか

### 例：ユーザー入力

yup

```ts
const schema = yup.object({
  email: yup.string().email().required(),
  age: yup.number().min(18),
});
```

- 「email は必須」「18 歳以上」

---

zod

```ts
const UserSchema = z.object({
  email: z.string().email(),
  age: z.number().min(18),
});
```

- データ構造の定義
- 「User はこういう形」

```ts
type User = z.infer<typeof UserSchema>;
```

- 型が自然に取れる

---

### yup は型定義出来ないのか？

<u>A.できる</u>

```ts
// Yup
type User = yup.InferType<typeof schema>;

// Zod
type User = z.infer<typeof schema>;
```

---

### 決定的な違い

> その infer 結果が実行時の挙動と 100% 一致するか？

yup の場合、使用ケースによっては型が意図しないものになるケースがある

1.  When

    ```ts
    const schema = yup.object({
      role: yup.string().required(),
      adminCode: yup.string().when("role", {
        is: "admin",
        then: (s) => s.required(),
      }),
    });
    ```

    **実行時の意味**

    - role === "admin" → adminCode 必須
    - それ以外 → 不要

    **InferType した結果**

    ```ts
    type User = yup.InferType<typeof schema>;

    // 結果
    // {
    // role: string;
    // adminCode?: string; // ← 常に optional
    // }
    ```

2.  default

    ```ts
    const schema = yup.object({
      age: yup.number().default(18),
    });
    ```

    **実行時の意味**

    - age 未指定 → 18 が入る

    **InferType した結果**

    ```ts
    type User = {
      age?: number; // ← default が反映されない
    };
    ```

- yuo は条件が型に反映されない
- zod では起きにくい

## yup、zod の強み・弱み

### Yup の強み

① 条件付きフォームが書きやすい

```ts
yup.string().when("role", {
  is: "admin",
  then: (s) => s.required(),
});
```

- 意味 : role が admin のときだけ必須
- フォームあるあるをそのまま書ける

② フォームライブラリとの相性が良い

- Formik
- React Hook Form

古くからの実績あり

---

### Yup の弱み

① 型が信用できない

```ts
type User = yup.InferType<typeof schema>;
```

- optional / when / default が混ざると型が壊れる or 期待とズレる
- 実行時は通るのに TS では通らない現象が起こる

② 自動化がほぼ無理

- OpenAPI
- Swagger
- API クライアント生成

理由 : スキーマ = 型にならないため

---

### Zod の強み

① スキーマ = 型の真実

```ts
const UserSchema = z.object({
  id: z.number(),
  name: z.string(),
});

type User = z.infer<typeof UserSchema>;
```

- 型と実行時が 完全一致
- ズレが起きない

② API・自動化と相性が良すぎる

```
Zod Schema
↓
TypeScript 型
↓
OpenAPI
↓
Swagger
↓
フロント用型生成
```

- 1 つ定義すれば全部作れる

---

### Zod の弱み

① Yup の when が書きづらい

```ts
.superRefine((data, ctx) => {
if (data.role === "admin" && !data.code) {
ctx.addIssue({ path: ["code"], message: "required" });
}
});
```

- 書くことはできるが、宣言的じゃない
- フォーム用途だと可読性が落ちる
- 自動化したいなら Zod 一択

---

### Zod が強い理由

- スキーマが 静的
- 型として完全に決まる
- 条件依存が少ない

→ 機械が理解しやすい

### Yup が弱い理由

- when / transform が多い
- 実行時ロジック依存
- 型に落とせない

→ Swagger に変換できない

## 結論

> Yup は「人間の入力チェック」向き
>
> Zod は「型と自動化」向き

### Yup

- フォーム向け
- 条件付きが強い
- 型は弱い
- 自動化に不向き

### Zod

- TypeScript-first
- 型と実行時が一致
- API 設計向き
- Swagger と相性 ◎
