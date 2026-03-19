# 調査レポート: 既存のスキル共有・管理ツールの全体像

> 調査日: 2026-03-19
> 目的: 社内スキル共有 Monorepo + ブラウズ CLI（skills-browse）と同等の機能を持つ既存ツール・サービスの有無を調査する

---

## 調査の背景

自チームで設計中の仕組み:

1. **Git Monorepo** — 個人ディレクトリ（`users/<name>/`）に各自のスキルを配置し、自動マージで摩擦なく更新
2. **skills-browse CLI** — Monorepo 内のスキルをブラウズ・検索・フォークするシェルスクリプト
3. **Starter Kit** — 未整備メンバー向けの推奨スキル集

これらと重複する、または代替となるツール・サービスが存在するか調査した。

---

## カテゴリ別の既存ツール

### A. SaaS 型プライベートレジストリ

#### SkillReg (skillreg.dev)

- **概要**: SKILL.md のプライベートレジストリ。CLI で push/pull/search
- **対応エージェント**: Claude Code, Codex, Cursor, Copilot, Windsurf 等
- **機能**: OAuth/SSO 認証、RBAC、セキュリティスキャン、セマンティックバージョニング、API トークン管理
- **価格**: Free（5 skills, 1 user）/ Team $29/mo（10 users）/ Enterprise $99/mo（無制限、セルフホスト可）
- **URL**: https://skillreg.dev/
- **自チーム方式との比較**:
  - (+) 検索・バージョニング・セキュリティスキャンが組み込み
  - (-) 外部 SaaS への依存。社内情報がレジストリに送信される
  - (-) 月額コスト。Free tier は実用に耐えない（5 skills）
  - (-) 「個人ディレクトリ＋自動マージ」のような個人の自律性を担保する仕組みはない

#### localskills.sh

- **概要**: チーム向けスキル共有プラットフォーム（公開ベータ）
- **対応エージェント**: Cursor, Claude Code, Windsurf, Cline, Copilot, Codex, OpenCode, Aider（8 ツール）
- **機能**: SSO（SAML 2.0）、SCIM ディレクトリ同期、チームロール、ダウンロード分析、監査ログ
- **CLI**: `npm i -g @localskills/cli`
- **URL**: https://localskills.sh/
- **自チーム方式との比較**:
  - (+) 対応ツール数が多い。SSO/SCIM によるエンタープライズ統合
  - (-) ベータ段階で安定性が未知数
  - (-) SaaS 依存。データの外部保管
  - (-) 個人の自律的な更新（自動マージ）の概念はなし

#### SkillSafe (skillsafe.ai)

- **概要**: セキュリティ重視の検証済みスキルレジストリ
- **機能**: インストール前のセキュリティスキャン、ダウンロード時の再検証
- **規模**: 15+ AI ツール対応、5,100+ スキル登録、100+ 脅威検出実績
- **URL**: https://skillsafe.ai/
- **自チーム方式との比較**:
  - (+) セキュリティスキャンは自チーム方式にない機能
  - (-) 公開レジストリ寄り。社内プライベート用途の適合性は不明

---

### B. オープンソース / セルフホスト型

#### SkillHub (iflytek/skillhub)

- **概要**: エンタープライズ向けセルフホスト型スキルレジストリ（Apache 2.0）
- **言語**: Java (70.2%)
- **GitHub**: https://github.com/iflytek/skillhub （★293、v0.1.0-beta.17）
- **機能**:
  - チーム Namespace + RBAC
  - 全文検索（フィルタ: Namespace、ダウンロード数、評価、新着順）
  - レビューワークフロー（チーム管理者がNamespace内を管理、プラットフォーム管理者がグローバル昇格を承認）
  - 監査ログ
  - セマンティックバージョニング + カスタムタグ（beta, stable）
  - プラガブルストレージ（ローカル / S3 / MinIO）
  - Star・評価・ダウンロード数のソーシャル機能
- **デプロイ**: `make dev-all` でローカル起動、K8s/Docker で本番運用
- **自チーム方式との比較**:
  - (+) 検索・バージョニング・レビューワークフロー・ソーシャル機能が充実
  - (+) セルフホストなのでデータは社内に閉じる
  - (-) Java + サーバーインフラが必要。導入・運用コストが高い
  - (-) まだ beta（v0.1.0-beta.17）
  - (-) Git Monorepo の手軽さ（GitHub Web UI でそのまま閲覧）は得られない

#### agent-skills-registry (kai98k)

- **概要**: Docker でデプロイ可能な集中レジストリ
- **CLI**: `agentskills push/pull` で操作
- **GitHub**: https://github.com/kai98k/agent-skills-registry
- **自チーム方式との比較**:
  - (+) 標準化された Skill Bundle フォーマット
  - (-) 情報が少なく、成熟度が不明

---

### C. 公式エコシステム

#### Claude Code Plugin Marketplace（Anthropic 公式）

- **概要**: Git リポジトリに `marketplace.json` を置いてプラグイン（スキル含む）をカタログ化・配布
- **操作**: `/plugin marketplace add <url>` → `/plugin install <name>@<marketplace>`
- **仕組み**:
  - Git リポジトリ（GitHub/GitLab 等）でホスト
  - `marketplace.json` にプラグイン一覧を定義
  - 各プラグインは `.claude-plugin/plugin.json` + `skills/` ディレクトリで構成
  - 名前空間で衝突回避（`/plugin-name:skill-name`）
  - `plugin marketplace update` で最新に追従
- **ドキュメント**: https://docs.anthropic.com/en/docs/claude-code/plugin-marketplaces
- **自チーム方式との比較**:
  - (+) Claude Code の公式配布手段。エコシステムとの親和性が最も高い
  - (+) Git ホスト型なので SaaS 依存なし
  - (-) **Claude Code 専用**（Cursor 等は対象外）
  - (-) 粒度が「プラグイン」であり、個々のスキル単位のブラウズには向かない
  - (-) 個人ディレクトリ＋自動マージのようなソーシャル設計はない
  - (-) ブラウズ・検索の体験は自前で用意する必要あり

#### Skills CLI (skills.sh / vercel-labs)

- **概要**: 公開スキルレジストリ + CLI（`npx skills find/add`）
- **社内利用の制約**: 別途 [Skills CLI 調査レポート](./skills-cli-investigation.md) 参照
- **要約**: 社内スキルのブラウズには使えない。インストーラとしての活用のみ可能

---

### D. チームでの実例

#### inkeep/team-skills

- **概要**: Inkeep 社がチームスキルを共有する GitHub リポジトリ
- **構成**: `plugins/eng/`, `plugins/gtm/`, `plugins/shared/` のようなチーム別ディレクトリ
- **インストール**: `npx skills add inkeep/team-skills/plugins/shared -y`
- **GitHub**: https://github.com/inkeep/team-skills （★2）
- **自チーム方式との比較**:
  - アプローチが最も近い（Git リポジトリでスキルを共有）
  - ただし自動マージ・ブラウズ CLI・個人ディレクトリの分離・Starter Kit はなし
  - チーム別（eng/gtm）の分割であり、個人別の分割ではない

#### ヘンリー社の事例

- 別途 [ヘンリー社参考レポート](./henry-skill-management.md) 参照
- 個人スキル + チームスキルの二層構造 + Starter Kit（任意利用）
- 自チーム方式の設計思想に最も近い先行事例

---

## 比較マトリクス

| 観点 | 自チーム方式 | SkillReg | localskills.sh | SkillHub (OSS) | Plugin Marketplace | inkeep/team-skills |
|---|---|---|---|---|---|---|
| **インフラコスト** | ゼロ（Git のみ） | $29〜99/mo | ベータ（無料?） | サーバー必要 | ゼロ（Git のみ） | ゼロ（Git のみ） |
| **外部依存** | なし | SaaS | SaaS | セルフホスト | なし | なし |
| **対応エージェント** | 全て（手動コピー） | 6+ | 8+ | 不明 | Claude Code のみ | Skills CLI 経由 |
| **ブラウズ体験** | CLI + GitHub Web | CLI + Web UI | CLI + Web UI | Web UI | `/plugin` コマンド | GitHub Web のみ |
| **検索** | キーワード全文検索 | フィルタ付き検索 | あり | 全文検索 + フィルタ | なし | なし |
| **個人の自律性** | 高い（自動マージ） | 低い（publish が必要） | 低い | 中（Namespace） | 低い | なし |
| **導入障壁** | `git clone` のみ | アカウント作成 + CLI | アカウント作成 + CLI | サーバー構築 | Claude Code 必須 | `git clone` のみ |
| **バージョニング** | Git 履歴のみ | セマンティック | あり | セマンティック | plugin.json | Git 履歴のみ |
| **セキュリティスキャン** | なし | あり | なし | なし | なし | なし |
| **Starter Kit** | あり | なし | なし | なし | なし | 部分的（shared/） |

---

## 評価と推奨

### 自チーム方式の強み（既存ツールにない価値）

1. **外部依存ゼロ**: SaaS もサーバーインフラも不要。Git + bash のみで完結
2. **個人ディレクトリ + 自動マージ**: 各自が自分の領域を自由に更新できる仕組みは、調査した既存ツールのいずれにもない設計
3. **GitHub Web UI でそのまま閲覧**: レジストリ型は別途 Web UI が必要だが、Monorepo は GitHub 上でそのままブラウズ可能
4. **導入障壁の低さ**: `git clone` するだけで参加可能。アカウント作成や CLI インストールが不要
5. **Starter Kit の概念**: 新規参加者向けの推奨スキル集という設計は独自性が高い

### 自チーム方式の弱み（既存ツールが優位な点）

1. **セマンティックバージョニング**: Git 履歴のみでは、特定バージョンのスキルを pin する体験が得られない
2. **セキュリティスキャン**: 悪意のあるスキルや秘密情報の混入を自動検出する仕組みがない
3. **分析・メトリクス**: 利用状況（誰がどのスキルを使っているか）を把握する手段がない
4. **マルチエージェント自動配布**: 1 コマンドで Claude/Cursor/Codex 全てに配置する仕組みは自前では持たない

### 注視すべき動向

1. **Claude Code Plugin Marketplace の進化**: Anthropic がプライベートマーケットプレイスや検索機能を充実させると、`skills-browse` の一部機能が不要になる可能性。ただし Cursor 等のマルチエージェント対応や個人ディレクトリの自律性は残る
2. **localskills.sh の正式リリース**: 公開ベータが安定し、価格が明確になれば、SaaS 許容チームにとって有力な選択肢になりうる
3. **SkillHub の成熟**: Apache 2.0 ライセンスのセルフホスト型が安定すれば、サーバー運用の余力があるチームには検討価値がある
4. **Skills CLI のプライベート対応**: Vercel Labs が private registry 機能を追加すれば、移行先になりうる

### 結論

**現時点では、自チーム方式を進めるのが妥当。** 「軽量・外部依存ゼロ・個人の自律性重視」というポジションは、既存ツールのいずれとも差別化されている。特に少〜中規模チームでの初期導入には最適な選択肢。

ただし、Claude Code Plugin Marketplace と SkillHub (OSS) の動向は定期的に確認し、将来的な移行・統合の選択肢として意識しておくべき。
