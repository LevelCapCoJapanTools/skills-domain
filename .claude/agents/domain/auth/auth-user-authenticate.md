---
name: auth-user-authenticate
description: ユーザー認証を行うドメインスキル。クレデンシャルを検証し、セッショントークンを発行する。
tools: Read, Write
---

# auth-user-authenticate — ユーザー認証

## 業務目的

ユーザーが提供するクレデンシャル（メールアドレス＋パスワード）を検証し、
認証成功時にセッショントークンを発行する。

## 入力

| フィールド | 型 | 必須 | 説明 |
| --- | --- | --- | --- |
| `email` | string | ○ | ユーザーのメールアドレス |
| `password` | string | ○ | 平文パスワード（転送時は TLS 前提） |
| `client_ip` | string | × | クライアント IP アドレス（レート制限・監査用） |

## 出力

| フィールド | 型 | 説明 |
| --- | --- | --- |
| `access_token` | string | 認証済みセッションの JWT |
| `refresh_token` | string | リフレッシュ用トークン |
| `expires_in` | number | アクセストークンの有効秒数 |
| `user_id` | string | 認証されたユーザー ID |

## 使用するcore/providerスキル

- `common/data-validate` — 入力フィールドの型・必須チェック
- `common/password-verify` — ハッシュ比較によるパスワード検証
- `common/token-generate` — JWT アクセストークン・リフレッシュトークンの生成
- `common/audit-log-write` — 認証イベントの監査ログ記録

## 副作用

- 認証成功時にセッションレコードが作成される
- 認証失敗回数がアカウントロック判定に使用されるためカウンターが更新される
- 監査ログに認証イベント（成功／失敗）が記録される
- 業務的失敗（アカウント未存在・パスワード不一致・アカウントロック）は明示的なエラーとして返す
- サイレント失敗は禁止
