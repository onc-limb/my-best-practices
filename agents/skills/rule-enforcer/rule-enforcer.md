# Rule Enforcer - ルール準拠チェック

> 前提: `agents/prompts/base.md` を読み込み済みであること

## 目的

コードがプロジェクトのコーディングルールに準拠しているかを検証し、違反箇所を指摘する。

## 参照ドキュメント

- `docs/coding-rules/nextjs/directory-structure.md`
- `docs/coding-rules/nextjs/naming-conventions.md`
- `docs/coding-rules/nextjs/implementation-patterns.md`
- `docs/coding-rules/nextjs/based/coding-rules/core-philosophy.md`
- `docs/coding-rules/nextjs/based/coding-rules/solid-principle.md`
- `docs/coding-rules/nextjs/based/coding-rules/clean-architecture.md`
- `docs/coding-rules/nextjs/based/coding-rules/feature-based.md`

## 手順

### Step 1: ルールの読み込み

上記の参照ドキュメントをすべて読み込み、チェック項目を頭に入れる。

### Step 2: 対象コードの分析

指定されたファイルまたはディレクトリのコードを読み込む。

### Step 3: ルール違反の検出

以下の観点でチェックする:

#### ディレクトリ構成

- [ ] `app/` にビジネスロジックが含まれていないか
- [ ] `features/` の分割がビジネス上の関心に基づいているか
- [ ] `lib/repositories/` に Repository 実装が配置されているか
- [ ] バレルファイル（`index.ts`）が存在しないか

#### 命名規則

- [ ] コンポーネントファイルは kebab-case か
- [ ] hooks は `use-` prefix + kebab-case か
- [ ] Server Actions は `-action` suffix か
- [ ] 変数・関数は camelCase、型は PascalCase、定数は UPPER_SNAKE_CASE か

#### 依存方向

- [ ] Domain 層がフレームワーク・ライブラリを import していないか
- [ ] feature 間の循環依存がないか
- [ ] 依存の方向が外側から内側になっているか

#### 実装パターン

- [ ] Server Component がデフォルトで使われているか（`"use client"` の不要な使用がないか）
- [ ] Server Actions がミューテーション専用になっているか
- [ ] 環境変数が `lib/env.ts` で一元管理されているか（`process.env` 直接参照がないか）
- [ ] DI が関数ベースで行われているか（クラスベース DI コンテナを使っていないか）

#### 基礎原則

- [ ] 不要な抽象化がないか（YAGNI）
- [ ] 知識の重複がないか（DRY - コードの文字列的重複ではなく知識の重複）
- [ ] 単一責任の原則が守られているか（SRP）

### Step 4: レポート出力

以下の形式で結果を報告する:

```markdown
## ルール準拠チェック結果

### 違反あり

| # | ファイル | 行 | 違反ルール | 内容 | 修正案 |
|---|---------|-----|----------|------|--------|
| 1 | ... | ... | ... | ... | ... |

### 準拠確認済み

- [x] ディレクトリ構成
- [x] 命名規則
- ...
```

## 注意事項

<!-- TODO: プロジェクト固有の例外ルールがあれば追記 -->
- ルールに明示されていないケースは指摘せず、コメントとして補足に留める
- 自動修正は行わない（修正は別スキルで実施）
