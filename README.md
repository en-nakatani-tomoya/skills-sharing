# Skills Sharing

社内エンジニアのエージェントスキルを共有・閲覧するためのリポジトリ。

## 構成

```
users/           各自のスキル（自分のディレクトリは自由に管理）
starter-kit/     推奨スキル集（レビュー必須）
scripts/         ブラウズ・インストール用ツール
```

## 参加方法

1. このリポジトリを clone する

```bash
git clone git@github.com:tomoya-nakatani/skills-sharing.git
```

2. 自分のディレクトリを作成する

```bash
cp -r users/.template users/<your-github-username>
```

3. `users/<your-github-username>/README.md` を編集して PR を出す

自分のディレクトリへの変更は自動マージされます。

## スキルのブラウズ

```bash
# 全員のスキル一覧
./scripts/skills-browse list

# キーワード検索
./scripts/skills-browse search "PR レビュー"

# 特定ユーザーのスキル一覧
./scripts/skills-browse list --user akira-yui
```

## スキルのインストール

```bash
# 他の人のスキルを Claude Code にインストール
./scripts/skills-browse install <user>/<skill> --agent claude

# Cursor にインストール
./scripts/skills-browse install <user>/<skill> --agent cursor
```

## ルール

- **自分のディレクトリ以外は変更しない**（starter-kit への貢献 PR を除く）
- 他人のスキルの参考・コピー・改変は自由
- スキルの内容は**汎用的なプロンプト設計・ワークフロー**に限定する（機密情報を含めない）
- README.md で自分のスキルの説明を書くことを推奨
