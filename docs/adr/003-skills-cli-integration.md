# ADR-003: Skills CLI の社内利用方針

| 項目 | 値 |
|---|---|
| ステータス | 採用 |
| 作成日 | 2026-03-19 |
| 関連 ADR | [ADR-002](./002-monorepo-design.md) |

---

## コンテキスト

ADR-002 で設計した社内 Monorepo のスキルを、Skills CLI (`npx skills`) のエコシステムと統合できるか検討した。
調査の詳細は [Skills CLI 調査レポート](../references/skills-cli-investigation.md) および [既存ツール全体像](../references/existing-tools-landscape.md) を参照。

調査の結果、Skills CLI には社内利用において以下の制約があることが判明した:

- `npx skills find` は公開インデックスのみ検索（社内スキルのブラウズに使えない）
- テレメトリがリポジトリ名・スキル名を外部送信する
- `gh auth token` を無断で取得する既知の Issue (#523) がある

## 検討した選択肢

### 選択肢 A: Skills CLI をブラウズ・インストールの両方に使う

`npx skills find` で社内スキルを検索し、`npx skills add` でインストールする。

### 選択肢 B: Skills CLI はインストーラとしてのみ使い、ブラウズは自前で用意する

`npx skills add` のローカルパス経由インストールのみ活用し、社内スキルのブラウズは自前 CLI（`skills-browse`）で対応する。

### 選択肢 C: Skills CLI を使わず、すべて自前で対応する

手動コピーやシンボリックリンクのみで運用する。

### 比較

| 観点 | A: フル活用 | B: インストーラのみ | C: 不使用 |
|---|---|---|---|
| 社内ブラウズ | 不可（公開インデックスのみ） | 自前 CLI で対応 | 自前 CLI で対応 |
| マルチエージェント配布 | CLI 一発 | CLI 一発 | 手動 |
| テレメトリリスク | 高い | 制御可能（環境変数で無効化） | なし |
| SKILL.md 互換性 | 活用できる | 活用できる | 関係なし |
| 将来の移行しやすさ | — | Skills CLI が private 対応すれば移行容易 | 互換性なし |

## 決定

選択肢 B を採用する。Skills CLI を社内スキルのブラウズには使わず、インストーラとしてのみ活用する。

`npx skills find` が社内スキルに対応しない以上、ブラウズ体験は自前で用意するしかない。一方で `npx skills add` のローカルパス経由インストールは、テレメトリリスクを制御しつつマルチエージェント配布の利便性を得られる。

Monorepo の SKILL.md 形式が Skills CLI と互換であることが強みで、将来 Skills CLI がプライベート対応した際にもスムーズに移行できる。

## 影響・トレードオフ

- 全メンバーの shell rc に `DISABLE_TELEMETRY=1` の設定を推奨する運用が必要
- 自前 CLI（`skills-browse`）のメンテナンスコストが発生するが、シェルスクリプトベースで最小限に抑える
- Skills CLI のフォークや大掛かりな拡張は不要と判断。必要十分な機能を最小コストで実現する方針

### 未解決事項

- [ ] Skills CLI の private registry 対応の動向監視
- [ ] Claude Code Plugin Marketplace の進化による影響評価
