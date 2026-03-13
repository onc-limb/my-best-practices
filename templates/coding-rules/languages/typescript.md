# TypeScript コーディング規約

## 基本方針

- `strict: true` を有効にする
- `any` の使用を禁止する（やむを得ない場合は `unknown` + 型ガードで対応）
- ESLint + Prettier でフォーマットを統一する

## 命名規則

| 対象 | 形式 | 例 |
|------|------|-----|
| 変数・関数 | camelCase | `getUserName` |
| 型・インターフェース | PascalCase | `UserProfile` |
| 定数 | UPPER_SNAKE_CASE | `MAX_RETRY_COUNT` |
| ファイル名 | kebab-case | `user-profile.ts` |
| テストファイル | `*.test.ts` / `*.spec.ts` | `user-profile.test.ts` |

<!-- ASSUMPTION: ファイル命名規則は kebab-case を採用。プロジェクトによっては camelCase や PascalCase の場合もある -->

## 型定義

- `interface` を基本とし、ユニオン型・交差型が必要な場合のみ `type` を使う
- 戻り値の型は明示する（推論に頼らない）
- `enum` より `as const` + ユニオン型を推奨する

<!-- ASSUMPTION: enum より as const を推奨する方針を採用。enum を好むチームもある -->

```typescript
// Good
const Status = { Active: "active", Inactive: "inactive" } as const;
type Status = (typeof Status)[keyof typeof Status];

// Avoid
enum Status {
  Active = "active",
  Inactive = "inactive",
}
```

## エラーハンドリング

- `try/catch` では具体的なエラー型で処理する
- `catch (e: unknown)` を使い、`instanceof` で型を絞る
- 非同期処理では `Promise` の reject を適切にハンドリングする

## インポート

- 相対パスよりパスエイリアス（`@/`）を使う
- `import type` で型のみのインポートを分離する
- バレルファイル（`index.ts`）は公開 API の整理にのみ使う

<!-- ASSUMPTION: パスエイリアスに @ を使用。tsconfig の paths 設定に依存する -->

## 非同期処理

- コールバックより `async/await` を使う
- `Promise.all` で並行実行できる場合は直列にしない
- タイムアウト処理を忘れない

## テスト

- テストフレームワーク: Vitest（または Jest）
- テストファイルはソースと同階層に配置する
- テスト名は日本語で「〜すること」形式にする

<!-- ASSUMPTION: テストフレームワークとして Vitest を第一候補とした。Jest を使うプロジェクトも多い -->

```typescript
describe("UserService", () => {
  it("ユーザーが存在する場合、プロフィールを返すこと", async () => {
    // Arrange, Act, Assert
  });
});
```

## 禁止事項

- `@ts-ignore` の使用（`@ts-expect-error` + コメントで代替）
- `!`（非null アサーション）の安易な使用
- `console.log` のコミット（ロガーを使う）
- 未使用の変数・インポートの放置
