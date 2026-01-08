# カスタムフックとは

## 標準フックを組み合わせって作る独自関数のこと

代表的なフック

- `useState` : 状態管理
- `useEffect` : 副作用（データ取得、購読、タイマーなど）
- `useRef` : DOM 参照やミューテーション変数
- `useContext`: コンテキスト利用
- `useReducer`: 複雑な状態ロジック
- `useMemo, useCallback`: パフォーマンス最適化

---

### サンプルコード

```tsx
import React, { useState, useEffect } from "react";

const useLocalStorage = (key, defaultValue) => {
  // 初回レンダリング時にローカルストレージから値を取得する
  const [value, setValue] = useState(() => {
    const jsonValue = window.localStorage.getItem(key);
    return jsonValue !== null ? JSON.parse(jsonValue) : defaultValue;
  });

  // valueが変更されるたびにローカルストレージに保存する
  useEffect(() => {
    window.localStorage.setItem(key, JSON.stringify(value));
  }, [key, value]);

  // [value, setValue]の形でstateと更新関数を返す
  return [value, setValue];
};

export default useLocalStorage;
```

### ルール

- 命名規則として use から始める
- カスタムフック内の関数名称に use をつけてはいけない
  □ react の公式ルールでフック関数は use から始める必要がある
  □ React では `eslint-plugin-react-hooks` という公式の ESLint プラグインがあり、ESlint がフック関数と判断してしまう

### 呼び出し方

```tsx
import React from "react";
import { useWindowWidth } from "./useWindowWidth";

const MyComponent = () => {
  const width = useWindowWidth();

  return <p>現在のウィンドウ幅: {width}px</p>;
};
```

- 関数の return で返されたものを代入させるだけでよいので、複数画面に同じ処理を何回も記載する必要がなくなる
- カスタムフックに複数の関数を返す場合は、分割代入して利用することが多い
