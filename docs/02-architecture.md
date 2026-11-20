# ۰۲ — معماری

## نمای کلی لایهها

```
┌──────────────────────────────────────────────────────────────┐
│  Browser (Jinja2 Templates + Vanilla JS + Socket.IO Client)   │
├──────────────────────────────────────────────────────────────┤
│  Flask App Factory  (app/__init__.py)                         │
│   ├── csrf.init_app        (Flask-WTF)                        │
│   ├── limiter.init_app     (Flask-Limiter)                    │
│   ├── register_firewall    (app/security.py)                  │
│   ├── Blueprint: main      (app/routes/*)                     │
│   ├── init_db / close_db   (app/database.py)                  │
│   └── socketio.init_app    (threading async mode)             │
├──────────────────────────────────────────────────────────────┤
│  PostgreSQL 18  (psycopg 3, dict_row)                         │
└──────────────────────────────────────────────────────────────┘
```

## ساختار پوشهها

```
NOVA-Messenger/
├── run.py                     نقطهی ورود اجرا (Socket.IO run)
├── config.py                  تنظیماتاز محیط (SECRET_KEY / DATABASE_URL / ...)
├── app/
│   ├── __init__.py            App Factory —تمام تنظیمات و ثبت Blueprint
│   ├── extensions.py          نمونههای socketio / limiter / csrf
│   ├── database.py            get_db / close_db / init_db (مهاجرت تجمعی)
│   ├── security.py            فایروال درخواستها + throttle رویدادها
│   ├── presence.py            حضور آنلاین / آفلاین کاربران
│   ├── chat.py                رویدادهای Socket.IO عمومی
│   ├── routes/
│   │   ├── __init__.py        Blueprint `main` + ثبتتمام ماژولها
│   │   ├── auth.py            ثبتنام / ورود / خروج
│   │   ├── chat.py            صفحه چت + API پیامهای عمومی
│   │   ├── servers.py         سرورها، کانالها، نقشها، pin
│   │   ├── dms.py             پیامهای خصوصی، ریاکشن، ویرایش/حذف
│   │   ├── contacts.py        دفترچه مخاطبین
│   │   ├── profile.py         پروفایل کاربر
│   │   ├── search.py          جستجوی پیامها (فاز ۳)
│   │   └── files.py           اشتراکگذاری فایل (فاز ۳)
│   ├── templates/             chat.html · dm.html · server.html · login.html · profile.html
│   └── static/
│       ├── css/style.css      تمام استایلها (CSS Variables)
│       └── js/                chat.js · server.js · dm.js · search.js · files.js
├── render.yaml                استقرار Render
└── ROADMAP.md                 نقشه راه و لاگ پیشرفت
```

## جریان یک درخواست

1. **فایروال (`before_request`)** → بررسی طول مسیر، الگوهای مخرب، و (JSON-only) بودن `/api/*`.
2. **CSRF** → تمام POSTها باید توکن معتبر داشته باشند.
3. **Rate Limiter** → محدودیتGlobal و per-route (مثل ۵ ورود در دقیقه).
4. **Route handler** → بررسی `session`، عضویت/مالکیت، اعتبارسنجی ورودی، rollback در خطا.
5. **پاسخ** → هدرهای امنیتی (CSP، X-Frame-Options، HSTS) توسط `after_request` اضافه میشوند.

## Socket.IO

- `async_mode="threading"` بهصورت صریح تنظیم شده تا رفتار بستهبندیهای PyInstaller پایدار باشد.
- حضور: اتصال → `join_room("global_chat")` و `join_room("user_<id>")`.
- رویدادها: `new_message`، `dm_message`، `dm_typing`، `presence`، `presence_snapshot`، `reaction`، `message_edited`، `message_deleted`.

## فلسفه طراحی

- **Additive migrations**: `CREATE TABLE IF NOT EXISTS` + `ALTER ... ADD COLUMN IF NOT EXISTS` — امن برای نسخههای قدیمی دیتابیس.
- **Fail-closed firewall**: هر خطا در لایه امنیتی → رد درخواست.
- **ثبتدستی Blueprintها**: ترتیب ثبت ماژولها در `routes/__init__.py` مشخص و قابل پیشبینی است.
