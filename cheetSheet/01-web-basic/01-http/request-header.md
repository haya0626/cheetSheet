# Request Header

ブラウザがサーバーへ送る「追加情報」です。

例えばブラウザは、API 通信するときにこんな情報を一緒に送っています。

- どのサイトへアクセスしたいか
- JSON を送るのか
- ログイン済みなのか
- どのページから来たのか
- どんなデータ形式が欲しいのか

これらをまとめたものが Request Header です

## Request Header の見方

サンプル

```text
POST /api/users HTTP/1.1
Host: api.example.com
Content-Type: application/json
Authorization: Bearer xxxxx
Accept: application/json
```

これは以下の意味を表す

| Header        | 意味                 |
| ------------- | -------------------- |
| Host          | 接続先               |
| Content-Type  | 送信データ形式       |
| Authorization | 認証情報             |
| Accept        | 欲しいレスポンス形式 |

## 最重要 Header 一覧

### 1. Host

```
Host: api.example.com
```

「どこのサーバーに送るか」を指定する

例

```
Host: github.com
```

→ GitHub へアクセス

---

### 2. Content-Type

```
Content-Type: application/json
```

「何形式でデータ送ってるか」を指定する

よくある値

| 値                                | 意味         |
| --------------------------------- | ------------ |
| application/json                  | JSON         |
| multipart/form-data               | ファイル送信 |
| application/x-www-form-urlencoded | Form 送信    |

#### JSON 送信例

```
Content-Type: application/json
```

```
{
  "name": "taro"
}
```

#### ファイルアップロード例

```
Content-Type: multipart/form-data
```

---

### 3. Authorization

```
Authorization: Bearer xxxxx
```

ログイン情報・認証情報を指定する

#### よくある用途

- ログイン判定
- API 認証
- JWT 認証

#### 補足 : Bearer Token とは？

```
Authorization: Bearer eyJxxxxx
```

この Token を持っている人だけ API 利用可能

ないと、、、

```
401 Unauthorized
```

---

### 4. Cookie

```
Cookie: session_id=xxxxx
```

ブラウザに保存された情報を示す

#### 主な用途

| 用途         | 内容         |
| ------------ | ------------ |
| ログイン維持 | session 保持 |
| ユーザー識別 | 誰か判定     |
| 設定保存     | 言語など     |

---

### 5. Accept

```
Accept: application/json
```

「どんな形式のレスポンスが欲しいか」を示す

#### JSON が欲しい

```
Accept: application/json
```

#### HTML が欲しい

```
Accept: text/html
```

---

### 6. User-Agent

```
User-Agent: Mozilla/5.0 ...
```

ブラウザ情報を示す

#### サーバー側でわかること

- Chrome か
- Safari か
- スマホか
- Windows か

| 用途         | 内容             |
| ------------ | ---------------- |
| スマホ判定   | モバイル表示     |
| Bot 判定     | クローラー検知   |
| ブラウザ対応 | Edge/Safari 対応 |

---

### 7. Referer

```
Referer: https://example.com/top
```

「どのページから来たか」を示す

#### イメージ

```
TOPページ
 ↓
詳細ページ
```

詳細ページへの通信で

```
Referer: https://example.com/top
```

が付く

---

### 8. Origin

```
Origin: https://example.com
```

「どのサイトからアクセスしてるか」を示す

#### イメージ

```
localhost:3000
 ↓
api.example.com
```

この時、

```
Origin: http://localhost:3000
```

が送られる。

---

### 9. Accept-Encoding

```
Accept-Encoding: gzip, br
```

「圧縮されたデータを OK か」を示す

#### 用途

通信量削減。

#### よくある値

| 値   | 内容        |
| ---- | ----------- |
| gzip | gzip 圧縮   |
| br   | Brotli 圧縮 |

---

#### 10. Accept-Language

```
Accept-Language: ja,en-US
```

ユーザーの言語設定を示す

---

### 11. Cache-Control

```
Cache-Control: no-cache
```

キャッシュ制御を示す

#### よくある値

| 値           | 意味       |
| ------------ | ---------- |
| no-cache     | 毎回確認   |
| no-store     | 保存禁止   |
| max-age=3600 | 1 時間保存 |
