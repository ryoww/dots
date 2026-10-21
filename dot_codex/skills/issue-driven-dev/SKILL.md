---
name: issue-driven-dev
description: 新しいリポジトリ(または Issue 駆動を後付けしたい既存リポジトリ)に、AI 駆動の Issue 駆動開発の基盤を敷く。AGENTS.md(Claude Code と Codex 兼用)・CLAUDE.md・Issue/PR テンプレート・5 つのスキル(create-issue / implement-issue / merge-pr / record-decision / check-docs)・読み取り専用 permissions・docs の骨組み(vision / workflow / specs / decisions / open-questions)を、質問でプロジェクト固有の項目を埋めてから配置する。docs の書き方(一次情報は 1 か所・仕様の状態と受け入れ条件・追記のみの決定記録・OQ で尋ねる)も一緒に敷く。「Issue 駆動で開発したい」「AI に仕様とコードをそろえさせたい」「docs の構成を整えたい」「chalo や kyouen-multi と同じ運用にしたい」「新しいリポジトリの開発ルールを作りたい」で使う。リポジトリの立ち上げ直後なら、頼まれなくてもこのスキルを提案する。
argument-hint: <owner/repo>(省略可)
---

対象リポジトリ: $ARGUMENTS(省略時はカレントの `git remote` から推定して確認する)

iOS アプリ chalo と Web アプリ kyouen-multi で実運用した Issue 駆動開発の基盤を統合した雛形を敷く。
どちらも「Issue から始める」「docs を先に書く」「決められないことは推測せず書いて尋ねる」を AI に守らせる仕組みで、
基盤は最初の 1 コミットで敷かれ、以後は運用で困ったところだけ足されていった。
このスキルはその「最初の 1 コミット」に当たる部分を敷く。
リリースや全体リグレッションのスキル、CI は含めない(必要になってから、そのプロジェクトの決定に合わせて作る)。

雛形は `references/templates/`、docs の書き方は `references/docs-writing-guide.md`。
**docs を配置する前に必ず `docs-writing-guide.md` を読む。** 配置後にユーザーへ構造を説明するときも、この文書の言葉で説明する。

## 敷くものと、なぜそれか

| 配置物 | 役割 | 由来 |
|---|---|---|
| `AGENTS.md` | AI への指示の本体。Claude Code と Codex で同じ決まりを使う | kyouen-multi(D-0013) |
| `CLAUDE.md` | `@AGENTS.md` で読み込み、Claude Code 専用のスラッシュコマンド表だけ持つ | 同上 |
| `.github/ISSUE_TEMPLATE/` | feature / bug / design の 3 種。関係するドキュメントと受け入れ条件を必須にする | chalo(3 種)+ kyouen-multi(目的・OQ・決定の欄) |
| `.github/pull_request_template.md` | 「ドキュメント」欄(仕様・OQ・決定)とテスト結果表、確認事項 | 両方 |
| `.claude/skills/` 5 本 | Issue 起票 → 実装 → マージ、決定の記録、docs の検査 | chalo の 5 スキルを新構造に合わせた |
| `.claude/settings.json` | git と GitHub の読み取り系だけを許可 | chalo |
| `docs/` 骨組み | vision / workflow / specs / decisions / open-questions | 構造は kyouen-multi、「一次情報は 1 か所」「印を付けない」は chalo |

## プレースホルダ

雛形の `{{…}}` を質問で埋める。

| プレースホルダ | 内容 | 例 |
|---|---|---|
| `{{REPO}}` | GitHub の `owner/repo` | `adelppi/chalo` |
| `{{PRODUCT_NAME}}` | プロダクト名 | `chalo` |
| `{{PRODUCT_DESCRIPTION}}` | プロダクトの一文説明 | `カップルが「いつか一緒に行きたい所」を貯めて共有する iOS アプリ。` |
| `{{STACK}}` | 技術スタックと前提の一文 | `Next.js + Liveblocks。日本語のみ。` |
| `{{LOGIC_TEST_TOOL}}` | 純粋関数のテストに使うツール | `Jest` / `vitest` / `pytest` |
| `{{E2E_TOOL}}` | E2E に使うツール。無ければ「なし」 | `Maestro` / `Playwright` |
| `{{PROJECT_SPECIFIC_CHECK}}` | 自動テストで守れないレビュー観点。無ければ行ごと削除 | `Supabase RLS・ペア境界に影響がない` |

## 手順

### 1. 前提を確認する

1. git リポジトリであること。main(または既定ブランチ)が存在すること。
2. `gh auth status` か GitHub MCP のどちらかで GitHub を操作できること。どちらも無ければ、配置はできるが Issue 起票・PR 作成のスキルは動かない旨を伝える。
3. 配置先に既存ファイルがあるか調べる: `AGENTS.md`、`CLAUDE.md`、`.github/ISSUE_TEMPLATE/`、`.github/pull_request_template.md`、`.claude/settings.json`、`.claude/skills/`、`docs/`。あるものは一覧にして、手順 3 で扱いを決める。

### 2. 質問で埋める

AskUserQuestion(選択肢で聞けるもの)か通常の質問で、**2〜3 個ずつ**聞く。`git remote`・パッケージ定義ファイル・README から分かることは聞かず、推定した値を見せて確認だけ取る。ユーザーが最初の依頼で値を与えていれば、その項目は聞かない。

1. `{{REPO}}`・`{{PRODUCT_NAME}}`・`{{PRODUCT_DESCRIPTION}}`
2. `{{STACK}}`・`{{LOGIC_TEST_TOOL}}`・`{{E2E_TOOL}}`
3. `{{PROJECT_SPECIFIC_CHECK}}`(無ければ「無し」)・既存の `docs/` をどう扱うか(手順 3-3)・配置後にコミットするか(手順 5)

### 3. 配置する

`references/templates/` の各ファイルを同じ相対パスで対象リポジトリにコピーし、プレースホルダを置換する。

1. **既存ファイルは黙って上書きしない。** 既存があるものは、雛形との差分を見せて「置き換える / 雛形の要素を既存に足す / 触らない」をユーザーに選んでもらう。ユーザーに聞けない状況(自動実行など)では「触らない」を選び、雛形の内容を `<ファイル名>.proposed.md` として隣に置いて、報告で差し替えを提案する。
   - `CLAUDE.md` が既にある場合は特に注意する。`AGENTS.md` を置いても `CLAUDE.md` に `@AGENTS.md` が無ければ Claude Code は `AGENTS.md` を読まない。既存の決まりの本文を `AGENTS.md` へ移し、`CLAUDE.md` を `@AGENTS.md` とスラッシュコマンド表だけにする案を、既存の行を 1 つも落とさない形で示す。
2. **E2E が「なし」のとき**は、次を削る。
   - `.github/pull_request_template.md`: `E2E(…)` のチェック行と「フロー / 結果」「手動チェック / 結果」の表とそのコメント
   - `AGENTS.md`: 「コードを書くとき」の E2E の行
   - `.claude/skills/implement-issue/SKILL.md`: 検証の E2E と全体リグレッションの 2 項目
   - `.github/ISSUE_TEMPLATE/feature.yml`・`bug.yml`: `regression` のチェックボックス
   - `.claude/skills/create-issue/SKILL.md`: 「全体リグレッション」の行
3. **`docs/` の扱い。**
   - `docs/` が無い: `references/templates/docs/` を丸ごと配置する。骨組みだけで、プロダクトの中身(vision の本文・仕様・決定)は書かない。
   - `docs/` がある: 雛形のうち**既存とファイル名がぶつからないものはすべて置く**(`README.md`・`workflow.md`・`open-questions.md`・`vision.md`・`specs/README.md`・`specs/_template.md`・`decisions/_template.md`)。置かないと `AGENTS.md` が正として参照する `workflow.md` や、`README.md` が挙げる `specs/` `decisions/` がリンク切れになり、`/check-docs` が最初から失敗する。ぶつかるファイル(たとえば既存の `README.md`)は手順 3-1 の差分提示に回す。既存の文書(ADR・機能一覧・ドメイン文書など)は**消さず書き換えず**、`README.md` の末尾に「既存文書との関係(移行中)」の表を足して、`docs-writing-guide.md` の層に照らした対応(ADR → `decisions/`、機能一覧の「持たない」→ `vision.md`「やらないこと」、「未実装」→ `specs/README.md` の予定、ドメイン文書 → `specs/<機能>.md`、`[保留]` の印 → `OQ-NNN`)を書く。移行そのものはユーザーの指示があってから別の Issue で行う。
4. `AGENTS.md` の「コードを書くとき」末尾のコメントは残す。構成やテスト戦略の決定(`D-XXXX`)が書かれてから埋める。
5. `{{PROJECT_SPECIFIC_CHECK}}` が「無し」なら、`AGENTS.md` と PR テンプレートの該当行をコメントごと削除する。

### 4. GitHub 側の設定を確認する

雛形が前提にしている設定。変更はユーザーの明示的な指示があるときだけ `gh` で行い、無ければ「やるべき設定」として報告する。

- ラベル `feature` / `bug` / `design` が存在する(Issue テンプレートが付ける)。`gh label list` で確認し、無ければ作成を提案する。
- マージ方法を squash のみにする(`Settings → General → Pull Requests`)。
- マージ後にブランチを自動削除する(merge-pr が前提にしている。無くても動く)。

### 5. 検証と報告

1. 配置したファイルに `{{` が残っていないことを `grep -rn "{{" <配置先>` で確かめる。E2E「なし」なら `E2E` の語も残っていないことを確かめる。
2. 配置したファイルの一覧を示す。
3. コミットするかは手順 2 で聞いた答えに従う。コミットするなら `chore: Issue 駆動開発の基盤を敷く` の 1 コミットにし、push はしない。
4. **次の一歩**を伝える。順番に意味がある。
   1. `docs/vision.md` を人が書く。「やらないこと」と「判断の基準」まで埋める。
   2. `/record-decision` で最初の決定(テスト戦略、ディレクトリ構成)を `D-0001` `D-0002` として書き、`docs/README.md` の表と `AGENTS.md` の「コードを書くとき」に番号を入れる。
   3. 最初の機能を `/create-issue` で起票し、`/implement-issue` で仕様 → 実装の順に進める。
   4. 運用で困ったら、そのときスキルを足す(chalo では release・regression-test、kyouen-multi では CI デプロイが後から加わった)。

## してはいけないこと

- `vision.md` の本文、`specs/` の仕様、`decisions/` の記録を**生成する**。骨組みと書き方までがこのスキルの範囲で、中身と判断は人が書く。
- 既存ファイルを確認なしに上書きする。
- push する。GitHub のリポジトリ設定(マージ方法・ブランチ保護)を指示なしに変える。
- プレースホルダを推測で埋めて進める。分からなければ聞く。
- `docs-writing-guide.md` を読まずに docs を配置する。
