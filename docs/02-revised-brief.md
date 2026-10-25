# Project Brief: T+0 — Paytm AI Hackathon, Track 3

*Revision 2, 25 Oct 2026. Supersedes the Ripple brief. Rationale for every change
is in `01-critique.md`.*

---

## Summary

**T+0** is an autonomous support teammate for Paytm merchants that is accountable
to the **regulator's clock**, not to the ticket queue.

In Indian payments, a stuck transaction is not a support ticket. It is a timer
with money attached: RBI's TAT circular makes a delayed UPI reversal cost **₹100
per day, payable automatically without the customer asking**; NPCI's UDIR turns a
P2M complaint with no response in **3 days into a chargeback**; silence across one
**settlement cycle** decides a chargeback against you by default; **30 days**
unresolved opens the door to the RBI Ombudsman.

Every support agent on the market optimises **deflection** — did the customer stop
talking? T+0 optimises **the clock** — did the money move before the deadline, and
was the compensation paid when it didn't?

> **Pitch line:** RBI gives you T+1. We settle at T+0.
> Every other agent closes tickets. Ours closes the regulator's clock — before it
> becomes a chargeback, a ₹100-a-day penalty, and an Ombudsman case.

- **Track:** 3, Autonomous AI Teammates — customer-service workflow with one sales moment.
- **User:** Paytm merchants (kirana shops, small businesses) with settlement, failed-payment and Soundbox issues.
- **Core bet:** every other team will pitch deflection, trust and cost-per-ticket. Paytm already *sells* that — Paytm Intelligence (Pi), launched Sept 2026, $11M+/yr saved, 10M+ queries/month. We pitch the one number Pi does not yet claim: **rupees of regulatory liability avoided.**

---

## Problem

Paytm's AI already answers merchants. **Nothing in the market closes the clock.**

1. **Every stuck rupee starts a legally enforceable timer**, and support systems
   are blind to it. Tickets are prioritised by age and queue, not by *hours until
   T+1 auto-reversal*, *hours until UDIR converts this to a chargeback*, or *days
   until the Ombudsman window opens*.
2. **Compensation that is legally owed is not paid unless someone asks.** The RBI
   TAT framework intends the ₹100/day credit to be automatic. In practice it
   depends on a human noticing. Nobody is counting what that leaks.
3. **Deflection metrics hide the failure.** "Resolved" can mean the merchant gave
   up. Klarna optimised exactly this: ~2.3M chats in month one, company-equated to
   ~700 FTE — then in May 2025 its CEO said cost had become too dominant a factor
   and quality fell, and Klarna rehired humans on a flexible model while keeping
   the AI. The lesson is not "AI failed." It is **"deflection-optimised AI failed."**
4. **When a bank drops, 5,000 clocks start at the same instant** — and every
   deployed AI agent re-diagnoses the same root cause 5,000 times. Zendesk's own
   documentation states that **AI agent tickets cannot be linked to problem and
   incident tickets**: the ITSM layer sees one incident, the AI layer sees 5,000.
5. **Small merchants don't type.** Many would rather give a missed call, and want
   the answer in Hindi.
6. **The liability is real and already litigated.** In *Moffatt v. Air Canada*
   (2024 BCCRT 149), the tribunal rejected the argument that a chatbot is a
   separate legal entity and held the airline responsible for what its bot said.
   An agent that states policy must be unable to invent it.

---

## Core USP: the liability clock

T+0 attaches a **regulatory clock** to every case the moment it exists — not when
the merchant complains — and drives it to zero.

**Each case carries four deadlines**, computed from the transaction, not from the
ticket:

| Clock | Rule | Consequence of breach |
|---|---|---|
| Auto-reversal | RBI TAT, UPI → T+1 | **₹100/day payable to the customer** |
| UDIR conversion | P2M complaint unanswered 3 days | **Auto-converts to chargeback** |
| Settlement-cycle response | NPCI, eff. 15 Feb 2025 | Chargeback **auto-decided** on TCC/RET in the next cycle |
| Grievance window | RBI-IOS | **30 days** → Ombudsman eligible |

**What the agent does with them:**

1. **Start the clock from telemetry, not from the complaint.** A failed or delayed
   transaction creates a case whether or not the merchant has noticed.
2. **Rank by money-at-risk × time-to-breach**, not by queue position. The agent's
   worklist is sorted by what the next hour costs.
3. **Act before the deadline.** Chase the settlement, raise the UDIR entry inside
   the settlement cycle, push the reversal — autonomously, inside policy caps.
4. **Compute and credit what is owed.** If T+1 has passed, the agent calculates
   the TAT compensation from the rule file and queues the credit with its
   citation. The merchant doesn't have to know the rule exists.
5. **Close only on proof.** The sole acceptable close state is **money moved AND
   clock stopped AND compensation paid if owed** — verified in the backend, not
   inferred from the merchant going quiet.
6. **Escalate on the clock, not on sentiment.** A case 4 hours from T+1 with no
   path to resolution goes to a human *with the deadline on the card*.

**Why it wins:** it converts support from a cost centre measured in deflection
into a **risk function measured in rupees of liability avoided** — penalty payouts
avoided, chargebacks won inside the settlement cycle, Ombudsman cases prevented.
That is a number a CFO signs, and it is a number Pi does not yet claim.

**Why it requires autonomy:** this is the Track 3 argument. A human queue cannot
hold a T+1 deadline across thousands of simultaneous cases. Autonomy here isn't
*cheaper* — it is the only thing that meets the deadline.

---

## How we keep the promise at scale: incident batching

When one bank goes down, thousands of clocks start in the same minute. Incident
correlation is **how we hold T+0 under load** — it is the mechanism, not the
novelty claim.

> **Say this plainly in the deck:** incident correlation has existed in ITSM for
> twenty years — Zendesk links child incidents to a problem ticket, ServiceNow has
> Major Incident Management with similar-incident prediction. What does not exist
> is the **agent** being wired to it. Zendesk's own docs say AI agent tickets can't
> be linked to incidents. We wire them together, and we point the result at a
> deadline.

- **Detect:** group by shared signal — same bank, same error code, same 15-minute window.
- **Diagnose once:** past a threshold (~20 cases), one investigation writes one root cause.
- **Link:** matching cases attach to that incident instead of opening their own investigation. Each is re-checked against the root cause before linking; mismatches fall back to the normal path.
- **Batch-resolve on the clock:** every affected merchant gets their own amount and time — *"Your ₹1,200 from 2:14 pm is safe and will settle by 6 pm"* — and every one of those clocks is tracked to T+1.
- **Pre-empt:** affected merchants who haven't complained get the same message, and their clocks are already running in our system.
- **One human decision:** ops sees one incident card — root cause, affected count, **total liability exposure in ₹**, proposed action — and approves once.
- **Verify or reopen:** if the settlement lands, confirm. If it doesn't, the incident reopens itself and the clocks keep running.

**Honest framing of frequency:** NPCI's technical decline rate was **0.02%** in Feb
2025 and full outages happen a handful of times a year. Incident mode is the
**stress case**, not the business case. The clock engine earns its keep every
single day on ordinary delayed settlements — which is precisely why we did not
build the product around outages.

---

## Supporting features

| # | Feature | What it does | Metric | Priority |
|---|---|---|---|---|
| 1 | **Liability ledger** | Live ops view: ₹ of exposure accruing right now, cases about to breach T+1, cases about to auto-chargeback at day 3, cases approaching day 30 | ₹ liability avoided/day; breaches prevented | **Must** |
| 2 | **Zero-question opening** | The merchant is authenticated, so the agent already sees the stuck transaction: *"Is this about the ₹5,000 from 2:14 pm? [Yes] [No]"* | Turns to resolution (~1 vs 4–6) | **Must** |
| 3 | **Cost router** | Known incident → template, no LLM. Simple case → small model. Risky or novel → large model or human. | Cost per case by route | **Must** |
| 4 | **Compensation-aware retention** *(sales moment)* | A merchant owed TAT compensation gets it credited automatically **with the apology** — a retention action that is simultaneously a compliance action. Churn risk scored from impact and history, retention offers inside policy caps. | Revenue at risk saved; offers accepted | Stretch |
| 5 | **Missed-call voice** | Missed call → callback in seconds, in Hindi, with the status of the money and the deadline. Sarvam speech. | Time to callback; % resolved by voice | Stretch (wow) |
| 6 | **Playbook compilation** | When a human resolves a novel case, the agent drafts a coded playbook for approval; the next identical case runs on code with zero LLM calls. | % cases at zero tokens | Engineering detail — *not* a headline. Decagon's Duet Autopilot already does this. |

---

## Table stakes we borrow

Judges expect these. Build simple versions; spend the effort on the clock engine.

| Feature | Seen in | Our version |
|---|---|---|
| Policy in code, not the LLM | ResolveMate, UPI Dispute Teammate, Resolvas | **Rule file is a machine-readable encoding of RBI/NPCI circulars.** The LLM plans and writes; it has no authority to state a policy that isn't in the file. |
| Amount thresholds and escalation | UPI Dispute Teammate (₹5,000), Commerce OS (₹25k) | Auto below ₹5,000; human approval above; instant escalation on fraud flags |
| Verified resolution | ResolveAI | Close only on money moved **and** clock stopped |
| Promise tracking | Nirnaya (Promise Guard) | Every *"settles by 6 pm"* becomes a tracked clock; a missed promise reopens the case |
| Live decision trace | UPI Dispute Teammate | Side panel: each tool call, each rule fired, **each circular cited** |
| Audit log | ResolveMate, Nirnaya | Append-only: every action, who, why, under which clause |
| Escalation with summary | Most repos, Agentforce | Human card carries issue, checks done, recommended action **and time-to-breach** |
| Hinglish | ResolveMate, Commerce OS | Sarvam / multilingual LLM for intent |
| Grounded replies | Cashfree Spark | Every amount and timestamp traces to a tool result |
| Cost dashboard | Nirnaya | Feeds the cost router |

---

## Competitive landscape

Deployed agents take real actions and some do correlate incidents. **None are
accountable to an Indian regulatory deadline.**

| Player | What it does well | Gap we exploit |
|---|---|---|
| **Paytm Intelligence (Pi)** — launched Sept 2026, sold to banks/lenders/insurers in India + UAE; sales, service and ops agents on a financial-services model; **$11M+/yr saved ($6.5M customer care, $4.8M merchant care), 10M+ queries/month** | The strongest agent layer in Indian fintech, and it is Paytm's own | **We are not a competitor to Pi — we are the liability engine Pi plugs into.** Pi optimises conversations and cost; nothing in its public positioning claims regulatory-clock accountability or automatic TAT compensation. |
| Zendesk / ServiceNow | Mature ITIL incident and problem management; linked child incidents; similar-incident prediction | **The AI layer isn't wired to the incident layer** — Zendesk's docs say AI agent tickets can't link to incidents. No money rail, no regulatory clock. |
| Decagon | AOPs; **Duet / Duet Autopilot** drafts procedures from transcripts, human-approved | Playbook learning is theirs, so we don't claim it. US product — no TAT, UDIR, TCC/RET or Ombudsman concept. |
| Sierra | Ghostwriter builds agents from procedures and transcripts; voice; Live Assist | Same: enterprise rollouts measured in months (per third-party comparisons), no Indian regulatory model. |
| Klarna | ~2.3M chats in month one, company-equated to ~700 FTE | Optimised cost and deflection; quality fell; rehired humans May 2025 while keeping the AI. The cautionary tale our metric is designed to avoid. |
| Intercom Fin | High claimed resolution rate, pay-per-resolution | Industry-wide problem: "resolved" is a deflection metric, not a money-moved metric. |
| Salesforce Agentforce | Full customer history, enterprise integration | No Indian payments regulatory model. |
| Cashfree Spark | Dashboard agent, vernacular voice | Lives in the web dashboard; merchant must log in. *(Vendor-claimed resolution rate — we do not cite a figure we can't verify.)* |
| Razorpay Agent Studio | Cart recovery, disputes, cash-flow agents | Revenue recovery, not deadline accountability. |
| Past Track 3 repos | Policy engines, audit trails, thresholds, Hinglish | All ticket-by-ticket; all pitch trust and cost. **None has a clock.** |

---

## Architecture

Three inputs (merchant chat, missed-call voice, transaction telemetry) → **clock
engine** (attaches and tracks the four deadlines) → **incident engine** (batches
when many clocks start together) → **cost router** → **policy gate** (RBI/NPCI
rules in code) → tools → **verification loop** (money moved? clock stopped?
compensation owed?) → ops console / liability ledger.

Two invariants:
- **Nothing that changes money bypasses the policy gate.**
- **Nothing closes without backend verification that the clock actually stopped.**

Human approvals in the ops console compile into new playbooks, which is how
routine work moves off the LLM over time.

---

## Metrics and evaluation

We run T+0 and an **ablation baseline — the identical agent with the clock engine
and incident engine switched off, same prompts, same tools, same model** — over
the same data. Not a strawman we wrote to lose; we say this explicitly on the
slide.

**Test data**
- **300+ individual cases** across settlement delay, failed payment, refund, Soundbox offline, fraud and out-of-scope, each in English, Hinglish and a deliberately messy phrasing.
- **An adversarial set** built to make the agent fail *expensively*: cases designed to induce compensation that isn't owed, or to close a clock that hasn't stopped.
- **One simulated bank outage**: 500–1,000 cases in 20 minutes, plus affected merchants who never complain.
- **Ground truth per case:** correct action, correct deadline, correct compensation amount, whether it should escalate.

**Dashboard**

| Metric | What it proves |
|---|---|
| **₹ liability avoided vs baseline** | The headline. Penalties, chargebacks and Ombudsman cases prevented. |
| % clocks stopped before T+1 | Core promise |
| Compensation computed correctly vs ground truth | The agent knows the rules |
| % chargebacks pre-empted inside the settlement cycle | UDIR awareness |
| Verified resolution rate (money actually moved) | Honest resolution, unlike "customer went quiet" |
| **False-action rate, with confidence interval** | Replaces "must stay at 0" — a measured number with a stated n is more credible than a zero |
| Adversarial-set false-payout rate | Safety under pressure |
| LLM calls per 1,000 outage cases | Incident batching efficiency |
| Cost per case by route | Cost router |
| Turns to resolution | Zero-question opening |
| Escalation accuracy | Escalates the right cases, and only those |

Every number comes from our own eval run on labelled synthetic data, and the slide
says so.

---

## Demo storyboard (45 seconds in the deck; 3 minutes live)

The demo builds to one moment: **5,000 clocks, one diagnosis, one human click.**

1. **Clock starts before the complaint (10 s).** Telemetry shows a stuck ₹5,000. A case opens with T+1 counting down. The merchant hasn't typed anything yet.
2. **Zero-question resolve (15 s).** Merchant opens chat: *"Is this about the ₹5,000 from 2:14 pm?"* One tap. Agent checks status, explains, confirms. Decision trace shows the circular it cited.
3. **The outage (15 s).** "Simulate HDFC UPI outage." 500 cases flood in, cluster into one incident, one diagnosis, 500 personalised messages. The liability ledger shows ₹ exposure climbing on the baseline and staying flat on T+0.
4. **Compensation (10 s).** One case breaches T+1. The agent computes the TAT credit, cites the clause, and queues it — unprompted.
5. **One click (5 s).** Ops approves the incident card. Settlement lands; T+0 verifies and confirms.

*(Live only: ₹18,000 refund blocked by the policy gate → human card with time-to-breach; and the Hindi missed-call callback.)*

---

## Build plan — team of 3

Mock everything Paytm-side. Real effort goes into the **clock engine**, the
**liability ledger** and the **eval**.

| Person | Owns | Deliverables |
|---|---|---|
| **A — Agent & backend** | Orchestrator, tools, policy engine | FastAPI; mock tools (`get_transaction`, `settlement_status`, `raise_udir`, `credit_compensation`, `refund`, `notify`); **rule file encoding RBI TAT + UDIR + settlement-cycle deadlines**; playbook store; cost router |
| **B — Clock & incident engine** | Deadlines, clustering, data, evals | Clock attachment and breach prediction; outage simulator; clustering by bank/error/window; **adversarial set**; ablation harness; metrics |
| **C — Frontend & deck** | Merchant chat, ops console, ledger | Zero-question chat; incident card with ₹ exposure; **live liability ledger**; pitch deck |

**Order of work**
- **Day 1–2:** mock backend, tools, rule file, agent loop resolving one case end to end.
- **Day 3:** clock engine — attach, track, predict breach, compute compensation. This is the USP; it gets a full day.
- **Day 4:** outage simulator, clustering, incident card.
- **Day 5:** liability ledger, zero-question opener, cost router.
- **Day 6:** eval runs against the ablation baseline; real numbers into the deck.
- **Day 7:** rehearse; stretch features only if everything above works.

**Stack:** Python + FastAPI; tool-calling LLM for hard cases, small model for
simple; SQLite; React or Streamlit; Sarvam for Hindi speech, n8n for messaging
(both event partners).

---

## Deck structure for the PPT round (10 slides)

1. **Hook** — "A stuck ₹5,000 isn't a ticket. It's a ₹100-a-day timer." The four clocks on one slide.
2. **Problem** — support optimises deflection; the clock is invisible. Klarna as the cautionary tale.
3. **Insight** — RBI TAT, UDIR, settlement cycle, Ombudsman. Four deadlines, cited.
4. **Solution** — T+0: the liability clock engine. One diagram.
5. **Why autonomy** — a T+1 deadline across thousands of cases cannot sit in a human queue. *This is the Track 3 slide.*
6. **Scale** — incident batching: 5,000 clocks, one diagnosis. Honest about ITSM prior art, sharp about the AI-to-incident gap.
7. **Safety** — Moffatt v. Air Canada; policy is codified regulation, not invented text; every rupee traces to a tool call.
8. **Why Paytm, why now** — Pi exists and is excellent. We are the liability layer it plugs into. Real numbers: 13.7 Mn merchant subscriptions, ~1.17 Cr Soundbox base, $11M+ already saved on conversations — and ₹X not yet saved on liability.
9. **Proof** — ablation baseline, metric table, the ₹ liability avoided number.
10. **Ask + demo storyboard.**

---

## Risks and judge Q&A

| Question | Answer |
|---|---|
| **"Paytm already has Pi. Why you?"** | Pi is excellent and we're not competing with it. Pi optimises the conversation and the cost per query. Nothing public claims Pi is accountable to RBI's TAT clock or credits compensation automatically. We're the liability layer underneath — Pi talks, we guarantee the deadline. |
| **"Who actually owes the ₹100/day — Paytm or the bank?"** | Under the TAT framework it sits with whichever entity holds the money, typically the bank on the UPI leg. Paytm's direct exposure is twofold: its own settlement obligations to merchants as a PA/PSP, and being the merchant's only point of contact — so Paytm absorbs the grievance, the CSAT hit and the Ombudsman case regardless of who caused it. *(Verify against the RBI annex table before presenting.)* |
| **"Isn't incident clustering 20-year-old ITSM?"** | Yes, and we say so on the slide. Zendesk and ServiceNow both do it. What nobody does is wire the **agent** to it — Zendesk's own docs say AI agent tickets can't be linked to incidents — or point the result at a regulatory deadline. |
| **"Outages are rare. What does it do the other 350 days?"** | Technical declines ran 0.02% in Feb 2025, which is exactly why we didn't build this for outages. Every delayed settlement starts a clock. Incident mode is the stress test; the clock engine is the product. |
| **"Can it move money wrongly?"** | Policy is code, not the LLM: amount caps, fraud flags, human approval above ₹5,000. We report a measured false-action rate with its confidence interval and an adversarial set built to induce wrongful payouts — not a claim of zero. |
| **"Can it invent a policy, like Air Canada's bot?"** | Structurally, no. It has no authority to state a policy that isn't in the rule file, and the rule file is a machine-readable encoding of published circulars. Every reply cites the clause and the tool result. |
| **"What if two problems cluster together?"** | Clusters require matching bank, error code and time window, and each case is re-checked against the root cause before linking. Mismatches fall back to the normal path. |
| **"What if the zero-question guess is wrong?"** | The merchant taps No and types normally. Nothing is lost. |
| **"Can a learned playbook go wrong?"** | A human approves every playbook, and it runs only on exact pattern matches. |
| **"Isn't this just a status page?"** | A status page tells you something is broken. We compute your deadline, chase your money, credit what you're owed, and prove it moved. |
| **"Your numbers look made up."** | Every number comes from our eval run on labelled synthetic data, against an ablation baseline — the same agent with our engines switched off, not a strawman. We state the n. |
| **Demo risk** | Pre-recorded backup; outage demo can run from cached results. |

---

## Open items before the deck is final

1. **Read the RBI TAT annex table directly** and confirm the per-rail, per-entity allocation of the ₹100/day. The brief currently works from secondary coverage.
2. **Confirm the NPCI Feb 2025 auto-chargeback scope** — reporting suggests it's limited to bulk upload + UDIR. Do not overstate it.
3. **Check whether the RBI Integrated Ombudsman Scheme was revised in 2026.** Secondary sources mention a 2026 version with different compensation caps; this is unconfirmed and the 2021 figures conflict across sources.
4. **Pull the latest Paytm merchant numbers** from the most recent quarterly release rather than Q2 FY26.
5. **Decide the name.** "Ripple" collides with a well-known payments/crypto company — a bad collision in a payments hackathon. **T+0** is recommended; **Tatkaal** is the fallback.
