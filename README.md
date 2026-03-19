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
- `--me` オプションで明示的にユーザー名を指定する場合は `gh` CLI は不要

## 参加方法

1. このリポジトリを clone する

```bash
git clone git@github.com:en-nakatani-tomoya/skills-sharing.git ~/skills-sharing
```

2. 自分のディレクトリを作成する

```bash
cp -r users/.template users/<your-github-username>
```

3. `users/<your-github-username>/README.md` を編集して PR を出す

自分のディレクトリへの変更は自動マージされます。

## ローカル環境のセットアップ

自分のディレクトリを各エージェントのスキルフォルダにリンクします。

```bash
./scripts/skills-browse setup --me <your-github-username>
# → ~/.claude/skills → users/<you>/ のシンボリックリンクを作成
# → ~/.cursor/skills → users/<you>/ のシンボリックリンクを作成
```

以降、スキルの追加・編集は `users/<you>/` で行い、`git push` するだけです。
`git pull` すれば最新のスキルがローカルに即反映されます。

## スキルのブラウズ

```bash
# 全員のスキル一覧
./scripts/skills-browse list

# キーワード検索
./scripts/skills-browse search "PR レビュー"

# スキルの詳細を見る
./scripts/skills-browse show sample-user/test-generator
```

## 他の人のスキルを使う

他の人のスキルは自分のディレクトリに**フォーク（コピー）**して使います。

```bash
# 他の人のスキルを自分のディレクトリにコピー
./scripts/skills-browse install sample-user/test-generator

# カスタマイズして自分のスキルとして push
git add users/<you>/test-generator
git commit -m "Add test-generator (forked from sample-user)"
git push
```

シンボリックリンクで直接参照するのではなく、コピーして自分のものにするのが基本方針です。
詳しくは [CONTRIBUTING.md](CONTRIBUTING.md) を参照してください。

## ルール

- **自分のディレクトリ以外は変更しない**（starter-kit への貢献 PR を除く）
- 他人のスキルの参考・コピー・改変は自由
- スキルの内容は**汎用的なプロンプト設計・ワークフロー**に限定する（機密情報を含めない）
- README.md で自分のスキルの説明を書くことを推奨
