# {{PRODUCT_NAME}}

このリポジトリの決まりは `AGENTS.md` に書く。Claude Code と Codex で同じ決まりを使うためで、Claude Code だけに当てはまることはこのファイルに書く。

@AGENTS.md

## スラッシュコマンド(Claude Code)

| コマンド | 用途 |
|---|---|
| `/create-issue <一文>` | 大まかな要求から質問で詳細を掘り下げ、Issue を起票する |
| `/implement-issue <番号>` | Issue を読み、ブランチ作成から実装・テスト・PR 作成まで実行する |
| `/merge-pr <番号>` | PR を squash merge し、リモート・ローカルのブランチを削除する |
| `/record-decision <テーマ>` | 議論が済んだ決定を `docs/decisions/` に D-XXXX として記録し、関係する OQ を解決済みにする |
| `/check-docs` | `docs/` の整合性(リンク・重複・目次・未解決の項目)を検査する |
