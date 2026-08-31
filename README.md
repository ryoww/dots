# dots

Codex と Claude Code の共有設定。

## Codex

`dot_codex/` には、現在PCの `~/.codex` からポータブルなポリシー、ルール、個人スキル、hook本体を反映しています。

認証情報、履歴・DB・ログ、プロジェクト信頼、MCP・UIの絶対パス、PC固有の権限設定は共有対象から除外しています。

`dot_codex/config.toml.example` と `dot_codex/hooks.json.example` は、他PCで設定を再現するときのテンプレートです。既存の `config.toml` / `hooks.json` は各PCの設定を確認してから統合してください。

`dot_claude/` のスキル・ルール参照は `dot_codex/` を共有元にします。
