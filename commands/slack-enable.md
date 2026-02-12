現在のプロジェクトでSlack分報の自動投稿を有効化してください。

## 手順

1. プロジェクトルートパスを特定する
2. `persona/` ディレクトリの有無を確認
   - 既に存在する場合 → 設定済みの旨を表示し、現在の人格設定を簡潔に紹介
   - 存在しない場合 → 以下のステップで新規作成
3. `/Users/kai.ko/dev/01_active/cursor-times-agent/persona/default.md` をテンプレートとして読み込み
4. `{project_root}/persona/` ディレクトリを作成
5. テンプレートをベースに `{project_root}/persona/kuro.md` を生成
   - `hashtags` にプロジェクト名（ディレクトリ名）を `#project-name` として追加
   - `created` / `updated` を当日日付に更新
6. 有効化完了を報告

## 報告フォーマット

```
✅ Slack分報の自動投稿を有効化しました
📁 プロジェクト: {プロジェクト名}
👤 メンバー: kuro
📌 投稿先: #kai-cursor-times
📋 人格設定: {project_root}/persona/kuro.md

以降、タスク完了時にSlack分報が自動投稿されます。
人格をカスタマイズする場合は persona/kuro.md を編集してください。
```
