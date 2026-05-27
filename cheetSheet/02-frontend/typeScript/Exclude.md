# Exclude<T, U>

## Exclude…除外する

### 「型 T から、型 U に当てはまるものを取り除く」

### サンプル

```TypeScript
type A = "a" | "b" | "c";
```

```TypeScript
type B = Exclude<A, "b">;
```

結果

```TypeScript
type B = "a" | "c";
```

---

### 複数消す場合

```TypeScript
type A = "a" | "b" | "c" | "d";
```

```TypeScript
type B = Exclude<A, "b" | "d">;
```

結果

```TypeScript
type B = "a" | "c";
```

---

### 数字・プリミティブでも同じ

```TypeScript
type Status = 200 | 400 | 401 | 500;
```

```TypeScript
type SuccessStatus = Exclude<Status, 400 | 401 | 500>;
```

結果

```TypeScript
type SuccessStatus = 200;
```
