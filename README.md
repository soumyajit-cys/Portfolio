# Portfolio with an AI terminal

- `index.html` is the website (host it on GitHub Pages).
- `backend/` is a FastAPI service that answers questions about you using Claude. Your API key lives only on the server.

## 1. Fill in your facts
Edit `backend/knowledge.md`. The AI answers only from this file, so correct the draft project descriptions and add anything recruiters may ask.

## 2. Test the backend locally
```bash
cd backend
python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
export ANTHROPIC_API_KEY=your-key
export ALLOWED_ORIGINS=http://localhost:5500
uvicorn main:app --reload
```
Test: `curl -N -X POST localhost:8000/ask -H "Content-Type: application/json" -d '{"question":"What is his tech stack?"}'`

## 3. Deploy the backend
Push this folder to a GitHub repo, then create a web service on Render, Railway or Fly.io.
- Root directory: `backend` (it has a Dockerfile)
- Environment variables: copy from `backend/.env.example`
- `ALLOWED_ORIGINS` must be your site origin exactly, with no trailing slash, for example `https://soumyajit-cys.github.io`
- Never commit `.env` or your API key. Set a monthly spend limit in the Anthropic Console.

## 4. Connect the website
In `index.html`, near the top, set:
```html
window.ASK_API_URL="https://YOUR-SERVICE.onrender.com/ask";
```

## 5. Publish the site
Create a repo named `soumyajit-cys.github.io` (or any repo), put `index.html` in the root, then enable Settings, Pages, deploy from the main branch.

## Safety built in
Per-visitor limits (6 per minute, 40 per day), a global daily cap (500), question length limit (300 characters), CORS locked to your site, no API docs exposed, and a prompt that refuses unrelated requests. Adjust limits through the environment variables.
