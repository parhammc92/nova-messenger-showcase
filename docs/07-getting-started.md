# ۰۷ — شروع سریع و بستهبندی

## پیشنیازها

- Python 3.10+
- PostgreSQL 16+ ( محلی یا Render)
- pip / venv

## نصب محلی

```bash
git clone https://github.com/parhammc92/NOVA-Messenger.git
cd NOVA-Messenger

python3 -m venv .venv
.venv/bin/pip install --upgrade pip
.venv/bin/pip install -r requirements.txt
```

## تنظیمات (`.env`)

```env
SECRET_KEY=<رشته-تصادفی-طولانی>
DATABASE_URL=postgresql://nova_user:PASSWORD@localhost:5432/nova_messenger
COOKIE_SECURE=false
```

```bash
chmod 600 .env
```

> برای تولید SECRET_KEY: `python3 -c "import secrets; print(secrets.token_hex(32))"`

## دیتابیس

```bash
sudo -u postgres psql -c "CREATE ROLE nova_user LOGIN PASSWORD '...';"
sudo -u postgres createdb -O nova_user nova_messenger
```

جداول بهصورت خودکار توسط `init_db()` هنگام اجرای اپ ساخته میشوند.

## اجرا

```bash
.venv/bin/python run.py       # http://127.0.0.1:5000
```

برای production:

```bash
gunicorn -k gthread -w 2 -b 0.0.0.0:5000 run:app
```

## استقرار Render

فایل `render.yaml` شامل سرویس web و دیتابیس PostgreSQL است. متغیرهای محیطی
`SECRET_KEY`، `DATABASE_URL` و `COOKIE_SECURE=true` باید تنظیم شوند.

## بستهبندی

| پلتفرم | خروجی | جزئیات |
|---|---|---|
| Linux | `.deb` (~19.6MB) | PyInstaller + dpkg-deb، باینری در `/opt/nova-messenger/`، کانفیگ `/etc/nova-messenger.env` |
| Windows | `.exe` (~15.9MB) | PyInstaller onefile، کانفیگ در `%APPDATA%\NOVA Messenger\.env` |
| CI | GitHub Actions | `.github/workflows/windows-exe.yml` — ساخت روی tag `v*` و attached به release |

نکات بستهبندی frozen:
- پوشه instance/logs در مکانی پایدار (غیر `_MEI`) قرار میگیرد.
- `async_mode="threading"` بهصورت صریح تنظیم میشود.
- `--hid-import engineio.async_drivers.threading` اضافه شده است.

## تستها

```bash
.venv/bin/python /tmp/opencode/nova_integration_test.py
```

۲۵ تست یکپارچگی شامل: احراز هویت، جستجو با کنترل دسترسی، آپلود/دانلود فایل و رابط کاربری.
