# Spring Boot レイヤー構造（全体像）

Spring Boot では、アプリケーションを役割ごとに分割する

`Layered Architecture`をよく使います

```text
[ Client ]
    ↓
[ Controller ]  ← プレゼンテーション層
    ↓
[ Service ]     ← ビジネスロジック層
    ↓
[ Repository ]  ← データアクセス層
    ↓
[ Database ]

※ Model は全層で使われるデータ構造
```

# 各レイヤーの定義

## 1. Controller（コントローラー）

役割：外部とのインターフェース（入口）

■ 主な責務

- HTTP リクエストの受付（GET / POST など）
- リクエストデータの受け取り（JSON → Java）
- Service の呼び出し
- レスポンスの返却（Java → JSON）

■ 特徴

- ビジネスロジックを書かない
- 軽量でシンプルに保つ

■ サンプル

```java
@RestController
@RequestMapping("/users")
public class UserController {

    @PostMapping
    public User create(@RequestBody User user) {
        return userService.createUser(user);
    }
}
```

## 2. Service（サービス）

役割：ビジネスロジックの中核（アプリの頭脳）

■ 主な責務

- 業務ルールの実装
- 複数処理の統合
- トランザクション管理

■ 具体例

- 「在庫がある場合のみ購入可能」
- 「登録時に初期値を設定」
- 「複数テーブルの更新」

■ 特徴

- 再利用可能なロジック
- Controller と Repository の橋渡し

■ サンプル

```java
@Service
public class UserService {

    @Transactional
    public User createUser(User user) {
        validate(user);
        user.setCreatedAt(LocalDateTime.now());
        return userRepository.save(user);
    }
}
```

## 3. Repository（リポジトリ）

役割：データアクセス（DB とのやり取り）

■ 主な責務

- CRUD 操作（Create / Read / Update / Delete）
- データベースとの通信

■ 特徴

- Spring Data JPA で自動実装可能
- SQL を直接書かないことが多い

■ サンプル

```java
public interface UserRepository extends JpaRepository<User, Long> {
}
```

## 4. Model（モデル / Entity）

役割：データ構造の定義

■ 主な責務

- データの保持
- DB テーブルとのマッピング

■ 種類

- 種類 説明
- Entity DB テーブルと対応
- DTO 通信データ用
- Domain Model ビジネス用モデル

■ 例（Entity）

```java
@Entity
public class User {

    @Id
    private Long id;

    private String name;

}
```

## データの流れ（重要）

登録処理の例

1. Controller：リクエスト受信
1. Service：業務ロジック実行
1. Repository：DB 保存
1. Controller：結果返却

```text
Client
↓
Controller（受け取る）
↓
Service（処理する）
↓
Repository（保存する）
↓
DB
```

## レイヤー分割の目的

1. 保守性の向上
   - 修正箇所が限定される
1. 再利用性
   - Service ロジックを複数 Controller で利用可能
1. テスト容易性
   - 各層を個別にテスト可能
1. 責務の分離（Separation of Concerns）
   - 役割が明確になる

## NG パターン

1. Controller にロジックを書く
1. Service を通さず Repository を直接呼ぶ
1. Repository に業務ロジックを書く

## 推奨ルール

1. Controller → Service → Repository の一方向依存。各層は責務を守る
1. Service にビジネスロジックを集約
   Controller → Service → Repository → DB

### パッケージ構成例

```text
com.example.app
├─ controller
├─ service（または svc）
├─ repository
├─ model（entity / dto）
└─ config
```
