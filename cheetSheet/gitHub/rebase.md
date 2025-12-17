# rebase

## rebaseとは

**ブランチの履歴を付け替え（付け直し）するためのコマンド**

ブランチの履歴を付け替えて直線的に並べ直すことで、履歴をきれいに整理できる

１/2

![git-rebase-002.png](images/git-rebase-002.png)

2/2

![git-rebase-003.png](images/git-rebase-003.png)

## **rebaseの主な使用用途**

**`git rebase`** は、主に次のような場面で活躍する。

- PR（Pull Request）を出す前に余計なコミットや雑な履歴を整理したい
- コミットコメントの修正や、コミットの削除を行いたい
- コミット順を並べ替えたい

## 使用コマンド

**① 他人の最新変更を取り込む（履歴を綺麗に保つ）**

自分の featureが古いとき → 自分の変更が main の後ろにきれいに並ぶ

```markdown
git checkout feature
git fetch origin
git rebase origin/main
```

**② PR前に履歴を整理したい ※実演する**

直近の5コミットを編集するケース

```markdown
git rebase -i HEAD~5
```

※ i はInteractive rebase の略称で、コミットを別ブランチの先頭に乗せるときに、**どのように乗せるか**を指定できるモード

エディタが開き、こう表示される

```markdown
pick abc123   add user model
pick dce456   fix typo
pick ef7890   debug
```

**ここで操作できる選択肢一覧**

| コマンド | 内容 |
| --- | --- |
| `pick` | そのまま使う |
| `reword` | メッセージを書き換える |
| `edit` | コミットの内容を修正する |
| `squash` | 前のコミットにまとめる |
| `drop` | コミットを削除する |

③ rebase中にやっぱやめたい

```markdown
git rebase --abort
```

編集が完了して反映させたい場合

```markdown
git push --force-with-lease
```

※ force-with-leaseとはPUSHの際、ローカルrefとリモートrefを比較しローカルが最新か判定し、最新でなければPUSHが失敗するというもの。

**git push --forceは基本つかわないこと!!!!!!**

### 使用してOK,NGパターン

---

### ✅ OKなパターン

| 状況 | rebaseしてOK？ | 理由 |
| --- | --- | --- |
| ✅ 自分だけが触っているブランチ | OK | ほかの人に影響がない |
| ✅ 他の誰もそのブランチをpullしていない | OK | 履歴が食い違わない |
| ✅ push後すぐ、まだ誰も触っていない | OK | 安全 |

### ❌ ダメなパターン

| 状況 | ダメな理由 |
| --- | --- |
| チームメンバーがその履歴を使って作業している | 他人の履歴と衝突 → pull不能、push不能、最悪コード消える |
| すでにPRが動いていて、レビュー中 | 履歴が消えるのでレビュアーが混乱 |
- 個人の feature ブランチ内だけで使うこと
- 共有ブランチでは原則使用しないこと（履歴が壊れる）