# {{PRODUCT_NAME}}

このファイルは、このリポジトリで AI(Claude Code・Codex)が従う指示をまとめたものである。Claude Code は `CLAUDE.md` からこのファイルを読み込む。

{{PRODUCT_DESCRIPTION}}
{{STACK}}

## 最初に読むもの

- `docs/README.md` の「読む順番」のとおりに読む。
- 作業する機能の `docs/specs/` と、関係する `docs/decisions/` を必ず読む。

## 開発の進め方

正は `docs/workflow.md` である。要点だけを示す。

- 1 Issue = 1 ブランチ = 1 PR とする。main へ直接コミットしない。
- ブランチ名は `feat/<Issue番号>-<英語スラッグ>` とする。バグは `fix/`、ドキュメントは `docs/`、それ以外は `chore/` を使う。
- マージは squash merge とする。PR タイトルがそのまま main のコミットメッセージになるため、`feat: 〇〇を追加` の形式(`feat:` `fix:` `docs:` `chore:` + 日本語)で書く。PR 本文に `Closes #<番号>` を書く。
- PR の中では docs を先に書き、そのあとでコードを書く(仕様、未解決の項目、決定、実装の順)。振る舞いを変える PR は、同じ PR で仕様も書き換える。仕様だけ・コードだけの PR はマージしない(docs だけの修正 PR を除く)。
- テストは仕様の受け入れ条件と対応させる。
- 決められないことは推測で決めず、`docs/open-questions.md` に OQ を起票して尋ねる。`docs/vision.md`「やらないこと」に当たる機能は作らない。
- Issue・PR・コミットメッセージはすべて日本語で書く。

## コードを書くとき

- テスト・lint・typecheck を通してから PR を作る。
- 計算・判定ロジックは純粋関数に切り出し、`{{LOGIC_TEST_TOOL}}` でテストする。
- 画面が変わる変更は `{{E2E_TOOL}}` のフローを流し、PR に結果を書く。

<!-- プロジェクトの決定(D-XXXX)が増えたら、構成・依存ルールの要点をここに番号つきで足す -->

## 品質チェック

- テスト・lint・typecheck が落ちたまま PR を作らない。
- {{PROJECT_SPECIFIC_CHECK}}
  <!-- 自動テストで守れないレビュー観点があれば書く。無ければこの行を消す -->
