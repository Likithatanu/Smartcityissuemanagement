# SmartCityissueManagement (CivicFlow)

> **AI-Powered Environmental and Neighbourhood Issue Management System**  
> *Stage 1: Frontend Foundation*

**CivicFlow** is a modern civic-tech web application that empowers residents to report and track environmental and neighbourhood issues (such as waste accumulation, sewage leaks, waterlogging, industrial/air pollution, broken roads, and street safety) while giving municipal departments an operational queue to triage, inspect, and verify resolutions.

---

## 📌 Project Overview & Stage 1 Scope

This repository represents **Stage 1 (Frontend Foundation)** of the system. In accordance with strict development guidelines:
- **No Mock Data:** We do not display fake complaints, fake citizens, or simulated metrics.
- **Honest Empty States:** Unconnected sections explicitly indicate that database records, authentication, and AI routing will be connected in future steps.
- **Supabase-Ready:** Safe client integration configured to use standard environment variables without hardcoded secrets or service keys.
- **Vercel-Ready:** SPA fallback routing configured in `vercel.json` for smooth page refreshes on subroutes (`/login`, `/register`, `/dashboard/citizen`, `/dashboard/department`).

---

## 📂 Project Structure

```text
smartissuemanagement/
├── public/
│   └── favicon.svg               # Civic-tech SVG favicon
├── src/
│   ├── components/
│   │   ├── layout/
│   │   │   ├── Navbar.tsx        # Responsive header with CivicFlow branding & navigation
│   │   │   ├── Footer.tsx        # Footer with links and build stage indicator
│   │   │   └── MainLayout.tsx    # App layout shell with React Router Outlet
│   │   └── ui/
│   │       ├── Button.tsx        # Accessible button primitive (primary, outline, ghost)
│   │       ├── Card.tsx          # Card container
│   │       ├── Badge.tsx         # Category/status badge tags
│   │       ├── EmptyState.tsx    # Honest empty state component with descriptive notes
│   │       └── StatusBanner.tsx  # Informational Stage 1 banner component
│   ├── lib/
│   │   └── supabase.ts           # Safe Supabase client init (reads VITE_SUPABASE_* env vars)
│   ├── pages/
│   │   ├── HomePage.tsx          # Hero, How It Works, Issue Categories, About
│   │   ├── LoginPage.tsx         # Accessible Login UI (Citizen / Staff role tabs)
│   │   ├── RegisterPage.tsx      # Registration UI design with community fields
│   │   ├── CitizenDashboardPage.tsx    # Resident dashboard layout with honest empty states
│   │   ├── DepartmentDashboardPage.tsx # Department queue layout with honest empty states
│   │   └── NotFoundPage.tsx      # 404 handler
│   ├── App.tsx                   # React Router routing definition
│   ├── index.css                 # Tailwind CSS directives
│   ├── main.tsx                  # React DOM root entry
│   └── vite-env.d.ts             # TypeScript definitions for Vite environment variables
├── .env.example                  # Template with placeholder variable names only
├── .gitignore                    # Git ignore file (keeps node_modules and .env secure)
├── package.json                  # Scripts and dependencies
├── postcss.config.js             # PostCSS configuration for Tailwind
├── tailwind.config.js            # Tailwind color scheme and typography
├── tsconfig.json                 # TypeScript project configuration
├── vercel.json                   # Vercel SPA rewrite configuration
└── README.md                     # Documentation
```

---

## 🚀 Running the Project Locally

### 1. Prerequisites
Ensure you have **Node.js** (version 18 or higher) and **npm** installed on your machine. Check with:
```bash
node -v
npm -v
```

### 2. Install Dependencies
Open your terminal in the project directory and install the necessary packages:
```bash
npm install
```

### 3. Setup Environment Variables (Optional in Stage 1)
Copy the example `.env` file to create your local environment configuration:
```bash
cp .env.example .env.local
```
Inside `.env.local`, you will see:
```env
VITE_SUPABASE_URL=
VITE_SUPABASE_PUBLISHABLE_KEY=
```
> **Note:** In Stage 1, leaving these empty is completely safe. The application includes safety guards (`src/lib/supabase.ts`) that will run the frontend without errors and will show an honest status indicating that credentials are not yet set.

### 4. Start the Development Server
```bash
npm run dev
```
Open your browser and navigate to `http://localhost:5173`.

### 5. Build for Production
To verify that TypeScript and Vite produce an error-free production bundle:
```bash
npm run build
```
To preview the production build locally:
```bash
npm run preview
```

---

## ☁️ Deploying to Vercel Using GitHub

Deploying CivicFlow to Vercel is straightforward:

### Step 1: Push Code to a GitHub Repository
1. Initialize git in your local directory (if not already done):
   ```bash
   git init
   git add .
   git commit -m "feat: Stage 1 frontend foundation for SmartCityissueManagement"
   ```
2. Create a new repository on [GitHub](https://github.com).
3. Link your remote repository and push:
   ```bash
   git branch -M main
   git remote add origin https://github.com/<your-username>/SmartCityissueManagement.git
   git push -u origin main
   ```

### Step 2: Import into Vercel
1. Log in to [Vercel](https://vercel.com) and click **"Add New"** &rarr; **"Project"**.
2. Select your `SmartCityissueManagement` GitHub repository.
3. Vercel automatically detects the **Vite** framework preset:
   - **Build Command:** `npm run build`
   - **Output Directory:** `dist`
   - **Install Command:** `npm install`

### Step 3: Configure Environment Variables in Vercel
In the project setup screen (or afterwards under **Project Settings &rarr; Environment Variables**), add your Supabase variables when ready for Stage 2:
- **`VITE_SUPABASE_URL`**: Your Supabase project URL (e.g., `https://xyzcompany.supabase.co`)
- **`VITE_SUPABASE_PUBLISHABLE_KEY`**: Your Supabase Publishable / Anon key

> ⚠️ **Important Security Rule:** Never add `service_role` or secret keys here. Only client-safe publishable keys prefixed with `VITE_` belong in frontend deployments.

### Step 4: Click Deploy
Vercel will build and deploy the application. With the included `vercel.json` SPA rewrite rules, direct links to `/dashboard/citizen`, `/dashboard/department`, `/login`, and `/register` will resolve seamlessly on refresh.

---

## 🏛️ System Architecture & Backend Specifications (Stages 2–5)

While **Stage 1** establishes the clean frontend UI foundation, the completed CivicFlow platform is engineered around these core pillars:

### 1. Complaint Intake & Storage (Supabase)
- **Data Persistence:** Submissions save geo-location coordinates, ward boundaries, category, citizen descriptions, and high-resolution photo evidence directly to Supabase PostgreSQL with strict Row Level Security (RLS).
- **Private Image Storage:** Storage buckets enforce access policies where only authenticated staff and the reporting resident can access original evidence photos.

### 2. AI-Assisted Complaint Classification & Verified Routing
- **AI Triage:** Machine learning classifies complaint severity, estimates urgency, detects duplicate complaints within proximity, and suggests the target municipal department.
- **Deterministic Jurisdiction Verification:** The system strictly checks AI recommendations against verified department, category, and municipal ward jurisdiction tables. **The system never routes complaints based on an unverified AI guess.**
- **Administrative Review Fallback:** If an AI recommendation has low confidence or lacks a verified jurisdiction match, the complaint is routed to an Administrative Review Queue for manual municipal triage.

### 3. Realtime Dashboard & Notification Engine
- **Supabase Realtime:** Assigned department staff dashboards update instantly via WebSocket change streams without manual browser refreshing.
- **Persistent Notification Records:** Every notification creates an auditable record with timestamps, delivery status (`queued`, `sent`, `delivered`, `failed`), and retry counters.
- **External Multi-Channel Providers:** Outbound alerts (Email via Resend/SendGrid; SMS & WhatsApp via Twilio/Meta API) require confirmed provider delivery receipts before marking status as delivered.
- **Citizen Lifecycle Timeline:** Transparent event history tracking milestones: `Submitted` &rarr; `Classified & Routed` &rarr; `Field Inspection Dispatched` &rarr; `Remediation Underway` &rarr; `Resolved & Evidence Uploaded`.
- **Automated Escalation:** Configurable deadline rules (e.g. hazardous sewage unresolved after 48h) automatically notify division supervisors and escalate ticket priority.

---

## 🧭 Multi-Stage Roadmap

| Stage | Focus Area | Status |
| :--- | :--- | :--- |
| **Stage 1** | Clean responsive UI, layout, honest empty states, Supabase/Vercel setup | ✅ **Complete** |
| **Stage 2** | Supabase database schema, Row-Level Security, Supabase Auth (Citizen & Staff) | ✅ **Complete** |
| **Stage 3** | Civic database foundation, verified jurisdictions & official provenance | ✅ **Complete** |
| **Stage 4** | Citizen complaint reporting with photo evidence storage & GPS, AI routing | ✅ **Complete** |
| **Stage 5** | Municipal department work queue, Super Admin governance, Realtime alerts | ✅ **Complete** |

