# プログラミングにおけるド・モルガンの法則

## 覚え方

### 否定が外に出たら、AND と OR が入れ替わる

```tsx
!(A && B) === (!A || !B);
```

条件式に否定が入るとわかりにくいので、括弧の外に!を使わないようにするためにこの考え方が生きる

## なぜ否定が外に出たら、AND と OR が入れ替わるのか

### 「全部 OK」を否定すると「どれか NG」になるから

**そのため、 `&&` を否定すると `||` に変わる**

日本語のイメージ

1. 「全部そろって OK」(&&)
2. 「それじゃない」(&&を否定)
3. 「じゃあどれか欠けてるよね？」(OR になる!!!!!)

### 例：合格の条件

先生の言葉

「宿題やってて && 名前書いてたら合格」

不合格は？

- 宿題やってない
- または
- 名前書いてない

→ **OR になるのは当たり前**

## サンプルコード

### 1. **認証チェック**

❌ ダメな例

```tsx
if (!(user && user.isActive && !user.isBanned)) {
  throw new Error("Unauthorized");
}
```

✅ よい例

```tsx
if (!user || !user.isActive || user.isBanned) {
  throw new Error("Unauthorized");
}
```

### 2. **early return**

❌ ダメな例

```tsx
if (user.isLoggedIn) {
  if (user.hasProfile) {
    render();
  }
}
```

✅ よい例

```tsx
if (!user.isLoggedIn || !user.hasProfile) {
  return;
}

render();
```

### 3. **権限チェック**

❌ ダメな例

```tsx
if (!(isAdmin || isOwner)) {
  deny();
}
```

✅ よい例

```tsx
if (!isAdmin && !isOwner) {
  deny();
}
```
