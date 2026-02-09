# 分割代入

### 配列から値を取り出して、あるいはオブジェクトからプロパティを取り出して別個の変数に代入することを可能にする JavaScript の構文のこと

## オブジェクト型の分割代入

```ts
const user = {
  name: "Taro",
  age: 25,
  job: "Engineer",
};

// 通常
const name = user.name;
const age = user.age;

// 分割代入
const { name, age } = user;

console.log(name); // "Taro"
console.log(age); // 25
```

## 配列の分割代入

```ts
const fruits = ["apple", "banana", "orange"];

// 通常
const first = fruits[0];
const second = fruits[1];

// 分割代入
const [first, second] = fruits;

console.log(first); // "apple"
console.log(second); // "banana"
```

## 応用テクニック

### オブジェクトの別名（リネーム）

```ts
const user = { name: "Taro", age: 25 };

const { name: userName } = user;
console.log(userName); // "Taro"
```

### ネストされたオブジェクトの分割

```ts
const user = {
  name: "Taro",
  address: {
    city: "Tokyo",
    zip: "123-4567",
  },
};

const {
  address: { city },
} = user;

console.log(city); // "Tokyo"
```

### 関数引数として使う

```ts
const greet = ({ name, age }) => {
  console.log(`Hello, ${name}. You are ${age} years old.`);
};

const user = { name: "Taro", age: 25 };
greet(user);
```
