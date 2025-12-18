## commit

### 直前のコミットを取り消してステージング前にする

```powershell
git reset --mixed HEAD~1
```

2個前ならHEAD~2

---

### プッシュしてしまった後のコミットを取り消す

1.コミットのログを確認する

```powershell
git log --oneline
```

イメージ

```powershell
a1b2c3d 修正: ボタン色変更        ← HEAD（最新）
d4e5f6g 追加: ログインバリデーション
h7i8j9k 初期設定の調整
```

2.該当箇所を確認してリベース

```powershell
git rebase -i HEAD~3
```

3.iを押下して編集モードにした後、削除したいコミットをdropに書き換える

イメージ

```powershell
pick h7i8j9k 初期設定の調整
drop d4e5f6g 追加: ログインバリデーション
pick a1b2c3d 修正: ボタン色変更
```

4.「;wq」と入力して実行

5.push

```powershell
git push --force-with-lease
```