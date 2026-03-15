# Refactoring - リファクタリング

> 前提: `agents/prompts/base.md` を読み込み済みであること

## 目的

外部から見た振る舞いを変えずに、コードの内部構造を改善する。

## 参照ドキュメント

- `docs/coding-rules/nextjs/based/coding-rules/core-philosophy.md` - DRY / YAGNI / KISS
- `docs/coding-rules/nextjs/based/coding-rules/solid-principle.md` - SOLID 原則
- `docs/coding-rules/nextjs/based/coding-rules/clean-architecture.md` - 依存方向
- `docs/coding-rules/nextjs/based/coding-rules/feature-based.md` - 機能ベース構成
- `docs/coding-rules/nextjs/directory-structure.md` - ディレクトリ構成
- `docs/coding-rules/nextjs/naming-conventions.md` - 命名規則
- `docs/coding-rules/nextjs/implementation-patterns.md` - 実装パターン
- `docs/coding-rules/nextjs/testing.md` - テスト方針

## 前提条件

リファクタリングを開始する前に、以下を確認する:

- [ ] 対象コードに十分なテストが存在する（なければ先にテストを書く）
- [ ] 全テストが通る状態である
- [ ] リファクタリングの目的が明確である

## 手順

### Step 1: 対象コードの分析

改善すべき問題を特定する:

#### コードスメルの検出

| コードスメル | 対応する原則 | 改善方向 |
|------------|-------------|---------|
| 巨大な関数/コンポーネント | SRP | 責務ごとに分割 |
| 知識の重複 | DRY | 共通の知識を1箇所に集約 |
| 使われていない抽象化 | YAGNI | 削除 |
| 複雑な条件分岐 | KISS | 早期リターン、ガード節 |
| feature 間の循環依存 | Feature-based | 依存方向の整理、共通化 |
| Domain 層のフレームワーク依存 | Clean Architecture | Port/Adapter パターンの導入 |
| 肥大化したインターフェース | ISP | インターフェースの分割 |
| `app/` 内のビジネスロジック | Feature-based | `features/` へ移動 |
| バレルファイルの存在 | プロジェクト規約 | 削除し直接 import に変更 |
| `process.env` 直接参照 | 実装パターン | `lib/env.ts` 経由に変更 |

### Step 2: リファクタリング計画

1. 改善する問題を1つずつ選ぶ（複数の問題を同時に直さない）
2. 変更後の状態を明確にイメージする
3. 影響範囲を確認する

### Step 3: リファクタリングの実施

#### 基本原則

- **小さなステップで進める** - 大きな変更を一度にやらない
- **各ステップでテストを実行する** - 壊れたらすぐ戻す
- **機能変更を混ぜない** - リファクタリングのみ
- **不要なコードは完全に削除する** - `_unused` 変数や `// removed` コメントは残さない

#### よく使うリファクタリングパターン

##### Extract Function

```typescript
// Before
function processOrder(order: Order) {
  // バリデーション（20行）
  // 計算処理（15行）
  // 保存処理（10行）
}

// After
function processOrder(order: Order) {
  validateOrder(order);
  const total = calculateTotal(order);
  saveOrder(order, total);
}
```

##### Move to Feature

```typescript
// Before: app/dashboard/page.tsx にビジネスロジックがある
// After: features/dashboard/ に移動し、page.tsx は組み立てのみ
```

##### Introduce Port/Adapter

```typescript
// Before: Domain 層が直接 DB クライアントを import
// After:
//   features/orders/repositories/order-repository.ts (Port = interface)
//   lib/repositories/drizzle-order-repository.ts (Adapter = 実装)
```

##### Replace Conditional with Guard Clause

```typescript
// Before
function getDiscount(user: User) {
  if (user.isPremium) {
    if (user.years > 5) {
      return 0.2;
    } else {
      return 0.1;
    }
  } else {
    return 0;
  }
}

// After
function getDiscount(user: User) {
  if (!user.isPremium) return 0;
  if (user.years > 5) return 0.2;
  return 0.1;
}
```

### Step 4: テスト実行

```bash
# 全テストが通ることを確認
pnpm vitest run

# Lint / 型チェック
pnpm eslint <対象ファイル>
pnpm tsc --noEmit
```

### Step 5: 変更のレビュー

リファクタリング完了後、自己レビューを行う:

- [ ] 外部から見た振る舞いが変わっていないか
- [ ] テストが全て通るか
- [ ] コーディングルールに準拠しているか
- [ ] 不要な変更（フォーマットだけの差分等）が含まれていないか
- [ ] コミットメッセージは `refactor:` プレフィックスか

## 注意事項

- テストがない状態でリファクタリングしない（先に `test-writer` スキルでテストを作成する）
- リファクタリングのコミットに機能変更を混ぜない
- 「ついでに」の改善を入れない - スコープを守る

<!-- TODO: リファクタリングの優先度判断基準（技術的負債の定量化）が未定義 -->
<!-- TODO: 大規模リファクタリング（feature の分割・統合等）の段階的移行手順が未定義 -->
