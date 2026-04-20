# interface と type の違い

各詳細は[interface](./interface.md)と[type]()を参照してください

## 1.宣言と代入

`interface`...型に名前をつける

`type`...無名で作られた型に参照のため別名をを与える

```ts
interface Book {
  title: string;
  pages: number;
}

type Book = {
  title: string;
  pages: number;
};
```

## 2.定義できる型の種類

`interface`...オブジェクトとクラスの型だけ定義

`type`...他の型も参照できる

```ts
type Color = "白" | "黒" | "赤" | "緑";

let color: Color = "白";
color = "青"; //Type '"青"' is not assignable to type 'Color'.
```

## 3.拡張の可否

`interface`...拡張できる

`type`...拡張できない

```ts
interface User {
  name: string;
}

interface User {
  level: number;
}

const user: User = {
  name: "apple",
  level: 0,
};

const user2: User = {
  name: "banana",
}; //Property 'level' is missing in type '{ name: string; }' but required in type 'User'
```

## 使用例

interface を使うべき場面

- オブジェクトの型定義
- public API / ライブラリ境界
- class に implements させる
- 将来拡張されそう
- React の props / state（特に公開コンポーネント）

---

type を使うべき場面

- Union / Intersection
- enum 代替
- 型演算・ジェネリクス
- 関数型
- 一時的・局所的な型
