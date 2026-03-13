# Python コーディング規約

## 基本方針

- PEP 8 に従う
- Ruff でリント・フォーマットを統一する
- 型ヒントを必須とする（Python 3.10+ の構文を使用）

<!-- ASSUMPTION: フォーマッターとして Ruff を採用。Black + isort を使うプロジェクトも多い -->
<!-- ASSUMPTION: Python 3.10+ を前提とした。3.9 以下では型ヒント構文が異なる -->

## 命名規則

| 対象 | 形式 | 例 |
|------|------|-----|
| 変数・関数 | snake_case | `get_user_name` |
| クラス | PascalCase | `UserProfile` |
| 定数 | UPPER_SNAKE_CASE | `MAX_RETRY_COUNT` |
| プライベート | `_` プレフィックス | `_validate_input` |
| ファイル名 | snake_case | `user_profile.py` |
| テストファイル | `test_*.py` | `test_user_profile.py` |

## 型ヒント

- 関数の引数と戻り値には必ず型を付ける
- `Any` の使用を避ける
- `Union` より `X | Y` 構文を使う
- コレクションは `list[str]`, `dict[str, int]` のように具体的に書く

```python
def get_user(user_id: str) -> User | None:
    """ユーザーを取得する。存在しなければ None を返す。"""
    ...
```

## エラーハンドリング

- 裸の `except:` / `except Exception:` を避け、具体的な例外を捕捉する
- カスタム例外は `Exception` を継承し、ドメイン固有の名前を付ける
- `try` ブロックは最小限にする

```python
class UserNotFoundError(Exception):
    """ユーザーが見つからない場合のエラー。"""
    pass

def get_user(user_id: str) -> User:
    user = repo.find(user_id)
    if user is None:
        raise UserNotFoundError(f"User {user_id} not found")
    return user
```

## プロジェクト構成

- パッケージマネージャ: uv（または Poetry）
- `src/` レイアウトを推奨する
- `__init__.py` は公開 API の定義にのみ使う

<!-- ASSUMPTION: uv を第一候補とした。Poetry や pip を使うプロジェクトも多い -->

## インポート

- 標準ライブラリ → サードパーティ → ローカルの順に並べる
- ワイルドカードインポート（`from module import *`）を禁止する
- 循環インポートを避ける

## テスト

- テストフレームワーク: pytest
- フィクスチャを活用し、テスト間の重複を減らす
- テスト名は日本語コメントまたは `test_` + 説明的な名前にする

<!-- ASSUMPTION: pytest を採用。unittest を使うプロジェクトもある -->

```python
class TestGetUser:
    def test_存在するユーザーを取得できる(self, user_repo: UserRepo) -> None:
        user = get_user("user-1")
        assert user.id == "user-1"

    def test_存在しないユーザーでエラーが発生する(self) -> None:
        with pytest.raises(UserNotFoundError):
            get_user("unknown")
```

## 禁止事項

- `type: ignore` の安易な使用（根本原因を修正する）
- ミュータブルなデフォルト引数（`def f(items=[])`）
- グローバル状態への依存
- `print` デバッグのコミット（`logging` を使う）
