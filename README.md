# HelmetGuard AI — Clean Command Center

This updated version removes the old multi-page UI and keeps a focused workflow:

- Secure login with **email OR 10-digit phone number**
- Password **show/hide** button
- AI-themed command-center dashboard
- YOLO helmet detection from **webcam or image upload**
- Rider totals: with helmet / without helmet
- EasyOCR number-plate reading when available
- AI Assistant: enter a bike plate after detection and see its recorded violation history
- Link a no-helmet detection to a manually entered plate
- Dedicated, clean violation history
- Logout

## Project structure

```text
helmetguard/
├── backend/
│   ├── Controller/app.py
│   ├── Model/detector.py
│   ├── models/best.pt
│   └── requirements.txt
├── frontend/
│   ├── src/
│   │   ├── App.jsx
│   │   ├── App.css
│   │   ├── Login.jsx
│   │   ├── Login.css
│   │   ├── api.js
│   │   ├── index.css
│   │   └── main.jsx
│   ├── package.json
│   └── vite.config.js
└── README.md
```

## Run backend

From `backend`:

```bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
python Controller/app.py
```

MongoDB should be running on `mongodb://127.0.0.1:27017/` unless `MONGO_URI` is configured.

## Run frontend

From `frontend`:

```bash
npm install
npm run dev
```

Open the Vite address shown in the terminal, normally `http://127.0.0.1:5173`.

## Important login behavior

- If the user enters only digits, the UI accepts **exactly 10 digits**.
- If the user enters an email, it is treated as an email login.
- Phone login requires the MongoDB user document to contain a `phone` field.
- The registration page is intentionally removed from the UI as requested. The backend registration endpoint is retained only for compatibility/migration if an administrator needs it.

## Detection → plate workflow

1. Open **AI Detection**.
2. Start the browser camera or upload an image.
3. Run the YOLO detection.
4. If a rider is detected without a helmet, the system creates a violation record.
5. Enter the bike number plate in **AI Assistant → Plate Intelligence**.
6. The assistant returns the plate's recorded violation count and latest record.
7. If OCR did not read the plate, use **LINK THIS DETECTION TO PLATE** to associate the current no-helmet evidence with the manually entered plate.
HELMETGUARD YOLO FIX - 2026-09-29

Files in this package:
1. app.py -> backend/Controller/app.py
2. detector.py -> backend/Model/detector.py
3. App.jsx -> frontend/src/App.jsx

api.js is intentionally unchanged. Keep the user's current frontend/src/api.js.

Main fixes:
- Camera routes use detect_camera_frame(..., return_metadata=True) and stream the annotated frame.
- /camera/frame returns the annotated frame and metadata.
- Camera status includes camera_running.
- Flask debug reloader is disabled so the webcam is not opened by duplicate processes.
- Detector retries the HelmetGuard model once at a controlled lower confidence when the normal pass returns zero HelmetGuard boxes.
- Detector returns frontend-compatible number_plates and a flat detections list while preserving raw_detections.
- Dashboard camera page/section is removed; AI Vision remains the only camera page.
- Dashboard default is Overview.
- Login success explicitly sets React user/page state.

Install locations:
backend/Controller/app.py
backend/Model/detector.py
frontend/src/App.jsx

Run backend:
cd ~/Desktop/Project
source venv/bin/activate
cd backend
PYTHONPATH=. python Controller/app.py

Run frontend in another terminal:
cd ~/Desktop/Project/frontend
npm run dev
