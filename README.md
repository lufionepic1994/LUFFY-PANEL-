# 🏴‍☠️ Luffy Panel

A lightweight VLESS-over-WebSocket proxy panel built with FastAPI, deployable on [Render](https://render.com).

---

## ✨ Features

- **VLESS over WebSocket (TLS)** tunneling
- Multi-inbound management with per-user traffic quotas
- Connection limits per inbound (max IPs)
- Expiry date support per inbound
- Subscription link (`/sub/<uid>`) compatible with v2rayNG, Hiddify, etc.
- Clean IP / alternative address management
- Real-time dashboard: CPU, memory, hourly traffic chart
- Bilingual UI (English / Persian)
- Dark & Light mode
- Session-based authentication with password change
- Keep-alive mechanism for free-tier hosting

---

## 🗂️ Project Structure

```
.
├── main.py             # FastAPI application (gateway + panel UI)
├── requirements.txt    # Python dependencies
├── render.yaml         # Render deployment config
└── Procfile            # Process entry point
```

---

## 🚀 Deploy on Render

### One-click via `render.yaml`

1. Fork or push this repo to GitHub.
2. Go to [render.com](https://render.com) → **New Web Service** → connect your repo.
3. Render will auto-detect `render.yaml` and configure everything.
4. Set your `ADMIN_PASSWORD` environment variable (default: `admin`).

> 💡 **Tip:** For better speed, set the **Region** to **Frankfurt (EU)** in Render settings.

### Manual Setup

| Field | Value |
|---|---|
| **Environment** | Python |
| **Build Command** | `pip install -r requirements.txt` |
| **Start Command** | `python main.py` |

---

## ▶️ Run Locally

```bash
pip install -r requirements.txt
python main.py
```

Panel will be available at: `http://localhost:8000/login`

> After deploying on Render, access your panel at: `https://yourdomain/login`

---


---

## 🗄️ Persistent Database (Turso)

Luffy Panel supports persistent storage using [Turso](https://turso.tech). If configured, your users, traffic data, and daily usage statistics will be saved every 5 minutes and persisted across restarts.

### How to set up Turso on Render:

1. **Create a Turso Database**:
   * Sign up at [Turso.tech](https://turso.tech).
   * Create a new database (e.g., `luffy-db`).
   * Get your **Database URL** (starts with `libsql://`).
   * Generate an **Auth Token**.

2. **Configure Environment Variables on Render**:
   * Go to your Render Dashboard → Select your service.
   * Go to **Environment**.
   * Add the following variables:
     * `TURSO_DATABASE_URL`: Your Turso connection URL.
     * `TURSO_AUTH_TOKEN`: Your Turso authentication token.

3. **Redeploy**:
   * Render will automatically redeploy or you can manually trigger a deploy.
   * The panel will now automatically create tables and start persisting data.

> 💡 **Note:** If these variables are not set, the panel will fall back to **in-memory storage** (data will be lost on restart).

## ⚙️ Environment Variables

| Variable | Description | Default |
|---|---|---|
| `ADMIN_PASSWORD` | Panel login password | `admin` |
| `SECRET_KEY` | Session & hash secret (auto-generated) | random |
| `PORT` | Server port | `8000` |

> ⚠️ **Change `ADMIN_PASSWORD` before deploying to production.**

---

## 📦 Dependencies

```
fastapi==0.104.1
uvicorn==0.24.0
websockets==12.0
httpx==0.25.1
psutil==5.9.6
```

---

## 🔌 API Endpoints

### Auth
| Method | Path | Description |
|---|---|---|
| `POST` | `/api/login` | Login with password |
| `POST` | `/api/logout` | Logout |
| `GET` | `/api/me` | Check session status |
| `POST` | `/api/change-password` | Change admin password |

### Inbounds
| Method | Path | Description |
|---|---|---|
| `GET` | `/api/links` | List all inbounds |
| `POST` | `/api/links` | Create new inbound |
| `PATCH` | `/api/links/{uid}` | Edit inbound |
| `DELETE` | `/api/links/{uid}` | Delete inbound |
| `GET` | `/api/links/{uid}/sub` | Get subscription info |

### Subscription
| Method | Path | Description |
|---|---|---|
| `GET` | `/sub/{uid}` | Base64 subscription (v2ray/Hiddify compatible) |

### Clean IPs
| Method | Path | Description |
|---|---|---|
| `GET` | `/api/addresses` | List alternative addresses |
| `POST` | `/api/addresses` | Add address |
| `DELETE` | `/api/addresses/{index}` | Remove address |

### System
| Method | Path | Description |
|---|---|---|
| `GET` | `/stats` | Server stats (auth required) |
| `GET` | `/health` | Health check |

---

## 🌐 VLESS Config Format

```
vless://<uuid>@<domain>:443?encryption=none&security=tls&type=ws&host=<domain>&path=/ws/<uuid>&sni=<domain>&fp=chrome&alpn=http/1.1#Luffy-<name>
```

---

## 🖥️ Panel Pages

| Page | Description |
|---|---|
| **Dashboard** | Traffic, uptime, CPU/memory, hourly chart |
| **Inbounds** | Create/edit/delete users, copy config, QR code |
| **Traffic** | Total stats |
| **Clean IP** | Manage alternative subscription addresses |
| **Security** | Change password |

---

## 📱 Client Setup (v2rayNG / Hiddify)

1. Open the panel and go to **Inbounds**.
2. Click **Sub** to copy the subscription URL.
3. In your client app, add a new subscription with that URL.
4. Update subscription — configs will appear automatically.

---

## ⚠️ Notes

- All data is stored **in-memory** by default. Restarting the service resets all inbounds and traffic stats.
- **Persistent storage** is supported via Turso (see the Database section above).
- The keep-alive task pings `/health` every 10 minutes to prevent Render free-tier spin-down.

---

## 📄 License

MIT — use freely, modify as needed.

---
---
---

[My telegram channel](https://t.me/Luffy_sh_op).

# 🏴‍☠️ لوفی پنل

یک پنل پراکسی سبک VLESS-over-WebSocket ساخته‌شده با FastAPI، قابل استقرار روی [Render](https://render.com).

---

## ✨ امکانات

- تانلینگ **VLESS روی WebSocket (TLS)**
- مدیریت چند اینباند با محدودیت ترافیک برای هر کاربر
- محدودیت تعداد اتصال (IP) برای هر اینباند
- پشتیبانی از تاریخ انقضا برای هر اینباند
- لینک اشتراک (`/sub/<uid>`) سازگار با v2rayNG، Hiddify و غیره
- مدیریت آی‌پی تمیز / آدرس‌های جایگزین
- داشبورد لحظه‌ای: CPU، حافظه، نمودار ترافیک ساعتی
- رابط کاربری دو زبانه (فارسی / انگلیسی)
- حالت تاریک و روشن
- احراز هویت مبتنی بر session با امکان تغییر رمز
- مکانیزم keep-alive برای هاستینگ رایگان

---

## 🗂️ ساختار پروژه

```
.
├── main.py             # اپلیکیشن FastAPI (گیت‌وی + رابط پنل)
├── requirements.txt    # وابستگی‌های پایتون
├── render.yaml         # تنظیمات استقرار Render
└── Procfile            # نقطه ورود پروسه
```

---

## 🚀 استقرار روی Render

### یک‌کلیکی با `render.yaml`

1. ریپو را fork کنید یا روی GitHub آپلود کنید.
2. به [render.com](https://render.com) بروید ← **New Web Service** ← ریپو را متصل کنید.
3. Render به‌صورت خودکار `render.yaml` را شناسایی و همه چیز را تنظیم می‌کند.
4. متغیر `ADMIN_PASSWORD` را تنظیم کنید (پیش‌فرض: `admin`).

> 💡 **نکته:** برای سرعت بهتر، **Region** را روی **Frankfurt (EU)** تنظیم کنید.

### تنظیم دستی

| فیلد | مقدار |
|---|---|
| **محیط** | Python |
| **دستور Build** | `pip install -r requirements.txt` |
| **دستور Start** | `python main.py` |

---

## ▶️ اجرای محلی

```bash
pip install -r requirements.txt
python main.py
```

پنل در این آدرس در دسترس است: `http://localhost:8000/login`

> بعد از استقرار روی Render، از این آدرس وارد پنل شوید: `https://yourdomain/login`

---


---

## 🗄️ دیتابیس دائمی (Turso)

لوفی پنل از ذخیره‌سازی دائمی با استفاده از [Turso](https://turso.tech) پشتیبانی می‌کند. در صورت تنظیم، کاربران، داده‌های ترافیک و آمار مصرف روزانه هر ۵ دقیقه ذخیره شده و با ریستارت شدن سرویس پاک نمی‌شوند.

### نحوه راه‌اندازی Turso در Render:

1. **ساخت دیتابیس در Turso**:
   * در [Turso.tech](https://turso.tech) ثبت‌نام کنید.
   * یک دیتابیس جدید بسازید (مثلاً `luffy-db`).
   * **Database URL** (که با `libsql://` شروع می‌شود) را کپی کنید.
   * یک **Auth Token** ایجاد کنید.

2. **تنظیم متغیرهای محیطی در Render**:
   * در پنل Render به بخش **Environment** بروید.
   * متغیرهای زیر را اضافه کنید:
     * `TURSO_DATABASE_URL`: آدرس اتصال به Turso.
     * `TURSO_AUTH_TOKEN`: توکن احراز هویت Turso.

3. **استقرار مجدد (Redeploy)**:
   * سرویس را ری‌دیپلوی کنید. پنل به‌طور خودکار جداول را ساخته و شروع به ذخیره‌سازی می‌کند.

> 💡 **نکته:** اگر این متغیرها تنظیم نشوند، پنل از **حافظه (In-memory)** استفاده می‌کند (داده‌ها با ریستارت پاک می‌شوند).

## ⚙️ متغیرهای محیطی

| متغیر | توضیح | پیش‌فرض |
|---|---|---|
| `ADMIN_PASSWORD` | رمز ورود به پنل | `admin` |
| `SECRET_KEY` | مخفی session و هش (خودکار تولید می‌شود) | تصادفی |
| `PORT` | پورت سرور | `8000` |

> ⚠️ **قبل از استقرار در محیط عمومی، `ADMIN_PASSWORD` را تغییر دهید.**

---

## 📦 وابستگی‌ها

```
fastapi==0.104.1
uvicorn==0.24.0
websockets==12.0
httpx==0.25.1
psutil==5.9.6
```

---

## 🔌 مسیرهای API

### احراز هویت
| متد | مسیر | توضیح |
|---|---|---|
| `POST` | `/api/login` | ورود با رمز |
| `POST` | `/api/logout` | خروج |
| `GET` | `/api/me` | بررسی وضعیت session |
| `POST` | `/api/change-password` | تغییر رمز ادمین |

### اینباندها
| متد | مسیر | توضیح |
|---|---|---|
| `GET` | `/api/links` | لیست همه اینباندها |
| `POST` | `/api/links` | ایجاد اینباند جدید |
| `PATCH` | `/api/links/{uid}` | ویرایش اینباند |
| `DELETE` | `/api/links/{uid}` | حذف اینباند |
| `GET` | `/api/links/{uid}/sub` | دریافت اطلاعات اشتراک |

### اشتراک
| متد | مسیر | توضیح |
|---|---|---|
| `GET` | `/sub/{uid}` | اشتراک Base64 (سازگار با v2ray/Hiddify) |

### آی‌پی تمیز
| متد | مسیر | توضیح |
|---|---|---|
| `GET` | `/api/addresses` | لیست آدرس‌های جایگزین |
| `POST` | `/api/addresses` | افزودن آدرس |
| `DELETE` | `/api/addresses/{index}` | حذف آدرس |

### سیستم
| متد | مسیر | توضیح |
|---|---|---|
| `GET` | `/stats` | آمار سرور (نیاز به احراز هویت) |
| `GET` | `/health` | بررسی سلامت سرور |

---

## 🌐 فرمت کانفیگ VLESS

```
vless://<uuid>@<domain>:443?encryption=none&security=tls&type=ws&host=<domain>&path=/ws/<uuid>&sni=<domain>&fp=chrome&alpn=http/1.1#Luffy-<name>
```

---

## 🖥️ صفحات پنل

| صفحه | توضیح |
|---|---|
| **داشبورد** | ترافیک، آپتایم، CPU/حافظه، نمودار ساعتی |
| **اینباندها** | ایجاد/ویرایش/حذف کاربر، کپی کانفیگ، کد QR |
| **ترافیک** | آمار کلی |
| **آی‌پی تمیز** | مدیریت آدرس‌های جایگزین اشتراک |
| **امنیت** | تغییر رمز |

---

## 📱 راه‌اندازی کلاینت (v2rayNG / Hiddify)

1. پنل را باز کنید و به **اینباندها** بروید.
2. روی **Sub** کلیک کنید تا لینک اشتراک کپی شود.
3. در اپ کلاینت، یک اشتراک جدید با آن لینک اضافه کنید.
4. اشتراک را آپدیت کنید — کانفیگ‌ها به‌صورت خودکار نمایش داده می‌شوند.

---

## ⚠️ نکات مهم

- تمام داده‌ها به‌صورت پیش‌فرض **در حافظه** ذخیره می‌شوند. با ریستارت سرویس، همه اینباندها و آمار ترافیک پاک می‌شوند.
- **ذخیره‌سازی دائمی** از طریق Turso پشتیبانی می‌شود (به بخش دیتابیس در بالا مراجعه کنید).
- تسک keep-alive هر ۱۰ دقیقه به `/health` پینگ می‌زند تا از خواب رفتن سرویس رایگان Render جلوگیری کند.

---

## 📄 لایسنس

MIT — آزادانه استفاده و ویرایش کنید.


[چنل تگراممون](https://t.me/Luffy_sh_op).
