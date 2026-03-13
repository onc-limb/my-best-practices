# クリーンアーキテクチャ規約

## 概要

依存性逆転の原則を中心に据え、ビジネスロジックを外部技術から完全に独立させる。

## 同心円構造

<!-- ASSUMPTION: Uncle Bob の原典に基づく4層構成を採用 -->

```
外側 ←―――――――――――――――→ 内側
Frameworks & Drivers → Interface Adapters → Use Cases → Entities
```

依存は**常に外側から内側**に向かう。内側は外側の存在を知らない。

## 各層の責務

| 層 | 責務 | 具体例 |
|----|------|--------|
| Entities | ビジネスルールの核心 | エンティティ、値オブジェクト、ドメインサービス |
| Use Cases | アプリケーション固有のビジネスルール | ユースケースインタラクター |
| Interface Adapters | データ形式の変換 | コントローラ、プレゼンター、ゲートウェイ |
| Frameworks & Drivers | 外部ツール・フレームワーク | Web フレームワーク、DB、UI |

## ディレクトリ構成例

<!-- ASSUMPTION: 言語非依存の一般的構成。実際のプロジェクトでは言語慣習に合わせる -->

```
src/
├── entities/           # ドメインモデル、ビジネスルール
├── use-cases/          # アプリケーションロジック
├── interface-adapters/
│   ├── controllers/    # 入力の変換
│   ├── presenters/     # 出力の変換
│   └── gateways/       # データアクセスの抽象
└── frameworks/
    ├── web/            # Webフレームワーク設定
    └── db/             # DB接続・マイグレーション
```

## 依存ルール

- 内側の層は外側の層を import しない
- 層の境界にはインターフェース（ポート）を定義する
- 外側の層がインターフェースを実装する（依存性逆転）
- データは境界を越える際に DTO で受け渡す

## 境界の越え方

```
Controller → Use Case Input Port → Use Case Interactor
                                         ↓
Presenter ← Use Case Output Port ← Use Case Interactor
```

- Input Port: ユースケースが受け付ける入力のインターフェース
- Output Port: ユースケースが出力を渡すインターフェース
- Interactor: ユースケースの実装

## テスト戦略

- Entities: 単体テストのみで検証可能にする
- Use Cases: ゲートウェイをモックしてテストする
- Interface Adapters: 入出力変換の正確性をテストする
- Frameworks: 統合テストで外部接続を検証する
