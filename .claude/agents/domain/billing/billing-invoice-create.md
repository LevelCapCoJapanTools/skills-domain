---
name: billing-invoice-create
description: 請求書を作成するドメインスキル。受注情報をもとに請求書データを生成し、請求管理システムへ登録する。
tools: Read, Write
---

# billing-invoice-create — 請求書作成

## 業務目的

受注確定後に、顧客への請求書を生成する。
請求書には品目・金額・税率・支払期日が含まれ、請求管理システムへ保存される。

## 入力

| フィールド | 型 | 必須 | 説明 |
| --- | --- | --- | --- |
| `order_id` | string | ○ | 受注 ID |
| `customer_id` | string | ○ | 顧客 ID |
| `line_items` | array | ○ | 品目リスト（品目名・単価・数量・税率） |
| `due_date` | string (ISO 8601) | ○ | 支払期日 |
| `currency` | string | ○ | 通貨コード（例: JPY） |

## 出力

| フィールド | 型 | 説明 |
| --- | --- | --- |
| `invoice_id` | string | 生成された請求書 ID |
| `total_amount` | number | 税込合計金額 |
| `status` | string | 請求書ステータス（`draft` または `issued`） |
| `issued_at` | string (ISO 8601) | 発行日時 |

## 使用するcore/providerスキル

- `common/data-validate` — 入力フィールドの型・必須チェック
- `common/id-generate` — 請求書 ID の生成
- `common/tax-calculate` — 税額計算ロジック（税率適用）

## 副作用

- 請求管理システムに請求書レコードが新規作成される
- 業務的失敗（顧客 ID 不正・品目数ゼロ・金額マイナス）は明示的なエラーとして返す
- サイレント失敗は禁止
