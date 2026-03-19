# Contributing

## オンボーディング

参加手順はエージェント経由で案内されます。
clone 後、エージェントに `users/.template/skills-sharing-guide/SKILL.md` を読ませてください。

## スキルの追加・更新

自分のディレクトリ (`users/<your-github-username>/`) への変更のみを含む PR は**自動マージ**されます。
初回の参加 PR は CODEOWNERS の変更を含むため、メンテナーの approve が必要です。

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

## フォークモデル

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
