# RESTful API

REST（Representational State Transfer）は、Web のアーキテクチャスタイルの一つです。

そして、REST の原則に沿って設計された API を「RESTful API」と呼びます。その最大の特徴は、「リソース」 という考え方に基づいている点です。

すべての情報やデータ（例：ユーザー、商品、投稿）を「リソース」として捉え、それぞれに一意の URL（Uniform Resource Locator）を割り当てます。クライアントは、この URL に対して HTTP メソッド（GET, POST など）を使ってリクエストを送ることで、リソースを操作（取得、作成、更新、削除）します。

## 7 つの設計原則

### 原則 1: リソース指向の URL 設計

API のエンドポイント（URL）は、リソースを表現する「名詞」であるべき。

動詞（アクション）を URL に含めるのは避けましょう。

- 良い例: /users, /users/123, /users/123/posts
- 悪い例: /getUsers, /createUser, /listPostsFromUser

リソースの階層構造を URL で表現することで、API の構造が直感的になる

---

### 原則 2: 適切な HTTP メソッドの使用

リソースに対する操作は、HTTP メソッドで表現します。

これにより、URL はリソースの場所を示し、メソッドがそのリソースに対して何をするかを示す、という役割分担が明確になります。

- GET: リソースの取得
- POST: リソースの新規作成
- PUT / PATCH: リソースの更新（PUT は全体置換、PATCH は部分更新）
- DELETE: リソースの削除

このマッピングに従うことで、誰にとっても予測可能な API となる

---

### 原則 3: ステータスコードの正しい使い分け

API のレスポンスには、リクエストの結果を示す HTTP ステータスコードを正しく含めることが重要です。

これにより、クライアントは処理が成功したのか、失敗したのか、そしてその理由を機械的に判断できます。

[ステータスコードの詳細](./HttpStatusCode.md)

---

### 原則 4: バージョニング戦略

API は一度公開すると、簡単には変更できません。

しかし、ビジネスの成長とともに機能追加や仕様変更は避けられません。

そこで、API にバージョン情報を持たせることで、既存のクライアントに影響を与えずに API を更新できます。

一般的には、URL のパスにバージョンを含める方法がよく使われます。

```
https://api.example.com/v1/users
https://api.example.com/v2/users
```

---

### 原則 5: ページネーションとフィルタリング

大量のデータを一度に返すと、サーバーとクライアントの両方に負荷がかかります。

GET リクエストでリソース一覧を返す際は、ページネーションを実装しましょう。

また、特定の条件でデータを絞り込むためのフィルタリング機能も提供すると、API の利便性が向上します。

これらの機能は、クエリパラメータを使って実装するのが一般的です。

ページネーション

```
GET /posts?page=2&limit=20 （2 ページ目の投稿を 20 件取得）
```

フィルタリング

```
GET /posts?status=published （公開済みの投稿のみ取得）
```

ソート

```
GET /posts?sort=-created_at （作成日時の降順でソート）
```

---

### 原則 6: エラーレスポンスの統一

エラーが発生した際に、ステータスコードだけでなく、エラーの詳細を JSON 形式で返すようにしましょう。

エラーレスポンスのフォーマットを API 全体で統一することで、クライアント側でのエラーハンドリングが容易になります。

```ts
{
  "error": {
    "code": "INVALID_PARAMETER",
    "message": "The 'email' field is required.",
    "details": "https://developer.example.com/errors#INVALID_PARAMETER"
  }
}
```

---

### 原則 7: セキュリティとレート制限

API を公開する際は、セキュリティ対策が必須です。

OAuth 2.0 や JWT（JSON Web Token）などの標準的な技術を利用して、認証・認可の仕組みを導入しましょう。

また、特定のユーザーや IP アドレスからのリクエスト数を制限する「レート制限」を設けることで、悪意のある攻撃やサーバーの過負荷を防ぐことができます。

#### 実装例(Express)

```ts
import express from "express";
const app = express();
app.use(express.json());

// GET /v1/users - ユーザー一覧取得（ページネーション対応）
app.get("/v1/users", (req, res) => {
  const page = parseInt(req.query.page) || 1;
  const limit = parseInt(req.query.limit) || 10;
  // ... データベースからユーザーを取得するロジック ...
  res.status(200).json({ users: [], page, limit });
});

// POST /v1/users - ユーザー作成
app.post("/v1/users", (req, res) => {
  const { name, email } = req.body;
  if (!name || !email) {
    return res.status(400).json({
      error: {
        code: "INVALID_PARAMETER",
        message: "Name and email are required.",
      },
    });
  }
  // ... データベースにユーザーを作成するロジック ...
  const newUser = { id: Date.now(), name, email };
  res.status(201).json(newUser);
});

app.listen(3000, () => console.log("API server running on port 3000"));
```

---

### まとめ

RESTful API を設計するための 7 つの基本原則

1. リソース指向の URL 設計
1. 適切な HTTP メソッドの使用
1. ステータスコードの正しい使い分け
1. バージョニング戦略
1. ページネーションとフィルタリング
1. エラーレスポンスの統一
1. セキュリティとレート制限
