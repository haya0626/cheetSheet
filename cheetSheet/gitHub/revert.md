# revert

## **revert とは**

**既存のコミットを取り消すためのコマンド**

「取り消したいコミットを打ち消すようなコミットを新しく作成する」という処理によって、既存のコミットを取り消す。「変更を取り消した」という記録を残すのが大きな特徴

![reset-revert-re-003.png](images/reset-revert-re-003.png)

## **git revertコマンドの主な用途**

**`git revert`** は、主に次のような場面で活躍する。

- リモートにpush済みのコミットを、履歴を壊さずに取り消したいとき
- チーム全員に「どの変更を取り消したか」という記録を残す必要がある場合
- 共有ブランチ内でコミットを取り消す際に、他の人の作業に影響を与えず元に戻したいとき

## 使用コマンド

**① 単一コミットを戻す**

```powershell
# 例: git revert 3ad2a09
git revert <commit>
```

→ 対象コミットを打ち消す新コミットが作成される

-eを書き加えると自分の言葉でメッセージを書けるようになる

```powershell
git revert -e 3ad2a09
```

ターミナルに以下のような文が出るので、必要なら手動で修正する

```
powershell
	Revert "Fix: update payment logic"
		
	Reason:
	- Caused regression in subscription flow on 2025-11-02
	- Incident ID #4421
	- Will re-apply after root cause fix
```

**② 複数コミットをまとめて戻す**

```powershell
# 例: 3つの個別コミットをまとめて指定
git revert <commitIdA> <commitIdB> <commitIdC>
```

③ **直前の revert をやっぱり取り消したい（“revert の revert”）**

```powershell
# もとの変更を「復活」させるイメージ
git revert <revert_commit>
```

**④ 全部なかったことにしたい（やり直し）要説明　実践**

```powershell
git revert --abort   # 進行中の revert を中止
```

**⑤ 一部だけ飛ばして進めたい　実践**

```powershell
git revert --skip
```

→ 対象コミットが複数で、特定のコミットは飛ばしたいときに使う

**⑥ マージされたコミットを取り消したい**

```powershell
git revert -m 1 <commitId>
```

**-m で指定する理由 　mの略語**

マージコミットは普通のコミットと違って**親が2つ以上ある（合流点）**から、「どっち側の変更を打ち消せばいいの？」と Git が判断できないため

数字はどちらの親かを指定する（git show やgit logで確認可）

基本的には1を使うことが多い

```markdown
(main) A --- B ------- C
                   \
(feature)           D --- E
```

merge されたとき

```
A --- B --- C ----------- M    ※ M がマージコミット
                   \     /
                    D---E
```