# Annotation

実務で見かけたアノテーションをまとめた

## 1. @RestController

```java
@RestController
public class UserController {
}
```

意味 : そのクラスを、REST API のリクエストを受け付ける Controller として Spring に登録する

内部的には、次の 2 つを組み合わせたもの

```java
@Controller
@ResponseBody
```

メソッドが返した Java オブジェクトは、通常 JSON などに変換されてレスポンス本文として返される

サンプル

```java
@RestController
public class UserController {

    @GetMapping("/users/1")
    public User getUser() {
        return new User(1, "田中");
    }
}
```

レスポンスイメージ

```
{
  "id": 1,
  "name": "田中"
}
```

画面の HTML を返す Spring MVC では @Controller、JSON を返す REST API では @RestController、という使い分けが基本

## 2. @RequestMapping

意味 : どの URL へのリクエストを、どの Controller やメソッドで処理するかを設定する

URL だけでなく、HTTP メソッド、リクエストパラメータ、ヘッダー、Content-Type なども条件にでる。クラスに付けると共通 URL、メソッドに付けると個別 URL を指定することが可能

サンプル

```java
@RestController
@RequestMapping("/api/users")
public class UserController {

    @GetMapping("/{id}")
    public User getUser(@PathVariable Long id) {
        // GET /api/users/1
        return userService.findById(id);
    }

    @PostMapping
    public void createUser() {
        // POST /api/users
    }
}
```

### クラスとメソッドの組み合わせ

```java
@RequestMapping("/api/users") // 共通部分
```

```java
@GetMapping("/{id}")          // 個別部分
```

この 2 つが結合される

```java
GET /api/users/{id}
```

その他使用するサンプル

```java
@GetMapping
@PostMapping
@PutMapping
@DeleteMapping
@PatchMapping
```

## 3. @Operation

```java
@Operation(summary = "ユーザーを取得する")
```

意味 : OpenAPI、Swagger の API 仕様書に、その API が何をするものなのか説明を追加する。

※ summary、description、レスポンス、セキュリティ情報などを設定可能

```java
@Operation(
    summary = "ユーザー取得",
    description = "指定されたユーザーIDに該当するユーザーを取得します"
)
@GetMapping("/{id}")
public UserResponse getUser(@PathVariable Long id) {
    return userService.findById(id);
}
```

## 4. @RequestBody

```java
public void create(@RequestBody UserRequest request)
```

意味 : HTTP リクエストの本文を読み取り、JSON などのデータを Java オブジェクトへ変換します。

Spring では、HttpMessageConverter を通してリクエスト本文がデシリアライズされます

#### デシリアライズとは

> ネットワーク経由で受信したりファイルから読み込んだりしたデータ列（文字列やバイナリ）を、プログラムが内部で直接扱えるオブジェクトやデータ構造に復元する処理のこと

サンプル

フロント側の送信

```json
{
  "name": "田中",
  "age": 25
}
```

受け取る Java クラス

```java
public class UserRequest {

    private String name;
    private Integer age;

    // getter、setter
}
```

Controller

```java
@PostMapping
public void createUser(@RequestBody UserRequest request) {
    System.out.println(request.getName());
    // 実行結果 : 田中
}
```

## 5. @Service

```
@Service
public class UserService {
}
```

意味 : そのクラスを、サービス層を担当する Spring Bean として登録する。

@Service は @Component の一種なので、Spring のコンポーネントスキャンによって自動検出され、DI できるようになります。

サンプル

```java
@Service
public class UserService {

    public User findById(Long id) {
        // ユーザー取得処理
    }
}
```

```java
@RestController
@RequestMapping("/users")
public class UserController {

    private final UserService userService;

    public UserController(UserService userService) {
        this.userService = userService;
    }
}
```

次のような、アプリケーションの処理やビジネスルールを実装するときに使用するイメージ

- ユーザーを登録する
- 注文を確定する
- 在庫があるか確認する
- 料金を計算する
- 複数の Repository を呼び出す

## 6. @Transactional

```java
@Transactional
public void createUser() {
}
```

意味 : 複数の DB 操作を、1 つのトランザクションとして処理する

簡単にいうと

> 全部成功したら確定
>
> 途中で失敗したら元に戻す

複数の DB 更新を、必ずセットで成功させたいときに使用します

```java
@Service
public class TransferService {

    @Transactional
    public void transfer(
            Long fromUserId,
            Long toUserId,
            int amount
    ) {
        accountRepository.withdraw(fromUserId, amount);
        accountRepository.deposit(toUserId, amount);
    }
}
```

## 7. @Scope

```java
@Scope("prototype")
```

意味 : Spring が管理するオブジェクト、つまり Spring Bean を、いつ生成し、どの範囲で使い回すかを指定します。
