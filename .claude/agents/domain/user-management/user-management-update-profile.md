---
name: user-management-update-profile
description: ユーザーのプロフィール情報を更新するドメインスキル。変更可能なフィールドのみを受け付け、変更履歴を記録する。
tools: Read, Write
---

# user-management-update-profile — プロフィール更新

## 業務目的

既存ユーザーのプロフィール情報（表示名・連絡先・設定等）を更新する。
変更可能なフィールドに限定し、変更履歴を監査ログへ記録する。

## 入力

| フィールド | 型 | 必須 | 説明 |
| --- | --- | --- | --- |
| `user_id` | string | ○ | 更新対象のユーザー ID |
| `display_name` | string | × | 新しい表示名 |
| `phone_number` | string | × | 電話番号（E.164 形式） |
| `locale` | string | × | 表示言語ロケール（例: `ja-JP`） |
| `notification_preferences` | object | × | 通知設定（メール・プッシュの有効無効） |

## 出力

| フィールド | 型 | 説明 |
| --- | --- | --- |
| `user_id` | string | 更新されたユーザー ID |
| `updated_fields` | array | 実際に更新されたフィールド名の一覧 |
| `updated_at` | string (ISO 8601) | 更新日時 |

## 使用するcore/providerスキル

- `common/data-validate` — 入力フィールドの型・必須チェック
- `common/phone-validate` — 電話番号フォーマット検証
- `common/audit-log-write` — プロフィール変更イベントの監査ログ記録

## 副作用

- ユーザーレコードの指定フィールドが更新される
- 変更内容が監査ログへ記録される（変更前後の値を含む）
- 業務的失敗（ユーザー未存在・無効な電話番号・未対応ロケール）は明示的なエラーとして返す
- サイレント失敗は禁止
