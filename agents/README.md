# Cursor Custom Agents

Cursor IDE用のカスタムエージェント（サブエージェント）集です。ドキュメントレビュー、プッシュ前レビュー、スライド生成などのワークフローを自動化します。

## 概要

このリポジトリには、Cursor IDEで使用できるカスタムエージェント定義ファイル（`.md`形式）が含まれています。各エージェントは特定のタスクに特化しており、複数のサブエージェントを組み合わせた複雑なワークフローを実行できます。

## エージェント一覧

### オーケストレーター（メインエージェント）

| エージェント | 説明 | 使用例 |
|-------------|------|--------|
| `document-review-all.md` | 5つのAIモデルで並列レビューし、統合して修正計画書を作成 | 「ドキュメントをレビューして修正計画書を作成」 |
| `pre-push-review.md` | Push前にセキュリティ・品質・依存関係をチェック | 「Push前にレビュー」「コードをチェック」 |
| `slide-generator.md` | ドキュメントから対象ユーザー向けスライド画像を作成 | 「スライドを作成」「プレゼン資料を作成」 |
| `project-manager.md` | プロジェクトのライフサイクル管理（作成・タスク・マイルストーン） | 「新規プロジェクト作成」「タスク追加」「ステータス変更」 |
| `project-analyzer.md` | プロジェクト横断分析・ダッシュボード生成・レポート作成 | 「ダッシュボード更新」「週次レビュー」「今日のタスク」 |
| `mtg-reporter.md` | MTG進捗報告サマリーの自動生成（定量+定性） | 「MTG報告を作成」「進捗報告」 |
| `requirement-definition.md` | あらゆるプロジェクトの要件定義書をMarkdown形式で作成 | 「要件定義を作成」「要件をまとめて」 |

### ドキュメントレビュー・サブエージェント

`document-review/` ディレクトリに含まれています：

| エージェント | 役割 | 観点 |
|-------------|------|------|
| `review-opus-4.6.md` | 戦略コンサルタント兼組織開発エキスパート | 全体構造、戦略的整合性 |
| `review-sonnet-4.5.md` | プロジェクトマネージャー兼実行設計スペシャリスト | 実行可能性、詳細設計 |
| `review-gpt-5.2-codex.md` | ロジカルシンキング専門家兼データアナリスト | 論理的整合性 |
| `review-gemini-3-pro.md` | ワークショップ・ファシリテーター兼UXデザイナー | 参加者体験、心理的安全性 |
| `review-grok-code.md` | リスクマネージャー兼イノベーションコンサルタント | リスク分析、代替案 |
| `review-integrate.md` | レビュー統合スペシャリスト | 複数レビューの統合 |
| `revision-plan.md` | 修正計画書作成スペシャリスト | 修正計画書の作成 |

### Pre-Pushレビュー・サブエージェント

`pre-push-review/` ディレクトリに含まれています：

| エージェント | 役割 | チェック内容 |
|-------------|------|------------|
| `security-check.md` | セキュリティスペシャリスト | 機密情報・APIキー・パスワード |
| `code-quality.md` | コード品質スペシャリスト | Linter・型チェック・コードスタイル |
| `git-hygiene.md` | Git衛生スペシャリスト | コミットメッセージ・.gitignore |
| `documentation.md` | ドキュメントスペシャリスト | README・必要なドキュメント |
| `dependency-check.md` | 依存関係スペシャリスト | 脆弱性・ライセンス問題 |
| `integrate-and-fix.md` | 統合・自動修正スペシャリスト | 結果統合・自動修正 |

### スライド作成・サブエージェント

`slide-generator/` ディレクトリに含まれています：

| エージェント | 役割 | 機能 |
|-------------|------|------|
| `analyze-document.md` | ドキュメント分析スペシャリスト | 要点抽出・スライド構成案作成 |
| `create-slide-outline.md` | スライド構成クリエイター | 各スライドの構成設計・画像生成プロンプト作成 |
| `google-slides-creator.md` | Googleスライドクリエイター | スライド画像からPPTX生成・Google Driveアップロード |

### プロジェクト分析・サブエージェント

`project-analyzer/` ディレクトリに含まれています：

| エージェント | 役割 | 機能 |
|-------------|------|------|
| `scan-project.md` | プロジェクトスキャナー | 個別プロジェクトの進捗データ収集 |
| `generate-dashboard.md` | ダッシュボードジェネレーター | スキャン結果からDASHBOARD.md生成 |
| `generate-report.md` | レポートジェネレーター | 週次レビュー・進捗分析・棚卸しレポート生成 |

### 要件定義・サブエージェント

`requirement-definition/` ディレクトリに含まれています：

| エージェント | 役割 | 機能 |
|-------------|------|------|
| `hearing-facilitator.md` | ヒアリングファシリテーター | 初期情報からヒアリングシート生成・ユーザーとの認識合わせ |
| `context-analyzer.md` | コンテキスト分析スペシャリスト | プロジェクト文脈分析・フレームワーク選定 |
| `stakeholder-mapper.md` | ステークホルダー分析スペシャリスト | ステークホルダー特定・利害分析・影響度マトリクス |
| `scope-definer.md` | スコープ定義スペシャリスト | MECE原則に基づくスコープ定義・In/Out判定 |
| `risk-constraint-analyzer.md` | リスク・制約分析スペシャリスト | リスク特定・前提検証・対策立案 |
| `document-composer.md` | ドキュメントアーキテクト | 分析結果統合・要件定義書の構成・執筆 |
| `humanize-editor.md` | ドキュメント・ヒューマナイザー | AI検出パターン除去・自然な文体への書き換え |
| `final-integrator.md` | 最終統合スペシャリスト | レビュー結果反映・最終版作成 |

## 使用方法

### 前提条件

- [Cursor IDE](https://cursor.sh/) がインストールされていること
- カスタムエージェント機能が有効になっていること

### セットアップ

1. このリポジトリをクローン：

```bash
git clone https://github.com/kai-kou/cursor-agents.git
```

2. Cursorの設定でエージェントディレクトリを指定

3. チャットで以下のように呼び出し：

```
# ドキュメントレビュー
ドキュメントをレビューして修正計画書を作成してください
対象: /path/to/document.md

# Push前レビュー
Push前にレビューしてください

# スライド作成（基本）
プロジェクト計画書.md を経営層向けにスライド化してください

# スライド作成（フルオプション）
API設計書.md をエンジニア向けにスライド化してください --google-slides

# 要件定義（ソフトウェア）
社内の勤怠管理システムを刷新したい。要件定義をまとめてほしい

# 要件定義（制度設計）
エンジニアの評価制度を新しく作りたい。要件を整理してほしい

# 要件定義（イベント企画）
来月の社内ハッカソンの企画をまとめたい
```

## ファイル構造

```
.
├── README.md                    # このファイル
├── LICENSE                      # MITライセンス
├── document-review-all.md       # ドキュメントレビュー・オーケストレーター
├── pre-push-review.md           # Pre-Pushレビュー・オーケストレーター
├── slide-generator.md           # スライド作成・オーケストレーター
├── project-manager.md           # プロジェクト管理エージェント
├── project-analyzer.md          # プロジェクト分析・オーケストレーター
├── mtg-reporter.md              # MTG進捗報告エージェント
├── document-review/             # ドキュメントレビュー・サブエージェント
│   ├── review-gemini-3-pro.md
│   ├── review-gpt-5.2-codex.md
│   ├── review-grok-code.md
│   ├── review-integrate.md
│   ├── review-opus-4.6.md
│   ├── review-sonnet-4.5.md
│   └── revision-plan.md
├── pre-push-review/             # Pre-Pushレビュー・サブエージェント
│   ├── code-quality.md
│   ├── dependency-check.md
│   ├── documentation.md
│   ├── git-hygiene.md
│   ├── integrate-and-fix.md
│   └── security-check.md
├── slide-generator/             # スライド作成・サブエージェント
│   ├── analyze-document.md
│   ├── create-slide-outline.md
│   └── google-slides-creator.md
├── project-analyzer/            # プロジェクト分析・サブエージェント
│   ├── scan-project.md
│   ├── generate-dashboard.md
│   └── generate-report.md
└── requirement-definition/      # 要件定義・サブエージェント
    ├── hearing-facilitator.md
    ├── context-analyzer.md
    ├── stakeholder-mapper.md
    ├── scope-definer.md
    ├── risk-constraint-analyzer.md
    ├── document-composer.md
    ├── humanize-editor.md
    └── final-integrator.md
```

## ライセンス

MIT License - 詳細は [LICENSE](LICENSE) を参照してください。

## 貢献

Issue や Pull Request を歓迎します。

## 作者

kai.ko
