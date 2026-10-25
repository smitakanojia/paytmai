# Build plan — T+0

Two plans. The first is the 27 hours to the PPT deadline (25/10/2026, 00:00).
The second is the prototype, split three ways.

---

# Part 1 — the next 27 hours (PPT submission)

The deck is done and covers all seven mandated sections. What's left is
verification and filling blanks. **Do not start coding before the deck is
submitted** — Stage 1 is scored on the PPT alone.

| Hour | Who | Task |
|---|---|---|
| 0–1 | All three | Read all 15 slides together. Write down every line you could not defend in Q&A. Kill or fix each one. |
| 1–3 | **A** | **Read the RBI TAT annex table directly** and confirm the per-rail, per-entity allocation of the ₹100/day. This is our one unverified load-bearing claim. If the annex says something different from the deck, fix slide 4 before anything else. |
| 1–3 | **B** | Confirm the NPCI Feb 2025 auto-chargeback scope (reporting says bulk upload + UDIR only — do not overstate). Check whether RBI-IOS was revised in 2026; secondary sources conflict. |
| 1–3 | **C** | Decide the name (T+0 vs Tatkaal), put real team names on the cover, pull the latest Paytm merchant figures from the most recent quarterly release. |
| 3–5 | All | Rehearse the deck aloud once, as if presenting. Anything you stumble over is badly written — fix it. |
| 5–7 | **C** | Export to PPTX and PDF from the artifact's Share → Export menu. **Open the PPTX and check every slide** — exports can reflow. |
| 7–8 | All | Submit. Keep 19 hours of buffer; portals fail. |

**Decisions to lock before submitting**

1. **The name.** "Ripple" is out — it collides with a crypto/payments company.
   T+0 is recommended; it *is* the pitch.
2. **Metrics are labelled targets, not results.** You have no prototype, so the
   Impact slide says "Targets — measured against the ablation in Stage 2." Do not
   quietly present a target as a measurement; one judge question kills the deck.
3. **Who answers what in Q&A.** A takes regulation, B takes measurement and
   clustering, C takes product and business model.

---

# Part 2 — the prototype, three ways

## The rule that makes parallel work possible

**Freeze the three interface contracts in hour one, before anyone writes
feature code.** Everything below depends on it. Put them in `contracts.py` and
treat changes as a team decision, not a commit.

### Contract 1 — the `Case` object (A ↔ B)

```python
Case = {
  "case_id": str,
  "merchant_id": str,
  "txn": {
    "id": str, "amount_paise": int, "ts": "iso8601",
    "rail": "UPI|IMPS|NEFT", "bank": "HDFC",
    "error_code": "U30", "status": "pending|failed|settled"
  },
  "clocks": [{
    "kind": "tat_reversal|udir_conversion|settlement_cycle|ios_window",
    "due_at": "iso8601", "breached": bool,
    "compensation_accrued_paise": int, "rule_id": "rbi_tat_upi_p2m"
  }],
  "incident_id": str | None,
  "state": "open|acting|awaiting_human|closed_verified|reopened",
  "remedy_rung": 1 | 2 | 3 | 4,
  "actions": [{"ts", "tool", "args", "result", "rule_cited"}]
}
```

### Contract 2 — the event stream (B → C)

Server-sent events. **B ships a fake emitter on day 1** so C is never blocked
waiting for a real engine.

```json
{"type": "case.opened|clock.tick|clock.breached|incident.formed|compensation.paid|case.closed",
 "case_id": "...", "ts": "iso8601", "payload": {}}
```

### Contract 3 — REST (A → C)

```
POST /chat/{merchant_id}          → agent turn
GET  /cases/{case_id}             → Case + decision trace
GET  /worklist                    → cases ranked by money_at_risk × time_to_breach
GET  /incidents                   → open incidents with ₹ exposure
POST /incidents/{id}/approve      → the one human decision
GET  /ledger/summary              → live ₹ exposure, breach counts
```

---

## Person A — Agent core and policy gate

*Owns everything that decides and everything that moves money.*

| Day | Deliverable |
|---|---|
| 1 | FastAPI skeleton, `Case` schema, SQLite, mock tools returning canned data: `get_transaction`, `settlement_status`, `raise_udir`, `credit_compensation`, `refund`, `notify`, `create_ticket`. **Mirror real Paytm API signatures** — that is the "how does this ship" answer. |
| 2 | Agent loop with tool calling. `rules.yaml` v1: RBI TAT deadlines, amount caps, fraud-flag triggers, escalation thresholds. |
| 3 | Policy gate enforcing `rules.yaml` deterministically — **no model in this path**. Compensation computation with the clause id attached. Append-only audit log. |
| 4 | Cost router with real tiers: known incident → template (0 calls), simple → Haiku 5.5, hard/risky → Opus 5.5, over caps → human. Zero-question opener. |
| 5 | Hardening, decision-trace output for C's side panel. |
| 6 | Stretch only: playbook compilation. |

**Done when:** a single case runs end to end — telemetry in, clock attached,
tool calls made, policy enforced, compensation computed, closed on verification.

---

## Person B — Clock engine, incident engine, evaluation

*Owns the USP and the proof. The hardest and most important role.*

| Day | Deliverable |
|---|---|
| 1 | Clock engine: attach all four deadlines per case from `rules.yaml`, countdown, breach prediction, compensation maths. **Plus the fake SSE emitter for C.** |
| 2 | Synthetic data generator: 300+ labelled cases across settlement delay, failed payment, refund, Soundbox offline, fraud, out-of-scope — each in English, Hinglish and a messy phrasing. Ground truth per case: correct action, correct deadline, correct compensation, should-escalate. |
| 3 | Incident clustering on bank × error code × 15-minute window, threshold-triggered single diagnosis. Outage simulator: 500–1,000 cases in 20 minutes plus silent affected merchants. |
| 4 | **Adversarial set** — cases engineered to induce a wrongful payout or a premature close. Ablation harness (same agent, engines off). |
| 5 | Full eval runs both variants → `metrics.json`. Fill every bracketed number in the deck. |
| 6 | Confidence intervals on the false-action rate. |

**Done when:** `python eval.py --variant t0` and `--variant ablation` both emit
`metrics.json`, and the difference is the pitch.

---

## Person C — Interfaces and demo

*Owns what the judges actually see.*

| Day | Deliverable |
|---|---|
| 1 | React shell + **the liability ledger, built against B's fake stream.** This is the hero visual — build it first, not last. Live ₹ exposure, per-case countdowns going red at breach. |
| 2 | Merchant chat with the zero-question opener: "Is this about the ₹5,000 from 2:14 pm? [Yes] [No]". |
| 3 | Ops console: incident card with root cause, affected count, **₹ exposure**, one approve button. |
| 4 | Decision-trace side panel — every tool call and the clause each rule cited. |
| 5 | Polish, then **record the backup demo video**. |
| 6 | Final deck numbers from B's `metrics.json`. |

**Done when:** the five-beat storyboard on the closing slide runs start to finish
without a developer touching a terminal.

---

## Critical path and risks

**The liability ledger is the demo.** Everything else can be mediocre and the
pitch still lands; if the ledger doesn't show exposure flatlining against the
baseline, nothing lands. That is why it is C's day-1 task against fake data.

| Risk | Mitigation |
|---|---|
| Integration hell on day 5 | 30-minute merge at a fixed time every day, starting day 2. Not optional. |
| B blocks C | Fake SSE emitter on day 1, before the real clock engine works. |
| A blocks B | `rules.yaml` is written on day 2 and B reads the same file. Agree the schema in hour one. |
| Live LLM or network dies in the demo | Recorded backup, and the outage demo runs from cached results. |
| Scope creep | Cut in this order: voice/missed-call → playbook compilation → rung 4 UI (keep the logic, mock the screen) → churn scoring. |

**Do not build:** real Paytm integration, auth, multi-tenancy, real payment
rails, a mobile app. Mock everything Paytm-side — it is the stated plan and it
is defensible.

---

## The one thing that decides Stage 2

Not features. The ablation. A working agent is table stakes by then; a working
agent **plus a credible measurement showing the clock engine beats the same
agent without it** is what no other team will have. Protect B's day 4 and 5.
