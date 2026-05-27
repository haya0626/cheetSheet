# interface

### オブジェクトが満たすべき“形（shape）”を宣言するための仕組み

- 実行時の値ではない
- 計算もしない
- 構造だけを表す

## 1. interface が生まれた目的（思想）

TypeScript は 構造的型付け（Structural Typing） の言語です。

```ts
interface User {
  name: string;
}
```

これは

「User という名前の型を作る」

というより

「name: string を持つオブジェクトは User として扱える」

```ts
interface User {
  name: string;
}

interface Person {
  name: string;
}

const x: User = { name: "Alice" };
const y: Person = x; // OK
```

名前付けしてるわけじゃないので上記でも正常に動く

## 2. interface が「契約」と呼ばれる理由

```ts
interface Config {
  url: string;
  timeout: number;
}
```

この interface の意味としては

- 実装を縛らない
- 振る舞いも縛らない

`「最低限この形を満たせ」`という約束だけ

```ts
function start(config: Config) {
  // config はこの shape を持つと保証される
}
```

interface は呼び出し側と実装側の合意

## 3. interface でできること

### optional（?）

```ts
interface User {
  name: string;
  age?: number;
}
```

これは

```ts
{
  name: string;
  age: number | undefined;
}
```

とほぼ同義

### readonly

```ts
interface Point {
  readonly x: number;
  readonly y: number;
}
```

- 代入を禁止
- 実行時の freeze ではない
- 型レベルの制約のみ

### 関数型 interface

```ts
interface Formatter {
  (value: string): string;
}

const upper: Formatter = (v) => v.toUpperCase();
```

### 動的キー

```ts
type sample = "A" | "B" | "C";

interface Dictionary {
  [key: sample]: number;
}
```

- キーは sample 型
- 値は number のオブジェクト

### interface の継承（extends）

```ts
interface Animal {
  name: string;
}

interface Dog extends Animal {
  bark(): void;
}

// Dog の中身
{
  name: string;
  bark(): void;
}
```

複数継承も可能

## 4. interface が向いている場面

- オブジェクトの shape
- public API
- props / state
- 設定オブジェクト
- class の契約
- 将来拡張される前提の型
