# ドメイン駆動設計

> DDD は、難しいクラスをたくさん作るための設計ではありません。  
> 業務の言葉・ルール・構造をコードに反映し、変更に強いソフトウェアを作るための考え方です。

## 目次

- [1. ドメイン駆動設計とは](#1-ドメイン駆動設計とは)
- [2. DDD が必要になる背景](#2-ddd-が必要になる背景)
- [3. 普通の設計で起きる問題](#3-普通の設計で起きる問題)
- [4. DDD の本質](#4-ddd-の本質)
- [5. DDD の 2 つの設計領域](#5-ddd-の-2-つの設計領域)
  - [戦略的設計](#戦略的設計)
  - [戦術的設計](#戦術的設計)
  - [ユビキタス言語](#ユビキタス言語)
  - [境界づけられたコンテキスト](#境界づけられたコンテキスト)
  - [サブドメイン](#サブドメイン)
  - [コンテキストマップ](#コンテキストマップ)
- [6. DDD をコードに落とし込むための設計要素](#6-ddd-をコードに落とし込むための設計要素)
  - [Entity](#entity)
  - [Value Object](#value-object)
  - [Entity と Value Object の違い](#entity-と-value-object-の違い)
  - [Aggregate](#aggregate)
  - [Repository](#repository)
  - [Domain Service](#domain-service)
  - [Factory](#factory)
  - [Domain Event](#domain-event)
  - [Application Service / UseCase](#application-service--usecase)
  - [Infrastructure](#infrastructure)
- [7. DDD とクリーンアーキテクチャの違い](#7-ddd-とクリーンアーキテクチャの違い)
- [8. 実務での DDD 設計手順](#8-実務での-ddd-設計手順)
- [9. 実装例：注文確定を DDD で実装する](#9-実装例注文確定を-ddd-で実装する)
- [10. アンチパターン](#10-アンチパターン)
- [11. DDD を使うべきケース・使わなくてもよいケース](#11-ddd-を使うべきケース使わなくてもよいケース)
- [12. よくある疑問](#12-よくある疑問)

---

## 1. ドメイン駆動設計とは

DDD とは、Domain-Driven Design の略です。

日本語では「ドメイン駆動設計」と呼ばれます。

> 業務知識を深く理解し、その理解をソフトウェアの設計とコードに反映するための考え方

です。

DDD は、エリック・エヴァンスの著書『Domain-Driven Design』で広く知られるようになった設計思想です。

DDD の目的は、単にきれいなクラス設計をすることではありません。

本当の目的は、以下です。

> 複雑な業務ルールを正しく扱い、変更に強いソフトウェアを作ること

---

### ドメインとは

ドメインとは、ソフトウェアで解決したい業務領域のことです。

例えば、EC サイトなら以下がドメインになります。

```text
ECサイト
├── 注文
├── 決済
├── 配送
├── 在庫
├── 商品管理
├── 顧客管理
└── クーポン
```

この中には、単純なデータ保存だけでは表現しきれない業務ルールがあります。

例えば、注文ドメインには以下のようなルールがあります。

- 商品が 1 件もない注文は確定できない
- 支払い済みの注文だけ配送できる
- キャンセル済みの注文は再度キャンセルできない
- 在庫がない商品は購入できない
- 有効期限切れのクーポンは使えない

DDD では、このような業務ルールをコードの中心に置きます。

---

### DDD を一言でいうと

DDD を一言でいうなら、以下です。

> 業務の言葉で、業務のルールを、業務に近い形でコードに表現する設計

例えば、業務担当者が「注文を確定する」と言っているなら、コードにもそれが表れているべきです。

悪い例：

```java
order.save();
```

`save` は保存という技術的な言葉です。

これだけでは、業務上何をしているのかわかりません。

良い例：

```java
order.confirm();
```

`confirm` は「注文を確定する」という業務上の意味を持ちます。

DDD では、このように業務で使われる言葉をコードにも反映することを重視します。

---

## 2. DDD が必要になる背景

小さなアプリケーションでは、単純な CRUD で十分なことが多いです。

CRUD とは、以下の基本操作のことです。

- Create：登録
- Read：取得
- Update：更新
- Delete：削除

例えば、以下のような処理だけなら、Controller / Service / Repository のような一般的な構成でも大きな問題は起きにくいです。

- ユーザーを登録する
- 商品を保存する
- 記事を更新する
- お知らせを削除する

この程度であれば、単純な Service クラスに処理を書いても管理できます。

しかし、実務のシステムはもっと複雑です。

例：EC サイト

- 会員ランク
- クーポン
- ポイント
- 在庫引当
- キャンセル
- 返品
- 配送ステータス
- 決済ステータス
- 定期購入
- キャンペーン
- 返金
- 不正注文チェック

このような業務ルールが増えてくると、単純な CRUD では表現しきれません。

---

### CRUD だけではつらくなる例

例えば、注文確定処理を考えます。

最初は、ただ注文ステータスを更新するだけかもしれません。

```text
注文ステータスを DRAFT から CONFIRMED に変更する
```

しかし、実務では次のような条件が追加されます。

- 注文明細が 1 件以上あること
- 在庫が足りていること
- 使用したクーポンが有効であること
- 支払い方法が利用可能であること
- 不正注文ではないこと
- すでに確定済みではないこと
- キャンセル済みではないこと

こうなると、ただの `updateOrderStatus()` ではなくなります。

これは「注文確定」という業務ルールのかたまりです。

DDD では、このような業務ルールを適切なモデルに閉じ込めることを考えます。

---

## 3. 普通の設計で起きる問題

よくある設計では、以下のような Service が生まれます。

- `OrderService`
- `UserService`
- `ProductService`
- `PaymentService`

最初は問題ありません。

しかし、機能追加が続くと、Service の中に多くの処理が集まります。

```text
OrderService
├── createOrder()
├── cancelOrder()
├── returnOrder()
├── applyCoupon()
├── calculatePoint()
├── changeShippingAddress()
├── confirmPayment()
├── reserveStock()
├── sendOrderMail()
├── checkFraud()
└── refundOrder()
```

このような状態になると、以下の問題が起きます。

- どこに何を書くべきかわからない
- 業務ルールがあちこちに分散する
- 同じような条件分岐が増える
- 修正時の影響範囲が読めない
- テストが難しくなる
- コードを読んでも業務が理解できない
- Service が巨大化する
- Entity が getter / setter だけになる

この状態は、よく「Fat Service」や「貧血ドメインモデル」と呼ばれます。

---

### 貧血ドメインモデルとは

貧血ドメインモデルとは、Entity がデータだけを持ち、業務ルールをほとんど持っていない状態です。

悪い例：

```java
public class Order {
    private String id;
    private String status;
    private int totalPrice;

    public String getId() {
        return id;
    }

    public void setId(String id) {
        this.id = id;
    }

    public String getStatus() {
        return status;
    }

    public void setStatus(String status) {
        this.status = status;
    }

    public int getTotalPrice() {
        return totalPrice;
    }

    public void setTotalPrice(int totalPrice) {
        this.totalPrice = totalPrice;
    }
}
```

一見、普通の Java クラスに見えます。

しかし、この `Order` は「注文」という業務ルールをほとんど表現していません。

注文を確定できるかどうか、キャンセルできるかどうか、合計金額をどう計算するかは、別の Service に書かれることになります。

```java
public class OrderService {
    public void confirm(Order order) {
        if (order.getStatus().equals("CANCELED")) {
            throw new IllegalStateException("キャンセル済みの注文は確定できません");
        }

        order.setStatus("CONFIRMED");
    }
}
```

この状態だと、注文のルールが `Order` ではなく `OrderService` に漏れています。

DDD では、できるだけ業務ルールを業務概念に近いクラスへ置きます。

---

## 4. DDD の本質

DDD の本質は、次の一文に集約できます。

> 業務の言葉・ルール・構造を、そのままコードに反映する

DDD は、単なるフォルダ構成でも、Entity や Value Object をたくさん作ることでもありません。

DDD で大切なのは、以下です。

- 業務を理解する
- 業務で使われる言葉を揃える
- 業務ルールを見つける
- そのルールを正しい場所に置く
- コードを読めば業務がわかる状態にする

---

### DDD で目指すコード

DDD で目指すコードは、以下のようなコードです。

```java
order.confirm();
```

この一行を見たときに、

> 注文を確定しているんだな

と読み取れる状態が理想です。

さらに、`confirm()` の中には、注文確定に必要な業務ルールが閉じ込められているべきです。

```java
public void confirm() {
    if (items.isEmpty()) {
        throw new IllegalStateException("商品が1件もない注文は確定できません");
    }

    if (status == OrderStatus.CANCELED) {
        throw new IllegalStateException("キャンセル済みの注文は確定できません");
    }

    status = OrderStatus.CONFIRMED;
}
```

こうすることで、注文確定のルールを知りたい人は `Order` を見ればよくなります。

---

## 5. DDD の 2 つの設計領域

DDD には、大きく分けて 2 つの設計領域があります。

```text
DDD
├── 戦略的設計
└── 戦術的設計
```

---

### 戦略的設計

戦略的設計とは、システム全体をどう分けるかを考える設計です。

主に以下を扱います。

- ドメイン
- サブドメイン
- コアドメイン
- ユビキタス言語
- 境界づけられたコンテキスト
- コンテキストマップ

戦略的設計は、コードを書く前の設計です。

特に大規模システムでは、戦術的設計よりも重要になることがあります。

なぜなら、最初に境界を間違えると、後からコードをきれいにしても全体の複雑さが消えないからです。

---

### 戦術的設計

戦術的設計とは、ドメインモデルをコードとしてどう表現するかを考える設計です。

主に以下を扱います。

- Entity
- Value Object
- Aggregate
- Repository
- Domain Service
- Factory
- Domain Event
- Application Service / UseCase

初学者が DDD と聞いて最初にイメージしやすいのは、こちらの戦術的設計です。

ただし、戦術的設計だけを真似しても DDD にはなりません。

> Entity や Value Object を作る前に、まず業務の理解と境界の整理が必要です。

---

### ユビキタス言語

ユビキタス言語とは、開発者と業務担当者が共通して使う言葉のことです。

> 業務上の言葉とコード上の言葉を揃えるための共通言語

です。

---

#### なぜユビキタス言語が重要なのか

業務担当者が「注文確定」と言っているのに、コード上では `saveOrder()` と書かれていたら、認識がずれます。

```text
業務上の意味：注文確定
コード上の表現：saveOrder()
```

この場合、`saveOrder()` は保存なのか、注文確定なのか、単なる更新なのかわかりません。

良い例：

```java
confirmOrder();
cancelOrder();
applyCoupon();
reserveStock();
shipOrder();
```

悪い例：

```java
saveOrder();
updateStatus();
process();
handle();
doSomething();
```

DDD では、業務の言葉とコードの言葉を揃えることで、仕様と実装のズレを減らします。

---

### 境界づけられたコンテキスト

境界づけられたコンテキストとは、ある言葉やモデルが同じ意味で通用する範囲のことです。

> 同じ言葉が同じ意味で使われる境界

です。

---

#### なぜ境界が必要なのか

同じ言葉でも、部門や業務領域によって意味が違うことがあります。

例：「顧客」

> 営業部門：まだ契約していない見込み客  
> 請求部門：請求先として登録された契約済みの相手  
> サポート部門：問い合わせ履歴を持つ利用者

同じ「顧客」という言葉でも、文脈によって意味が異なります。

悪い例：すべての文脈で同じ `Customer` モデルを使う。

```text
Customer
├── salesStatus
├── billingAddress
├── supportTickets
├── contractPlan
├── invoiceSetting
└── leadScore
```

このようなモデルは、複数の文脈が混ざって肥大化します。

最初は便利に見えますが、営業の都合で変更した項目が請求やサポートに影響するなど、変更の影響範囲が読みにくくなります。

良い例：文脈ごとにモデルを分ける。

```text
SalesContext
└── Customer

BillingContext
└── Customer

SupportContext
└── Customer
```

同じ `Customer` という名前でも、各コンテキスト内では意味が異なります。

---

### サブドメイン

サブドメインとは、事業や業務を分解した領域のことです。

例えば EC サイトなら、以下のように分けられます。

```text
ECサイト
├── 注文
├── 決済
├── 配送
├── 在庫
├── 商品管理
├── 顧客管理
└── クーポン
```

DDD では、サブドメインをさらに以下の 3 つに分類します。

```text
サブドメイン
├── コアドメイン
├── 支援サブドメイン
└── 汎用サブドメイン
```

---

#### ■ コアドメイン

コアドメインとは、事業上もっとも重要で、競争優位につながる領域です。

例えば、フードデリバリーサービスなら以下がコアドメインになりやすいです。

- 配達員と注文のマッチング
- 配達時間の最適化
- 需要予測
- 配達ルートの最適化

ここは、自社でしっかり設計・実装する価値があります。

> コアドメインは、サービスの強みそのものになりやすい領域です。

---

#### ■ 支援サブドメイン

支援サブドメインとは、コアドメインを支える業務領域です。

例：

- 管理画面
- 通知
- レポート
- 顧客管理
- 問い合わせ管理

重要ではありますが、直接的な競争優位にはなりにくい領域です。

---

#### ■ 汎用サブドメイン

汎用サブドメインとは、多くのサービスで共通して必要になる一般的な機能です。

例：

- 認証
- 決済
- メール送信
- ログ管理
- ファイルアップロード

これらは外部サービスや既存ライブラリを活用してもよい領域です。

すべてを自作する必要はありません。

---

### コンテキストマップ

コンテキストマップとは、複数の境界づけられたコンテキスト同士の関係を整理したものです。

例えば EC サイトでは、以下のような関係があります。

```text
[注文コンテキスト]
      ↓
[決済コンテキスト]
      ↓
[配送コンテキスト]
```

もう少し具体的にすると、以下のようになります。

```text
注文コンテキスト
├── 注文を作成する
├── 注文を確定する
└── 決済コンテキストへ支払い依頼を出す

決済コンテキスト
├── 支払いを受け付ける
├── 支払い結果を管理する
└── 注文コンテキストへ支払い完了を通知する

配送コンテキスト
├── 配送指示を受け取る
├── 配送ステータスを管理する
└── 注文コンテキストへ配送完了を通知する
```

コンテキストマップを作ることで、システム全体の境界と依存関係を把握しやすくなります。

---

## 6. DDD をコードに落とし込むための設計要素

ここからは、DDD の戦術的設計を見ていきます。

戦術的設計は、業務ルールをコードに落とし込むための道具です。

ただし、道具を覚えることが目的ではありません。

> どの業務ルールを、どの場所に置くべきか

を考えるために使います。

---

### Entity

Entity とは、同一性を持つオブジェクトです。

> 属性が変わっても「同じもの」として扱われる業務オブジェクト

です。

例：

- ユーザー
- 注文
- 商品
- 口座
- 契約

例えばユーザーは、名前やメールアドレスが変わっても同じユーザーです。

理由は、ユーザー ID によって同一性を判断できるからです。

---

#### ■ Entity の特徴

Entity には、以下の特徴があります。

- ID を持つ
- ライフサイクルを持つ
- 状態が変化することがある
- 業務ルールを持つことがある

---

#### Entity の例

```java
public class User {

    private final UserId id;
    private Email email;

    public User(UserId id, Email email) {
        if (id == null) {
            throw new IllegalArgumentException("ユーザーIDは必須です");
        }
        if (email == null) {
            throw new IllegalArgumentException("メールアドレスは必須です");
        }

        this.id = id;
        this.email = email;
    }

    public void changeEmail(Email newEmail) {
        if (newEmail == null) {
            throw new IllegalArgumentException("新しいメールアドレスは必須です");
        }

        this.email = newEmail;
    }

    public UserId getId() {
        return id;
    }

    public Email getEmail() {
        return email;
    }
}
```

この `User` は、`email` が変わっても同じユーザーです。

なぜなら、同一性は `id` で判断するからです。

---

### Value Object

Value Object とは、値そのものに意味があるオブジェクトです。

> ID ではなく、値によって同一性を判断するオブジェクト

です。

例：

- メールアドレス
- 金額
- 住所
- 電話番号
- 期間
- 数量
- 注文番号

Entity と違い、Value Object は ID を持ちません。

値が同じであれば、同じものとして扱います。

---

#### なぜ Value Object が必要なのか

普通に実装すると、メールアドレスは `String` で表現されがちです。

```java
String email = "test@example.com";
```

しかし、ただの `String` では不正な値を防げません。

```java
String email = "invalid-email";
```

この文字列はメールアドレスとしては不正ですが、`String` としては正しい値です。

Value Object にすると、不正な値を生成時点で防げます。

```java
import java.util.Objects;

public class Email {

    private final String value;

    public Email(String value) {
        if (value == null || value.isBlank()) {
            throw new IllegalArgumentException("メールアドレスは必須です");
        }
        if (!value.contains("@")) {
            throw new IllegalArgumentException("メールアドレスの形式が正しくありません");
        }

        this.value = value;
    }

    public String getValue() {
        return value;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (!(o instanceof Email)) return false;
        Email email = (Email) o;
        return Objects.equals(value, email.value);
    }

    @Override
    public int hashCode() {
        return Objects.hash(value);
    }
}
```

---

#### Value Object のメリット

Value Object には、以下のメリットがあります。

- 不正な値を作れない
- バリデーションを一箇所に集約できる
- 意味のある型として扱える
- コードの可読性が上がる
- 引数の渡し間違いを防ぎやすい

例えば、`String` だけで ID を扱うと、以下のようなミスが起きます。

```java
String userId = "user-001";
String orderId = "order-001";

cancelOrder(userId); // 本当は orderId を渡すべき
```

`UserId` と `OrderId` を別の型にしておけば、このようなミスをコンパイル時に防ぎやすくなります。

---

### Entity と Value Object の違い

| 項目     | Entity               | Value Object             |
| -------- | -------------------- | ------------------------ |
| 同一性   | ID で判断する        | 値で判断する             |
| 状態変化 | することがある       | 基本的に不変             |
| ID       | 持つ                 | 持たない                 |
| 例       | User, Order, Account | Email, Money, Address    |
| 目的     | 業務上の対象を表す   | 業務上意味のある値を表す |

---

#### 判断基準

迷ったときは、以下の質問をします。

> 値が変わっても、同じものとして追跡したいか？

YES なら Entity の候補です。

例：

- ユーザーはメールアドレスが変わっても同じユーザー
- 注文はステータスが変わっても同じ注文
- 口座は残高が変わっても同じ口座

NO なら Value Object の候補です。

例：

- 1000 円は 1000 円
- `test@example.com` は `test@example.com`
- 2026-01-01 から 2026-01-31 の期間は、その値そのものに意味がある

---

### Aggregate

Aggregate とは、整合性を守るためのまとまりです。

> 関連するオブジェクトをひとまとまりにし、その内部ルールを守る単位

です。

DDD の中でも特に重要で、難しい概念です。

---

#### Aggregate の基本

例えば、注文には複数の注文明細があります。

```text
Order
├── OrderItem
├── OrderItem
└── OrderItem
```

このとき、`OrderItem` を外部から自由に変更できると、注文全体の整合性が壊れる可能性があります。

例えば、本来は以下であるべきです。

```text
注文合計金額 = 注文明細の小計の合計
```

しかし、`OrderItem` だけが勝手に変更されると、注文合計金額と明細が一致しなくなるかもしれません。

そこで、Aggregate では外部から直接内部要素を操作させず、Aggregate Root を通して操作します。

---

#### ■ Aggregate Root

Aggregate の入口となる Entity を Aggregate Root と呼びます。

注文の例では、`Order` が Aggregate Root です。

```text
Order ← Aggregate Root
├── OrderItem
└── OrderItem
```

外部からは `OrderItem` を直接操作せず、必ず `Order` を通して操作します。

```java
order.addItem(productId, quantity, price);
order.confirm();
order.cancel();
```

このようにすると、`Order` が注文全体の整合性を守れます。

---

#### ■ Aggregate のルール

Aggregate には、以下のルールがあります。

- 外部から操作できるのは Aggregate Root のみ
- Aggregate 内部の整合性は Root が守る
- Repository は Aggregate Root 単位で扱う
- Aggregate をまたぐ強い整合性はなるべく避ける
- 他の Aggregate は、オブジェクト参照ではなく ID で参照することが多い

---

#### Aggregate の例

```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

public class Order {

    private final OrderId id;
    private final List<OrderItem> items = new ArrayList<>();
    private OrderStatus status = OrderStatus.DRAFT;

    public Order(OrderId id) {
        if (id == null) {
            throw new IllegalArgumentException("注文IDは必須です");
        }
        this.id = id;
    }

    public void addItem(ProductId productId, Quantity quantity, Money price) {
        if (status != OrderStatus.DRAFT) {
            throw new IllegalStateException("未確定の注文にしか商品を追加できません");
        }

        items.add(new OrderItem(productId, quantity, price));
    }

    public Money totalPrice() {
        Money total = Money.zero();

        for (OrderItem item : items) {
            total = total.add(item.subtotal());
        }

        return total;
    }

    public List<OrderItem> getItems() {
        return Collections.unmodifiableList(items);
    }
}
```

この設計では、`OrderItem` を外部から直接追加・変更せず、必ず `Order` 経由で操作しています。

---

### Repository

Repository とは、Aggregate を永続化するための抽象です。

> 永続化されている Aggregate を、まるでメモリ上のコレクションのように扱うための窓口

です。

DDD における Repository は、単なる DB アクセスクラスではありません。

ドメインや UseCase から見ると、保存先が MySQL なのか PostgreSQL なのか、ファイルなのかは本質ではありません。

そのため、Repository は interface として定義し、具体的な DB アクセスは Infrastructure に隠します。

```java
import java.util.Optional;

public interface OrderRepository {

    Optional<Order> findById(OrderId id);

    void save(Order order);
}
```

---

#### Repository Interface はどこに置くのか

実務では、Repository Interface をどこに置くかは設計方針によって分かれます。

主に以下の 2 パターンがあります。

| 置き場所       | 考え方                                                 |
| -------------- | ------------------------------------------------------ |
| Domain 層      | Aggregate を保存・取得する概念もドメインに近いと考える |
| Application 層 | UseCase が必要とする永続化の口として考える             |

このドキュメントでは、説明をシンプルにするために `domain.repository` に置きます。

重要なのは、どこに置くかよりも以下です。

> Domain Model が DB や ORM の詳細に依存しないこと

---

#### ■ Repository でやってはいけないこと

Repository に業務ロジックを書いてはいけません。

悪い例：

```java
public class OrderRepository {

    public void save(Order order) {
        if (order.totalPrice().isGreaterThan(new Money(10000))) {
            // 送料無料にする
        }

        // DB に保存する
    }
}
```

送料無料の判断は業務ルールです。

Repository ではなく、Entity / Domain Service / UseCase など、ルールの内容に合った場所へ置くべきです。

Repository の責務は、あくまで保存・取得です。

---

### Domain Service

Domain Service とは、Entity や Value Object に自然に置けないドメインルールを表現するものです。

> 特定の Entity ひとつに押し込むと不自然な業務ルールを表すためのもの

です。

---

#### ■ Domain Service が必要になる例

例えば、ユーザー同士のマッチング判定を考えます。

```text
ユーザー A とユーザー B がマッチング可能か？
```

このルールは、User A だけの責務でも、User B だけの責務でもありません。

このような場合、Domain Service として表現できます。

```java
public class MatchingService {

    public boolean canMatch(User userA, User userB) {
        if (userA == null || userB == null) {
            throw new IllegalArgumentException("ユーザーは必須です");
        }

        return userA.isActive() && userB.isActive();
    }
}
```

---

#### ■ Domain Service の注意点

Domain Service は便利ですが、使いすぎると何でも入る Service になります。

悪い例：

```text
UserDomainService
├── register()
├── login()
├── changeEmail()
├── follow()
├── withdraw()
└── updateProfile()
```

これは DDD ではなく、従来の Service 肥大化と同じです。

Domain Service は、Entity や Value Object に置けない純粋なドメインルールに限定することが大事です。

---

### Factory

Factory とは、複雑なオブジェクト生成を担当するものです。

> オブジェクト生成のルールが複雑なときに、生成処理をまとめるためのもの

です。

---

#### ■ Factory が必要になる例

注文作成時に以下が必要な場合を考えます。

- 注文 ID の生成
- 注文番号の生成
- 初期ステータス設定
- 注文明細の作成
- 初期金額計算

このような生成処理を UseCase に直接書くと、UseCase が複雑になります。

その場合、Factory を使います。

```java
import java.util.List;
import java.util.UUID;

public class OrderFactory {

    public Order create(List<OrderItemRequest> itemRequests) {
        OrderId orderId = new OrderId(UUID.randomUUID().toString());
        Order order = new Order(orderId);

        for (OrderItemRequest request : itemRequests) {
            order.addItem(
                    new ProductId(request.getProductId()),
                    new Quantity(request.getQuantity()),
                    new Money(request.getUnitPriceYen())
            );
        }

        return order;
    }
}
```

Factory は必須ではありません。

生成処理がシンプルなら、無理に作る必要はありません。

---

### Domain Event

Domain Event とは、ドメイン内で発生した重要な出来事を表すものです。

> 業務上意味のある「起きたこと」を表現するオブジェクト

です。

例：

- 注文が確定された
- 支払いが完了した
- 在庫が引き当てられた
- 会員ランクが上がった
- 商品が発送された

---

#### ■ なぜ Domain Event を使うのか

例えば「注文確定」後に、以下の処理が必要だとします。

- 確認メールを送る
- 在庫を引き当てる
- ポイントを付与する
- 管理画面に通知する

これらをすべて `confirmOrder()` の中に直接書くと、注文確定処理が肥大化します。

```text
OrderConfirmed
├── SendOrderMail
├── ReserveStock
├── AddPoint
└── NotifyAdmin
```

そこで、注文確定時に `OrderConfirmed` というイベントを発行します。

これにより、注文確定そのものと、その後に行う処理を分けられます。

---

#### Domain Event のシンプルな例

```java
import java.time.LocalDateTime;

public class OrderConfirmedEvent {

    private final OrderId orderId;
    private final LocalDateTime occurredAt;

    public OrderConfirmedEvent(OrderId orderId) {
        this.orderId = orderId;
        this.occurredAt = LocalDateTime.now();
    }

    public OrderId getOrderId() {
        return orderId;
    }

    public LocalDateTime getOccurredAt() {
        return occurredAt;
    }
}
```

Domain Event は、システム連携や非同期処理と相性が良いです。

ただし、初学者のうちは無理に使わなくても問題ありません。

まずは Entity / Value Object / Aggregate / Repository / UseCase の役割を理解することが優先です。

---

### Application Service / UseCase

Application Service、または UseCase とは、アプリケーションとしての操作手順を表すものです。

> ユーザーや外部システムからの要求に対して、処理の流れを組み立てる場所

です。

---

#### ■ UseCase の役割

UseCase は、以下のような流れを管理します。

1. 入力を受け取る
2. 必要な Value Object を作る
3. Repository から Aggregate を取得する
4. Domain Model のメソッドを呼ぶ
5. Repository に保存する
6. 必要に応じて外部サービスを呼ぶ
7. 結果を返す

例：

```java
public class ConfirmOrderUseCase {

    private final OrderRepository orderRepository;

    public ConfirmOrderUseCase(OrderRepository orderRepository) {
        this.orderRepository = orderRepository;
    }

    public void execute(ConfirmOrderCommand command) {
        OrderId orderId = new OrderId(command.getOrderId());

        Order order = orderRepository.findById(orderId)
                .orElseThrow(() -> new IllegalArgumentException("注文が存在しません"));

        order.confirm();

        orderRepository.save(order);
    }
}
```

ここで重要なのは、UseCase が注文確定の細かい業務ルールを知らないことです。

UseCase は `order.confirm()` を呼ぶだけです。

注文を確定できる条件は、`Order` の中に書きます。

---

#### ■ UseCase に書くもの

UseCase に書くものは、主に以下です。

- 処理の流れ
- Repository の呼び出し
- Transaction 制御
- 認可チェック
- 外部サービス呼び出し
- Domain Model の呼び出し
- 入力値から Value Object への変換

---

#### ■ UseCase に書かないもの

UseCase に書かないものは、主に以下です。

- Entity に置ける業務ルール
- Value Object に置けるバリデーション
- DB アクセスの詳細
- HTTP Request / Response の処理
- UI の表示制御

UseCase は「処理の流れ」を書く場所です。

業務ルールそのものは、できるだけ Domain Model に寄せます。

---

### Infrastructure

Infrastructure とは、技術詳細を扱う層です。

例：

- DB
- ORM
- 外部 API
- メール送信
- 決済サービス
- ファイルストレージ
- メッセージキュー

DDD では、ドメインモデルが Infrastructure に依存しないようにします。

---

#### Infrastructure の例

```java
import java.util.HashMap;
import java.util.Map;
import java.util.Optional;

public class InMemoryOrderRepository implements OrderRepository {

    private final Map<OrderId, Order> store = new HashMap<>();

    @Override
    public Optional<Order> findById(OrderId id) {
        return Optional.ofNullable(store.get(id));
    }

    @Override
    public void save(Order order) {
        store.put(order.getId(), order);
    }
}
```

これは学習用の実装です。

実務では、この部分が MySQL、PostgreSQL、JPA、MyBatis、外部 API などに置き換わります。

それでも、`Order` や `ConfirmOrderUseCase` は DB の詳細を知りません。

これが依存関係を分けるメリットです。

---

## 7. DDD とクリーンアーキテクチャの違い

DDD とクリーンアーキテクチャは、よく一緒に語られます。

しかし、同じものではありません。

---

### DDD

DDD は、業務をどう理解し、どうモデル化するかの設計思想です。

> DDD = 業務理解とモデル化の思想

関心の中心は、業務ルールです。

- 業務の言葉をどう整理するか
- 境界をどう分けるか
- Entity や Value Object をどう設計するか
- 業務ルールをどこに置くか

を考えます。

---

### クリーンアーキテクチャ

クリーンアーキテクチャは、依存関係をどう整理し、技術詳細から業務ロジックを守るかの設計思想です。

> クリーンアーキテクチャ = 依存関係と責務分離の構造

関心の中心は、依存関係です。

- Domain を DB に依存させない
- UseCase を Controller に依存させない
- Infrastructure を外側に置く
- 技術詳細を交換しやすくする

を考えます。

---

### 関係性

実務では、DDD とクリーンアーキテクチャを組み合わせることが多いです。

```text
DDD で業務モデルを設計する
        ↓
クリーンアーキテクチャで配置する
```

DDD は「中身をどう設計するか」。

クリーンアーキテクチャは「それをどこに置き、どう依存させるか」。

---

### 比較表

| 観点       | DDD                                  | クリーンアーキテクチャ                                      |
| ---------- | ------------------------------------ | ----------------------------------------------------------- |
| 主な関心   | 業務理解・モデル化                   | 依存関係・責務分離                                          |
| 中心       | ドメインモデル                       | ユースケースと依存方向                                      |
| 目的       | 業務ルールを正しく表現する           | 技術詳細から業務ロジックを守る                              |
| 扱うもの   | Entity, Value Object, Aggregate など | Controller, UseCase, Interface Adapter, Infrastructure など |
| 使いどころ | 業務が複雑なシステム                 | 変更に強い構造を作りたいシステム                            |

---

### よくある誤解

#### 誤解 1：DDD = クリーンアーキテクチャ

違います。

DDD は業務モデリングの考え方です。

クリーンアーキテクチャは依存関係を整理する考え方です。

---

#### 誤解 2：DDD をやれば必ずクリーンアーキテクチャになる

なりません。

DDD はモデルの設計思想であり、レイヤー構成を必ず指定するものではありません。

ただし、DDD で作ったドメインモデルを守るために、クリーンアーキテクチャの考え方が役立つことは多いです。

---

#### 誤解 3：クリーンアーキテクチャを使えば DDD になる

これも違います。

フォルダを `domain`、`application`、`infrastructure` に分けても、業務ルールが Service に散らばっていたら DDD とは言いにくいです。

---

## 8. 実務での DDD 設計手順

DDD を実務で使う場合、いきなり Entity や Value Object を作り始めるのはおすすめしません。

以下の順番で考えると整理しやすいです。

---

### 1. 業務を理解する

まず、対象業務を理解します。

考えるべきこと：

- 誰が使うのか
- 何をしたいのか
- どんなルールがあるのか
- 何が重要なのか
- どこが複雑なのか
- 何がよく変更されるのか

DDD はコードから始めるのではなく、業務理解から始めます。

---

### 2. 業務用語を集める

業務担当者や仕様書で使われている言葉を集めます。

例：

- 注文
- 注文明細
- 注文確定
- 注文キャンセル
- 支払い完了
- 在庫引当
- 配送準備
- 返品
- 返金
- クーポン適用

これがユビキタス言語の材料になります。

---

### 3. コンテキストを分ける

同じ言葉が違う意味で使われていないかを確認します。

例：「商品」

販売側では：

> ユーザーに販売する対象

在庫側では：

> 倉庫に存在する管理対象

配送側では：

> 梱包・配送する物品

同じ「商品」でも意味が違う可能性があります。

この場合、無理に 1 つの `Product` モデルにまとめると複雑になります。

---

### 4. 重要なルールを見つける

業務上重要なルールを洗い出します。

例：

- 在庫がない商品は購入できない
- 支払い済みの注文だけ配送できる
- キャンセル済みの注文は再度キャンセルできない
- 有効期限切れのクーポンは使えない
- 会員ランクによって割引率が変わる

このようなルールが、ドメインモデルに入る候補になります。

---

### 5. ルールの置き場所を決める

見つけたルールをどこに置くかを考えます。

| 置き場所       | 向いているルール                             |
| -------------- | -------------------------------------------- |
| Value Object   | 値そのものの制約                             |
| Entity         | その Entity 自身の状態変更ルール             |
| Aggregate      | まとまり全体の整合性ルール                   |
| Domain Service | 特定の Entity に置くと不自然なドメインルール |
| UseCase        | 処理の流れ、トランザクション、外部連携       |

---

### 6. UseCase を定義する

ユーザーやシステムが行う操作を UseCase として定義します。

例：

- `RegisterUserUseCase`
- `ConfirmOrderUseCase`
- `CancelOrderUseCase`
- `ApplyCouponUseCase`
- `ShipOrderUseCase`

UseCase は、画面や API に近い操作単位になることが多いです。

---

### 7. Aggregate を決める

どの単位で整合性を守る必要があるかを考えます。

例：注文

```text
Order
├── OrderItem
└── OrderItem
```

注文と注文明細は同時に整合性を守りたいので、同じ Aggregate にする候補になります。

一方、注文と在庫は別 Aggregate にすることが多いです。

なぜなら、注文と在庫を常に 1 つの巨大な Aggregate にすると、変更範囲が大きくなりすぎるからです。

---

### 8. Repository を定義する

Aggregate を取得・保存するための Repository Interface を定義します。

```java
import java.util.Optional;

public interface OrderRepository {

    Optional<Order> findById(OrderId id);

    void save(Order order);
}
```

Repository は Aggregate Root 単位で定義するのが基本です。

---

### 9. Infrastructure で実装する

DB や ORM に依存する具体実装は Infrastructure 層に置きます。

```java
public class JpaOrderRepository implements OrderRepository {

    @Override
    public Optional<Order> findById(OrderId id) {
        // JPA を使った取得処理
        return Optional.empty();
    }

    @Override
    public void save(Order order) {
        // JPA を使った保存処理
    }
}
```

ここで重要なのは、Domain Model に JPA や SQL の詳細を漏らさないことです。

---

### 10. テストする

DDD では、Domain Model のテストが重要です。

例えば、注文確定のルールは `Order` の単体テストで確認できます。

```java
// イメージ
// 商品がない注文は確定できない
// キャンセル済みの注文は確定できない
// 商品がある未確定注文は確定できる
```

業務ルールが Domain Model に集まっていると、DB や HTTP を使わずにテストしやすくなります。

---

## 9. 実装例：注文確定を DDD で実装する

ここでは、EC サイトの「注文を確定する」という処理を例に、DDD の考え方を Java のコードに落とし込みます。

この章の目的は、DDD の用語を覚えることではなく、以下を理解することです。

> 業務ルールをどこに書くべきか

---

### 今回作るもの

今回作る UseCase は、以下です。

> 注文を確定する

業務ルールはシンプルにします。

- 注文には複数の商品明細がある
- 商品明細が 1 件もない注文は確定できない
- すでに確定済みの注文は、再度確定できない
- キャンセル済みの注文は確定できない
- 注文確定時に、注文ステータスを `CONFIRMED` に変更する

---

### まず役割を整理する

今回の実装では、以下のように役割を分けます。

| 要素                      | 役割                                         |
| ------------------------- | -------------------------------------------- |
| `Order`                   | 注文を表す Entity / Aggregate Root           |
| `OrderItem`               | 注文明細を表すオブジェクト                   |
| `OrderStatus`             | 注文状態を表す Enum                          |
| `OrderId`                 | 注文 ID を表す Value Object                  |
| `ProductId`               | 商品 ID を表す Value Object                  |
| `Money`                   | 金額を表す Value Object                      |
| `Quantity`                | 数量を表す Value Object                      |
| `OrderRepository`         | 注文 Aggregate を保存・取得する抽象          |
| `ConfirmOrderUseCase`     | 注文確定の処理手順を表す Application Service |
| `InMemoryOrderRepository` | Repository の仮実装                          |

ポイントは、以下です。

> 注文を確定できるかどうかの業務ルールは、UseCase ではなく Order に書く

なぜなら、「注文を確定できる条件」はアプリケーション都合ではなく、注文という業務概念そのもののルールだからです。

---

### ディレクトリ構成例

実務では、以下のように分けると理解しやすいです。

```text
src/main/java/com/example/order
├── domain
│   ├── model
│   │   ├── Order.java
│   │   ├── OrderItem.java
│   │   ├── OrderStatus.java
│   │   ├── OrderId.java
│   │   ├── ProductId.java
│   │   ├── Money.java
│   │   └── Quantity.java
│   └── repository
│       └── OrderRepository.java
│
├── application
│   ├── ConfirmOrderUseCase.java
│   └── ConfirmOrderCommand.java
│
└── infrastructure
    └── InMemoryOrderRepository.java
```

DDD では、特に以下の依存関係が重要です。

```text
Application
    ↓
Domain

Infrastructure
    ↓
Domain
```

Domain は、Application や Infrastructure に依存しません。

つまり、`Order` の中に DB、HTTP、Controller、ORM の知識を入れません。

---

### Value Object：OrderId

`OrderId` は注文 ID を表す Value Object です。

ただの `String` でも実装できますが、DDD では業務上意味のある値を専用の型にすることが多いです。

```java
package com.example.order.domain.model;

import java.util.Objects;

public class OrderId {

    private final String value;

    public OrderId(String value) {
        if (value == null || value.isBlank()) {
            throw new IllegalArgumentException("注文IDは必須です");
        }
        this.value = value;
    }

    public String getValue() {
        return value;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (!(o instanceof OrderId)) return false;
        OrderId orderId = (OrderId) o;
        return Objects.equals(value, orderId.value);
    }

    @Override
    public int hashCode() {
        return Objects.hash(value);
    }

    @Override
    public String toString() {
        return value;
    }
}
```

このクラスを作ることで、`String orderId` ではなく、`OrderId orderId` として扱えます。

コードを読んだときに「これはただの文字列ではなく、注文 ID なんだ」とわかります。

---

### Value Object：ProductId

`ProductId` は商品 ID を表す Value Object です。

```java
package com.example.order.domain.model;

import java.util.Objects;

public class ProductId {

    private final String value;

    public ProductId(String value) {
        if (value == null || value.isBlank()) {
            throw new IllegalArgumentException("商品IDは必須です");
        }
        this.value = value;
    }

    public String getValue() {
        return value;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (!(o instanceof ProductId)) return false;
        ProductId productId = (ProductId) o;
        return Objects.equals(value, productId.value);
    }

    @Override
    public int hashCode() {
        return Objects.hash(value);
    }

    @Override
    public String toString() {
        return value;
    }
}
```

`OrderId` と似ていますが、意味が違うため別クラスにしています。

`String` のままだと、注文 ID と商品 ID を間違えて渡してもコンパイルできてしまいます。

Value Object にすると、意味の違いを型で表現できます。

---

### Value Object：Money

`Money` は金額を表す Value Object です。

```java
package com.example.order.domain.model;

import java.util.Objects;

public class Money {

    private final int yen;

    public Money(int yen) {
        if (yen < 0) {
            throw new IllegalArgumentException("金額は0円以上である必要があります");
        }
        this.yen = yen;
    }

    public int getYen() {
        return yen;
    }

    public Money add(Money other) {
        if (other == null) {
            throw new IllegalArgumentException("加算する金額は必須です");
        }
        return new Money(this.yen + other.yen);
    }

    public Money multiply(int count) {
        if (count < 0) {
            throw new IllegalArgumentException("掛ける数は0以上である必要があります");
        }
        return new Money(this.yen * count);
    }

    public static Money zero() {
        return new Money(0);
    }

    @Override
    public String toString() {
        return yen + "円";
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (!(o instanceof Money)) return false;
        Money money = (Money) o;
        return yen == money.yen;
    }

    @Override
    public int hashCode() {
        return Objects.hash(yen);
    }
}
```

Money を `int` のまま扱うと、以下のような問題が起きやすいです。

```java
int price = -1000;
```

金額としてありえない値が簡単に作れてしまいます。

`Money` にすることで、「0 円未満の金額は作れない」というルールを一箇所に閉じ込められます。

> 実務で厳密な金額計算を扱う場合は、丸め誤差や通貨単位も考慮するため、`BigDecimal` や通貨コードを使う設計も検討します。  
> このドキュメントでは初学者向けに、円の整数としてシンプルに表現しています。

---

### Value Object：Quantity

`Quantity` は数量を表す Value Object です。

```java
package com.example.order.domain.model;

import java.util.Objects;

public class Quantity {

    private final int value;

    public Quantity(int value) {
        if (value <= 0) {
            throw new IllegalArgumentException("数量は1以上である必要があります");
        }
        this.value = value;
    }

    public int getValue() {
        return value;
    }

    @Override
    public String toString() {
        return String.valueOf(value);
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (!(o instanceof Quantity)) return false;
        Quantity quantity = (Quantity) o;
        return value == quantity.value;
    }

    @Override
    public int hashCode() {
        return Objects.hash(value);
    }
}
```

数量は 0 やマイナスになると業務的におかしいです。

そのため、生成時点で不正な値を防ぎます。

---

### Entity / Aggregate 内部の要素：OrderItem

`OrderItem` は注文明細を表します。

```java
package com.example.order.domain.model;

public class OrderItem {

    private final ProductId productId;
    private final Quantity quantity;
    private final Money unitPrice;

    public OrderItem(ProductId productId, Quantity quantity, Money unitPrice) {
        if (productId == null) {
            throw new IllegalArgumentException("商品IDは必須です");
        }
        if (quantity == null) {
            throw new IllegalArgumentException("数量は必須です");
        }
        if (unitPrice == null) {
            throw new IllegalArgumentException("単価は必須です");
        }

        this.productId = productId;
        this.quantity = quantity;
        this.unitPrice = unitPrice;
    }

    public Money subtotal() {
        return unitPrice.multiply(quantity.getValue());
    }

    public ProductId getProductId() {
        return productId;
    }

    public Quantity getQuantity() {
        return quantity;
    }

    public Money getUnitPrice() {
        return unitPrice;
    }
}
```

`subtotal()` は小計を計算するメソッドです。

```text
単価 × 数量 = 小計
```

これは注文明細に関するルールなので、`OrderItem` の中に置いています。

---

### Enum：OrderStatus

注文の状態を表します。

```java
package com.example.order.domain.model;

public enum OrderStatus {
    DRAFT,
    CONFIRMED,
    CANCELED
}
```

今回はシンプルに 3 つだけ用意します。

| ステータス  | 意味           |
| ----------- | -------------- |
| `DRAFT`     | 未確定         |
| `CONFIRMED` | 確定済み       |
| `CANCELED`  | キャンセル済み |

---

### Entity / Aggregate Root：Order

`Order` は注文を表す Entity であり、Aggregate Root です。

注文に関する重要なルールは、できるだけ `Order` の中に閉じ込めます。

```java
package com.example.order.domain.model;

import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

public class Order {

    private final OrderId id;
    private final List<OrderItem> items;
    private OrderStatus status;

    public Order(OrderId id) {
        if (id == null) {
            throw new IllegalArgumentException("注文IDは必須です");
        }

        this.id = id;
        this.items = new ArrayList<>();
        this.status = OrderStatus.DRAFT;
    }

    public void addItem(ProductId productId, Quantity quantity, Money unitPrice) {
        if (status != OrderStatus.DRAFT) {
            throw new IllegalStateException("未確定の注文にしか商品を追加できません");
        }

        OrderItem item = new OrderItem(productId, quantity, unitPrice);
        items.add(item);
    }

    public void confirm() {
        if (items.isEmpty()) {
            throw new IllegalStateException("商品が1件もない注文は確定できません");
        }

        if (status == OrderStatus.CONFIRMED) {
            throw new IllegalStateException("すでに確定済みの注文です");
        }

        if (status == OrderStatus.CANCELED) {
            throw new IllegalStateException("キャンセル済みの注文は確定できません");
        }

        status = OrderStatus.CONFIRMED;
    }

    public void cancel() {
        if (status == OrderStatus.CONFIRMED) {
            throw new IllegalStateException("確定済みの注文はこの例ではキャンセルできません");
        }

        if (status == OrderStatus.CANCELED) {
            throw new IllegalStateException("すでにキャンセル済みの注文です");
        }

        status = OrderStatus.CANCELED;
    }

    public Money totalPrice() {
        Money total = Money.zero();

        for (OrderItem item : items) {
            total = total.add(item.subtotal());
        }

        return total;
    }

    public OrderId getId() {
        return id;
    }

    public OrderStatus getStatus() {
        return status;
    }

    public List<OrderItem> getItems() {
        return Collections.unmodifiableList(items);
    }
}
```

ここで重要なのは、`confirm()` の中に業務ルールが書かれていることです。

```java
public void confirm() {
    if (items.isEmpty()) {
        throw new IllegalStateException("商品が1件もない注文は確定できません");
    }

    if (status == OrderStatus.CONFIRMED) {
        throw new IllegalStateException("すでに確定済みの注文です");
    }

    if (status == OrderStatus.CANCELED) {
        throw new IllegalStateException("キャンセル済みの注文は確定できません");
    }

    status = OrderStatus.CONFIRMED;
}
```

このルールは、UseCase に書くよりも `Order` に書いた方がよいです。

なぜなら、「注文が確定できる条件」は注文そのもののルールだからです。

---

### なぜ items を直接変更させないのか

`Order` では、外部に `items` をそのまま渡していません。

```java
public List<OrderItem> getItems() {
    return Collections.unmodifiableList(items);
}
```

これは、外部から勝手に注文明細を変更されないようにするためです。

悪い例：

```java
order.getItems().clear();
```

もしこれができてしまうと、注文の整合性が壊れます。

DDD の Aggregate では、内部の状態変更は Aggregate Root 経由にします。

今回であれば、注文明細を追加したいなら必ず以下を使います。

```java
order.addItem(productId, quantity, unitPrice);
```

つまり、`OrderItem` を外から直接操作させず、`Order` が注文全体の整合性を守ります。

---

### Repository：OrderRepository

Repository は、Aggregate を保存・取得するための抽象です。

```java
package com.example.order.domain.repository;

import com.example.order.domain.model.Order;
import com.example.order.domain.model.OrderId;

import java.util.Optional;

public interface OrderRepository {

    Optional<Order> findById(OrderId id);

    void save(Order order);
}
```

ここでは interface だけを定義しています。

重要なのは、Repository の interface には DB の詳細を書かないことです。

例えば、以下のようなことは書きません。

```java
// 悪い例
void saveToMySql(Order order);
```

MySQL は技術詳細です。

ドメインや UseCase から見ると、保存先が MySQL なのか PostgreSQL なのか、ファイルなのかは本質ではありません。

---

### Application Service / UseCase：ConfirmOrderCommand

UseCase に渡す入力値を表します。

```java
package com.example.order.application;

public class ConfirmOrderCommand {

    private final String orderId;

    public ConfirmOrderCommand(String orderId) {
        if (orderId == null || orderId.isBlank()) {
            throw new IllegalArgumentException("注文IDは必須です");
        }

        this.orderId = orderId;
    }

    public String getOrderId() {
        return orderId;
    }
}
```

Controller から受け取った値を、そのまま UseCase に渡すためのクラスです。

`String` を受け取っているのは、外部入力に近い層だからです。

UseCase の中で `OrderId` に変換します。

---

### Application Service / UseCase：ConfirmOrderUseCase

UseCase は、アプリケーションとしての処理手順を管理します。

```java
package com.example.order.application;

import com.example.order.domain.model.Order;
import com.example.order.domain.model.OrderId;
import com.example.order.domain.repository.OrderRepository;

public class ConfirmOrderUseCase {

    private final OrderRepository orderRepository;

    public ConfirmOrderUseCase(OrderRepository orderRepository) {
        if (orderRepository == null) {
            throw new IllegalArgumentException("OrderRepositoryは必須です");
        }
        this.orderRepository = orderRepository;
    }

    public void execute(ConfirmOrderCommand command) {
        OrderId orderId = new OrderId(command.getOrderId());

        Order order = orderRepository.findById(orderId)
                .orElseThrow(() -> new IllegalArgumentException("注文が存在しません"));

        order.confirm();

        orderRepository.save(order);
    }
}
```

UseCase がやっていることはシンプルです。

1. 入力値から `OrderId` を作る
2. Repository から `Order` を取得する
3. `order.confirm()` を呼ぶ
4. Repository に保存する

ここで大事なのは、UseCase が注文確定の細かい業務ルールを知らないことです。

UseCase は、以下のようなルールを直接判定していません。

```java
if (order.getItems().isEmpty()) {
    throw new IllegalStateException("商品が1件もない注文は確定できません");
}
```

このルールは `Order` の責務です。

UseCase はあくまで流れを管理します。

---

### Infrastructure：InMemoryOrderRepository

今回は DB を使わず、メモリ上に保存する Repository を作ります。

```java
package com.example.order.infrastructure;

import com.example.order.domain.model.Order;
import com.example.order.domain.model.OrderId;
import com.example.order.domain.repository.OrderRepository;

import java.util.HashMap;
import java.util.Map;
import java.util.Optional;

public class InMemoryOrderRepository implements OrderRepository {

    private final Map<OrderId, Order> store = new HashMap<>();

    @Override
    public Optional<Order> findById(OrderId id) {
        return Optional.ofNullable(store.get(id));
    }

    @Override
    public void save(Order order) {
        store.put(order.getId(), order);
    }
}
```

これはあくまで学習用の実装です。

実務では、この部分が MySQL、PostgreSQL、JPA、MyBatis、外部 API などに置き換わります。

しかし、`Order` や `ConfirmOrderUseCase` は DB の詳細を知りません。

これが依存関係を分けるメリットです。

---

### 動作確認用のコード

最後に、簡単に動かしてみます。

```java
package com.example.order;

import com.example.order.application.ConfirmOrderCommand;
import com.example.order.application.ConfirmOrderUseCase;
import com.example.order.domain.model.Money;
import com.example.order.domain.model.Order;
import com.example.order.domain.model.OrderId;
import com.example.order.domain.model.ProductId;
import com.example.order.domain.model.Quantity;
import com.example.order.infrastructure.InMemoryOrderRepository;

public class Main {

    public static void main(String[] args) {
        InMemoryOrderRepository orderRepository = new InMemoryOrderRepository();

        Order order = new Order(new OrderId("order-001"));
        order.addItem(
                new ProductId("product-001"),
                new Quantity(2),
                new Money(1000)
        );

        orderRepository.save(order);

        ConfirmOrderUseCase useCase = new ConfirmOrderUseCase(orderRepository);

        useCase.execute(new ConfirmOrderCommand("order-001"));

        Order confirmedOrder = orderRepository.findById(new OrderId("order-001"))
                .orElseThrow();

        System.out.println(confirmedOrder.getStatus());
        System.out.println(confirmedOrder.totalPrice());
    }
}
```

実行結果のイメージです。

```text
CONFIRMED
2000円
```

---

### この実装のどこが DDD なのか

この実装では、以下のように責務が分かれています。

| 処理                               | 書く場所                 |
| ---------------------------------- | ------------------------ |
| 注文 ID が空でないこと             | `OrderId`                |
| 商品 ID が空でないこと             | `ProductId`              |
| 金額が 0 円以上であること          | `Money`                  |
| 数量が 1 以上であること            | `Quantity`               |
| 注文明細の小計計算                 | `OrderItem`              |
| 注文を確定できる条件               | `Order`                  |
| 注文を取得して確定して保存する流れ | `ConfirmOrderUseCase`    |
| 注文の保存方法                     | `OrderRepository` の実装 |

DDD では、何でもかんでも Service に書くのではなく、ルールに一番近い場所へ処理を置きます。

---

### 悪い例：UseCase に業務ルールを詰め込む

例えば、以下のように UseCase に業務ルールを全部書くと、DDD らしさが薄くなります。

```java
public void execute(ConfirmOrderCommand command) {
    OrderId orderId = new OrderId(command.getOrderId());

    Order order = orderRepository.findById(orderId)
            .orElseThrow(() -> new IllegalArgumentException("注文が存在しません"));

    if (order.getItems().isEmpty()) {
        throw new IllegalStateException("商品が1件もない注文は確定できません");
    }

    if (order.getStatus() == OrderStatus.CONFIRMED) {
        throw new IllegalStateException("すでに確定済みの注文です");
    }

    if (order.getStatus() == OrderStatus.CANCELED) {
        throw new IllegalStateException("キャンセル済みの注文は確定できません");
    }

    order.setStatus(OrderStatus.CONFIRMED); // このような setter があると不正変更されやすい

    orderRepository.save(order);
}
```

この実装の問題は、注文のルールが UseCase に漏れていることです。

もし別の UseCase でも注文確定が必要になったら、同じ条件分岐が重複する可能性があります。

DDD では、注文に関するルールは `Order` に寄せます。

```java
order.confirm();
```

この一行で「注文を確定する」という業務操作を表現できるのが理想です。

---

### DDD 実装で意識すること

DDD で実装するときは、以下を意識するとよいです。

- 業務ルールは、できるだけ業務概念に近いクラスへ置く
- UseCase は処理の流れを書く場所
- Entity は状態と業務ルールを持つ
- Value Object は値の制約を守る
- Aggregate Root は内部の整合性を守る
- Repository は保存・取得の抽象
- Infrastructure は DB や外部 API などの技術詳細を担当する

逆に、以下のような状態は避けます。

- UseCase に業務ルールが集中する
- Repository に業務ルールを書く
- Entity が getter / setter だけになる
- Value Object を作りすぎて逆に読みにくくなる
- DDD のためにクラスを増やすことが目的になる

DDD の目的は、難しいクラスをたくさん作ることではありません。

> 業務ルールを正しい場所に置き、コードを読めば業務がわかる状態にすること

これが DDD の目的です。

---

## 10. アンチパターン

ここでは、DDD を実務で使うときによくある失敗を整理します。

---

### 1. DDD をフォルダ構成だと思う

DDD はフォルダ構成ではありません。

`domain`、`application`、`infrastructure` というフォルダを作っただけでは DDD ではありません。

重要なのは、業務ルールが正しく表現されているかです。

悪い例：

```text
src
├── domain
│   └── Order.java  // getter / setter だけ
├── application
│   └── OrderService.java  // 業務ルールが全部ここ
└── infrastructure
    └── OrderRepository.java
```

フォルダは DDD っぽく見えます。

しかし、業務ルールが `OrderService` に集中しているなら、DDD とは言いにくいです。

---

### 2. すべてを Entity にする

何でも Entity にすればよいわけではありません。

ID で同一性を判断する必要がないものは、Value Object の方が適していることがあります。

例：

- Email
- Money
- Address
- Quantity
- DateRange

これらは、ID で管理するより、値そのものとして扱う方が自然です。

---

### 3. Value Object を作りすぎる

Value Object は便利ですが、作りすぎると逆に読みにくくなります。

例えば、何の制約も業務的意味もない値まで全部 Value Object にすると、クラスが増えすぎます。

```java
UserNameText
UserMemoText
UserDescriptionText
UserProfileMessageText
```

このように、区別する意味が薄いものまで型にすると、開発効率が落ちることがあります。

Value Object にする基準は、以下です。

- 業務上の意味がある
- 値に制約がある
- 型として区別したい
- バリデーションを一箇所にまとめたい
- 間違えて渡すと問題になる

---

### 4. Domain Service に何でも入れる

Domain Service は便利ですが、使いすぎると従来の Service と同じになります。

悪い例：

```text
OrderDomainService
├── createOrder()
├── confirmOrder()
├── cancelOrder()
├── addItem()
├── changeAddress()
└── calculateTotal()
```

このようになると、`Order` が持つべき業務ルールまで Domain Service に流れてしまいます。

Domain Service は、Entity や Value Object に置けないドメインルールに限定します。

---

### 5. UseCase に業務ルールを詰め込みすぎる

UseCase は処理の流れを管理する場所です。

Entity や Value Object に置けるルールは、なるべく Domain Model に寄せます。

悪い例：

```java
public void execute(ConfirmOrderCommand command) {
    Order order = orderRepository.findById(new OrderId(command.getOrderId()))
            .orElseThrow();

    // 注文の業務ルールが UseCase に漏れている
    if (order.getItems().isEmpty()) {
        throw new IllegalStateException("商品がありません");
    }

    if (order.getStatus() == OrderStatus.CANCELED) {
        throw new IllegalStateException("キャンセル済みです");
    }

    orderRepository.save(order);
}
```

良い例：

```java
public void execute(ConfirmOrderCommand command) {
    Order order = orderRepository.findById(new OrderId(command.getOrderId()))
            .orElseThrow();

    order.confirm();

    orderRepository.save(order);
}
```

---

### 6. Repository に業務ルールを書く

Repository は保存・取得の責務を持ちます。

業務ルールを書く場所ではありません。

悪い例：

```java
public void save(Order order) {
    if (order.totalPrice().getYen() >= 10000) {
        order.applyFreeShipping();
    }

    // DB 保存
}
```

送料無料の判定は、保存処理ではなく業務ルールです。

Repository に書くと、DB 保存しない場面ではそのルールが適用されない可能性があります。

---

### 7. Entity と DB テーブルを完全に一致させようとする

Entity は業務上の概念です。

DB テーブルは保存形式です。

この 2 つは一致することもありますが、必ず一致するわけではありません。

例えば、1 つの `Order` Aggregate が、DB では以下のように分かれることがあります。

```text
orders
order_items
```

逆に、複数の概念が 1 つのテーブルに保存されることもあります。

DDD では、DB 都合ではなく業務概念を中心にモデルを考えます。

---

## 11. DDD を使うべきケース・使わなくてもよいケース

DDD は強力ですが、すべての開発に必要なわけではありません。

DDD は複雑な業務ルールを扱うための設計思想です。

単純な CRUD にまで無理に DDD を適用すると、かえって複雑になります。

---

### DDD を使うべきケース

DDD が向いているのは、以下のようなケースです。

- 業務ルールが複雑
- 仕様変更が多い
- 業務用語が多い
- 部門ごとに言葉の意味が違う
- Service が肥大化している
- 同じような条件分岐が増えている
- 業務担当者との認識ズレが起きやすい
- 長期的に育てるシステム

例：

- EC サイトの注文・決済・在庫
- 金融システム
- 保険システム
- 物流システム
- 予約システム
- 契約管理システム

---

### DDD を使わなくてもよいケース

DDD があまり向いていない、または優先度が低いケースもあります。

- 単純な CRUD が中心
- 業務ルールがほとんどない
- 短期間で捨てるプロトタイプ
- 管理画面の単純なマスタメンテナンス
- 小規模で変更が少ないアプリケーション

例えば、単純にお知らせを登録・編集・削除するだけなら、DDD を本格的に導入しなくてもよいことが多いです。

---

### DDD を部分的に使うという選択

DDD は、システム全体に一気に導入しなくてもよいです。

特に重要なドメインだけに適用するのも現実的です。

```text
ECサイト
├── 注文        ← DDD をしっかり適用
├── 決済        ← DDD をしっかり適用
├── 在庫        ← DDD をしっかり適用
├── お知らせ    ← 単純な CRUD
└── 管理画面    ← 必要に応じてシンプルに実装
```

DDD は目的ではなく手段です。

複雑さに見合った設計を選ぶことが大切です。

---

## 12. よくある疑問

### Q. DDD はクリーンアーキテクチャと同じですか？

A. 違います。

DDD は、業務をどう理解し、どうモデル化するかの思想です。

クリーンアーキテクチャは、依存関係をどう整理するかの構造です。

ただし、実務では組み合わせて使われることが多いです。

---

### Q. Entity と DB のテーブルは同じですか？

A. 同じとは限りません。

Entity は業務上の概念です。

DB テーブルは保存形式です。

1 つの Entity が複数テーブルに分かれることもあります。

逆に、複数の Entity が 1 つのテーブルに保存されることもあります。

---

### Q. Value Object は必ず作るべきですか？

A. すべての値を Value Object にする必要はありません。

ただし、業務上意味があり、制約を持つ値は Value Object にする価値があります。

例：

- Email
- Money
- Quantity
- DateRange
- Address
- PhoneNumber

逆に、業務的な意味や制約が薄い値まで無理に Value Object にすると、コードが読みにくくなります。

---

### Q. Aggregate はどう決めればいいですか？

A. 整合性を守りたい単位で考えます。

同時に変更される必要があるもの、外部から勝手に変更されると困るものは、同じ Aggregate にまとめる候補になります。

例：注文と注文明細

```text
Order
├── OrderItem
└── OrderItem
```

注文の中の注文明細が勝手に変更されると、注文全体の整合性が壊れます。

そのため、`Order` を Aggregate Root にして、`OrderItem` を内部に持たせる設計が自然です。

---

### Q. UseCase と Domain Service の違いは何ですか？

A. UseCase はアプリケーションの処理手順、Domain Service はドメインルールです。

| 項目                | UseCase                        | Domain Service                           |
| ------------------- | ------------------------------ | ---------------------------------------- |
| 役割                | 処理の流れを管理する           | Entity に置けない業務ルールを表す        |
| 例                  | 注文を取得して確定して保存する | 2 人のユーザーがマッチング可能か判定する |
| 関心                | アプリケーション操作           | 業務ルール                               |
| Repository 呼び出し | よく行う                       | 必要な場合もあるが乱用しない             |

---

### Q. Controller には何を書けばいいですか？

A. HTTP の入出力に関する処理を書きます。

Controller に書くもの：

- Request の受け取り
- パラメータの取り出し
- UseCase の呼び出し
- Response の作成

Controller に書かないもの：

- 業務ルール
- DB アクセスの詳細
- 複雑な状態変更ロジック

Controller は薄く保つのが基本です。

---

### Q. DDD は難しいので、最初から完璧にやるべきですか？

A. 完璧にやろうとしなくて大丈夫です。

最初は以下だけ意識すれば十分です。

- 業務の言葉をコードに使う
- Value Object で不正な値を防ぐ
- Entity に業務ルールを少しずつ寄せる
- UseCase にルールを詰め込みすぎない
- Repository に業務ルールを書かない

DDD は一度で完成させるものではなく、業務理解が深まるたびにモデルを育てていくものです。
