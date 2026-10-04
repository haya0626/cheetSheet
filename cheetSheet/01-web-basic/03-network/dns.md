# DNS

DNS（Domain Name System）は、

> ドメイン名と IP アドレスなどの情報を対応付ける、インターネット上の分散データベースシステム

■ 代表例 **Name Resolution** ( IP アドレスを取得する処理
)

www.example.com ⇒ 93.184.216.34

他にも以下のような様々な情報を登録できる

- メールサーバー
- ネームサーバー
- ドメイン所有確認用情報
- 証明書発行制御
- サービス情報

DNS は巨大な 1 台のサーバーで管理されているわけではなく、名前空間を階層化し、管理範囲を複数の DNS サーバーへ分散する仕組みになっている

## なぜ DNS が必要なのか

コンピューター同士の通信では、最終的には IP アドレスを利用する

`https://example.com`へアクセスしたとしても、実際には通信先の IP アドレスが必要になる

しかし人間が

```
93.184.216.34
142.250.xxx.xxx
104.18.xxx.xxx
```

のような IP アドレスをすべて覚えるのは現実的ではない

```
example.com
↓
93.184.216.34
```

のように、

**人間が覚えやすい名前 ⇒ コンピューターが通信に使う IP アドレス**

へ変換する仕組みとして DNS が使われる

## 処理イメージ

■ `https://www.example.com/products`へアクセスした場合

```
① URLを解析
    ↓
② www.example.com のIPアドレスをDNSで名前解決
    ↓
③ 取得したIPアドレスへ接続
    ↓
④ TCP / QUICなどで通信
    ↓
⑤ HTTPSの場合はTLS通信を確立
    ↓
⑥ HTTPリクエスト送信
    ↓
⑦ Webサーバーからレスポンス
```

つまり、HTTP 通信を行う前に
「どのサーバーへ接続すればよいか」
を調べる役割を持つ

## DNS を構成する主な要素

DNS の名前解決では、主に以下の DNS サーバーが登場する

```
DNSクライアント
    ↓
キャッシュDNS
    ↓
ルートDNS
    ↓
ルートDNS & TLD
    ↓
権威DNS
```

#### ■ DNS クライアント（スタブリゾルバ）

- PC やスマートフォン側で、DNS に名前解決を依頼する仕組み
- 基本的に自分ですべての DNS サーバーを調べるのではなく、キャッシュ DNS サーバーに問い合わせを任せる

---

#### ■ キャッシュ DNS サーバー（フルリゾルバー / 再帰的リゾルバー）

- DNS クライアントの代わりに、必要な DNS サーバーへ問い合わせて名前解決を行う
- 一度取得した DNS 情報は一定時間キャッシュし、同じ問い合わせが来た場合はキャッシュから返す

---

#### ■ ルート DNS サーバー

- DNS の階層構造の一番上にある DNS サーバー

  例えば`www.example.com`を調べる場合、「.com ならこの TLD DNS サーバーに聞いてください」というように、次に問い合わせる DNS サーバーを案内する役割を持つ

---

#### ■ TLD（トップレベルドメイン）サーバー

- .com や .jp などのトップレベルドメインを管理する DNS サーバー

  例えば、`www.example.com`なら「 .com の TLD DNS サーバーが、example.com ならこの権威 DNS サーバーに聞いてください」と案内する

---

#### ■ 権威 DNS サーバー（コンテンツサーバー）

- そのドメインについての正式な DNS 情報を管理しているサーバー
- 名前解決では、最終的にここから IP アドレスなどの情報を取得する

---

### 名前解決の流れ

`www.example.com` の IP アドレスを取得する場合

```
PC
 │
 │ www.example.com は？
 ▼
DNSキャッシュサーバー
 │
 │ キャッシュがなければ問い合わせ
 ▼
ルートDNS
 │
 │ 「.com のDNSに聞いて」
 ▼
.com のTLD DNS
 │
 │ 「example.com の権威DNSに聞いて」
 ▼
権威DNS
 │
 │ 「www.example.com は 192.0.2.10」
 ▼
DNSキャッシュサーバー
 │
 ▼
PC
```

取得した IP アドレスを使って、`192.0.2.10`へ通信する

## TTL

TTL（Time To Live）は、**DNS 情報をキャッシュしてよい時間**

```
TTL = 300
```

なら、

```
300秒 = 5分
```

キャッシュできる

## DNS レコード

DNS に登録する情報を **DNS レコード** と呼ぶ

Web 開発では、まず以下を理解

| レコード | 用途                   |
| -------- | ---------------------- |
| A        | IPv4 アドレス          |
| AAAA     | IPv6 アドレス          |
| CNAME    | 別のドメイン名への別名 |
| NS       | DNS サーバー           |
| MX       | メールサーバー         |
| TXT      | 認証・ドメイン確認など |

---

#### ■ A レコード

ドメイン名と IPv4 アドレスを対応付ける

```
www.example.com
↓
192.0.2.10
```

最も基本的な DNS レコード

---

#### ■ AAAA レコード

ドメイン名と IPv6 アドレスを対応付ける

```
www.example.com
↓
2001:db8::1
```

A レコードの IPv6 版

---

#### ■ CNAME レコード

あるドメイン名を、別のドメイン名の別名として設定する

```text
www.example.com
↓
example-server.example.net
↓
example-server.example.net
↓
192.0.2.10
```

---

#### ■ NS レコード

そのドメインを管理する DNS サーバーを指定する

```
example.com
↓
ns1.example.net
ns2.example.net
```

つまり、

> 「example.com の DNS 情報については、この DNS サーバーに聞いてください」

---

#### ■ MX レコード

メールを受信するメールサーバーを指定する

---

#### ■ TXT レコード

文字列を DNS に登録するためのレコード

実務例

- ドメイン所有確認
- メール認証
- 外部サービス連携

などで利用される

## DNS の確認方法（Windows-powershell）

■ IP アドレスを取得

```powershell
nslookup example.com
```

```text
Name:    example.com
Address: 192.0.2.10
```

■ DNS レコードを取得

```powershell
Resolve-DnsName example.com
```

以下付与で絞込も可能

- A レコードだけ

  ```powershell
  Resolve-DnsName example.com -Type A
  ```

- CNAME レコードだけ

  ```powershell
  Resolve-DnsName example.com -Type CNAME
  ```

- MX レコードだけ

  ```powershell
  Resolve-DnsName example.com -Type MX
  ```
