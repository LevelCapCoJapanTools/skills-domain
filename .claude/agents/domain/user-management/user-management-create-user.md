---
name: user-management-create-user
description: 新規ユーザーを作成するドメインスキル。入力を検証し、アカウントを発行して初期設定を行う。
tools: Read, Write
---

# user-management-create-user — ユーザー作成

## 業務目的

新しいユーザーアカウントを作成する。
入力情報を検証し、パスワードをハッシュ化してユーザーレコードを永続化し、
初期ロールを割り当てる。

## 入力

| フィールド | 型 | 必須 | 説明 |
| --- | --- | --- | --- |
| `email` | string | ○ | ユーザーのメールアドレス（一意制約） |
| `password` | string | ○ | 初期パスワード（8 文字以上・複雑性要件あり） |
| `display_name` | string | ○ | 表示名 |
| `role` | string | ○ | 初期ロール（例: `viewer`、`editor`、`admin`） |
| `organization_id` | string | × | 所属組織 ID（マルチテナント環境で使用） |

## 出力

| フィールド | 型 | 説明 |
| --- | --- | --- |
| `user_id` | string | 作成されたユーザー ID |
| `email` | string | 登録されたメールアドレス |
| `created_at` | string (ISO 8601) | 作成日時 |
| `status` | string | アカウントステータス（`active` または `pending_verification`） |

## 使用するcore/providerスキル

- `common/data-validate` — 入力フィールドの型・必須チェック
- `common/email-validate` — メールアドレス形式・重複チェック
- `common/password-policy-check` — パスワード複雑性ポリシーの検証
- `common/password-hash` — パスワードのハッシュ化
- `common/id-generate` — ユーザー ID の生成
- `common/audit-log-write` — アカウント作成イベントの監査ログ記録

## 副作用

- ユーザーレコードがユーザーストアへ新規作成される
- 指定ロールがユーザーに付与される
- 監査ログにアカウント作成イベントが記録される
- 業務的失敗（メール重複・パスワード不適合・無効なロール・組織 ID 不存在）は明示的なエラーとして返す
- サイレント失敗は禁止
