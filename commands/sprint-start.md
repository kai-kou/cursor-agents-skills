スプリントを手動で開始してください。

プロジェクト名: {プロジェクト名}

---

## 必須前提

- `{project_root}/milestones.md` と `{project_root}/tasks.md` が存在すること
- 存在しない場合はエラーメッセージを表示して終了

---

## 既存スプリント確認

`{project_root}/.sprint-logs/sprint-backlog.md` を確認する:
- ステータスが `in_progress` の場合 → 「既にスプリントが実行中です。/sprint-end で終了するか、/sprint-status で状態を確認してください」と案内して終了
- 存在しない、またはステータスが `completed` / `aborted` の場合 → 新規スプリント開始

---

## ライフサイクル（必ずすべてのフェーズを通過すること）

```
[Phase 1: プランニング]
     │
     ▼
[Phase 2: タスク実行]
     │
     ▼
[Phase 3: 終了ガード] ← ★ コミット&pushはここで止める
     │
     ▼
[Phase 4: レビュー & レトロスペクティブ] ← /sprint-end で実行
```

### ★ 重要: ライフサイクルガード

> **全タスク消化後、絶対にコミット&pushを先に提案してはならない。**
> まずレビュー→レトロスペクティブのフェーズに進むこと。
> コミット&pushはレトロスペクティブ完了後にPOに確認する。

---

## Phase 1: プランニング

### 1-1. 入力データの読み込み（宣言必須）

以下のファイルを読み込み、**読み込んだことをPOに明示する**:

```
📖 読み込み中...
  ├── milestones.md ✅
  ├── tasks.md ✅
  ├── product-backlog.md ✅（存在する場合）
  └── ~/.cursor/try-stock.md ✅（存在する場合）
```

### 1-2. Skill/Subagent利用の宣言（可視性確保）

プランニング開始時に、以下を宣言する:

```
🧩 利用するSkill/Subagent:
  ├── sprint-planner（プランニングワークフロー）
  ├── po-assistant（タスク候補提案・優先度分析）
  └── sp-estimator（SP見積もり）← story-point-guide.mdc が存在する場合
```

**宣言後、対応するSkill定義ファイルを実際に読み込んで従うこと。**

- sprint-planner: `~/.cursor/agents/sprint-master/sprint-planner.md` のワークフローに従う
- po-assistant: `~/.cursor/skills/po-assistant/SKILL.md` を参照
- sp-estimator: `~/.cursor/skills/sp-estimator/SKILL.md` を参照（存在する場合）

### 1-3. sprint-backlog.md の生成（必須）

**sprint-backlog.md は必ず作成すること。省略不可。**

- テンプレート: `~/.cursor/templates/sprint-backlog.md`
- 出力先: `{project_root}/.sprint-logs/sprint-backlog.md`
- PO承認後、ステータスを `in_progress` に変更

### 1-4. PO承認

バックログ案を表形式で提示し、POの「OK」で確定。

---

## Phase 2: タスク実行

### 2-1. 各タスク着手時の宣言（可視性確保）

タスク着手のたびに、以下を表示する:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📌 タスク着手: {タスクID} - {タスク名}
🧩 担当Skill: {sprint-coder / sprint-documenter}
📊 進捗: {N}/{全タスク数} タスク
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

**担当Skillの定義ファイルを実際に読み込んで、品質基準・作業フローに従うこと。**

- コーディング系: `~/.cursor/skills/sprint-coder/SKILL.md`
- ドキュメント系: `~/.cursor/skills/sprint-documenter/SKILL.md`

### 2-2. Flower 5段階モデルの可視化

SP 3以上のタスクでは、5段階の進行をPOに見える形で実行する:

```
  [1/5] 🔍 Research（リサーチ）  ← 現在のフェーズを表示
  [2/5] 📚 Learn（学習）
  [3/5] 📝 Plan（計画）
  [4/5] 💻 Code（コーディング）
  [5/5] 🔎 Review（セルフレビュー）
```

SP 1〜2のタスクではライト版（Plan→Code→簡易Review）でよいが、フェーズの明示は行う。

### 2-3. タスク完了時の更新

- tasks.md のステータスを ✅ に更新（YAMLフロントマター再計算含む）
- sprint-backlog.md の該当タスクを ✅ に更新、SP集計を再計算

### 2-4. タスク完了報告

```
✅ {タスクID}: {タスク名} が完了しました
📁 変更ファイル: {ファイル一覧}
📊 進捗: {完了数}/{全タスク数} タスク ({完了SP}/{計画SP} SP)
```

### 2-5. メンバー別Slack分報投稿（persona/がある場合は必須）

タスク完了報告のたびに、**cursor-times-agentを担当Skill名で起動**する:

- `member_name`: sprint-backlog.mdの「担当」カラムのSkill名（sprint-coder, sprint-documenter 等）
- `post_type`: `task_complete`
- 選定ロジック詳細: `cursor-times-agent.mdc` Section 1.1 を参照

```
例: sprint-documenter が担当タスク完了
  → cursor-times-agent(member_name=sprint-documenter)
  → persona/sprint-documenter.md の人格「ミミ」として投稿
```

#### フォールバック（サブエージェント起動失敗時）

cursor-times-agentサブエージェントがリソース制限等で起動失敗した場合、**メインエージェントが直接投稿する**:

1. `{project_root}/persona/{member_name}.md` を読み込む
2. 人格設定に基づいてタスク完了投稿文を生成（100〜300文字、Slack mrkdwn形式）
3. `slack_post_message` MCPツールで投稿（channel: persona内の `default_channel`）

```
例: サブエージェント起動失敗時
  → persona/sprint-documenter.md を直接読み込み
  → 人格「ミミ」の口調で投稿文を生成
  → slack_post_message MCPで直接投稿
```

**サブエージェント失敗を理由に投稿を省略してはならない。必ずフォールバックを実行すること。**

---

## Phase 3: 終了ガード（★最重要★）

### 全タスク消化後の行動規則

全タスクが完了したら、以下のメッセージを**必ず**表示する:

```
🏁 計画したタスクがすべて完了しました！

📋 次のステップ: レビュー & レトロスペクティブ
  → /sprint-end を実行するか、このまま続けて終了処理に進みます。

スプリントを終了してレビュー・レトロに進みますか？
```

### 禁止事項

- ❌ 全タスク完了後に「コミット&pushしますか？」と聞くこと
- ❌ レビュー・レトロを省略してセッションを終了すること
- ❌ sprint-backlog.md を作成せずにスプリントを完了すること

### POの応答パターン

| POの応答 | 行動 |
|---------|------|
| 「OK」「はい」「進めて」 | /sprint-end の手順（レビュー→レトロ）を自動実行する |
| 「追加作業がある」 | スコープ変更として処理し、Phase 2に戻る |
| 「スプリント不要だった」 | sprint-backlog.md を aborted にして終了 |

### POが「OK」した場合の自動実行

POがスプリント終了を承認したら、**`/sprint-end` の手順を自動的に実行する**:
1. sprint-review-runner のワークフローに従ってレビュー
2. **レビュー完了 → レトロスペクティブに自動遷移**（PO再確認不要）
3. sprint-retro のワークフローに従ってレトロスペクティブ
4. スプリントログ保存
5. **レトロスペクティブ完了後に**「コミット&pushしますか？」と確認

### ★ レビュー→レトロ遷移ルール（ループ防止）

> **背景**: SPRINT-018でレビュー完了後にPO応答ループが発生。

レビュー完了後、**レトロスペクティブにPO再確認なしで自動遷移する**:

- レビューのフィードバック確認は**閉鎖型質問**（「フィードバックはありますか？」）で**1回のみ**
- PO応答「OK」「はい」「特にない」等 → フィードバックなし → **即座にレトロ開始**
- 具体的フィードバック → 処理完了後 → **即座にレトロ開始**

**禁止事項**:
- ❌ レビュー完了後に「レトロに進んでよいですか？」と再確認
- ❌ PO応答を待つ開放型質問でレビューを終了

---

## 可視性チェックリスト（セルフチェック用）

スプリント実行中、以下が守られているか常に確認すること:

- [ ] sprint-backlog.md が作成されているか
- [ ] 各フェーズでどのSkill/Subagentを使っているか宣言したか
- [ ] Skill定義ファイルを実際に読み込んで従っているか
- [ ] タスク着手/完了の報告で担当Skillを明示したか
- [ ] SP 3以上のタスクでFlower 5段階モデルの進行を可視化したか
- [ ] 全タスク完了後、コミット&pushではなくレビュー・レトロへの遷移を提案したか

---

## sprint-workflow.mdc との関係

このコマンドは `sprint-workflow.mdc` のライフサイクルに従って進行する。
差異がある場合は sprint-workflow.mdc を正とする。
このコマンドは sprint-workflow.mdc の**実行ガイド・可視性強化版**である。
