---
name: skills-sharing-guide
description: skills-sharing リポジトリの設計思想・構造・運用ルールを熟知したガイド。スキルの追加・ブラウズ・フォーク・セットアップなど、このリポジトリに関する操作や質問に対応する。
---

# Skills Sharing Guide

skills-sharing リポジトリの案内役スキル。

## When to Use This Skill

- skills-sharing リポジトリで作業しているとき
- スキルの追加・編集・削除の方法を知りたいとき
- 他の人のスキルを使いたい（フォークしたい）とき
- skills-browse ツールの使い方を聞かれたとき
- 新しいメンバーの参加手順を案内するとき
- このリポジトリの設計思想や運用ルールについて質問されたとき

## Repository Overview

このリポジトリは社内エンジニアのエージェントスキルを共有・閲覧するための場所。
まず README.md と CONTRIBUTING.md を読んで最新の情報を把握すること。

### 構造

```
skills-sharing/
├── users/                  各自のスキル（フラット配置）
│   ├── .template/          新規参加者用テンプレート
│   └── <username>/         個人ディレクトリ
│       ├── README.md       スキル一覧
│       └── <skill>/SKILL.md
├── starter-kit/            推奨スキル集（レビュー必須）
├── scripts/skills-browse   ブラウズ・フォーク CLI
├── CODEOWNERS
└── .github/workflows/      自動マージ
```

## Design Principles

以下の設計原則に基づいて行動すること。

### 1. スキルはエージェント非依存

スキルは Claude / Cursor / Codex 等のエージェント間で共通。リポジトリ内にエージェント別ディレクトリ（claude/, cursor/ 等）は作らない。エージェントへの配置はローカル環境の関心事。

### 2. スキルの所有権は個人に帰属する

- `users/<name>/` 配下のスキルはその人のもの
- 他人のディレクトリを変更する PR は出さない
- 他人のスキルを使いたい場合はフォーク（コピー）する

### 3. フォークモデル

他人のスキルを使う正しい手順：

1. `./scripts/skills-browse install <user>/<skill>` で自分のディレクトリにコピー
2. 必要に応じてカスタマイズ
3. 自分のスキルとして commit & push

シンボリックリンクで他人のスキルを参照してはいけない。理由：
- カスタマイズ時に他人のディレクトリに影響する
- 元の所有者が削除・変更するとリンク切れ
- 所有権が曖昧になる

### 4. 自動マージ

`users/<PR作者>/` 配下のみの変更は自動マージされる。それ以外の変更（starter-kit/, scripts/, CODEOWNERS 等）はレビューが必要。

### 5. ローカル環境はシンボリックリンク

`~/.claude/skills` や `~/.cursor/skills` を自分のユーザーディレクトリへのシンボリックリンクにする。スキルを追加すれば即座にエージェントから使える。

## Common Tasks

### 新規参加者のオンボーディング

以下の手順でユーザーを案内すること。

```bash
# 1. リポジトリを clone（まだの場合）
git clone git@github.com:<ORG>/skills-sharing.git ~/skills-sharing
cd ~/skills-sharing

# 2. join コマンドを実行（ディレクトリ作成 + CODEOWNERS 追加）
./scripts/skills-browse join

# 3. join が表示するコマンドに従って PR を作成
#    (git checkout -b, git add, git commit, git push, gh pr create)

# 4. PR がマージされたら、ローカル環境をセットアップ
./scripts/skills-browse setup

# 5. 環境が正しいか確認
./scripts/skills-browse doctor
```

join コマンドのデフォルト動作：
- `users/.template/` から `users/<name>/` を作成
- CODEOWNERS にエントリーを追加
- 続きの git / gh コマンドを表示（実行はしない）

`--auto` を付けると commit → push → PR 作成まで自動実行する（dirty worktree チェックあり）。

初回 PR は CODEOWNERS の変更を含むため、メンテナーの approve が必要。

### ローカル環境の診断

`doctor` コマンドで環境の状態を確認できる。問題があれば修正コマンドを提案してくれる。

```bash
./scripts/skills-browse doctor --me <name>
```

チェック項目：
- ユーザーディレクトリの存在
- CODEOWNERS エントリーの有無
- スキルの検出数
- `~/.claude/skills` / `~/.cursor/skills` のシンボリックリンク状態
- リモートとの同期状態

### skills-browse 全コマンドリファレンス

```bash
# --- オンボーディング ---
./scripts/skills-browse join                          # 参加（ファイル作成 + 手順案内）
./scripts/skills-browse join --auto                   # 参加（commit / push / PR 作成まで自動）
./scripts/skills-browse setup                         # ローカル環境セットアップ（シンボリックリンク作成）
./scripts/skills-browse doctor                        # ローカル環境の診断

# --- ブラウズ ---
./scripts/skills-browse list                          # 全スキル一覧
./scripts/skills-browse list --user <name>            # 特定ユーザーのスキル
./scripts/skills-browse search "<keyword>"            # キーワード検索
./scripts/skills-browse show <user>/<skill>           # スキル詳細表示
./scripts/skills-browse users                         # 参加者一覧

# --- フォーク ---
./scripts/skills-browse install <user>/<skill>        # 他人のスキルを自分のディレクトリにコピー
```

### 還元のフロー

改善が生まれた場合はコードレベルの統合ではなく知見の共有として還元する。Slack や勉強会で元の所有者に伝え、取り込むかは所有者の判断に委ねる。
