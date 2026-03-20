# New User Anti-Spam Monitoring System

This document outlines the architecture, integration points, and implementation strategy for the proposed Anti-Spam monitoring system, focusing on Locality Sensitive Hashing (LSH) for immediate detection and AI-based behavioral scoring.

## 🎯 Architecture Overview

The system transitions Rocket.Chat from binary walls (block/allow) to a **Behavioral Intelligence Layer** combining rapid heuristics checking with asynchronous AI scoring. 

- **Layer 1 (Heuristics/LSH):** Integrated directly into the message pipeline. Swiftly compares message text and detects aggressive velocity using LSH to block repeated spammers near-instantly without blocking the event loop.
- **Layer 2 (AI Scoring):** Asynchronous evaluation of a user's broader behavioral profile (room-hopping, activity spikes, fan-out ratios) over their first 6-10 weeks. 

---

## 📂 Key Codebase Files & Integration Points

Here is a map of where you will need to inject or modify logic to implement the full MVP.

### 1. The Core Message Pipeline (Backend)

*   **`apps/meteor/app/lib/server/methods/sendMessage.ts` & `apps/meteor/app/lib/server/functions/sendMessage.ts`**
    *   **Why you need it:** These files execute when a user posts a message. This is the main funnel for all chat interactions.
    *   **Implementation:** You will intercept the message here for the fast heuristic check before it enters the database.

*   **`apps/meteor/app/lib/server/startup/rateLimiter.js`**
    *   **Why you need it:** This currently handles DDP rate limits (e.g., max messages per second). 
    *   **Implementation:** You will extend these definitions so that users within the "6-10 weeks" timeframe hit tighter velocity throttles.

### 2. Immediate Pattern Check (The LSH Layer)

*   **`apps/meteor/server/services/messages/hooks/` (Directory)**
    *   **Why you need it:** You can see existing message interceptors here (like `BeforeSaveBadWords.ts` and `BeforeSavePreventMention.ts`).
    *   **Implementation:** You will create a file here: `BeforeSaveSpamCheck.ts`. In this hook, you will apply the Locality Sensitive Hashing (LSH) to compare the new message's hash against recent messages by that user. If similarity is excessively high across multiple rooms within minutes, you temporarily read-only restrict them.

### 3. The Analytics & AI Scoring Layer

*   **`apps/meteor/server/services/spamsentry/spamsentry.service.ts` (New Service)**
    *   **Why you need it:** We shouldn't run expensive background evaluations in the main web threads. LSH is fast, but AI behavioral scoring requires a dedicated space.
    *   **Implementation:** Create this backend service using Rocket.Chat's service infrastructure. This service listens to event streams (like user joins, messages sent). It updates a dedicated `UserSafety` TTL index collection. This service handles the async calculation for "Daily Risk Scoring."

### 4. User Interface & Admin Verification (Frontend)

*   **`apps/meteor/client/views/admin/` (Directory)**
    *   **Why you need it:** Admins need to see the behavioral scoring and adjust the "Risk Profiles." 
    *   **Implementation:** Add a dedicated "Anti-Spam Intelligence" dashboard or augment the existing "Users" tab to surface users with high AI-assigned risk scores.

*   **`apps/meteor/client/views/room/composer/ComposerMessage.tsx`**
    *   **Why you need it:** If a user triggers the LSH immediate block, the frontend composer needs to inform them gracefully.
    *   **Implementation:** Catch the custom error thrown cleanly by `BeforeSaveSpamCheck.ts` and show a banner indicating they are temporarily muted.

---

## 🛠️ Step-by-Step Implementation Guide

### Phase 1: Establish the Fast LSH Hook (Days 1-3)
1. Write the LSH comparison logic function (keep it lightweight!).
2. Attach it into `apps/meteor/server/services/messages/hooks/BeforeSaveSpamCheck.ts`.
3. Have it apply a temporary role or flag if triggered.

### Phase 2: Create the Daily Scoring Service (Days 4-7)
1. Create `spamsentry.service.ts` in the services folder. 
2. Emit events from user actions (sendMessage, joinRoom) and capture them in `spamsentry`.
3. Compute daily risk scores continuously. Store them using a MongoDB TTL (Time-to-Live) index so data naturally expires after the 6-10 week watch window concludes.

### Phase 3: Expose to Admins (Days 8-10)
1. Add new endpoints in `apps/meteor/app/api/server/v1/` to fetch the high-risk users list.
2. Build UI blocks in `apps/meteor/client/views/admin/spam/` with a simple table showing risk status, LSH triggers, and buttons to override.
