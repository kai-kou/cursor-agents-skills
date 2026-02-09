# ~/.cursor 配下の Git リポジトリ管理 要件定義書

**作成日**: 2026年2月5日  
**ステータス**: 調査完了・実装可能

---

## 1. 概要

### 1.1 目的
`~/.cursor` 配下の agents および skills フォルダを GitHub リポジトリで管理し、設定の可搬性・バックアップ・バージョン管理を実現する。

### 1.2 現状調査結果

#### ~/.cursor の構造
```
~/.cursor/
├── agents/              # カスタムサブエージェント定義 ★管理対象
├── skills/              # ユーザー独自スキル ★管理対象
├── skills-cursor/       # Cursor公式スキル（更新される可能性あり）
├── mcp.json             # MCPサーバー設定
├── argv.json            # VS Code起動引数（変更非推奨）
├── ide_state.json       # IDE状態（動的に変化）
├── unified_repo_list.json
├── blocklist
├── projects/            # プロジェクト固有キャッシュ
├── extensions/          # 拡張機能
├── browser-logs/        # ブラウザログ
├── ai-tracking/         # AI追跡データベース
└── worktrees/           # ワークツリー
```

#### agents フォルダの現状
- **既にリポジトリ化済み**: `git@github.com:kai-kou/cursor-agents.git`
- **リモートにプッシュ済み**: `origin/main` から1コミット先行
- **未コミットの変更あり**: `README.md` 修正、新規ファイル追加

---

## 2. 調査結果と推奨事項

### 2.1 .cursor を直接リポジトリ化すべきか？

**結論: 避けるべき（非推奨）**

#### 理由
| ファイル/フォルダ | 性質 | リポジトリ管理 |
|---|---|---|
| `ide_state.json` | 動的に変化（最近開いたファイル等） | ❌ 不適切 |
| `argv.json` | マシン固有のクラッシュレポートID含む | ❌ 不適切 |
| `projects/` | プロジェクト固有キャッシュ・大容量 | ❌ 不適切 |
| `extensions/` | 拡張機能バイナリ | ❌ 不適切 |
| `browser-logs/` | 一時ログ | ❌ 不適切 |
| `ai-tracking/` | SQLiteデータベース | ❌ 不適切 |
| `mcp.json` | 環境依存の可能性・APIキー含む場合あり | ⚠️ 要検討 |
| `agents/` | ユーザー定義サブエージェント | ✅ 適切 |
| `skills/` | ユーザー独自スキル | ✅ 適切 |
| `skills-cursor/` | 公式スキル（Cursor更新で変更される可能性） | ⚠️ 要検討 |

### 2.2 シンボリックリンクは使用可能か？

**結論: 現時点では非推奨**

#### 現状の制限
- **Cursor の Agent Skills 機能はシンボリックリンクを認識しない**（既知の制限事項）
- Cursor Forum で機能リクエストとして議論中
- 現状ではハードコピーが必要

#### 代替アプローチ
1. **直接リポジトリ化**（推奨）
   - `~/.cursor/agents/` と `~/.cursor/skills/` を直接 Git 管理
   
2. **同期スクリプト**（次善策）
   - 別ディレクトリでリポジトリ管理
   - rsync 等で定期同期

### 2.3 リポジトリ管理すべきフォルダ

| フォルダ | 推奨 | 備考 |
|---|---|---|
| `agents/` | ✅ 強く推奨 | カスタムサブエージェント定義 |
| `skills/` | ✅ 強く推奨 | ユーザー独自スキル |
| `skills-cursor/` | ⚠️ 条件付き | Cursor更新で上書きされる可能性あり。カスタマイズする場合は別リポジトリで管理 |
| `mcp.json` | ⚠️ 個別管理 | APIキー等の機密情報を含む場合は除外 |

---

## 3. 実装計画

### 3.1 agents フォルダのリポジトリ化を取り消す手順

既存の `kai-kou/cursor-agents.git` リポジトリを無かったことにする場合：

#### 3.1.1 ローカルのみ削除（リモートは維持）
```bash
# .git ディレクトリを削除
rm -rf ~/.cursor/agents/.git

# .gitignore も削除する場合
rm ~/.cursor/agents/.gitignore
```

#### 3.1.2 リモートリポジトリも削除する場合
1. GitHub で `kai-kou/cursor-agents` リポジトリを削除
2. ローカルの `.git` を削除

#### 3.1.3 注意事項
- `.git` 削除後、コミット履歴は完全に失われる
- リモートにプッシュ済みのコミットはリモート削除まで残る
- ファイル自体は削除されない（agents/, skills/ 内のファイルは保持）

### 3.2 推奨構成案

#### 案1: 統合リポジトリ（推奨）
agents と skills を1つのリポジトリで管理

```
cursor-config/
├── agents/
│   ├── document-review/
│   ├── pre-push-review/
│   ├── slide-generator/
│   └── *.md
├── skills/
│   ├── infographic-generator/
│   └── resume-screening/
├── .gitignore
└── README.md
```

**メリット**:
- 一元管理で見通しが良い
- 関連する変更を同時にコミット可能

**デメリット**:
- シンボリックリンクが使えないため、同期が必要

#### 案2: 個別リポジトリ（現状維持を改善）
```
cursor-agents/   # 既存リポジトリを活用
cursor-skills/   # 新規作成
```

**メリット**:
- 既存の `cursor-agents` リポジトリを活用可能
- 独立して管理・共有可能

### 3.3 同期スクリプト（シンボリックリンクの代替）

シンボリックリンクが使えないため、リポジトリとの同期スクリプトを用意：

```bash
#!/bin/bash
# sync-cursor-config.sh

REPO_DIR="$HOME/dev/cursor-config"
CURSOR_DIR="$HOME/.cursor"

# agents 同期
rsync -av --delete "$REPO_DIR/agents/" "$CURSOR_DIR/agents/"

# skills 同期
rsync -av --delete "$REPO_DIR/skills/" "$CURSOR_DIR/skills/"

echo "同期完了"
```

---

## 4. 実装手順（詳細）

### 4.1 現状を維持して改善する場合

1. **未コミットの変更をコミット**
   ```bash
   cd ~/.cursor/agents
   git add .
   git commit -m "feat: 最新の変更を追加"
   git push
   ```

2. **skills を同じリポジトリに追加**（任意）
   ```bash
   # agents リポジトリに skills を追加する場合
   cp -r ~/.cursor/skills/* ~/.cursor/agents/skills/
   ```

### 4.2 新規構成で始める場合

1. **既存リポジトリを削除**
   ```bash
   rm -rf ~/.cursor/agents/.git
   rm ~/.cursor/agents/.gitignore
   ```

2. **新規リポジトリを作成**（ユーザーがGitHubで作成）

3. **ローカルで初期化**
   ```bash
   mkdir -p ~/dev/cursor-config
   cd ~/dev/cursor-config
   git init
   
   # agents をコピー
   cp -r ~/.cursor/agents/* ./agents/
   
   # skills をコピー
   cp -r ~/.cursor/skills/* ./skills/
   
   # .gitignore 作成
   cat > .gitignore << 'EOF'
   .DS_Store
   *.log
   *.tmp
   .env
   EOF
   
   git add .
   git commit -m "Initial commit: Cursor agents and skills"
   ```

4. **リモートに接続してプッシュ**
   ```bash
   git remote add origin git@github.com:YOUR_USERNAME/cursor-config.git
   git push -u origin main
   ```

5. **同期スクリプトを設定**
   ```bash
   # ~/.cursor に同期
   rsync -av ~/dev/cursor-config/agents/ ~/.cursor/agents/
   rsync -av ~/dev/cursor-config/skills/ ~/.cursor/skills/
   ```

---

## 5. 今後の考慮事項

### 5.1 Cursor アップデートへの対応
- `skills-cursor/` は Cursor 更新時に変更される可能性
- 公式スキルをカスタマイズする場合は別フォルダで管理推奨

### 5.2 複数マシン間の同期
- GitHub を介して agents/skills を共有可能
- 同期スクリプトを各マシンで実行

### 5.3 mcp.json の取り扱い
- APIキー等の機密情報が含まれる場合は `.gitignore` に追加
- または環境変数で管理するパターンを検討

### 5.4 シンボリックリンク対応待ち
- Cursor 公式がシンボリックリンク対応を実装した場合
- 同期スクリプトからシンボリックリンクに移行可能

---

## 6. 結論と推奨アクション

### 即座に実行可能なアクション

| アクション | 優先度 | 備考 |
|---|---|---|
| 既存 agents リポジトリを活用継続 | 高 | 既に設定済み。未コミット分をプッシュ |
| skills を別リポジトリで管理開始 | 高 | ユーザーがリモートリポジトリを作成後に対応 |
| agents リポジトリを削除して再構成 | 中 | 統合リポジトリにしたい場合 |
| 同期スクリプトの作成 | 低 | シンボリックリンク代替として必要な場合 |

### 推奨構成
**案1: 統合リポジトリ**を推奨。agents と skills を1つのリポジトリで管理することで、関連する変更を一元管理でき、メンテナンスコストを低減できる。

---

## 7. 参考情報

- [Cursor Forum: Agent Skills and Symlinks](https://forum.cursor.com/t/agent-skills-must-see-symlinks/150093)
- [Cursor Docs: MCP Configuration](https://cursor.com/docs/context/mcp)
- [Cursor Docs: Ignore Files](https://docs.cursor.sh/context/ignore-files)
