# 実習手順書：good first issue に取り組む

対象：[tamadalab/welfare-time](https://github.com/tamadalab/welfare-time)
コマンドは、ターミナル（Windows は Git Bash か WSL）で実行します。`<...>` の部分は、自分の値に置き換えてください。

全体の流れ：**0 準備 → 1 Issue を選ぶ → 2 Fork と Clone → 3 動作確認 → 4 ブランチ → 5 修正 → 6 確認 → 7 commit と push → 8 PR → 9 レビュー対応**

---

## 0. 準備

次のものが入っているか確認します。

```bash
git --version
gh --version
python3 --version   # 3.11
node --version
hugo version        # extended 版
just --version
```

| ツール | 入れ方（macOS の例） |
|---|---|
| git | `brew install git` |
| gh (GitHub CLI) | `brew install gh` |
| Python 3.11 | `brew install python@3.11` |
| Node | `brew install node` |
| Hugo (extended) | `brew install hugo` |
| just | `brew install just` |

GitHub にログインします（ブラウザが開くので、案内に従います）。

```bash
gh auth login
gh auth status
```

Git に名前とメールを設定します（commit に記録されます）。

```bash
git config --global user.name "<あなたの名前>"
git config --global user.email "<GitHub に登録したメール>"
```

> 公開したくないメールの場合は、GitHub の Settings → Emails で、noreply アドレスを確認して使えます。

---

## 1. Issue を選ぶ

1. [good first issue の一覧](https://github.com/tamadalab/welfare-time/issues?q=is%3Aissue+is%3Aopen+label%3A%22good+first+issue%22)を開く。
2. 取り組めそうな Issue を選び、本文を最後まで読む。
3. 他の人が「取り組みます」と宣言していないか、コメントを確認する。
4. 宣言がなければ、Issue に「取り組みます」とコメントする。

**以降、`<番号>` は、選んだ Issue の番号です。**

---

## 2. Fork と Clone

自分のアカウントに Fork し、手元に Clone します。

```bash
cd ~/
gh repo fork tamadalab/welfare-time --clone
cd welfare-time
```

remote を確認します。`origin` が自分の Fork、`upstream` が元のリポジトリになっていれば成功です。

```bash
git remote -v
```

---

## 3. 動作確認（修正する前に）

修正の前に、そのままの状態で動くことを確認します。

```bash
pip install -r requirements.txt
npm ci
just generate
just serve
```

ブラウザで <http://localhost:1313/welfare-time/> を開き、サイトが表示されれば成功です。止めるときは、ターミナルで `Ctrl + C` を押します。

テストも実行します。

```bash
just test
```

> 動かないときは「付録：困ったとき」を見てください。ここで詰まった場合は、修正に進まず、教員か周りの人に聞いてください。

---

## 4. ブランチを作る

`main` で直接作業せず、作業用のブランチを作ります。名前は `fix-<番号>-<内容>` のようにします。

```bash
git switch -c fix-<番号>-<内容>
# 例: git switch -c fix-82-label-typo
```

---

## 5. 修正する

- Issue の「対応案」「修正方法」に沿って、ファイルを編集します。
- **Issue に書かれた範囲だけ**を変更します。ついでの修正はしません。
- Issue の行番号は、ずれている場合があります。関数名や文字列で探してください。

```bash
grep -rn "<探したい文字列>" .
```

変更内容を確認します。

```bash
git status
git diff
```

意図しない変更が入っていないかを、必ず確認します。

---

## 6. 動作を確認する

Issue の「確認方法」に従います。共通で、次を実行します。

```bash
just test
just serve   # ブラウザで見た目や動作を確認
```

- 自分の目で確認した内容を、メモしておきます（PR に書きます）。
- 画面に関わる修正は、スクリーンショットを撮っておくと便利です。

---

## 7. commit して push する

```bash
git add <変更したファイル>
git commit -m "<変更内容を一行で>"
# 例: git commit -m "LABE_ を LABEL_ に改名する"
git push -u origin fix-<番号>-<内容>
```

- `git add .` は、意図しないファイルが入りやすいので、避けます。
- commit メッセージは、1行目に変更内容を簡潔に書きます。

---

## 8. Pull Request を作る

```bash
gh pr create --repo tamadalab/welfare-time --base main
```

または、`push` 後に表示される URL、もしくは GitHub のブランチのページから、ブラウザで作ります。

**宛先（base）は `tamadalab/welfare-time` の `main`、元（compare）は自分の Fork のブランチです。**

PR のタイトルと本文を書きます。**テンプレートはありません。自分で書きます。** 次の項目を含めてください。

```markdown
## 概要
何をどう変更したかを書く。

## 関連 Issue
Closes #<番号>

## 確認方法
どのコマンド・操作で、どう確認したか。

## AI の利用
自分で行ったこと / AI に任せたことを書く。使っていない場合は「使っていません」。

## 補足
気になる点、迷った点があれば。
```

PR を作ったら、次を確認します。

- 画面の「Files changed」に、**意図した変更だけ**が出ている。
- CI（`テスト`）が実行されている。

---

## 9. レビューへの対応

- コメントや CI の結果は、GitHub の通知と PR のページで確認します。
- 修正が必要なときは、**同じブランチ**で直して、push します。PR は自動で更新されます。

```bash
# ファイルを直してから
git add <変更したファイル>
git commit -m "<指摘への対応を一行で>"
git push
```

- 指摘には、感謝を添えて返信します。納得できないときは、理由を添えて質問します。
- 他の人の PR にコメントするときは、[行動規範](https://github.com/tamadalab/welfare-time/blob/main/CODE_OF_CONDUCT.md)に従います。

### CI が失敗したとき

PR のページで失敗した項目の「Details」を開き、ログの赤い部分を読みます。手元で `just test` を実行して、同じ失敗が再現するかを確認します。

### conflict が起きたとき

PR に「This branch has conflicts」と出た場合は、最新の `main` を取り込んで、衝突した箇所を直し、push します。やり方は、「付録：Git と GitHub の使い方が分からないとき」の参考資料を見てください。

---

## 提出

次の2つを行います。

1. PR を出す（上の手順）。
2. Moodle の課題に、次を書く。
   - PR の番号
   - 取り組んだ感想
   - AI の利用（自分で行った範囲 / AI に任せた範囲）

締切：**2026-11-14 (Sat) 13:15**

---

## 付録：困ったとき

| 症状 | 確認すること |
|---|---|
| `gh: command not found` | gh を入れる（0 を参照） |
| `Permission denied` / 認証エラー | `gh auth status` で、ログインできているか確認 |
| `just: command not found` | just を入れる |
| `just generate` が失敗する | Hugo が **extended 版**か、`pip install` と `npm ci` が終わっているか確認 |
| `npm ci` が失敗する | Node のバージョンを確認（README の「開発環境」を参照） |
| 間違えて `main` で作業してしまった | commit する前なら、`git switch -c <新しいブランチ>` で、そのまま作業を移せる |
| 変更を全部やり直したい（commit 前） | `git restore <ファイル>`（そのファイルの変更が消える。実行前に確認） |
| push が拒否された | ブランチ名と、`origin`（自分の Fork）に push しているか確認 |
| 何が起きたか分からない | `git status` と `git log --oneline -5` の結果を、教員に見せる |

質問するときは、**実行したコマンド、エラーメッセージの全文、使っている OS**を伝えてください。

---

## 付録：Git と GitHub の使い方が分からないとき

Git の基本操作や conflict の解消方法は、この手順書では説明しません。分からないときは、次の資料を参照してください。

| 知りたいこと | 資料 |
|---|---|
| Git の基本（commit、ブランチ、push など） | [Pro Git（日本語版）](https://git-scm.com/book/ja/v2) |
| Git の入門を一通り学ぶ | [Git でのバージョン コントロールの概要](https://learn.microsoft.com/ja-jp/training/paths/intro-to-vc-git/)（Microsoft Learn） |
| GitHub での開発の流れ | [GitHub フロー](https://docs.github.com/ja/get-started/using-github/github-flow) |
| Fork のしかた、upstream の設定 | [リポジトリをフォークする](https://docs.github.com/ja/get-started/quickstart/fork-a-repo) |
| Fork から PR を作る | [フォークからのプルリクエストの作成](https://docs.github.com/ja/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/creating-a-pull-request-from-a-fork) |
| conflict の解消（コマンドライン） | [マージ競合の解決](https://docs.github.com/ja/pull-requests/collaborating-with-pull-requests/addressing-merge-conflicts/resolving-a-merge-conflict-using-the-command-line) |
| commit メッセージの書き方 | [Conventional Commits](https://www.conventionalcommits.org/ja/v1.0.0/) |
| 手を動かして学ぶ | [GitHub Skills](https://skills.github.com/) |

AI に質問するのも有効です。そのときは、`git status` の結果と、実行したコマンドを伝えてください。
