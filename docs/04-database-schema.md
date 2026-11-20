# ۰۴ — دیتابیس و Schema

PostgreSQL + psycopg 3 (`dict_row`) استفاده میشود. تمام migrationها **تجمعی**اند:
`CREATE TABLE IF NOT EXISTS` و `ALTER TABLE ... ADD COLUMN IF NOT EXISTS`.

## جداول

### `users`
| ستون | نوع | توضیح |
|---|---|---|
| `id` | BIGINT IDENTITY PK | شناسه کاربر |
| `username` | VARCHAR(24) UNIQUE | نام کاربری (۳-۲۴ حرف/عدد/زیرخط) |
| `password_hash` | TEXT | هش رمز (werkzeug) |
| `display_name` | VARCHAR(40) | نام نمایشی |
| `bio` | TEXT | درباره کاربر |
| `created_at` | TIMESTAMP | تاریخ ساخت |

### `messages` — چت عمومی
`id` · `user_id` → users · `content` · `created_at`

### `servers` — سرورها
`id` · `name` · `description` · `owner_id` → users · `invite_code` (UNIQUE) · `created_at`

### `server_members` — عضویت و نقش
`id` · `server_id` → servers (CASCADE) · `user_id` → users (CASCADE) ·
`role` ('owner' \| 'admin' \| 'moderator' \| 'member') · `joined_at` · `UNIQUE(server_id, user_id)`

### `server_categories`
`id` · `server_id` · `name` · `position` · `UNIQUE(server_id, name)`

### `server_channels` — کانالها
`id` · `server_id` · `category_id` (SET NULL) · `name` · `topic` · `position` · `created_by` · `created_at`

### `channel_messages` — پیام کانال
`id` · `channel_id` (CASCADE) · `user_id` (CASCADE) · `content` · `created_at`
شاخص: `(channel_id, id DESC)`

### `channel_pins` — پیامهای پینشده
`id` · `channel_id` · `message_id` · `pinned_by` · `created_at` · `UNIQUE(channel_id, message_id)`

### `dm_messages` — پیام خصوصی
`id` · `sender_id` · `recipient_id` · `content` · `reply_to_id` (SET NULL) ·
`edited_at` · `deleted_at` · `read_at` · `created_at` · `kind` ('text' \| 'sticker')
شاخص: `(LEAST(sender_id,recipient_id), GREATEST(...), id DESC)`

### `dm_reactions`
`id` · `message_id` (CASCADE) · `user_id` (CASCADE) · `emoji` · `UNIQUE(message_id, user_id, emoji)`

### `contacts` — دفترچه مخاطبین
`id` · `owner_id` · `contact_id` · `created_at` · `UNIQUE(owner_id, contact_id)`
شاخص: `(owner_id, created_at DESC)`

### `shared_files` — فایلهای بهاشتراکگذاشتهشده (فاز ۳)
`id` · `uploader_id` · `recipient_id` · `original_name` · `stored_name` (UNIQUE) ·
`content_type` · `size_bytes` · `created_at`
شاخص: `(uploader_id, recipient_id, id DESC)`

## نمودار روابط (سادهشده)

```
users ──┬── messages
        ├── servers ── server_members ── server_channels ── channel_messages ── channel_pins
        ├── dm_messages ── dm_reactions
        ├── contacts
        └── shared_files
```

## نکات مهم

- تمام جداول وابسته به کاربر دارای `ON DELETE CASCADE`اند.
- `dm_messages.deleted_at` یعنی حذف نرم (پیام در دیتابیس میماند ولی نمایش داده نمیشود).
- `init_db()` هنگام ساخت اپلیکیشن اجرا میشود و طوری طراحی شده که بارها اجرا شود بدون آسیب.
