現在のセッション内容をSlack分報に投稿してください。

## 手順

1. プロジェクトルートの `persona/` ディレクトリを確認
   - 存在しない場合 → `/slack-enable` の手順でまず有効化してから続行
2. cursor-times-agent サブエージェントを起動:
   - `project_path`: 現在のプロジェクトルートパス
   - `member_name`: persona/ 内のメンバー名（.mdファイル名から取得）
   - `post_type`: 状況に応じて選択（task_complete / progress / catchup）
3. 投稿完了を報告
