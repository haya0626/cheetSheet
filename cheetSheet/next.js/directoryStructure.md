# Next.js ディレクトリ構成

### 前提 : Next.js プロジェクトでファイルやフォルダを整理する方法には「正しい」「間違っている」はない

## サンプル

```
src
|
+-- app        # Routing files（Next.jsが標準で提供するファイル）
|
+-- components # 横断的（ドメインに依存しない）なUIコンポーネント
|
+-- features   # 特定のドメイン・機能に関係するコンポーネント
|
+-- hooks      # ドメインに依存しない、横断的なhooks
|
+-- providers  # アプリケーションプロバイダー
|
+-- utils      # 横断的な汎用関数
|
+-- constants  # 横断的な定数
|
+-- types      # 横断的な型定義
|
+-- styles     # スタイリング（css）に関するファイル
|
+-- lib        # ライブラリの処理や標準処理を共通化したコード
|
+-- e2eTests      # 自動テスト関連
```

`app`: ルーティングの責務を持つディレクトリ。Routing files（Next.js が標準で提供するファイル。page.tsx や layout.tsx など）のみを配置

`components`: ボタンやモーダルなど、再利用可能でドメインに依存しない UI コンポーネントを格納する

`features`: 特定の機能に関連するファイルを集約し、機能ごとのサブディレクトリに格納しています。例えば、ユーザー管理機能に関連するコンポーネント、hooks、ユーティリティ関数、型定義などをこのディレクトリ内でまとめる

`hooks`: グローバルに使用されるカスタムフックを格納します。ドメインに依存しない汎用的なロジックはここにまとめる

`providers`: React の Context API やその他のプロバイダーを配置します。グローバルな状態管理やテーマ設定など、アプリケーション全体で使用されるプロバイダーをここに集約する

`utils`: 簡潔で再利用可能なユーティリティ関数を格納します。これには、日付処理、文字列操作、API 通信など、特定のドメインに依存しない関数が含まれる

`constants`: アプリケーション全体で使用される定数を格納する

`types`: TypeScript の型定義をまとめる

`styles`: グローバルスタイルやテーマ設定、Tailwind CSS のカスタマイズを含むスタイルシートをここに配置する

`lib`: プロジェクト内で再利用されるライブラリや、外部ライブラリの実装をここに格納する

`tests`: ユニットテストや統合テストなど、自動テストに関連するファイルを格納する

参考になるベストプラクティス[bulletproof-react](https://github.com/alan2207/bulletproof-react/blob/master/docs/project-structure.md)

## features の切り方(サンプル)

```
features
|
+-- user            # 特定のドメインに関するディレクトリ
|   |
|   +-- hooks       # 特定のドメインに関するhooks
|   +-- utils       # 特定のドメインに関する汎用関数
|   +-- components  # 特定のドメインに関するコンポーネント
|   +-- types       # 特定のドメインに関する型
|   +-- providers   # 特定のドメインに関するプロバイダー
|   +-- pages       # appディレクトリ配下から呼び出されるページコンポーネントを格納する
|       +-- users_page.tsx
|       +-- user_new_page.tsx
|       +-- ...
+-- ...
```

- 機能 id ごとにフォルダを作成すれば、ドメインに依存するものすべて 1 フォルダに集約できる
- 横断的に使用するものと画面固有使用で分離できる
- ジャンルごとに１個にまとめれない（ type は すべて type フォルダに集約など）

## features を使用しないケース

シンプルさ重視

```
src
|
+-- app
|
+-- components
|       +-- sampleList1　# サンプル１画面
|       +       +-- sampleList1Child
|       +-- sampleList2 # サンプル２画面
|               +-- sampleList2Child
|
+-- hooks
|
+-- providers
|
+-- utils
|
+-- constants
|
+-- types
|      +-- sampleList1
|               +-- sampleList1type
|      +-- sampleList2
|               +-- sampleList2type
|
+-- styles
|
+-- lib
|
+-- e2eTests
```

- ２階層目ですべて集約させれる

## 子コンポーネントの切り方(サンプル)

Atomic Design をベースに作成する

```
components
|
|-- atoms            # UIの最小単位
|-- molecules        # 2つ目の構成要素
|-- organisms        # atoms と moleculesの組み合わせ
+-- ...
```
