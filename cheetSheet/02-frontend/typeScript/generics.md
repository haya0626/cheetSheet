# Generics

型をあとから決められる型

## Generics がない場合（問題）

```ts
function echo(value: string): string {
  return value;
}
```

string しか使えない

```ts
echo("Hello"); // OK
echo(123); // NG
```

## any で逃げる

```ts
function echo(value: any): any {
  return value;
}
```

型安全がなくなる

## Generics を使う

```ts
function echo<T>(value: T): T {
  return value;
}
```

## 何が起きてる？

```ts
echo("Hello"); // T = string
echo(123); // T = number
```

呼び出し時に型が決まる

## 超イメージ

```text
function echo<T>(value: T)
```

T = 「型の箱」

```text
echo("Hello") → T = string
echo(123)     → T = number
```

## 実務で一番多いパターン

### 配列の 1 つ目を取る

```ts
function first<T>(arr: T[]): T {
  return arr[0];
}
```

```ts
const a = first([1, 2, 3]); // number
const b = first(["a", "b", "c"]); // string
```

型が自動で変わる

## Generics の本質

**「同じ形の処理を、どんな型でも使えるようにする」**

## Generics なしだと

```ts
function toArray(value: any): any[] {
  return [value];
}
```

型が崩壊

## 複数の型（現場でよく使う）

```ts
function pair<T, U>(a: T, b: U): [T, U] {
  return [a, b];
}
```

```ts
pair("age", 20);
// [string, number]
```

## React での具体例

```ts
const [name, setName] = useState<string>("");
```

これも Generics

```text
useState<T>()
```

型をあとから指定してる

## よくある疑問

### Q. T って何？

ただの名前（何でも OK）

```ts
function test<A>(value: A): A {}
```

## よくあるミス

### Generics を使わず any

```ts
function test(value: any): any {}
```

型チェックできない

### 無理に Generics 使う

普通の型でいい場面も多い

## 重要な考え方

- Generics は「型の再利用」
- 同じ処理をいろんな型で使うためのもの
- any の代わりに使う
- 「型をあとから入れてください」という設計で使う
