# axios の Promise

axios 宣言時のデータの型（戻り値）について

## axios は何を返している？

```ts
axios.get("/users");
```

### 実態

```ts
Promise<AxiosResponse>;
```

- 通信が終わったら AxiosResponse が返ってくる Promise
- 将来、data や status を持つレスポンスが手に入る

### つまり

```ts
const p = axios.get("/users");

p.then((res) => {
  console.log(res.data);
});
```

- 見えてないが、中で promise の型を返している
- Promise は消えてない。書かなくてよくなっただけ。

## async / await 　使用時

```ts
const response = await axios.get("/users");
console.log(response.data);
```

- axios.get → Promise<AxiosResponse>
- await を付けると中身の AxiosResponse が取り出される

```ts
const response: AxiosResponse;
```

## 補足 : AxiosResponse の全体像

```ts
interface AxiosResponse<T = any> {
  data: T;
  status: number;
  statusText: string;
  headers: any;
  config: AxiosRequestConfig;
  request?: any;
}
```

### 各プロパティ

① data（一番よく使う）

```ts
res.data;
```

- サーバーが返した レスポンスボディ
- JSON の中身
- T によって型付けされる

```ts
axios.get<User[]>("/users");
res.data; // User[]
```

② status（HTTP ステータスコード）

```ts
res.status;
```

- 200
- 201
- 404
- 500 など

```ts
if (res.status === 200) {
  // 成功
}
```

- HTTP レベルの制御に使う

③ statusText（人間向けステータス）
res.statusText

- "OK"
- "Created"
- "Not Found"など

実務ではあまり使われない（ログやデバッグ用）

④ headers（レスポンスヘッダ）

```ts
res.headers;
```

- 認証トークン
- レートリミット情報
- ページネーション情報など

```ts
res.headers["x-total-count"];
```

API 仕様によっては重要

⑤ config（リクエスト設定）

```ts
res.config;
```

- URL
- method
- headers
- timeout など

このレスポンスは、どんなリクエストの結果か

⑥ request（低レベル情報）

```ts
res.request;
```

- ブラウザ or Node の内部リクエスト

通常は触らない
