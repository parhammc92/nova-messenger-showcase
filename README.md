# 🌐 NOVA Messenger — Public Showcase

> **پیامرسان ترکیبی Discord × Telegram**
>
> 📦 این ریپو **نسخهی نمایشی و مستندات کامل** است. کد منبع در ریپوی خصوصی
> `parhammc92/NOVA-Messenger` نگهداری میشود.

---

## 🎯 NOVA چیست؟

NOVA Messenger یک پیامرسان وب است که دو تجربه را باهم ترکیب میکند:

| الهامگرفته از | قابلیت در NOVA |
|---|---|
| **Discord** | سرورها، کانالها، نقشها (owner/admin/moderator/member)، سیستم invite،.pin کردن پیام |
| **Telegram** | چت خصوصی ۱:۱، تایپینگ زنده، ریپلای، ویرایش/حذف، ریاکشن، تیک خواندهشدن، استیکر، مخاطبین |

---

## ✨ تمام بخشها (نمای کامل)

دسترسی زیر با جزئیات کامل در پوشه [`docs/`](docs/) توضیح داده شدهاند:

| # | بخش | توضیح | مستند |
|---|---|---|---|
| ۰ | معرفی وvision | چرا NOVA، مقایسه، نمای کلی | [01-overview.md](docs/01-overview.md) |
| ۱ | معماری | Flask + Socket.IO + PostgreSQL، ساختار لایهها | [02-architecture.md](docs/02-architecture.md) |
| ۲ | قابلیتها | تمام فازهای ۰ تا ۴ بهتفصیل | [03-features.md](docs/03-features.md) |
| ۳ | دیتابیس | تمام جداول، ستونها، روابط و ایندکسها | [04-database-schema.md](docs/04-database-schema.md) |
| ۴ | API | تمام endpointها و رویدادهای Socket.IO | [05-api-reference.md](docs/05-api-reference.md) |
| ۵ | امنیت | CSRF، rate limit، فایروال، کنترل دسترسی | [06-security.md](docs/06-security.md) |
| ۶ | شروع سریع | نصب، تنظیمات، اجرا و بستهبندی | [07-getting-started.md](docs/07-getting-started.md) |
| ۷ | نقشه راه | کارهای انجامشده و برنامههای آینده | [08-roadmap.md](docs/08-roadmap.md) |

---

## 🛠 استک فنی

```
Frontend   HTML5 · CSS3 (CSS Variables) · Vanilla JavaScript · Socket.IO Client
Backend    Python 3 · Flask 3 · Flask-SocketIO · Jinja2 · Flask-WTF · Flask-Limiter
Database   PostgreSQL 16+ · psycopg 3 (binary)
Server     Gunicorn (gthread) · threading async mode
Deploy     Render (render.yaml)
Packaging  PyInstaller → .deb (Linux) + .exe (Windows) + GitHub Actions
```

---

## 🚀 شروع سریع (برای علاقهمندان)

```bash
# 1. کلون ریپوی خصوصی (دسترسی لازم است)
git clone https://github.com/parhammc92/NOVA-Messenger.git
cd NOVA-Messenger

# 2. محیط مجازی و وابستگیها
python3 -m venv .venv
.venv/bin/pip install -r requirements.txt

# 3. فایل .env بساز (هرگز کامیت نشود)
cat > .env <<'ENV'
SECRET_KEY=<یک-رشته-تصادفی-طولانی>
DATABASE_URL=postgresql://user:password@localhost:5432/nova_messenger
COOKIE_SECURE=false
ENV
chmod 600 .env

# 4. اجرا
.venv/bin/python run.py     # → http://127.0.0.1:5000
```

---

## 🔒 نکته امنیتی مهم

این ریپو **فاقد هرگونه کلید، توکن، پسوورد یا فایل `.env`** است.
تمام مستندات بهصورت انتزاعی و بدون مقادیر واقعی نوشته شدهاند.

---

## 📬 لینک شیر

🔗 **https://github.com/parhammc92/nova-messenger-showcase**

---

<p align="center">ساختهشده توسط <b>parhammc92</b> · ۲۰۲۶</p>
