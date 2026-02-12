新規プロジェクトを作成してください。

プロジェクト名: {ここにプロジェクト名を入力}

以下を実行してください：
1. `/Users/kai.ko/dev/_templates/` のテンプレートを読み込む
2. `01_active/{プロジェクト名}/` に以下のファイルを生成：
   - `README.md`（project-init.md テンプレート、YAMLフロントマター必須）
   - `tasks.md`（tasks.md テンプレート、YAMLフロントマター必須）
   - `milestones.md`（milestones.md テンプレート、YAMLフロントマター必須）
   - `.cursor/rules/project-management.mdc`（プロジェクト管理ルール）
3. `_management/PROJECT_STATUS.md` にプロジェクトを追記し、サマリーの件数を再計算
4. YAMLフロントマターの日付に現在日時を設定
5. 変更履歴に記録を追加

## 生成後の自動チェックリスト（必ず確認）

- [ ] README.md に YAMLフロントマター（name, title, status, priority, created, updated, owner, tags, summary, next_action）が含まれているか
- [ ] tasks.md に YAMLフロントマター（total, completed, in_progress, blocked, overall_progress）が含まれているか
- [ ] milestones.md に YAMLフロントマター（total, completed, in_progress, overall_progress）が含まれているか
- [ ] `.cursor/rules/project-management.mdc` が配置されているか
- [ ] PROJECT_STATUS.md のサマリーテーブルの件数が正しいか
- [ ] 変更履歴に記録が追加されているか

## .cursor/rules/project-management.mdc の内容

以下の内容で生成すること：
```
---
description: プロジェクト管理ルール準拠ガイド（全プロジェクト共通）
globs:
  - "**/*"
alwaysApply: true
---

# プロジェクト管理ルール

## 必須ファイル構成
- `README.md` - プロジェクト概要（YAMLフロントマター必須）
- `tasks.md` - タスク管理（YAMLフロントマター必須）
- `milestones.md` - マイルストーン管理（必要に応じて）

## タスク操作ルール
- タスク追加時: `tasks.md` にIDを連番で追加（T001〜T099: Phase 1, T101〜: Phase 2）
- タスク開始時: ステータスを 🔄 に更新
- タスク完了時: ステータスを ✅ に更新、完了日を記録
- 操作後は必ず: YAMLフロントマターの集計値を再計算、updated日付を更新

## ステータス記号
⬜未着手 / 🔄進行中 / ✅完了 / ⏸️保留 / ❌キャンセル / 🚫ブロック / ⚠️遅延

## 優先度
P0（即対応）> P1（今週中）> P2（2週間以内）> P3（適時）

## プロジェクト操作後の必須アクション
- `_management/PROJECT_STATUS.md` を同期すること
- サマリーテーブルの件数を再計算すること
- 変更履歴に記録すること
```
