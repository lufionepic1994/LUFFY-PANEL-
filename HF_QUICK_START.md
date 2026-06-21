# 🚀 Luffy Panel on Hugging Face Spaces - Quick Start

## One-Click Deploy

### Step 1: Create Space
1. Go to https://huggingface.co/spaces
2. Click **"Create new Space"**
3. Choose:
   - **SDK**: Docker
   - **Visibility**: Private (recommended)
   - **Space name**: `luffy-panel`

### Step 2: Clone & Push
```bash
git clone https://github.com/shovkatkanatli-stack/LUFFY_PANEL.git
cd LUFFY_PANEL
git checkout huggingface-deployment
git remote add hf https://huggingface.co/spaces/YOUR_USERNAME/luffy-panel
git push hf huggingface-deployment:main
```

### Step 3: Wait for Deployment
- HF Spaces will automatically build and deploy
- Check **"Logs"** tab for progress
- Takes 2-5 minutes typically

### Step 4: Login
- Open your Space URL: `https://huggingface.co/spaces/YOUR_USERNAME/luffy-panel`
- Default password: `admin`
- **Change it immediately in Security tab!**

---

## Environment Variables (Optional)

Set in Space Settings → Variables:

| Variable | Default | Description |
|----------|---------|-------------|
| `ADMIN_PASSWORD` | `admin` | Panel login password |
| `SECRET_KEY` | auto | Session encryption key |
| `PORT` | `7860` | Server port (don't change) |

---

## Features ✨

✅ **Dashboard** - Real-time stats (CPU, RAM, traffic)
✅ **Inbounds** - Create/manage VLESS proxy users
✅ **Traffic** - Monitor bandwidth usage
✅ **Clean IP** - Alternative subscription addresses
✅ **Security** - Change admin password
✅ **Bilingual** - English & Persian UI
✅ **Dark/Light Mode** - Theme toggle

---

## API Usage

### Login
```bash
curl -X POST https://YOUR_SPACE_URL/api/login \
  -H "Content-Type: application/json" \
  -d '{"password":"admin"}'
```

### Create Inbound
```bash
curl -X POST https://YOUR_SPACE_URL/api/links \
  -H "Content-Type: application/json" \
  -H "Cookie: ren_session=YOUR_SESSION" \
  -d '{
    "label": "User1",
    "limit_value": 10,
    "limit_unit": "GB",
    "max_connections": 5,
    "days_valid": 30
  }'
```

### Get Subscription
```bash
curl https://YOUR_SPACE_URL/sub/{uid}
```

---

## Limitations on HF Spaces

⚠️ **Important:**
- **In-memory storage** - Data resets on restart
- **Resource limits** - 0.5 CPU, 2GB RAM max
- **No persistent volume** - Use SQLite/DB for production
- **Auto-restart** - No sleep like Render free tier

---

## Recommended Alternatives

For production with persistent storage:
- **Render**: render.com (see `render.yaml`)
- **Railway**: railway.app
- **DigitalOcean**: digitalocean.com
- **AWS**: Lightsail or EC2

---

## Support

- 📖 See `HF_SPACES_DEPLOYMENT.md` for detailed guide
- 🐙 GitHub: https://github.com/shovkatkanatli-stack/LUFFY_PANEL
- 💬 Telegram: https://t.me/Luffy_sh_op

---

**Enjoy your Luffy Panel! 🏴‍☠️**
