---
project:
  name: "cursor-agents-skills"
  title: "Cursor Agents & Skills管理"
  status: active
  priority: medium
  created: "2026-02-05"
  updated: "2026-02-10"
  owner: "kai.ko"
  tags: [cursor, agents, skills, github]
  summary: "CursorのAgents/SkillsをGitHubで管理"
  next_action: "Agents/Skills整理・登録"
---

# Cursor Agents & Skills

Cursor IDE用のカスタムエージェントとスキルの統合リポジトリです。

## 概要

このリポジトリは `~/.cursor/` 配下の `agents` と `skills` を一元管理するためのものです。

```
cursor-agents-skills/
├── agents/      # カスタムサブエージェント定義
├── skills/      # ユーザー独自スキル
└── README.md
```

## 含まれるコンテンツ

### Agents（サブエージェント）

Cursor の Task ツールで呼び出せるカスタムサブエージェント定義です。

| エージェント | 説明 |
|-------------|------|
| `document-review-all.md` | 5つのAIモデルで並列レビューし、修正計画書を作成 |
| `pre-push-review.md` | Push前にセキュリティ・品質・依存関係をチェック |
| `slide-generator.md` | ドキュメントから対象ユーザー向けスライド画像を作成 |
| `requirement-definition.md` | あらゆるプロジェクトの要件定義書をMarkdown形式で作成 |

詳細は [agents/README.md](agents/README.md) を参照してください。

### Skills（スキル）

Cursor の Agent Skills 機能で自動的に読み込まれるスキル定義です。

| スキル | 説明 | トリガー例 |
|--------|------|-----------|
| `infographic-generator` | グラレコ風インフォグラフィック生成 | 「インフォグラフィックを作成」 |

> **Note**: 一部のスキル（社内機密情報を含む可能性があるもの）は `.gitignore` で除外されています。

## セットアップ

### 方法1: 直接配置（推奨）

```bash
# リポジトリをクローン
git clone git@github.com:kai-kou/cursor-agents-skills.git ~/dev/cursor-agents-skills

# ~/.cursor に同期
rsync -av ~/dev/cursor-agents-skills/agents/ ~/.cursor/agents/
rsync -av ~/dev/cursor-agents-skills/skills/ ~/.cursor/skills/
```

### 方法2: ~/.cursor 内で直接管理

```bash
# ~/.cursor/agents と ~/.cursor/skills を直接このリポジトリで管理する場合
cd ~/.cursor
git clone git@github.com:kai-kou/cursor-agents-skills.git temp
mv temp/agents agents
mv temp/skills skills
rm -rf temp
```

## 同期スクリプト

リポジトリの内容を `~/.cursor` に同期するスクリプト例：

```bash
#!/bin/bash
# sync-to-cursor.sh

REPO_DIR="$(dirname "$0")"
CURSOR_DIR="$HOME/.cursor"

# agents 同期
rsync -av --delete "$REPO_DIR/agents/" "$CURSOR_DIR/agents/"

# skills 同期
rsync -av --delete "$REPO_DIR/skills/" "$CURSOR_DIR/skills/"

echo "~/.cursor への同期完了"
```

## ライセンス

MIT License - 詳細は [agents/LICENSE](agents/LICENSE) を参照してください。

## 作者

kai.ko
