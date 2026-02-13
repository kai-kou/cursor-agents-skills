Tryストックの一覧を表示してください。

---

## 入力

`~/.cursor/try-stock.md` を読み取る。

---

## 表示フォーマット

### デフォルト表示（Pending のみ）

```
## 📋 Try ストック一覧

**合計**: {total}件（Pending: {pending} / In Progress: {in_progress} / Completed: {completed}）
**次の棚卸しトリガー**: {next_stocktake_trigger}

### 🔴 優先度 High（次スプリントで着手）

| TRY-ID | 登録日 | ソース | 改善内容 | 対象 |
|--------|--------|--------|---------|------|
| {TRY-ID} | {date} | {source} | {content} | {target} |

### 🟡 優先度 Medium（3スプリント以内）

| TRY-ID | 登録日 | ソース | 改善内容 | 対象 |
|--------|--------|--------|---------|------|
| {TRY-ID} | {date} | {source} | {content} | {target} |

### 🟢 優先度 Low（機会があれば）

| TRY-ID | 登録日 | ソース | 改善内容 | 対象 |
|--------|--------|--------|---------|------|
| {TRY-ID} | {date} | {source} | {content} | {target} |
```

### オプション: 全件表示

ユーザーが「全件」「完了も含めて」と指示した場合:
- Completed / Cancelled セクションも表示する

### オプション: フィルター表示

ユーザーが特定の条件を指定した場合:
- 「Highだけ」→ 優先度High のみ表示
- 「Process系だけ」→ 対象が Process のみ表示
- 「{project_name}のTry」→ ソースに {project_name} を含むもののみ表示

---

## 表示後のアクション提案

一覧表示後、以下のアクション候補を提示する:

```
🤔 次のアクション:
1. 「TRY-xxx を着手に変更」→ In Progress に移動
2. 「TRY-xxx を完了に」→ Completed に移動（効果を記録）
3. 「棚卸しして」→ 陳腐化したTryの棚卸し実施
4. 「High のTryをタスク化して」→ scrum/tasks.md への登録を検討
```

---

## エラーハンドリング

| 状況 | 対応 |
|------|------|
| try-stock.md が存在しない | 「try-stock.md が見つかりません。`~/.cursor/templates/try-stock.md` からコピーして配置してください」と案内 |
| Pending が 0件 | 「🎉 未着手のTryはありません！」と表示。Completed件数を参考情報として表示 |
| Pending が 20件超 | 「⚠️ Tryが{N}件蓄積しています。棚卸しを推奨します」と追加通知 |
