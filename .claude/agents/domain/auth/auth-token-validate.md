---
name: auth-token-validate
description: セッショントークンを検証するドメインスキル。有効期限・署名・失効状態を確認し、ユーザーコンテキストを返す。
tools: Read
---

# auth-token-validate — トークン検証

## 業務目的

リクエストに含まれるアクセストークン（JWT）の有効性を検証し、
後続処理が利用できるユーザーコンテキストを返す。

## 入力

| フィールド | 型 | 必須 | 説明 |
| --- | --- | --- | --- |
| `access_token` | string | ○ | 検証対象の JWT |

## 出力

| フィールド | 型 | 説明 |
| --- | --- | --- |
| `valid` | boolean | トークンが有効かどうか |
| `user_id` | string \| null | 有効時のユーザー ID |
| `roles` | array \| null | 有効時のロール一覧 |
| `expires_at` | string (ISO 8601) \| null | 有効時のトークン有効期限 |
| `error_code` | string \| null | 無効時のエラーコード（例: `token_expired`、`token_revoked`） |

## 使用するcore/providerスキル

- `common/token-verify` — JWT 署名・有効期限の検証
- `common/token-revocation-check` — トークン失効リストとの照合

## 副作用

- 副作用なし（読み取り専用操作）
- 業務的失敗（署名不正・有効期限切れ・失効済み）は `valid: false` と明示的なエラーコードで返す
- サイレント失敗は禁止
