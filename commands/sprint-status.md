現在のスプリントの状況を表示してください。

プロジェクト名: {プロジェクト名}

以下の情報を集約して表示してください:

1. `{project_root}/.sprint-logs/sprint-backlog.md` を読み取る
2. `{project_root}/tasks.md` から該当タスクの最新ステータスを読み取る

### 表示フォーマット

```
## 📊 スプリント状況: {SPRINT-ID}

**日付**: {date}
**ステータス**: {status}
**目標**: {sprint goal}

### バックログ進捗

| # | タスクID | タスク名 | SP | 担当 | ステータス |
|---|---------|---------|-----|------|-----------|
| 1 | {T-ID} | {name} | {SP} | {skill} | {status} |

### SP集計
- 計画: {planned} SP
- 完了: {completed} SP
- 消化率: {rate}%
- 残り: {remaining} SP

### 次のアクション
- {次に着手すべきタスクの提案}
```

### 注意事項
- sprint-backlog.md が存在しない場合は「現在スプリントは実行されていません。/sprint-start で開始してください」と案内
- ステータスが completed の場合は「このスプリントは完了済みです」と表示
