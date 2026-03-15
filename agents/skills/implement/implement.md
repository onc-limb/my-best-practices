# Implement - 機能実装

> 前提: `agents/prompts/base.md` を読み込み済みであること

## 目的

コーディングルールに準拠した形で新機能を実装する。

## 参照ドキュメント

- `docs/coding-rules/nextjs/README.md` - 技術スタック概要
- `docs/coding-rules/nextjs/directory-structure.md` - ファイル配置
- `docs/coding-rules/nextjs/naming-conventions.md` - 命名規則
- `docs/coding-rules/nextjs/implementation-patterns.md` - 実装パターン
- `docs/coding-rules/nextjs/based/coding-rules/clean-architecture.md` - 依存方向
- `docs/coding-rules/nextjs/based/coding-rules/contract-programming.md` - 契約プログラミング
- `docs/coding-rules/nextjs/based/coding-rules/domain-model.md` - DDD パターン
- `docs/coding-rules/nextjs/based/coding-rules/feature-based.md` - 機能ベース構成
- `docs/coding-rules/nextjs/based/coding-rules/core-philosophy.md` - DRY / YAGNI / KISS
- `docs/coding-rules/nextjs/based/coding-rules/solid-principle.md` - SOLID 原則
- `docs/security-checklist/README.md` - セキュリティチェックリスト

## 手順

### Step 1: 要件の理解

実装する機能の要件を明確にする:

- 何を実装するか（機能の説明）
- 入出力の仕様
- 関連する既存コードの把握

### Step 2: 設計

#### 2-1. レイヤーの特定

実装する機能が属するレイヤーを特定する:

| レイヤー | 配置先 | 責務 |
|---------|--------|------|
| Domain | `features/*/models/` | エンティティ・値オブジェクト・ビジネスルール |
| UseCase / Actions | `features/*/actions/` | Server Actions（ミューテーション） |
| Repository Port | `features/*/repositories/` | Repository インターフェース |
| Repository Adapter | `lib/repositories/` | Repository 実装 |
| UI (Server) | `app/` + `features/*/components/` | Server Components |
| UI (Client) | `features/*/components/` | Client Components（`"use client"`） |
| API | `app/api/` | Route Handlers |
| Shared | `components/`, `lib/` | 共通 UI・ユーティリティ |

#### 2-2. ファイル構成の決定

- feature ディレクトリの新規作成が必要か、既存 feature に追加か
- バレルファイル（`index.ts`）は作成しない
- 1ファイル1エクスポートを基本とする

### Step 3: 実装

#### Domain 層

```typescript
// features/orders/models/order.ts

// 値オブジェクト: 不変、生成時バリデーション
interface OrderId {
  readonly value: string;
}

function createOrderId(value: string): OrderId {
  // 事前条件
  if (!value || value.trim() === "") {
    throw new Error("OrderId must not be empty");
  }
  return { value };
}
```

#### Server Actions

```typescript
// features/orders/actions/create-order-action.ts
"use server";

import { Type } from "@sinclair/typebox";
import { Value } from "@sinclair/typebox/value";

const CreateOrderSchema = Type.Object({
  productId: Type.String(),
  quantity: Type.Number({ minimum: 1 }),
});

export async function createOrderAction(formData: FormData) {
  // 事前条件: TypeBox でバリデーション
  const raw = Object.fromEntries(formData);
  if (!Value.Check(CreateOrderSchema, raw)) {
    return { success: false, error: "Invalid input" };
  }

  // ビジネスロジック呼び出し
  // ...

  // Result 型で返す（Server Actions は例外を throw しない）
  return { success: true, data: /* ... */ };
}
```

#### Server / Client Components

```typescript
// デフォルトは Server Component（"use client" を書かない）
// useState, useEffect, イベントハンドラが必要な場合のみ "use client"
```

### Step 4: セキュリティチェック

- [ ] 入力バリデーションを行っているか
- [ ] SQL インジェクション対策（パラメータ化クエリ）
- [ ] XSS 対策（ユーザー入力のサニタイズ）
- [ ] 認証・認可チェック（Route Handler では個別チェック）
- [ ] 機密情報がハードコードされていないか
- [ ] 環境変数は `lib/env.ts` 経由か

### Step 5: Lint / 型チェック

```bash
pnpm eslint <対象ファイル>
pnpm tsc --noEmit
```

### Step 6: 動作確認

```bash
pnpm dev
# ブラウザで動作確認
```

## 注意事項

- 推測で実装した箇所には `// ASSUMPTION:` コメントを残す
- 機能変更とリファクタリングのコミットは分ける
- `process.env` を直接参照しない（`lib/env.ts` を使う）
- Middleware での一括認証ではなく、各エンドポイントで個別チェック

<!-- TODO: エラーハンドリングの統一パターンが Domain 層（throw）と Server Actions（Result 型）で分かれている。UseCase 層でのエラー変換方針を明確化する必要あり -->
<!-- TODO: DI の具体的な実装例（関数ベース注入のパターン）が不足。Repository の注入方法を具体化する必要あり -->
<!-- TODO: TanStack Query のキー管理・キャッシュ戦略が未定義 -->
