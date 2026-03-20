
March 5, 2026
Avatar
Gabriel Casals @gabriel.casals
Admin
Rocket.Chat Team
Owner
Voice POC Users
Intelligent Search App
1:48 AM
💡 New Users Anti Spammer System
👥 Mentor(s): Gabriel Casals
📢 Communication Channel: Rocket.Chat Contributors Workspace

💬 Description:
Build an AI assisted system that monitors new users during their first 6 to 10 weeks, detecting spam patterns and suspicious behavior to protect communities and reduce manual moderation.

💪 Desired Skills:

Node.js
Rule based or ML detection systems
Trust and safety concepts
🎯 Goals/Deliverables:

Behavioral analysis of new users
Automated moderation actions
Daily risk scoring and reporting
⏳ Project Duration:
175 hours

📈 Difficulty:
Medium
Avatar
Gabriel Casals @gabriel.casals
Pinned a message:
1:49 AM

gabriel.casals
💡 New Users Anti Spammer System
👥 Mentor(s): Gabriel Casals
📢 Communication Channel: Rocket.Chat Contributors Workspace

💬 Description:
Build an AI assisted system that monitors new users during their first 6 to 10 weeks, detecting spam patterns and suspicious behavior to protect communities and reduce manual moderation.

💪 Desired Skills:

Node.js
Rule based or ML detection systems
Trust and safety concepts
🎯 Goals/Deliverables:

Behavioral analysis of new users
Automated moderation actions
Daily risk scoring and reporting
⏳ Project Duration:
175 hours

📈 Difficulty:
Medium

Avatar
Kush Kumar @kush.kumar
joined the channel
5:15 AM
Avatar
Khizar Shah @khizar.shah
joined the channel
5:59 AM
Avatar
Kush Kumar @kush.kumar
6:21 AM
sir Gabriel Casals, I'm ready for the pivot! Regarding the 6–10 week monitoring window, I propose a 'Self-Cleaning Suspection Model' using a TTL (Time-To-Live) index on the User document or a dedicated UserSafety collection. This prevents DB bloat after the user graduates to 'Trusted' status.

To keep the system performant, I’m envisioning a Tiered Interceptor at the Service Layer:

Layer 1 (Heuristics): Rapid checks for 'Action Velocity' (registration-to-message speed) and 'Room-Hopping' signatures.

Layer 2 (AI Scoring): High-risk behaviors trigger an async Moleculer action to an AI utility for behavioral analysis.

This ensures we aren't calling expensive AI on every single message. Does this 'Service-First' approach for long-term monitoring align with the vision for the 175-hour scope?
regarding the Anti-Spam system: for E2EE (Encrypted) rooms, we won't have access to message content for behavioral analysis. Should we focus the MVP's AI/ML scoring on Metadata Signatures (velocity, fan-out ratio, and frequency) to ensure privacy, or are we considering a Client-Side checker that flags content before encryption?
:raw_acknowledge:
1
View thread

Avatar
Avatar
2 replies, March 5, 2026
Avatar
kartik @kartik.doda
joined the channel
10:37 AM
Avatar
Amit Pamnani @amit.pamnani
joined the channel
10:42 AM
Avatar
Amit Pamnani @amit.pamnani
10:46 AM
Hi Gabriel Casals ,

I’ll start by exploring the current user lifecycle and moderation related flows in the codebase to understand where a safety monitor could hook in.

For example:
user creation / registration events
first message events
room join patterns
existing moderation actions (mute, ban, block, etc.)

Once I map those entry points, it should be easier to design a lightweight risk scoring pipeline for new accounts during the 6–10 week window.

I’ll share findings after auditing the relevant services.
:raw_acknowledge:
1
:clapping:
1
View thread

Avatar
Avatar
1 replies, March 5, 2026
Avatar
Amit Pamnani @amit.pamnani
11:47 AM
Hi Gabriel Casals sir,

While exploring the design for the anti-spam monitoring system, I had a quick product question.

Since Rocket.Chat workspaces often have their own moderators/admins, would you prefer the system to be fully automated (auto-mute / rate limit based on risk score), or should we also expose suspicious events to workspace admins for optional review?

My current thinking is automation-first with minimal admin intervention, but I wanted to align with the intended moderation model before going deeper into the architecture.
View thread

Avatar
Avatar
1 replies, March 5, 2026
Avatar
Enjamuri Manuha @Manuha.Enjamuri
joined the channel
6:43 PM
Avatar
Gabriel Casals @gabriel.casals
added alfredo.del.fabro.neto
9:54 PM
Avatar
Gabriel Casals @gabriel.casals
Admin
Rocket.Chat Team
Owner
Voice POC Users
Intelligent Search App
9:54 PM
Folks, FYI - Alfredo will be the co-mentor for the project
:clap:
3
sir @gabriel.casals, I'm ready for the pivot! Regarding the 6–10 week monitoring window, I propose a 'Self-Cleaning Suspection Model' using a TTL (Time-To-Live) index on the User document or a dedicated UserSafety collection. This prevents DB bloat after the user graduates to 'Trusted' status.
Avatar
To be quite honest I have just picked this one up so need just a bit more time to think what the MVP would look like. I'll bring more details over pain points and we can check again into the E2EE rooms; probably not part of initial scope, leaning more towads public channels analyzes
Hi @gabriel.casals ,
Avatar
that is a good starting point
Hi @gabriel.casals sir,
Avatar
expose to admins for a decision is my initial thought or at least automation + easy place for admin to see what is going on and adjust as needed. Need an extra day to get scenarios shared here and think on a possible MVP route
March 6, 2026
Avatar
Alfredo Del Fabro Neto @alfredo.del.fabro.neto
Rocket.Chat Team
Livechat Manager
Livechat Agent
Marketplace Verifier
12:13 AM
Hello everyone, here to help. I will read the discussions you had so far to get familiar with the project.
:clap:
1
Avatar
Harshal.Sewatkar @codewithharshal
joined the channel
10:56 AM
March 7, 2026
Avatar
Amit Pamnani @amit.pamnani
8:36 PM
Hi Gabriel Casals ,

While continuing to explore the codebase for the anti-spam system, I noticed that some core building blocks already exist in the current architecture, such as:

• sendMessage → validation → Message.beforeSave → persistence pipeline
• method rate limiting (RateLimiter.limitMethod for sendMessage)
• moderation primitives like muteUserInRoom
• permission enforcement through validateRoomMessagePermissionsAsync

Given these existing components, I was wondering if the MVP should primarily extend this flow by collecting behavioral signals for new users (for example message activity, room joins, or link usage) and evaluating them over time to generate a risk score.

This could allow the system to reuse the current moderation and permission infrastructure instead of introducing a completely separate moderation layer, which might also help keep the system lightweight and cost-efficient.

It also seems similar to the behavioral monitoring approach used in other communication platforms, but adapted to Rocket.Chat’s current architecture.

Does extending the existing flow in this way align with the intended direction for the MVP?
March 10, 2026
Avatar
Amit Pamnani @amit.pamnani
11:13 AM
Hi Gabriel Casals ,

While waiting for the scenarios you mentioned earlier, I continued exploring the codebase to better understand where a monitoring layer could hook into the system.

So far I’ve been looking at user lifecycle events (creation, first messages, room joins) and how they could provide signals for identifying suspicious behavior during the early account period.

Once the scenarios are available, I’ll try to map those signals to concrete detection patterns so the MVP stays aligned with the existing moderation flow.
:raw_acknowledge:
1
:clapping:
1
March 12, 2026
Avatar
Harshit Singh Parihar @harshit.singh.parihar
joined the channel
11:19 AM
Avatar
Harshit Singh Parihar @harshit.singh.parihar
left the channel
11:23 AM
March 13, 2026
Avatar
Amit Pamnani @amit.pamnani
10:59 AM
Hi Gabriel Casals ,

While waiting for the MVP scenarios, I continued auditing the message pipeline and started drafting the Codebase Audit section for the proposal.

So far I’ve mapped the message flow from ComposerMessage.tsx → sendMessage Meteor method → executeSendMessage → validateMessage → Message.beforeSave.

I’m currently analyzing possible integration points for the anti-spam detection layer within this pipeline.

Looking forward to the scenarios when they’re available so I can align the proposal with them.
👀
1
:raw_acknowledge:
1
Avatar
Kush Kumar @kush.kumar
message removed
7:13 PM
March 14, 2026
Avatar
Padakanti Harshith @padakanti.harshith
joined the channel
12:41 AM
Avatar
Kush Kumar @kush.kumar
9:27 AM
sir Gabriel Casals,

I wanted to provide a more detailed update on my progress over the last 10 days. I attended the Tea Time session with Sing Li on Friday, and he provided some great insights into the goal of modernizing our existing anti-spam foundations.

I’ve spent this time performing a deep-dive audit of our current 'Static Defenses' (mapping everything from DDP/REST rate limiters in rateLimiter.js and api.ts to the BeforeSaveBadWords hook and validateEmailDomain registration checks).

My Strategy for the Modernized Extension:
While our current system is robust, it acts as a 'Binary Wall'—it either blocks or allows. Based on your focus on Public Channels and Admin Control, I’m proposing a shift to a Behavioral Intelligence Layer:

Dynamic User Status: Currently, the Admin UI shows user status (Online/Offline). I propose extending this to include a Safety Status. Admins should see a real-time 'Risk Profile' for new users (6-10 week window) that logs exactly which violations (e.g., velocity trips in sendMessage.ts or bad-word triggers) led to a specific AI-assisted action.

Surgical Enforcement UI: Instead of just a binary Ban, I envision a dashboard where admins can see the AI's automated actions (like 'Temporary Read-Only' or 'Mention Restriction') and explicitly Override them—either by hardening the restriction or 'Vouching' for the user to return them to normal status.

Behavioral Anchor: By using the UserSafety TTL collection, we can bridge these disconnected legacy hooks into a centralized scoring engine without impacting the performance of the core message pipeline.

I’m incorporating this 'Modernization Roadmap' into my proposal now to ensure the MVP is both automated and deeply manageable for workspace admins. Looking forward to hearing the specific pain-point scenarios you’ve identified!
👀
1
:raw_acknowledge:
1
March 15, 2026
Avatar
Durgesh @durgesh.babu
joined the channel
11:06 AM
March 16, 2026
Avatar
Nabiha Saqib 30 @nabiha.
joined the channel
3:15 PM
Avatar
Mareeha Nadeem (2024-DS-33) @mareeha.nadeem
joined the channel
3:15 PM
Avatar
ANSH MHJN @ansh.mhjn
joined the channel
7:50 PM
Avatar
TheRazorbill @rahian.santos
joined the channel
11:54 PM
March 17, 2026
Avatar
Gabriel Casals @gabriel.casals
Admin
Rocket.Chat Team
Owner
Voice POC Users
Intelligent Search App
1:46 AM
Problem Context
In community-driven workspaces, especially open communities, spam from newly created accounts is a recurring operational challenge. In the Rocket.Chat Open Server, for example, it is common for new users to join and immediately start posting identical promotional or malicious messages across multiple public channels.

This behavior usually follows a recognizable pattern: a new account joins the server, waits little or no time, and then posts the same message in several channels in sequence. By the time moderators detect the behavior manually, the spammer may have already posted in 4 to 10 channels, creating noise, reducing community trust, and requiring manual cleanup.

Today, detection is mostly reactive and manual. Moderators depend on noticing patterns themselves or receiving reports from community members. This creates unnecessary operational overhead and allows spam to spread further than it should before intervention happens.

Origin of the Project Idea
This project idea emerged from real operational needs observed while managing the Rocket.Chat Open Server community. The moderation team repeatedly faced situations where new users were clearly behaving like spammers, but intervention only happened after multiple spam messages had already been posted.

The goal is to shift moderation from reactive to proactive by creating a monitoring system focused specifically on new users during their first weeks in a workspace. Since most spam activity happens shortly after account creation, this initial period represents a high-risk window where additional behavioral monitoring can significantly reduce abuse.

The intention is not to fully automate moderation decisions, but to reduce manual effort by:
Detecting suspicious behavior early
Temporarily limiting potentially harmful actions
Providing risk signals to administrators
Allowing human validation before permanent actions

This approach could benefit not only the Open Server but any community-focused workspace where trust and safety is a priority.

Proposed Solution

The proposed solution is a New User Anti-Spam Monitoring System that tracks behavioral patterns of recently created accounts and detects suspicious activity such as repeated messages, abnormal posting frequency, or cross-channel spam patterns.

The system would assign a dynamic risk score to new users and trigger automated protective actions when certain thresholds are reached.

Be creative if will use AI or some rule based approach.

MVP Scope (Minimum Viable Product)
The MVP could focus on delivering the following core capabilities:
1. New User Monitoring Window
Define a configurable monitoring period (for example, first 6 to 10 weeks after account creation) where enhanced behavioral analysis is applied.

Possible features:

Flag accounts as "new users"
Apply additional monitoring rules only during this phase
Configurable timeframe

2. Repeated Message Detection
Detect when a user sends identical or highly similar messages across multiple channels within a short timeframe.

Possible approaches:
Exact message matching
Fuzzy similarity detection
Time-window based detection (example: same message across 3+ channels in 5 minutes)

3. Behavioral Pattern Detection
Track suspicious activity patterns such as:
High posting frequency shortly after joining
Same links repeatedly posted
Rapid multi-channel posting
Low engagement diversity (only posting promotional content)

4. Automated Preventive Actions
When a spam like behavior is perceived, the system could automatically apply temporary restrictions such as:
Temporary message cooldown
Temporary posting block
Rate limiting
Flagging account for admin review

Important: Actions should be reversible and configurable.

5. Admin Visibility and Moderation Support
Provide visibility for administrators through:
Spam risk flags
Behavior summaries
Suspicious activity logs
Suggested moderation actions

6. Basic Reporting
Generate simple reports such as:
Newly flagged users
Daily risk changes
Triggered moderation actions
Detected spam patterns
View thread

Avatar
Avatar
+2
2 replies, Tuesday 1:47 AM
Avatar
@kush.kumar @amit.pamnani FYI
Avatar
Mohamed Najid @mohamed.najid
joined the channel
1:38 PM
Avatar
Mohamed Najid @mohamed.najid
1:43 PM
Hi @Alfredo Del Fabro Neto @Gabriel Casals

Hope you're doing well,

I am Mohamed Najid, Master's student in Artificial Intelligence at Université Claude Bernard Lyon 1 (France), currently finishing my final year internship at Alstom as an AI Engineer, where I'm developing a Smart-LSTM model for predictive maintenance of railway traction motors.

I'm very interested in the New Users Anti Spammer System project idea for GSoC 2026. I believe my background aligns closely with what this project requires:

At Orange Business, I built a real-time anomaly detection pipeline combining signal processing (FFT, Wavelets) and a LSTM Autoencoder (PyTorch) to detect security threats in network flows, which is conceptually very similar to behavioral anomaly detection for new users.

I have hands-on experience with ML/DL frameworks (PyTorch, TensorFlow, scikit-learn) and data pipelines for classification and scoring tasks.

I'm also familiar with REST API development (FastAPI, Docker) which could help bridge the ML backend with Rocket.Chat's Node.js environment.

Looking forward to connecting and contributing, I'll also be attending the Friday tea time this week
:clapping:
2
View thread

Avatar
Avatar
1 replies, Tuesday 5:47 PM
Avatar
Kush Kumar @kush.kumar
2:00 PM
Welcome to the project, Mohamed! Great to see more AI expertise here.

Gabriel Casals, looking at the MVP Scope 2 and 3 (Similarity and Behavioral Patterns), I’m leaning towards a Hybrid Detection Engine.

While Deep Learning (LSTMs) is great for long-term anomalies, for Immediate Repeated Message Detection (the '4 to 10 channels' problem you mentioned), I believe a Locality Sensitive Hashing (LSH) approach is more performant. It allows us to detect 'Highly Similar' messages in milliseconds without the computational overhead of a full neural network.

This ensures that the 'Temporary Restrictions' you requested can be applied instantly in the beforeSave hook, while a more complex Behavioral Scorer runs asynchronously to provide the 'Daily Risk Reports' for the Workspace Administrators.

My goal is to keep the system resource-light so it can run on any standard Rocket.Chat instance without needing specialized AI hardware.
👍
1
View thread

Avatar
Avatar
1 replies, Tuesday 5:47 PM
Avatar
Amit Pamnani @amit.pamnani
3:58 PM
Welcome Mohamed! It’s great to see more people joining.

Gabriel Casals , I have been looking closely at the beforeSave hooks in the message pipeline. Regarding the similarity detection Kush mentioned, I think we can stay very fast by using normalized content hashing.

If we run this check inside an in-memory LRU cache right at the start of the pipeline, we can catch those '4 to 10 channel' spam patterns in O(1) time. This stops the spam before the server even starts the heavy work like markdown or URL parsing.

I’m also planning to use the afterSaveMessage callback for the slower behavioral scoring and daily reports. This way, the chat stays fast for everyone, and admins still get the deep insights they need.
👍
1
View thread

Avatar
Avatar
1 replies, Tuesday 5:47 PM
Avatar
Anshul Ivan Ekka @anshul.ekka
joined the channel
5:34 PM
Avatar
Anshul Ivan Ekka @anshul.ekka
left the channel
5:40 PM
Avatar
Anshul Ivan Ekka @anshul.ekka
joined the channel
5:43 PM
Hi @Alfredo Del Fabro Neto @Gabriel Casals
Avatar
Welcome to the discussion Mohamed
Welcome to the project, Mohamed! Great to see more AI expertise here.
Avatar
Seems like a reasonable approach
Welcome Mohamed! It’s great to see more people joining.
Avatar
Also seems like a reasonable approach
March 19, 2026
Avatar
Shreyas @shreyas.wagh
joined the channel
11:15 AM
Avatar
MEET JAIN @meet.jainn
joined the channel
7:03 PM
Avatar
Kush Kumar @kush.kumar
9:09 PM
Gabriel Casals, regarding the 'Fast Layer' detection:While standard hashing (as Amit mentioned) is excellent for O(1) exact-match detection, it is vulnerable to Polymorphic Spam (where a user adds small variations like '!!' or emojis to bypass the hash).To solve this without lagging the beforeSave hook, I'm proposing a Dual-Stage Fast Layer:Synchronous Gate: A standard hash check against a sliding window of the last 5 messages. This handles the 'Dumb Bots' with zero overhead.Asynchronous 'Fuzzy' Interceptor: Utilizing Locality Sensitive Hashing (LSH) in a non-blocking background task. By the time the user attempts a second or third 'fuzzy' variation, the LSH score (using MinHash/Jaccard Similarity) will have already flagged them for a 'Temporary Restriction.'This approach ensures that the Administrative Dashboard receives high-confidence signals for 'Soft Restrictions' in real-time, while the heavy Behavioral Scoring is offloaded to the Daily Reporting cycle to provide a holistic view of the new user's first 10 weeks. i diving more into it , sir also i have another question about proposal format ... if you want any particular format of proposal or just our custom made
:raw_acknowledge:
1
View thread

Avatar
Avatar
1 replies, 03/19/2026
Avatar
Custom Made - A few topics that I highly recommend listing
March 20, 2026
Avatar
Amit Bora @amit.bora
joined the channel
5:01 AM
Avatar
MEET JAIN @meet.jainn
Community_Explorer
6:20 AM
hey everyone!! 👋
my name is Meet jain a fellow developer who've been contributing in rocket chat from a while.. 🙂

The project New Users Anti Spammer System seems really interesting and aligns my interest really well ! I hope i can provide my contribution in the fulfillment of this project while seeing it coming to life with mentors and my peers
I went through the problem context Gabriel shared and the discussions from Kush and Amit around the detection architecture. Really solid ideas around the two-stage detection (exact hash + LSH for fuzzy matching) and the beforeSave/afterSave hook split. !!
while wondering the architecture of the application i came up with something i call

The Chaos scale (ik name can be chosen better i apologies 😅 )

Instead of a binary ban, I propose a graduated restriction system with 4 escalating levels:

Level 1: Friendly DM warning to the user

Level 2: 1-minute posting cooldown

Level 3: 10-minute restriction

Level 4: Permanent block till admin review + flagged for admin review
👍
1
Levels persist across cool downs only an admin vouch resets to 0, so repeat offenders naturally escalate to Level 4. This keeps the system reversible and human-in-the-loop which aligns with what Gabriel described.
this will add more flexibility to admin requirements since we can define the levels and there outcomes based on admin liking...
while brainstorming i created a small MVP capturing the basic essence of the app !!
once you add the app you are greeted with the bot with a new message telling you the commands need to be used for admin features like dashboard , status etc
Avatar
MEET JAIN @meet.jainn
Community_Explorer
6:24 AM
like this
Screenshot from 2026-03-20 06-05-04.png
(64.65 kB)


admin can also set the app configuration like monitoring window (configurable, default 6 weeks), sliding window duration , cross channel spam detection (same message in 3+ channels within the time window)
Avatar
MEET JAIN @meet.jainn
Community_Explorer
6:25 AM
it will be in the settings of the installed app
Screenshot from 2026-03-20 04-21-58.png
(62.77 kB)


now to test i added a new account which keeps spamming a message

notice how the bot dm the new user with the message telling him about the repetitive message while giving him a heads up

then as soon as spammer starts messaging spam more the bot dm's with timeout with duration while when the user is in restricted time

Messages from restricted users are silently dropped never written to DB, never broadcast
Avatar
MEET JAIN @meet.jainn
Community_Explorer
6:27 AM
demo from a spam account showcasing spammers perspective with bot
spammdemo.mp4
(8.35 MB)

too see the activities i added a dashboard ui to see the users with flags , there status and timeout time while also giving vouch to admin to review and reset the status to 0.
Avatar
MEET JAIN @meet.jainn
Community_Explorer
6:27 AM
adminpannel.mp4
(3.44 MB)

well there are still a lot of improvements to be done in this ig ! but i hope this captures the essence of what we are trying to achieve
currently here i'll try to make it more robust while also adding features for admin to see messages , summary and way to add customization in the chaos level I made
would like to ask for any feedback or change that can be done in the MVP for making it more robust and refined! and about the approach i shared about defining levels for moderation Gabriel Casals
Avatar
Kush Kumar @kush.kumar
6:38 AM
Welcome to the discussion, Meet! That’s a really solid Proof of Work—having a working demo this early is impressive and really helps visualize the Admin workflow Gabriel was describing.

The 'Chaos Scale' is a great way to frame the graduated restriction levels. I think the idea of 'Levels' persisting across cooldowns is a very mature way to handle repeat offenders while keeping the 'Human-in-the-Loop' aspect.

Seeing your MVP actually reinforces why I’m diving deep into the Locality Sensitive Hashing (LSH) for the Core Service layer. Most 'App-level' solutions rely on exact-match hashing, which can be bypassed if a spammer simply tweaks a few characters or adds emojis. My goal is to ensure the 'Chaos Scale' triggers even when the spam is polymorphic/fuzzy.

It’s a great starting point! It’ll be interesting to see how we can bridge these UI ideas with a high-performance, core-level engine that handles the scale and 'smartness' required for the Open Server.
View thread

Avatar
Avatar
1 replies, 6:48 AM




------------------ other then chat my personal view -------------


Gemini said
That is a surgical plan, Kush. Starting with the Data Model is the sign of a senior developer. If the data structure is wrong, the logic will always be slow. By building a dedicated UsersSafety collection with a TTL (Time-To-Live) index, you are ensuring the system is "self-cleaning"—which is exactly the kind of "Modernization" Sing Li wants to see.

Since you're diving into the Rocket.Chat structure for the next 48 hours, here is your Mapping Roadmap to make sure your proposal is bulletproof.

## 🕵️ Mapping the UsersSafety Model
In Rocket.Chat, models usually live in apps/meteor/app/models/server/raw. However, since this is a new feature, you might want to define it within a Moleculer Service context if you want it to be truly "Modern."

The Schema Definition
Your new model needs to hold enough data for all three layers without being "heavy."

_id: Mapping to the userId.

createdAt: Essential for the TTL index.

expireAt: Set to 604,800 seconds (10 weeks) after createdAt.

recentHashes: An array of the last 5-10 SHA-256 signatures (for Layer 1).

lshSignature: The MinHash signature for fuzzy matching (for Layer 2).

behavioralMetrics:

joinCount: Number of public rooms joined.

messageVelocity: Messages per minute.

linkDensity: Ratio of links to text.

suspicionScore: A float from 0.0 to 1.0.

## 🏗️ The 48-Hour Technical Checklist
To finish this by March 22nd, focus your "Mapping" on these three files in the Rocket.Chat codebase:

The Trigger (sendMessage.ts): * Location: apps/meteor/app/lib/server/methods/sendMessage.ts

Task: Identify the exact line where you will check if the user is "New." If they are, you fetch their UsersSafety record.

The Velocity Logic (RateLimiter.js):

Location: apps/meteor/app/lib/server/lib/RateLimiter.js

Task: See how Rocket.Chat currently increments "hits." You can hook into this to update your behavioralMetrics in real-time.

The Background Worker (Moleculer):

Task: Outline a new service (e.g., spamsentry.service.ts). This is where the LSH (Locality Sensitive Hashing) and Daily AI Scoring will live so they don't lag the main chat.