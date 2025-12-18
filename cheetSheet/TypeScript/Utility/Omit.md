# Omit<T, keys>

## Omit…省略する

### 「型 T から、キー K を削除した新しい型を作る」

### サンプル

```tsx
type User = {
  id: number;
  name: string;
  email: string;
};
```

id を消したい

```tsx
type UserWithoutId = Omit<User, "id">;
```

結果

```tsx
type UserWithoutId = {
  name: string;
  email: string;
};
```

---

### 複数キーを消す場合

<span style="color: yellow;">ユニオンで指定する<span>

```tsx
type User = {
  id: number;
  name: string;
  email: string;
  createdAt: Date;
};
```

```tsx
type PublicUser = Omit<User, "email" | "createdAt">;
```

結果

```tsx
type PublicUser = {
  id: number;
  name: string;
};
```

---

### 存在しないキーを Omitできるので対策すること

```tsx
type User = {
  id: number;
  name: string;
};
```

```tsx
type X = Omit<User, "password">;
```

**エラーにならない！！**

<span style="color: yellow;">**対策…typeに対象のキーのみ対象となるように型を設定する**<span>

```tsx
type StrictOmit<T, K extends keyof T> = Omit<T, K>;
```
```
type X = StrictOmit<User, "password">; // ❌ エラー
```