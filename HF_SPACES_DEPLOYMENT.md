# 🚀 Deploy Luffy Panel on Hugging Face Spaces

This guide explains how to deploy **Luffy Panel** on Hugging Face Spaces.

---

## 📋 Prerequisites

- A [Hugging Face](https://huggingface.co) account
- Git installed locally
- Docker (for local testing, optional)

---

## ✅ Quick Deploy Steps

### 1. Fork/Clone This Repository

```bash
git clone https://github.com/shovkatkanatli-stack/LUFFY_PANEL.git
cd LUFFY_PANEL
git checkout huggingface-deployment
```

### 2. Create a New Space on Hugging Face

1. Go to [Hugging Face Spaces](https://huggingface.co/spaces)
2. Click **"Create new Space"**
3. Fill in the details:
   - **Space name**: `luffy-panel` (or your preferred name)
   - **License**: MIT
   - **Space SDK**: Docker
   - **Visibility**: Private (recommended for security)

### 3. Connect Your Repository

```bash
git remote add hf https://huggingface.co/spaces/YOUR_USERNAME/YOUR_SPACE_NAME
git branch -M huggingface-deployment main
git push -u hf main
```

Replace `YOUR_USERNAME` and `YOUR_SPACE_NAME` with your actual values.

### 4. Configure Environment Variables (Optional)

In your Space settings, add:
- `ADMIN_PASSWORD`: Your panel login password (default: `admin`)
- `SECRET_KEY`: Session encryption key (auto-generated if not set)

---

## 🔐 Security Configuration

### Change Default Password

**Important:** Always change the default password before deploying to production.

1. Set the `ADMIN_PASSWORD` environment variable in your Space settings
2. Access the panel at `https://huggingface.co/spaces/YOUR_USERNAME/YOUR_SPACE_NAME`
3. Login and change password in the **Security** tab

### Firewall & Access Control

- Keep your Space **private** unless you want public access
- Use strong passwords (min 4 characters, recommended: 12+ chars with mixed case)
- Rotate passwords regularly

---

## 🌐 Access Your Panel

Once deployed, your panel will be available at:

```
https://huggingface.co/spaces/YOUR_USERNAME/YOUR_SPACE_NAME
```

**Default login:**
- Username: (N/A - no username, just password)
- Password: `admin` (or your configured `ADMIN_PASSWORD`)

---

## 📱 Features Available

✅ **Dashboard** - Real-time traffic, CPU, memory, uptime
✅ **Inbounds** - Create/manage VLESS proxy users
✅ **Traffic Stats** - Detailed bandwidth usage
✅ **Clean IP** - Alternative subscription addresses
✅ **Security** - Change admin password
✅ **Bilingual UI** - English & Persian

---

## 🔗 API Endpoints

Your Space provides a REST API for automation:

### Authentication
- `POST /api/login` - Login with password
- `POST /api/logout` - Logout
- `POST /api/change-password` - Update password

### Inbound Management
- `GET /api/links` - List all inbounds
- `POST /api/links` - Create new inbound
- `PATCH /api/links/{uid}` - Edit inbound
- `DELETE /api/links/{uid}` - Delete inbound

### Subscriptions
- `GET /sub/{uid}` - Get base64 subscription config

### System
- `GET /stats` - Server statistics
- `GET /health` - Health check

---

## 📊 Resource Requirements

**Minimum:**
- CPU: 0.25 vCPU
- RAM: 512 MB
- Disk: 1 GB

**Recommended:**
- CPU: 0.5-1 vCPU
- RAM: 1-2 GB
- Disk: 2-5 GB

> **Note:** Hugging Face Spaces has resource limitations. For production use, consider dedicated hosting (Render, Railway, DigitalOcean, etc.)

---

## 🔄 Updates & Maintenance

### Pull Latest Changes

```bash
git pull origin huggingface-deployment
git push hf main
```

### Monitor Logs

View deployment logs in your Space's settings → "Logs"

### Restart Space

If needed, restart your Space from the settings menu or via:
```bash
# Force redeploy
git commit --allow-empty -m "Restart Space"
git push hf main
```

---

## 🐛 Troubleshooting

### Space Won't Start
- Check logs: Settings → Logs
- Verify `ADMIN_PASSWORD` is set
- Ensure `Dockerfile` is in root directory

### Can't Access Panel
- Wait 2-3 minutes for initial deployment
- Clear browser cache
- Check if Space is in "Building" state

### High Resource Usage
- Close unused inbounds
- Reduce polling frequency in browser console
- Restart the Space

### Keep-Alive Issues
- HF Spaces don't auto-sleep unlike Render
- Sessions persist across restarts
- Data is lost on Space restart (in-memory storage)

---

## 💾 Persistent Storage (Advanced)

Currently, **all data is in-memory** and resets on Space restart.

For persistent storage, you can add:
- **SQLite** (included with Python)
- **PostgreSQL** via external service
- **MongoDB Atlas** (free tier available)

See `main.py` to implement database integration.

---

## 📞 Support & Issues

- **GitHub**: [shovkatkanatli-stack/LUFFY_PANEL](https://github.com/shovkatkanatli-stack/LUFFY_PANEL)
- **Telegram**: [Luffy Channel](https://t.me/Luffy_sh_op)
- **HF Spaces Community**: [Hugging Face Community](https://huggingface.co/community)

---

## 📄 License

MIT License - Use freely, modify as needed.

---

**Happy deploying! 🏴‍☠️**
