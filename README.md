# MimeCraft — Scan. Generate. Own.

3D scan → AI analysis (Gemini) → 3D model with **voice control** (ElevenLabs + Gemini).

## Quick setup (no merge conflicts)

API keys are **not** stored in the repo. Each developer uses a local config file:

1. **Copy the example config**
   ```bash
   copy config.example.js config.js
   ```
   (On Mac/Linux: `cp config.example.js config.js`)

2. **Add your keys** in `config.js`. Get them from:
   - **Gemini**: https://aistudio.google.com/apikey
   - **ElevenLabs**: https://elevenlabs.io/app/settings/api-keys (voice commands + spoken feedback)
   - **Meshy** (optional): https://www.meshy.ai/settings/api
   - **Thingiverse** (optional): https://www.thingiverse.com/apps/create

3. **Do not commit** `config.js` — it is in `.gitignore`. This keeps your keys local and avoids merge conflicts when others pull the repo.

## Voice commands (ElevenLabs + Gemini)

- **Speech-to-text**: Browser mic → ElevenLabs STT (or Web Speech API fallback).
- **Intent**: Your phrase is sent to **Gemini** so similar phrasings are recognized (e.g. “make it a bit larger”, “scale up”, “bigger”).
- **Spoken feedback**: **ElevenLabs TTS** reads the confirmation back (e.g. “Scaled up”).

After generating a 3D model, use **Hold to Speak** and try: “make it bigger”, “rotate left”, “change to metal”, “red”, “reset”.

## Run locally

Open `index.html` in a browser, or serve the folder (e.g. `npx serve .`) if you use features that need a real origin.
