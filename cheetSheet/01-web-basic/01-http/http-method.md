# HTTP Method

HTTP Method（HTTP メソッド）とは、クライアントがサーバーに対して

**「このリソースに対して何をしたいのか」**

を伝えるための HTTP リクエストの種類

**主なメソッド**

```
GET     → データを取得したい
POST    → データを登録・処理したい
PUT     → データ全体を更新したい
PATCH   → データの一部分を更新したい
DELETE  → データを削除したい
```

例えば以下のリクエストでは、

```
GET /api/users/100
```

- GET ⇒ 取得したい
- /api/users/100 ⇒ ID=100 のユーザーを

という意味になる

つまり HTTP API は基本的に、`HTTP Method + URL`によって「何に対して何をするのか」を表現する

## HTTP Method 一覧

| Method    | 主な用途                  |
| --------- | ------------------------- |
| `GET`     | 取得                      |
| `POST`    | 登録・処理実行            |
| `PUT`     | 全体更新・置換            |
| `PATCH`   | 部分更新                  |
| `DELETE`  | 削除                      |
| `HEAD`    | Header のみ取得           |
| `OPTIONS` | 利用可能な通信方法を確認  |
| `QUERY`   | Body を使用した安全な検索 |

## GET

サーバーからリソースを取得するための Method

**代表的な用途**

- 一覧取得
- 詳細取得
- 検索
- 画像取得
- ファイル取得

**特徴・留意点**

1. GET によってサーバーの業務データを変更してはいけない
1. 検索条件には Query Parameter を使用することが多い
1. GET で Body は使わない  
   ( HTTP 仕様上、GET リクエストの Body には一般的な意味が定義されておらず、サーバー・Proxy・Framework などによって正常に扱われない可能性があるため )

## POST

指定したリソースにデータを渡して処理してもらうための Method

**代表的な用途**

- 新規登録
- 処理実行
- 状態変更

**特徴・留意点**

1. POST は「登録専用」ではない ( 作成や、キャンセルでも使用できる)
1. POST は基本的に冪等ではない( 重複防止設計が重要 )

## PUT

指定したリソースを送信した内容で置き換える Method

**特徴・留意点**

1. 基本的には、全体更新として利用する

## PATCH

リソースを部分的に変更する Method

**特徴・留意点**

1. 基本的には、部分更新として利用する

**PUT と PATCH の違い**

|            | PUT          | PATCH               |
| ---------- | ------------ | ------------------- |
| 意味       | 全体置換     | 部分変更            |
| 送信データ | リソース全体 | 変更部分            |
| 冪等性     | ○            | 必ずしも ○ ではない |
| 主な用途   | 全体更新     | 一部項目更新        |

## DELETE

指定したリソースを削除する Method

**特徴・留意点**

1. DELETE = 物理削除とは限らない( 論理削除しても問題ない )

   理由: HTTP Method は、API 利用者から見たリソース操作を表すもののため

1. DELETE で Body は基本使わない( URL で対象を特定する )

   複数件削除などで複雑な条件が必要なら、API 設計として POST などを利用するケースもある

## HEAD

GET とほぼ同じだが、レスポンス Body を返さない Method

**特徴・留意点**

- リソースの存在確認
- リソースの最終更新日時の取得
- コンテンツネゴシエーション( Accept ヘッダーなど )
- リソースの変更の有無の確認( 条件付きリクエスト )

**GET と HEAD の違い**

|                        | GET                                  | HEAD                                       |
| ---------------------- | ------------------------------------ | ------------------------------------------ |
| 目的                   | リソース自体を取得する               | ソースのメタデータ(ヘッダー情報)のみを取得 |
| レスポンスボディの有無 | 有                                   | 無                                         |
| サーバ側の処理         | サーバ側でリソースの生成や取得が必要 | リソースの本体を生成する必要はない         |
| キャッシュ対象         | レスポンスボディ                     | レスポンスヘッダーのみ                     |

## OPTIONS

URL に対して、どのような通信方法が利用できるかを確認する Method

行おうとしているリクエストをサーバが許可しているか、事前に調べるために使用する

⇒ **CORS（Cross-Origin Resource Sharing）の「プリフライトリクエスト（Preflight Request）」**

### CORS のプリフライトリクエストとは

異なるドメイン（オリジン）間で API 通信を行う際、ブラウザの安全機能を担保するために使われます

ブラウザが本番のリクエスト（POST や PUT など）を送信する直前に、自動的に OPTIONS メソッドを送信し、「このリクエストをそちらのサーバーに送っても安全か？」を問い合わせます

■ 具体例

`https://example.com`（フロントエンド）から `https://example.com`（バックエンド）へ、カスタムヘッダー付きの POST リクエストを送る場合

■ 流れ

1. ブラウザが自動で OPTIONS /users を送信。
2. サーバーが「GET と POST は許可するよ」「カスタムヘッダーも OK だよ」と返答（200 OK）
3. ブラウザが安全だと判断し、本来の POST /users を送信

## QUERY

GET では URL に収まりにくい複雑な検索条件を
Body で安全に送信する HTTP Method

## 重要概念

### Safe（安全）

> そのリクエストによってサーバー上のリソース変更を要求しない

■ 代表例

- GET
- HEAD
- OPTIONS
- QUERY

■ イメージ

GET を 100 回実行してもユーザーの削除、追加、更新が起こってはいけない

※ 内部的な副作用まで一切禁止という意味ではない

- アクセスログ
- アクセス数
- 監視ログ

---

### Idempotent（冪等）

> 同じリクエストを何度実行しても、意図される最終状態が同じになる性質

---

Method と安全・冪等性対応表

# Method と冪等性一覧

| Method  | Safe | Idempotent |
| ------- | ---: | ---------: |
| GET     |    ○ |          ○ |
| HEAD    |    ○ |          ○ |
| OPTIONS |    ○ |          ○ |
| QUERY   |    ○ |          ○ |
| PUT     |    × |          ○ |
| DELETE  |    × |          ○ |
| POST    |    × |          × |
| PATCH   |    × |         ×※ |

※PATCH は処理内容によって冪等に設計することもできる

## CRUD と HTTP Method

DB の CRUD と HTTP Method はよく対応付けられる

| CRUD   | SQL    | HTTP        |
| ------ | ------ | ----------- |
| Create | INSERT | POST        |
| Read   | SELECT | GET         |
| Update | UPDATE | PUT / PATCH |
| Delete | DELETE | DELETE      |

## Java / Spring Boot の対応

Spring Boot では HTTP Method と Controller の Annotation が対応する

### GET

```java
@GetMapping("/users/{id}")
public UserResponse getUser(@PathVariable Long id) {
    // ...
}
```

### POST

```java
@PostMapping("/users")
public UserResponse createUser(
        @RequestBody CreateUserRequest request) {
    // ...
}
```

### PUT

```java
@PutMapping("/users/{id}")
public UserResponse updateUser(
        @PathVariable Long id,
        @RequestBody UpdateUserRequest request) {
    // ...
}
```

### PATCH

```java
@PatchMapping("/users/{id}")
public UserResponse updateUserPartially(
        @PathVariable Long id,
        @RequestBody PatchUserRequest request) {
    // ...
}
```

### DELETE

```java
@DeleteMapping("/users/{id}")
public void deleteUser(@PathVariable Long id) {
    // ...
}
```

■ 対応関係

| HTTP   | Spring           |
| ------ | ---------------- |
| GET    | `@GetMapping`    |
| POST   | `@PostMapping`   |
| PUT    | `@PutMapping`    |
| PATCH  | `@PatchMapping`  |
| DELETE | `@DeleteMapping` |

## JavaScript / TypeScript での対応

### GET

```ts
const response = await fetch("/api/users/100");
```

`fetch`は Method を指定しなければ GET。

### POST

```ts
const response = await fetch("/api/users", {
  method: "POST",
  headers: {
    "Content-Type": "application/json",
  },
  body: JSON.stringify({
    name: "Tanaka",
    age: 25,
  }),
});
```

### PATCH

```ts
const response = await fetch("/api/users/100", {
  method: "PATCH",
  headers: {
    "Content-Type": "application/json",
  },
  body: JSON.stringify({
    age: 26,
  }),
});
```

### DELETE

```ts
const response = await fetch("/api/users/100", {
  method: "DELETE",
});
```
