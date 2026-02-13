チームの現在の状態を表示してください。

以下の情報を統合して、チームロスターの状態を一覧表示してください:

1. `{project_root}/scrum/team-roster.md` からメンバー一覧・ペルソナ情報を読み取る
2. `{project_root}/.sprint-logs/sprint-backlog.md` から現在のスプリント稼働状況を読み取る
3. `{project_root}/scrum/tasks.md` から各メンバーの担当タスク状況を読み取る

### 表示フォーマット

```
## 🏃 チーム状態: {project_name}

**現在のスプリント**: {SPRINT-ID} ({status})

### メンバー状態

| メンバー | 種別 | 担当中タスク | 直近完了タスク | ステータス |
|---------|------|------------|-------------|-----------|
| {name} | {Agent/Skill} | {task_id: task_name} | {task_id} | {Active/Idle} |

### スプリント進捗
- SP消化率: {N}%
- タスク: {completed}/{total}
```

### 注意事項
- scrum/team-roster.mdが存在しない場合は「チームロスターが未作成です」と表示
- スプリントが実行中でない場合は「現在スプリントは実行されていません」と表示
- 各メンバーのステータスは、現在のスプリントで担当タスクがあれば Active、なければ Idle
