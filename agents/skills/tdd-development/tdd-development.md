# TDD Development - テスト駆動開発

> 前提: `agents/prompts/base.md` を読み込み済みであること

## 目的

新しい機能を実装する際に、テストを先に書き、テストが通るように実装を進める（Red → Green → Refactor）。

## 参照ドキュメント

- `docs/coding-rules/nextjs/testing.md` - テストの書き方・配置ルール
- `docs/coding-rules/nextjs/based/testing-strategy/testing-trophy.md` - テスト戦略
- `docs/coding-rules/nextjs/implementation-patterns.md` - 実装パターン
- `docs/coding-rules/nextjs/directory-structure.md` - ファイル配置
- `docs/coding-rules/nextjs/naming-conventions.md` - 命名規則
- `docs/coding-rules/nextjs/based/coding-rules/contract-programming.md` - 事前条件・事後条件

## 手順

### Step 1: 要件の整理

実装する機能の要件を整理し、以下を明確にする:

- 入力と出力の仕様
- 正常系のケース
- 異常系・境界値のケース
- 事前条件と事後条件

### Step 2: テストファイルの作成（Red）

テスト対象と同じディレクトリに `*.test.ts(x)` ファイルを作成する。

#### テスト設計のルール

1. **AAA パターン**（Arrange-Act-Assert）で記述する
2. **テスティングトロフィーに従い、Integration テストを最も厚くする**
   - Unit: 純粋関数・ビジネスロジックのみ
   - Component: 単一コンポーネントの振る舞い
   - Integration: コンポーネントの組み合わせ・Server Actions 経由の処理
3. **モックはシステム境界のみ**（DB、外部 API）
   - 内部モジュール間のモックは使わない
   - 手動スタブを優先する
4. **テスト名は日本語でも英語でもよいが、何をテストしているか明確にする**

```typescript
// 例: features/auth/models/user.test.ts
import { describe, it, expect } from "vitest";
// import { createUser } from "./user"; // まだ実装しない

describe("createUser", () => {
  it("should create a user with valid email and name", () => {
    // Arrange
    const input = { email: "test@example.com", name: "Test User" };

    // Act
    // const user = createUser(input);

    // Assert
    // expect(user.email).toBe("test@example.com");
    // expect(user.name).toBe("Test User");
  });

  it("should throw an error when email is invalid", () => {
    // Arrange
    const input = { email: "invalid", name: "Test User" };

    // Act & Assert
    // expect(() => createUser(input)).toThrow("Invalid email");
  });
});
```

### Step 3: テストが失敗することを確認（Red の確認）

```bash
pnpm vitest run <テストファイルパス>
```

テストが失敗する（Red）ことを確認する。

### Step 4: 最小限の実装（Green）

テストが通る最小限の実装を行う。

#### 実装時のルール

- `docs/coding-rules/nextjs/implementation-patterns.md` のパターンに従う
- `docs/coding-rules/nextjs/naming-conventions.md` の命名規則に従う
- ファイルは `docs/coding-rules/nextjs/directory-structure.md` の規定に配置する
- 事前条件のバリデーションを先頭に記述する（契約プログラミング）
- 過剰な実装をしない（YAGNI）

### Step 5: テストが通ることを確認（Green の確認）

```bash
pnpm vitest run <テストファイルパス>
```

### Step 6: リファクタリング（Refactor）

テストが通った状態を維持しつつ、コードの品質を改善する:

- 重複の除去（知識の重複のみ。文字列的な重複は許容）
- 命名の改善
- 関心の分離

### Step 7: Lint / 型チェック

```bash
pnpm eslint <対象ファイル>
pnpm tsc --noEmit
```

## サイクルの繰り返し

1つのテストケースにつき Step 2〜6 を繰り返す。
一度に大量のテストを書かず、1ケースずつ Red → Green → Refactor を回す。

## 注意事項

- UI コンポーネントの Unit テストは原則書かない（Integration / Storybook で検証）
- E2E テストはこのスキルのスコープ外（クリティカルパスのみ別途作成）

<!-- TODO: テストで使用するヘルパー関数やファクトリの配置場所が未定義。プロジェクトごとに `tests/helpers/` や `tests/factories/` のようなディレクトリを設けるか検討 -->
<!-- TODO: データベースを使う Integration テストのセットアップ・ティアダウン方針が未定義（テスト用 DB、トランザクションロールバック等） -->
