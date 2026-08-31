# Global Codex hooks

このディレクトリは、このPCのCodexセッション全体に適用するhookを管理します。

## 構成

- `~/.codex/hooks.json`: lifecycleイベントとdispatcherの対応（`CODEX_HOME` を設定している場合はその配下）
- `codex_hooks.py`: Windows / Python 3 向けの判定・検証・記録処理
- `logs\events.jsonl`: プロンプト本文・ツール出力本文を保存しない評価用メタデータ

## 適用される保護

- 危険な削除、Git履歴破壊、強制push、公開操作、ディスク・レジストリ操作の自動拒否
- 認証情報を含むパスへの書き込みと資格情報らしい追加行の拒否
- プロンプト内の資格情報パターンの拒否
- MCPや読み取り系ツール出力に含まれるプロンプトインジェクションらしい文面の警告
- サブエージェントの完了報告と、サブエージェントの `max` / `ultra` 指定の拒否
- 編集後のPython構文・ruff・Git whitespace検査
- 最終応答時の編集・検証状態確認

## 初回の信頼登録

Codexの `/hooks` から、この定義を確認してtrust登録してください。定義を変更すると再レビューが必要になります。

## 設計上の制約

- hookは安全ガードであり、完全なセキュリティ境界ではありません。
- `PostToolUse` は実行済みの副作用を取り消しません。
- `Stop` と `SubagentStop` の追加パスは各セッション一回に限定します。
- `PermissionRequest` は承認モードで危険なBash操作が要求された場合にも拒否します。
