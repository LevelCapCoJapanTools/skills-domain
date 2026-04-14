---
name: reporting-export-csv
description: 業務データをCSV形式でエクスポートするドメインスキル。フィルター条件を受け取り、ダウンロード可能なファイルを生成する。
tools: Read, Write
---

# reporting-export-csv — CSV エクスポート

## 業務目的

指定された業務データをフィルタリングし、CSV 形式のファイルとして出力する。
外部システムへのデータ連携や一時的なデータ分析用途を想定する。

## 入力

| フィールド | 型 | 必須 | 説明 |
| --- | --- | --- | --- |
| `entity_type` | string | ○ | エクスポート対象エンティティ（例: `invoices`、`users`） |
| `filters` | object | × | 絞り込み条件（例: 期間・ステータス） |
| `columns` | array | × | 出力するカラム名リスト（省略時は全カラム） |
| `encoding` | string | × | ファイルエンコーディング（デフォルト: `UTF-8`） |

## 出力

| フィールド | 型 | 説明 |
| --- | --- | --- |
| `file_id` | string | 生成されたファイル ID |
| `download_url` | string | CSV ファイルの取得 URL |
| `row_count` | number | エクスポート行数（ヘッダー除く） |
| `expires_at` | string (ISO 8601) | ダウンロード URL の有効期限 |

## 使用するcore/providerスキル

- `common/data-validate` — 入力フィールドの型・必須チェック
- `common/id-generate` — ファイル ID の生成
- `provider/data-store-query` — フィルター条件による業務データ取得
- `provider/file-store-write` — CSV ファイルのオブジェクトストレージへの書き込み

## 副作用

- CSV ファイルがオブジェクトストレージへ保存される
- ファイルメタデータと有効期限がファイル管理ストアへ記録される
- 業務的失敗（未対応エンティティ・無効なカラム指定・データなし）は明示的なエラーとして返す
- サイレント失敗は禁止
