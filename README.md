---
project:
  name: "cursor-agents-skills"
  title: "Cursor カスタム設定管理"
  status: active
  priority: medium
  created: "2026-02-05"
  updated: "2026-02-13"
  owner: "kai.ko"
  tags: [cursor, agents, skills, commands, rules, github]
  summary: "CursorのAgents/Skills/Commands/RulesをGitHubで一元管理"
  next_action: "ドキュメント整備・棚卸し完了"
---

# Cursor Agents & Skills

Cursor IDE用のカスタム設定（エージェント、スキル、コマンド、ルール）の統合リポジトリです。

## 概要

このリポジトリは `~/.cursor/` 配下の Cursor カスタム設定を一元管理するためのものです。

```
cursor-agents-skills/
├── agents/      # カスタムサブエージェント定義
├── skills/      # ユーザー独自スキル
├── commands/    # スラッシュコマンド定義
├── rules/       # グローバルルール（~/.cursor/rules/）
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
| `chat-history-analyzer.md` | チャット履歴を分析しSubagent/Skill/Command/Ruleを自動提案 |

詳細は [agents/README.md](agents/README.md) を参照してください。

### Skills（スキル）

Cursor の Agent Skills 機能で自動的に読み込まれるスキル定義です。

| スキル | 説明 | トリガー例 |
|--------|------|-----------|
| `chat-history-analyzer` | チャット履歴分析・カスタム設定自動提案 | 「チャット分析」「設定を提案」 |
| `infographic-generator` | グラレコ風インフォグラフィック生成 | 「インフォグラフィックを作成」 |

> **Note**: 一部のスキル（社内機密情報を含む可能性があるもの）は `.gitignore` で除外されています。

### Commands（スラッシュコマンド）

Cursor のスラッシュコマンド（`/` で呼び出し）の定義です。`~/.cursor/commands/` に配置されます。

| カテゴリ | コマンド | 説明 |
|---------|---------|------|
| **スプリント管理** | `sprint-start` | スプリントを手動で開始 |
| | `sprint-end` | スプリントを終了（レビュー・レトロ） |
| | `sprint-status` | スプリントの状態を確認 |
| **タスク管理** | `task-add` | タスクを追加 |
| | `task-update` | タスクのステータスを更新 |
| | `milestone-update` | マイルストーンを更新 |
| | `status-change` | プロジェクトのステータスを変更 |
| **プロジェクト管理** | `project-new` | 新規プロジェクトを作成 |
| | `project-list-sync` | プロジェクト一覧を同期 |
| **レポート・分析** | `mtg-report` | MTG進捗報告サマリーを生成 |
| | `weekly-review` | 週次レビューを実行 |
| | `analyze` | プロジェクト分析を実行 |
| | `analyze-chat` | チャット履歴を分析して設定を提案 |
| | `dashboard` | ダッシュボードを生成 |
| | `today` | 今日対応すべきタスクを抽出 |
| | `team-status` | チーム状態を確認 |
| **ツール** | `slack-post` | Slack分報に投稿 |
| | `slack-enable` | Slack投稿を有効化 |
| | `try-list` | Try一覧を確認 |
| | `infographic` | インフォグラフィックを作成 |
| | `slides` | スライドを作成 |
| | `pre-push` | Push前レビューを実行 |
| | `doc-review` | ドキュメントレビューを実行 |
| | `inventory` | 棚卸しを実行 |

### Rules（グローバルルール）

プロジェクト横断で適用されるグローバルルール（`.mdc` ファイル）です。`~/.cursor/rules/` に配置されます。

| ルール | 説明 | 適用条件 |
|--------|------|---------|
| `regression-prevention.mdc` | デグレ防止3段階防御モデル（保存時チェック） | alwaysApply: true |
| `story-point-guide.mdc` | SP見積もり4軸評価ガイド | tasks.md, milestones.md 等 |

> **Note**: ここで管理するのは `~/.cursor/rules/`（グローバルルール）のみです。各プロジェクト固有の `.cursor/rules/`（ワークスペースルール）は対象外です。

## セットアップ

### 方法1: 直接配置（推奨）

```bash
# リポジトリをクローン
git clone git@github.com:kai-kou/cursor-agents-skills.git ~/dev/cursor-agents-skills

# ~/.cursor に同期
rsync -av ~/dev/cursor-agents-skills/agents/ ~/.cursor/agents/
rsync -av ~/dev/cursor-agents-skills/skills/ ~/.cursor/skills/
rsync -av ~/dev/cursor-agents-skills/commands/ ~/.cursor/commands/
rsync -av ~/dev/cursor-agents-skills/rules/ ~/.cursor/rules/
```

### 方法2: ~/.cursor 内で直接管理

```bash
# ~/.cursor の各ディレクトリを直接このリポジトリで管理する場合
cd ~/.cursor
git clone git@github.com:kai-kou/cursor-agents-skills.git temp
mv temp/agents agents
mv temp/skills skills
mv temp/commands commands
mv temp/rules rules
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

# commands 同期
rsync -av --delete "$REPO_DIR/commands/" "$CURSOR_DIR/commands/"

# rules 同期（グローバルルール）
rsync -av --delete "$REPO_DIR/rules/" "$CURSOR_DIR/rules/"

echo "~/.cursor への同期完了"
```

## 同期対象一覧

| リポジトリ側 | ~/.cursor/ 側 | 内容 |
|-------------|--------------|------|
| `agents/` | `~/.cursor/agents/` | サブエージェント定義 |
| `skills/` | `~/.cursor/skills/` | スキル定義 |
| `commands/` | `~/.cursor/commands/` | スラッシュコマンド定義 |
| `rules/` | `~/.cursor/rules/` | グローバルルール |

## ライセンス

MIT License - 詳細は [agents/LICENSE](agents/LICENSE) を参照してください。

## 作者

kai.ko
