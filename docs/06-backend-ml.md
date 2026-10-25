# Backend and ML — correctness pass

Read with `05-ui-spec.md`. This fixes several things in the earlier brief that
would not survive a Paytm engineer's questions.

---

## 1. What is actually ML, and what is not

The fastest way to lose credibility is to claim ML where there is arithmetic, or
to put a language model somewhere a classifier belongs. Be able to say this table
out loud.

| Component | Correct technique | The mistake to avoid |
|---|---|---|
| Deadline computation | Integer arithmetic over a rule file + banking calendar | An LLM "working out" the deadline — nondeterministic and unauditable |
| Compensation amount | Integer arithmetic in paise | LLM arithmetic. It will be wrong, and it will be wrong about money |
| Policy gate | Deterministic rule evaluation | LLM judgement |
| **Incident detection** | **EWMA / Poisson control chart on failure rate** | A fixed ticket threshold, or k-means on embeddings |
| **Breach prediction** | **Gradient-boosted trees on tabular features** | Asking a model to guess |
| Worklist ranking | Expected-cost arithmetic | A "priority" the LLM assigns |
| Zero-question opener | Ranking with a confidence margin | Free-text LLM guess |
| Cost routing | Small classifier + confidence floor | The model choosing its own tier |
| Intent + entity extraction | Small LLM, structured output, schema-validated | Regex alone on Hinglish |
| Reply drafting | LLM, grounded in tool results | — |

**One sentence for the Q&A:** *there is exactly one trained model in the money
path, it is gradient-boosted trees over tabular features, and everything that
actually moves money is deterministic code.*

---

## 2. The biggest correction: detection is not clustering

The earlier brief said "cluster by bank, error code and 15-minute window." That
is a `GROUP BY`. Calling it clustering oversells it, and a judge will notice.

Split it into two honest steps:

**Step 1 — Grouping (SQL, not ML).**
```sql
GROUP BY bank, rail, error_code, time_bucket_15min
```
Say plainly: this is a grouping key, not a model.

**Step 2 — Detection (this is the real work).** A fixed threshold of 20 tickets
is wrong in both directions: 20 failures from HDFC at 2pm is a normal Tuesday,
20 failures from a small bank at 3am is an outage. Use a per-`(bank, rail,
error_code)` baseline with an **EWMA control chart**:

```python
# baseline maintained per group, updated continuously
mu_t    = alpha * observed + (1 - alpha) * mu_prev      # alpha ≈ 0.3
sigma_t = sqrt(mu_t)                                     # Poisson counts
z       = (observed - mu_t) / max(sigma_t, 1.0)
if z > Z_TRIGGER and observed >= MIN_COUNT:              # Z≈6, MIN≈15
    open_incident()
```

Three things this buys you:
- **It fires on transaction telemetry, not ticket count** — which is what makes
  "fix it before the complaint" an honest claim rather than a slogan.
- It adapts per bank automatically; no hand-tuned thresholds per partner.
- It gives the ops card a **σ figure** instead of a count, which is what makes
  detection look like engineering.

Guard against the obvious failure: a group whose baseline is near zero produces
a huge z from two events. Hence `MIN_COUNT`, and hold the incident in `candidate`
state for one more window before acting.

---

## 3. The one real model: breach prediction

**Task.** Given a case now, will the money move before its TAT deadline?

**Label.** `settled_before_deadline ∈ {0,1}` — known by construction in
synthetic data, observable in production.

**Features** (all tabular, all available at decision time):

```
amount_paise, rail, bank, error_code
hour_of_day, day_of_week, is_bank_holiday, hours_to_deadline
bank_rail_median_settle_latency_7d
bank_failure_rate_1h            # ties the model to the control chart
incident_active (bool), incident_age_min
merchant_tenure_days, merchant_prior_breach_count
retry_count
```

**Model.** LightGBM or XGBoost. Not a neural net, not an LLM. Tabular, hundreds
of features at most, needs to be explainable to a compliance reviewer, and
trains in seconds on a laptop.

**Calibration matters more than accuracy.** The output feeds a rupee decision,
so a probability of 0.8 must mean 80%. Apply isotonic or Platt calibration and
**report a reliability curve and Brier score, not just AUC.** Almost no hackathon
team does this; it reads as genuinely professional.

**Cold start.** With no history, fall back to the per-group median settlement
latency. Say this unprompted — "what about a new bank" is a guaranteed question.

---

## 4. Ranking: expected cost, not time remaining

Sorting by time-to-breach is wrong. It puts a ₹200 case breaching in 20 minutes
above an ₹80,000 case breaching in 3 hours.

```python
expected_cost = (
    p_breach * tat_compensation_paise(case)
  + p_chargeback_lost * case.amount_paise
  + p_escalation_to_ombudsman * OMBUDSMAN_HANDLING_COST
)
priority = expected_cost / max(hours_to_deadline, 0.25)   # ₹ per hour of delay
```

Show the reason beside the row in the UI. An operator who cannot see why a row is
first will override the ordering and you lose the whole argument.

---

## 5. Correctness traps that will be tested

These are what a payments engineer actually checks.

**Money is an integer number of paise. Never a float.**
```python
amount_paise: int    # ✅
amount: float        # ❌ 0.1 + 0.2 != 0.3, and this is someone's money
```

**T+1 is a banking day, not 24 hours.** A Friday failure is not due Saturday. You
need a holiday calendar table and a `next_banking_day()` function. Settlement
cycles and bank holidays are explicitly called out in Paytm's own merchant
documentation as a cause of delay, so this will be asked.
```python
def tat_deadline(txn_ts: datetime, rail: str, cal: BankingCalendar) -> datetime:
    local = txn_ts.astimezone(ZoneInfo("Asia/Kolkata"))
    return cal.next_banking_day(local.date(), n=RULES[rail].tat_days).at(cal.cutoff)
```
Store UTC, compute in IST, test across a weekend and a gazetted holiday.

**Every money-moving call is idempotent.** This is the single most important line
of code in the system. Without it, one retry double-credits a merchant.
```python
key = f"{case_id}:{clock_id}:compensation:v1"   # deterministic, not a uuid4
credit_compensation(..., idempotency_key=key)
```

**The agent and the clock engine will race.** Both can act on the same case. Use
an explicit state machine with optimistic locking:
```
open → acting → awaiting_human → closed_verified
         ↕                            ↓
      reopened ←──────────────────────┘
```
```sql
UPDATE cases SET state=?, version=version+1
WHERE case_id=? AND version=?     -- 0 rows affected = someone else moved it
```

**Notifications go through an outbox.** Writing the case and sending the message
are not one transaction. Write the intent to an `outbox` table in the same commit
as the state change; a worker drains it. Otherwise a crash either drops the
message or sends it twice.

**The audit log is append-only and hash-chained.**
```python
entry.prev_hash = last.hash
entry.hash = sha256(canonical_json(entry) + last.hash)
```
Cheap to build, and it turns "we have logs" into "we have tamper-evident logs"
— which is the right answer when the premise is regulatory defensibility.

---

## 6. Schema sketch

```sql
CREATE TABLE cases (
  case_id TEXT PRIMARY KEY,
  merchant_id TEXT NOT NULL,
  txn_id TEXT NOT NULL,
  amount_paise INTEGER NOT NULL CHECK (amount_paise >= 0),
  rail TEXT NOT NULL, bank TEXT NOT NULL, error_code TEXT,
  incident_id TEXT REFERENCES incidents(incident_id),
  state TEXT NOT NULL, remedy_rung INTEGER NOT NULL DEFAULT 1,
  version INTEGER NOT NULL DEFAULT 0,
  created_at TEXT NOT NULL
);

CREATE TABLE clocks (
  clock_id TEXT PRIMARY KEY,
  case_id TEXT NOT NULL REFERENCES cases(case_id),
  kind TEXT NOT NULL,              -- tat_reversal | udir_conversion | settlement_cycle | ios_window
  rule_id TEXT NOT NULL,           -- traces to rules.yaml, cited in every reply
  due_at TEXT NOT NULL,
  stopped_at TEXT,
  breached INTEGER NOT NULL DEFAULT 0,
  compensation_accrued_paise INTEGER NOT NULL DEFAULT 0,
  UNIQUE (case_id, kind)           -- one clock of each kind per case
);

CREATE TABLE actions (                -- append-only, hash-chained
  seq INTEGER PRIMARY KEY AUTOINCREMENT,
  case_id TEXT NOT NULL, ts TEXT NOT NULL,
  tool TEXT NOT NULL, args_json TEXT NOT NULL, result_json TEXT,
  rule_cited TEXT, actor TEXT NOT NULL,      -- agent | human:<id> | clock_engine
  idempotency_key TEXT UNIQUE,
  prev_hash TEXT, hash TEXT NOT NULL
);
```

`rules.yaml`, the file the whole pitch rests on:
```yaml
rbi_tat_upi_p2m:
  source: "RBI DPSS.CO.PD No.629/02.01.014/2019-20"
  applies_to: {rail: UPI, type: P2M}
  deadline: {banking_days: 1, cutoff: "23:59", tz: "Asia/Kolkata"}
  on_breach:
    compensation: {per_day_paise: 10000, accrues_from: "deadline"}
    auto_credit: true
npci_udir_p2m:
  source: "NPCI UDIR"
  deadline: {calendar_days: 3}
  on_breach: {action: converts_to_chargeback, auto: true}
```
Every reply cites the `source` string. That is the Air Canada defence, in a file.

---

## 7. Evaluation — two things that would invalidate your results

**Do not let a model write the ground truth.** If an LLM generates the cases and
an LLM grades them, you have measured agreement, not correctness. Generate cases
**programmatically from `rules.yaml`** — you know the right deadline and the right
compensation by construction. Use the LLM only to paraphrase the merchant's
wording into English, Hinglish and a messy variant.

**Do not grade money with a model.** Compare integers. `assert
actual_paise == expected_paise`. An LLM judge is for tone, never for amounts.

**The adversarial set must contain at least these six:**
1. Settled at T+0.9 — compensation is **not** owed. Paying it is a failure.
2. Deadline spanning a weekend and a gazetted holiday.
3. Two complaints for the same txn — must not double-credit (tests idempotency).
4. Merchant states an amount that disagrees with the record.
5. One time window containing **two distinct root causes** — must not merge.
6. A near-threshold group where z is just under trigger — must not open an incident.

**Report honestly:** per-category n, a false-action rate with a Wilson interval,
and the calibration curve. A measured 1.2% [0.4–3.1%] beats a claimed 0%, because
the second one tells a technical judge you did not look.

---

## 8. Cost router — it must fail upward

```python
if playbook := exact_match(case):        return Route.TEMPLATE   # 0 model calls
if case.touches_money and case.amount_paise > CAP:  return Route.HUMAN
conf, tier = classifier.predict(case)
if conf < CONFIDENCE_FLOOR:              return Route.LARGE      # never down
return tier
```

Uncertainty escalates the tier; it never cheapens it. A router that saves money
by guessing when unsure is the Klarna failure mode rebuilt, and that is the exact
story on your own problem slide.

---

## 9. Build order, revised

The earlier plan had the clock engine on day 3. Move it to day 1 — it is the USP,
and everything else reads from it.

| Day | Critical path |
|---|---|
| 1 | `rules.yaml` + deadline arithmetic + banking calendar + **tests across a weekend and a holiday**. Fake SSE emitter for C. |
| 2 | Case state machine with optimistic locking, idempotent mock tools, hash-chained audit log. |
| 3 | Programmatic dataset generator; control-chart detection; incident formation. |
| 4 | Breach model: train, **calibrate**, wire into expected-cost ranking. Adversarial set. |
| 5 | Ablation runs both variants; metrics JSON; fill the deck's bracketed figures. |
| 6 | Hardening, demo rehearsal. |

Write the banking-calendar tests on day 1. Every date bug you find on day 5 costs
an hour you will not have.
