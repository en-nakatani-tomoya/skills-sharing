# 調査レポート: Skills CLI (npx skills / skills.sh)

> 調査日: 2026-03-19
> ソース: skills.sh 公式ドキュメント、GitHub vercel-labs/skills、関連記事・Issue

---

## 概要

Skills CLI は Vercel Labs が開発するオープンソースのエージェントスキル管理ツール。
GitHub リポジトリからスキルをインストールし、AI エージェント（Claude Code, Cursor, Codex 等）で利用可能にする。

### 3 つの構成要素

| 要素 | 説明 |
|---|---|
| **skills.sh** | スキルの公開カタログ（Web UI）。リーダーボードで人気スキルを表示 |
| **Skills CLI** | `npx skills` で実行するコマンドラインツール（vercel-labs/skills） |
| **ローカルスキルフォルダ** | `.agents/skills/` 等、エージェントごとの配置先 |

---

## 主要コマンド

| コマンド | 用途 |
|---|---|
| `npx skills find <keywords>` | キーワードでスキルを検索 |
| `npx skills add <source>` | スキルをインストール |
| `npx skills add <source> --skill <name>` | リポジトリ内の特定スキルのみインストール |
| `npx skills list` | インストール済みスキルの一覧 |
| `npx skills remove <name>` | スキルの削除 |
| `npx skills check` | 更新の確認 |
| `npx skills update` | 全スキルの更新 |

### インストールオプション

| オプション | 説明 |
|---|---|
| `-g, --global` | ユーザーレベル（プロジェクト外）にインストール |
| `-a, --agent <name>` | 対象エージェントを指定（`claude-code`, `cursor` 等） |
| `-s, --skill <name>` | 特定スキルのみインストール（`'*'` で全件） |
| `-l, --list` | インストールせずに利用可能なスキルを一覧表示 |
| `--copy` | シンボリックリンクの代わりにファイルコピー |
| `-y, --yes` | 確認プロンプトをスキップ |

### 対応ソースフォーマット

```bash
npx skills add vercel-labs/agent-skills                          # GitHub shorthand
npx skills add https://github.com/vercel-labs/agent-skills       # Full GitHub URL
npx skills add https://github.com/.../tree/main/skills/foo       # リポジトリ内パス直指定
npx skills add https://gitlab.com/org/repo                       # GitLab URL
npx skills add git@github.com:vercel-labs/agent-skills.git       # 任意の git URL
npx skills add ./my-local-skills                                 # ローカルパス
```

### インストールの仕組み

1. 指定されたソースから git clone（またはローカル参照）
2. リポジトリ内の SKILL.md を検出
3. 対話的に対象エージェント・スキルを選択
4. シンボリックリンク（推奨）またはコピーでエージェントのスキルフォルダに配置

---

## テレメトリ

### 送信されるデータ

`npx skills add` 実行時に `https://add-skill.vercel.sh/t` へ以下を送信:

| データ | 内容 |
|---|---|
| Event type | `install` 等のイベント種別 |
| **Skill name(s)** | インストールするスキル名 |
| **Repository source** | ソースリポジトリ名/URL |
| Source type | GitHub, raw URL, provider ID 等 |
| CLI version | CLI のバージョン |
| CI environment flag | CI 環境かどうか |
| Skill files | スキル名→パスのマッピング（JSON） |

### 無効化方法

```bash
DISABLE_TELEMETRY=1 npx skills add ...
# または
DO_NOT_TRACK=1 npx skills add ...
```

---

## セキュリティ上の懸念

### 懸念 1: テレメトリによる情報漏洩

テレメトリデータに**リポジトリ名/URL とスキル名**が含まれる。
社内の private リポジトリから `npx skills add` を実行した場合、リポジトリ名が Vercel のサーバーに送信される。

- 社内リポジトリ名からプロジェクト構成や技術スタックが推測される可能性
- `DISABLE_TELEMETRY=1` で回避可能だが、全メンバーが常に設定する保証がない

### 懸念 2: GitHub 認証トークンの無断取得

Issue #523（2026-03-06 報告、未解決）:

- `npx skills add` 実行時に `gh auth token` を `execSync` で呼び出し、GitHub 認証トークンを取得する
- **public リポジトリへのアクセス時でも**トークン取得が行われる
- ユーザーの同意や明示的なオプトインなしに行われる
- `GITHUB_TOKEN` / `GH_TOKEN` 環境変数を先に参照し、なければ `gh auth token` にフォールバック

この挙動は `src/skill-lock.ts` の `getGitHubToken()` に実装されており、スキルの更新追跡（folder hash）目的で使われている。

### 懸念 3: サプライチェーンリスク

- `npx skills` は毎回最新バージョンを取得して実行する設計
- セキュリティ監査は Vercel が定期実施しているとされるが、全スキルの品質・安全性は保証されない
- 公式ドキュメントでも「インストール前に自分で確認することを推奨」としている

---

## `npx skills find` の動作

`find` コマンドは **skills.sh の公開インデックス**を検索する。

- 検索対象は skills.sh に登録された公開スキルのみ
- **社内 private リポジトリのスキルは検索対象にならない**
- つまり `npx skills find` を社内スキルのブラウズに直接使うことはできない

### リーダーボード

skills.sh のトップページにはインストール数に基づくリーダーボードがある:

| スキル | インストール数 |
|---|---|
| find-skills | 610.9K |
| vercel-react-best-practices | 224.4K |
| web-design-guidelines | 178.4K |

---

## 対応エージェント

Skills は以下のエージェントと互換:

- Claude Code
- Cursor
- OpenAI Codex
- Windsurf
- Continue
- その他カスタムランナー

16+ の AI ツールが Agent Skills をオープンスタンダードとしてサポート（2026 年 3 月時点）。

---

## 社内利用における評価まとめ

| 観点 | 評価 | 詳細 |
|---|---|---|
| 公開スキルの発見・導入 | 良好 | `find` + `add` で簡単に導入できる |
| **社内スキルのブラウズ** | **不可** | `find` は公開インデックスのみ検索。社内スキルは対象外 |
| 社内リポジトリからのインストール | 可能 | git URL やローカルパスでインストールできる |
| テレメトリの安全性 | **要注意** | リポジトリ名が送信される。環境変数で無効化可能だが徹底が必要 |
| 認証トークンの扱い | **要注意** | 無断で `gh auth token` を実行する既知の Issue あり |
| マルチエージェント対応 | 良好 | Claude Code, Cursor, Codex 等に横断的にインストール可能 |
