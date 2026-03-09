# fix up

rebase -i で使えるオプションの一つ

コミットをまとめつつ、統合されたコミットコメントは破棄する

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

## autosquash

- 統合させたいコミットが複数ある
- 手動で pick を並び替えるのがめんどくさい
- ハッシュコードを手動で触るのが怖い

上記の問題点を`autosquash`が解決してくれる

### <u>autosquash は、fixup! や squash! という名前のコミットを自動的に並べ替えて、rebase 時にまとめてくれる Git の機能</u>

1.  直前のコミットに吸収したい場合

    ```
    git add .
    git commit --amend --no-edit
    ```

    意味

    - --amend で直前コミットを書き換える
    - --no-edit でコミットメッセージはそのまま

1.  直前ではない過去コミットに吸収したい

    ケース : Add login page (直近３番目)に対する軽微修正を入れ忘れた

    コミット履歴

    ```bash
    a1b2c3d Add login page
    b2c3d4e Add tests
    c3d4e5f Update README
    ```

    使用コマンド

    ```bash
    git add .
    git commit --fixup a1b2c3d
    ```

    ※コミットメッセージは自動でこうなる

    ```bash
    fixup! Add login page
    ```

    コミットのログが適切か確認（間違って違うコミットに統合するのを防ぐため）

    大丈夫なら--amend で一旦 rebase 終了

    ```bash
    git rebase -i HEAD~4
    ```

    autosquash して履歴を自動で並び替える

    ```bash
    git rebase -i --autosquash HEAD~4
    ```

    結果

    ```bash
    pick a1b2c3d Add login page
    fixup xxxxxxx fixup! Add login page
    pick b2c3d4e Add tests
    pick c3d4e5f Update README
    ```

    保存して閉じると fixup のコミットが a1b2c3d に吸収される

1.  過去のコミット済みのものを対象とする場合

    ケース : `c333333`を`a111111`に統合させたい

    履歴

    ```bash
    d444444 Add tests
    c333333 Fix typo in login page
    b222222 Add header
    a111111 Add login page
    ```

    - reword でコミットを書き換える(!fix を使用)
    - 統合させたいコミット名をコピペする

    ※`git log --oneline` で吸収先件名を確認してミスを防止

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

    コミットのログが適切か確認（間違って違うコミットに統合するのを防ぐため）

    大丈夫なら--amend で一旦 rebase 終了

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

    自動統合

    ```
    git rebase -i --autosquash HEAD~4
    ```

## 統合後、コミットコメントを変えたくなったら

`reword`で書き換えることで対応可
