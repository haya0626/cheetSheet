# fix up

rebase -i で使えるオプションの一つ

コミットをまとめつつ、統合されたコミットコメントは破棄する

実際に使用して、競合が起きやすかったので、軽微な漏れの時のみ使用することを心がけます

## 動作イメージ

- pick の下に統合させたいコミットを対象に`fixed`を付与する

vim

```bash
git rebase -i HEAD~2
```

```bash
pick a1b2c3 add login form
fixup d4e5f6 fix typo
```

結果

```bash
add login form
```

2 つ目のコミットメッセージは消える

## 過去のコミットに統合させたい

手動だけだと困る問題

- 統合させたいコミットが複数ある
- 手動で pick を並び替えるのがめんどくさい
- ハッシュコードを手動で触るのが怖い

1.  現在の修正を過去コミットに吸収したい場合

    ケース : Add login page (直近３番目)に対する軽微修正を入れ忘れた

    コミット履歴

    ```bash
    a1b2c3d Add login page
    b2c3d4e Add tests
    c3d4e5f Update README
    ```

    使用コマンド

    `git log --oneline`でコミットのハッシュコードをコピペすると安全

    ```bash
    git add .
    git commit --fixup a1b2c3d
    ```

    ※コミットメッセージは自動でこうなる

    ```bash
    fixup! Add login page
    ```

    コミットのログが適切か確認（間違って違うコミットに統合するのを防ぐため）

    中止したい場合は`--abort` で一旦 rebase 終了

    ```bash
    git rebase -i HEAD~4
    ```

    結果

    ```bash
    pick a1b2c3d Add login page
    fixup xxxxxxx fixup! Add login page
    pick b2c3d4e Add tests
    pick c3d4e5f Update README
    ```

    保存（:wq）して閉じると fixup のコミットが a1b2c3d に吸収される

1.  過去のコミット済みのものを過去コミットに吸収したい場合

    ケース : `c333333`を`a111111`に統合させたい

    履歴

    ```bash
    d444444 Add tests
    c333333 Fix typo in login page
    b222222 Add header
    a111111 Add login page
    ```

    - reword でコミットを書き換える
    - 統合させたいコミット名をコピペする

    ```bash
    pick   a111111 Add login page
    pick   b222222 Add header
    reword c333333 Fix typo in login page
    pick   d444444 Add tests
    ```

    統合させたいコミットに合わせてコメント変更

    ```bash
    fixup! Add login page
    ```

    rebase のログが適切か確認（間違って違うコミットに統合するのを防ぐため）

    中止したい場合は`--abort` で一旦 rebase 終了

    ```bash
    git rebase -i HEAD~4
    ```

    統合前の履歴

    ```bash
    pick  a111111 Add login page
    fixup x999999 fixup! Add login page
    pick  b222222 Add header
    pick  d444444 Add tests
    ```

    保存（:wq）して閉じると fixup のコミットが a111111 に吸収される

    別の方法

    - `pick`の状態で直接行移動させて、統合させたいコミットに合わせてコメント変更することもできる
    - `reword`で書き換えた上で、再度 rebase して確認取った方が、fixup が適切になっているかの担保が取れるので（指定コミットの直下に配置されるため）前者がおすすめ

    Vim 操作

    **上に移動**

    ```
    dd = 行切り取り
    P = 上に貼り付け
    ```

    **下に移動**

    ```
    dd = 行切り取り
    p = 下に貼り付け
    ```

## 統合後、コミットコメントを変えたくなったら

`reword`で書き換えることで対応可
