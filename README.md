# Skills Sharing

社内エンジニアのエージェントスキルを共有・閲覧するためのリポジトリ。

## 構成

```
users/           各自のスキル（自分のディレクトリは自由に管理）
starter-kit/     推奨スキル集（レビュー必須）
scripts/         ブラウズ・フォーク用ツール
```

## 前提条件

- [gh CLI](https://cli.github.com/) がインストール・認証済みであること
  - `gh auth login` で GitHub にログイン済みであること

## はじめかた

### 1. リポジトリを clone する

```bash
git clone git@github.com:en-nakatani-tomoya/skills-sharing.git ~/skills-sharing
cd ~/skills-sharing
```

### 2. エージェントにオンボーディングを依頼する

clone したら、お使いのエージェント（Claude, Cursor 等）に以下のスキルを読ませてください。
参加手順・環境セットアップ・使い方まで案内してくれます。

```
users/.template/skills-sharing-guide/SKILL.md
```

エージェントへの指示例：

> `users/.template/skills-sharing-guide/SKILL.md` を読んで、このリポジトリへの参加手順を案内してください。

### クイックリファレンス（手動で行う場合）

```bash
# 参加（ディレクトリ作成 + CODEOWNERS 追加）
./scripts/skills-browse join

# 表示されるコマンドに従って PR を作成
# PR がマージされたら、ローカル環境をセットアップ
./scripts/skills-browse setup

# 環境が正しいか確認
./scripts/skills-browse doctor
```

## スキルのブラウズ

```bash
./scripts/skills-browse list                    # 全スキル一覧
./scripts/skills-browse search "PR レビュー"     # キーワード検索
./scripts/skills-browse show sample-user/test-generator  # 詳細表示
```

## ルール

- **自分のディレクトリ以外は変更しない**（starter-kit への貢献 PR を除く）
- 他人のスキルの参考・コピー・改変は自由
- スキルの内容は**汎用的なプロンプト設計・ワークフロー**に限定する（機密情報を含めない）
- 詳しくは [CONTRIBUTING.md](CONTRIBUTING.md) を参照
