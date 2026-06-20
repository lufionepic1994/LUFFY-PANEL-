# 🏴‍☠️ Luffy Panel

A lightweight VLESS-over-WebSocket proxy panel built with FastAPI, deployable on [Render](https://render.com) or [Railway](https://railway.app).

[![Deploy to Render](https://render.com/images/deploy-to-render-button.svg)](https://render.com/deploy?repo=https://github.com/luffy-sh-op/LUFFY_PANEL)
&nbsp;&nbsp;
[![Deploy on Railway](https://railway.app/button.svg)](https://railway.app/new/template?template=https://github.com/luffy-sh-op/LUFFY_PANEL)

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

[![Deploy to Render](https://render.com/images/deploy-to-render-button.svg)](https://render.com/deploy?repo=https://github.com/luffy-sh-op/LUFFY_PANEL)

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

### 🌐 Render & Cloudflare Clean IPs

> **This panel on Render routes through Cloudflare's clean IPs exclusively.**
>
> Render's infrastructure sits behind Cloudflare's network, so all VLESS+WS configs will automatically use **Cloudflare clean IP ranges** — which are generally unblocked and stable in restricted regions.
>
> ✅ Use the panel URL directly — Cloudflare CDN handles routing automatically.
>
> If configs don't connect, try manually entering a known Cloudflare clean IP (e.g. `104.21.x.x` or `172.67.x.x`) in your client instead of the hostname.

---

## 🚂 Deploy on Railway

### One-click deploy

[![Deploy on Railway](https://railway.app/button.svg)](https://railway.app/new/template?template=https://github.com/luffy-sh-op/LUFFY_PANEL)

1. Fork or push this repo to GitHub.
2. Go to [railway.app](https://railway.app) → **New Project** → **Deploy from GitHub repo** → select your repo.
3. Wait for the deployment to finish. You'll be given a URL — that's your service domain. To access the panel, just add `/login` to the end of your domain.

### ⚠️ Railway IP Addresses

> **Railway does NOT use Cloudflare. It uses its own dedicated IP ranges.**
>
> Railway's outbound IPs typically fall in the range **`69.46.46.x`**, so your configs will use Railway's own IPs — not Cloudflare's. These may or may not be accessible depending on your network restrictions.
>
> **If configs don't work on Railway:**
> 1. Check whether the `69.46.46.x` range is reachable from your network.
> 2. Enable **Fragment Mode** in your v2ray / v2rayNG client (see section below).
> 3. Switch to Render for Cloudflare clean IP routing.

---

## 🔧 Fragment Mode (v2rayNG / v2ray)

If your configurations are not connecting — especially on Railway — enable **Fragment Mode** in your client:

**v2rayNG (Android):**
1. Go to **Settings → Fragment**
2. Enable Fragment and set: Packets `tlshello`, Length `10-30`, Interval `10-20`
3. Reconnect

**v2ray (Desktop):** Add to your `outbound` → `streamSettings`:

```json
"sockopt": {
  "dialerProxy": "fragment",
  "tcpKeepAliveIdle": 100
}
```

Fragment mode splits the TLS ClientHello packet to bypass deep packet inspection (DPI) firewalls.

---

## ▶️ Run Locally

```bash
pip install -r requirements.txt
python main.py
```

Panel will be available at: `http://localhost:8000/login`

> After deploying on Render or Railway, access your panel at: `https://yourdomain/login`

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

## 📌 Static IPs

| Platform | Static IP? | Notes |
|---|---|---|
| **Render** (Free) | ❌ No | Shared Cloudflare IPs; clean and stable |
| **Render** (Paid) | ✅ Yes | Available on Starter plan and above |
| **Railway** | ✅ Optional | Enable via Settings → Networking → Static IP (paid feature) |

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

## 🤝 Contributing

1. Fork the repository
2. Create a new branch: `git checkout -b feature/amazing-feature`
3. Commit your changes: `git commit -m 'Add amazing feature'`
4. Push to your branch: `git push origin feature/amazing-feature`
5. Open a **Pull Request**

---

## 📄 License

MIT — use freely, modify as needed.

---

[My Telegram channel](https://t.me/Luffy_sh_op)

---
---
---

# 🏴‍☠️ لوفی پنل

یک پنل پراکسی سبک VLESS-over-WebSocket ساخته‌شده با FastAPI، قابل استقرار روی [Render](https://render.com) یا [Railway](https://railway.app).

[![Deploy to Render](https://render.com/images/deploy-to-render-button.svg)](https://render.com/deploy?repo=https://github.com/luffy-sh-op/LUFFY_PANEL)
&nbsp;&nbsp;
[![Deploy on Railway](https://railway.app/button.svg)](https://railway.app/new/template?template=https://github.com/luffy-sh-op/LUFFY_PANEL)

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

[![Deploy to Render](https://render.com/images/deploy-to-render-button.svg)](https://render.com/deploy?repo=https://github.com/luffy-sh-op/LUFFY_PANEL)

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

### 🌐 Render و آی‌پی‌های تمیز Cloudflare

> **⭐ این پنل روی Render فقط از آی‌پی‌های تمیز Cloudflare استفاده می‌کند.**
>
> زیرساخت Render پشت شبکه Cloudflare قرار دارد، بنابراین تمام کانفیگ‌های VLESS+WS به‌صورت خودکار از **آی‌پی‌های تمیز Cloudflare** عبور می‌کنند — که معمولاً آنبلاک و پایدار هستند.
>
> ✅ URL پنل را مستقیم استفاده کنید — Cloudflare CDN مسیریابی را خودکار انجام می‌دهد.
>
> اگر کانفیگ‌ها وصل نشدند، یک آی‌پی تمیز شناخته‌شده Cloudflare (مثل `104.21.x.x` یا `172.67.x.x`) را در کلاینت خود به جای hostname وارد کنید.

---

## 🚂 استقرار روی Railway

### استقرار یک‌کلیکی

[![Deploy on Railway](https://railway.app/button.svg)](https://railway.app/new/template?template=https://github.com/luffy-sh-op/LUFFY_PANEL)

1. ریپو را fork کنید یا روی GitHub آپلود کنید.
2. به [railway.app](https://railway.app) بروید ← **New Project** ← **Deploy from GitHub repo** ← ریپو را انتخاب کنید.
3.صبر کنید تا deploy شود بعد از deploy یک url به شما داده میشود که ان دامنه سرویس شماست برای ورود به پنل کافیست به اخر دامنه تان /login اضافه کنید.


### ⚠️ آی‌پی‌های Railway

> **⭐ Railway از Cloudflare استفاده نمی‌کند و از آی‌پی‌های اختصاصی خودش استفاده می‌کند.**
>
> آی‌پی‌های خروجی Railway معمولاً در رنج **`69.46.46.x`** هستند، بنابراین کانفیگ‌های شما از آی‌پی‌های خود Railway عبور می‌کنند — نه از Cloudflare. این آی‌پی‌ها ممکن است بسته به محدودیت‌های شبکه شما در دسترس باشند یا نباشند.
>
> **اگر کانفیگ‌ها روی Railway کار نکرد:**
> 1. بررسی کنید که رنج `69.46.46.x` از شبکه شما در دسترس است.
> 2. **حالت Fragment را در کلاینت v2ray / v2rayNG فعال کنید** (بخش زیر را ببینید).
> 3. برای استفاده از آی‌پی‌های تمیز Cloudflare، به Render بروید.

---

## 🔧 فعال‌کردن Fragment Mode (در v2rayNG / v2ray)

اگر کانفیگ‌ها وصل نمی‌شوند — به‌خصوص روی Railway — **حالت Fragment را فعال کنید:**

**v2rayNG (اندروید):**
1. به **Settings → Fragment** بروید
2. Fragment را فعال کنید و تنظیم کنید: Packets روی `tlshello`، Length روی `10-30`، Interval روی `10-20`
3. مجدداً وصل شوید

**v2ray (دسکتاپ):** به `outbound` → `streamSettings` اضافه کنید:

```json
"sockopt": {
  "dialerProxy": "fragment",
  "tcpKeepAliveIdle": 100
}
```

حالت Fragment بسته TLS ClientHello را تقسیم می‌کند تا از فایروال‌های DPI عبور کند.

---

## ▶️ اجرای محلی

```bash
pip install -r requirements.txt
python main.py
```

پنل در این آدرس در دسترس است: `http://localhost:8000/login`

> بعد از استقرار روی Render،Railway از این آدرس وارد پنل شوید: `https://yourdomain/login`

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

> ⚠️ **بعد از استقرار در محیط عمومی، `ADMIN_PASSWORD` را تغییر دهید.**

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

## 📌 آی‌پی استاتیک

| پلتفرم | آی‌پی استاتیک؟ | توضیحات |
|---|---|---|
| **Render** (رایگان) | ❌ خیر | آی‌پی‌های مشترک Cloudflare؛ تمیز و پایدار |
| **Render** (پولی) | ✅ بله | از پلان Starter به بالا در دسترس |
| **Railway** | ✅ اختیاری | از طریق Settings → Networking → Static IP فعال شود (ویژگی پولی) |

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

---

[چنل تلگراممون](https://t.me/Luffy_sh_op)
