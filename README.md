# my-best-practices

個人的な開発のベストプラクティス

## ディレクトリ構成

```
.
├── templates/                          # 各種テンプレート
│   ├── coding-rules/
│   │   ├── architecture/               # アーキテクチャ規約テンプレート
│   │   └── languages/                  # 言語別コーディング規約テンプレート
│   ├── testing-strategy/               # テスト方針テンプレート
│   ├── review-checklist/               # レビュー観点テンプレート
│   └── adr/                            # ADR (Architecture Decision Record) テンプレート
│
├── standards/                          # 個人的なスタンダード（テンプレートの代表例）
│   ├── git-workflow/                   # Git 運用ルール
│   ├── dev-environment/                # 開発環境構築セット
│   └── security-checklist/             # セキュリティチェックリスト
│
└── agents/                             # AI エージェント向け定義
    ├── prompts/                        # 共通プロンプト・指示書
    └── skills/                         # エージェントスキル定義
        ├── tdd-development/            # TDD 開発エージェント
        ├── test-writer/                # 既存コードへのテスト作成エージェント
        ├── rule-enforcer/              # ルール準拠の実装エージェント
        ├── code-review/                # コードレビューエージェント
        ├── refactoring/                # リファクタリングエージェント
        └── error-debug/                # エラー解決・デバッグエージェント
```

## 各ディレクトリの説明

### templates/

プロジェクト開始時にコピーして使うテンプレート集。プロジェクトの種類や言語に合わせてカスタマイズして利用する。

| ディレクトリ | 内容 |
|---|---|
| `coding-rules/architecture/` | レイヤード、クリーンアーキテクチャ等の規約テンプレート |
| `coding-rules/languages/` | TypeScript, Go, Python 等の言語別規約テンプレート |
| `testing-strategy/` | テスト方針（テストレベル、カバレッジ目標、テストツール等） |
| `review-checklist/` | コードレビュー時の観点チェックリスト |
| `adr/` | アーキテクチャ意思決定記録のテンプレート |

### standards/

テンプレートの「自分なりの標準例」。新規プロジェクトのデフォルトとして使う。

| ディレクトリ | 内容 |
|---|---|
| `git-workflow/` | ブランチ戦略、コミットメッセージ規約、マージルール等 |
| `dev-environment/` | エディタ設定、linter/formatter、Docker 構成等 |
| `security-checklist/` | 依存関係チェック、シークレット管理、OWASP 対策等 |

### agents/

AI エージェント（Claude Code 等）に読み込ませるプロンプトとスキル定義。

| ディレクトリ | 内容 |
|---|---|
| `prompts/` | 複数スキルで共通利用するプロンプト・指示書 |
| `skills/tdd-development/` | TDD サイクル（Red→Green→Refactor）で開発を進めるエージェント |
| `skills/test-writer/` | 既存コードの仕様を確認しながらテストコードを書くエージェント |
| `skills/rule-enforcer/` | コーディング規約に従った実装を行うエージェント |
| `skills/code-review/` | コードレビューを実施するエージェント |
| `skills/refactoring/` | リファクタリングを実施するエージェント |
| `skills/error-debug/` | エラー解決・デバッグを支援するエージェント |
