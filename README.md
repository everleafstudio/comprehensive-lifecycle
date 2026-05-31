<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Meridian CX — System Design & Metric Definitions</title>
<link href="https://fonts.googleapis.com/css2?family=DM+Mono:wght@400;500&family=Fraunces:ital,opsz,wght@0,9..144,300;0,9..144,400;0,9..144,500;1,9..144,300&family=DM+Sans:wght@300;400;500&display=swap" rel="stylesheet">
<style>
:root{--bg:#0e0f11;--bg2:#16181c;--bg3:#1e2128;--bg4:#242830;--border:rgba(255,255,255,0.07);--border2:rgba(255,255,255,0.13);--text:#e8e6df;--text2:#9b9890;--text3:#5a5855;--accent:#c8b560;--accent2:#8eb89a;--danger:#d46b5a;--warn:#d49a4a;--info:#6a9fd4;--purple:#9b84c8;--teal:#78c4b0;--font-d:'Fraunces',Georgia,serif;--font-b:'DM Sans',sans-serif;--font-m:'DM Mono',monospace;--r:8px;--r2:12px;--max:900px}
*,*::before,*::after{box-sizing:border-box;margin:0;padding:0}
body{background:var(--bg);color:var(--text);font-family:var(--font-b);font-size:14px;line-height:1.7;min-height:100vh}

.doc{max-width:var(--max);margin:0 auto;padding:60px 40px 100px}

/* HEADER */
.doc-header{margin-bottom:60px;padding-bottom:32px;border-bottom:1px solid var(--border)}
.doc-eyebrow{font-family:var(--font-m);font-size:11px;color:var(--text3);text-transform:uppercase;letter-spacing:.1em;margin-bottom:12px}
.doc-title{font-family:var(--font-d);font-size:42px;font-weight:300;color:var(--text);letter-spacing:-0.03em;line-height:1.15;margin-bottom:16px}
.doc-title span{color:var(--accent);font-style:italic}
.doc-desc{font-size:15px;color:var(--text2);max-width:640px;line-height:1.7}
.doc-meta{display:flex;gap:24px;margin-top:24px;flex-wrap:wrap}
.doc-meta-item{font-family:var(--font-m);font-size:11px;color:var(--text3)}
.doc-meta-item span{color:var(--text2)}

/* NAV */
.doc-nav{display:flex;gap:4px;margin-bottom:48px;border-bottom:1px solid var(--border);padding-bottom:0}
.doc-nav-btn{padding:10px 18px;font-family:var(--font-b);font-size:13px;color:var(--text3);cursor:pointer;border:none;background:none;border-bottom:2px solid transparent;margin-bottom:-1px;transition:all .15s}
.doc-nav-btn:hover{color:var(--text2)}
.doc-nav-btn.active{color:var(--text);border-bottom-color:var(--accent)}

/* SECTION */
.section{display:none}.section.active{display:block}
.section-intro{font-size:14px;color:var(--text2);margin-bottom:36px;max-width:680px;line-height:1.7;padding:16px 20px;background:var(--bg2);border-radius:var(--r);border-left:2px solid var(--accent)}

/* METRIC CARD */
.metric-card{background:var(--bg2);border:1px solid var(--border);border-radius:var(--r2);margin-bottom:20px;overflow:hidden}
.metric-header{padding:20px 24px;cursor:pointer;display:flex;align-items:flex-start;gap:16px;transition:background .1s}
.metric-header:hover{background:var(--bg3)}
.metric-title-block{flex:1}
.metric-name{font-family:var(--font-d);font-size:20px;font-weight:400;color:var(--text);letter-spacing:-0.01em}
.metric-range{font-family:var(--font-m);font-size:11px;color:var(--text3);margin-top:3px}
.metric-desc-preview{font-size:13px;color:var(--text2);margin-top:6px;line-height:1.5}
.metric-sources{display:flex;gap:6px;flex-wrap:wrap;margin-top:10px}
.source-chip{font-family:var(--font-m);font-size:10px;padding:2px 8px;border-radius:4px;background:var(--bg3);color:var(--text3);border:1px solid var(--border)}
.expand-arrow{color:var(--text3);font-size:18px;transition:transform .2s;flex-shrink:0;margin-top:2px}

.metric-body{display:none;padding:0 24px 24px}
.metric-body.open{display:block}

.mb-section{margin-top:20px}
.mb-label{font-size:11px;font-weight:500;color:var(--text3);text-transform:uppercase;letter-spacing:.07em;margin-bottom:10px;padding-bottom:6px;border-bottom:1px solid var(--border)}
.mb-text{font-size:14px;color:var(--text2);line-height:1.7}

/* FORMULA TABLE */
.formula-table{width:100%;border-collapse:collapse;font-size:13px}
.formula-table thead th{text-align:left;padding:8px 12px;font-size:11px;font-weight:500;color:var(--text3);text-transform:uppercase;letter-spacing:.06em;border-bottom:1px solid var(--border);white-space:nowrap}
.formula-table tbody tr{border-bottom:1px solid var(--border)}
.formula-table tbody tr:last-child{border-bottom:none}
.formula-table tbody td{padding:10px 12px;vertical-align:top;color:var(--text2)}
.formula-table tbody td:first-child{color:var(--text);font-weight:500;white-space:nowrap}
.weight-pill{display:inline-block;background:rgba(200,181,96,.1);color:var(--accent);font-family:var(--font-m);font-size:10px;padding:2px 7px;border-radius:3px}

/* THRESHOLD TABLE */
.threshold-row{display:flex;align-items:flex-start;gap:12px;padding:10px 0;border-bottom:1px solid var(--border)}
.threshold-row:last-child{border-bottom:none}
.threshold-band{font-family:var(--font-m);font-size:11px;min-width:100px;flex-shrink:0}
.threshold-label-wrap{min-width:100px;flex-shrink:0}
.threshold-label{display:inline-block;font-size:11px;font-weight:500;padding:2px 8px;border-radius:4px;font-family:var(--font-m)}
.tl-green{background:rgba(142,184,154,.12);color:var(--accent2)}
.tl-amber{background:rgba(212,154,74,.12);color:var(--warn)}
.tl-red{background:rgba(212,107,90,.12);color:var(--danger)}
.tl-gold{background:rgba(200,181,96,.12);color:var(--accent)}
.tl-neutral{background:var(--bg3);color:var(--text3)}
.tl-amber-red{background:rgba(212,107,90,.08);color:var(--warn)}
.threshold-action{font-size:13px;color:var(--text2);flex:1}

.caveat-box{background:var(--bg3);border-radius:var(--r);padding:12px 16px;font-size:13px;color:var(--text2);border-left:2px solid var(--text3);line-height:1.6}
.caveat-box strong{color:var(--text3);font-size:11px;text-transform:uppercase;letter-spacing:.06em;display:block;margin-bottom:4px}

/* INTEGRATION CARD */
.int-card{background:var(--bg2);border:1px solid var(--border);border-radius:var(--r2);margin-bottom:20px;overflow:hidden}
.int-header{padding:20px 24px;cursor:pointer;display:flex;align-items:flex-start;gap:16px;transition:background .1s}
.int-header:hover{background:var(--bg3)}
.int-name{font-family:var(--font-d);font-size:20px;font-weight:400;color:var(--text);letter-spacing:-0.01em}
.int-concept{font-size:13px;color:var(--text2);margin-top:6px;line-height:1.5}
.tools-row{display:flex;gap:6px;flex-wrap:wrap;margin-top:10px}
.tool-chip{font-family:var(--font-m);font-size:10px;padding:2px 8px;border-radius:4px;background:rgba(106,159,212,.08);color:var(--info);border:1px solid rgba(106,159,212,.15)}

.int-body{display:none;padding:0 24px 24px}
.int-body.open{display:block}

.int-grid{display:grid;grid-template-columns:1fr 1fr;gap:12px;margin-top:4px}
.int-kv{background:var(--bg3);border-radius:var(--r);padding:12px 14px}
.int-k{font-size:10px;color:var(--text3);text-transform:uppercase;letter-spacing:.06em;margin-bottom:6px}
.int-v{font-size:13px;color:var(--text2);line-height:1.6}

.fields-list{display:flex;flex-wrap:wrap;gap:6px;margin-top:4px}
.field-chip{font-family:var(--font-m);font-size:10px;padding:2px 8px;border-radius:4px;background:var(--bg3);color:var(--text3);border:1px solid var(--border)}

/* ARCH DIAGRAM */
.arch-wrap{margin:24px 0;background:var(--bg3);border-radius:var(--r2);padding:24px;border:1px solid var(--border)}
.arch-title{font-size:11px;font-weight:500;color:var(--text3);text-transform:uppercase;letter-spacing:.07em;margin-bottom:20px}
.arch-row{display:flex;align-items:center;gap:0;flex-wrap:wrap;justify-content:center;gap:4px}
.arch-node{background:var(--bg2);border:1px solid var(--border2);border-radius:var(--r);padding:10px 14px;font-size:12px;font-weight:500;color:var(--text);text-align:center;min-width:100px}
.arch-node.primary{border-color:rgba(200,181,96,.3);background:rgba(200,181,96,.06);color:var(--accent)}
.arch-arrow{color:var(--text3);font-size:14px;padding:0 4px}
.arch-label{font-family:var(--font-m);font-size:9px;color:var(--text3);text-align:center;margin-top:3px}

/* PRINT */
@media print{
  body{background:#fff;color:#111}
  .doc-nav{display:none}
  .section{display:block!important}
  .metric-body,.int-body{display:block!important}
  .metric-header,.int-header{cursor:default}
  .expand-arrow{display:none}
}

::-webkit-scrollbar{width:4px}
::-webkit-scrollbar-track{background:transparent}
::-webkit-scrollbar-thumb{background:var(--border2);border-radius:2px}
</style>
</head>
<body>
<div class="doc">

  <div class="doc-header">
    <div class="doc-eyebrow">System design document · prototype</div>
    <div class="doc-title">Meridian <span>CX</span><br>Metric Definitions & Integration Spec</div>
    <div class="doc-desc">This document defines the reasoning behind every metric in the lifecycle automation system, and specifies how each data point is sourced, calculated, and maintained. It is intended as both a reference for operators using the dashboard and a technical specification for teams implementing the system in a live environment.</div>
    <div class="doc-meta">
      <div class="doc-meta-item">Version <span>1.0</span></div>
      <div class="doc-meta-item">Dataset <span>Synthetic · 40 accounts · 177 users</span></div>
      <div class="doc-meta-item">Status <span>Prototype · pre-production</span></div>
    </div>
  </div>

  <div class="doc-nav">
    <button class="doc-nav-btn active" onclick="switchSection('metrics',this)">Metric definitions</button>
    <button class="doc-nav-btn" onclick="switchSection('integrations',this)">Integration spec</button>
    <button class="doc-nav-btn" onclick="switchSection('architecture',this)">System architecture</button>
  </div>

  <!-- METRICS -->
  <div class="section active" id="section-metrics">
    <div class="section-intro">Every metric in this system has a defined formula, a reason it was chosen over alternatives, explicit thresholds that trigger actions, and an honest statement of what it does not capture. Metrics without documented limitations tend to be over-trusted.</div>
    <div id="metrics-container"></div>
  </div>

  <!-- INTEGRATIONS -->
  <div class="section" id="section-integrations">
    <div class="section-intro">Each integration is defined at the concept level first — what problem it solves and what data contract it must fulfil — before naming specific tools. This means the spec remains valid if the tooling changes, and makes it readable to stakeholders who are not tool-specific.</div>
    <div class="arch-wrap">
      <div class="arch-title">Data flow overview</div>
      <div class="arch-row">
        <div><div class="arch-node">CRM</div><div class="arch-label">Account & contact</div></div>
        <div class="arch-arrow">→</div>
        <div><div class="arch-node primary">Automation<br>Platform</div><div class="arch-label">Meridian CX core</div></div>
        <div class="arch-arrow">→</div>
        <div><div class="arch-node">Email & In-app</div><div class="arch-label">Touch execution</div></div>
      </div>
      <div style="display:flex;justify-content:center;margin:8px 0;color:var(--text3);font-size:18px">↑ &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; ↑</div>
      <div class="arch-row">
        <div><div class="arch-node">Product Analytics</div><div class="arch-label">Usage & behaviour</div></div>
        <div style="width:60px"></div>
        <div><div class="arch-node">NPS Tool</div><div class="arch-label">Satisfaction</div></div>
        <div style="width:20px"></div>
        <div><div class="arch-node">Enrichment</div><div class="arch-label">External signals</div></div>
        <div style="width:20px"></div>
        <div><div class="arch-node">Slack / CRM</div><div class="arch-label">Internal alerts</div></div>
      </div>
    </div>
    <div id="integrations-container"></div>
  </div>

  <!-- ARCHITECTURE -->
  <div class="section" id="section-architecture">
    <div class="section-intro">This section describes the key architectural decisions made in the system design — why the hybrid account/user model was chosen, how the alert logic is structured to avoid fatigue, and what the implementation sequence looks like for a real deployment.</div>

    <div class="metric-card">
      <div style="padding:24px">
        <div class="mb-label">Hybrid account / user model</div>
        <div class="mb-text" style="margin-bottom:16px">In a B2B lifecycle system, there is a fundamental tension between account-level coherence and user-level responsiveness. Two pure approaches exist, and both have meaningful drawbacks.</div>
        <div style="display:grid;grid-template-columns:1fr 1fr;gap:12px;margin-bottom:20px">
          <div class="int-kv">
            <div class="int-k" style="color:var(--warn)">Account-only model</div>
            <div class="int-v">Simple to manage. Every user in a company gets the same message. Misses individual disengagement until it becomes an account-level problem. A dormant manager goes undetected until the account owner notices.</div>
          </div>
          <div class="int-kv">
            <div class="int-k" style="color:var(--warn)">User-only model</div>
            <div class="int-v">Highly personalised but operationally complex. A company could be in three lifecycle stages simultaneously. AM teams cannot act on individual user signals without account context. Conflicting messages can confuse a single customer.</div>
          </div>
        </div>
        <div class="caveat-box">
          <strong>The hybrid approach</strong>
          The account owns the lifecycle stage and drives renewal, expansion, and AM-level decisions. Users own their own engagement signals and receive persona-targeted messaging within the account's stage. User-level signals bubble up to the account level only when they cross a threshold — specifically, when 50%+ of users on an account are dormant, this triggers an account-level alert rather than individual nudges.
        </div>
      </div>
    </div>

    <div class="metric-card">
      <div style="padding:24px">
        <div class="mb-label">Alert design principles</div>
        <div class="mb-text" style="margin-bottom:16px">Alert fatigue is the primary failure mode of internal notification systems. When every account triggers an alert, no account gets attention. The following principles govern alert design in this system.</div>
        <div style="display:flex;flex-direction:column;gap:10px">
          <div class="threshold-row">
            <div class="threshold-band" style="color:var(--text);font-weight:500;min-width:180px">Every alert has an action</div>
            <div class="threshold-action">No alert fires without a specific recommended action attached. An alert that says "low engagement" without saying "send re-engagement email, review user list, escalate to AM if no response in 48h" is just noise.</div>
          </div>
          <div class="threshold-row">
            <div class="threshold-band" style="color:var(--text);font-weight:500;min-width:180px">Severity drives urgency</div>
            <div class="threshold-action">Critical alerts (churn risk, dormant bubble) fire in real-time to Slack. Opportunity alerts (high satisfaction, advocacy ready) are batched into a daily digest. This preserves the signal value of real-time alerts.</div>
          </div>
          <div class="threshold-row">
            <div class="threshold-band" style="color:var(--text);font-weight:500;min-width:180px">Alerts have cooldown periods</div>
            <div class="threshold-action">The same alert cannot fire for the same account more than once every 14 days unless a new data point changes the underlying condition. This prevents a persistently at-risk account from flooding the CS team.</div>
          </div>
          <div class="threshold-row" style="border:none">
            <div class="threshold-band" style="color:var(--text);font-weight:500;min-width:180px">Start narrow, expand</div>
            <div class="threshold-action">In a real deployment, launch with only the two most critical alerts (churn risk, white glove assignment). Add others over 30-day increments as the team builds the habit of acting on them.</div>
          </div>
        </div>
      </div>
    </div>

    <div class="metric-card">
      <div style="padding:24px">
        <div class="mb-label">Implementation sequence</div>
        <div class="mb-text" style="margin-bottom:20px">A phased approach reduces the risk of building the right system on bad data. Each phase has a clear definition of done before the next begins.</div>
        <div style="display:flex;flex-direction:column;gap:12px">
          <div style="display:flex;gap:16px;align-items:flex-start">
            <div style="font-family:var(--font-m);font-size:11px;color:var(--accent);background:rgba(200,181,96,.1);padding:4px 10px;border-radius:4px;white-space:nowrap;margin-top:2px">Phase 1</div>
            <div><div style="font-weight:500;color:var(--text);margin-bottom:4px">Data foundation</div><div style="font-size:13px;color:var(--text2)">CRM sync clean and verified. Product analytics events mapped to feature taxonomy. Account and user records created with correct persona assignments. No automation yet — just data.</div></div>
          </div><div style="display:flex;gap:16px;align-items:flex-start">
            <div style="font-family:var(--font-m);font-size:11px;color:var(--accent);background:rgba(200,181,96,.1);padding:4px 10px;border-radius:4px;white-space:nowrap;margin-top:2px">Phase 2</div>
            <div><div style="font-weight:500;color:var(--text);margin-bottom:4px">Health scoring</div><div style="font-size:13px;color:var(--text2)">Health score and churn risk score calculating correctly for all accounts. Validate against known at-risk and healthy accounts. Adjust weights based on historical data if available.</div></div>
          </div><div style="display:flex;gap:16px;align-items:flex-start">
            <div style="font-family:var(--font-m);font-size:11px;color:var(--accent);background:rgba(200,181,96,.1);padding:4px 10px;border-radius:4px;white-space:nowrap;margin-top:2px">Phase 3</div>
            <div><div style="font-weight:500;color:var(--text);margin-bottom:4px">Lifecycle placement</div><div style="font-size:13px;color:var(--text2)">All accounts placed into correct lifecycle stage based on enrolled date, health score, and usage signals. AM team reviews placements manually for first 2 weeks to catch misclassifications.</div></div>
          </div><div style="display:flex;gap:16px;align-items:flex-start">
            <div style="font-family:var(--font-m);font-size:11px;color:var(--accent);background:rgba(200,181,96,.1);padding:4px 10px;border-radius:4px;white-space:nowrap;margin-top:2px">Phase 4</div>
            <div><div style="font-weight:500;color:var(--text);margin-bottom:4px">Alert system</div><div style="font-size:13px;color:var(--text2)">Launch churn risk and white glove alerts only. Run for 30 days. Measure response rate and time-to-action. Add low engagement and high satisfaction alerts in phase 5.</div></div>
          </div><div style="display:flex;gap:16px;align-items:flex-start">
            <div style="font-family:var(--font-m);font-size:11px;color:var(--accent);background:rgba(200,181,96,.1);padding:4px 10px;border-radius:4px;white-space:nowrap;margin-top:2px">Phase 5</div>
            <div><div style="font-weight:500;color:var(--text);margin-bottom:4px">Comms automation</div><div style="font-size:13px;color:var(--text2)">Enable automated touch sequences starting with onboarding (lowest risk). Add adoption, retention, and at-risk sequences over 60 days. Advocate and expansion sequences last.</div></div>
          </div><div style="display:flex;gap:16px;align-items:flex-start">
            <div style="font-family:var(--font-m);font-size:11px;color:var(--accent);background:rgba(200,181,96,.1);padding:4px 10px;border-radius:4px;white-space:nowrap;margin-top:2px">Phase 6</div>
            <div><div style="font-weight:500;color:var(--text);margin-bottom:4px">External signals</div><div style="font-size:13px;color:var(--text2)">Enrich accounts with external signal data. Wire expansion opportunity alerts. Train AM team on how to use signal context in outreach conversations.</div></div>
          </div>
        </div>
      </div>
    </div>

  </div>

</div>

<script>
const SPEC = {"metrics":[{"id":"health_score","name":"Health Score","range":"0\u2013100","description":"A composite account-level signal that rolls up usage, satisfaction, and communication engagement into a single number. It is the primary indicator used to place accounts in the lifecycle flow and trigger internal alerts.","why_it_matters":"A single number that any team member \u2014 CS, AM, executive \u2014 can read at a glance without needing to interpret multiple data points. It also enables consistent alerting logic without subjective judgment calls.","formula":{"type":"weighted_average","components":[{"name":"Usage score","weight":0.4,"input":"login_frequency_30d + session depth","notes":"Normalised 0\u2013100 against plan benchmark. A growth account logging in 3x/week scores differently to a free account doing the same."},{"name":"Feature adoption","weight":0.25,"input":"feature_adoption_score","notes":"% of persona-relevant features activated. Weighted by feature tier \u2014 core features count more than advanced ones."},{"name":"NPS / satisfaction","weight":0.2,"input":"nps_score (0\u201310)","notes":"Normalised to 0\u2013100. Only updated when a new NPS response is received \u2014 stale NPS (>90 days) is down-weighted by 50%."},{"name":"Comms engagement","weight":0.15,"input":"comms_engagement enum","notes":"active=100, passive=50, unresponsive=0. Based on email open/click rate and in-app notification interactions over 30 days."}]},"thresholds":[{"band":"70\u2013100","label":"Healthy","colour":"green","action":"Monitor. Flag for expansion or advocacy if sustained >90 days."},{"band":"40\u201369","label":"At watch","colour":"amber","action":"CS to review monthly. Check which component is dragging the score."},{"band":"0\u201339","label":"At risk","colour":"red","action":"Trigger low_engagement or churn_risk alert depending on churn_risk_score."}],"what_it_doesnt_capture":"Sentiment outside the platform (social, reviews), quality of usage (logging in without doing anything meaningful), or team-level dynamics (a champion leaving).","data_sources":["Product analytics","NPS tool","Email platform"]},{"id":"churn_risk_score","name":"Churn Risk Score","range":"0.0\u20131.0","description":"A predictive score estimating the probability that an account will not renew or will cancel within the next 90 days. It is directional, not actuarial \u2014 it is designed to prompt human investigation, not to be treated as a precise forecast.","why_it_matters":"Lagging indicators like cancellation requests arrive too late to act on. Churn risk is a leading signal that gives the CS and AM team a 60\u201390 day window to intervene.","formula":{"type":"weighted_composite","components":[{"name":"Usage trend","weight":0.3,"input":"% change in login_frequency vs prior 30d","notes":"A 40%+ drop in a single month is a strong signal regardless of absolute level."},{"name":"Health score trajectory","weight":0.25,"input":"health_score delta over 60d","notes":"A declining health score trend matters more than a single low reading."},{"name":"NPS recency and score","weight":0.2,"input":"nps_score + days since last NPS","notes":"NPS \u22645 contributes heavily. No NPS response in >90 days is treated as a mild risk signal (passive churn indicator)."},{"name":"Renewal proximity","weight":0.15,"input":"days_to_renewal","notes":"Risk weight increases non-linearly as renewal approaches. An at-risk account with 14 days to renewal scores higher than the same account with 180 days."},{"name":"Comms disengagement","weight":0.1,"input":"comms_engagement + last_comms_interaction","notes":"Unresponsive to 3+ consecutive touches is a meaningful churn predictor."}]},"thresholds":[{"band":"0.0\u20130.35","label":"Low risk","colour":"green","action":"No action required. Standard lifecycle comms continue."},{"band":"0.36\u20130.55","label":"Moderate risk","colour":"amber","action":"CS to review. Consider proactive check-in or help centre nudge."},{"band":"0.56\u20130.70","label":"Elevated risk","colour":"amber-red","action":"Trigger risk_reactivation email. Flag to CS team."},{"band":"0.71\u20131.0","label":"Critical risk","colour":"red","action":"Trigger churn_risk alert. AM personal outreach within 24 hours."}],"what_it_doesnt_capture":"External factors (budget cuts, company acquisition, champion departure). These require human intelligence \u2014 the score is a starting point for a conversation, not a verdict.","data_sources":["Product analytics","CRM","NPS tool","Email platform"]},{"id":"feature_adoption_score","name":"Feature Adoption Score","range":"0\u2013100","description":"The percentage of persona-relevant features that a user has meaningfully engaged with \u2014 not just visited, but used in a way that reflects the feature's intended purpose.","why_it_matters":"Breadth of feature adoption is one of the strongest predictors of long-term retention. Customers using one feature are fragile. Customers embedded across five are sticky.","formula":{"type":"percentage","components":[{"name":"Feature set","weight":null,"input":"Per-persona feature list","notes":"Account owners are scored against admin/reporting features. Managers against workflow/collaboration features. Limited users against task-level features. This prevents a limited user being penalised for not using API access."},{"name":"Engagement threshold","weight":null,"input":"Minimum usage count per feature","notes":"A feature is counted as 'adopted' only after 3+ meaningful interactions within 30 days, not a single visit. This filters out accidental clicks."},{"name":"Recency weighting","weight":null,"input":"last_feature_used date","notes":"Features used in the last 14 days count fully. Features used 15\u201345 days ago count at 50%. Features not used in 45+ days are excluded."}]},"thresholds":[{"band":"75\u2013100","label":"Power user","colour":"green","action":"Flag for advocacy track. Candidate for beta access or referral ask."},{"band":"40\u201374","label":"Developing","colour":"amber","action":"Target with JTBD-specific nudges for unused relevant features."},{"band":"0\u201339","label":"Surface user","colour":"red","action":"High churn risk. Trigger feature spotlight sequence."}],"what_it_doesnt_capture":"Quality of usage. A user could be running the same report 30 times a day (high score) without extracting real value. Qualitative signals like NPS and support tickets are needed alongside this.","data_sources":["Product analytics"]},{"id":"comms_engagement","name":"Comms Engagement","range":"active / passive / unresponsive","description":"A rolled-up signal of how a user is responding to outbound communications across email and in-app channels over the last 30 days.","why_it_matters":"A user who stops opening emails before they stop logging in is showing early disengagement. Comms engagement is often the first signal to move before usage metrics drop.","formula":{"type":"enum_logic","components":[{"name":"Active","weight":null,"input":"Opened or clicked \u22651 email AND interacted with \u22651 in-app message in last 30d","notes":"Interaction = click, dismissal with intent, or CTA tap. Not just impression."},{"name":"Passive","weight":null,"input":"Opened \u22651 email but no clicks, OR no in-app interaction, in last 30d","notes":"Still reachable but not acting. Adjust messaging cadence and CTA clarity."},{"name":"Unresponsive","weight":null,"input":"No email open AND no in-app interaction in last 30d","notes":"Check deliverability first. If emails are reaching inbox, this is a meaningful churn signal."}]},"thresholds":[{"band":"active","label":"Engaged","colour":"green","action":"Standard cadence. Test new CTAs."},{"band":"passive","label":"Drifting","colour":"amber","action":"Review subject lines and send times. Consider channel switch to in-app."},{"band":"unresponsive","label":"Disengaged","colour":"red","action":"Escalate to human outreach. Pause automated sequence to avoid spam flagging."}],"what_it_doesnt_capture":"Email clients that auto-open (certain mobile clients register opens without human action). Apple MPP inflates open rates \u2014 click rate is a more reliable signal.","data_sources":["Email platform","Product analytics (in-app)"]},{"id":"external_signals","name":"External Signals","range":"none / funding / hiring / product_launch / big_logo / buzz","description":"Indicators of growth, momentum, or change at a customer's company that are observable outside the platform. These signals inform timing and tone of outreach \u2014 a company announcing a funding round is in a different headspace to one that is quietly contracting.","why_it_matters":"The best time to have an expansion conversation is when your customer is already in growth mode. External signals let the AM team be proactive and contextually relevant rather than reactive.","formula":{"type":"enrichment_classification","components":[{"name":"Funding","weight":null,"input":"Seed / Series A\u2013D / growth round announced","notes":"Source: Crunchbase, LinkedIn, press. Funding events typically precede headcount growth \u2014 an expansion conversation is highly relevant within 30\u201360 days of announcement."},{"name":"Hiring","weight":null,"input":"Open roles posted on LinkedIn / job boards in relevant departments","notes":"Hiring in the same department that uses your platform is the strongest signal. Filter for role type \u2014 a DevOps hire matters differently to a Sales hire depending on your platform."},{"name":"Product launch","weight":null,"input":"New product, major feature, or version announced","notes":"Often correlates with needing to scale tooling. Good entry point for a conversation about whether the platform supports the new workflow."},{"name":"Big logo","weight":null,"input":"Customer win or partnership announced publicly","notes":"Signals growth and new use cases. Also a potential case study or co-marketing opportunity."},{"name":"Buzz","weight":null,"input":"Press coverage, social mentions, awards, industry recognition","notes":"Lower intent signal than the above but useful for personalising outreach and showing you are paying attention."}]},"thresholds":[{"band":"funding / hiring","label":"High intent","colour":"gold","action":"AM to reach out within 48 hours. Prioritise expansion conversation."},{"band":"product_launch / big_logo","label":"Medium intent","colour":"gold","action":"Personalise next touch with congratulations and relevant case study."},{"band":"buzz","label":"Low intent","colour":"neutral","action":"Reference in next scheduled communication. No urgency."}],"what_it_doesnt_capture":"Private signals (internal restructuring, undisclosed financial pressure, leadership changes not yet public). These require relationship intelligence from the AM.","data_sources":["Clearbit / Apollo","LinkedIn Sales Navigator","Google Alerts / press monitoring"]},{"id":"nps_score","name":"NPS Score","range":"0\u201310 (individual response)","description":"Net Promoter Score collected at the individual user level and rolled up to the account. Used as a satisfaction anchor \u2014 it tells you how the customer feels about the platform independent of usage patterns.","why_it_matters":"Usage without satisfaction is fragile. A power user who scores a 4 is a churned customer waiting for a better alternative. NPS catches sentiment that usage metrics miss.","formula":{"type":"survey_response","components":[{"name":"Collection cadence","weight":null,"input":"Account owner: every 90 days. Managers: every 120 days. Limited users: every 180 days.","notes":"Over-surveying kills response rates. Account owners are surveyed most frequently because their score carries the most weight in renewal decisions."},{"name":"Account rollup","weight":null,"input":"Weighted average: account_owner (50%), manager average (35%), limited user average (15%)","notes":"Account owner NPS is weighted most heavily because they control renewal. A manager scoring 9 does not offset an owner scoring 3."},{"name":"Staleness decay","weight":null,"input":"Score collected >90 days ago","notes":"Down-weighted by 50% in health score calculation. A 6-month-old NPS 9 is not as meaningful as a 2-week-old NPS 7."}]},"thresholds":[{"band":"9\u201310","label":"Promoter","colour":"green","action":"Trigger advocacy track. Referral ask, community invite, beta access."},{"band":"7\u20138","label":"Passive","colour":"amber","action":"Nurture to promoter. Ask what would make it a 10."},{"band":"0\u20136","label":"Detractor","colour":"red","action":"Immediate CS follow-up. Understand specific pain point. Do not trigger promotional content."}],"what_it_doesnt_capture":"Why. NPS tells you the temperature, not the diagnosis. Always follow a low score with an open text question or a direct conversation.","data_sources":["NPS tool (Delighted / Typeform / in-app survey)"]},{"id":"upsell_ready","name":"Upsell Ready Flag","range":"true / false","description":"A boolean flag that identifies free accounts showing enough engagement signals to be ready for a conversion conversation to a paid plan.","why_it_matters":"Free-to-paid conversion is the highest-leverage growth motion for most SaaS products. The flag ensures the CS team is reaching out at the moment of maximum intent \u2014 not too early (before the user has seen value) and not too late (after they have decided the free tier is sufficient).","formula":{"type":"boolean_logic","components":[{"name":"Plan type","weight":null,"input":"plan_type = free","notes":"Only applies to free accounts."},{"name":"Health signal","weight":null,"input":"health_score > 55","notes":"Must show meaningful engagement before conversion is attempted. Pushing conversion on a disengaged free user accelerates churn, not conversion."},{"name":"Lifecycle stage","weight":null,"input":"lifecycle_stage in [adoption, expansion]","notes":"Onboarding is too early (user hasn't seen full value). Re-engagement and at_risk suggest the free tier itself isn't working."},{"name":"Active usage","weight":null,"input":"At least 1 user with login_frequency_30d \u2265 5","notes":"Ensures at least one team member is genuinely using the product, not just the account owner who signed up and forgot."}]},"thresholds":[{"band":"true","label":"Ready","colour":"gold","action":"Trigger upsell email sequence. AM to personalise if plan_size > 3."},{"band":"false","label":"Not ready","colour":"neutral","action":"Continue standard nurture. Re-evaluate weekly."}],"what_it_doesnt_capture":"Intent. A user could be highly engaged with the free tier and have no intention of paying. Pricing objections and free-tier ceiling moments require human conversation to resolve.","data_sources":["Product analytics","CRM"]}],"integrations":[{"id":"crm","name":"CRM","concept":"The system of record for account and contact data. It holds company-level information, deal history, renewal dates, AM assignments, and relationship context that the platform itself does not capture.","real_tools":["Salesforce","HubSpot CRM","Pipedrive"],"fields_provided":["company_name","domain","plan_type","plan_size","enrolled_date","renewal_date","mrr","am_assigned","white_glove flag"],"sync_method":"Bidirectional. CRM is the master for account and contact fields. Lifecycle stage and health score are written back from the automation platform to CRM so sales and CS work from the same data.","sync_frequency":"Account/contact fields: daily batch. Renewal date and MRR: real-time webhook on change.","fallback":"If CRM sync fails, lifecycle stage placement uses last known values with a staleness flag. AM is alerted if account data is >48 hours old.","notes":"The most important integration. Without clean CRM data, renewal dates and AM assignments will be wrong, and the white glove flag will misfire."},{"id":"product_analytics","name":"Product Analytics","concept":"Captures how users actually behave inside the platform \u2014 what they click, what they complete, how long they spend, and how often they return. This is the primary source for usage-based metrics.","real_tools":["Mixpanel","Amplitude","Heap","Segment (as pipeline)","PostHog"],"fields_provided":["login_frequency_30d","last_login","feature_adoption_score","last_feature_used","session_depth","help_center_visits_30d"],"sync_method":"Event stream via Segment or direct API. Each user action is an event. The automation platform aggregates events into the metric fields on a rolling 30-day window.","sync_frequency":"Event-level: real-time stream. Aggregated metrics (feature adoption score, login frequency): recalculated every 24 hours.","fallback":"If event stream is interrupted, last known aggregated metrics are held for up to 72 hours before health score is flagged as stale.","notes":"Feature adoption scoring requires a maintained feature taxonomy \u2014 someone needs to own the mapping of events to features and personas. This is an ongoing data ops responsibility, not a one-time setup."},{"id":"email_platform","name":"Email & Comms Platform","concept":"The execution layer for customer-facing email and in-app messaging. It sends the touches defined in the comms flow and reports back engagement signals (opens, clicks, bounces) that feed into the comms_engagement metric.","real_tools":["Customer.io","HubSpot Marketing","Klaviyo","Braze","Intercom"],"fields_provided":["comms_engagement","last_comms_interaction","current_touch","email open/click rates","in-app interaction events"],"sync_method":"The automation platform writes trigger instructions to the email platform (e.g. 'enrol account X in sequence Y'). The email platform writes engagement events back via webhook.","sync_frequency":"Trigger instructions: real-time on lifecycle stage change or alert fire. Engagement data: webhook on event (open, click, bounce) \u2014 near real-time.","fallback":"If engagement data is not received within 7 days of a send, comms_engagement defaults to 'passive'. Bounces are flagged immediately for AM review.","notes":"Personalisation tokens ({{first_name}}, {{company}}, etc.) must be resolvable at send time. If a field is missing, the sequence should hold and alert CS rather than send a broken email."},{"id":"nps_tool","name":"NPS & Survey Tool","concept":"Collects satisfaction scores from users at defined intervals. The trigger for survey sends is managed by the automation platform based on lifecycle stage and survey cadence rules.","real_tools":["Delighted","Typeform","Medallia","In-app (via Intercom or Pendo)","SurveyMonkey"],"fields_provided":["nps_score","nps_date","verbatim feedback (qualitative)"],"sync_method":"Survey sends triggered by the automation platform. Responses written back via webhook to update nps_score and nps_date.","sync_frequency":"Responses: real-time webhook on submission. Score immediately updates health_score and churn_risk_score calculations.","fallback":"If no NPS response is received within the survey window, nps_score is held at last known value with staleness decay applied. A non-response is not treated as a neutral score.","notes":"Verbatim NPS feedback is the most underused data in most CS systems. It should feed into a qualitative tagging workflow (product feedback, support issues, competitor mentions) rather than being stored and ignored."},{"id":"external_enrichment","name":"External Signal Enrichment","concept":"Third-party data providers that monitor public signals about customer companies \u2014 funding announcements, job postings, press coverage, social mentions. This data is matched to accounts by domain and written into the external_signals field.","real_tools":["Clearbit","Apollo.io","LinkedIn Sales Navigator","Bombora (intent data)","Google Alerts (lightweight)"],"fields_provided":["external_signals array","company growth indicators","hiring department and role type","funding round size and stage"],"sync_method":"Daily batch enrichment job matches customer domains against enrichment provider data. New signals append to external_signals array and can trigger alerts.","sync_frequency":"Daily batch. For high-value white glove accounts, near-real-time monitoring can be configured via LinkedIn Sales Navigator alerts or Clearbit webhooks.","fallback":"If enrichment provider is unavailable, last known signals are held. No alert fires for a missing enrichment \u2014 only for a positive signal detected.","notes":"Signal quality varies significantly by company size. Enrichment data is much richer for funded startups and public companies than for small private businesses. Set expectations accordingly."},{"id":"internal_alerting","name":"Internal Alerting","concept":"The channel through which the automation platform notifies the CS and AM team of triggered conditions. It is not a data source \u2014 it is a delivery mechanism for the alert logic defined in the platform.","real_tools":["Slack (primary)","Microsoft Teams","Email digest","CRM task creation (Salesforce / HubSpot)"],"fields_provided":["Alert payload: account data, trigger condition, recommended action, deep link to account"],"sync_method":"Webhook from automation platform to Slack/Teams on alert fire. CRM task also created for AM-facing alerts so they appear in the AM's daily workflow.","sync_frequency":"Real-time on trigger. Critical alerts (churn_risk, dormant_bubble) fire immediately. Opportunity alerts (high_satisfaction, advocacy_ready) are batched into a daily digest to avoid noise.","fallback":"If Slack webhook fails, alert is queued and retried 3 times over 10 minutes. If still failing, fallback email is sent to CS lead distribution list.","notes":"Alert fatigue is the primary failure mode of internal alerting systems. Start with fewer, higher-quality alerts and expand over time. Every alert should have a clear recommended action \u2014 an alert without an action is just noise."}]};

const COLOUR_MAP = {green:'tl-green',amber:'tl-amber',red:'tl-red',gold:'tl-gold',neutral:'tl-neutral','amber-red':'tl-amber-red'};

function switchSection(id, btn) {
  document.querySelectorAll('.section').forEach(s => s.classList.remove('active'));
  document.querySelectorAll('.doc-nav-btn').forEach(b => b.classList.remove('active'));
  document.getElementById('section-' + id).classList.add('active');
  btn.classList.add('active');
}

function toggleCard(header, bodyId) {
  const body = document.getElementById(bodyId);
  const arrow = header.querySelector('.expand-arrow');
  body.classList.toggle('open');
  if (arrow) arrow.style.transform = body.classList.contains('open') ? 'rotate(90deg)' : '';
}

function buildMetrics() {
  const container = document.getElementById('metrics-container');
  container.innerHTML = SPEC.metrics.map((m, i) => {
    const sources = m.data_sources.map(s => `<span class="source-chip">${s}</span>`).join('');
    const formulaRows = m.formula.components.map(c => `
      <tr>
        <td>${c.name}</td>
        <td>${c.weight !== null ? `<span class="weight-pill">${Math.round(c.weight*100)}%</span>` : '—'}</td>
        <td>${c.input}</td>
        <td style="font-size:12px">${c.notes}</td>
      </tr>`).join('');
    const thresholdRows = m.thresholds.map(t => `
      <div class="threshold-row">
        <div class="threshold-band">${t.band}</div>
        <div class="threshold-label-wrap"><span class="threshold-label ${COLOUR_MAP[t.colour]||'tl-neutral'}">${t.label}</span></div>
        <div class="threshold-action">${t.action}</div>
      </div>`).join('');
    return `
    <div class="metric-card">
      <div class="metric-header" onclick="toggleCard(this,'mb-${i}')">
        <div class="metric-title-block">
          <div class="metric-name">${m.name}</div>
          <div class="metric-range">Range: ${m.range}</div>
          <div class="metric-desc-preview">${m.description.substring(0,120)}...</div>
          <div class="metric-sources">${sources}</div>
        </div>
        <div class="expand-arrow">›</div>
      </div>
      <div class="metric-body" id="mb-${i}">
        <div class="mb-section">
          <div class="mb-label">What it measures</div>
          <div class="mb-text">${m.description}</div>
        </div>
        <div class="mb-section">
          <div class="mb-label">Why this metric</div>
          <div class="mb-text">${m.why_it_matters}</div>
        </div>
        <div class="mb-section">
          <div class="mb-label">How it's calculated</div>
          <div style="background:var(--bg3);border-radius:var(--r);padding:4px;margin-top:4px;overflow-x:auto">
            <table class="formula-table">
              <thead><tr><th>Component</th><th>Weight</th><th>Input</th><th>Notes</th></tr></thead>
              <tbody>${formulaRows}</tbody>
            </table>
          </div>
        </div>
        <div class="mb-section">
          <div class="mb-label">Thresholds & actions</div>
          <div style="background:var(--bg3);border-radius:var(--r);padding:8px 12px;margin-top:4px">
            ${thresholdRows}
          </div>
        </div>
        <div class="mb-section">
          <div class="mb-label">Limitations</div>
          <div class="caveat-box"><strong>What this metric does not capture</strong>${m.what_it_doesnt_capture}</div>
        </div>
      </div>
    </div>`;
  }).join('');
}

function buildIntegrations() {
  const container = document.getElementById('integrations-container');
  container.innerHTML = SPEC.integrations.map((int, i) => {
    const tools = int.real_tools.map(t => `<span class="tool-chip">${t}</span>`).join('');
    const fields = int.fields_provided.map(f => `<span class="field-chip">${f}</span>`).join('');
    return `
    <div class="int-card">
      <div class="int-header" onclick="toggleCard(this,'ib-${i}')">
        <div class="metric-title-block">
          <div class="int-name">${int.name}</div>
          <div class="int-concept">${int.concept.substring(0,130)}...</div>
          <div class="tools-row" style="margin-top:10px">${tools}</div>
        </div>
        <div class="expand-arrow">›</div>
      </div>
      <div class="int-body" id="ib-${i}">
        <div class="mb-section">
          <div class="mb-label">What it provides</div>
          <div class="mb-text">${int.concept}</div>
        </div>
        <div class="mb-section">
          <div class="mb-label">Fields provided</div>
          <div class="fields-list">${fields}</div>
        </div>
        <div class="int-grid" style="margin-top:16px">
          <div class="int-kv">
            <div class="int-k">Sync method</div>
            <div class="int-v">${int.sync_method}</div>
          </div>
          <div class="int-kv">
            <div class="int-k">Sync frequency</div>
            <div class="int-v">${int.sync_frequency}</div>
          </div>
          <div class="int-kv">
            <div class="int-k">Fallback behaviour</div>
            <div class="int-v">${int.fallback}</div>
          </div>
          <div class="int-kv" style="border-left:2px solid rgba(200,181,96,.3)">
            <div class="int-k" style="color:var(--accent)">Implementation note</div>
            <div class="int-v">${int.notes}</div>
          </div>
        </div>
      </div>
    </div>`;
  }).join('');
}

buildMetrics();
buildIntegrations();
</script>
</body>
</html>
