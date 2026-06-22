# クリーンアーキテクチャ

> Clean Architecture は、難しいフォルダ構成を作るための設計ではありません。  
> 業務ルールを、Web・DB・フレームワークなどの技術詳細から守るための設計思想です。

## 目次

- [1. Clean Architecture とは](#1-clean-architecture-とは)
- [2. Clean Architecture が必要になる背景](#2-clean-architecture-が必要になる背景)
- [3. 普通の設計で起きる問題](#3-普通の設計で起きる問題)
- [4. Clean Architecture の本質](#4-clean-architecture-の本質)
- [5. 依存関係のルール](#5-依存関係のルール)
- [6. Clean Architecture のレイヤー構成](#6-clean-architecture-のレイヤー構成)
  - [Entity](#entity)
  - [UseCase / Application Service](#usecase--application-service)
  - [Interface Adapter](#interface-adapter)
  - [Infrastructure / Frameworks & Drivers](#infrastructure--frameworks--drivers)
  - [Repository](#repository)
  - [DTO / Command / Response](#dto--command--response)
- [7. レイヤードアーキテクチャとの違い](#7-レイヤードアーキテクチャとの違い)
- [8. DDD と Clean Architecture の違い](#8-ddd-と-clean-architecture-の違い)
- [9. 実務での設計手順](#9-実務での設計手順)
- [10. Spring Boot でのディレクトリ構成](#10-spring-boot-でのディレクトリ構成)
- [11. 実装例：注文キャンセルを Clean Architecture で実装する](#11-実装例注文キャンセルを-clean-architecture-で実装する)
- [12. テスト例](#12-テスト例)
- [13. MVC との違い](#13-mvc-との違い)
- [14. フロントエンドで Clean Architecture は必要か](#14-フロントエンドで-clean-architecture-は必要か)
- [15. メリット・デメリット](#15-メリットデメリット)
- [16. レイヤー判定チートシート](#16-レイヤー判定チートシート)
- [17. よくある疑問](#17-よくある疑問)
- [18. アンチパターン](#18-アンチパターン)

---

## 1. Clean Architecture とは

Clean Architecture とは、Robert C. Martin、通称 Uncle Bob が提唱したアーキテクチャ設計の考え方です。

一言でいうと、以下です。

> **業務ルールを技術詳細から守る設計**

ここでいう技術詳細とは、例えば以下のようなものです。

- Web フレームワーク
- DB
- ORM
- 外部 API
- メール送信ライブラリ
- 認証ライブラリ
- 画面
- HTTP
- JSON
- GraphQL

これらはもちろん必要です。  
ただし、アプリケーションの中心ではありません。

アプリケーションの中心に置くべきなのは、以下のような**業務ルール**です。

- 発送済みの注文はキャンセルできない
- 在庫がない商品は購入できない
- 残高不足なら送金できない
- 退会済みユーザーはログインできない
- 有効期限切れのクーポンは使えない

Clean Architecture では、このような業務ルールを中心に置き、DB や Web などの技術を外側に追い出します。

---

### Clean Architecture を一言でいうと

Clean Architecture を一言でいうなら、以下です。

> **変わりやすいものを外側に置き、変わりにくい業務ルールを内側に置く設計**

例えば、以下は変わりやすいです。

```text
MySQL        → PostgreSQL
REST API     → GraphQL
Spring MVC   → Spring WebFlux
JWT          → Session
React        → Vue
外部決済API   → 別の決済サービス
```

一方で、以下のような業務ルールは比較的変わりにくいです。

```text
発送済みの注文はキャンセルできない
在庫がない商品は購入できない
有効期限切れのクーポンは使えない
```

だから Clean Architecture では、以下を目指します。

> **技術が変わっても、業務ルールへの影響を最小限にする**

---

## 2. Clean Architecture が必要になる背景

小さなアプリケーションでは、Controller に処理を書いても動きます。

例えば、ログイン API を Express で書くと、以下のような実装になりがちです。

```ts
app.post("/login", async (req, res) => {
  const user = await prisma.user.findUnique({
    where: { email: req.body.email },
  });

  if (!user) {
    return res.status(404).json({ error: "not found" });
  }

  const ok = await bcrypt.compare(req.body.password, user.password);

  if (!ok) {
    return res.status(401).json({ error: "invalid" });
  }

  const token = jwt.sign({ userId: user.id }, "secret");

  res.json({ token });
});
```

最初はこれでも問題なさそうに見えます。

しかし、この中にはいろいろな関心ごとが混ざっています。

```text
/login の Controller
├── HTTP リクエストを受け取る
├── 入力値を読む
├── Prisma で DB を検索する
├── パスワードを検証する
├── JWT を発行する
├── HTTP レスポンスを返す
└── エラーレスポンスを作る
```

つまり、1 つの場所に以下が混ざっています。

- HTTP の都合
- DB の都合
- ORM の都合
- 認証ライブラリの都合
- レスポンス形式の都合
- 業務ルール

この状態でも、個人開発や小規模機能なら動きます。

ただし、実務では後から変更が入ります。

- iPhone アプリ対応
- 管理画面対応
- GraphQL 対応
- DB 変更
- 認証方式変更
- 監査ログ追加
- 権限チェック追加
- 外部サービス連携
- テスト追加

すると、最初はシンプルだったコードが少しずつつらくなります。

---

## 3. 普通の設計で起きる問題

よくある設計では、以下のような構成になります。

```text
Controller
↓
Service
↓
Repository
↓
DB
```

これは一般的なレイヤードアーキテクチャです。  
シンプルでわかりやすいので、悪い設計というわけではありません。

ただし、運用が続くと Service に多くの処理が集まりやすいです。

例：注文処理

```text
OrderService
├── createOrder()
├── cancelOrder()
├── returnOrder()
├── applyCoupon()
├── calculatePoint()
├── reserveStock()
├── confirmPayment()
├── sendOrderMail()
├── refundOrder()
├── checkFraud()
└── changeShippingAddress()
```

このような状態になると、以下の問題が起きます。

- どこに何を書くべきかわからない
- 業務ルールが Service に集まりすぎる
- DB の都合と業務ルールが混ざる
- Controller が太る
- Repository に業務ロジックが漏れる
- テストしにくい
- 変更時の影響範囲が読めない
- フレームワーク変更の影響が広がる

---

### 技術変更が業務ロジックを巻き込むとは

例えば、ログイン処理の中で以下が直接混ざっているとします。

```text
ログイン処理
├── Express の req / res
├── Prisma の findUnique
├── bcrypt の compare
├── JWT の sign
└── ログイン可能かどうかのルール
```

この状態で、認証方式を JWT から Session に変えるとどうなるでしょうか。

本来変えたいのは、トークン発行の技術詳細だけです。

しかし、コードが混ざっていると、ログインルールそのものを触る必要が出てきます。

```text
変更したいこと：JWT → Session
本来触りたい場所：認証技術の実装だけ
実際に触る場所：Controller / Service / 業務ルール / テスト
```

これが、

> **技術変更が業務ロジックを巻き込む**

という状態です。

Clean Architecture は、この巻き込みを減らすための設計です。

---

## 4. Clean Architecture の本質

Clean Architecture の本質は、次の一文に集約できます。

> **業務ルールを中心に置き、外側の技術詳細に依存させない**

Clean Architecture は、単なるフォルダ構成ではありません。

大切なのは、以下です。

- 何が業務ルールなのかを見極める
- 何が技術詳細なのかを見極める
- 業務ルールを内側に置く
- 技術詳細を外側に置く
- 内側から外側へ依存しない

---

### 中心に置くもの

Clean Architecture で中心に置くのは、以下です。

```text
業務ルール
アプリケーションのユースケース
```

例えば、注文キャンセルなら中心に置くべきなのは以下です。

```text
注文をキャンセルする
発送済みの注文はキャンセルできない
キャンセル済みの注文を再度キャンセルできない
```

中心に置くべきではないものは以下です。

```text
HTTP の受け取り方
JSON の形式
DB のテーブル構造
JPA の Entity
Prisma の query
外部 API の呼び出し方法
```

これらは外側の詳細です。

---

### Clean Architecture で目指すコード

Clean Architecture で目指すのは、UseCase が業務の流れを読みやすく表現している状態です。

```java
public void execute(CancelOrderCommand command) {
    Order order = orderRepository.findById(command.orderId());
    order.cancel();
    orderRepository.save(order);
}
```

このコードからは、以下が読み取れます。

```text
注文を取得する
↓
注文をキャンセルする
↓
注文を保存する
```

ここには、以下が出てきません。

- HTTP
- JSON
- SQL
- JPA
- Spring MVC
- 外部 API の細かい形式

UseCase は、アプリケーションとして何をするかに集中しています。

---

## 5. 依存関係のルール

Clean Architecture で最も重要なのが、依存関係のルールです。

> **ソースコードの依存は、外側から内側に向ける**

図にすると、以下のようなイメージです。

```text
外側

Frameworks & Drivers
        ↓
Interface Adapters
        ↓
UseCases
        ↓
Entities

内側
```

内側にあるものほど、重要で変わりにくいものです。  
外側にあるものほど、技術詳細で変わりやすいものです。

---

### 依存してよい方向

以下は OK です。

```text
Controller → UseCase
UseCase → Entity
Infrastructure → Repository Interface
Infrastructure → Entity
```

外側が内側を知るのは OK です。

---

### 依存してはいけない方向

以下は NG です。

```text
Entity → Controller
Entity → DB
Entity → JPA
UseCase → Spring MVC
UseCase → Prisma
UseCase → HTTP Request
UseCase → JSON Response
```

内側が外側を知ってはいけません。

---

### なぜ内側から外側に依存してはいけないのか

例えば Entity が JPA に依存しているとします。

```java
@Entity
@Table(name = "orders")
public class Order {
    // 業務ルール
}
```

一見よくある実装ですが、これは Domain Model が JPA という技術詳細を知っている状態です。

この場合、以下のような問題が起きやすくなります。

- JPA の都合で Domain Model が歪む
- DB なしでテストしにくい
- ORM を変更しにくい
- 業務ルールと永続化の都合が混ざる

Clean Architecture を厳密にやるなら、Domain Model と JPA Entity は分けます。

```text
domain.model.Order
infrastructure.persistence.entity.OrderJpaEntity
```

ただし、実務では規模やチームによって、あえて兼用することもあります。

重要なのは、原則を理解したうえで判断することです。

---

### 処理の流れと依存の向きは違う

ここは初学者が混乱しやすいポイントです。

処理の流れは、外側から内側へ入り、また外側へ戻ります。

```text
HTTP Request
↓
Controller
↓
UseCase
↓
Repository Interface
↓
Repository Implementation
↓
DB
```

一方で、ソースコードの依存方向は内側向きにします。

```text
Controller → UseCase
UseCase → Repository Interface
Repository Implementation → Repository Interface
```

UseCase は Repository Implementation を直接知りません。

```java
public class CancelOrderUseCase {
    private final OrderRepository orderRepository;
}
```

ここで知っているのは `OrderRepository` という interface だけです。

実際の DB アクセス実装は、外側の Infrastructure に置きます。

```java
public class OrderRepositoryImpl implements OrderRepository {
    // DB アクセス
}
```

このようにすることで、UseCase は DB の詳細を知らずに済みます。

---

## 6. Clean Architecture のレイヤー構成

Clean Architecture は、よく以下のようなレイヤーで説明されます。

```text
Entities
UseCases
Interface Adapters
Frameworks & Drivers
```

ただし、実務では以下のような名前で表現されることも多いです。

```text
domain
application
presentation
infrastructure
```

このドキュメントでは、初学者が理解しやすいように以下の対応で説明します。

| Clean Architecture   | 実務でよく見る名前     | 役割                                       |
| -------------------- | ---------------------- | ------------------------------------------ |
| Entities             | domain                 | 業務ルールを表す                           |
| UseCases             | application            | アプリケーションの操作手順を表す           |
| Interface Adapters   | presentation / adapter | 外部形式と内部形式を変換する               |
| Frameworks & Drivers | infrastructure         | DB・外部 API・フレームワークなどの技術詳細 |

---

### Entity

Entity とは、業務ルールを表現する層です。

DDD の Entity と近いですが、Clean Architecture ではより広く、**重要な業務ルールを持つ中心的なオブジェクト**として考えます。

例：注文

```java
public class Order {
    private final Long id;
    private OrderStatus status;

    public Order(Long id, OrderStatus status) {
        if (id == null) {
            throw new IllegalArgumentException("注文IDは必須です");
        }
        if (status == null) {
            throw new IllegalArgumentException("注文ステータスは必須です");
        }
        this.id = id;
        this.status = status;
    }

    public void cancel() {
        if (status == OrderStatus.SHIPPED) {
            throw new OrderCannotCancelException("発送済みの注文はキャンセルできません");
        }
        if (status == OrderStatus.CANCELED) {
            throw new OrderCannotCancelException("キャンセル済みの注文は再度キャンセルできません");
        }
        this.status = OrderStatus.CANCELED;
    }

    public Long getId() {
        return id;
    }

    public OrderStatus getStatus() {
        return status;
    }
}
```

この `Order` は、以下の業務ルールを持っています。

```text
発送済みの注文はキャンセルできない
キャンセル済みの注文は再度キャンセルできない
```

このルールは、Controller や Repository ではなく、Order に置かれています。

---

#### Entity に書くもの

Entity には、以下を書きます。

- 業務上の状態
- 業務上の振る舞い
- 業務ルール
- 不正な状態を防ぐ処理

例：

```text
注文をキャンセルする
注文を確定する
残高を減らす
商品を購入可能か判定する
クーポンが使用可能か判定する
```

---

#### Entity に書かないもの

Entity には、以下を書きません。

- HTTP の処理
- DB アクセス
- SQL
- ORM の都合
- JSON 変換
- 画面表示の都合
- メール送信処理

悪い例：

```java
public class Order {
    public void cancel() {
        // 業務ルール

        // DB 保存
        orderRepository.save(this);

        // メール送信
        mailClient.sendCancelMail(this.id);
    }
}
```

Entity が DB やメール送信を知ってしまっています。  
これは外側の技術詳細に依存している状態です。

---

### UseCase / Application Service

UseCase とは、ユーザーや外部システムからの要求に対して、アプリケーションが行う処理の流れを表す層です。

> **UseCase = アプリケーションとしての操作手順**

例：

- 注文を作成する
- 注文をキャンセルする
- 商品を購入する
- クーポンを適用する
- ユーザーを登録する
- ログインする

---

#### UseCase の役割

UseCase は、以下のような流れを組み立てます。

```text
入力を受け取る
↓
必要なデータを取得する
↓
Entity のメソッドを呼ぶ
↓
必要に応じて保存する
↓
結果を返す
```

例：注文キャンセル

```java
public class CancelOrderUseCase {
    private final OrderRepository orderRepository;

    public CancelOrderUseCase(OrderRepository orderRepository) {
        this.orderRepository = orderRepository;
    }

    public void execute(CancelOrderCommand command) {
        Order order = orderRepository.findById(command.orderId());
        order.cancel();
        orderRepository.save(order);
    }
}
```

この UseCase は、以下の手順を表しています。

```text
注文を取得する
↓
注文をキャンセルする
↓
注文を保存する
```

---

#### UseCase に書くもの

UseCase には、以下を書きます。

- アプリケーションの処理手順
- トランザクション境界
- Repository の呼び出し
- Entity の呼び出し
- 外部サービス呼び出しの調整
- 権限チェックの呼び出し
- 結果の組み立て

---

#### UseCase に書かないもの

UseCase には、以下を書きません。

- HTTP Request / Response への直接依存
- DB の具体的な query
- ORM の Entity
- 画面表示の都合
- JSON の細かい形式

悪い例：

```java
public class CancelOrderUseCase {
    public ResponseEntity<?> execute(HttpServletRequest request) {
        Long orderId = Long.valueOf(request.getParameter("orderId"));
        // 処理
        return ResponseEntity.noContent().build();
    }
}
```

UseCase が HTTP を知ってしまっています。

UseCase は Web API から呼ばれるかもしれませんし、CLI、バッチ、GraphQL、テストから呼ばれるかもしれません。

そのため、UseCase は HTTP に依存させない方が変更に強くなります。

---

### Interface Adapter

Interface Adapter は、外側の形式と内側の形式を変換する層です。

代表例は Controller です。

Controller は HTTP リクエストを受け取り、UseCase が扱いやすい形に変換します。

```text
HTTP Request
↓ 変換
Command
↓
UseCase
```

そして UseCase の結果を HTTP レスポンスに変換します。

```text
UseCase Result
↓ 変換
HTTP Response
```

---

#### Controller の役割

Controller は、以下を担当します。

- HTTP リクエストを受け取る
- PathVariable / QueryParameter / RequestBody を読む
- 入力を Command や DTO に変換する
- UseCase を呼ぶ
- UseCase の結果を HTTP レスポンスに変換する

例：

```java
@RestController
@RequestMapping("/orders")
public class OrderController {
    private final CancelOrderUseCase cancelOrderUseCase;

    public OrderController(CancelOrderUseCase cancelOrderUseCase) {
        this.cancelOrderUseCase = cancelOrderUseCase;
    }

    @PostMapping("/{orderId}/cancel")
    public ResponseEntity<Void> cancel(@PathVariable Long orderId) {
        CancelOrderCommand command = new CancelOrderCommand(orderId);
        cancelOrderUseCase.execute(command);
        return ResponseEntity.noContent().build();
    }
}
```

Controller は HTTP の世界にいます。  
そのため、`@RestController` や `@PostMapping` を知っていて問題ありません。

ただし、Controller に業務ルールを書いてはいけません。

---

#### Controller に書いてはいけない例

悪い例：

```java
@PostMapping("/{orderId}/cancel")
public ResponseEntity<Void> cancel(@PathVariable Long orderId) {
    Order order = orderRepository.findById(orderId);

    if (order.getStatus() == OrderStatus.SHIPPED) {
        throw new RuntimeException("発送済みの注文はキャンセルできません");
    }

    order.setStatus(OrderStatus.CANCELED);
    orderRepository.save(order);

    return ResponseEntity.noContent().build();
}
```

この Controller には、以下が混ざっています。

- HTTP の処理
- DB 取得
- 業務ルール
- 状態変更
- DB 保存

Clean Architecture では、Controller は薄く保ちます。

---

### Infrastructure / Frameworks & Drivers

Infrastructure は、技術詳細を担当する層です。

例：

- DB アクセス
- ORM
- 外部 API
- メール送信
- ファイル保存
- Redis
- S3
- メッセージキュー
- フレームワーク設定

Infrastructure は外側の層なので、技術に依存して OK です。

```text
infrastructure
├── persistence
│   ├── entity
│   ├── mapper
│   ├── springdata
│   └── repository
├── external
│   └── payment
└── mail
```

---

#### Infrastructure の役割

Infrastructure の役割は、内側で定義された interface を実装することです。

例えば、UseCase は以下の interface に依存します。

```java
public interface OrderRepository {
    Order findById(Long id);
    void save(Order order);
}
```

Infrastructure はこの interface を実装します。

```java
@Repository
public class OrderRepositoryImpl implements OrderRepository {
    private final SpringDataOrderRepository repository;
    private final OrderPersistenceMapper mapper;

    public OrderRepositoryImpl(
            SpringDataOrderRepository repository,
            OrderPersistenceMapper mapper
    ) {
        this.repository = repository;
        this.mapper = mapper;
    }

    @Override
    public Order findById(Long id) {
        OrderJpaEntity entity = repository.findById(id)
                .orElseThrow(() -> new OrderNotFoundException("注文が見つかりません"));
        return mapper.toDomain(entity);
    }

    @Override
    public void save(Order order) {
        OrderJpaEntity entity = mapper.toJpaEntity(order);
        repository.save(entity);
    }
}
```

ここには JPA や Spring Data の詳細が出てきます。  
Infrastructure は外側なので問題ありません。

---

### Repository

Repository は、Entity や Aggregate を保存・取得するための窓口です。

> **Repository = 永続化の詳細を隠すための抽象**

UseCase から見ると、保存先が MySQL なのか PostgreSQL なのか、ファイルなのか、外部 API なのかは本質ではありません。

そのため、UseCase は具体的な DB 実装ではなく interface に依存します。

```java
public interface OrderRepository {
    Order findById(Long id);
    void save(Order order);
}
```

---

#### Repository Interface はどこに置くのか

実務では、Repository Interface の置き場所は設計方針によって分かれます。

| 置き場所       | 考え方                                                  |
| -------------- | ------------------------------------------------------- |
| domain 層      | Repository をドメインモデルの保存・取得の概念として見る |
| application 層 | UseCase が必要とする入出力の口として見る                |

どちらの流派もあります。

このドキュメントでは、説明をシンプルにするために `domain.repository` に置きます。

重要なのは、置き場所そのものよりも以下です。

> **UseCase や Entity が DB の具体実装に依存しないこと**

---

#### Repository に書いてはいけないもの

Repository に業務ルールを書いてはいけません。

悪い例：

```java
public class OrderRepositoryImpl implements OrderRepository {
    public void save(Order order) {
        if (order.getTotalPrice() >= 10000) {
            order.setShippingFee(0);
        }
        // DB保存
    }
}
```

送料無料かどうかは業務ルールです。  
Repository ではなく、Entity / Domain Service / UseCase など、ルールの意味に合った場所へ置くべきです。

Repository の責務は、あくまで保存・取得です。

---

### DTO / Command / Response

DTO や Command は、層の境界をまたぐためのデータ構造です。

代表例：

```text
Request DTO
Command
Response DTO
Result
```

---

#### Request DTO

Request DTO は、HTTP リクエストの形を表します。

```java
public class CancelOrderRequest {
    private String reason;

    public String getReason() {
        return reason;
    }
}
```

これは HTTP の都合に近いので、presentation 層に置くことが多いです。

---

#### Command

Command は、UseCase に渡す入力値です。

```java
public record CancelOrderCommand(
        Long orderId,
        String reason
) {
}
```

Controller は Request を Command に変換して UseCase に渡します。

```text
HTTP Request
↓
Request DTO
↓
Command
↓
UseCase
```

---

#### Response DTO

Response DTO は、HTTP レスポンスの形を表します。

```java
public record OrderResponse(
        Long id,
        String status
) {
}
```

Response DTO は外部に返す形なので、presentation 層に置くことが多いです。

---

#### DTO を使う理由

DTO を使う理由は、外側の都合を内側に持ち込まないためです。

悪い例：

```java
public void execute(CancelOrderRequest request) {
    // UseCase が HTTP 用の Request DTO を知っている
}
```

良い例：

```java
public void execute(CancelOrderCommand command) {
    // UseCase は HTTP を知らない
}
```

こうすると、UseCase は Web API 以外からも呼びやすくなります。

---

## 7. レイヤードアーキテクチャとの違い

レイヤードアーキテクチャは、一般的に以下のような構成です。

```text
Controller
↓
Service
↓
Repository
↓
DB
```

処理の流れがそのまま依存の向きになりがちです。

```text
Service → Repository → DB
```

この場合、Service が具体的な Repository 実装や DB 都合に引っ張られることがあります。

---

### Clean Architecture の場合

Clean Architecture では、UseCase は Repository の interface に依存します。

```text
Controller
↓
UseCase → Repository Interface
             ↑
             Repository Implementation
             ↓
             DB
```

ポイントは以下です。

> **UseCase は DB 実装を知らない**

Repository Implementation は外側に置かれ、内側の Repository Interface を実装します。

---

### 違いをざっくり比較

| 観点        | レイヤードアーキテクチャ           | Clean Architecture           |
| ----------- | ---------------------------------- | ---------------------------- |
| 中心        | Service                            | Entity / UseCase             |
| 依存方向    | 上から下になりがち                 | 外側から内側                 |
| DB への依存 | Service が DB 都合に近くなりやすい | UseCase は DB 実装を知らない |
| 業務ルール  | Service に集まりやすい             | Entity / UseCase に分ける    |
| テスト      | DB や Framework に引っ張られやすい | UseCase 単体でテストしやすい |
| 変更耐性    | 技術変更が内側へ波及しやすい       | 技術詳細を差し替えやすい     |

---

## 8. DDD と Clean Architecture の違い

DDD と Clean Architecture は混同されやすいですが、目的が違います。

---

### DDD

DDD は、業務をどう理解し、どうモデル化するかに重点を置きます。

> **業務の言葉・ルール・構造をコードに反映するための設計思想**

DDD で主に考えること：

- ドメイン
- ユビキタス言語
- 境界づけられたコンテキスト
- Entity
- Value Object
- Aggregate
- Repository
- Domain Service
- Domain Event

---

### Clean Architecture

Clean Architecture は、コードの依存関係をどう整理するかに重点を置きます。

> **業務ルールを技術詳細から守るための設計思想**

Clean Architecture で主に考えること：

- Entity
- UseCase
- Interface Adapter
- Infrastructure
- 依存関係の向き
- 技術詳細の分離
- テストしやすさ

---

### 違いを一言でいうと

```text
DDD
→ 業務をどう理解してモデル化するか

Clean Architecture
→ その業務モデルを技術詳細からどう守るか
```

---

### 一緒に使うとどうなるか

DDD と Clean Architecture は相性が良いです。

```text
Clean Architecture
├── presentation
├── application
├── domain        ← DDD のモデルを置く
└── infrastructure
```

DDD で作った Entity / Value Object / Aggregate を domain 層に置き、Clean Architecture で外側の技術詳細から守るイメージです。

---

## 9. 実務での設計手順

Clean Architecture を実務で考えるときは、いきなりフォルダを作らない方がよいです。

まずは、何を分離したいのかを整理します。

---

### 手順 1：ユースケースを洗い出す

まず、ユーザーや外部システムが何をしたいのかを考えます。

例：EC サイト

```text
注文を作成する
注文を確定する
注文をキャンセルする
クーポンを適用する
在庫を引き当てる
支払いを確定する
商品を発送する
```

UseCase は、アプリケーションの操作単位です。

---

### 手順 2：業務ルールを見つける

次に、そのユースケースに含まれる業務ルールを見つけます。

例：注文キャンセル

```text
発送済みの注文はキャンセルできない
キャンセル済みの注文は再度キャンセルできない
支払い済みの場合は返金処理が必要
キャンセル理由を記録する
```

---

### 手順 3：ルールを置く場所を決める

業務ルールが見つかったら、どこに置くかを考えます。

```text
注文自身の状態に関するルール
→ Order Entity

複数の集約や外部サービスが絡む手順
→ UseCase

特定の Entity に置くと不自然なドメインルール
→ Domain Service
```

---

### 手順 4：外部との境界を決める

次に、外部技術との境界を決めます。

```text
DB 保存が必要
→ Repository Interface を内側に定義
→ Repository Implementation を Infrastructure に作る

外部決済 API が必要
→ PaymentGateway Interface を内側に定義
→ PaymentApiClient を Infrastructure に作る

メール送信が必要
→ MailSender Interface を内側に定義
→ SmtpMailSender を Infrastructure に作る
```

---

### 手順 5：Controller を薄くする

Controller は、HTTP と UseCase の橋渡しに集中させます。

```text
Request を受け取る
↓
Command に変換する
↓
UseCase を呼ぶ
↓
Response に変換する
```

Controller に業務ルールを書かないようにします。

---

### 手順 6：テストを書く

UseCase は DB 実装を知らないので、Fake Repository を使ってテストしやすくなります。

```text
UseCase
↓
FakeRepository
```

DB を使わずに、業務の流れをテストできます。

---

## 10. Spring Boot でのディレクトリ構成

あくまでサンプルです。

```text
src/main/java/com/example/app
├── presentation
│   ├── controller
│   │   └── OrderController.java
│   └── dto
│       ├── request
│       │   └── CancelOrderRequest.java
│       └── response
│           └── OrderResponse.java
│
├── application
│   ├── usecase
│   │   ├── CancelOrderUseCase.java
│   │   └── CreateOrderUseCase.java
│   ├── command
│   │   └── CancelOrderCommand.java
│   └── result
│       └── OrderResult.java
│
├── domain
│   ├── model
│   │   ├── Order.java
│   │   └── OrderStatus.java
│   ├── valueobject
│   │   ├── OrderId.java
│   │   └── Money.java
│   ├── repository
│   │   └── OrderRepository.java
│   └── exception
│       ├── OrderCannotCancelException.java
│       └── OrderNotFoundException.java
│
├── infrastructure
│   ├── persistence
│   │   ├── entity
│   │   │   └── OrderJpaEntity.java
│   │   ├── mapper
│   │   │   └── OrderPersistenceMapper.java
│   │   ├── springdata
│   │   │   └── SpringDataOrderRepository.java
│   │   └── OrderRepositoryImpl.java
│   └── external
│       └── PaymentApiClient.java
│
└── config
    └── BeanConfig.java
```

---

### 各ディレクトリの役割

| ディレクトリ   | 役割                                                          |
| -------------- | ------------------------------------------------------------- |
| presentation   | HTTP などの入出力を扱う                                       |
| application    | UseCase、アプリケーションの処理手順を書く                     |
| domain         | 業務ルール、Entity、Value Object、Repository Interface を置く |
| infrastructure | DB、外部 API、メールなどの技術詳細を書く                      |
| config         | DI 設定やフレームワーク設定を書く                             |

---

### 依存方向のイメージ

```text
presentation
    ↓
application
    ↓
domain

infrastructure
    ↓
domain / application の interface
```

重要なのは、以下です。

```text
domain は presentation / infrastructure を知らない
application は DB 実装を知らない
infrastructure は内側の interface を実装する
```

---

## 11. 実装例：注文キャンセルを Clean Architecture で実装する

ここでは、注文キャンセルを例にします。

業務ルールは以下です。

```text
発送済みの注文はキャンセルできない
キャンセル済みの注文は再度キャンセルできない
それ以外の注文はキャンセルできる
```

---

### Domain Model / Entity

```java
package com.example.app.domain.model;

import com.example.app.domain.exception.OrderCannotCancelException;

public class Order {
    private final Long id;
    private OrderStatus status;

    public Order(Long id, OrderStatus status) {
        if (id == null) {
            throw new IllegalArgumentException("注文IDは必須です");
        }
        if (status == null) {
            throw new IllegalArgumentException("注文ステータスは必須です");
        }
        this.id = id;
        this.status = status;
    }

    public void cancel() {
        if (status == OrderStatus.SHIPPED) {
            throw new OrderCannotCancelException("発送済みの注文はキャンセルできません");
        }
        if (status == OrderStatus.CANCELED) {
            throw new OrderCannotCancelException("キャンセル済みの注文は再度キャンセルできません");
        }
        this.status = OrderStatus.CANCELED;
    }

    public Long getId() {
        return id;
    }

    public OrderStatus getStatus() {
        return status;
    }
}
```

---

### Enum

```java
package com.example.app.domain.model;

public enum OrderStatus {
    DRAFT,
    CONFIRMED,
    PAID,
    SHIPPED,
    CANCELED
}
```

---

### Domain Exception

```java
package com.example.app.domain.exception;

public class OrderCannotCancelException extends RuntimeException {
    public OrderCannotCancelException(String message) {
        super(message);
    }
}
```

```java
package com.example.app.domain.exception;

public class OrderNotFoundException extends RuntimeException {
    public OrderNotFoundException(String message) {
        super(message);
    }
}
```

---

### Repository Interface

```java
package com.example.app.domain.repository;

import com.example.app.domain.model.Order;

public interface OrderRepository {
    Order findById(Long id);
    void save(Order order);
}
```

この interface は内側に置きます。

UseCase はこれに依存します。  
DB の具体的な実装には依存しません。

---

### Command

```java
package com.example.app.application.command;

public record CancelOrderCommand(Long orderId) {
    public CancelOrderCommand {
        if (orderId == null) {
            throw new IllegalArgumentException("注文IDは必須です");
        }
    }
}
```

Command は、UseCase に渡す入力値です。

---

### UseCase

```java
package com.example.app.application.usecase;

import com.example.app.application.command.CancelOrderCommand;
import com.example.app.domain.model.Order;
import com.example.app.domain.repository.OrderRepository;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

@Service
public class CancelOrderUseCase {
    private final OrderRepository orderRepository;

    public CancelOrderUseCase(OrderRepository orderRepository) {
        this.orderRepository = orderRepository;
    }

    @Transactional
    public void execute(CancelOrderCommand command) {
        Order order = orderRepository.findById(command.orderId());
        order.cancel();
        orderRepository.save(order);
    }
}
```

UseCase の役割は、処理の流れを表すことです。

```text
注文を取得する
↓
注文をキャンセルする
↓
注文を保存する
```

キャンセル可能かどうかの判断は `order.cancel()` の中にあります。

---

### Controller

```java
package com.example.app.presentation.controller;

import com.example.app.application.command.CancelOrderCommand;
import com.example.app.application.usecase.CancelOrderUseCase;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequestMapping("/orders")
public class OrderController {
    private final CancelOrderUseCase cancelOrderUseCase;

    public OrderController(CancelOrderUseCase cancelOrderUseCase) {
        this.cancelOrderUseCase = cancelOrderUseCase;
    }

    @PostMapping("/{orderId}/cancel")
    public ResponseEntity<Void> cancel(@PathVariable Long orderId) {
        CancelOrderCommand command = new CancelOrderCommand(orderId);
        cancelOrderUseCase.execute(command);
        return ResponseEntity.noContent().build();
    }
}
```

Controller は薄いです。

やっていることは以下だけです。

```text
HTTP リクエストを受け取る
↓
Command を作る
↓
UseCase を呼ぶ
↓
HTTP レスポンスを返す
```

---

### JPA Entity

```java
package com.example.app.infrastructure.persistence.entity;

import jakarta.persistence.Entity;
import jakarta.persistence.Id;
import jakarta.persistence.Table;

@Entity
@Table(name = "orders")
public class OrderJpaEntity {
    @Id
    private Long id;

    private String status;

    protected OrderJpaEntity() {
    }

    public OrderJpaEntity(Long id, String status) {
        this.id = id;
        this.status = status;
    }

    public Long getId() {
        return id;
    }

    public String getStatus() {
        return status;
    }
}
```

これは DB 永続化のための Entity です。  
Domain Model の `Order` とは別物として扱います。

---

### Mapper

```java
package com.example.app.infrastructure.persistence.mapper;

import com.example.app.domain.model.Order;
import com.example.app.domain.model.OrderStatus;
import com.example.app.infrastructure.persistence.entity.OrderJpaEntity;
import org.springframework.stereotype.Component;

@Component
public class OrderPersistenceMapper {
    public Order toDomain(OrderJpaEntity entity) {
        return new Order(
                entity.getId(),
                OrderStatus.valueOf(entity.getStatus())
        );
    }

    public OrderJpaEntity toJpaEntity(Order order) {
        return new OrderJpaEntity(
                order.getId(),
                order.getStatus().name()
        );
    }
}
```

Mapper は、DB 用の形と Domain 用の形を変換します。

```text
OrderJpaEntity → Order
Order → OrderJpaEntity
```

---

### Spring Data Repository

```java
package com.example.app.infrastructure.persistence.springdata;

import com.example.app.infrastructure.persistence.entity.OrderJpaEntity;
import org.springframework.data.jpa.repository.JpaRepository;

public interface SpringDataOrderRepository extends JpaRepository<OrderJpaEntity, Long> {
}
```

これは Spring Data JPA の Repository です。  
完全に Infrastructure の詳細です。

---

### Repository Implementation

```java
package com.example.app.infrastructure.persistence;

import com.example.app.domain.exception.OrderNotFoundException;
import com.example.app.domain.model.Order;
import com.example.app.domain.repository.OrderRepository;
import com.example.app.infrastructure.persistence.entity.OrderJpaEntity;
import com.example.app.infrastructure.persistence.mapper.OrderPersistenceMapper;
import com.example.app.infrastructure.persistence.springdata.SpringDataOrderRepository;
import org.springframework.stereotype.Repository;

@Repository
public class OrderRepositoryImpl implements OrderRepository {
    private final SpringDataOrderRepository repository;
    private final OrderPersistenceMapper mapper;

    public OrderRepositoryImpl(
            SpringDataOrderRepository repository,
            OrderPersistenceMapper mapper
    ) {
        this.repository = repository;
        this.mapper = mapper;
    }

    @Override
    public Order findById(Long id) {
        OrderJpaEntity entity = repository.findById(id)
                .orElseThrow(() -> new OrderNotFoundException("注文が見つかりません"));
        return mapper.toDomain(entity);
    }

    @Override
    public void save(Order order) {
        OrderJpaEntity entity = mapper.toJpaEntity(order);
        repository.save(entity);
    }
}
```

このクラスは外側の Infrastructure にあります。

`OrderRepository` という内側の interface を実装しています。

---

### 全体の依存関係

```text
OrderController
    ↓
CancelOrderUseCase
    ↓
OrderRepository interface
    ↑
OrderRepositoryImpl
    ↓
SpringDataOrderRepository
    ↓
DB
```

重要なのは、UseCase が `OrderRepositoryImpl` や `SpringDataOrderRepository` を知らないことです。

---

## 12. テスト例

Clean Architecture にすると、UseCase のテストが書きやすくなります。

理由は、UseCase が DB 実装に依存していないからです。

---

### Fake Repository

```java
import com.example.app.domain.model.Order;
import com.example.app.domain.repository.OrderRepository;

import java.util.HashMap;
import java.util.Map;

public class FakeOrderRepository implements OrderRepository {
    private final Map<Long, Order> store = new HashMap<>();

    public void add(Order order) {
        store.put(order.getId(), order);
    }

    @Override
    public Order findById(Long id) {
        return store.get(id);
    }

    @Override
    public void save(Order order) {
        store.put(order.getId(), order);
    }
}
```

---

### UseCase Test

```java
import com.example.app.application.command.CancelOrderCommand;
import com.example.app.application.usecase.CancelOrderUseCase;
import com.example.app.domain.model.Order;
import com.example.app.domain.model.OrderStatus;
import org.junit.jupiter.api.Test;

import static org.junit.jupiter.api.Assertions.assertEquals;

class CancelOrderUseCaseTest {
    @Test
    void 注文をキャンセルできる() {
        FakeOrderRepository repository = new FakeOrderRepository();
        Order order = new Order(1L, OrderStatus.PAID);
        repository.add(order);

        CancelOrderUseCase useCase = new CancelOrderUseCase(repository);
        useCase.execute(new CancelOrderCommand(1L));

        Order saved = repository.findById(1L);
        assertEquals(OrderStatus.CANCELED, saved.getStatus());
    }
}
```

DB を使わずに UseCase をテストできています。

これが Clean Architecture の大きなメリットです。

---

## 13. MVC との違い

MVC は、主に画面やリクエスト処理を整理するための考え方です。

```text
Model
View
Controller
```

Web API では、以下のような構成で使われることが多いです。

```text
Controller
↓
Service
↓
Repository
```

MVC 自体は悪くありません。

ただし、MVC だけでは、業務ルールをどこに置くかが曖昧になりやすいです。

その結果、Service が何でも屋になりがちです。

```text
OrderService
├── HTTP に近い処理
├── 業務ルール
├── DB 取得
├── 外部 API 呼び出し
├── メール送信
└── レスポンス整形
```

Clean Architecture では、これらを役割ごとに分けます。

```text
Controller      → HTTP の受け取り
UseCase         → アプリケーションの処理手順
Entity          → 業務ルール
Repository      → 保存・取得の抽象
Infrastructure  → DB・外部 API・メールなどの詳細
```

---

## 14. フロントエンドで Clean Architecture は必要か

結論からいうと、フロントエンドでは必須ではありません。

特に React の場合、主な責務は以下です。

- 画面表示
- ユーザー入力
- API 呼び出し
- 状態管理
- フォーム制御
- ローディング管理
- エラー表示

多くの業務ルールはバックエンドに置くことが多いです。

---

### フロントとバックエンドの責務の違い

| フロントエンド       | バックエンド           |
| -------------------- | ---------------------- |
| 入力フォームの制御   | 購入可能かの判定       |
| ボタンの表示・非表示 | キャンセル可能かの判定 |
| ローディング表示     | 在庫引当               |
| エラー表示           | 決済確定               |
| API 呼び出し         | 権限チェック           |
| URL パラメータ管理   | データ整合性の保証     |
| 一覧のソート表示     | 排他制御               |

---

### フロントで Clean Architecture 的な分離が役立つケース

以下のような場合は、フロントでも Clean Architecture 的な考え方が役立ちます。

- フロント側に複雑な業務ルールがある
- オフライン対応がある
- 複雑な入力ウィザードがある
- API を差し替える可能性がある
- Web / Mobile でロジックを共通化したい
- 状態管理が複雑

---

### React での現実的な構成

多くの React アプリでは、以下のような feature ベースの構成で十分なことが多いです。

```text
src
├── features
│   └── order
│       ├── components
│       │   └── CancelOrderButton.tsx
│       ├── hooks
│       │   └── useCancelOrder.ts
│       ├── api
│       │   └── orderApi.ts
│       ├── types
│       │   └── order.ts
│       └── utils
│           └── orderViewHelper.ts
├── shared
│   ├── components
│   └── api
└── pages
```

この場合、ざっくり以下のように考えます。

```text
components
→ 見た目

hooks
→ 画面の状態管理・操作手順

api
→ API 呼び出し

types
→ 型定義

utils
→ 表示用の補助ロジック
```

---

### フロントでやりすぎ注意

フロントに以下のような構成を必ず入れる必要はありません。

```text
domain
application
infrastructure
presentation
```

小規模な画面でこれをやると、かえって複雑になります。

フロントでは、以下の判断が大事です。

> **画面都合のロジックなのか、業務の本質なのか**

画面都合なら、React の責務に合った分け方で十分です。  
業務の本質なら、バックエンドに置くか、フロントでも明確に分離します。

---

## 15. メリット・デメリット

### メリット

Clean Architecture のメリットは以下です。

---

#### 1. 業務ルールを守りやすい

業務ルールを Entity や UseCase に集めるため、コードを読んだときに仕様を追いやすくなります。

```text
注文キャンセルのルールを知りたい
↓
Order.cancel() を見る
```

---

#### 2. 技術変更に強い

UseCase が DB 実装や Web フレームワークを知らないため、外側の技術を差し替えやすくなります。

```text
MySQL → PostgreSQL
REST → GraphQL
JWT → Session
```

このような変更でも、業務ルールへの影響を抑えやすくなります。

---

#### 3. テストしやすい

UseCase は interface に依存しているため、Fake や Mock に差し替えてテストできます。

```text
DB なしで UseCase をテストする
外部 API なしで UseCase をテストする
```

---

#### 4. 役割が明確になる

各層の責務が分かれるため、どこに何を書くべきか判断しやすくなります。

```text
HTTP なら Controller
処理手順なら UseCase
業務ルールなら Entity
DB なら Infrastructure
```

---

### デメリット

Clean Architecture にはデメリットもあります。

---

#### 1. 初期コストが高い

小さな機能でも、以下のようにファイル数が増えます。

```text
Controller
UseCase
Command
Entity
Repository Interface
Repository Implementation
Mapper
JPA Entity
```

そのため、小規模な CRUD だけのアプリでは過剰設計になることがあります。

---

#### 2. 学習コストがある

依存関係の向き、DTO、Repository Interface、Mapper など、理解することが増えます。

チーム全員が意図を理解していないと、形だけ Clean Architecture になりがちです。

---

#### 3. 過剰に分けると読みにくい

何でもかんでも細かく分けると、1 つの処理を追うために多くのファイルを開く必要があります。

```text
単純な1テーブルCRUD
↓
無理に Clean Architecture
↓
ファイルが増えすぎて逆に読みにくい
```

Clean Architecture は万能薬ではありません。

---

## 16. レイヤー判定チートシート

迷ったときは、以下で判断します。

| 書きたい処理                          | 置く場所                                     |
| ------------------------------------- | -------------------------------------------- |
| HTTP リクエストを受け取る             | Controller / presentation                    |
| RequestBody を読む                    | Controller / presentation                    |
| レスポンス形式を作る                  | Controller / presentation                    |
| アプリケーションの操作手順を書く      | UseCase / application                        |
| トランザクションを張る                | UseCase / application                        |
| Entity を取得して操作する             | UseCase / application                        |
| 業務ルールを書く                      | Entity / domain                              |
| 状態変更のルールを書く                | Entity / domain                              |
| 値の不正を防ぐ                        | Value Object / domain                        |
| 保存・取得の抽象を書く                | Repository Interface / domain or application |
| DB アクセスを書く                     | Repository Implementation / infrastructure   |
| SQL・ORM の処理を書く                 | infrastructure                               |
| 外部 API を呼ぶ                       | infrastructure                               |
| メールを送る                          | infrastructure                               |
| DB Entity と Domain Entity を変換する | Mapper / infrastructure                      |
| 画面表示の都合を書く                  | frontend / presentation                      |

---

### 判断フローチャート

```text
それは業務ルール？
├── YES → domain
└── NO
    ↓
それはアプリケーションの処理手順？
├── YES → application / usecase
└── NO
    ↓
それは HTTP や画面の入出力？
├── YES → presentation
└── NO
    ↓
それは DB・外部 API・メールなどの技術詳細？
├── YES → infrastructure
└── NO → どの変更理由に近いかで判断する
```

---

## 17. よくある疑問

### Q. Clean Architecture は必ずやるべき？

必ずではありません。

単純な CRUD アプリや小規模な個人開発では、かえって複雑になることがあります。

Clean Architecture が特に役立つのは、以下のようなケースです。

- 業務ルールが複雑
- 長期運用する
- チーム開発する
- テストをしっかり書きたい
- DB や外部 API の変更可能性がある
- 複数の入出力に対応したい

---

### Q. Service と UseCase は何が違う？

名前だけなら似ています。

ただし、Clean Architecture では UseCase を以下のように考えます。

> **ユーザーや外部システムが実行したい操作単位**

例：

```text
CreateOrderUseCase
CancelOrderUseCase
ApplyCouponUseCase
RegisterUserUseCase
```

一方で、よくある Service は粒度が大きくなりがちです。

```text
OrderService
UserService
PaymentService
```

Service が悪いわけではありません。  
ただし、何でも入る Service になると責務が曖昧になります。

---

### Q. UseCase に業務ルールを書いていい？

場合によります。

Entity 自身の状態に関するルールは Entity に置くのが自然です。

```text
発送済み注文はキャンセルできない
→ Order.cancel()
```

一方で、複数の Entity や外部サービスをまたぐ処理手順は UseCase に置くことがあります。

```text
注文を確定する
↓
在庫を引き当てる
↓
決済を実行する
↓
メールを送る
```

このような流れは UseCase が調整します。

---

### Q. Repository Interface は domain と application のどちらに置く？

どちらの考え方もあります。

このドキュメントでは `domain.repository` に置いていますが、UseCase が必要とする port として `application.port` に置く設計もあります。

重要なのは、以下です。

```text
UseCase が具体的な DB 実装に依存しないこと
Domain が ORM や DB を知らないこと
```

---

### Q. JPA Entity と Domain Entity は分けるべき？

理想的には分けると責務が明確になります。

```text
Domain Entity
→ 業務ルールを表す

JPA Entity
→ DB テーブルとのマッピングを表す
```

ただし、実務では小規模なアプリで兼用することもあります。

判断基準は以下です。

```text
業務ルールが複雑
長期運用する
テストしやすくしたい
DB 都合に引っ張られたくない
→ 分ける価値が高い

単純な CRUD
短期の管理画面
業務ルールがほぼない
→ 兼用でもよい場合がある
```

---

### Q. Controller はどこまで薄くする？

Controller は、基本的に以下だけにします。

```text
入力を受け取る
Command に変換する
UseCase を呼ぶ
Response に変換する
```

業務ルールや DB アクセスは書かないようにします。

---

### Q. Clean Architecture と DI は関係ある？

関係あります。

UseCase は Repository Interface に依存します。

```java
public class CancelOrderUseCase {
    private final OrderRepository orderRepository;
}
```

実行時には、Spring などの DI コンテナが `OrderRepositoryImpl` を注入します。

```text
UseCase が知っているもの
→ OrderRepository interface

実際に動くもの
→ OrderRepositoryImpl
```

DI によって、依存関係のルールを守りながら実装を差し替えられます。

---

## 18. アンチパターン

### 1. Controller に全部書く

悪い例：

```text
Controller
├── 入力チェック
├── DB 取得
├── 業務ルール
├── 状態変更
├── DB 保存
└── レスポンス生成
```

Controller は HTTP の入口です。  
業務ルールを書く場所ではありません。

---

### 2. UseCase が巨大化する

悪い例：

```text
OrderUseCase
├── create()
├── cancel()
├── returnOrder()
├── applyCoupon()
├── confirmPayment()
├── reserveStock()
└── refund()
```

UseCase は操作単位で分けると読みやすくなります。

```text
CreateOrderUseCase
CancelOrderUseCase
ApplyCouponUseCase
ConfirmPaymentUseCase
```

---

### 3. Entity が getter / setter だけ

悪い例：

```java
public class Order {
    private OrderStatus status;

    public OrderStatus getStatus() {
        return status;
    }

    public void setStatus(OrderStatus status) {
        this.status = status;
    }
}
```

この場合、業務ルールはどこか別の場所に漏れます。

良い例：

```java
public void cancel() {
    if (status == OrderStatus.SHIPPED) {
        throw new OrderCannotCancelException("発送済みの注文はキャンセルできません");
    }
    this.status = OrderStatus.CANCELED;
}
```

Entity が業務ルールを持つことで、コードから仕様を読み取りやすくなります。

---

### 4. Repository に業務ルールを書く

悪い例：

```java
public void save(Order order) {
    if (order.getStatus() == OrderStatus.SHIPPED) {
        throw new RuntimeException("発送済み注文は変更できません");
    }
    repository.save(order);
}
```

Repository は保存・取得のための場所です。  
業務ルールを書く場所ではありません。

---

### 5. Infrastructure の都合を Domain に持ち込む

悪い例：

```java
@Entity
public class Order {
    @Id
    private Long id;

    @Column(name = "order_status")
    private String status;

    public void cancel() {
        // 業務ルール
    }
}
```

JPA を使うこと自体が悪いわけではありません。

ただし、Domain Model が DB マッピングの都合に強く引っ張られるなら、JPA Entity と Domain Entity を分けることを検討します。

---

### 6. 形だけ Clean Architecture にする

フォルダだけ分けても、依存関係が逆向きなら意味がありません。

悪い例：

```text
domain の中で Spring Data JPA を import している
application の中で HttpServletRequest を使っている
Entity の中で MailClient を呼んでいる
Repository の中に業務ルールがある
```

Clean Architecture で大事なのは、フォルダ名ではありません。

> **何が何に依存しているか**

です。
