# ドメイン駆動設計

## 目次

## 1.ドメイン駆動設計とは

エリック・エヴァンスが著書『Domain-Driven Design - Tackling Complexity in the Heart of Software』の中で提唱した"ドメイン" の知識にフォーカスした設計手法のこと

複雑な業務知識を正しく理解し、 その理解をコードに反映するための設計思想を目的とする

### ドメインとは

> 「ソフトウェアを使って問題解決しようとしている領域」や「プログラムを適用する対象となる業務領域」のこと

## 2. DDD が必要になる背景

小さなアプリケーションでは、単純な CRUD で十分なことが多い

- ユーザーを登録する
- 商品を保存する
- 記事を更新する

→ この程度であれば、Controller と Service と Repository だけでも大きな問題はない

しかし、実務のシステムはもっと複雑

例 : EC サイト

- 会員ランク
- クーポン
- ポイント
- 在庫引当
- キャンセル
- 返品
- 配送ステータス
- 決済ステータス
- 定期購入
- キャンペーンなど

このような業務ルールが増えると、単純な Service クラスに処理を詰め込むだけでは管理できなくなる

## 3.普通の設計で起きる問題

よくある設計では、以下のような Service が生まれます

- OrderService
- UserService
- ProductService

最初は問題ないが、機能追加が続くと、Service の中に多くの処理が集まる

```
OrderService
├ createOrder()
├ cancelOrder()
├ returnOrder()
├ applyCoupon()
├ calculatePoint()
├ changeShippingAddress()
├ confirmPayment()
├ reserveStock()
└ sendOrderMail()
```

このような状態になると、以下の問題が起きる

- どこに何を書くべきかわからない
- 業務ルールがあちこちに分散する
- 同じような条件分岐が増える
- 修正時の影響範囲が読めない
- テストが難しくなる
- コードを読んでも業務が理解できない

この状態を防ぐために、DDD では業務概念を正しく分解し、コード上に表現します

## 4. DDD の本質

DDD の本質は、次の一文に集約できる

> 業務の言葉・ルール・構造を、そのままコードに反映する

例えば、業務担当者が「注文を確定する」と言っているなら、コードにもそれが表れるべき

悪い例：

```java
order.save()
```

save は技術的な言葉すぎる

良い例：

```java
order.confirm()
```

confirm は業務上の意味を持つ言葉

<u>DDD では、このように 業務で使われる言葉をコードにも反映する ことを重視します</u>

## 5. DDD の 2 つの設計領域

DDD には、大きく分けて 2 つの設計領域がある

```
DDD
├ 戦略的設計
└ 戦術的設計
```

### 戦略的設計

戦略的設計は、システム全体をどう分けるかを考える設計

主に以下を扱う

- [ドメイン](#ドメインとは)
- [サブドメイン](#サブドメイン)
- [ユビキタス言語](#ユビキタス言語)
- [境界づけられたコンテキスト](#境界づけられたコンテキスト)
- [コンテキストマップ](#コンテキストマップ)

戦略的設計は、コードを書く前の設計です。

特に大規模システムでは、戦術的設計よりも重要になることがある

---

### 戦術的設計

戦術的設計は、ドメインモデルをコードとしてどう表現するかを考える設計

主に以下を扱う

- Entity
- Value Object
- Aggregate
- Repository
- Domain Service
- Factory
- Domain Event

---

### ユビキタス言語

ユビキタス言語とは

> 開発者と業務担当者が共通して使う言葉

#### <u>なぜユビキタス言語が重要なのか</u>

業務担当者が「注文確定」と言っているのに、コード上では saveOrder と書かれていたら、認識がずれる

> 業務上の意味：注文確定
>
> コード上の表現：confirmOrder()

このように、業務の言葉とコードの言葉を揃えることで、仕様と実装のズレを減らすことを目的にする

悪い例 :

```java
saveOrder()
updateStatus()
process()
handle()
execute()
```

→ 技術的・抽象的すぎて、業務上何をしているのかわかりにくい

良い例 :

```java
confirmOrder()
cancelOrder()
applyCoupon()
reserveStock()
shipOrder()
```

→ 業務上の意味がコードから読み取れる

---

### 境界づけられたコンテキスト

境界づけられたコンテキストとは

> ある言葉やモデルが同じ意味で通用する範囲のこと

#### <u>なぜ境界が必要なのか</u>

同じ言葉でも、部門や業務領域によって意味が違うことがある

例 : 「顧客」の意味

> 営業部門：まだ契約していない見込み客
>
> 請求部門：請求先として登録された契約済みの相手
>
> サポート部門：問い合わせ履歴を持つ利用者

→ 同じ「顧客」という言葉でも文脈によって意味が異なることがある

悪い例 : すべての文脈で同じ Customer モデルを使う。

```
Customer
├ salesStatus
├ billingAddress
├ supportTickets
├ contractPlan
├ invoiceSetting
└ leadScore
```

→ このようなモデルは、複数の文脈が混ざって肥大化する

```ts
// TODO 具体的に何が肥大化してるか後で聞く
```

良い例 : 文脈ごとにモデルを分ける

```
SalesContext
└ Customer

BillingContext
└ Customer

SupportContext
└ Customer
```

同じ Customer という名前でも、各コンテキスト内では意味が異なるとわかる

---

### サブドメイン

サブドメインとは

> 事業や業務を分解した領域のこと

例えば EC サイトなら、以下のように分けられる

```
ECサイト
├ 注文
├ 決済
├ 配送
├ 在庫
├ 商品管理
├ 顧客管理
└ クーポン
```

DDD では、サブドメインをさらに以下の 3 つに分類します。

```
サブドメイン
├ コアドメイン
├ 支援サブドメイン
└ 汎用サブドメイン
```

---

#### ■ コアドメイン

コアドメインとは

> 事業上もっとも重要で、競争優位につながる領域

例えば、フードデリバリーサービスなら以下がコアドメインになりやすい

- 配達員と注文のマッチング
- 配達時間の最適化
- 需要予測

ここは自社でしっかり設計・実装する価値あり

---

#### ■ 支援サブドメイン

支援サブドメインとは

> コアドメインを支える業務領域

例）

- 管理画面
- 通知
- レポート
- 顧客管理

重要ではあるものの、直接的な競争優位にはなりにくい領域である

#### ■ 汎用サブドメイン

汎用サブドメインとは

> 多くのサービスで共通して必要になる一般的な機能

例）

- 認証
- 決済
- メール送信
- ログ管理

これらは外部サービスや既存ライブラリを活用してもよい領域であるのがポイント

---

### コンテキストマップ

コンテキストマップとは

> 複数の境界づけられたコンテキスト同士の関係を整理したもの

例えば EC サイトでは、以下のような関係がある

```
[注文コンテキスト]
↓
[決済コンテキスト]
↓
[配送コンテキスト]
```

コンテキストマップを作ることで、システム全体の境界と依存関係を把握しやすくなる

## 6.DDD をコードに落とし込むための設計要素

### Entity

Entity とは

> 同一性を持つオブジェクト

つまり、属性が変わっても「同じもの」として扱われる業務オブジェクト

例）

- ユーザー
- 注文
- 商品
- 口座
- 契約

例えばユーザーは、名前やメールアドレスが変わっても同じユーザー

理由 → ユーザー ID によって同一性を判断できるから

#### ■ Entity の特徴

- ID を持つ
- ライフサイクルを持つ
- 状態が変化する
- 業務ルールを持つ

サンプル

```java
class User {

  constructor(
    private readonly id: UserId,
    private email: Email
  ) {}

  changeEmail(newEmail: Email) {
    this.email = newEmail
  }

  getId() {
    return this.id
  }

  getEmail() {
    return this.email
  }
}
```

---

### Value Object

Value Object とは

> 値そのものに意味があるオブジェクト

Entity と違い、ID を持たない

値が同じであれば、同じものとして扱う

例）

- メールアドレス
- 金額
- 住所
- 電話番号
- 期間
- 数量
- 注文番号

#### なぜ Value Object が必要なのか

普通に実装すると、メールアドレスは文字列で表現されがち

```java
const email: string = "test@example.com"
```

しかし、ただの文字列では不正な値を防げない

```java
const email: string = "invalid-email"
```

Value Object にすると、不正な値を生成時点で防ぐことが出来る

```java
class Email {
  constructor(private readonly value: string) {
    if (!value.includes("@")) {
      throw new Error("invalid email")
    }
  }

  getValue() {
    return this.value
  }
}
```

#### Value Object のメリット

- 不正な値を作れない
- バリデーションを一箇所に集約できる
- 意味のある型として扱える
- コードの可読性が上がる

#### Entity と Value Object の違い

| 項目     | Entity               | Value Object             |
| -------- | -------------------- | ------------------------ |
| 同一性   | ID で判断する        | 値で判断する             |
| 状態変化 | することがある       | 基本的に不変             |
| 例       | User, Order, Account | Email, Money, Address    |
| 目的     | 業務上の対象を表す   | 業務上意味のある値を表す |

---

### Aggregate

Aggregate とは

> 整合性を守るためのまとまり

※DDD の中でも特に重要で、難しい概念

#### Aggregate の基本

例えば注文には、複数の注文明細があります。

```
Order
├ OrderItem
├ OrderItem
└ OrderItem
```

このとき、OrderItem を外部から自由に変更できると、注文全体の整合性が壊れる可能性がある

例えば

注文合計金額 = 注文明細の合計

であるべきなのに、OrderItem だけが勝手に変更されると、注文合計金額と明細が一致しなくなる可能性がある

#### ■ Aggregate Root

Aggregate の入口となる Entity を Aggregate Root と呼ぶ

注文の例では、Order が Aggregate Root

```
Order ← Aggregate Root
├ OrderItem
└ OrderItem
```

外部からは OrderItem を直接操作せず、必ず Order を通して操作する

#### ■ Aggregate のルール

Aggregate には以下のルールがある

- 外部から操作できるのは Aggregate Root のみ
- Aggregate 内部の整合性は Root が守る
- Repository は Aggregate Root 単位で扱う
- Aggregate をまたぐ強い整合性はなるべく避ける

例）

```java
class Order {
  private items: OrderItem[] = []

  constructor(
    private readonly id: OrderId
  ) {}

  addItem(productId: ProductId, quantity: Quantity, price: Money) {
    const item = new OrderItem(productId, quantity, price)
    this.items.push(item)
  }

  calculateTotal(): Money {
    return this.items.reduce(
      (total, item) => total.add(item.calculateSubtotal()),
      Money.zero()
    )
  }
}
```

この設計では、OrderItem を外部から直接追加・変更せず、必ず Order 経由で操作している

---

### Repository

Repository とは

> Aggregate を永続化するための抽象

DDD における Repository は、単なる DB アクセスクラスではない

ドメイン層から見ると、Repository は以下のような役割を持つ

> 永続化されている Aggregate を、まるでメモリ上のコレクションのように扱うための窓口

例）

```java
interface OrderRepository {
  findById(id: OrderId): Promise<Order | null>
  save(order: Order): Promise<void>
}
```

重要なのは、Repository の Interface はドメイン層またはアプリケーション層に置き、実装はインフラ層に置くこと

#### ■ Repository でやってはいけないこと

Repository に業務ロジックを書いてはいけない

悪い例：

```java
class OrderRepository {
  async save(order: Order) {
    if (order.totalPrice > 10000) {
      // 送料無料にする
    }

    await prisma.order.create(...)
  }
}
```

送料無料の判断は業務ルール

Repository ではなく、Entity / Domain Service / UseCase などに置くべき

---

### Domain Service

Domain Service とは

> Entity や Value Object に自然に置けないドメインルールを表現するもの

#### ■ Domain Service が必要になる例

例えば、ユーザー同士のマッチング判定

ユーザー A とユーザー B がマッチング可能か

このルールは、User A だけの責務でも、User B だけの責務でもありません

このような場合、Domain Service として表現します

```java
class MatchingService {
  canMatch(userA: User, userB: User): boolean {
    return userA.isActive() && userB.isActive()
  }
}
```

#### ■ Domain Service の注意点

Domain Service は便利ですが、使いすぎると何でも入る Service になります

悪い例：

```
UserDomainService
├ register()
├ login()
├ changeEmail()
├ follow()
├ withdraw()
```

これは DDD ではなく、ただの Service 肥大化

Domain Service は、Entity や Value Object に置けない純粋なドメインルールに限定することが大事

### Factory

Factory とは

> 複雑なオブジェクト生成を担当するもの

#### ■ Factory が必要になる例

注文作成時に以下が必要な場合

- 注文 ID の生成
- 注文番号の生成
- 初期ステータス設定
- 注文明細の作成
- 初期金額計算

このような生成処理を UseCase に直接書くと、UseCase が複雑になります。

その場合、Factory を使う

```Java
class OrderFactory {
  create(customerId: CustomerId, items: OrderItemInput[]): Order {
    const orderId = OrderId.generate()
    const order = new Order(orderId, customerId)

    for (const item of items) {
      order.addItem(item.productId, item.quantity, item.price)
    }

    return order
  }
}
```

---

### Domain Event

Domain Event とは

> ドメイン内で発生した重要な出来事を表すもの

例）

- 注文が確定された
- 支払いが完了した
- 在庫が引き当てられた
- 会員ランクが上がった

これらは、業務上意味のある出来事

#### ■ なぜ Domain Event を使うのか

例えば「注文確定」後に、以下の処理が必要だとする

- 確認メールを送る
- 在庫を引き当てる
- ポイントを付与する
- 管理画面に通知する

これらをすべて confirmOrder() の中に直接書くと、注文確定処理が肥大化する

そこで、注文確定時に OrderConfirmed というイベントを発行する

```
OrderConfirmed
├ SendOrderMail
├ ReserveStock
├ AddPoint
└ NotifyAdmin
```

これにより、処理を疎結合にすることが出来る

---

### Application Service / UseCase

Application Service、または UseCase とは

> アプリケーションとしての操作手順

#### ■ UseCase の役割

UseCase は、以下のような流れを管理する

1. 入力を受け取る
2. Repository から Aggregate を取得する
3. Domain Model のメソッドを呼ぶ
4. Repository に保存する
5. 必要に応じて外部サービスを呼ぶ
6. 結果を返す

例）

```java
class ConfirmOrderUseCase {
  constructor(
    private readonly orderRepository: OrderRepository
  ) {}

  async execute(input: ConfirmOrderInput): Promise<void> {
    const order = await this.orderRepository.findById(
      new OrderId(input.orderId)
    )

    if (!order) {
      throw new Error("order not found")
    }

    order.confirm()

    await this.orderRepository.save(order)
  }
}
```

#### ■ UseCase に書くもの

- 処理の流れ
- Repository の呼び出し
- Transaction 制御
- 外部サービス呼び出し
- Domain Model の呼び出し

#### ■ UseCase に書かないもの

- Entity に置ける業務ルール
- Value Object に置けるバリデーション
- DB アクセスの詳細
- HTTP Request / Response の処理
- UI の表示制御

---

### Infrastructure

Infrastructure とは

> 技術詳細を扱う層

例）

- DB
- ORM
- 外部 API
- メール送信
- 決済サービス
- ファイルストレージ
- メッセージキュー

DDD では、ドメインモデルが Infrastructure に依存しないようにする

## 7. DDD とクリーンアーキテクチャの違い

### DDD

DDD は、業務をどう理解し、どうモデル化するかの設計思想

> DDD = 業務理解とモデル化の思想

---

### クリーンアーキテクチャ

クリーンアーキテクチャは、依存関係をどう整理し、技術詳細から業務ロジックを守るかの設計思想

> クリーンアーキテクチャ = 依存関係と責務分離の構造

---

### 関係性

実務では、DDD とクリーンアーキテクチャを組み合わせることが多いです。

```
DDD で業務モデルを設計する
↓
クリーンアーキテクチャで配置する
```

DDD は「中身をどう設計するか」

クリーンアーキテクチャは「それをどこに置き、どう依存させるか」

## 8. 実務での DDD 設計手順

DDD を実務で使う場合、いきなり Entity や Value Object を作り始めるのはおすすめしない

以下の順番で考えると整理しやすい

---

### 1. 業務を理解する

まず、対象業務を理解する

考えるべきこと：

- 誰が使うのか
- 何をしたいのか
- どんなルールがあるのか
- 何が重要なのか
- どこが複雑なのか

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

これがユビキタス言語の材料になります。

---

### 3. コンテキストを分ける

同じ言葉が違う意味で使われていないかを確認する

例：

商品

販売側では：

ユーザーに販売する対象

在庫側では：

倉庫に存在する管理対象

配送側では：

梱包・配送する物品

同じ「商品」でも意味が違う可能性がある

---

### 4. 重要なルールを見つける

業務上重要なルールを洗い出します。

例：

- 在庫がない商品は購入できない
- 支払い済みの注文だけ配送できる
- キャンセル済みの注文は再度キャンセルできない
- 有効期限切れのクーポンは使えない

---

### 5. ルールの置き場所を決める

ルールをどこに置くかを考えます。

Value Object に置くもの
Entity に置くもの
Aggregate に置くもの
Domain Service に置くもの
UseCase に置くもの

---

### 6. UseCase を定義する

ユーザーやシステムが行う操作を UseCase として定義する

例：

- RegisterUserUseCase
- ConfirmOrderUseCase
- CancelOrderUseCase
- ApplyCouponUseCase
- ShipOrderUseCase

---

### 7. Repository を定義する

Aggregate を取得・保存するための Repository Interface を定義する

例：

```java
interface OrderRepository {
  findById(id: OrderId): Promise<Order | null>
  save(order: Order): Promise<void>
}
```

---

### 8. Infrastructure で実装する

DB や ORM に依存する具体実装は Infrastructure 層に置く

例：

```java
class PrismaOrderRepository implements OrderRepository {
  async findById(id: OrderId): Promise<Order | null> {
    // Prismaを使った取得処理
  }

  async save(order: Order): Promise<void> {
    // Prismaを使った保存処理
  }
}
```

## 9.

## 10.アンチパターン

### 1. DDD をフォルダ構成だと思う

DDD はフォルダ構成ではない

重要なのは、業務ルールが正しく表現されているか

---

### 2. すべてを Entity にする

何でも Entity にすればよいわけではない

ID で同一性を判断する必要がないものは、Value Object の方が適していることがある

---

### 3.Domain Service に何でも入れる

Domain Service は便利だが、使いすぎると従来の Service と同じになる

Domain Service は、Entity や Value Object に置けないドメインルールに限定すること

---

### 4. UseCase に業務ルールを詰め込みすぎる

UseCase は処理の流れを管理する場所

Entity や Value Object に置けるルールは、なるべく Domain Model に寄せること

## 11.よくある疑問

### Q. DDD はクリーンアーキテクチャと同じですか？

<u>A. 違う</u>

DDD は業務をどうモデル化するかの思想

クリーンアーキテクチャは依存関係をどう整理するかの構造

---

### Q. Entity と DB のテーブルは同じですか？

<u>A. 同じとは限らない</u>

Entity は業務上の概念

DB テーブルは保存形式

1 つの Entity が複数テーブルに分かれることもあるし、逆に、複数の Entity が 1 つのテーブルに保存されることもある

---

### Q. Value Object は必ず作るべきですか？

<u>A. すべての値を Value Object にする必要はない</u>

ただし、業務上意味があり、制約を持つ値は Value Object にする価値がある

例：

- Email
- Money
- Quantity
- DateRange
- Address
- PhoneNumber

---

### Q. Aggregate はどう決めればいいですか？

<u>A. 整合性を守りたい単位で考える</u>

同時に変更される必要があるもの、外部から勝手に変更されると困るものは、同じ Aggregate にまとめる候補になりうる
