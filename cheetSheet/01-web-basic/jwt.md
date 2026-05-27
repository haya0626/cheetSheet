# JWT（JSON Web Token）

## 目次

1. [JWT とは](#jwtとは)
1. [JWT が生まれた理由](#jwtが生まれた理由)
1. [JWT の基本構造](#jwtの基本構造)
1. [JWT の中身を分解して理解する](#jwtの中身を分解して理解する)
1. [署名(Signature)とは](#署名signatureとは)
1. [JWT の認証フロー](#jwtの認証フロー)
1. [Access Token と Refresh Token](#access-token-と-refresh-token)
1. [Stateless 認証とは](#stateless認証とは)
1. [Session 認証との違い](#session認証との違い)
1. [JWT のメリット・デメリット](#jwtのメリットデメリット)
1. [JWT の代表的な用途](#jwtの代表的な用途)
1. [JWT のアルゴリズム](#jwtのアルゴリズム)
1. [JWT のセキュリティ](#jwtのセキュリティ)
1. [JWT の危険な実装例](#jwtの危険な実装例)
1. [Cookie 保存 vs LocalStorage 保存](#cookie保存-vs-localstorage保存)
1. [JWT 認証の実践設計](#jwt認証の実践設計)
1. [OAuth/OIDC と JWT](#oauthoidc-と-jwt)
1. [JWT の実装例](#jwtの実装例)
1. [JWT のデバッグ方法](#jwtのデバッグ方法)
1. [よくある誤解](#よくある誤解)
1. [JWT 設計ベストプラクティス](#jwt設計ベストプラクティス)
1. [まとめ](#まとめ)

## 1. JWT とは

JWT = JSON Web Token

JSON 形式のデータを安全にやり取りするためのトークン規格

RFC 7519 で標準化されている

JWT は

- 「ユーザー情報」
- 「認証状態」
- 「権限情報」

などを安全に保持できる

## 2. JWT が生まれた理由

昔の Web 認証は主に

- Session
- Cookie

が中心だったが以下の問題が発生した

---

### Session 認証の問題

#### サーバーが状態を持つ必要がある

```
Server Memory
 ├─ session_id: abc123
 ├─ user_id: 15
 └─ expires: ...
```

- メモリ消費
- Redis 必要
- サーバー増設時の同期
- スケーリング問題

が発生

### JWT はどう解決したのか

JWT は

> 「状態をサーバーに保存しない」

つまり

> ユーザー情報そのものをトークンに入れる

- DB 参照不要
- Session 不要
- 分散システムに強い

が可能になった

## 3. JWT の基本構造

JWT は 3 つの部分で構成される

```
HEADER.PAYLOAD.SIGNATURE
```

例：

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9
.
eyJ1c2VySWQiOjEsIm5hbWUiOiJUYXJvIn0
.
abcxyz123
```

## 4. JWT の中身

### 全体像

```
HEADER.PAYLOAD.SIGNATURE
```

### HEADER

アルゴリズム情報

例：

```
{
  "alg": "HS256",
  "typ": "JWT"
}
```

意味

- alg → 署名方式
- typ → JWT 形式

### PAYLOAD

データ本体

例：

```
{
  "userId": 1,
  "name": "Taro",
  "role": "admin"
}
```

ここに

- ユーザー ID
- 権限
- 有効期限

などを入れる

### SIGNATURE

改ざん検知用

```
HMACSHA256(
  base64UrlEncode(header) + "." +
  base64UrlEncode(payload),
  secret
)
```

ヘッダー + ペイロードを秘密鍵で署名

## 5. 署名(Signature)とは

「送信者の本人証明」と「データ改ざんの検知」を行うためのセキュリティ機構

### そもそもなぜ必要か

PAYLOAD は Base64 なので暗号化されてない → 誰でも読める

そうなると改ざん問題が起きうる

例：

```
{
  "role": "user"
}
```

を

```
{
  "role": "admin"
}
```

に変更

### 署名が作られる仕組み

1. ヘッダーとペイロードをそれぞれ Base64 エンコードする
1. 2 つをピリオドで繋ぐ（例：ヘッダー.ペイロード）
1. サーバーが持つ秘密鍵（または共通鍵）を使い、指定された暗号アルゴリズム（HS256 など）で計算し、署名データを作成する

署名があると

```
payload変更
↓
signature不一致
↓
改ざん検知
```

が可能

<u>JWT は暗号化しているものではないため、署名して保護する必要がある</u>

## 6. JWT の認証フロー

### 1. ログイン

```
Client → Server
ID/PW送信
```

### 2. サーバー

認証成功後、JWT 生成

### 3. クライアント保存

- Cookie
- LocalStorage
- Memory など

### 4. API 呼び出し

```http
Authorization: Bearer xxxxx.yyyyy.zzzzz
```

### 5. サーバー

署名検証

---

### フロー図

```
Login
  ↓
JWT発行
  ↓
Client保存
  ↓
API Request
  ↓
Bearer Token送信
  ↓
JWT検証
  ↓
認証OK
```

補足 : Bearer Token（ベアラートークン）送信とは

> Web API を利用する際、「私にはこの情報にアクセスする権限があります」という証明（アクセストークン）をサーバーに提示する通信方法

## 7. Access Token と Refresh Token
