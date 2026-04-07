# Edge TTS API — Deploy on Vercel (Free)

A self-hosted REST API for Microsoft Edge TTS using [`rany2/edge-tts`](https://github.com/rany2/edge-tts).  
**No Microsoft API key needed. No Windows. Completely free.**

---

## 🚀 Deploy to Vercel

### Step 1 — Push to GitHub

```bash
git init
git add .
git commit -m "edge-tts API"
gh repo create edge-tts-api --public --push --source=.
```

### Step 2 — Import to Vercel

1. Go to [vercel.com/new](https://vercel.com/new)
2. Click **"Import Git Repository"** → select your GitHub repo
3. Framework Preset: **Other**
4. Click **"Environment Variables"** and add:
   - `API_KEY` → `your-secret-key-here`  
     *(leave blank to make the API public with no auth)*
5. Click **Deploy**

Your API is live at: `https://your-project.vercel.app`

---

## 📡 API Endpoints

### `GET /` — Health check
```
GET https://your-project.vercel.app/
```

### `GET /voices` — List all voices
```bash
curl https://your-project.vercel.app/voices \
  -H "X-API-Key: your-secret-key-here"
```

### `POST /tts` — Generate speech (recommended)
```bash
curl -X POST https://your-project.vercel.app/tts \
  -H "Content-Type: application/json" \
  -H "X-API-Key: your-secret-key-here" \
  -d '{
    "text": "Hello from Edge TTS!",
    "voice": "en-US-AriaNeural",
    "rate": "+0%",
    "volume": "+0%",
    "pitch": "+0Hz"
  }' \
  --output speech.mp3
```

### `GET /tts` — Generate speech via query params (easy for n8n HTTP node)
```
GET https://your-project.vercel.app/tts?text=Hello+world&voice=en-US-GuyNeural
```
Returns: MP3 audio stream

---

## 🔧 Parameters

| Parameter | Default | Example | Description |
|-----------|---------|---------|-------------|
| `text` | required | `"Hello!"` | Text to synthesize |
| `voice` | `en-US-AriaNeural` | `en-GB-SoniaNeural` | Voice name |
| `rate` | `+0%` | `+20%` or `-10%` | Speaking speed |
| `volume` | `+0%` | `+10%` or `-20%` | Volume |
| `pitch` | `+0Hz` | `+50Hz` or `-30Hz` | Pitch |

### Popular Voices
| Voice | Language | Gender |
|-------|----------|--------|
| `en-US-AriaNeural` | English (US) | Female |
| `en-US-GuyNeural` | English (US) | Male |
| `en-GB-SoniaNeural` | English (UK) | Female |
| `en-AU-NatashaNeural` | English (AU) | Female |

---

## 🔗 Use in n8n

Use an **HTTP Request** node:
- Method: `POST`
- URL: `https://your-project.vercel.app/tts`
- Auth Header: `X-API-Key: your-secret-key`
- Body (JSON):
  ```json
  {
    "text": "{{ $json.caption }}",
    "voice": "en-US-AriaNeural"
  }
  ```
- Response: **File** (binary) → pipe to next node

---

## 🔒 API Key Auth

Set `API_KEY` env var in Vercel. All requests must include:
```
X-API-Key: your-secret-key-here
```

Leave `API_KEY` empty in Vercel to make the API public (no auth).
