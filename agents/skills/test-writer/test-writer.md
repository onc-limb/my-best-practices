# Test Writer - 既存コードへのテスト作成

> 前提: `agents/prompts/base.md` を読み込み済みであること

## 目的

既存の実装コードに対して、テスティングトロフィーに基づいたテストを作成する。

## 参照ドキュメント

- `docs/coding-rules/nextjs/testing.md` - テストの書き方・配置・モック方針
- `docs/coding-rules/nextjs/based/testing-strategy/testing-trophy.md` - テスト戦略
- `docs/coding-rules/nextjs/based/coding-rules/contract-programming.md` - 事前条件・事後条件

## 手順

### Step 1: 対象コードの分析

テスト対象のコードを読み込み、以下を把握する:

1. **関数/コンポーネントの責務** - 何をしているか
2. **入出力** - 引数、戻り値、副作用
3. **事前条件** - バリデーション、型制約
4. **依存関係** - 外部モジュール、DB、API との結合点
5. **エッジケース** - 境界値、null/undefined、空配列、空文字

### Step 2: テスト種別の判断

テスティングトロフィーに基づき、対象コードに適したテスト種別を選択する:

| 対象 | テスト種別 | ツール |
|------|----------|--------|
| 純粋関数（値オブジェクト、ビジネスロジック） | Unit | Vitest |
| 単一コンポーネント | Component | Vitest + React Testing Library |
| 複数コンポーネントの連携・Server Actions 経由の処理 | Integration | Vitest + React Testing Library |
| ビジュアル確認が必要なコンポーネント | Visual | Storybook |
| クリティカルパスの End-to-End | E2E | Playwright |

**優先度: Integration > Unit > Component > E2E**

UI コンポーネントの Unit テストは原則書かない。

### Step 3: テストケースの設計

#### 3-1. 正常系

- 代表的な入力で期待通りの出力が得られるか
- 複数の有効なパターン（パラメタライズドテスト検討）

#### 3-2. 異常系

- 無効な入力でエラーが適切に返されるか
- 事前条件違反時の挙動

#### 3-3. 境界値

- 最小値・最大値
- 空文字列・空配列
- null / undefined（TypeScript strict で型レベルで防いでいる場合は省略可）

### Step 4: テストの実装

#### ファイル配置

対象ファイルと同じディレクトリに配置:

```
features/orders/models/order.ts
features/orders/models/order.test.ts  ← ここ
```

#### 記述ルール

1. **AAA パターン**（Arrange-Act-Assert）
2. **1テスト1アサーション**を基本とする（関連する複数アサーションは許容）
3. **テスト間の独立性を保つ**（共有状態を作らない）
4. **モックはシステム境界のみ**

```typescript
import { describe, it, expect, vi } from "vitest";
import { calculateTotal } from "./calculate-total";

describe("calculateTotal", () => {
  it("should return the sum of item prices multiplied by quantities", () => {
    // Arrange
    const items = [
      { price: 100, quantity: 2 },
      { price: 200, quantity: 1 },
    ];

    // Act
    const result = calculateTotal(items);

    // Assert
    expect(result).toBe(400);
  });

  it("should return 0 for an empty items array", () => {
    // Arrange & Act
    const result = calculateTotal([]);

    // Assert
    expect(result).toBe(0);
  });

  it("should throw when quantity is negative", () => {
    // Arrange
    const items = [{ price: 100, quantity: -1 }];

    // Act & Assert
    expect(() => calculateTotal(items)).toThrow();
  });
});
```

### Step 5: テスト実行

```bash
pnpm vitest run <テストファイルパス>
```

すべてのテストが通ることを確認する。

### Step 6: カバレッジ確認

```bash
pnpm vitest run --coverage <テストファイルパス>
```

不足しているパスがあればテストケースを追加する。

## 注意事項

- 既存の実装コードは変更しない（テストを書くことが目的）
- テストで実装の問題が見つかった場合は、テストコード内にコメントで記録し、別途修正する
- Storybook のストーリーもテストの一部として考慮する

<!-- TODO: テストデータのファクトリパターン（fixture / factory）の標準的な実装方法が未定義 -->
<!-- TODO: MSW (Mock Service Worker) 等の API モックツールの利用方針が未定義 -->
<!-- TODO: Storybook の play function を活用した Interaction テストの方針が未定義 -->
