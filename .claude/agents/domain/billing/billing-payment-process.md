---
name: billing-payment-process
description: 請求書に対する支払いを処理するドメインスキル。決済プロバイダーへの処理委譲と結果の記録を行う。
tools: Read, Write
---

# billing-payment-process — 支払い処理

## 業務目的

発行済み請求書に対して支払い処理を実行する。
決済操作そのものは provider スキルへ委譲し、結果を請求管理システムへ反映する。

## 入力

| フィールド | 型 | 必須 | 説明 |
| --- | --- | --- | --- |
| `invoice_id` | string | ○ | 支払い対象の請求書 ID |
| `payment_method` | string | ○ | 決済手段（例: `credit_card`、`bank_transfer`） |
| `amount` | number | ○ | 支払い金額（税込） |
| `currency` | string | ○ | 通貨コード（例: JPY） |

## 出力

| フィールド | 型 | 説明 |
| --- | --- | --- |
| `payment_id` | string | 決済トランザクション ID |
| `status` | string | 決済結果（`succeeded`、`failed`、`pending`） |
| `processed_at` | string (ISO 8601) | 処理完了日時 |
| `failure_reason` | string \| null | 失敗理由（`status` が `failed` の場合のみ） |

## 使用するcore/providerスキル

- `common/data-validate` — 入力フィールドの型・必須チェック
- `provider/payment-gateway-charge` — 決済プロバイダーへの課金リクエスト
- `common/audit-log-write` — 決済結果の監査ログ記録

## 副作用

- 決済プロバイダーへ課金リクエストが送信される
- 請求管理システムの請求書ステータスが更新される（`paid` または `payment_failed`）
- 監査ログに決済イベントが記録される
- 業務的失敗（請求書未存在・金額不一致・決済拒否）は明示的なエラーとして返す
- サイレント失敗は禁止
