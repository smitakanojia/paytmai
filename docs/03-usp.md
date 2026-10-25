# The final USP

*This supersedes the USP section of `02-revised-brief.md`. The deck is built on this.*

## Three layers, in the order you present them

**1. The hook — "the agent that pays."**

> Every other agent ends in a sentence. Ours ends in a credit.
>
> It pays merchants money they did not know they were owed, before they ask,
> because a regulator says they are owed it.

Support is the only function at Paytm whose output is a sentence. Every other
function ships money. This is the line that makes a room sit up, and it is
literally true — we searched for a deployed product that pays regulatory
compensation unprompted and found nothing. Adjacent things exist (an agent that
refunds a fee when a customer complains, provisional credit on a fraud dispute),
but all of them are triggered by a customer asking.

**2. The moat — the liability clock.**

Four published deadlines on every stuck rupee: RBI TAT (T+1, then ₹100/day
automatically), NPCI UDIR (3 days → auto-chargeback), the settlement cycle
(silence loses the chargeback), RBI-IOS (30 days → Ombudsman). Every support
product optimises deflection. None optimises the clock.

This is what makes the hook defensible rather than a slogan: the agent isn't
being generous, it's being compliant. And it is why Sierra, Decagon, Fin and
Agentforce can't follow — they're US products with no concept of TAT or UDIR.
That's a market gap, not a feature gap.

**3. The business case — Pi's missing SKU, and the buyer is the bank.**

This is the slide for Paytm leadership specifically.

Paytm Intelligence (Pi) launched September 2026: sales, service and ops agents
sold to banks, lenders and insurers. $11M+/yr saved, 10M+ queries/month. Pitching
Paytm a support cost saving is pitching them their own P&L slide.

So don't. Instead:
- Pi optimises the conversation. Nothing in it answers to a TAT deadline.
- **Banks** are who owes the ₹100/day, who loses chargebacks by missing a
  settlement cycle, and who collects the Ombudsman cases — **13.34 lakh in FY25,
  up 13.5%**, 16.86% of them mobile/electronic banking.
- Pi is already selling to banks. T+0 is the agent in that catalogue they'd pay
  the most for.

You are pitching a **new SKU for the business they launched six weeks ago**, not a
cost saving. That is the reframe.

*(State this as our proposal, not as a claim about Paytm's roadmap.)*

## The remedy ladder

The USP made concrete, and it ends in revenue rather than cost:

| Rung | Action | Why it matters |
|---|---|---|
| 1. **Inform** | "Your ₹5,000 from 2:14 pm is held at the bank. Due back by 6 pm tomorrow." | Table stakes, done properly — with the deadline attached |
| 2. **Chase** | Raises the UDIR entry inside the settlement cycle, pushes the reversal | Autonomous, no human in the path |
| 3. **Compensate** | Computes the TAT credit at ₹100/day and pays it, citing the clause | **Nobody does this.** The merchant never asked |
| 4. **Bridge** | Advances the settlement when the supplier can't wait | **The sales moment.** Paytm already sells instant settlement and already runs delinquency models to price it |

Rung 4 replaces the churn-catcher idea, which was dead on arrival because Paytm
already predicts merchant churn in production.

## Why this answers Track 3 better than a cost pitch

The track asks for an *autonomous* teammate. Most teams will argue autonomy
because it's cheaper — and that loses to a judge who points out a human is more
accurate. Three arguments that don't lose:

1. **A T+1 clock cannot queue.** Thousands of simultaneous deadlines, each with
   its own amount and expiry hour, is not work a human queue can hold at any
   staffing level.
2. **Nobody opens the ticket.** A merchant can't ask for compensation they've
   never heard of. If the agent waits to be asked, the money is never paid.
3. **The window is a settlement cycle, not a business day.** Silence doesn't
   postpone the chargeback; it loses it.

> A human is more accurate than an agent. A human is not faster than a clock.

## What this does to the old pitch

| Old | New |
|---|---|
| Incident clustering is the USP | Clustering is the *mechanism* for holding T+0 under load — and we concede the ITSM prior art openly |
| "Gets cheaper every day" | Demoted to an engineering detail (Decagon's Duet Autopilot already does it) |
| Churn catcher | Replaced by rung 4, Bridge |
| Cost per ticket | ₹ of liability avoided |
| Fires on ~5–20 outage days/year | Fires on every delayed settlement, every day |
