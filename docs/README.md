# docs/

意思決定の記録、設計の詳細、調査資料を管理するディレクトリ。

## ディレクトリ構成

| ディレクトリ | 役割 | 読者 |
|---|---|---|
| `adr/` | 意思決定の記録（なぜそう決めたか） | 方針の経緯を知りたい人 |
| `design/` | 設計の詳細（どう作るか） | 実装・運用する人 |
| `references/` | 調査レポート・参考資料 | 判断材料を確認したい人 |

## ADR 一覧

| # | タイトル | ステータス |
|---|---|---|
| [001](adr/001-personal-skill-repo-structure.md) | 個人スキルリポジトリの構成方式 | 採用 |
| [002](adr/002-monorepo-design.md) | 個人スキル共有 Monorepo の設計方針 | 採用 |
| [003](adr/003-skills-cli-integration.md) | Skills CLI の社内利用方針 | 採用 |
| [004](adr/004-flat-structure-and-ownership.md) | フラット構造の採用とスキル所有権モデル | 採用 |

## 設計書

| タイトル | 前提 ADR |
|---|---|
| [Monorepo 設計](design/monorepo-design.md) | ADR-001, 002, 004 |

## 参考資料

| タイトル | 概要 |
|---|---|
| [既存ツール全体像](references/existing-tools-landscape.md) | スキル共有・管理ツールの調査 |
| [ヘンリー社の事例](references/henry-skill-management.md) | チームスキル展開の課題と対策 |
| [Skills CLI 調査](references/skills-cli-investigation.md) | npx skills の機能・制約・セキュリティ |
| [Slack 会話ログ](references/raw-conversation.md) | 本リポジトリの発端となった議論 |
