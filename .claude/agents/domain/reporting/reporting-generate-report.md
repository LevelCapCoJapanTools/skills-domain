---
name: reporting-generate-report
description: 指定条件でレポートを生成するドメインスキル。データソースからの集計とフォーマット変換を行う。
tools: Read, Write
---

# reporting-generate-report — レポート生成

## 業務目的

指定された条件（期間・対象・集計軸）に基づき、業務データを集計してレポートを生成する。
生成されたレポートはレポートストアへ保存される。

## 入力

| フィールド | 型 | 必須 | 説明 |
| --- | --- | --- | --- |
| `report_type` | string | ○ | レポート種別（例: `monthly_sales`、`user_activity`） |
| `period_start` | string (ISO 8601) | ○ | 集計開始日時 |
| `period_end` | string (ISO 8601) | ○ | 集計終了日時 |
| `filters` | object | × | 追加フィルター条件（例: 部署ID・商品カテゴリ） |
| `format` | string | ○ | 出力フォーマット（`json`、`csv`、`pdf`） |

## 出力

| フィールド | 型 | 説明 |
| --- | --- | --- |
| `report_id` | string | 生成されたレポート ID |
| `download_url` | string | レポートファイルの取得 URL |
| `row_count` | number | 集計行数 |
| `generated_at` | string (ISO 8601) | 生成完了日時 |

## 使用するcore/providerスキル

- `common/data-validate` — 入力フィールドの型・必須チェック
- `common/date-range-validate` — 期間の整合性確認（開始 ≤ 終了）
- `common/id-generate` — レポート ID の生成
- `provider/data-store-query` — 業務データの集計クエリ実行
- `provider/file-store-write` — 生成ファイルのオブジェクトストレージへの書き込み

## 副作用

- レポートファイルがオブジェクトストレージへ保存される
- レポートメタデータ（ID・種別・期間・URL）がレポートストアへ記録される
- 業務的失敗（無効な期間・未対応レポート種別・データなし）は明示的なエラーとして返す
- サイレント失敗は禁止
