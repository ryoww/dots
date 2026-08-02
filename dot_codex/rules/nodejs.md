# Node.js ルール

- このリポジトリで Node.js 関連の作業を行う場合、Windows ではランタイム管理に必ず `volta` を使い、Linux（WSL を含む）では必ず `nvm` を使う。
- プロジェクトで使用する Node.js のバージョンは、Windows では `volta pin node@<version>` で固定し、Linux では `.nvmrc` に記録して `nvm install` と `nvm use` で適用する。
- このリポジトリのパッケージマネージャとしては `pnpm` を優先する。
- Node.js のワークフローでは、`pnpm install`、`pnpm add`、`pnpm remove`、`pnpm run <script>`、`pnpm exec <tool>` を使う。
- 文書化された例外がない限り、このリポジトリでは `npm`、`yarn`、`fnm`、`asdf` を使わない。また、Windows では `nvm`、Linux では `volta` を使わない。
- Windows で `pnpm` を Volta 経由で管理する場合、Volta の `pnpm` サポートには回避策が必要になることがある。その場合は、回避策を使う前に理由を説明すること。

