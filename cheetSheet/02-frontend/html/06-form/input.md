# Input

ユーザーからデータを入力してもらうための基本パーツ

## input タグとは

`input`は、ユーザーから値を入力・選択してもらうためのフォーム部品を作成する HTML 要素。

```html
<input type="text" />
```

一言で`input`といっても、`type`属性によって役割が大きく変わる。

例えば、

```html
<input type="text" />
```

なら文字入力欄。

```html
<input type="number" />
```

なら数値入力欄。

```html
<input type="checkbox" />
```

ならチェックボックス。

```html
<input type="radio" />
```

ならラジオボタン。

```html
<input type="file" />
```

ならファイル選択になる。

つまり`input`は、

```text
input
+
type属性
+
その他の属性
```

の組み合わせによって、さまざまな入力 UI を作る要素。

実務ではフォーム画面で非常によく使用する。

## input の基本的な使い方

最も基本的な文字入力欄。

```html
<input type="text" />
```

ただし実際のフォームでは、これだけで使うことは少ない。

通常は、

- `label`
- `id`
- `name`
- `type`

などを組み合わせる。

```html
<label for="user-name"> ユーザー名 </label>

<input id="user-name" name="userName" type="text" />
```

それぞれの役割は次の通り。

```text
label
↓
この入力欄が何なのかを表示

for="user-name"
↓
id="user-name" のinputと紐づける

id
↓
HTML上でinputを識別する

name
↓
フォーム送信時の項目名

type
↓
どんな入力欄なのかを指定
```

## type 属性

`type`は`input`で最も重要な属性。

```html
<input type="text" />
```

のように指定する。

`type`によって、

- 入力できる値
- ブラウザに表示される UI
- バリデーション
- スマートフォンのキーボード
- 使用できる属性

などが変わる。

### 実務でよく使う type

| type             | 用途                  |
| ---------------- | --------------------- |
| `text`           | 通常の文字入力        |
| `search`         | 検索キーワード        |
| `email`          | メールアドレス        |
| `password`       | パスワード            |
| `tel`            | 電話番号              |
| `url`            | URL                   |
| `number`         | 数値                  |
| `date`           | 日付                  |
| `time`           | 時刻                  |
| `datetime-local` | 日付＋時刻            |
| `checkbox`       | 複数選択・ON/OFF      |
| `radio`          | 複数候補から 1 つ選択 |
| `file`           | ファイル選択          |
| `hidden`         | 画面に表示しない値    |
| `range`          | スライダー            |
| `color`          | 色選択                |
| `submit`         | フォーム送信          |

実務では特に、

```text
text
email
password
tel
number
date
checkbox
radio
file
hidden
```

あたりをよく使う。

## text

通常の 1 行テキスト入力。

```html
<input type="text" name="userName" />
```

名前・タイトル・コードなど、一般的な文字列入力で使用する。

### 例

```html
<label for="user-name"> ユーザー名 </label>

<input id="user-name" name="userName" type="text" />
```

## search

検索キーワード入力用。

```html
<input type="search" name="keyword" />
```

例えば一覧画面。

```html
<form action="/users">
  <label for="keyword"> キーワード </label>

  <input id="keyword" name="keyword" type="search" />

  <button type="submit">検索</button>
</form>
```

見た目は`text`と近いが、ブラウザによって検索用 UI が追加される場合がある。

## email

メールアドレス入力。

```html
<input type="email" name="email" />
```

例えば、

```html
<label for="email"> メールアドレス </label>

<input id="email" name="email" type="email" autocomplete="email" required />
```

`email`を使用すると、ブラウザの入力チェックやスマートフォンのキーボードなどがメール入力向けになる。

単なる文字列だから、

```html
<input type="text" />
```

でも入力自体はできるが、意味に合った`type`を使用した方がよい。

## password

パスワード入力。

```html
<input type="password" name="password" />
```

入力文字が画面上で隠される。

```html
<label for="password"> パスワード </label>

<input
  id="password"
  name="password"
  type="password"
  autocomplete="current-password"
/>
```

新規パスワード登録なら、

```html
<input type="password" autocomplete="new-password" />
```

などを使う。

### 注意

`type="password"`は、

```text
画面上で文字を隠す
```

ためのもの。

通信内容そのものを暗号化する機能ではない。

パスワード送信には HTTPS が必要。

## tel

電話番号入力。

```html
<input type="tel" name="phoneNumber" />
```

電話番号は数字だけに見えるが、

```text
09012345678
```

のような値を計算することはない。

さらに、

```text
+81
-
()
```

などが入る場合もある。

そのため、

```html
<input type="number" />
```

ではなく、

```html
<input type="tel" />
```

を使用する。

# number

数値入力。

```html
<input type="number" name="age" />
```

範囲も指定できる。

```html
<input type="number" name="age" min="0" max="150" />
```

1 ずつ増減。

```html
<input type="number" step="1" />
```

0.5 刻み。

```html
<input type="number" step="0.5" />
```

### number の注意点

HTML 上では数値入力だが、JavaScript で、

```js
input.value;
```

を取得すると文字列。

例えば、

```html
<input id="age" type="number" />
```

```js
const input = document.querySelector("#age");

console.log(typeof input.value);
```

結果。

```text
string
```

数値として取得したい場合、

```js
input.valueAsNumber;
```

を使う方法もある。

React でも、

```tsx
onChange={(event) => {
  console.log(
    event.target.value
  );
}}
```

の`value`は基本的に文字列として扱う。

## date

日付入力。

```html
<input type="date" name="birthday" />
```

範囲指定。

```html
<input type="date" min="2026-01-01" max="2026-12-31" />
```

実際の表示 UI はブラウザ・OS によって異なる。

## checkbox

チェックボックス。

```html
<input type="checkbox" />
```

例えば利用規約。

```html
<label>
  <input type="checkbox" name="agree" value="yes" />

  利用規約に同意する
</label>
```

チェックされると、

```text
agree=yes
```

として送信される。

### checkbox の重要な注意点

**チェックされていない Checkbox は、通常 Form Data に含まれない。**

つまり、

```html
<input type="checkbox" name="agree" value="yes" />
```

が未選択だからといって、

```text
agree=false
```

が送られるわけではない。

```text
agree自体が送られない
```

という動きになる。

バックエンドとの API 設計では注意する。

## radio

複数候補から 1 つだけ選択する。

```html
<label>
  <input type="radio" name="plan" value="free" />

  Free
</label>

<label>
  <input type="radio" name="plan" value="premium" />

  Premium
</label>
```

重要なのは、

```html
name="plan"
```

を同じにすること。

同じ`name`を持つ Radio Button が 1 つのグループになる。

### NG

```html
<input type="radio" name="free" />

<input type="radio" name="premium" />
```

`name`が違うので両方選択できてしまう。

## file

ファイル選択。

```html
<input type="file" name="document" />
```

### 複数ファイル

```html
<input type="file" name="documents" multiple />
```

### 画像だけ

```html
<input type="file" accept="image/*" />
```

### PDF

```html
<input type="file" accept=".pdf,application/pdf" />
```

### accept 属性

ユーザーが選択するファイル形式のヒント。

```html
<input type="file" accept=".pdf" />
```

画像。

```html
<input type="file" accept="image/png,image/jpeg" />
```

または、

```html
<input type="file" accept="image/*" />
```

## accept の注意点

`accept`はセキュリティ機能ではない。

```text
accept=".pdf"
```

と指定したから、

```text
PDF以外が絶対に送信されない
```

わけではない。

クライアント側の制御は回避できる。

バックエンド側でも、

- MIME Type
- 拡張子
- File Signature
- File Size

などを検証する。

## hidden

画面には表示しない入力値。

```html
<input type="hidden" name="userId" value="123" />
```

ユーザー ID などをフォーム送信 Data に含める用途。

### 重要

Hidden だから秘密になるわけではない。

ブラウザの DevTools から簡単に確認・変更できる。

```html
<input type="hidden" name="role" value="admin" />
```

を、

```text
Userが変更できないから安全
```

とは考えない。

Server 側で必ず権限チェックする。

## name 属性

フォーム送信時の項目名。

```html
<input name="userName" value="Taro" />
```

送信すると、

```text
userName=Taro
```

になる。

つまり、

```text
name
→ Key

value
→ Value
```

という関係。

### name を書き忘れた場合

```html
<input id="user-name" type="text" />
```

Native Form Submission では通常この値は送信されない。

`id`と`name`は別物。

```text
id
→ HTML上で要素を識別

name
→ Form DataのKey
```

## id 属性

HTML 文書内で要素を識別する。

```html
<input id="user-name" />
```

`label`との紐付け。

```html
<label for="user-name"> ユーザー名 </label>

<input id="user-name" />
```

```text
label for
      ↓
input id
```

が対応する。

## value 属性

Input の値。

```html
<input type="text" value="Taro" />
```

Form 送信すると、

```text
name=value
```

として使用される。

## placeholder

入力例・短い補助情報。

```html
<input type="text" placeholder="例: 田中 太郎" />
```

### アンチパターン

```html
<input placeholder="名前" />
```

だけで項目名を表現する。

入力すると Placeholder が消えるので、何の入力欄か分からなくなる。

基本は、

```html
<label for="name"> 名前 </label>

<input id="name" placeholder="例: 田中 太郎" />
```

とする。

## required

必須入力にする。

```html
<input type="text" required />
```

Form Submit 時に Browser の Constraint Validation 対象になる。

`required`は Boolean Attribute。

```html
<input required />
```

属性が存在することで true になる。

そのため、

```html
<input required="false" />
```

としても、

```text
required = true
```

として扱われる。

無効化するなら属性自体を付けない。

これは、

- `disabled`
- `readonly`
- `multiple`
- `checked`

などでも重要な考え方。

## minlength / maxlength

文字数制限。

```html
<input type="text" minlength="2" maxlength="20" />
```

```text
minlength
→ 最小文字数

maxlength
→ 最大文字数
```

## min / max

数値・日付などの範囲。

```html
<input type="number" min="0" max="100" />
```

```text
0〜100
```

Date。

```html
<input type="date" min="2026-01-01" max="2026-12-31" />
```

## step

入力値の刻み幅。

```html
<input type="number" min="0" max="10" step="0.5" />
```

例えば、

```text
0
0.5
1
1.5
2
```

のような値。

`number` / `range`では`step`の既定値が 1 で、`min`との組み合わせによって有効な値の刻みが決まる点に注意する。

## pattern

正規表現による入力 Pattern。

```html
<input type="text" pattern="[A-Za-z0-9]+" />
```

英数字だけを許可する例。

### pattern を使いすぎない

複雑な Business Rule を、

```html
pattern="ものすごく長い正規表現..."
```

だけで管理すると読みづらい。

```text
簡単な入力形式
→ HTML pattern

複雑なBusiness Rule
→ Application Validation
```

と分ける。

さらに Server 側 Validation も必要。

## disabled

入力欄を無効化。

```html
<input type="text" disabled />
```

`disabled`になると一般に、

- ユーザーが編集できない
- Focus できない
- Form 送信 Data に含まれない
- Constraint Validation 対象外

となる。

## readonly

値を編集できなくする。

```html
<input type="text" value="ABC123" readonly />
```

`disabled`との違いが非常に重要。

|                    | readonly | disabled |
| ------------------ | -------- | -------- |
| 編集               | ×        | ×        |
| Focus              | ○        | ×        |
| Form 送信          | ○        | ×        |
| Control として機能 | ○        | ×        |

読み取り専用 Control は Focus 可能で Form 送信にも含まれる一方、disabled Control は Focus できず、通常 Form 送信にも含まれない。

### 実務でありがちなバグ

ユーザー ID を編集させたくない。

```html
<input name="userId" value="123" disabled />
```

画面には、

```text
123
```

と表示されている。

しかし Submit すると、

```text
userIdが送られていない
```

となる。

値を送信したいが編集だけ禁止したいなら、

```html
<input name="userId" value="123" readonly />
```

を検討する。

## autocomplete

ブラウザへ「この入力欄にはどんな情報が入るか」を伝え、自動入力を支援する属性。`input`だけでなく`textarea`、`select`、`form`でも利用できる。

名前。

```html
<input type="text" autocomplete="name" />
```

姓。

```html
<input autocomplete="family-name" />
```

名。

```html
<input autocomplete="given-name" />
```

メール。

```html
<input type="email" autocomplete="email" />
```

電話番号。

```html
<input type="tel" autocomplete="tel" />
```

郵便番号。

```html
<input autocomplete="postal-code" />
```

現在の Password。

```html
<input type="password" autocomplete="current-password" />
```

新しい Password。

```html
<input type="password" autocomplete="new-password" />
```

### autocomplete を何でも off にしない

```html
<input autocomplete="off" />
```

は使えるが、Password Manager や住所自動入力などユーザーの便利な機能を壊す場合がある。

原則として、

```text
何のFieldなのか
```

を正しい Token で Browser へ伝える。

## inputmode

スマートフォンなどの Software Keyboard に対する入力モードのヒント。

```html
<input type="text" inputmode="numeric" />
```

数字 Keyboard を出したい場合など。

### number との違い

```text
type="number"
→ Dataの意味が数値

inputmode="numeric"
→ KeyboardのHint
```

例えば郵便番号。

```html
<input type="text" inputmode="numeric" autocomplete="postal-code" />
```

郵便番号は、

```text
5400001
```

のように数字で構成されるが、加算・減算する「数値」ではない。

このような Data に`number`を使うべきかは考える。

同様に、

- 電話番号
- 郵便番号
- 会員番号
- 商品コード

などは識別子。

## checked

Checkbox / Radio の初期選択状態。

```html
<input type="checkbox" checked />
```

Boolean Attribute。

## multiple

複数値を許可。

File。

```html
<input type="file" multiple />
```

Email でも、

```html
<input type="email" multiple />
```

とすると複数 Email を受け付けられる。

## list

`datalist`と紐付ける。

```html
<label for="framework"> Framework </label>

<input id="framework" name="framework" list="framework-list" />

<datalist id="framework-list">
  <option value="React"></option>
  <option value="Vue"></option>
  <option value="Angular"></option>
</datalist>
```

```text
select
→ 候補から選択

datalist
→ 候補は出すが自由入力可能
```

## form 属性

Input を Form の外に置いても Form と関連付けできる。

```html
<form id="search-form" action="/users"></form>

<input type="search" name="keyword" form="search-form" />

<button type="submit" form="search-form">検索</button>
```

Layout の都合で Form Control が Form 要素の直接の子にならない場合に使える。

## React で input を使う場合

### Controlled Component

```tsx
const [name, setName] = useState("");

return (
  <input
    type="text"
    value={name}
    onChange={(event) => {
      setName(event.target.value);
    }}
  />
);
```

```text
input
↓
onChange
↓
setName
↓
state更新
↓
valueへ反映
```

React State を Source of Truth にする。

---

### Checkbox

```tsx
const [enabled, setEnabled] = useState(false);

return (
  <input
    type="checkbox"
    checked={enabled}
    onChange={(event) => {
      setEnabled(event.target.checked);
    }}
  />
);
```

Checkbox では、

```tsx
event.target.value;
```

ではなく、

```tsx
event.target.checked;
```

を使う。

---

## よくあるミス

### 1. 全部 text にする

```html
<input type="text" />
```

だけで全部作らない。

Email なら、

```html
type="email"
```

Telephone なら、

```html
type="tel"
```

適切な Type を使う。

---

### 2. Label がない

NG。

```html
<input placeholder="ユーザー名" />
```

OK。

```html
<label for="user-name"> ユーザー名 </label>

<input id="user-name" />
```

---

### 3. disabled なのに値が送られると思う

```text
disabled
→ Form Dataに通常含まれない
```

かなりハマりやすい。

---

### 4. accept だけで File Validation する

```html
accept=".pdf"
```

は User Experience 上の Hint。

Security Validation は Server 側。

---

### 5. number を数字なら何でも使う

```text
年齢
金額
数量
→ number候補

電話番号
郵便番号
社員番号
商品コード
→ 文字列/識別子として考える
```

---

### 6. Client Validation だけを信用する

```html
required pattern min max
```

などは便利だが、Browser 側なので変更可能。

```text
HTML Validation
→ User Experience

Backend Validation
→ Dataの正当性・Security
```

両方必要。

---

## 実務で input を作るときのチェック

```text
① 何を入力するData？
        ↓
② 適切なtypeは？
        ↓
③ labelはある？
        ↓
④ nameは必要？
        ↓
⑤ required？
        ↓
⑥ 文字数・範囲制限は？
        ↓
⑦ autocompleteは？
        ↓
⑧ disabled / readonlyどっち？
        ↓
⑨ BackendでもValidationしている？
```

## 覚えておくこと

`input`で一番大事なのは、

```text
type
```

を Data の意味に合わせること。

次に、

```text
name
id
value
required
disabled
readonly
autocomplete
```

を理解する。

特に実務で忘れやすいのは、

```text
disabled
→ 送信されない

readonly
→ 送信される

checkbox未選択
→ falseではなく
   項目自体が送信されない

numberのvalue
→ JavaScriptでは文字列として扱う場面がある

accept
→ Security Validationではない
```

あたり。

`input`は単なる入力欄ではなく、

**入力する Data の意味を Browser へ伝え、入力・Validation・自動補完・Form 送信を制御するための要素**

として理解しておく。
