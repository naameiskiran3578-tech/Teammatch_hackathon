# TeamMatch — Hackathon Teammate Finder 🚀

**TeamMatch** is a modern, responsive web application engineered to help students find suitable hackathon teammates based on skills, interests, experience, and location. Built with **React 19**, **TypeScript**, **Tailwind CSS**, and **Supabase** (PostgreSQL with Row Level Security, Auth, and Realtime).

![TeamMatch Preview](https://images.unsplash.com/photo-1522071820081-009f0129c71c?w=1200&auto=format&fit=crop&q=80)

---

## ✨ Key Features

1. **Authentication & Session Persistence:**
   - Sign up, login, and logout securely.
   - Powered by Supabase Auth with automatic profile and contact record provisioning.
   - Built-in one-click demo profile switcher for seamless pair testing across user accounts.

2. **Rich Student Profiles:**
   - Full student profile showcasing name, handle, avatar, headline, bio, experience level, location, and timezone.
   - Technical skills tag cloud with categories and matching highlights.
   - Preferred hackathon roles (Frontend, Backend, Fullstack, AI/ML Engineer, UI/UX Designer, Mobile, DevOps, etc.).
   - Domain interests (AI & Agents, Web3/DeFi, HealthTech, FinTech, Sustainability, DevTools).
   - Target hackathons selector and live availability toggle.
   - External links to GitHub, LinkedIn, portfolio, and Discord.

3. **Smart Teammate Matchmaking & Scoring:**
   - Multi-factor compatibility algorithm generating dynamic **0–99% Match Scores**.
   - Scores calculate:
     - **Complementary Role Synergy:** High bonus when complementary disciplines unite (e.g. Frontend + Backend, AI/ML + Fullstack, UI/UX + Frontend).
     - **Shared Tech Stack:** Direct technology overlap (e.g. React, TypeScript, Python).
     - **Shared Target Hackathon:** Alignment on specific events (e.g. HackMIT, CalHacks).
     - **Domain Interests:** Shared tracks (e.g. AI Agents, HealthTech).
   - Multi-faceted filter bar: filter by skills, roles, experience, target hackathon, remote-friendly, and minimum match score.

4. **Real-Time 1-on-1 Messaging:**
   - Private real-time chat between students using Supabase Realtime channels (`postgres_changes` / broadcast).
   - Fast icebreaker prompt chips for quick hackathon outreach.
   - Conversation thread list with unread counters and message timestamps.

5. **Row Level Security (RLS) Contact Privacy Gateway:**
   - **Zero Contact Exposure by Default:** Personal email and phone numbers are isolated in a separate `contact_details` table.
   - **PostgreSQL Row Level Security:** The database enforces that only the profile owner OR an authenticated user with an `accepted` contact request can view private contact info.
   - In-app request & approval workflow: send personalized pitches, accept/decline, and unlock contact details.

6. **Hackathon Hub:**
   - Explore upcoming hackathons (HackMIT 2026, CalHacks 13.0, ETHGlobal Paris, TreeHacks 2026).
   - Prize pools, location/virtual tags, official dates, and countdown deadlines.
   - "Looking for Teammates" roster preview showing registered student avatars with 1-click filtering.

7. **Saved Teammates & Interactive Team Builder:**
   - Save/bookmark student profiles to build candidate shortlists.
   - Visual **Team Builder Workspace** with 4 dedicated role slots.
   - **Squad Readiness Analysis:** Real-time visual progress bars analyzing team coverage across Frontend, Backend, AI/ML, and UI/UX design, paired with smart recommendations.

8. **Notifications & Theming:**
   - Real-time in-app notification center for new messages, contact request alerts, and hackathon milestones.
   - Signature **Dark Dashboard** with rich purple (`#8b5cf6`) and teal (`#14b8a6`) glowing accents.
   - Full Light Mode toggle with instant theme switching.

---

## 🛠 Tech Stack

- **Frontend:** React 19, TypeScript, Vite 5, Tailwind CSS
- **Icons:** Lucide React & custom brand SVGs
- **Celebrations:** Canvas Confetti
- **Backend / Database:** Supabase (PostgreSQL 15+, Supabase Auth, Row Level Security, Supabase Realtime)

---

## 📂 Project Architecture

```
Quantum/
├── .env.example                     # Environment template with Supabase instructions
├── .env                             # Active credentials (git-ignored)
├── supabase/
│   └── schema.sql                   # Complete PostgreSQL DDL, RLS policies, triggers, and seed data
├── src/
│   ├── types/
│   │   └── index.ts                 # TypeScript interfaces and domain models
│   ├── lib/
│   │   ├── supabase.ts              # Supabase client singleton with connection status check
│   │   ├── matchScore.ts            # Teammate compatibility and skill-match scoring algorithm
│   │   └── mockData.ts              # Seed data mirroring PostgreSQL database
│   ├── context/
│   │   ├── AuthContext.tsx          # Supabase auth, profile hydration, and demo switcher
│   │   └── AppContext.tsx           # Global state (profiles, chat, requests, teams, notifications)
│   ├── components/
│   │   ├── layout/
│   │   │   ├── Sidebar.tsx          # Navigation sidebar with notification badges and mobile drawer
│   │   │   ├── Header.tsx           # Search, theme toggle, and demo user switcher
│   │   │   └── NotificationDropdown.tsx
│   │   ├── auth/
│   │   │   └── AuthModal.tsx        # Login, registration, and quick demo logins
│   │   ├── profile/
│   │   │   ├── ProfileCard.tsx      # Student card with match score and protected contact info
│   │   │   ├── ProfileModal.tsx     # Full profile modal with portfolio links
│   │   │   ├── EditProfileModal.tsx # Profile editor (skills, roles, hackathons, phone)
│   │   │   └── ContactRequestModal.tsx # Pitch message modal
│   │   ├── teammates/
│   │   │   └── FilterBar.tsx        # Multi-filter (skills, roles, hackathons, experience)
│   │   ├── chat/
│   │   │   ├── ChatView.tsx         # Real-time chat interface with icebreakers
│   │   │   └── ConversationList.tsx # Thread list with unread counters
│   │   ├── hackathons/
│   │   │   └── HackathonCard.tsx    # Hackathon card with roster preview
│   │   └── team-builder/
│   │       └── TeamBuilderWorkspace.tsx # Interactive team composer & skill radar
│   ├── views/
│   │   ├── ExploreView.tsx          # Teammate finder with match scores
│   │   ├── HackathonsView.tsx       # Hackathon hub
│   │   ├── MessagesView.tsx         # 1-to-1 messaging view
│   │   ├── RequestsView.tsx         # Contact request approval dashboard
│   │   ├── SavedView.tsx            # Saved profiles & Team Builder workspace
│   │   ├── ProfileView.tsx          # Current user profile and availability toggle
│   │   └── SettingsView.tsx         # Theme, RLS security breakdown, and DB status
│   ├── App.tsx                      # Root application orchestrator
│   ├── main.tsx                     # Vite entry point
│   └── index.css                    # Tailwind CSS directives and custom styles
├── tailwind.config.js               # Dark mode & color palette definitions
├── tsconfig.json                    # TypeScript compiler configuration
└── package.json                     # Project scripts and dependencies
```

---

## 🔒 Security & Row Level Security (RLS)

TeamMatch enforces strict privacy and security rules directly inside PostgreSQL:

### 1. Contact Details Protection Policy
```sql
-- Private phone and email can ONLY be read by the owner or an approved requester
CREATE POLICY "Approved contacts can view contact details" 
    ON contact_details FOR SELECT 
    USING (
        auth.uid() = user_id OR
        EXISTS (
            SELECT 1 FROM contact_requests 
            WHERE status = 'accepted'
              AND (
                  (sender_id = auth.uid() AND receiver_id = contact_details.user_id)
                  OR
                  (receiver_id = auth.uid() AND sender_id = contact_details.user_id)
              )
        )
    );
```

### 2. Private 1-to-1 Messaging Policy
```sql
CREATE POLICY "Users can view messages in their conversations" 
    ON messages FOR SELECT 
    USING (auth.uid() = sender_id OR auth.uid() = receiver_id);

CREATE POLICY "Users can send messages" 
    ON messages FOR INSERT 
    WITH CHECK (auth.uid() = sender_id);
```

### 3. Automatic Profile Provisioning Trigger
When a user registers via Supabase Auth, PostgreSQL automatically executes `handle_new_user()` to create their public `profiles` record and private `contact_details` row.

---

## 🚀 Setup & Local Development

### 1. Prerequisites
- **Node.js**: v18.0.0 or higher
- **npm**: v9.0.0 or higher

### 2. Clone & Install
```bash
# Navigate to the project directory
cd Quantum

# Install dependencies
npm install
```

### 3. Run Development Server
```bash
npm run dev
```
Open [http://localhost:5173](http://localhost:5173) in your browser.

> **Note on Demo Mode:** If Supabase environment variables are not configured yet, TeamMatch automatically launches in **Local Preview Mode** with pre-seeded student profiles, hackathons, and conversations. You can immediately test all search, filtering, chat, team building, and contact request workflows without any setup!

---

## 🗄️ Supabase Backend Configuration

To connect your own live Supabase project:

1. **Create Project**: Go to [supabase.com](https://supabase.com) and create a new project.
2. **Execute Database Schema**:
   - In your Supabase Dashboard, navigate to the **SQL Editor**.
   - Open and copy the entire contents of [`supabase/schema.sql`](./supabase/schema.sql).
   - Click **Run** to provision the tables, triggers, Row Level Security policies, and seed hackathons.
3. **Configure Environment Variables**:
   - In Supabase, go to **Project Settings** > **API**.
   - Copy your **Project URL** and **Project API Key (anon/public)**.
   - Edit `.env` in the root directory:
     ```env
     VITE_SUPABASE_URL=https://your-project-id.supabase.co
     VITE_SUPABASE_ANON_KEY=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
     ```
4. **Restart Vite Server**:
   ```bash
   npm run dev
   ```
   The header pill will now display **Supabase Live Connected** with real-time sync enabled!

---

## 🧪 Testing the Application Features

1. **Find Teammates & Match Scores:**
   - Navigate to the **Find Teammates** tab.
   - Filter by skill (e.g. *React*, *PyTorch*, *Figma*).
   - Adjust the **Minimum Match Score** slider to see compatibility calculations update in real time.
2. **Contact Requests & Privacy:**
   - Click **Request Access** on any profile card to send a personalized pitch.
   - Use the **Switch User** dropdown in the top header to switch to the recipient's perspective (e.g. *Sarah Lin* or *Elena Rostova*).
   - Go to the **Contact Requests** tab to approve the pending request.
   - Watch the celebratory confetti fire and verify that the contact details (email and phone) are now unlocked!
3. **Real-time Messaging:**
   - Click **Chat** on any profile or open the **Messaging** tab.
   - Click an icebreaker chip or type a message to chat in real time.
4. **Hackathon Hub:**
   - Visit the **Hackathon Hub** tab.
   - Click **Browse** on *HackMIT 2026* to automatically filter students registered for that hackathon.
5. **Team Builder Workspace:**
   - Head to the **Saved & Teams** tab.
   - Review your squad readiness radar.
   - Click **+ Assign Teammate** on vacant slots to slot in saved candidates and see team balance update to 100%.
6. **Dark / Light Theme:**
   - Click the sun/moon icon in the top header to toggle between dark and light themes.
