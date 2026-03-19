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

## スキルのディレクトリ構成

各スキルは `SKILL.md` を含むディレクトリとして管理します。

```
users/<your-github-username>/
├── README.md                     # 自己紹介・スキル一覧
├── claude/
│   └── skills/
│       └── <skill-name>/
│           └── SKILL.md          # スキル本体
└── cursor/
    └── skills/
        └── <skill-name>/
            └── SKILL.md
```

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

## starter-kit への貢献

`starter-kit/` への変更は自動マージの対象外です。通常の PR レビューフローに従ってください。
