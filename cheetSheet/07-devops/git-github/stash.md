## stash

### stash をコメント付きで保存したい

```powershell
git stash push -m "メッセージ"
```

### stash をコメント付きで保存したい(未追跡含)

```powershell
git stash push -u -m "メッセージ"
```

---

### 過去の stash したリストが見たい

```powershell
git stash list
```

---

### stash を適応させたい（最新のもの）

```powershell
git stash apply
```

---

### stash の削除

```powershell
git stash clear
```

---

### 過去 x 番目の stash を適応させたい

1.git stash list で過去ログの確認

```powershell
stash@{0}: WIP on main: abc1234 修正1
stash@{1}: WIP on dev: def5678 作業途中2
stash@{2}: WIP on feature: ghi9012 テスト追加
```

- `stash@{0}` = 一番新しいスタッシュ
- `stash@{1}` = 2 番目に新しい
- `stash@{2}` = 3 番目に新しい

  2.指定したい stash の指定
