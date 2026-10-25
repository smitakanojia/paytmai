# UI specification — T+0

Researched 25 Oct 2026. Sources at the bottom. Read `06-backend-ml.md` alongside
this.

---

## Two corrections first

**1. "Paytm Assist" is not a support feature.** It is a *payments* product —
one-tap frictionless payments for a merchant's customers. If the deck or demo
says "we plug into Paytm Assist," you will be wrong in front of the people who
built it. The merchant support surface is **Help & Support inside the Paytm for
Business app**, plus the 24x7 AI chat Paytm advertises on its business site.

**2. Paytm's own acknowledgement SLA is longer than the regulatory deadline.**
Paytm's payment-gateway support pages state an initial response or
acknowledgement within **24 to 48 working hours** for online merchant queries.
The RBI reversal deadline is T+1. **The acknowledgement window alone can outrun
the deadline it needs to beat.** This is now on the hook slide — screenshot the
source page before you present it, because it is a pointed claim about the host.

---

## Part 1 — What the research says

### Paytm's visual language

**There is no published Paytm design system.** Nothing official exists for
colors, type or components. Everything below is assembled from brand-asset
aggregators and observable app patterns, and the sources disagree.

| Source | Dark blue | Cyan |
|---|---|---|
| Brandfetch (lists a style guide) | `#002970` Midnight Blue | `#00BAF2` Cerulean |
| Brandpalettes | `#002E6E` | `#00B9F1` (PMS 294 C / 306 C) |
| BrandColorCode | `#172B75` | `#00BCF1` |

**Pick the Brandfetch pair and verify by sampling a real screenshot.** Open the
Paytm for Business app, screenshot the home screen, and eyedrop the actual
values. Ten minutes of work; it is the difference between "looks native" and
"looks like a knockoff." Do the same for the typeface — no source names Paytm's
font, so do not guess it in the deck.

Observable patterns from redesign case studies: Paytm moved from a cluttered
layout to a **clean, white-themed UI with generous white space**. So: white
surfaces, cyan as the action colour, navy for text and headers, cards with soft
radius, minimal chrome.

### Comparable product UIs

| Product | Pattern worth stealing | Pattern to avoid |
|---|---|---|
| **Intercom Fin** | One shared inbox — the AI and the human work the same conversation and the same customer record. Behaviour rules like *"suggest the action, but don't run it until a person approves"* are exactly our approval gate. | Copilot needs manual prompting; limited real-time guidance (per a competitor's comparison, so discount it). |
| **Zendesk Agent Workspace** | All channels in one screen; ownership, escalation, queues and SLAs visible in one place. | Reviewers say it prioritises ticket management over conversational flow. Our ops console should be case-and-clock-centric, not ticket-centric. |
| **ServiceNow Horizon `now-sla-timer`** | A real, documented SLA timer component: **colour changes by stage, red when breached, remaining time in mixed units (days + hours)**. Horizon explicitly warns **not to use the timer where the record doesn't need critical-speed resolution.** | Overusing it. If every row has a red timer, none of them mean anything. |
| **Atlassian/Jira SLA guidance** | Put the timer **inside the work item** so nobody hunts for the deadline. Show *remaining* time, not elapsed. | — |
| **UI Coach "breach recovery" challenge** | A timer alone doesn't show *why* work is stuck. Give the operator a **next-few-hours risk filter**, and note that reassigning everything red just moves the bottleneck. | A wall of red with no triage affordance. |

**Accessibility:** dashboard guidance warns that red/green status coding fails
for colour-blind users. Our palette already uses **orange/teal**, which differ in
lightness as well as hue — keep it, and always pair the colour with a word
("Breached", "2h left"), never colour alone.

---

## Part 2 — The design decision that matters most

**The two surfaces must NOT share a design language.** This is the mistake to
avoid: making the ops console look like a consumer app makes it worse.

| | Merchant chat | Ops console + ledger |
|---|---|---|
| Goal | Indistinguishable from Paytm for Business | Dense, fast, scannable |
| Who | A kirana owner on a phone, stressed | A trained operator on a 27" monitor |
| Look | Paytm cyan/navy, white cards, large type, big tap targets | Dark, information-dense, keyboard-driven |
| Density | One idea per screen | Fifty rows visible at once |

Only the merchant surface needs to look native to Paytm. The ops console is an
internal tool and should look like one — PagerDuty and ServiceNow don't look
like consumer apps for good reasons.

---

## Part 3 — Surface specs

### Tokens

```css
/* Merchant surface — Paytm-native. Verify against a real screenshot. */
--pt-cyan:        #00BAF2;   /* primary action */
--pt-navy:        #002970;   /* headings, primary text */
--pt-surface:     #FFFFFF;
--pt-bg:          #F5F7FA;
--pt-text:        #1A2B47;
--pt-muted:       #5A6B85;
--pt-divider:     #E4E9F0;
--pt-success:     #0E7490;   /* teal, not green */
--pt-warn:        #C2410C;   /* burnt orange, not red */
--radius:         12px;

/* Ops surface — our own identity, matches the deck */
--ops-bg:         #0C1B33;
--ops-panel:      #16294A;
--ops-text:       #F7F8F5;
--ops-muted:      #B8C4D9;
--ops-urgent:     #FF6B35;
--ops-ok:         #0FA3B1;
```

### Surface A — Merchant chat (Paytm-native)

Lives where Help & Support lives. Phone-first, 375px design width.

```
┌─────────────────────────────┐
│ ←  Help & Support      ⋯    │  navy text on white, 56px bar
├─────────────────────────────┤
│                             │
│  ┌───────────────────────┐  │  ← THE ZERO-QUESTION OPENER
│  │ ₹5,000                │  │    fires before the merchant types
│  │ Today, 2:14 pm · HDFC │  │
│  │ ⏱ Expected by 6:00 pm │  │
│  │                       │  │
│  │ Is this what you      │  │
│  │ wanted to check?      │  │
│  │ ┌─────────┐ ┌───────┐ │  │
│  │ │   Yes   │ │   No  │ │  │  Yes = cyan fill, No = cyan outline
│  │ └─────────┘ └───────┘ │  │  48px tall minimum
│  └───────────────────────┘  │
│                             │
│  [agent message bubble]     │  white card, left-aligned
│  [merchant bubble]          │  cyan-tinted, right-aligned
│                             │
├─────────────────────────────┤
│ Type your message…      🎤  │  mic → Hindi voice
└─────────────────────────────┘
```

Rules:
- **Never show a countdown to the merchant.** Show the *promise* ("by 6:00 pm").
  A ticking clock on a stressed merchant's screen creates anxiety and support
  calls — the exact opposite of the goal. Countdowns are an ops affordance.
- Amounts in `₹1,23,456` Indian grouping, always.
- Every factual claim renders with its source chip: `✓ Checked with HDFC, 2:41 pm`.
- Hinglish in, Hinglish out. Match the merchant's language, don't correct it.
- When compensation is credited, it is its own card, not a line of prose:
  `₹200 credited · RBI delayed-reversal compensation · 2 days × ₹100`.

### Surface B — The liability ledger (the hero)

This is what wins the demo. Build it first.

```
┌──────────────────────────────────────────────────────────────────────┐
│  LIVE EXPOSURE          ₹ 2,41,800        ▲ ₹100/min    [T+0] [Base] │ ← toggle
├──────────────────────────────────────────────────────────────────────┤
│  ┌────────────────────────────────────────────────────────────────┐  │
│  │  ₹ exposure over time                                          │  │
│  │                                           ╭──── baseline       │  │  ← THE MONEY SHOT
│  │                                      ╭────╯                    │  │
│  │  ──────────────────────────────────────────  T+0 (flat)        │  │
│  └────────────────────────────────────────────────────────────────┘  │
├──────────────┬──────────────┬──────────────┬─────────────────────────┤
│ BREACHING <1h│ BREACHING <4h│ AT UDIR DAY 3│ APPROACHING DAY 30      │
│      14      │     212      │      3       │          0              │
├──────────────┴──────────────┴──────────────┴─────────────────────────┤
│  CASE        MERCHANT     AMOUNT    CLOCK          STATE             │
│  c_8812      Sharma Str…  ₹5,000    ⏱ 1h 12m left  Acting            │
│  c_8813      Gupta Kira…  ₹1,200    ⏱ BREACHED     Compensating      │
└──────────────────────────────────────────────────────────────────────┘
```

- The **baseline/T+0 toggle is the single most important control in the demo.**
  One click, two lines diverge. Rehearse that click.
- SLA timer per Horizon: colour by stage, mixed units ("1d 4h", "1h 12m"),
  **word alongside colour**. Not every row gets a loud timer — only rows inside
  the risk window, or the wall-of-red problem kicks in.
- Add the **next-4-hours filter** the UI Coach challenge recommends. It is what
  separates a dashboard from a worklist.
- Rows sort by **expected cost**, not time remaining. A ₹80,000 case breaching in
  3 hours outranks a ₹200 case breaching in 20 minutes. Show the ranking reason.

### Surface C — Ops console, incident card

One card, one decision.

```
┌──────────────────────────────────────────────────────────────┐
│  INCIDENT inc_41          ● ACTIVE          detected 2:27 pm │
│  HDFC · UPI · error U30 · window 2:10–2:30 pm                │
├──────────────────────────────────────────────────────────────┤
│  ROOT CAUSE                                                  │
│  HDFC UPI timeouts, settlements delayed. Failure rate 11.4%  │
│  against a 0.3% baseline — 38σ above expected.               │
├───────────────────────┬──────────────────────────────────────┤
│  AFFECTED      1,284  │  OF WHICH COMPLAINED           217   │
│  EXPOSURE   ₹1,28,400 │  BREACHING WITHIN 4H           212   │
├───────────────────────┴──────────────────────────────────────┤
│  PROPOSED            Notify all 1,284 · chase settlement ·   │
│                      auto-credit TAT on breach               │
│  POLICY              ✓ within caps  ✓ no fraud flags         │
├──────────────────────────────────────────────────────────────┤
│         [ Approve once ]          [ Review 3 exceptions ]    │
└──────────────────────────────────────────────────────────────┘
```

Show the **σ figure**, not just the ticket count — it is what makes detection
look like engineering rather than a threshold someone guessed.

### Surface D — Decision trace

Right-hand panel, per case. Follows Intercom's shared-record pattern.

```
2:41:03  get_transaction(txn_9912)      → pending, HDFC, ₹5,000
2:41:03  clock.attach                    → TAT due 26 Oct 18:00  [rbi_tat_upi_p2m]
2:41:04  incident.match(inc_41)          → linked, root cause reused
2:41:04  policy.check(notify)            → ALLOW  [cap_notify_any]
2:41:05  notify(merchant_4471)           → sent, WhatsApp
—— no model call: resolved from incident template ——
```

That last line is the cost-router story told visually. Make it visible in the demo.

---

## Sources

- [Brandfetch — Paytm brand assets](https://brandfetch.com/paytm.com)
- [Brandpalettes — Paytm logo colors](https://brandpalettes.com/paytm-logo-colors/)
- [BrandColorCode — Paytm](https://www.brandcolorcode.com/paytm)
- [ServiceNow Horizon — `now-sla-timer`](https://horizon.servicenow.com/workspace/components/now-sla-timer)
- [Atlassian Community — SLA dashboards for support managers](https://community.atlassian.com/forums/App-Central-articles/12-SLA-Dashboards-and-Reports-Every-Support-Manager-Should-Use/ba-p/3165107)
- [UI Coach — Plan recovery before an SLA breach](https://www.uicoach.io/challenges/plan-recovery-before-an-sla-breach)
- [Zendesk vs Intercom comparison — agent workspace patterns](https://www.usefini.com/guides/intercom-vs-zendesk-support-agents)
- [Paytm for Business — contact and support](https://business.paytm.com/contact-us)
- [Paytm Payments — settlement reports on dashboard](https://www.paytmpayments.com/docs/settlement-reports-on-dashboard)
- [Lollypop Design — mobile app redesign case study (Paytm homepage)](https://lollypop.design/blog/2025/november/mobile-app-redesign-case-study-improve-ux/)
