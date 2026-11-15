# AstroVive – Astronaut Health Monitoring (NASA Space Apps 2026)

**Challenge:** Create Health Monitoring Software for Astronauts on Space Missions.

## Run
    pip install flask          # (flask already includes werkzeug)
    python app.py      # then open http://localhost:5000

Demo accounts (access code): Aya 1111 – Alex 2222 – John 3333, or register a new astronaut (Join Mission).
For the demo: Dashboard → "Load Demo Data (10 days)".

## What it does
- **Collects indicators:** 8 vital signs (typed or by voice) + a daily sleep and mental-health check-in + a spacecraft environment check.
- **Evaluates:** green / yellow / red using the `status_logic` rules, compared against the astronaut's own baseline, with early trend detection.
- **Acts:** every status comes with the action to take and when to contact Ground, from a knowledge base (53 conditions, 5 hazards) with evidence level and sources.
- **Works offline**, with store-and-forward reporting to Ground and a simulated communication delay (simulation only).

## Interactive body map
- The dashboard body model is clickable: head, chest & lungs, heart, abdomen, and each arm and leg.
- Clicking a region opens `/body/<region>` with the live metrics tied to that part (status, trend, normal range, guidance), its recent logs, a pain log (0–10 with notes and history) and the related knowledge-base conditions.
- Pain reported in the last 24 hours shows as a pulsing marker on the body and is included in the report sent to Ground.
- Left and right are from the astronaut's own point of view (the astronaut's right arm appears on the left of the screen).
- Which vitals belong to which body part is a team assumption, not part of the data file. Arms and legs have no live sensor, so they show pain logs and reference conditions only.

## Personalization per astronaut
- **Profile** (`/profile`): age, sex, weight, height, plus conditions: trained athlete, beta-blocker, treated high blood pressure, diabetes.
- Conditions adjust the astronaut's own limits: heart rate (athlete / beta-blocker: lower limit 40/50), systolic pressure (upper limit 129), fasting glucose (80–130, critical > 180). Old readings are re-evaluated on save.
- Age, sex, weight and height are used only to compute BMI, because the data file has no age- or sex-specific limits.
- **Compound rules** combine several indicators (shown in Smart Alerts with the 🔗 mark): high heart rate + low oxygen, fast breathing + low oxygen, high CO₂ + fast breathing, low pressure + high heart rate, low glucose + high heart rate, temperature / heart rate / respiration (2 of 3), pressure and heart rate high + stress, short sleep + reduced attention.

## Explicit limits
- Personal-limit adjustments and compound rules are team assumptions, not from the data file or NASA.
- Educational prototype: limits are Earth-based adult reference values, not NASA flight limits; decisions belong to the flight surgeon.
- Vital-sign alert settings (baseline = first two readings, alerts from the third reading, 10% deviation, trend from 3 consecutive readings) are team assumptions, not from the data file.
- Pain-level labels (mild 1–3, moderate 4–6, severe 7–10) are a team convention.
- Demo data is fictional. Rule B02 is not enabled (its threshold is TBD in the data file).
- Medical guidance text is in English, as in the data file.

## Project layout
    app.py  astra.db  requirements.txt  run.bat
    data/astra_health_data.json
    templates/  base, login, dashboard, body, details, voice, reports, wellbeing, hazards, ground, profile
    static/     style.css, app.js


## Update (v2) – what changed
- **Voice assistant (/voice):** now an interactive diagnostic assistant. It asks about symptoms, then about vitals, parses the answers (English + Arabic, digits or number words, "120 over 80", "thirty eight point five"), runs the diagnostic and shows a Smart Card. Handles mic-permission / offline / no-speech errors with clear messages; typed answers always work. Voice needs Chrome/Edge and an internet connection (browser speech recognition); open the app at http://localhost:5000.
- **Diagnose (/diagnose):** symptom form (multi-select + free text + vitals, Dashboard values fill the gaps). Dual-state engine in `diagnostic_engine.py`: State 1 = abnormal vitals (personal limits) + matching symptoms -> space hazard, 🟡/🔴/⬛️ and action plan; State 2 = normal vitals + mild symptoms -> no hazard alarm, 🟢/🟡 and wellness advice. Every session is saved to the `diagnostics` table and listed in **Reports -> Diagnostic History**.
- **Streamlit version:** `streamlit run diagnostic_streamlit.py` (same engine, same astra.db).
- **Accounts:** access codes are now hashed (old plain codes migrate automatically on first start). Each astronaut has identity (role, astronaut ID, email) and **Basic Information** (blood type, nationality, date of birth, mission, emergency contact, allergies, medications, notes) on the Profile page; role/ID/blood type are shown on the Dashboard. The Join-mission form now saves the ID and email fields.
- **Ground:** simulation speed (x1/x10/x60/x600) uses a persistent server clock: no time jumps when changing speed, survives page reload. Link state (online/down) is saved; reports sent while down are stored and transmitted automatically when the link is restored. Network/session errors are reported instead of failing silently.
- **Hide UI elements:** edit `ui_config.json` (`hidden_pages`, `hidden_elements`), save and refresh. Hidden pages disappear from the menu, their links and their APIs return 404; no other page breaks.
- Session key is no longer hard-coded (`ASTRA_SECRET` env var, or a random `.secret_key` file).


## Deploying on Vercel (fix for 500 FUNCTION_INVOCATION_FAILED)
- Put these files at the **root** of the repository (`app.py`, `templates/`, `static/`, `data/`, `astra.db`, `requirements.txt`). Vercel detects the Flask `app` object in `app.py` automatically.
- Add an Environment Variable `ASTRA_SECRET` (any long random string) in Vercel -> Settings -> Environment Variables.
- Vercel's filesystem is read-only except `/tmp`, so on Vercel the app copies `astra.db` to `/tmp/astra.db` at start. **Data saved there is temporary** (lost on cold start and not shared between instances). For permanent data use an external database (Neon / Supabase / Vercel Postgres) or a host with a writable disk (Render, Railway, PythonAnywhere).
- `diagnostic_streamlit.py` and `run.bat` are for local use only.
