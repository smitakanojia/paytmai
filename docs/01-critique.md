# Critique of the Ripple brief — Paytm AI Hackathon Track 3

Reviewed 25 Oct 2026 against web research. Every claim below is sourced at the
bottom. Read this before the rewrite in `02-revised-brief.md`.

---

## Verdict in one paragraph

The brief is well organised and the writing is disciplined, but the **positioning
is wrong for this specific judge panel**, and two of the three claimed novelties
have prior art that a judge can find in under a minute. The pitch is a
*support-cost-efficiency* story. Paytm launched its own AI agent product in
September 2026, already saves $11M+/year on exactly this, handles 10M+ queries a
month, and is now **selling that capability to banks and insurers**. Pitching
cheaper support to Paytm is pitching Paytm's own P&L slide back at them. The
brief needs to stop competing with Paytm's roadmap and start plugging into it.

There is a far stronger USP available, it is India-specific, it is
regulation-backed, it is measured in rupees, and no deployed product on earth
has it. It is below.

---

## 1. The fatal gap: the brief does not know about Paytm Pi

This is the single most dangerous omission. In **September 2026** — one month
before this brief was written — Paytm launched **Paytm Intelligence ("Pi")**, an
enterprise agentic-AI product sold to banks, lenders and insurers in India and
the UAE. Pi's agents cover **sales, customer service and operations**, built on a
financial-services model trained on ~2 years of Paytm's own transaction data.

Paytm's own published numbers: **$11M+ annual cost savings, split ~$6.5M customer
care + ~$4.8M merchant care**, on **10M+ queries per month**.

What this does to the brief:

| Brief says | Reality |
|---|---|
| "Paytm itself already runs AI support internally" | It is no longer internal. It is a **launched commercial product**. |
| "we position Ripple as the layer above" | Fine instinct, but the brief never names Pi, so the slide reads as if you didn't check. |
| USP #2 "gets cheaper every day" | Cost-per-ticket reduction *is Pi's headline claim*. You are selling them their own number. |
| USP #4 "churn catcher" | Paytm's Q2 FY26 release states **AI models already predict merchant churn and delinquencies**. This is in production. |

**A judge who works on Pi is likely in the room.** Not naming Pi is the
difference between "these kids did the work" and "these kids googled for an hour."

---

## 2. Two of your three "ours alone" claims have prior art

The brief states: *"Not found in anything we reviewed: incident clustering,
zero-question opening, and self-compiling playbooks. Those are ours."*

**Incident clustering — prior art, roughly 20 years of it.**
- **Zendesk** ships a native ITIL incident practice: major incidents get
  declaration/communication fields and **a list of linked child incidents**;
  recurring-pattern detection opens a problem record with repeat incidents
  already attached. Its older model (link many duplicate tickets to one problem
  ticket, solve the problem, all children close) has existed for years.
- **ServiceNow** has formal Major Incident Management; practitioners auto-promote
  a parent to Major Incident at ≥10 children, and **Task Intelligence** ships a
  similar-incident prediction model for auto-linking.
- AIOps alert correlation (PagerDuty, BigPanda, Moogsoft) is the same pattern.

**Self-compiling playbooks — prior art, shipped and named.**
- **Decagon** runs on **Agent Operating Procedures (AOPs)**. Its **Duet** feature
  drafts AOPs from past transcripts; **Duet Autopilot** turns live-conversation
  signals into proposed agent updates, **human-approved before going live**.
  That is, line for line, your USP #2.
- **Sierra** has **Ghostwriter**, which builds and modifies agents from operating
  procedures and transcripts.

**Zero-question opening — weakest claim, but survives.** I found no deployed
product doing authenticated-context-first opening in payments support. Still, any
judge will say "that's just personalisation," and they're half right. It is a
*feature*, not a USP. Demote it.

### The honest novelty that IS defensible

Here is the version of the clustering claim that is true and citable:

> Incident correlation has existed in ITSM for twenty years. Autonomous agents
> exist. **Nobody has wired the two together.** Zendesk's own documentation
> states that **AI agent tickets cannot be linked to problem and incident
> tickets.** So when a bank goes down, the ITSM layer sees one incident and the
> AI layer re-diagnoses it five thousand times.

That is a *verifiable gap in a named competitor's own docs*. It is worth ten
times more than "nobody does this," because it survives the judge checking.

---

## 3. The base-rate problem: you built a product for a rare day

NPCI data puts the UPI **technical decline rate at 0.02% (Feb 2025)**. Genuine
nationwide outages happen a handful of times a year — the March 2025 event was
roughly an hour. One vendor blog claims 0.3–0.5% declines, well above the
official figure; treat that as unverified.

So: **your core USP fires maybe 5–20 times a year.** The obvious judge question
is *"what does the agent do on the other 350 days?"* The current brief has no
answer, because incident mode is the headline and everything else is
table stakes.

The reframe in §5 fixes this completely — because every delayed settlement has a
clock running, outage or not. Outage mode stops being the product and becomes the
**stress test** of the product.

---

## 4. Claim-by-claim fact check

| Brief claim | Verdict | Correction |
|---|---|---|
| Paytm AI "cut support costs by over $11M a year" | ✅ Accurate | But it's now **Pi, a launched product** ($6.5M customer care + $4.8M merchant care, 10M+ queries/mo). Name it. |
| "crores of merchants" | ⚠️ Vague | Use real figures: **13.7 Mn merchant subscriptions (Q2 FY26)**, **~1.17 Cr Soundbox base (Q3 FY25)**. Precision reads as homework. |
| Klarna "AI-first support criticised, rehired people" | ⚠️ Oversimplified | Klarna: ~2.3M chats in month one (2024), company-equated to ~700 FTE. May 2025, CEO said cost had become too dominant a factor and quality fell; they rehired via a **flexible/gig remote model and kept the AI running**. Don't say "AI failed" — say "deflection-optimised AI failed." That framing serves your pitch. |
| Air Canada chatbot liability | ✅ Accurate | Cite properly: ***Moffatt v. Air Canada*, 2024 BCCRT 149**, 14 Feb 2024, **CAD $812.02** total. Tribunal rejected the argument that the chatbot was a separate legal entity; the airline is responsible for all information on its site, static page or chatbot alike. |
| Intercom Fin "resolved can mean customer went quiet" | ⚠️ Unverified | Fair as a category critique of deflection metrics, but I could not source it to Intercom. Frame as an industry-wide metric critique, not a named-vendor accusation. |
| Cashfree Spark "~60% of tickets resolved by AI" | ❌ Unverified | I could not source this. Drop the number or mark it as vendor-claimed. A fabricated competitor stat is the worst possible thing to be caught on. |
| Salesforce Agentforce "cannot reach out proactively" | ❌ Likely wrong | Agentforce has proactive/outbound capability. Delete this line; it's an easy kill for a judge. |
| Sierra/Decagon "3 to 7 month rollouts" | ⚠️ Vendor-sourced | This comes from competitors' comparison blogs. Soften to "enterprise rollouts measured in months, per third-party comparisons." |
| "none of the deployed products do this" (clustering) | ❌ False | See §2. |

---

## 5. The USP you should actually pitch

### The insight

In Indian payments, a failed or delayed transaction is **not a support ticket. It
is a timer with money attached.** There is a stack of hard, legally enforceable
deadlines on every single one:

- **RBI TAT circular** (DPSS.CO.PD No.629/02.01.014/2019-20, 20 Sep 2019): failed
  UPI transaction → auto-reversal due by **T+1**; beyond that, **₹100 per day is
  payable to the customer — automatically, without the customer filing a claim.**
- **NPCI UDIR**: a **P2M** complaint with no response in **3 days auto-converts
  into a chargeback**. P2P auto-chargebacks if the beneficiary bank is silent at T+1.
- **NPCI circular, 10 Feb 2025** (effective 15 Feb): chargebacks are
  **auto-accepted or auto-rejected based on TCC/RET raised in the next settlement
  cycle**. Miss a settlement cycle and the case is decided against you by default.
  (Scope is narrow — bulk upload + UDIR — don't overstate it.)
- **RBI ODR mandate** (6 Aug 2020, live 1 Jan 2021): authorised PSOs **must** run
  an online dispute resolution system for failed-transaction disputes.
- **RBI Integrated Ombudsman Scheme**: unresolved at **30 days** → the customer
  can go to the Ombudsman, with awards against the entity.

### The gap

**Every support product on earth optimises deflection** — did the customer stop
talking? **None optimise the clock** — did the money move before the deadline, and
was the compensation paid when it didn't?

I searched specifically for this. Vendor tooling has generic "SLA countdown"
timers. **I found nothing that ties an autonomous agent to a regulatory deadline
with an automatic compensation payout.** Sierra, Decagon, Intercom Fin and
Agentforce are US products — they have no concept of TAT, UDIR, TCC/RET or
Ombudsman timelines. This is not a feature gap they can close in a sprint; it's a
market gap that needs an India payments team.

### The pitch

> **T+0.** RBI gives you T+1. We settle at T+0.
>
> Every other agent closes tickets. Ours closes the regulator's clock — before it
> becomes a chargeback, a ₹100-a-day penalty, and an Ombudsman case.

### Why this beats the current USP on all five axes

| | "Incident mode" | "T+0 liability clock" |
|---|---|---|
| Fires how often? | ~5–20 outages/year | **Every delayed settlement, every day** |
| Prior art? | 20 years of ITSM | **None found** |
| Competes with Paytm Pi? | Yes — same cost story | **No — Pi plugs into it** |
| Metric a CFO signs? | Cost per ticket (Pi owns this) | **₹ of liability avoided** (new number) |
| Why autonomy? | It's cheaper | **A T+1 deadline cannot wait in a human queue** |

That last row is the one that wins Track 3. The track asks for an *autonomous*
teammate. "Autonomous because cheaper" is weak — a human is more accurate.
"Autonomous because the regulator's clock expires in 14 hours and there are 5,000
of them" is unanswerable.

### Safety comes free

Air Canada lost because its bot **invented a policy**. T+0's policy layer is not
invented — it is a machine-readable encoding of published RBI and NPCI circulars,
and every reply cites the clause plus the tool result it came from. The line for
the deck:

> Our agent cannot invent a policy, because it has no authority to state one that
> isn't in the rule file — and every rupee and timestamp in a reply traces to a
> tool call.

### Where incident mode goes

**Keep it. Demote it from USP to mechanism.** When one bank drops, 5,000 clocks
start at the same instant. One diagnosis → 5,000 clocks stopped before T+1. You
are no longer claiming clustering is novel; you are claiming it is **how you keep
the T+0 promise at scale.** This kills the prior-art attack outright.

---

## 6. Accuracy risk you must handle before the Q&A

**Who actually owes the ₹100/day?** Under the TAT circular the liability sits on
the entity in the chain holding the money — for the UPI leg, typically the
remitter/beneficiary **bank**, not necessarily Paytm. Do not assert on a slide
that Paytm pays it. The honest and *stronger* framing:

> The clock sits on whichever entity holds the money. Paytm's direct exposure is
> (a) its own settlement obligations to merchants as a PA/PSP, and (b) being the
> merchant's only point of contact — so Paytm absorbs the grievance, the CSAT hit
> and the Ombudsman case regardless of which bank caused it.

Knowing that nuance and saying it unprompted is worth more than any slide. **Verify
the exact entity-wise allocation in the RBI annex table before you present it** —
I worked from secondary sources for the per-rail breakdown and did not read the
annex itself.

---

## 7. Evaluation: the current plan will not survive a technical judge

| Problem | Fix |
|---|---|
| **Strawman baseline.** "A normal one-ticket-at-a-time agent" that you wrote yourself. Judges assume you nerfed it. | **Ablation baseline**: the exact same agent with the incident engine and clock engine switched off. Same prompts, same tools, same model. Now the comparison is honest and you can say so. |
| **n = 50 tickets.** Too small for any per-category claim. | 300+ across categories, or report per-category n and stop making per-category claims. |
| **"Wrong autonomous actions must stay at 0."** Zero on 50 samples is not evidence; it's a small sample. | Report **false-action rate with a confidence interval**, and add an **adversarial set** explicitly designed to trick the agent into paying compensation that isn't owed or closing a clock that hasn't stopped. Reporting a non-zero number you caught yourself reads as *more* rigorous, not less. |
| No clock metrics at all. | Add: % clocks stopped before T+1 · ₹ compensation correctly computed vs ground truth · % chargebacks pre-empted inside the settlement cycle · ₹ liability avoided vs baseline. |
| "Verified resolution rate" is good but underspecified. | Define the only acceptable close state: **money moved AND clock stopped AND compensation paid if owed.** |

---

## 8. Kill list

1. **The name "Ripple."** In a *payments* hackathon, naming your product after a
   well-known crypto/payments company (Ripple / XRP) is an unforced error.
   Suggested: **T+0** (the pitch is the name), or **Tatkaal**.
2. **"Gets cheaper every day" as a headline USP** → engineering detail. Decagon
   Duet does this; Pi already claims the savings.
3. **"Churn catcher"** as written → Paytm already predicts merchant churn in
   production. Reframe as **compensation-aware retention**: the merchant owed
   ₹100/day gets it credited automatically with an apology — a retention action
   that is simultaneously a compliance action. That version is novel.
4. **The Agentforce "cannot reach out proactively" line** → factually shaky.
5. **The unverified Cashfree 60% figure** → cut or label vendor-claimed.
6. **4-minute demo script** → this is the **PPT round**. The deck is the
   deliverable. Compress the demo to a 45-second storyboard on one slide and
   spend the reclaimed space on the clock engine and the liability metric.

---

## Sources

- [RBI — Harmonisation of TAT and customer compensation for failed transactions](https://www.rbi.org.in/commonman/English/scripts/Notification.aspx?Id=3074)
- [RBI — Index to circulars (ODR for digital payments; Ombudsman escalation)](https://m.rbi.org.in/Scripts/BS_CircularIndexDisplay.aspx?Id=11946)
- [Khaitan & Co — RBI rolls out ODR framework for digital payments](https://www.khaitanco.com/thought-leaderships/RBI-rolls-out-framework-for-online-dispute-resolution-systems-for-digital-payments)
- [Razorpay — All you need to know about NPCI-led UDIR](https://razorpay.com/blog/all-you-need-to-know-about-npci-led-udir/)
- [Business Standard — New UPI rule on automatic acceptance/rejection of chargebacks from Feb 15](https://www.business-standard.com/finance/personal-finance/new-upi-rule-on-automatic-acceptance-rejection-of-chargebacks-from-feb-15-125021200820_1.html)
- [Paytm — Q2 FY26 earnings release](https://paytm.com/document/ir/financial-results/fy2025-26/Earnings-Release_FY26-Q2-USD.pdf)
- [MediaNama — Paytm forays into enterprise AI with 'Paytm Intelligence'](https://www.medianama.com/2026/09/223-paytm-paytm-intelligence/)
- [Free Press Journal — Paytm bets big on AI, plans to offer AI agents to banks and insurers](https://www.freepressjournal.in/business/paytm-bets-big-on-ai-plans-to-offer-ai-agents-to-banks-and-insurers-in-india-and-uae)
- [Zendesk — Working with problem and incident tickets](https://support.zendesk.com/hc/en-us/articles/4408835103898-Working-with-problem-and-incident-tickets)
- [Zendesk — Release notes through 2026-09-25 (ITIL incident practice, linked child incidents)](https://support.zendesk.com/hc/en-us/articles/11298618235290-Release-notes-through-2026-09-25)
- [ServiceNow — Incident Management (major incident workflows)](https://www.servicenow.com/products/incident-management.html)
- [ServiceNow Community — Automatically link similar incidents (Task Intelligence)](https://www.servicenow.com/community/incident-management-forum/automatically-link-similar-incidents/m-p/3506933)
- [Decagon vs Sierra comparison — AOPs, Duet, Duet Autopilot, Ghostwriter](https://www.dragapp.com/blog/decagon-vs-sierra/)
- [Business Standard — UPI outages traced to tech glitches (0.02% technical decline, Feb 2025)](https://www.business-standard.com/finance/news/upi-outages-likely-caused-by-telecom-network-issues-hardware-glitches-125040801048_1.html)
- [Business Standard — NPCI attributes UPI outage to year-end transaction rush](https://www.business-standard.com/industry/banking/npci-attributes-upi-outage-to-year-end-transaction-rush-at-banks-125040100968_1.html)
- [Forbes — Klarna reverses AI push, says customers prefer human support](https://www.forbes.com/sites/quickerbettertech/2025/05/18/business-tech-news-klarna-reverses-on-ai-says-customers-like-talking-to-people/)
- [CRT — *Moffatt v. Air Canada*, 2024 BCCRT 149](https://decisions.civilresolutionbc.ca/crt/crtd/en/525448/1/document.do)
- [Paytm for Business — What is my settlement cycle?](https://business.paytm.com/support/what-is-my-settlement-cycle)
