# Meridian CX — System Design & Metric Definitions

### System Design Document · Prototype
**Version:** 1.0  
**Dataset:** Synthetic · 40 accounts · 177 users  
**Status:** Prototype · pre-production  

This document defines the reasoning behind every metric in the lifecycle automation system, and specifies how each data point is sourced, calculated, and maintained. It is intended as both a reference for operators using the dashboard and a technical specification for teams implementing the system in a live environment.

---

## 📋 Metric Definitions
Every metric in this system has a defined formula, a reason it was chosen over alternatives, explicit thresholds that trigger actions, and an honest statement of what it does not capture. *Metrics without documented limitations tend to be over-trusted.*

### 1. Health Score (0–100)
A composite account-level signal that rolls up usage, satisfaction, and communication engagement into a single number. It is the primary indicator used to place accounts in the lifecycle flow and trigger internal alerts.

*   **Why it matters:** A single number that any team member can read at a glance without needing to interpret multiple data points. It also enables consistent alerting logic without subjective judgment calls.
*   **Data Sources:** Product analytics, NPS tool, Email platform

#### Formula (Weighted Average)
| Component | Weight | Input | Notes |
| :--- | :--- | :--- | :--- |
| **Usage score** | 40% | `login_frequency_30d + session depth` | Normalised 0–100 against plan benchmark. |
| **Feature adoption** | 25% | `feature_adoption_score` | % of persona-relevant features activated. Weighted by feature tier. |
| **NPS / satisfaction** | 20% | `nps_score (0–10)` | Normalised to 0–100. Stale NPS (>90 days) is down-weighted by 50%. |
| **Comms engagement** | 15% | `comms_engagement enum` | active=100, passive=50, unresponsive=0. Based on 30d open/click rates. |

#### Thresholds & Actions
*   🟢 **70–100 (Healthy):** Monitor. Flag for expansion or advocacy if sustained >90 days.
*   🟡 **40–69 (At watch):** CS to review monthly. Check which component is dragging the score.
*   🔴 **0–39 (At risk):** Trigger low engagement or churn risk alert depending on churn risk score.

> ⚠️ **What it doesn't capture:** Sentiment outside the platform (social, reviews), quality of usage (logging in without doing anything meaningful), or team-level dynamics (a champion leaving).

---

### 2. Churn Risk Score (0.0–1.0)
A predictive score estimating the probability that an account will not renew or will cancel within the next 90 days. It is directional, not actuarial — it is designed to prompt human investigation, not to be treated as a precise forecast.

*   **Why it matters:** Lagging indicators like cancellation requests arrive too late to act on. Churn risk is a leading signal that gives the CS and AM team a 60–90 day window to intervene.
*   **Data Sources:** Product analytics, CRM, NPS tool, Email platform

#### Formula (Weighted Composite)
| Component | Weight | Input | Notes |
| :--- | :--- | :--- | :--- |
| **Usage trend** | 30% | `% change in login_frequency vs prior 30d` | A 40%+ drop in a single month is a strong signal. |
| **Health score trajectory** | 25% | `health_score delta over 60d` | A declining health score trend matters more than a single low reading. |
| **NPS recency & score** | 20% | `nps_score + days since last NPS` | NPS ≤5 contributes heavily. No NPS in >90 days is a mild risk signal. |
| **Renewal proximity** | 15% | `days_to_renewal` | Risk weight increases non-linearly as renewal approaches. |
| **Comms disengagement** | 10% | `comms_engagement` | Unresponsive to 3+ consecutive touches is a meaningful predictor. |

#### Thresholds & Actions
*   🟢 **0.0–0.35 (Low risk):** No action required. Standard lifecycle comms continue.
*   🟡 **0.36–0.55 (Moderate risk):** CS to review. Consider proactive check-in or help centre nudge.
*   🟠 **0.56–0.70 (Elevated risk):** Trigger risk reactivation email. Flag to CS team.
*   🔴 **0.71–1.0 (Critical risk):** Trigger churn risk alert. AM personal outreach within 24 hours.

> ⚠️ **What it doesn't capture:** External factors (budget cuts, company acquisition, champion departure). These require human intelligence.

---

### 3. Feature Adoption Score (0–100)
The percentage of persona-relevant features that a user has meaningfully engaged with — not just visited, but used in a way that reflects the feature's intended purpose.

*   **Why it matters:** Breadth of feature adoption is one of the strongest predictors of long-term retention. Customers using one feature are fragile. Customers embedded across five are sticky.
*   **Data Sources:** Product analytics

#### Logic Components
*   **Feature set:** Per-persona feature list. Account owners are scored against admin/reporting, managers against workflow/collaboration, and limited users against task-level features.
*   **Engagement threshold:** Minimum usage count per feature. A feature is counted as 'adopted' only after 3+ meaningful interactions within 30 days to filter out accidental clicks.
*   **Recency weighting:** Features used in the last 14 days count fully; 15–45 days ago count at 50%; 45+ days are excluded.

#### Thresholds & Actions
*   🟢 **75–100 (Power user):** Flag for advocacy track. Candidate for beta access or referral ask.
*   🟡 **40–74 (Developing):** Target with JTBD-specific nudges for unused relevant features.
*   🔴 **0–39 (Surface user):** High churn risk. Trigger feature spotlight sequence.

> ⚠️ **What it doesn't capture:** Quality of usage. A user could be running the same report 30 times a day without extracting real value.

---

### 4. Comms Engagement (Active / Passive / Unresponsive)
A rolled-up signal of how a user is responding to outbound communications across email and in-app channels over the last 30 days.

*   **Why it matters:** A user who stops opening emails before they stop logging in is showing early disengagement. Comms engagement is often the first signal to move before usage metrics drop.
*   **Data Sources:** Email platform, Product analytics (in-app)

#### Classification Logic
*   🟢 **Active:** Opened or clicked ≥1 email AND interacted with ≥1 in-app message in last 30d. *Action:* Standard cadence. Test new CTAs.
*   🟡 **Passive:** Opened ≥1 email but no clicks, OR no in-app interaction, in last 30d. *Action:* Review subject lines and send times. Consider channel switch to in-app.
*   🔴 **Unresponsive:** No email open AND no in-app interaction in last 30d. *Action:* Escalate to human outreach. Pause automated sequence to avoid spam flagging.

---

### 5. External Signals (High / Medium / Low Intent)
Indicators of growth, momentum, or change at a customer's company that are observable outside the platform.

*   **Data Sources:** Clearbit / Apollo, LinkedIn Sales Navigator, Google Alerts / press monitoring
*   ⭐ **High Intent (Funding / Hiring):** Seed/Series A–D rounds or open roles posted in relevant departments. *Action:* AM to reach out within 48 hours. Prioritise expansion conversation.
*   🔹 **Medium Intent (Product Launch / Big Logo):** New product versions or public customer wins. *Action:* Personalise next touch with congratulations and case study.
*   ⚪ **Low Intent (Buzz):** Press coverage, social mentions, or industry awards. *Action:* Reference in next scheduled communication. No urgency.

---

## ⚙️ Integration Spec & Data Flow
Each integration is defined at the concept level first — what problem it solves and what data contract it must fulfil — before naming specific tools.
