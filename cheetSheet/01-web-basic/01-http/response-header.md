# Response Header

サーバーがブラウザへ返す「追加情報」です。

ブラウザが API やページへアクセスすると、サーバーはデータと一緒に色々な情報を返します。

- JSON を返します
- キャッシュ OK です
- Cookie 保存してください
- gzip 圧縮してます
- CORS 許可します

など。

これらをまとめたものが Response Header です

## Response Header の見方

サンプル

```
HTTP/1.1 200 OK
Content-Type: application/json
Set-Cookie: session_id=xxxxx
Cache-Control: max-age=3600
```

意味

- JSON 形式で返します
- Cookie 保存してください
- 1 時間キャッシュして OK です

## 最重要 Response Header 一覧

### 1. Content-Type

```
Content-Type: application/json
```

「返却データの形式」を示す

#### よくある値

| 値                     | 内容       |
| ---------------------- | ---------- |
| application/json       | JSON       |
| text/html              | HTML       |
| text/css               | CSS        |
| application/javascript | JavaScript |
| image/png              | PNG 画像   |

#### JSON レスポンス例

```
Content-Type: application/json
```

```
{
  "name": "taro"
}
```

#### HTML レスポンス例

```
Content-Type: text/html
```

---

### 2. Set-Cookie

```
Set-Cookie: session_id=xxxxx
```

ブラウザへ Cookie を保存させる

#### 主な用途

| 用途         | 内容         |
| ------------ | ------------ |
| ログイン維持 | session 管理 |
| 認証         | ユーザー識別 |
| 設定保存     | テーマ・言語 |

#### 保存後

次回リクエストで

```
Cookie: session_id=xxxxx
```

が送られる

---

### 3. Cache-Control

```
Cache-Control: max-age=3600
```

キャッシュ制御

#### よくある値

| 値           | 意味             |
| ------------ | ---------------- |
| no-cache     | 毎回確認         |
| no-store     | 保存禁止         |
| max-age=3600 | 1 時間キャッシュ |

---

### 4. Access-Control-Allow-Origin

```
Access-Control-Allow-Origin: *
```

CORS 許可設定

#### 例

```
localhost:3000
 ↓
api.example.com
```

別ドメイン通信時に必要

#### どのサイトからでもアクセス OK

```
Access-Control-Allow-Origin: *
```

#### 特定サイトのみ許可

```
Access-Control-Allow-Origin: http://localhost:3000
```

---

### 5. Content-Encoding

```
Content-Encoding: gzip
```

レスポンスを圧縮している

#### よくある値

| 値   | 内容        |
| ---- | ----------- |
| gzip | gzip 圧縮   |
| br   | Brotli 圧縮 |

---

### 6. Content-Length

```
Content-Length: 5320
```

レスポンスサイズ(byte)

#### 用途

- ダウンロード進捗
- 通信量管理

---

### 7. Date

```
Date: Mon, 18 May 2026 10:00:00 GMT
```

レスポンス生成時刻

#### 用途

- キャッシュ管理
- デバッグ

---

### 8. Server

```
Server: nginx
```

サーバーソフト情報

#### よくある値

| 値         | 内容       |
| ---------- | ---------- |
| nginx      | nginx      |
| Apache     | Apache     |
| cloudflare | Cloudflare |

---

### 9. Location

```
Location: /login
```

リダイレクト先

#### よくあるケース

```
未ログイン
 ↓
/login へ移動
```

#### ステータスコードとセット

```
HTTP/1.1 302 Found
Location: /login
```
