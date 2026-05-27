# ChangeEvent

`ChangeEvent` は、**input / textarea / select などの値が変更されたときに発生するイベントの型**。

TypeScript ではこれを使うことで、

- `e.target.value` の型が分かる
- 要素ごとの扱い方が明確になる
- フォーム処理を安全に書ける

## 基本

```tsx
import { ChangeEvent } from "react";

const handleChange = (e: ChangeEvent<HTMLInputElement>) => {
  console.log(e.target.value);
};
```

```tsx
<input onChange={handleChange} />
```

## まず押さえるべきこと

`ChangeEvent` は単体で使うというより、基本はこう使う。

```tsx
ChangeEvent<HTMLInputElement>;
ChangeEvent<HTMLTextAreaElement>;
ChangeEvent<HTMLSelectElement>;
```

つまり、

- `ChangeEvent` = 変更イベント
- `<...>` = どの HTML 要素で起きた変更か

を表している。

## 何のために使うのか

TypeScript ではイベント引数 `e` の型が分からないと、以下のようなコードで型安全が落ちる。

```tsx
const handleChange = (e) => {
  setValue(e.target.value);
};
```

これを `ChangeEvent` で明示すると、

```tsx
const handleChange = (e: ChangeEvent<HTMLInputElement>) => {
  setValue(e.target.value);
};
```

- `value` が存在すること
- `value` の型が文字列であること

が分かるようになる。

## 基本構文

```tsx
const handleChange = (e: ChangeEvent<HTMLInputElement>) => {
  const value = e.target.value;
};
```

## 一番よく使う対象

### input

```tsx
const handleChange = (e: ChangeEvent<HTMLInputElement>) => {
  console.log(e.target.value);
};
```

### textarea

```tsx
const handleChange = (e: ChangeEvent<HTMLTextAreaElement>) => {
  console.log(e.target.value);
};
```

### select

```tsx
const handleChange = (e: ChangeEvent<HTMLSelectElement>) => {
  console.log(e.target.value);
};
```

## 入力要素ごとの具体例

## 1. text input

```tsx
import { ChangeEvent, useState } from "react";

function App() {
  const [name, setName] = useState("");

  const handleChange = (e: ChangeEvent<HTMLInputElement>) => {
    setName(e.target.value);
  };

  return <input type="text" value={name} onChange={handleChange} />;
}
```

### ポイント

- `value` は文字列
- 一番よく使う形

## 2. textarea

```tsx
import { ChangeEvent, useState } from "react";

function App() {
  const [message, setMessage] = useState("");

  const handleChange = (e: ChangeEvent<HTMLTextAreaElement>) => {
    setMessage(e.target.value);
  };

  return <textarea value={message} onChange={handleChange} />;
}
```

### ポイント

- `textarea` でも `value` は文字列
- `HTMLInputElement` ではなく `HTMLTextAreaElement`

## 3. select

```tsx
import { ChangeEvent, useState } from "react";

function App() {
  const [fruit, setFruit] = useState("apple");

  const handleChange = (e: ChangeEvent<HTMLSelectElement>) => {
    setFruit(e.target.value);
  };

  return (
    <select value={fruit} onChange={handleChange}>
      <option value="apple">りんご</option>
      <option value="orange">みかん</option>
    </select>
  );
}
```

### ポイント

- `select` も `value` は文字列
- `HTMLSelectElement` を使う

## 4. checkbox

```tsx
import { ChangeEvent, useState } from "react";

function App() {
  const [checked, setChecked] = useState(false);

  const handleChange = (e: ChangeEvent<HTMLInputElement>) => {
    setChecked(e.target.checked);
  };

  return <input type="checkbox" checked={checked} onChange={handleChange} />;
}
```

### ポイント

- checkbox は `value` より `checked` を使う
- `checked` は boolean

## 5. radio

```tsx
import { ChangeEvent, useState } from "react";

function App() {
  const [gender, setGender] = useState("");

  const handleChange = (e: ChangeEvent<HTMLInputElement>) => {
    setGender(e.target.value);
  };

  return (
    <>
      <label>
        <input
          type="radio"
          name="gender"
          value="male"
          onChange={handleChange}
        />
        男性
      </label>

      <label>
        <input
          type="radio"
          name="gender"
          value="female"
          onChange={handleChange}
        />
        女性
      </label>
    </>
  );
}
```

### ポイント

- radio は `value` を使う
- ただし選択状態そのものを見るなら `checked` も使える

## 6. number input

```tsx
import { ChangeEvent, useState } from "react";

function App() {
  const [age, setAge] = useState("");

  const handleChange = (e: ChangeEvent<HTMLInputElement>) => {
    setAge(e.target.value);
  };

  return <input type="number" value={age} onChange={handleChange} />;
}
```

### 重要ポイント

`type="number"` でも `e.target.value` は **string**。

```tsx
const handleChange = (e: ChangeEvent<HTMLInputElement>) => {
  console.log(typeof e.target.value); // string
};
```

数値にしたいなら自分で変換する。

```tsx
const handleChange = (e: ChangeEvent<HTMLInputElement>) => {
  const value = Number(e.target.value);
  setAge(value);
};
```

## 7. file input

```tsx
import { ChangeEvent } from "react";

function App() {
  const handleChange = (e: ChangeEvent<HTMLInputElement>) => {
    const files = e.target.files;
    if (!files || files.length === 0) return;

    console.log(files[0]);
  };

  return <input type="file" onChange={handleChange} />;
}
```

### ポイント

- `value` ではなく `files` を使う
- `files` は `FileList | null`
- null チェックが必要

## `e.target.value` が使える要素 / 使えない要素

### 使うことが多い

- `input[type="text"]`
- `input[type="email"]`
- `input[type="password"]`
- `textarea`
- `select`
- `radio`

### 別のプロパティを見ることが多い

- checkbox → `e.target.checked`
- file → `e.target.files`

## `target` と `currentTarget` の違い

`ChangeEvent` では普通 `e.target` を使うことが多いが、違いは知っておくべき。

### target

実際にイベントが発生した要素

### currentTarget

イベントハンドラが付いている要素

React のフォームでは多くの場合ほぼ同じ感覚で使えるが、概念としては別。

## 基本的な書き方パターン

## 1. 関数を分ける

```tsx
const handleChange = (e: ChangeEvent<HTMLInputElement>) => {
  setValue(e.target.value);
};

return <input onChange={handleChange} />;
```

## 2. インラインで書く

```tsx
<input
  onChange={(e: ChangeEvent<HTMLInputElement>) => {
    setValue(e.target.value);
  }}
/>
```

### 注意

React の JSX では型推論が効くので、インラインでは型注釈を書かなくても動くことが多い。

```tsx
<input
  onChange={(e) => {
    setValue(e.target.value);
  }}
/>
```

ただし、外に関数を切り出すなら型を書く方が分かりやすい。

## React が型推論してくれるケース

```tsx
<input
  onChange={(e) => {
    console.log(e.target.value);
  }}
/>
```

この場合、`e` は React が `ChangeEvent<HTMLInputElement>` と推論してくれることが多い。

## でも明示した方がいい場面

- 関数を外に切り出すとき
- 共通ハンドラにするとき
- 型の意味をコードで明確にしたいとき

## 型を import しない書き方

```tsx
const handleChange = (e: React.ChangeEvent<HTMLInputElement>) => {
  setValue(e.target.value);
};
```

これでも OK。

## import して書く方法

```tsx
import { ChangeEvent } from "react";

const handleChange = (e: ChangeEvent<HTMLInputElement>) => {
  setValue(e.target.value);
};
```

## どっちがいいか

どちらでもよいが、統一するのが大事。

### import する派

```tsx
import { ChangeEvent } from "react";
```

- 短く書ける

### React.ChangeEvent 派

```tsx
React.ChangeEvent<HTMLInputElement>;
```

- どこ由来の型か分かりやすい

## 共通ハンドラで複数要素を扱う

### input と textarea を両方扱う例

```tsx
import { ChangeEvent } from "react";

const handleChange = (
  e: ChangeEvent<HTMLInputElement | HTMLTextAreaElement>
) => {
  console.log(e.target.value);
};
```

```tsx
<>
  <input onChange={handleChange} />
  <textarea onChange={handleChange} />
</>
```

### ポイント

Union 型で複数の要素を扱える。

## name 属性と組み合わせる実践例

```tsx
import { ChangeEvent, useState } from "react";

function App() {
  const [form, setForm] = useState({
    name: "",
    email: "",
  });

  const handleChange = (e: ChangeEvent<HTMLInputElement>) => {
    const { name, value } = e.target;

    setForm((prev) => ({
      ...prev,
      [name]: value,
    }));
  };

  return (
    <>
      <input name="name" value={form.name} onChange={handleChange} />
      <input name="email" value={form.email} onChange={handleChange} />
    </>
  );
}
```

### ポイント

- `name` 属性を使って共通化できる
- 実務でかなりよく使う

## checkbox を共通ハンドラで扱うときの注意

```tsx
const handleChange = (e: ChangeEvent<HTMLInputElement>) => {
  const { type, value, checked } = e.target;

  const nextValue = type === "checkbox" ? checked : value;
  console.log(nextValue);
};
```

### ポイント

checkbox は `value` ではなく `checked` が本体になることが多い。

## よくあるエラーと原因

## 1. 型を書いていない

```tsx
const handleChange = (e) => {
  setValue(e.target.value);
};
```

### 問題

- `noImplicitAny` が有効だと怒られる
- 型安全が下がる

### 改善

```tsx
const handleChange = (e: ChangeEvent<HTMLInputElement>) => {
  setValue(e.target.value);
};
```

## 2. 要素型を間違える

```tsx
const handleChange = (e: ChangeEvent<HTMLDivElement>) => {
  console.log(e.target.value);
};
```

### 問題

`div` には通常 `value` がない。

## 3. checkbox で value を見てしまう

```tsx
const handleChange = (e: ChangeEvent<HTMLInputElement>) => {
  setChecked(e.target.value);
};
```

### 問題

checkbox の本体は普通 `checked`。

### 改善

```tsx
const handleChange = (e: ChangeEvent<HTMLInputElement>) => {
  setChecked(e.target.checked);
};
```

## 4. file input で null を考慮しない

```tsx
const handleChange = (e: ChangeEvent<HTMLInputElement>) => {
  console.log(e.target.files[0]);
};
```

### 問題

`files` は null の可能性がある。

### 改善

```tsx
const handleChange = (e: ChangeEvent<HTMLInputElement>) => {
  const files = e.target.files;
  if (!files) return;

  console.log(files[0]);
};
```

## `onChange` と `ChangeEvent` の関係

React では `onChange` に渡されるイベント引数の型が `ChangeEvent`。

つまり、

```tsx
<input onChange={handleChange} />
```

の `handleChange` の引数に入ってくる `e` が `ChangeEvent<HTMLInputElement>` になる。

## DOM の change イベントとの違い

React の `onChange` は、素の DOM より少し感覚が違う。

### 素の DOM

- `change` は「値が確定したとき」に発火することが多い

### React

- `input[type="text"]` では入力中にもかなり細かく反応する

つまり、React の `onChange` は実務上ほぼ「入力中の監視」として使える。

## SyntheticEvent との関係

React のイベントはネイティブ DOM イベントそのものではなく、React がラップしたイベント。

`ChangeEvent` もその一種。

ざっくり言うと、

- ブラウザのイベントを
- React が扱いやすく包んでいる

と考えればよい。

## 実務での定番パターン集

## 1. 単一 input

```tsx
const [value, setValue] = useState("");

const handleChange = (e: React.ChangeEvent<HTMLInputElement>) => {
  setValue(e.target.value);
};
```

## 2. textarea

```tsx
const [message, setMessage] = useState("");

const handleChange = (e: React.ChangeEvent<HTMLTextAreaElement>) => {
  setMessage(e.target.value);
};
```

## 3. select

```tsx
const [category, setCategory] = useState("");

const handleChange = (e: React.ChangeEvent<HTMLSelectElement>) => {
  setCategory(e.target.value);
};
```

## 4. checkbox

```tsx
const [isChecked, setIsChecked] = useState(false);

const handleChange = (e: React.ChangeEvent<HTMLInputElement>) => {
  setIsChecked(e.target.checked);
};
```

## 5. 共通フォーム更新

```tsx
const [form, setForm] = useState({
  name: "",
  email: "",
});

const handleChange = (e: React.ChangeEvent<HTMLInputElement>) => {
  const { name, value } = e.target;

  setForm((prev) => ({
    ...prev,
    [name]: value,
  }));
};
```

## 覚え方

### text / textarea / select

```tsx
e.target.value;
```

### checkbox

```tsx
e.target.checked;
```

### file

```tsx
e.target.files;
```

## まとめ

- `ChangeEvent` は入力変更イベントの型
- 基本は `ChangeEvent<HTMLInputElement>` の形で使う
- textarea は `HTMLTextAreaElement`
- select は `HTMLSelectElement`
- checkbox は `checked`
- file は `files`
- React + TypeScript のフォーム処理では必須級

## 一言で覚える

**「フォーム入力の e の型」**
