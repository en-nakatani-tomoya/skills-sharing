# Contributing

## 初回参加

```bash
# 1. テンプレートから自分のディレクトリを作成
cp -r users/.template users/<your-github-username>

# 2. README.md を編集
vi users/<your-github-username>/README.md

# 3. PR を作成
git checkout -b add-<your-github-username>
git add users/<your-github-username>/
git commit -m "Add <your-github-username>"
git push -u origin HEAD
gh pr create --title "Add <your-github-username>"
```

初回 PR では CODEOWNERS への追加も含めてください（メンテナーが approve します）。

## スキルの追加・更新

自分のディレクトリ (`users/<your-github-username>/`) への変更のみを含む PR は**自動マージ**されます。

```bash
git checkout -b update-my-skills
# スキルを追加・編集
git add users/<your-github-username>/
git commit -m "Add new skill: <skill-name>"
git push -u origin HEAD
gh pr create --title "Update <your-github-username> skills"
# → 自動 approve & マージ
```

## ディレクトリ構成

各スキルは `SKILL.md` を含むディレクトリとして、ユーザーディレクトリ直下にフラットに配置します。

```
users/<your-github-username>/
├── README.md                     # 自己紹介・スキル一覧
├── dev-workflow/
│   └── SKILL.md
├── pr-review/
│   └── SKILL.md
└── test-generator/
    └── SKILL.md
```

エージェント別（claude/, cursor/ 等）のサブディレクトリは不要です。
スキルはエージェント間で共通であり、ローカルでの配置は各自が管理します。

### SKILL.md の書き方

```markdown
---
name: my-skill
description: このスキルが何をするか（1行で）
---

# スキル名

## When to Use This Skill

（どういう場面で使うか）

## Instructions

（具体的な手順・プロンプト）
```

`name` と `description` のフロントマターは、ブラウズツールでの一覧表示に使われます。

## 他の人のスキルを使う

他の人のスキルを使いたい場合は、**自分のディレクトリにコピー（フォーク）**してください。

```bash
./scripts/skills-browse install sample-user/test-generator
```

### なぜコピーなのか

- カスタマイズした場合、変更は自分のスキルとして管理すべき（他人のディレクトリに push しない）
- 元のスキルが更新・削除されても自分の環境に影響しない
- スキルに求めるものは人によって異なる。各自が自分に最適化した版を持つのが自然

### 還元の方法

コピー → カスタマイズ → 改善が生まれた場合は、Slack や勉強会で元の所有者に共有してください。
コードレベルの統合は元の所有者の判断に委ねます。

## starter-kit への貢献

`starter-kit/` への変更は自動マージの対象外です。通常の PR レビューフローに従ってください。
