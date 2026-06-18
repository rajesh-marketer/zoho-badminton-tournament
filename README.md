# Zoho-wide Badminton Selection Tournament — Corporate Olympics 2026

A single-file web app for running a company-wide, single-elimination badminton tournament across nine categories. Everyone with the link sees live brackets; only the organizer (passcode-protected) can edit draws, set match times/courts, and advance winners. Data is shared and updates in real time via Firebase Firestore.

> Built as one self-contained `index.html` — no build step, no framework. Host it anywhere static (Netlify, GitHub Pages, etc.).

## Features

- **Nine event tabs** — Men's / Women's Singles & Doubles, Mixed Doubles, and the four 35+ categories.
- **Single-elimination brackets** with automatic seeding, bye distribution, and walkover support.
- **Realtime sync** — the organizer advances a winner and every viewer's screen updates within moments.
- **Organizer mode** — a passcode unlocks editing; everyone else is read-only.
- **Build draws by paste or image** — type/paste the entry list, or upload a two-column matchup image and let on-device OCR extract the names in row order (review before generating).
- **In-bracket editing** — rename a player, remove a player (leaving a bye), or drop a player into an empty bye slot.
- **Scheduling** — per-match date/time picker (locked to the event date) and a Court 1–4 dropdown.
- **Order of Matches** — a chronological, all-categories running order grouped by time slot, showing category, court, players, and score.
- **Winners** — auto-filled Winner / Runner-up table per category.
- **Guidelines** — tournament details and rules.
- **CSV export** — organizer can download each category's fixtures.

## Tech stack

- Plain HTML + CSS + JavaScript (ES modules), no bundler.
- [Firebase Firestore](https://firebase.google.com/docs/firestore) for shared, realtime storage (loaded from the gstatic CDN).
- [Tesseract.js](https://github.com/naptha/tesseract.js) for in-browser OCR (loaded on demand from a CDN only when the image-import button is used).

## Setup

### 1. Create a Firebase project

1. Go to the [Firebase console](https://console.firebase.google.com/) and create a project (the free Spark plan is enough).
2. Add a **Web app** (the `</>` icon) and copy the generated `firebaseConfig` object.
3. Open **Firestore Database → Create database** and start in **test mode**.

### 2. Add your config

Open `index.html` and replace the placeholder values in the `firebaseConfig` block near the top of the `<script>` section with your own:

```js
const firebaseConfig = {
  apiKey:            "…",
  authDomain:        "your-project.firebaseapp.com",
  projectId:         "your-project",
  storageBucket:     "your-project.appspot.com",
  messagingSenderId: "…",
  appId:             "…",
};
const COLLECTION = "smashcup2026"; // change to start a fresh, separate tournament
```

### 3. Firestore rules

Test mode leaves the database open. For an internal event this is usually acceptable. A minimal open ruleset:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /{document=**} { allow read, write: if true; }
  }
}
```

To tighten later, restrict by domain/API-key referrer or move writes behind Firebase Auth.

## Deploy

The app must be served over **http/https** (Firebase won't initialise from a `file://` page).

**Netlify (drag & drop):** go to [app.netlify.com](https://app.netlify.com/), drag the folder (or the `index.html`) onto the deploy area, and share the resulting URL.

**Netlify (from this repo):** connect the repo; no build command is needed and the publish directory is the repo root (or wherever `index.html` lives).

**GitHub Pages:** enable Pages on the `main` branch / root; the app is served directly.

## Usage

1. Open the deployed URL and click **Organizer login**. The first time, you set the passcode (keep it private). Everyone else stays read-only.
2. In each event's **Manage players** panel, paste the entries (one per line; for doubles separate partners with `/`, `&`, or a comma) or use **Extract names from image**, then **Generate / rebuild bracket**. Lines reading `BYE` keep their exact position.
3. Set each match's **court** and **time**, and click a player to mark the winner — they advance automatically.
4. **Order of Matches**, **Winners**, and **Guidelines** (header buttons / tab) update automatically.
5. Share the same URL with all players for live, read-only viewing.

## Notes & limitations

- The Firebase Web **API key is not a secret** — it identifies the project and is expected to be visible in client code. Protection comes from Firestore rules.
- The organizer passcode is convenience-level (hashed client-side), suitable for an internal event — not bank-grade security.
- OCR is approximate; always proofread imported names before generating a draw.
- Only matches with a scheduled time appear in **Order of Matches**.

## Project structure

```
.
├── index.html      # the entire app
└── README.md
```

---

Internal tooling for a Zoho company event. Not affiliated with any official Corporate Olympics organiser.
