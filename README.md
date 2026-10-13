# Portfolio with an AI terminal

- `index.html` is the website (served from https://MYDOMAIN.com).
- `backend/` is a FastAPI service that answers questions about you using NVIDIA NIM. Your API key lives only on the server.

## 1. Fill in your facts
Edit `backend/knowledge.md`. The AI answers only from this file, so correct the draft project descriptions and add anything recruiters may ask.

## 2. Test the backend locally
```bash
cd backend
python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
export NVIDIA_API_KEY=your-key
export MODEL=put-exact-model-id-from-build-nvidia-here
export ALLOWED_ORIGINS=http://localhost:5500
uvicorn main:app --reload
```
Pick `MODEL` from https://build.nvidia.com/models (exact NIM model ID, do not guess).

Test: `curl -N -X POST localhost:8000/ask -H "Content-Type: application/json" -d '{"question":"What is his tech stack?"}'`

## 3. Deploy the backend (Ubuntu 22.04 VPS, systemd + Caddy)
See the deployment guide in the chat response / below. Summary:
- Copy `backend/` to `/opt/portfolio-api`, create a venv, install `requirements.txt`.
- Store secrets in `/etc/portfolio-api.env` (`chmod 600`, owned by the app user).
- Run uvicorn under systemd (listens on `127.0.0.1:8000` only).
- Caddy reverse-proxies `https://api.MYDOMAIN.com` to it with automatic HTTPS.
- `ALLOWED_ORIGINS` must be your site origin exactly, with no trailing slash: `https://MYDOMAIN.com`.
- Never commit `.env` or your API key. Set a spend limit in the NVIDIA dashboard.

## 4. Connect the website
In `index.html`, near the top, set:
```html
<script>window.ASK_API_URL="https://api.MYDOMAIN.com/ask";</script>
```

## 5. Publish the site
Serve `index.html` from `https://MYDOMAIN.com` (Caddy or any static host).

## Safety built in
Per-visitor limits (6 per minute, 40 per day), a global daily cap (500), question length limit (300 characters), CORS locked to your site, no API docs exposed, and a prompt that refuses unrelated requests. Adjust limits through the environment variables.
