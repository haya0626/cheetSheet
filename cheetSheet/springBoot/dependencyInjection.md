# 依存性注入

依存性注入とは、あるクラスが必要としている別のクラスを、自分で作るのではなく、Spring が代わりに渡してくれる仕組みです。

## 1.イメージ

たとえば OrderService が PaymentService を使いたいとします。

普通に Java だけで書くと、こうなります。

```java
public class OrderService {

    private PaymentService paymentService = new PaymentService();

    public void order() {
        paymentService.pay();
    }

}
```

この書き方では、OrderService が PaymentService を自分で作っています。

つまり OrderService は

- 注文処理をする責任
- 決済サービスを作る責任

の両方を持ってしまっています。

これを Spring ではこう書きます。

```java
@Service
public class OrderService {

    private final PaymentService paymentService;

    public OrderService(PaymentService paymentService) {
        this.paymentService = paymentService;
    }

    public void order() {
        paymentService.pay();
    }

}
@Service
public class PaymentService {

    public void pay() {
        System.out.println("支払い完了");
    }

}
```

この場合、OrderService は PaymentService を自分で作っていません。
その代わりに、

PaymentService が必要です

と宣言しているだけです。

そして Spring が

じゃあ PaymentService を用意して渡しておくね

とやってくれます。

これが依存性注入。

## 2. 単語理解

### 2.1 依存とは

「このクラスは、他のクラスがないと動けない」という関係のことです。

たとえば OrderService が PaymentService を使っているなら、

OrderService は PaymentService に依存している

と言います。

### 2.2 注入とは

必要なものを外から渡すことです。

自分で作るのではなく、引数などで受け取るイメージです。

### 2.3 Bean とは

Spring が管理しているオブジェクトのことです。

@Service や @Component がついたクラスは、Spring に管理される Bean になります。

つまり

```java
@Service
public class PaymentService {
}
```

この PaymentService はただの Java オブジェクトではなく、Spring 管理の Bean です。

### 2.4 コンテナとは

Bean を作って、保存して、必要な場所へ渡す Spring の管理箱みたいなものです。

イメージはこうです。

```text
Spring コンテナ
├─ PaymentService を作る
├─ OrderService を作る
└─ OrderService に PaymentService を渡す
```

## 3. 依存性注入を図で理解する

### 3.1 DI なし

```text
   OrderService
   └─ 自分で new PaymentService() する
```

コードで書くとこうです。

```java
public class OrderService {

    private PaymentService paymentService = new PaymentService();

}
```

この状態だと、OrderService が PaymentService の作り方まで知っています。

つまり結びつきが強いです。

### 3.2 DI あり

```text
OrderService
└─ PaymentService が必要です、と宣言するだけ

Spring
├─ PaymentService を作る
└─ OrderService に渡す
```

コードで書くとこうです。

```java
@Service
public class OrderService {

    private final PaymentService paymentService;

    public OrderService(PaymentService paymentService) {
        this.paymentService = paymentService;
    }

}
```

この状態だと、OrderService は「必要です」と言うだけです。

何をどう作るかは `Spring` が担当します。

## 4. メリット

### 4.1 結合が弱くなる

自分で new しないので、依存先を差し替えやすくなります。

たとえば本番では本物の PaymentService を使い、テストでは偽物を使うことができます。

### 4.2 テストしやすい

```java
public class FakePaymentService extends PaymentService {
@Override
public void pay() {
System.out.println("テスト用の支払い");
}
}
@Test
void test() {
PaymentService fake = new FakePaymentService();
OrderService orderService = new OrderService(fake);

    orderService.order();

}
```

依存を外から渡せるので、テストがとてもやりやすいです。

### 4.3 責務がきれいに分かれる

OrderService は注文処理だけに集中できます。

PaymentService の生成方法や管理方法まで考えなくてよくなります。

## 5. Spring Boot では何が起きているのか

Spring Boot を起動すると、Spring はアノテーションを見て Bean を探します。

たとえば次のようなものです。

- @Component
- @Service
- @Repository
- @Controller
- @RestController

これらがついたクラスを見つけると、Spring はそれを Bean として管理します。

その後、コンストラクタなどを見て、このクラスは何を必要としているかを調べて、自動で渡します。

イメージはこうです。

1.  Spring Boot 起動
2.  @Service などを探す
3.  Bean を作る
4.  必要な依存関係を調べる
5.  依存先を注入する
6.  実際の最小構成の例

    ```java
    @Service
    public class PaymentService {

        public void pay() {
            System.out.println("支払いしました");
        }

    }
    @Service
    public class OrderService {

        private final PaymentService paymentService;

        public OrderService(PaymentService paymentService) {
            this.paymentService = paymentService;
        }

        public void order() {
            paymentService.pay();
        }

    }
    ```

このとき Spring はだいたい次のように考えます。

```text
OrderService を作りたい
↓
コンストラクタを見る
↓
PaymentService が必要らしい
↓
PaymentService は Bean にある
↓
それを渡して OrderService を作る
```

## 7. 注入の方法

## 7.1 コンストラクタインジェクション

いちばん大事です。まずこれを覚えれば十分です。

```java
@Service
public class UserService {

    private final MailService mailService;

    public UserService(MailService mailService) {
        this.mailService = mailService;
    }

}
```

特徴

- 必要な依存がはっきり見える
- final にできる
- テストしやすい
- 途中で未設定になりにくい

### 7.2 セッターインジェクション

```java
@Service
public class UserService {

      private MailService mailService;

      @Autowired
      public void setMailService(MailService mailService) {
          this.mailService = mailService;
      }

}
```

後から setter で注入するやり方です。

特徴

- setter で注入する
- 必須ではない依存に向いている
- 途中で値がない状態も作れてしまう

### 7.3 フィールドインジェクション

```java
@Service
public class UserService {

      @Autowired
      private MailService mailService;

}
```

一見ラクに見えますが、避けた方がいい

理由

- 何に依存しているか見えにくい
- テストしにくい
- final にできない
- 設計が雑になりやすい
