## 特定dir以下のすべてのdirでgit grepする

```bash
cd ~/Projects
for i in *; do ( cd $i; echo $i; git grep "<検索>" HEAD ); done
```

## nuでorgのrepo全部clone

```nu
gh repo list PORT-INC -L 300 --json sshUrl | from json | par-each -t 10 {|f| git clone $f.sshUrl}
```

## bash

```bash
gh repo list PORT-INC --no-archived --limit 300 --json sshUrl | jq -r '.[].sshUrl' | xargs -I{} git clone {}
```

## リモートブランチの削除

```go
git push origin :branch-name
```

## 特定ファイルの変更差分のみdifff

```go
git diff --diff-filter=M
```

## 差分のあるファイル名のみ出す

```go
git diff --name-only <commit> <commit>
```

## 特定ファイルを別ブランチ・コミットからコピー

```go
git restore --source <tree-ish> <path>
```

## gitで誤操作したときにリカバリするには
git reflogでgitの操作履歴を見ることができる

```bash
git reflog
50b3a70 (HEAD -> main, origin/main) HEAD@{0}: pull: Fast-forward
32bd306 HEAD@{1}: checkout: moving from feature/query to main
af312ec (origin/feature/query, feature/query) HEAD@{2}: commit: select query
32bd306 HEAD@{3}: checkout: moving from main to feature/query
・
・
・
```

git resetでその作業の時点に戻れる

```bash
git reset --hard HEAD@{1}
```

## 追跡されてないファイルをすべて削除

```bash
git clean -df
```

- `git reset --hard`と組み合わせることでクリーンな`HEAD`の状態に戻すことができる

## commitし忘れた変更を追加

```bash
git commit --amend --no-edit
```

## ブランチ名の変更

```bash
git branch -m [<oldbranch>] <newbranch>
```

## マージ済み・マージされてないブランチの確認

```bash
git branch --merged
git branch --no-merged
```

## 問題のあるコミットを調査

[git bisect で問題箇所を特定する - Qiita](https://qiita.com/usamik26/items/cce867b3b139ea5568a6)
