# AI Agent Prompts & Skills

AI エージェントがプロジェクトのコーディングルールに従って作業するためのプロンプトとスキル定義。

## 構成

```
agents/
├── prompts/
│   └── base.md              # ベースプロンプト（全スキル共通）
└── skills/
    ├── tdd-development/      # TDD: テスト先行で実装
    ├── implement/            # 機能実装
    ├── test-writer/          # 既存コードへのテスト作成
    ├── code-review/          # コードレビュー
    ├── error-debug/          # デバッグ・修正
    ├── refactoring/          # リファクタリング
    └── rule-enforcer/        # ルール準拠チェック
```

## 使い方

1. `agents/prompts/base.md` をシステムプロンプトまたはコンテキストとして読み込ませる
2. 目的に応じたスキルファイルを追加で読み込ませる

### スキル一覧

| スキル | 用途 | 入力 | 出力 |
|--------|------|------|------|
| `tdd-development` | 新機能をテスト先行で開発 | 要件 | テスト + 実装コード |
| `implement` | コーディングルール準拠で実装 | 要件 | 実装コード |
| `test-writer` | 既存コードにテスト追加 | 対象ファイル | テストコード |
| `code-review` | 変更差分をレビュー | diff / PR | レビューコメント |
| `error-debug` | バグの原因特定と修正 | エラー情報 | 修正コード + レポート |
| `refactoring` | 振る舞いを変えずに構造改善 | 対象コード | リファクタリング済みコード |
| `rule-enforcer` | ルール違反の検出 | 対象コード | 違反レポート |

## ドキュメントパスについて

スキル内のドキュメント参照パスは、このリポジトリの `my-base/docs/` の内容がプロジェクトの `docs/` にコピーされることを前提としています。

```
my-base/docs/coding-rules/nextjs/...  →  docs/coding-rules/nextjs/...
```
