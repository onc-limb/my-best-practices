# Error Debug - デバッグ・修正

> 前提: `agents/prompts/base.md` を読み込み済みであること

## 目的

エラーやバグの原因を特定し、コーディングルールに準拠した形で修正する。

## 参照ドキュメント

- `docs/coding-rules/nextjs/implementation-patterns.md` - 実装パターン（エラーハンドリング）
- `docs/coding-rules/nextjs/based/coding-rules/contract-programming.md` - 事前条件・事後条件
- `docs/coding-rules/nextjs/testing.md` - テストの書き方
- `docs/coding-rules/nextjs/directory-structure.md` - ファイル配置
- `docs/security-checklist/03-input-validation.md` - 入力バリデーション

## 手順

### Step 1: 問題の把握

以下の情報を収集する:

1. **エラーメッセージ** - 正確なエラー内容
2. **再現手順** - どの操作で発生するか
3. **期待される動作** - 本来どうなるべきか
4. **実際の動作** - 何が起きているか
5. **発生環境** - 開発/ステージング/本番、ブラウザ等

### Step 2: 原因の特定

#### 2-1. エラーログの確認

```bash
# サーバーサイドのログ
# ブラウザの DevTools Console
# ターミナルの出力
```

#### 2-2. 関連コードの調査

エラーの発生箇所から呼び出し元をたどり、根本原因を特定する:

1. エラーが発生しているファイル・行を特定
2. そのコードの入力値を確認（事前条件の違反がないか）
3. 依存先のコードに問題がないか確認
4. 最近の変更（`git log`, `git blame`）を確認

#### 2-3. 原因の分類

| 分類 | 例 |
|------|-----|
| 事前条件の未検証 | 入力バリデーションの漏れ |
| 型の不整合 | API レスポンスと型定義のずれ |
| 状態管理の不備 | 競合状態、古いステートの参照 |
| 依存関係の問題 | パッケージのバージョン不整合 |
| 環境の問題 | 環境変数の未設定、権限不足 |
| ロジックエラー | 条件分岐の誤り、計算ミス |

### Step 3: 修正方針の決定

修正方針を決定する際の優先順位:

1. **根本原因を修正する**（ワークアラウンドではなく）
2. **影響範囲を最小限にする**
3. **既存のテストを壊さない**
4. **コーディングルールに準拠する**

### Step 4: テストの作成（修正前）

バグを再現するテストを先に書く（回帰テスト）:

```typescript
it("should handle empty response from API without crashing", () => {
  // このテストは修正前は失敗する（バグの再現）
  // Arrange
  const emptyResponse = { data: null };

  // Act & Assert
  expect(() => processResponse(emptyResponse)).not.toThrow();
});
```

### Step 5: 修正の実装

#### エラーハンドリングのルール（`implementation-patterns.md` 準拠）

| レイヤー | エラー処理方式 |
|---------|--------------|
| Domain 層 | 例外を throw する |
| Server Actions | Result 型（`{ success, data, error }`）で返す |
| Server Components | `error.tsx` で処理する |
| Client Components | Error Boundary / try-catch |

#### 修正時のチェックポイント

- [ ] 事前条件のバリデーションを追加したか
- [ ] エラーメッセージに違反した条件を明記したか
- [ ] 推測で修正した箇所に `// ASSUMPTION:` コメントを残したか

### Step 6: テストの実行

```bash
# 回帰テストが通ることを確認
pnpm vitest run <テストファイルパス>

# 既存テストが壊れていないことを確認
pnpm vitest run
```

### Step 7: Lint / 型チェック

```bash
pnpm eslint <対象ファイル>
pnpm tsc --noEmit
```

## レポート出力

修正完了後、以下の形式で報告する:

```markdown
## デバッグレポート

### 問題
- エラー: ...
- 発生箇所: ...

### 原因
- 根本原因: ...
- 分類: ...

### 修正内容
- 変更ファイル: ...
- 修正方法: ...

### テスト
- 回帰テスト: 追加済み / 既存テストでカバー
- 全テスト: Pass / Fail
```

## 注意事項

- バグ修正とリファクタリングは別コミットにする
- 修正に関係のないコードは変更しない
- セキュリティに関わるバグは優先度を上げる

<!-- TODO: エラー監視サービス（Sentry 等）との連携方針が未定義 -->
<!-- TODO: 本番環境でのデバッグ手順（ログレベル変更、Feature Flag による切り戻し等）が未定義 -->
