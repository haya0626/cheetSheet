# useRouter とは

## 1. useRouter とは

useRouter は **Next.js（App Router）で「コードから画面遷移（ルーティング）を行うためのフック」**。

ボタン押下・ログイン成功後・フォーム送信後など、ユーザー操作に応じてページを移動したいときに使います。

> App Router（app/ ディレクトリ）では next/navigation から import する

## 2. どこで使う？（重要）

useRouter は Client Component でしか使えない。

- OK："use client"; があるコンポーネント

- NG：Server Component（デフォルト）や layout.tsx（基本 Server）

### Client Component にする

```ts
"use client";

import { useRouter } from "next/navigation";
```

## 3. import（正しい場所）

- App Router の正解

```ts
import { useRouter } from "next/navigation";
```

- Pages Router の import（古い）

```ts
import { useRouter } from "next/router";
```

**next/router は pages/ ディレクトリのときに使うもの。**

## 4. 使い方（最小）

```ts
"use client";
import { useRouter } from "next/navigation";

export default function Example() {
  const router = useRouter();

  return <button onClick={() => router.push("/top")}>Top へ移動</button>;
}
```

## 5. router の代表メソッド一覧

### 5.1 router.push(url)

履歴に残して移動します（ブラウザの「戻る」で戻れる）。

```ts
router.push("/top");
```

用途例

- 一般的なページ遷移
- 「詳細へ」「次へ」など自然に戻れる導線

### 5.2 router.replace(url)

履歴を置き換えて移動します（戻るで戻りにくい）。

```ts
router.replace("/top");
```

用途例（実務で超重要）

- ログイン成功後
- 認証後の遷移
- 完了画面 → 戻って再送信を防ぐ

ログイン後に戻るでログイン画面へ戻せるのが嫌な場合、replace が推奨。

### 5.3 router.back()

ブラウザの戻ると同じ。

```ts
router.back();
```

用途例

- 画面上に「戻る」ボタンを付けたい
- モーダルや詳細画面から戻る

### 5.4 router.forward()

ブラウザの進むと同じ。

```ts
router.forward();
```

### 5.5 router.refresh()

現在のルートを再取得（再レンダ）します。

Server Components のデータ再取得（再フェッチ）にも使われます。

```ts
router.refresh();
```

用途例

- 更新ボタン
- データ保存後に一覧を更新

Server Component の fetch を再実行したい

### 5.6 router.prefetch(url)

次に行きそうなページを事前読み込みして高速化します。

```ts
router.prefetch("/top");
```

用途例

- 「次へ」ボタンに hover したら先読み
- SPA 的な体感速度を上げたい

※ Link コンポーネントは自動で prefetch する場合もある。
