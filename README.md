# Temperature Validation and Monitoring System

An academic IoT sensor telemetry web application (**IoT Sensor Data Validator**) for simulating temperature sensor readings, converting values into Kelvin, validating physical feasibility against absolute zero (`0 K`), and storing per-user monitoring history.

The repository includes both the full-stack web application (React + TypeScript + Express) and the standalone Python CLI reference implementation (`temperature_validator.py`).

---

## Features

- **Secure User Authentication & Data Isolation**
  - Signup and Login pages with consistent styling and client/server validation.
  - Passwords hashed with **PBKDF2-HMAC-SHA256** and a unique 32-byte random salt per user.
  - Persistent 7-day session tokens (`HttpOnly` cookie + Bearer token) enabling multi-device login and account persistence across sessions.
  - Strict per-user isolation: each user only accesses their own telemetry readings, dashboard state, and history.
- **Interactive Temperature Monitoring Dashboard**
  - **Temperature Scale** selector (`Celsius (°C)`, `Fahrenheit (°F)`, `Kelvin (K)`).
  - **Time Interval (seconds)** numeric input with frontend and backend validation (rejects empty, zero, negative, or non-numeric values).
  - **Single Simulation ON/OFF Toggle** that starts or stops live readings on the backend.
  - Live display of **Current Temperature**, **Temperature in Kelvin**, **Validation Status**, **Reading Number**, and **Last Updated** timestamp.
  - **Remove Dashboard Data** option that clears the dashboard display without deleting records from the History page.
- **Real-Time History Log**
  - Readings are recorded one-by-one as they are generated during active simulation.
  - Filterable by `All`, `Valid`, or `Invalid` status.
  - **Export CSV** for academic laboratory reports.
  - **Clear History** option to permanently remove all stored readings for the logged-in user (also resets the dashboard display).
- **Responsive Interface**
  - Adapts cleanly across mobile phones, tablets, laptops, and desktop screens.
- **Standalone Python Script**
  - Includes `temperature_validator.py` using Python's standard library (`random`, `time`) with matching temperature ranges, formulas, and validation rules.

---

## Temperature Ranges & Validation Logic

Every generated temperature is rounded to 2 decimal places, converted to Kelvin, and checked against absolute zero (`0 K`):

| Scale | Generation Range | Conversion to Kelvin | Validation Rule |
| :--- | :--- | :--- | :--- |
| **Celsius (°C)** | `-300.00` to `-246.30 °C` | `K = C + 273.15` | `K >= 0` → Valid, `K < 0` → Invalid |
| **Fahrenheit (°F)** | `-500.00` to `-419.34 °F` | `K = (F - 32) × 5/9 + 273.15` | `K >= 0` → Valid, `K < 0` → Invalid |
| **Kelvin (K)** | `-50.00` to `50.00 K` | `K = K` | `K >= 0` → Valid, `K < 0` → Invalid |

---

## Account Validation Rules

- **Username:** English letters only (`A-Z`, `a-z`), no spaces, numbers, or special characters; must include at least one uppercase and one lowercase letter.
- **Password:** Minimum 8 characters, including at least one uppercase letter, one lowercase letter, one number, and one special character (`@`, `#`, `$`, `%`, `!`, `&`, etc.).

---

## Tech Stack

- **Frontend:** React 19, TypeScript, Tailwind CSS v4, Lucide Icons, Vite
- **Backend:** Node.js, Express (`server.ts`), `tsx`
- **Persistence:** Local JSON store (`instance/data.json`) for users, sessions, and isolated sensor history
- **Standalone CLI:** Python 3 (`temperature_validator.py`, zero external dependencies)

---

## Project Structure

```text
.
├── server.ts                        # Express backend, auth, simulation & API routes
├── temperature_validator.py         # Standalone Python temperature validator script
├── requirements.txt                 # Python environment notes (stdlib only)
├── package.json                     # Node.js dependencies and npm scripts
├── index.html                       # HTML entry point
├── .env.example                     # Example environment variables
├── instance/                        # Persistent database storage (data.json)
└── src/
    ├── App.tsx                      # Root app & view routing (Dashboard / History)
    ├── api.ts                       # Frontend API client & token management
    ├── types.ts                     # Shared TypeScript interfaces
    ├── index.css                    # Tailwind CSS configuration
    ├── main.tsx                     # React entry point
    └── components/
        ├── AuthPage.tsx             # Login and Signup interface
        ├── Header.tsx               # Responsive navigation header & logout
        ├── Dashboard.tsx            # Simulation controls & live telemetry cards
        └── HistoryPage.tsx          # Real-time history table, filters & CSV export
```

---

## Getting Started

### Prerequisites

- **Node.js** (v18+ recommended) and **npm**
- **Python 3.x** (optional, for running the standalone CLI script)

### 1. Install Dependencies

```bash
npm install
```

### 2. Environment Setup (Optional)

Copy `.env.example` to `.env` if you need custom environment variables:

```bash
cp .env.example .env
```

The server runs on port `3000` by default (configurable via `PORT`).

### 3. Run in Development Mode

```bash
npm run dev
```

Open `http://localhost:3000` in your browser.

### 4. Build & Run in Production

```bash
npm run build
NODE_ENV=production npm start
```

### 5. Type Check

```bash
npm run lint
```

### 6. Run the Standalone Python Script

```bash
python3 temperature_validator.py
```

---

## API Endpoints

| Method | Endpoint | Auth | Description |
| :--- | :--- | :--- | :--- |
| `GET` | `/api/health` | No | Service health check |
| `POST` | `/api/signup` | No | Create a new user account and session |
| `POST` | `/api/login` | No | Authenticate credentials and issue session token |
| `POST` | `/api/logout` | Yes | Stop active user simulation and clear session cookie |
| `GET` | `/api/me` | Yes | Retrieve current authenticated user profile |
| `POST` | `/api/simulation/start` | Yes | Validate scale/interval and start background simulation |
| `POST` | `/api/simulation/stop` | Yes | Stop active simulation while preserving readings |
| `GET` | `/api/simulation/status` | Yes | Fetch simulation state and latest dashboard reading |
| `GET` | `/api/history` | Yes | List all stored readings for the authenticated user |
| `DELETE` | `/api/history` | Yes | Clear all history and dashboard data for the user |
| `POST` | `/api/dashboard/reset` | Yes | Clear only the dashboard display (preserves history) |
| `GET` | `/api/student/daily-routine` | Yes | Get daily reading statistics for the authenticated user |
| `GET` | `/api/python-code` | No | Retrieve source contents of `temperature_validator.py` |
