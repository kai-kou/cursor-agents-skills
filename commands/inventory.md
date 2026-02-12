`/Users/kai.ko/dev/` 配下の全プロジェクトを一斉点検（棚卸し）してください。

以下を実行してください：

## 1. フォルダ走査
- 各ステータスフォルダ（01_active〜05_archive）のプロジェクトを走査

## 2. PROJECT_STATUS.md 整合性チェック
- `_management/PROJECT_STATUS.md` との整合性チェック
- フォルダに存在するがPROJECT_STATUS.mdに未登録のプロジェクトを検出
- PROJECT_STATUS.mdに登録されているがフォルダに存在しないプロジェクトを検出
- サマリーテーブルの件数が正しいか検証

## 3. テンプレート準拠チェック（全アクティブプロジェクト対象）
以下のファイルの有無と形式を確認：
- [ ] `README.md` が存在し、YAMLフロントマター（name, title, status, priority, created, updated, owner, tags, summary, next_action）が含まれているか
- [ ] `tasks.md` が存在し、YAMLフロントマター（total, completed, in_progress, blocked, overall_progress）が含まれているか
- [ ] `.cursor/rules/project-management.mdc` が配置されているか

## 4. 停滞プロジェクト検出
- 長期停滞プロジェクトの検出（30日以上更新なし）
- ステータス変更の提案（例: 長期停滞 → 保留への移行を推奨）

## 5. 更新
- PROJECT_STATUS.md を最新状態に更新

## 出力形式

### 整合性チェック結果
| チェック項目 | 結果 |
|---|---|

### テンプレート準拠状況
| プロジェクト | README.md (YAML) | tasks.md | .cursor/rules |
|---|---|---|---|

### 停滞プロジェクト
| プロジェクト | 最終更新日 | 停滞日数 | 推奨アクション |
|---|---|---|---|

### フォルダ別プロジェクト一覧
- 01_active: X件
- 02_completed: X件
- 03_on-hold: X件
- 04_not-started: X件
- 05_archive: X件

### 不整合・問題点
（あれば記載）

### アクション項目
（あれば記載）
