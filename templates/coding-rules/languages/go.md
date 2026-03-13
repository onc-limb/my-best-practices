# Go コーディング規約

## 基本方針

- 公式スタイルガイド（Effective Go）に従う
- `gofmt` / `goimports` でフォーマットを統一する
- `golangci-lint` で静的解析を行う

## 命名規則

| 対象 | 形式 | 例 |
|------|------|-----|
| パッケージ名 | 小文字・単数形 | `user`, `auth` |
| エクスポート関数/型 | PascalCase | `GetUser`, `UserService` |
| 非エクスポート関数/変数 | camelCase | `getUserByID` |
| 定数 | PascalCase（エクスポート）/ camelCase | `MaxRetryCount` |
| インターフェース | `-er` サフィックス推奨 | `Reader`, `Validator` |
| ファイル名 | snake_case | `user_service.go` |
| テストファイル | `*_test.go` | `user_service_test.go` |

## エラーハンドリング

- エラーは必ずチェックする（`_ =` で握りつぶさない）
- `errors.New` / `fmt.Errorf` でエラーを生成する
- エラーのラップには `%w` を使う
- センチネルエラーには `Err` プレフィックスを付ける

```go
var ErrUserNotFound = errors.New("user not found")

func GetUser(id string) (*User, error) {
    user, err := repo.Find(id)
    if err != nil {
        return nil, fmt.Errorf("get user %s: %w", id, err)
    }
    return user, nil
}
```

## パッケージ設計

- パッケージは単一責務にする
- 循環依存を避ける
- `internal/` で非公開パッケージを整理する
- `util` / `common` / `helper` パッケージを作らない

<!-- ASSUMPTION: util パッケージを禁止する方針を採用。小規模プロジェクトでは許容される場合もある -->

## インターフェース

- インターフェースは使う側で定義する（実装側ではない）
- 小さいインターフェースを好む（1〜3 メソッド）
- 不要な抽象化を避ける

## 並行処理

- goroutine のリークに注意する（`context` でキャンセルを伝搬）
- チャネルよりも `sync.WaitGroup` / `errgroup` を優先する場面を見極める
- 共有状態には `sync.Mutex` またはチャネルで保護する

<!-- ASSUMPTION: errgroup を推奨。プロジェクトの並行処理パターンに依存する -->

## テスト

- テーブル駆動テストを基本とする
- テストヘルパーには `t.Helper()` を付ける
- モックは `interface` を使って差し替える

```go
func TestGetUser(t *testing.T) {
    tests := []struct {
        name    string
        id      string
        want    *User
        wantErr bool
    }{
        {"正常系", "user-1", &User{ID: "user-1"}, false},
        {"存在しないユーザー", "unknown", nil, true},
    }
    for _, tt := range tests {
        t.Run(tt.name, func(t *testing.T) {
            got, err := GetUser(tt.id)
            if (err != nil) != tt.wantErr {
                t.Errorf("error = %v, wantErr %v", err, tt.wantErr)
            }
            // ...
        })
    }
}
```

## 禁止事項

- `init()` 関数の多用（テストが困難になる）
- グローバル変数への依存
- `panic` を通常のエラー処理に使う
- `interface{}` / `any` の安易な使用
