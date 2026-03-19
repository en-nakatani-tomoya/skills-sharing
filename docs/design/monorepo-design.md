# 個人スキル共有 Monorepo 設計

> 前提: [ADR-001](../adr/001-personal-skill-repo-structure.md)、[ADR-002](../adr/002-monorepo-design.md)、[ADR-004](../adr/004-flat-structure-and-ownership.md)
> 作成日: 2026-03-19

---

## リポジトリ構造

ADR-004 のフラット構造を反映した最新の構成。

```
skills-sharing/
├── README.md
├── CONTRIBUTING.md
│
├── users/
│   ├── .template/                  # 初回参加用テンプレート
│   ├── tomoya-nakatani/
│   │   ├── README.md               # 自己紹介・スキル一覧（任意）
│   │   ├── dev-workflow/
│   │   │   └── SKILL.md
│   │   └── pr-review/
│   │       └── SKILL.md
│   │
│   ├── akira-yui/
│   │   ├── README.md
│   │   ├── code-refactor/
│   │   │   └── SKILL.md
│   │   └── ...
│   │
│   └── ...
│
├── starter-kit/
│   ├── README.md
│   ├── basic-dev/
│   │   └── SKILL.md
│   └── ...
│
├── docs/
│   ├── adr/
│   ├── design/
│   └── references/
│
├── CODEOWNERS
└── .github/
    └── workflows/
        ├── auto-merge.yml
        └── label-user.yml
```

### ディレクトリ命名

- `users/` 配下は **GitHub ユーザー名**（小文字ハイフン区切り）をディレクトリ名とする
- スキル個別のディレクトリ名は各自の自由

### starter-kit/

まだスキル環境を持っていない人が最初に導入するための推奨スキル集。

- **変更には PR レビューが必要**（個人ディレクトリとは異なるルール）
- 個人スキルから有用なものをここに昇格させるフローを想定
- 利用時は自分のディレクトリにコピーして使う（直接参照しない）

---

## 自動マージ

### 前提条件

- デフォルトブランチ（`main`）へのマージには PR を必須とする
- ブランチ保護ルールで「Require approvals」を有効にする（1 approval）
- 自動マージ bot が approve + merge を行う

### ワークフロー

```yaml
# .github/workflows/auto-merge.yml
name: Auto Merge Personal Skills

on:
  pull_request:
    types: [opened, synchronize, reopened]

permissions:
  contents: write
  pull-requests: write

jobs:
  auto-merge:
    runs-on: ubuntu-latest
    steps:
      - name: Get changed files
        id: changed
        uses: tj-actions/changed-files@v46

      - name: Check if all changes are in author's directory
        id: check
        run: |
          AUTHOR="${{ github.event.pull_request.user.login }}"
          ALLOWED_PREFIX="users/${AUTHOR}/"

          for file in ${{ steps.changed.outputs.all_changed_files }}; do
            if [[ ! "$file" == ${ALLOWED_PREFIX}* ]]; then
              echo "out_of_scope=true" >> "$GITHUB_OUTPUT"
              echo "::notice::${file} is outside ${ALLOWED_PREFIX}"
              exit 0
            fi
          done

          echo "out_of_scope=false" >> "$GITHUB_OUTPUT"

      - name: Approve and merge
        if: steps.check.outputs.out_of_scope == 'false'
        run: |
          gh pr review "${{ github.event.pull_request.number }}" --approve
          gh pr merge "${{ github.event.pull_request.number }}" --squash --auto
        env:
          GH_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

### 判定ロジック

| 変更内容 | 動作 |
|---|---|
| 全て `users/<PR作者>/` 配下 | 自動 approve → 自動マージ |
| `users/<PR作者>/` 以外を含む | 自動マージしない（通常のレビューフロー） |
| `starter-kit/` への変更 | 自動マージしない（レビュー必須） |

---

## CODEOWNERS

```
# 個人ディレクトリは各自がオーナー
/users/tomoya-nakatani/   @tomoya-nakatani
/users/akira-yui/         @akira-yui

# starter-kit はメンテナーチームがオーナー
/starter-kit/             @org/skill-maintainers

# ワークフロー・リポジトリ設定
/.github/                 @org/skill-maintainers
/CODEOWNERS               @org/skill-maintainers
```

新メンバー参加時に CODEOWNERS にエントリーを追加する。この追加自体はメンテナーが approve する。

---

## スキルの利用方法

### 自分のスキルを使う: シンボリックリンク

自分のディレクトリを各エージェントのスキルフォルダにリンクする（ADR-004）。

```bash
ln -s ~/skills-sharing/users/tomoya-nakatani ~/.claude/skills
ln -s ~/skills-sharing/users/tomoya-nakatani ~/.cursor/skills
```

- シンボリックリンク 1 本で全スキルが全エージェントで使える
- スキルの追加・削除時にリンクの張り直しが不要
- `git pull` だけで最新のスキルがローカルに反映される

### 他人のスキルを使う: コピー（フォーク）

他人のスキルを使いたい場合、自分のディレクトリにコピーして取り込む（ADR-004）。

```bash
cp -r users/sample-user/test-generator users/tomoya-nakatani/test-generator
# カスタマイズして自分のスキルとして push
```

`skills-browse` を使う場合:

```bash
skills-browse install sample-user/test-generator
# → users/<自分>/test-generator/ にコピーされる
```

### 初回セットアップ

```bash
skills-browse setup
# → ~/.claude/skills → users/<自分>/ のシンボリックリンクを作成
# → ~/.cursor/skills → users/<自分>/ のシンボリックリンクを作成
```

---

## オンボーディング

### 新メンバーの参加手順

1. リポジトリを clone する
2. `users/<github-username>/` ディレクトリを作成し、README.md を置く
3. CODEOWNERS にエントリーを追加する PR を出す（メンテナーが approve）
4. 以降、自分のディレクトリへの変更は PR を出すだけで自動マージ

### テンプレート

初回参加用のディレクトリテンプレートを用意しておく。

```bash
# 参加スクリプト（構想）
./scripts/join.sh tomoya-nakatani
# → users/tomoya-nakatani/ を雛形から作成
# → CODEOWNERS にエントリー追加
# → PR を自動作成
```

---

## セキュリティ

- 本リポジトリは**社内 Organization の private リポジトリ**とする
- 社内全員が閲覧可能であることを前提に、機密性の高い情報（API キー、内部 URL、業務ロジック詳細）は含めない
- 業務固有の情報はチームスキル（各開発リポジトリ）側に閉じる
- スキルの内容は**汎用的なプロンプト設計・ワークフロー設計のノウハウ**に限定する

---

## 運用ルール

1. **自分のディレクトリ以外を変更する PR は出さない**（starter-kit への貢献を除く）
2. **他人のスキルを参考にするのは自由**。コピー・改変して自分のディレクトリに置いてよい
3. **スキルの削除・大幅変更も自由**。自分のディレクトリは自分の判断で管理する
4. **README.md の維持を推奨**。何のスキルがあるか、簡単な説明があると他の人が見つけやすい
5. **勉強会・Slack での共有を推奨**。良いスキルができたらチームに知らせる
