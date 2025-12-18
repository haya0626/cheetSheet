## stash

### stashをコメント付きで保存したい

```	powershell
git stash push -m "メッセージ"
```

---

### 過去のstashしたリストが見たい

```	powershell
git stash list
```

---

### stashを適応させたい（最新のもの）

``` powershell
git stash apply
```

---

### stashの削除

```	powershell
git stash clear
```

---

### 過去x番目のstashを適応させたい

1.git stash listで過去ログの確認

``` powershell
stash@{0}: WIP on main: abc1234 修正1
stash@{1}: WIP on dev: def5678 作業途中2
stash@{2}: WIP on feature: ghi9012 テスト追加
```

- `stash@{0}` = 一番新しいスタッシュ
- `stash@{1}` = 2番目に新しい
- `stash@{2}` = 3番目に新しい

2.指定したいstashの指定