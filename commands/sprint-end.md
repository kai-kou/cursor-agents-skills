スプリントを手動で終了してください。

プロジェクト名: {プロジェクト名}

---

## 事前確認

### sprint-backlog.md の状態確認

`{project_root}/.sprint-logs/sprint-backlog.md` を確認する:

| 状態 | 行動 |
|------|------|
| 存在しない | → **復旧モード**で実行（下記「復旧モード」参照） |
| ステータスが `completed` | → 「終了可能なスプリントがありません」と案内して終了 |
| ステータスが `in_progress` | → 通常の終了処理を実行 |

---

## 復旧モード（sprint-backlog.md が存在しない場合）

`/sprint-start` でスプリントを開始したが sprint-backlog.md が作成されなかった場合の救済措置:

1. **現在のセッションの作業内容を復元する**
   - `git diff --stat` で変更ファイルを特定
   - `git log --oneline -5` で直近のコミットを確認
   - tasks.md の🔄/✅ステータスから実行タスクを推定

2. **簡易 sprint-backlog.md を生成する**
   - テンプレートから sprint-backlog.md を作成
   - 復元した情報でバックログを埋める
   - ステータスは `in_progress` で生成

3. **POに確認する**
   ```
   ⚠️ sprint-backlog.md が存在しませんでした。
   セッションの作業内容から復元しました:
   
   | タスク | 変更内容 | ステータス |
   |--------|---------|-----------|
   | {復元した内容} | ... | ✅/🔄 |
   
   この内容で終了処理を進めてよいですか？
   ```

4. POの承認後、通常の終了処理に合流する

---

## 通常の終了処理

### Phase 1: 未完了タスクの確認

sprint-backlog.md で未完了タスク（⬜/🔄）がある場合:

```
⚠️ 未完了タスクがあります:

| タスクID | タスク名 | SP | 現ステータス |
|---------|---------|-----|-----------|
| {T-ID} | {名前} | {SP} | {⬜/🔄} |

終了してよいですか？未完了タスクは scrum/tasks.md に持ち越しとして記録します。
```

POの承認後、持ち越し処理を実施する。

### Phase 2: レビュー

**Skill/Subagent利用の宣言（可視性確保）:**

```
🧩 レビューフェーズ開始
  └── sprint-review-runner を使用
```

**sprint-review-runner の定義ファイル（`~/.cursor/agents/sprint-master/sprint-review-runner.md`）を実際に読み込んで、ワークフローに従うこと。**

実行内容:
1. 成果サマリー作成（消化タスク一覧・変更ファイル・SP実績）
2. 品質セルフレビュー（Flower Phase 5: コードクリーンアップ・整合性・セキュリティ・アンチパターン）
3. PO共有（サマリー+セルフレビュー結果を提示）
4. フィードバック処理
   - 即座対応: SP 2以下、新規ファイル作成なし、追記・修正レベル → その場で対応
   - 持越し: 上記に該当しない → scrum/tasks.md に次スプリント向けタスクとして登録
5. cursor-agents-skills の変更があった場合は連携チェックを実施

### ★ Phase 2→3 遷移ルール（ループ防止）

> **背景**: SPRINT-018でレビュー完了後にPO応答ループが発生。以下のルールで防止する。

レビュー完了後、**レトロスペクティブにPO再確認なしで自動遷移する**:

| レビューでのPO応答 | 遷移アクション |
|-------------------|-------------|
| 「OK」「はい」「特にない」「なし」「進めて」 | フィードバックなし → 即座にPhase 3へ |
| 具体的なフィードバック → 処理完了 | 全処理完了 → 即座にPhase 3へ |

**禁止事項**:
- ❌ レビュー完了後に「レトロに進んでよいですか？」と再確認
- ❌ PO応答を待つ開放型質問でPhase 2を終了

**正しい遷移**:
```
レビュー完了
  → 「レビューが完了しました。レトロスペクティブに進みます。」と宣言
  → 即座にPhase 3を開始（PO再応答不要）
```

### Phase 3: レトロスペクティブ

**Skill/Subagent利用の宣言（可視性確保）:**

```
🧩 レトロスペクティブ開始
  ├── sprint-retro を使用
  └── 各Skillの振り返り視点を参照:
      ├── sprint-coder/SKILL.md（コーダー視点）
      ├── sprint-documenter/SKILL.md（ドキュメンテーション視点）
      └── po-assistant/SKILL.md（PO補佐視点）
```

**sprint-retro の定義ファイル（`~/.cursor/agents/sprint-master/sprint-retro.md`）を実際に読み込んで、ワークフローに従うこと。**

実行内容:
1. KPT整理（Keep/Problem/Try）
2. メンバー視点の集約（実際に稼働したSkillの視点のみ）
3. Tryの具体化（TRY-ID採番・対象・優先度）
4. メトリクス計算（SP消化率・セッション有効稼働率・PO判断待ち時間・自律実行率）
5. PO確認（Tryの即座実施判断）
6. Try登録（`~/.cursor/try-stock.md`）

### Phase 4: ログ保存・集計更新

1. **スプリントログ保存**
   - テンプレート: `~/.cursor/templates/sprint-log.md`
   - 保存先: `{project_root}/.sprint-logs/SPRINT-{連番3桁}.md`
   - 連番: 既存の SPRINT-*.md から最大番号を取得 +1

2. **KPT履歴更新**: `{project_root}/scrum/kpt-history.md` に追記

3. **メンバー状態更新**: `{project_root}/scrum/team-roster.md` の稼働履歴を更新

4. **scrum/tasks.md の集計更新**: YAMLフロントマターの集計値を再計算

5. **scrum/milestones.md の進捗更新**: 対象マイルストーンの進捗率を再計算

6. **sprint-backlog.md のステータスを `completed` に更新**

### Phase 5: スプリント完了Slack投稿（persona/がある場合は必須）

レトロスペクティブ完了後、**cursor-times-agentを `sprint-master` で起動**し、スプリント全体の振り返りをSlack分報に投稿する:

- `member_name`: `sprint-master`
- `post_type`: `task_complete`
- `session_summary`: スプリント全体の成果・SP消化率・主な成果物・レトロのTryなど

```
例: SPRINT-011 完了
  → cursor-times-agent(member_name=sprint-master)
  → persona/sprint-master.md の人格「シバ」として投稿
```

### Phase 6: 完了報告

#### ★ コミット&push前提条件チェック（cpコマンドガード）

> **背景**: SPRINT-022でレビュー/レトロ前にコミット&pushが実行される問題が発生。

コミット&pushを提案する**前に**、以下の前提条件を確認する:

| # | 前提条件 | 確認方法 |
|---|---------|---------|
| 1 | レビュー（Phase 2）が完了していること | sprint-backlog.md にレビュー結果が記録済み |
| 2 | レトロスペクティブ（Phase 3）が完了していること | sprint-backlog.md のステータスが `completed` |
| 3 | スプリントログが保存されていること | `.sprint-logs/SPRINT-{ID}.md` が存在 |

**すべての前提条件を満たしている場合のみ**、「コミット&pushしますか？」を提案する。
満たしていない場合は、未完了のフェーズに遷移する。

```
🏁 SPRINT-{ID} が完了しました！

📊 SP消化率: {完了SP}/{計画SP} = {消化率}%
📋 完了タスク: {完了数}/{全タスク数}
📁 変更ファイル: {総数}件
📝 スプリントログ: .sprint-logs/SPRINT-{ID}.md

🧩 利用したSkill/Subagent:
  ├── sprint-planner（プランニング）
  ├── sprint-review-runner（レビュー）
  ├── sprint-retro（レトロスペクティブ）
  ├── {実行時に使用したSkill一覧}
  └── ...

📣 Slack分報投稿:
  ├── {タスク完了時に投稿したメンバー一覧}
  └── sprint-master（スプリント完了報告）

✅ レビュー・レトロスペクティブ完了確認済み
🤔 次のアクション: コミット&pushしますか？
```

**コミット&pushの提案はこのタイミング（レトロスペクティブ完了後）で初めて行う。**

POがスプリント終了フロー外で「cp」と指示した場合も、このPhase 6の前提条件チェックを適用する。レビュー/レトロが未実施であれば「先にレビューとレトロスペクティブを実施します」と案内してPhase 2に遷移する。

---

## 可視性チェックリスト（セルフチェック用）

- [ ] レビューフェーズで sprint-review-runner を宣言・読み込みしたか
- [ ] レトロフェーズで sprint-retro を宣言・読み込みしたか
- [ ] 各Skillの振り返り視点を実際に参照したか
- [ ] メトリクスを計算・提示したか
- [ ] Try登録を try-stock.md に実施したか
- [ ] スプリントログを保存したか
- [ ] コミット&pushの提案がレトロ完了後のタイミングになっているか
- [ ] cursor-agents-skills の変更有無を確認し、結果を報告したか
- [ ] スプリント完了のSlack分報投稿をsprint-masterとして実施したか（persona/がある場合）
