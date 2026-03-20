# GigShield — AI-Powered Parametric Insurance for India's Q-Commerce Delivery Partners

> *Your income. Protected by intelligence.*

GigShield is a parametric income-protection platform purpose-built for grocery and quick-commerce delivery partners working with platforms such as Zepto, Blinkit, and Swiggy Instamart. When an uncontrollable external disruption — extreme rainfall, toxic air quality, a flash curfew, or a city-wide internet outage — forces a delivery partner off the road, GigShield detects the event automatically, calculates the lost income, and transfers the payout within minutes. No claim forms. No manual review delays. No confusion.

**Resilience engineered. Fraud-resistant. Fair.**

---

## Table of Contents

1. [Persona and Scenario Analysis](#1-persona-and-scenario-analysis)
2. [Application Workflow](#2-application-workflow)
3. [Weekly Premium Model](#3-weekly-premium-model)
4. [Parametric Triggers](#4-parametric-triggers)
5. [Adversarial Defense & Anti-Spoofing Strategy](#5-adversarial-defense--anti-spoofing-strategy) ⚡ **NEW**
6. [AI and ML Integration](#6-ai-and-ml-integration)
7. [Tech Stack and Architecture](#7-tech-stack-and-architecture)
8. [Development Plan](#8-development-plan)
9. [Innovation Beyond the Brief](#9-innovation-beyond-the-brief)

---

## 1. Persona and Scenario Analysis

### Who we are insuring

**Persona: The Q-Commerce Dark Store Delivery Partner**

Q-commerce delivery partners differ fundamentally from food delivery riders. They operate in tight 10–20 minute delivery windows from fixed dark store locations (micro-warehouses operated by Zepto, Blinkit, etc.), completing 20–40 deliveries per shift. Their income is entirely order-count dependent — if the platform pauses deliveries due to weather, AQI alerts, or civil disruptions, they earn nothing for those hours. A typical partner earns ₹600–₹1,200 per day and operates across 5–6 days per week.

Because dark stores are geo-fixed (not spread across a city like restaurant hubs), the risk for Q-commerce partners is **hyper-local and zone-specific**. A partner assigned to a dark store in a waterlogging-prone ward of Chennai or a flood-adjacent zone in Bangalore faces materially different risk than one operating in an elevated, well-drained area. GigShield prices this at the zone level — not at the city level.

### Scenario walkthroughs

**Scenario A — Heavy rainfall, Chennai (July)**
Karthik operates from the Zepto dark store in Velachery. At 4:00 PM, rainfall exceeds the parametric threshold (≥ 50mm in 3 hours) for his zone. The Zepto platform automatically suspends deliveries in that zone. GigShield's trigger engine detects the IMD weather event, cross-validates that platform orders in the zone dropped by more than 80%, and fires an automatic income-replacement payout of ₹180 (3 hours × average hourly rate) to Karthik's UPI handle — with no action required from him.

**Scenario B — High AQI event, Delhi NCR (November)**
Priya works from the Blinkit dark store in Dwarka. AQI crosses 400 (Severe category) and the state issues an outdoor worker advisory. GigShield's AQI trigger fires. Since Priya has maintained a 90-day clean claims history, her behavioral trust score gives her an expedited approval path. Payout is credited in under 4 minutes.

**Scenario C — Flash curfew, Bangalore**
Rohan is mid-shift when Section 144 is imposed in his delivery zone following a local protest. GigShield's civil disruption feed (sourced from government notification APIs and validated against GPS inactivity) detects the curfew, confirms that Rohan's device shows no movement for 45 minutes during an active policy window, and initiates a payout for the lost shift hours.

**Scenario D — Platform-level app outage (novel trigger)**
Divya is logged into the Blinkit partner app when a confirmed platform outage lasting more than 60 continuous minutes is detected via third-party uptime monitoring. Since this is beyond her control, GigShield treats it as a covered income disruption and credits a proportional payout.

**Scenario E — Legitimate worker, poor network (Real-world fairness)**
Ananya is legitimately stranded in a flood zone but her GPS starts drifting 500 meters away due to multipath reflection in heavy rain (a documented real-world phenomenon in urban canyons). Instead of auto-rejecting her claim as spoofed, GigShield's contextual validator recognizes the weather conditions, validates her device motion against expected escape routes, checks her platform app logs, and confirms her claim with confidence instead of unfairly penalizing her.

---

## 2. Application Workflow

```
Onboarding → Risk Profiling → Policy Creation → Active Monitoring → Trigger Detection → Claim → Validation → Payout
```

### Step 1: Onboarding (LLM-powered, multilingual)

The onboarding flow is handled by an LLM chatbot (Claude API) embedded in the web application. The chatbot supports English, Hindi, Tamil, and Kannada. Workers are guided through:

- Platform they deliver for (Zepto / Blinkit / Swiggy Instamart)
- Their assigned dark store zone (pin code or map pin)
- Average weekly earnings (self-declared, cross-referenced with platform estimates)
- Work schedule pattern (days per week, typical shift hours)
- Device information (for behavioral baseline establishment — with consent)

The chatbot dynamically adjusts its questions based on previous answers. A worker who indicates they operate double shifts gets prompted for split-shift income details. The entire onboarding takes under 3 minutes and requires no form-filling.

### Step 2: Automated risk profiling + behavioral baseline capture

Immediately after onboarding, GigShield's **GigBrain** causal risk engine generates a zone risk score for the worker's dark store location. Simultaneously, the system captures an initial behavioral baseline:

- Typical work hours and location patterns (from first 2 weeks of active monitoring)
- Device fingerprint characteristics
- Network behavior (typical network provider, connection type)
- Platform app interaction patterns
- Delivery completion rates and consistency

This baseline is **never** shared with third parties but is used internally for anomaly detection.

### Step 3: Policy creation and payment

The worker is shown a clear, one-screen policy summary:

- Coverage amount per disruption event (calculated from their declared weekly earnings)
- Maximum payable disruptions per week (capped at 3)
- Weekly premium (₹49–₹149 depending on zone risk and earnings level)
- Coverage window (active from Monday 00:00 to Sunday 23:59)

The worker pays the weekly premium via UPI or debit card. The policy activates instantly. Renewals are automatic with a 24-hour opt-out window each Sunday.

### Step 4: Active monitoring (zero-touch, privacy-first)

Once the policy is active, GigShield continuously monitors:

- Weather conditions at the dark store zone (Open-Meteo API, IMD)
- AQI levels (CPCB API)
- Platform order volume proxy (simulated via mock API in Phase 1)
- Civil event feeds (government gazette notifications + news API)
- Platform uptime (UptimeRobot or similar public monitors)
- **[NEW]** Worker behavioral patterns at a zone and cluster level (aggregate-only, no individual tracking)

The worker does not need to do anything during an active policy week.

### Step 5: Trigger detection and multi-layer validation

When a parametric trigger fires:

1. **Geographic validation:** Trigger location is confirmed against the worker's registered dark store
2. **Temporal validation:** Worker's recent activity patterns match the trigger window
3. **Device context validation:** Device motion, network type, and app state are analyzed
4. **Behavioral anomaly detection:** Claim is validated against both individual baseline and cluster patterns
5. **Coordinated fraud detection:** System checks for statistical anomalies in claim clustering
6. **Contextual reasoning:** AI model evaluates claim credibility given real-world conditions

The worker receives an in-app push notification and SMS explaining what happened, what payout they will receive, and if flagged, why, with a clear human review timeline.

### Step 6: Payout or review

- **Auto-approved claims (95% honest claims expected):** Approved payouts credited via UPI within 5 minutes of claim completion
- **Flagged claims (network anomalies, edge cases):** Escalated to human underwriter within 2 hours with full context
- **High-confidence fraud signals:** Held for investigation with the worker notified of the hold reason and 48-hour escalation policy

---

## 3. Weekly Premium Model

### Why weekly

Q-commerce delivery partners are paid weekly by their platforms (Zepto, Blinkit pay every Monday or Tuesday for the prior week). Aligning the insurance premium cycle to the platform payment cycle means GigShield can offer a seamless auto-debit experience where the premium is collected at the same time the worker receives their earnings — reducing friction and lapse risk dramatically.

### Premium structure

The weekly premium is calculated by GigBrain using the following formula:

```
Weekly Premium = Base Rate × Zone Risk Multiplier × Earnings Band Multiplier × Trust Score Discount × Fraud Signal Adjustment*
```

**Base rate:** ₹79 per week (covers up to ₹500 per disruption event, up to 3 events per week)

**Zone risk multiplier (0.7 – 1.5):**
Derived from the historical disruption frequency index for the worker's dark store zone. Low-risk zones (elevated terrain, low flood history, low AQI breach count) get a sub-1.0 multiplier. High-risk zones (coastal low-lying areas, industrial-adjacent high-AQI zones) get up to 1.5×.

**Earnings band multiplier:**
| Declared weekly earnings | Coverage cap | Multiplier |
|---|---|---|
| ₹2,000 – ₹3,500 | ₹400/event | 0.9× |
| ₹3,500 – ₹5,000 | ₹600/event | 1.0× |
| ₹5,000 – ₹7,500 | ₹900/event | 1.2× |
| ₹7,500+ | ₹1,200/event | 1.5× |

**Trust score discount (0% – 20% off):**
Workers accumulate a behavioral trust score over time (see Section 5). After 8 weeks of clean claims history, they become eligible for up to 20% premium reduction. This is a long-term retention and fraud-prevention mechanism.

**Fraud signal adjustment (±0% premium modification):**
Workers exhibiting minor anomaly patterns (not fraud, but unusual) may temporarily pay +5% premium pending normalization. This is transparent and documented.

**Example calculation:**
Karthik earns ₹4,200/week, operates in a zone with risk multiplier 1.2, and has no trust score history yet.

```
₹79 × 1.2 × 1.0 × 1.0 × 1.0 = ₹94.80/week → rounded to ₹95/week
```

### Payout calculation

When a trigger fires, the income replacement amount is calculated as:

```
Payout = (Daily earnings ÷ average working hours) × hours disrupted
```

Disruption hours are determined from the parametric event timestamp and duration, capped at 6 hours per trigger event and 3 trigger events per policy week.

---

## 4. Parametric Triggers

GigShield implements five parametric triggers for Q-commerce delivery income loss. Each trigger has a defined data source, threshold, and validation method.

### Trigger 1: Heavy rainfall
- **Data source:** Open-Meteo API + IMD district alerts
- **Threshold:** ≥ 50mm cumulative rainfall in any 3-hour window at the dark store zone coordinates
- **Validation:** Cross-referenced with IMD Nowcast alert for the district; platform order volume drop > 70% in the zone (mock API in Phase 1); worker location remains within 2km of dark store zone
- **Covered loss:** Hours during which rainfall exceeds threshold AND platform deliveries are suspended

### Trigger 2: Severe air quality (AQI)
- **Data source:** CPCB Air Quality API (free tier)
- **Threshold:** AQI ≥ 400 (Severe category) sustained for ≥ 2 hours at the nearest CPCB monitoring station to the dark store
- **Validation:** State outdoor worker advisory issued (scraped from government notification feed); worker GPS and app activity during the window is analyzed; correlated with zone-level order suspension data
- **Covered loss:** Hours during Severe AQI window within active policy period

### Trigger 3: Flood or waterlogging
- **Data source:** IMD flood alerts + city municipal flood sensor feeds (mock for Phase 1)
- **Threshold:** Official flood/waterlogging alert issued for the pin code zone of the dark store
- **Validation:** Cross-referenced with IMD flood bulletin; platform zone suspension confirmation (mock API); worker's device accelerometer shows stationary or slow movement (not normal delivery movement)
- **Covered loss:** Full disruption duration as stated in the alert

### Trigger 4: Civil disruption (curfew / Section 144)
- **Data source:** Government e-gazette API + curated news event feed (NewsAPI)
- **Threshold:** Section 144 or equivalent restriction confirmed in the delivery zone
- **Validation:** Two independent news sources confirm the event; worker GPS inactivity during the declared window; no anomalous device activity (such as abrupt distance jumps)
- **Covered loss:** Duration of the declared restriction

### Trigger 5: Platform app outage (novel trigger — unique to GigShield)
- **Data source:** UptimeRobot public status monitors + IsItDownRightNow / DownDetector API for partner platforms
- **Threshold:** Confirmed platform-level outage of ≥ 60 continuous minutes, reported across ≥ 500 user reports or confirmed by the platform's own status page
- **Validation:** Cross-referenced across two uptime monitoring sources; worker must be in an active session (logged in, GPS active) at onset; app logs confirm repeated connection attempts during outage window
- **Covered loss:** Hours during the outage window where the worker was demonstrably ready to work

---

## 5. Adversarial Defense & Anti-Spoofing Strategy

**The Threat:** Organized fraud syndicates exploit parametric insurance by coordinating GPS spoofing attacks, using fake locations to claim payouts while remaining physically safe. GigShield's defense strategy moves beyond naive location-checking to forensic multi-layer contextual validation.

### 5.1 The Differentiation: Individual Behavioral Profiles vs. Coordinated Anomalies

Traditional GPS-only verification fails because determined attackers can spoof coordinates. GigShield's approach is fundamentally different:

**Layer 1: Individual Baseline Behavioral Profile (Established in first 2 weeks)**
Each worker's profile captures:
- Daily delivery density and route entropy (how varied the delivery patterns are)
- Typical speed distributions (acceleration/deceleration patterns)
- Platform app interaction latency (how quickly they respond to new orders)
- Network provider stability patterns (which carriers they use, typical signal strength)
- Time-of-day preferences (when they typically work)
- Zone stickiness (do they consistently stay within 1.5km of the dark store, or are there unexplained excursions)

A genuine stranded delivery partner trying to escape a flood zone will show:
- **Sustained higher-than-normal velocity** (running/cycling out of the zone)
- **Directional coherence** (consistent direction away from hazard, not random jumps)
- **Repeated app check-ins** (checking if deliveries resume, trying to log in)
- **Network provider consistency** (even in emergencies, they use their phone)

A spoofing attacker shows:
- **Teleportation** (GPS jumps of 1000+ meters in seconds, physically impossible)
- **Velocity anomalies** (speeds exceeding 200 km/h, then drops back to 0)
- **Stationary phone behavior** (accelerometer shows no movement, but GPS claims rapid movement)
- **No app interaction pattern** (worker never tries to log in or check for deliveries)
- **Network signature shift** (suddenly connecting from different country IP, carrier change inconsistent with known patterns)

**Layer 2: Cluster-Level Anomaly Detection (Fraud Ring Pattern Recognition)**

When 500 workers from a single Telegram group file identical claims at the time for the same event, statistical patterns emerge:

- **Timestamp clustering:** 10+ claims within 30 seconds → statistical anomaly
- **Zone cluster coherence:** Claims originate from workers spanning 50+ km radius but device fingerprints show same WiFi provider fingerprint
- **Velocity impossibility:** Claimed "escape" movements show multiple workers moving at 80 km/h in residential neighborhoods with poor networks
- **Correlated on-off timing:** Workers claim to go offline at the exact same second (real disruptions have 10-60 second variance)
- **Behavioral class anomaly:** Workers with 2 days of history suddenly show confident travel patterns that took legitimate workers 2 weeks to develop

GigShield's Fraud Graph AI detects these macro patterns in real-time.

### 5.2 The Data: Multi-Dimensional Contextual Signals

Beyond GPS, GigShield analyzes:

**Device-Level Signals:**
- **Accelerometer/Gyroscope data:** Confirms motion consistency. Spoofed GPS will have zero accelerometer activity while claiming rapid movement.
- **Barometer data:** Detects elevation changes. A worker claiming to escape a flooded area should show elevation increase. Spoofed claims will not.
- **Battery drain patterns:** Emergency movement (running with active GPS, constant app refreshing) causes 5-8% battery drain per hour. Stationary actors show near-zero drain.
- **Cell tower triangulation:** Cross-validates GPS against cell tower hop patterns (which towers the device connects to as it moves). Spoofed GPS will have discontinuous tower patterns.

**Network & Device Fingerprint:**
- **DNS query patterns:** Spoofed devices often use VPNs or proxies, leaving DNS request signatures. Genuine emergency devices use their normal network stack.
- **TLS certificate properties:** Enterprise VPNs and location-spoofing apps have distinctive TLS handshake signatures.
- **Device identifier consistency:** Real devices maintain consistent build properties, hardware identifiers, and Android/iOS versions. Spoofing apps often mask hardware ID, creating detectable inconsistency.
- **NTP timestamp skew:** Spoofing apps often rely on local device clocks that gradually drift, detectable by NTP query analysis.

**Behavioral & Temporal Context:**
- **Platform app engagement:** Genuine stranded workers repeatedly try to accept or reject orders, refresh the app, check status. Fraudsters claim outages while showing zero app engagement.
- **UPI/Payment interaction:** Legitimate workers check their earnings and payout status during downtime. Fraudsters do not.
- **Social signal correlation:** Check if the worker's phone contacts or email correspond to known legitimate gig worker communities. Coordinated fraud rings often use VOIP numbers or shared email domains.
- **Claim consistency across time:** Legitimate workers, if they made a false claim once (mistakenly), do not repeat it. Organized fraudsters follow scripts.

**Environmental & Geographic Context:**
- **Weather data validation:** Is the claimed weather hazard actually present at the claimed location? Cross-reference satellite rain/cloud data against claimed rainfall. Cross-validate AQI at multiple stations.
- **Platform order data:** Genuine disruptions show 70%+ order volume drop (confirmed via platform APIs). Fraudulent claims may occur during normal order flow.
- **Civil disruption multi-source validation:** Curfew claims are validated against government gazette, news, police dispatch data (where available via public APIs). Spoofed curfew claims fail this validation.
- **Dark store closure state:** Is the dark store actually closed? Cross-reference with Zepto/Blinkit operational data (if accessible via partnerships or public APIs).

**Graph Database Patterns (Real-Time Cluster Detection):**
- **Common UPI wallet:** Multiple workers claiming same event with UPI wallets sequentially numbered (wallet_999, wallet_1000, wallet_1001) → likely test ring
- **Same device ID, different workers:** Multiple accounts using the same device fingerprint → coordinated fraud
- **Timezone anomaly:** Workers claiming to be in India but using devices set to Singapore timezone → likely VPN/spoofing
- **Claim velocity:** A worker with 1 week history claims 3 times in 1 week (peak rate). Workers with 52 weeks history claim 0.5 times per week. Statistical outlier detection flags new workers claiming at rates only 0.1% of legitimate population achieve.

### 5.3 The UX Balance: Fair Handling of Flagged Claims

The critical constraint: **Never unfairly penalize a legitimate worker experiencing real-world network issues or edge-case scenarios.**

**Tiered Flagging & Review Strategy:**

**Tier 1: Auto-Approved (Green Flag)**
Confidence score > 95%. Claim shows:
- Behavioral consistency with worker's history
- Environmental validation confirms trigger event occurred
- No anomalies detected
- **Action:** Payout credited within 5 minutes. Worker sees: "✅ Trigger confirmed. Your payout of ₹X has been credited."

**Tier 2: Fast-Track Review (Yellow Flag)**
Confidence score 75–95%. Claim shows minor anomalies:
- GPS drift observed but device motion/accelerometer data consistent
- Network handoff during claim window (real-world scenario in mobile networks)
- Behavioral pattern is within 1-2 standard deviations of baseline
- **Action:** Human underwriter reviews within 60 minutes (SLA: 90 minutes for all yellow flags). System shows underwriter: "Minor GPS drift detected, but accelerometer confirms motion. Claim likely legitimate. Recommend approval." Worker sees: "⏳ We're verifying your claim (typically 30–90 minutes). We found a small network blip, but your movement data looks real. We'll update you soon."

**Tier 3: Deep Investigation (Red Flag)**
Confidence score < 75%. Claim shows multiple concerning signals:
- GPS teleportation detected
- Accelerometer stationary but location changing
- No app engagement during claimed disruption
- Cluster anomaly detected (similar pattern to known fraud ring)
- **Action:** Dedicated fraud investigator assigned within 2 hours. System shows:
  - Worker: "🔍 We're investigating your claim to ensure fairness. Unusual activity detected. We'll confirm within 24 hours. If approved, your payout is backdated from claim time."
  - Investigator: "Red flag: GPS teleportation 800m in 3 seconds + zero accelerometer motion + zero app engagement. High fraud probability. Also: Similar device fingerprint to claim from worker123 (same area, same timestamp). GNN fraud graph score: 0.87 (likely coordinated attack)."

**Special Case: Legitimate Edge Cases**

- **Multipath GPS reflection in rain (urban canyon effect):** Recognized as a documented phenomenon in bad weather. If environmental validation shows severe rainfall and device motion is consistent, claim is auto-approved even with GPS drift up to 1 km.
- **Network outage during escape attempt:** Worker's device motion shows escape trajectory, but sudden network loss occurred just as they were leaving. Escalated to fast-track with message: "Network dropped during your escape, but we can see you moved to safety. Approving your claim."
- **Accelerometer malfunction (hardware failure):** Rare. If a worker has 20+ weeks of history with perfect behavior, then suddenly has 1 week of claims with zero accelerometer data, account is flagged for device replacement/re-verification, not denied.

**Transparency & Appeal:**

Every flagged or delayed claim includes:
1. **Clear explanation:** "Your claim was flagged because [specific reason]. This is not a rejection; it's a review."
2. **SLA commitment:** "You will hear from us by [exact time/date]. If approved, your payout is backdated from now."
3. **Appeal path:** Workers can appeal by providing additional context (photos, videos, platform chat logs) via in-app form. Appeals are reviewed by different human underwriter within 4 hours.

**Trust Score Impact:**

- Legitimate claim flagged then approved → +2 trust points (system learned worker's edge case pattern, improves future claims)
- Legitimate claim flagged then denied (if truly fraudulent on investigation) → 0 trust impact (not worker's fault)
- Flagged claim disputed by worker (appeal) and appeal approved → +5 trust points (worker's credibility verified under challenge)

### 5.4 Technical Architecture for Real-Time Detection

**Data Collection Pipeline:**
```
Worker Device
  ├── GPS + IMU (accelerometer, gyroscope, barometer)
  ├── Network metadata (cell tower, DNS, TLS handshake)
  ├── App engagement telemetry
  └── Battery/storage metrics
       ↓
Secure Telemetry Service (encrypted, privacy-first)
       ↓
Real-time Stream Processing (Kafka, RabbitMQ)
       ├── Behavioral Anomaly Detection (PyTorch LSTM)
       ├── Cluster Anomaly Detection (Graph Neural Network)
       ├── Environmental Validation (Open-Meteo, CPCB)
       └── Temporal Consistency Checks
              ↓
        Confidence Scoring Engine (0–100)
              ↓
        Decision Logic (Auto-Approve → Fast-Track → Deep Investigation)
```

**Key Compute Services:**
- **PyTorch LSTM Model:** Trained on 50,000+ legitimate gig worker trajectories to learn normal movement. Flags movements < 1% likelihood under normal conditions.
- **Graph Neural Network (Fraud Ring Detection):** Models worker-claim relationships. Detects subgraphs with > 5 sigma statistical anomaly.
- **Accelerometer-GPS Validator:** Real-time cross-correlation. Detects impossible motion (GPS change without accelerometer motion > 99.5% confidence).

**Privacy Guarantee:**
- GPS and IMU data is never persisted after claim validation (deleted within 24 hours)
- Behavioral baselines are anonymized, stored at zone-cluster level (not individual worker)
- No surveillance outside of claim validation windows

---

## 6. AI and ML Integration

### GigBrain — Causal Risk Engine

GigBrain is the core intelligence layer. It uses **causal inference** (via Microsoft's DoWhy library) rather than simple correlation-based ML to model the actual causal pathways between external disruptions and income loss at the zone level. This is important for insurance accuracy: correlation-based models may overfit to periods of high claim activity, while causal models understand *why* income drops.

GigBrain is trained on:
- 3 years of IMD historical weather data (district level)
- CPCB AQI historical records
- Proxy income loss data derived from food delivery order volume datasets (public research)
- Dark store location coordinates for Zepto and Blinkit (scraped from public maps)
- **[NEW]** Behavioral baseline patterns from 50,000+ legitimate worker trajectories

GigBrain outputs a weekly risk score per zone, which feeds directly into premium calculation. It is retrained monthly as new disruption event data accumulates.

**Phase 1 implementation:** GigBrain runs as a FastAPI microservice. The model is pre-trained on historical data and served as a REST endpoint. Premium calculation calls this endpoint on policy creation.

### Zone Disruption Heatmap

A geospatial layer built using Mapbox GL JS or React-Leaflet visualises historical and predicted disruption risk at the dark store zone level across major Indian cities. This gives both workers (to understand their risk) and insurers (for portfolio management) a live view of where disruption events are occurring.

**Phase 1:** Static heatmap using pre-processed historical data. Phase 2 onwards: live event overlay with real-time trigger detection.

### Fraud Graph AI (GNN-based anomaly detection)

Insurance fraud in gig worker claims typically follows network patterns: clusters of workers claiming the same non-existent event, GPS coordinates that do not match the claimed dark store zone, or claim timestamps that contradict platform activity logs.

GigShield addresses this with a **Graph Neural Network** (PyTorch Geometric) that models workers, claims, dark store zones, and disruption events as nodes in a graph. Edges represent relationships (same zone, same claim window, shared device fingerprint, etc.). The GNN is trained to detect anomalous subgraphs — clusters of claims that do not conform to the physical reality of the triggering event.

**Specific fraud signals detected:**
- **GPS spoofing:** Device reports a location matching a high-risk zone but telemetry velocity and accelerometer data are physically inconsistent (acceleration profile doesn't match claimed movement)
- **Claim velocity fraud:** A worker or cluster of workers files claims at a rate statistically incompatible with the declared disruption duration (e.g., 3 claims in one day when disruptions last 2 hours each)
- **Dark store mismatch:** The worker's claimed zone does not match their registered dark store location, and device motion does not show credible travel
- **Synthetic worker rings:** Multiple new accounts with similar onboarding patterns (device fingerprint, IP subnet, UPI handle prefix) claiming the same event
- **Network signature consistency:** Multiple devices using identical TLS certificate chains or DNS patterns (indicative of shared VPN or spoofing app)
- **Behavioral class clustering:** New workers (< 2 weeks history) exhibiting claim patterns that would take legitimate workers months to develop

**Phase 1 implementation:** A rule-based anomaly detection system is implemented as a proxy for the GNN. The GNN architecture is scaffolded, trained on synthetic fraud data, and ready for Phase 2 with real production behavioral data.

### LLM Onboarding and Claims Companion

GigShield integrates the Claude API (claude-sonnet-4-20250514) to power two conversational interfaces:

**Onboarding chatbot:** Guides the worker through registration in their preferred language. Dynamically adjusts questions based on prior answers. Extracts structured profile data (earnings, zone, schedule) from natural language input and populates the risk profile automatically. Also establishes initial trust through human-like conversation ("How long have you been working as a delivery partner?" → natural context, not just extracting data).

**Claims companion:** When a trigger fires, the chatbot proactively contacts the worker via in-app notification to explain what happened, confirm the payout amount, and answer any questions in natural language. For flagged claims, the chatbot explains the specific anomaly detected and what the investigation process looks like, reducing anxiety and supporting trust.

### Behavioral Trust Score (Enhanced)

Each worker accumulates a trust score over time, updated weekly. The score now incorporates anti-spoofing signals:

**Positive trust factors:**
- Clean claim week (no disputed claims): +5 points
- Claim validated without anomaly flags: +3 points
- GPS data consistent with declared zone throughout policy week: +2 points
- **[NEW]** Behavioral baseline stability (pattern similarity > 90%): +2 points
- **[NEW]** Multiple claims approved, all passing multi-layer validation: +3 points per 10 claims

**Negative trust factors:**
- Disputed or flagged claim: −15 points
- Confirmed fraudulent claim: account suspended
- **[NEW]** Minor anomaly detected (but claim approved after review): −1 point (learning signal, not punishment)
- **[NEW]** Device spoofing app detected: −50 points + immediate investigation + device re-verification required

Trust score tiers:

| Score | Tier | Premium discount | Claim approval path | Payout speed |
|---|---|---|---|---|
| 0–49 | Standard | 0% | Standard review (60-90 min) | 30 min |
| 50–79 | Trusted | 10% | Fast-track (30 min) | 10 min |
| 80–100 | Verified | 20% | Instant auto-approve (no review) | 5 min |

---

## 7. Tech Stack and Architecture

### Frontend
- **Framework:** Next.js 14 (App Router)
- **UI:** Tailwind CSS + shadcn/ui components
- **Maps:** React-Leaflet (zone heatmap and dark store pin selection)
- **Charts:** Recharts (worker dashboard earnings and payout history)
- **Auth:** Supabase Auth (phone OTP — appropriate for the persona)
- **Telemetry:** Custom React hooks for GPS, accelerometer, network metadata collection (privacy-first, opt-in, with clear consent messaging)

### Backend
- **Runtime:** Node.js 20 with Express
- **AI microservice:** Python 3.11 + FastAPI (GigBrain causal risk engine, fraud scoring, multi-layer validation)
- **LLM integration:** Anthropic Claude API (claude-sonnet-4-20250514)
- **Scheduler:** Node-cron (trigger monitoring loop, runs every 5 minutes)
- **Streaming:** Apache Kafka or RabbitMQ (real-time telemetry ingestion for fraud detection)

### Database
- **Primary:** Supabase (PostgreSQL) — workers, policies, claims, payouts, trust scores, **[NEW]** behavioral baselines (anonymized)
- **Geospatial:** PostGIS extension on Supabase for zone-level risk queries
- **Graph DB:** Neo4j (optional, for Fraud Graph GNN; can use Supabase with ltree extension for Phase 1)

### External APIs (Phase 1)
| API | Purpose | Notes |
|---|---|---|
| Open-Meteo | Real-time rainfall + elevation data | Free, no key required |
| CPCB AQI API | Air quality index + satellite rain confirmation | Free government API |
| NewsAPI | Civil disruption detection | Free tier (100 req/day) |
| Razorpay Test Mode | Payout simulation | Sandbox, no real money |
| Anthropic Claude API | LLM chatbot + contextual claims reasoning | claude-sonnet-4-20250514 |
| UptimeRobot (public) | Platform outage detection | Public status pages |
| **[NEW]** Google Play Integrity API | Device spoofing app detection | Flags known GPS spoofing apps installed |

### Architecture overview

```
Browser (Next.js)
    │
    ├── /api/* (Next.js API routes)
    │       │
    │       ├── Node.js / Express service
    │       │       ├── Policy management
    │       │       ├── Trigger monitoring loop
    │       │       ├── Claims orchestration
    │       │       ├── Payout initiation (Razorpay)
    │       │       └── Trust score updates (real-time)
    │       │
    │       └── Python FastAPI service
    │               ├── GigBrain risk scoring
    │               ├── Multi-layer claim validation
    │               │   ├── Accelerometer-GPS validator
    │               │   ├── Behavioral anomaly detection (LSTM)
    │               │   ├── Cluster anomaly detection (GNN)
    │               │   └── Environmental context validator
    │               └── Fraud confidence scoring
    │
    ├── Telemetry Stream (Kafka / RabbitMQ)
    │       │
    │       ├── Real-time telemetry ingestion (GPS, IMU, network metadata)
    │       ├── Stream processing pipeline
    │       └── Anomaly detection on streaming data
    │
    └── Supabase (Postgres + Auth + Realtime + PostGIS)
```

### Repository structure

```
gigshield/
├── apps/
│   ├── web/               # Next.js frontend
│   └── api/               # Node.js Express backend
├── services/
│   ├── gigbrain/          # Python FastAPI ML service
│   │   ├── models/
│   │   │   ├── causal_risk_model.py     # DoWhy causal inference
│   │   │   ├── lstm_behavioral.py       # Behavior anomaly detection
│   │   │   ├── gnn_fraud_detector.py    # Fraud ring detection
│   │   │   └── accelerometer_validator.py # Physics-based validation
│   │   ├── endpoints/
│   │   │   ├── risk_scoring.py
│   │   │   └── claim_validation.py
│   │   └── config/
│   │       └── model_configs.yaml
│   └── telemetry/         # Streaming data pipeline
│       └── validators.py
├── packages/
│   └── shared/            # Shared TypeScript types + security utilities
├── docs/
│   ├── architecture/
│   │   ├── anti_spoofing_threat_model.md
│   │   ├── data_collection_privacy.md
│   │   └── fraud_detection_faq.md
│   └── api/
│       └── claim_validation_spec.md
└── README.md
```

---

## 8. Development Plan

### Phase 1 (Weeks 1–2): Ideation and Foundation + **Adversarial Defense** — CURRENT

- [x] Persona research and scenario definition
- [x] Premium model design and parametric trigger specification
- [x] **[NEW] Adversarial Defense & Anti-Spoofing Strategy (comprehensive)**
- [x] AI and ML architecture planning with fraud resistance
- [ ] Next.js project scaffold with Supabase auth
- [ ] Worker onboarding UI (chatbot shell with Claude API integration)
- [ ] GigBrain risk engine (rule-based proxy + FastAPI endpoint)
- [ ] Multi-layer claim validation engine (confidence scoring)
- [ ] Policy creation flow with weekly premium calculation
- [ ] Basic telemetry collection framework (with privacy controls)

### Phase 2 (Weeks 3–4): Automation and Anti-Fraud Protection

- [ ] Full registration and KYC-lite flow
- [ ] Live trigger monitoring loop (Open-Meteo, CPCB, NewsAPI)
- [ ] 5 automated parametric trigger implementations
- [ ] Claims management UI with tiered review (auto-approve → fast-track → investigation)
- [ ] **Real-time multi-layer validation:**
  - Accelerometer-GPS cross-validation
  - Behavioral baseline anomaly detection
  - Cluster-level fraud ring detection
  - Environmental context validation
- [ ] Dynamic premium calculation with zone heatmap
- [ ] Razorpay sandbox payout integration
- [ ] In-app fraud explanation UI (clear, non-blaming messaging for flagged claims)
- [ ] Trust score system with behavioral signals

### Phase 3 (Weeks 5–6): Scale, Optimize, and Harden

- [ ] GNN fraud detection model (PyTorch Geometric, trained on synthetic + production data)
- [ ] GPS spoofing app detection (Google Play Integrity integration)
- [ ] Accelerometer pattern library (detect known spoofing acceleration profiles)
- [ ] Behavioral trust score dashboard (worker view + admin analytics)
- [ ] Dual dashboard (worker view + admin/insurer view)
- [ ] Predictive disruption forecasting (next-week risk alerts)
- [ ] Full end-to-end simulation (simulated fraud ring attack, system response)
- [ ] Security audit + penetration testing (adversarial scenarios)
- [ ] Final pitch deck (PDF) with anti-spoofing case studies
- [ ] API documentation + integration guide for insurers

---

## 9. Innovation Beyond the Brief

GigShield introduces four innovations that go beyond the hackathon's required feature set, each addressing a real gap in the gig insurance landscape.

### Innovation 1: Dark store zone heatmap with micro-level risk pricing

No existing gig insurance product prices risk at the dark store zone level. GigShield maps every major Zepto and Blinkit dark store in India's top 10 cities and associates each with a hyper-local risk score derived from 3 years of IMD and CPCB data. This means a partner working in a waterlogging-prone ward of Mumbai's Dharavi area pays a different premium than one operating from a dark store on elevated terrain in the same city — which is actuarially correct. This level of precision is novel in the Indian insurtech space and is practically achievable because Q-commerce partners are geo-fixed to dark stores, unlike food delivery riders.

### Innovation 2: Platform app outage as a covered parametric event

The existing literature on parametric gig insurance focuses exclusively on weather and civil disruption. GigShield is the first platform to cover income loss from confirmed platform-level technology failures — a genuine and frequent income disruption for Q-commerce workers, who have no alternative income channel when Zepto or Blinkit goes down. This trigger is validated across multiple public uptime monitors to prevent spurious claims and is restricted to outages of 60+ minutes to avoid noise from transient connectivity issues.

### Innovation 3: Behavioral trust score as a dynamic pricing and fraud prevention mechanism

Traditional insurance fraud detection is reactive (catch bad claims). GigShield's trust score system is proactive: it continuously rewards legitimate claimants with lower premiums and faster payouts, creating a strong incentive against fraudulent behavior. Workers who build a high trust score over months pay 20% less and receive instant payouts — a tangible loyalty benefit. Workers who file suspicious claims lose score quickly and face increased scrutiny. This turns the fraud detection problem into a behavioral economics problem, which is far more scalable than adversarial detection alone.

### **Innovation 4: Multi-Layer Physics-Based Anti-Spoofing Defense**

GigShield is the first gig insurance platform to combine **device behavioral fingerprinting** (accelerometer, barometer, network context), **real-time cluster anomaly detection** (Graph Neural Networks), and **contextual environmental validation** to prevent organized GPS spoofing attacks. Unlike simple GPS verification (which can be spoofed), this approach detects the *forensic contradiction* between claimed location changes and physical motion. The system distinguishes between a legitimate worker experiencing network issues in bad weather and a bad actor spoofing from home. This architecture is resilient to the current threat landscape and is transparent to users — no surveillance, only claim-validation-time telemetry collection.

---

## Team

| Name | Role |
|---|---|
| Allam Keerthana Chowdary | Full-stack development (Next.js, Node.js, telemetry) |
| Molakalapalli Rishitha | ML engineering (GigBrain, fraud detection, behavioral modeling) |
| Mettu Sathvika | Product design, backend integration, claims UX |

---

## Appendix: Security and Privacy

### Data Collection Principles

1. **Minimal & Purposeful:** Telemetry (GPS, IMU, network metadata) is collected only during claim validation windows, not continuously.
2. **Transient Storage:** All raw telemetry is deleted within 24 hours of claim validation. Only aggregated behavioral baselines are retained (anonymized at zone level).
3. **Consent & Transparency:** Workers are informed at onboarding that telemetry is used for "claim accuracy and fraud prevention" and can view exactly what data is collected.
4. **Cryptographic Anonymization:** Behavioral profiles are hashed and stored as zone aggregates, not individual records.

### Fraud Defense Limitations & Transparency

- **Spoofing detection is probabilistic, not deterministic:** GigShield aims for < 5% false positive rate (wrongly flagging legitimate workers). Some sophisticated attacks may slip through in Phase 1. This is acceptable because:
  - Phase 1 is limited to simulation; Phase 2 collects production data to improve the model
  - Even with 5% false positives, the appeal + fast-track review process ensures fair outcomes
  - No legitimate worker is permanently denied

- **Evolving adversaries:** As attackers develop new spoofing techniques, GigShield's models will be retrained monthly. This is an ongoing arms race, but the architecture is designed to adapt.

### Regulatory Compliance

- **Insurance regulation:** Parametric triggers are pre-defined and objective (not subjective judgment by underwriters), which simplifies regulatory review.
- **Data protection:** GDPR-compliant consent + right-to-deletion for raw telemetry.
- **Algorithm transparency:** Workers can request explanations for claim denials ("why was my claim flagged?"), and GigShield provides clear, non-technical summaries.

---

## Links

- **GitHub Repository:** https://github.com/rishithaa9/GigShield.git
- **2 Minute Video"** : https://drive.google.com/file/d/1T0DdWZWvkLqbJ9HojMkGn7TsEvHAvKfQ/view?usp=sharing

---
