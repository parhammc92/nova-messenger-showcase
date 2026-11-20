# ۰۵ — مرجع API و رویدادها

تمام endpointهای `/api/*` فقطJSON میپذیرند/برمیگردانند (فایروال Content-Type را بررسی میکند).
تمام درخواستهای تغییردهنده نیاز به توکن CSRF و session معتبر دارند.

## صفحات و احراز هویت

| متد | مسیر | توضیح |
|---|---|---|
| GET/POST | `/` | ورود؛ با فیلد `action=register` ثبتنام |
| POST | `/logout` | خروج و پاکسازی session |
| GET | `/chat` | صفحه چت عمومی |
| GET | `/dm/<username>` | صفحه مکالمه خصوصی |
| GET | `/servers/<id>` | صفحه سرور |
| GET | `/profile` | پروفایل |

## پیامها

| متد | مسیر | توضیح |
|---|---|---|
| GET | `/api/messages` | پیامهای عمومی |
| GET/POST | `/api/dms/<partner_id>/messages` | خواندن/ارسال DM |
| POST | `/api/dms/message/<id>/edit` | ویرایش پیام خود |
| POST | `/api/dms/message/<id>/delete` | حذف نرم پیام خود |
| POST | `/api/dms/message/<id>/react` | toggle ریاکشن |
| GET | `/api/dms/unread` | تعداد پیامهای خواندهشده |
| GET | `/api/channels/<id>/messages` | پیامهای کانال |

## سرورها و مخاطبین

| متد | مسیر | توضیح |
|---|---|---|
| POST | `/servers/create` | ایجاد سرور |
| POST | `/servers/join` | پیوستن با invite code |
| POST | `/servers/<id>/channels` | ایجاد کانال ( Admin only) |
| POST | `/servers/<id>/members/<member_id>/role` | تغییر نقش |
| POST | `/servers/<id>/members/<member_id>/kick` | اخراج عضو |
| GET | `/api/servers/<id>` | اطلاعات سرور |
| GET | `/api/channels/<id>/pins` | پیامهای پینشده |
| POST | `/api/channels/<id>/pins/<message_id>` | toggle پین |
| POST | `/contacts/add` | اضافه کردن مخاطب |
| POST | `/contacts/<id>/remove` | حذف مخاطب |
| GET | `/api/contacts` | لیست مخاطبین + حضور |

## فاز ۳ — جستجو و فایل

| متد | مسیر | توضیح |
|---|---|---|
| GET | `/api/search/messages?q=&scope=all\|global\|dm\|channel` | جستجوی امن پیامها |
| POST | `/files/upload` | آپلود multipart (غیر `/api/*` به دلیل اجازه multipart) |
| GET | `/files/<id>/download` | دانلود (فقط طرفین) |
| GET | `/api/files` | لیست فایلهای من |

## رویدادهای Socket.IO

| رویداد | جهت | توضیح |
|---|---|---|
| `connect` / `disconnect` | Client→Server | ورود به roomها / بهروزرسانی حضور |
| `send_message` | C→S | ارسال پیام عمومی |
| `new_message` | S→C | پیام عمومی جدید (`room=global_chat`) |
| `dm_send` | C→S | ارسال DM |
| `dm_typing` | both | وضعیت تایپینگ |
| `presence` / `presence_snapshot` | S→C | آنلاین/آفلاین شدن |
| `dm_reaction` / `dm_deleted` / `dm_edited` | S→C | sync تغییرات DM |

## مثال پاسخ جستجو

```json
{
  "query": "secretword",
  "scope": "all",
  "count": 3,
  "results": [
    {
      "id": 12,
      "content": "dm secretword beta",
      "created_at": "2026-11-20T16:20:00",
      "author": "alice_test",
      "kind": "dm",
      "context": "bob_test",
      "partner_username": "bob_test",
      "server_id": null
    }
  ]
}
```
