# const 宣言なのにキャメルケースじゃない変数を見つけ方

### １．検索を開く

```text
cmd + shift + F
```

### 2.大文字、小文字の区別と、正規表現を有効にする

![searchVSCode.png](images/searchVSCode.png)

### 3.以下ワードで検索

```text
\bconst\s+[A-Z][A-Za-z0-9_]*
```
