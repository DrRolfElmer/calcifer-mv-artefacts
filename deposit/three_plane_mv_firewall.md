# The three-plane measurement-and-verification firewall (FMEA)

*De-identified deposit version accompanying the paper "A measurement-and-verification-firewalled
living lab in an occupied single-family home" (reference [3]). This is the failure-mode-and-effects
analysis (FMEA) of the cross-plane corruption paths and the guards that close them. Project-internal
identifiers (device, file, and vendor names; internal cross-references) have been generalised; the
architecture and the guard logic are reproduced faithfully. No personal or household data appears.*

## 1. Why a firewall at all
The system is simultaneously a **live controller**, a **published-savings measurement system**
(IPMVP Option C), and a **research programme** (thermal identification, occupant-environment studies,
price forecasting). Each plane needs the others' data, yet each can *corrupt* the others if they
share state:
- a control tweak that alters the frozen baseline would silently invalidate every published savings
  number;
- a research experiment that feeds a signal into the controller would make control behaviour depend
  on unvalidated science;
- pooling experiment-contaminated data into the attribution baseline would bias the headline result.

The firewall is the standing rule that **each plane reads what it needs but writes only its own
state.**

## 2. The three planes

| Plane | What it is | Reads | Writes |
|---|---|---|---|
| **Control** | the model-predictive optimiser + device dispatch — produces cost/comfort-optimal setpoints | prices, forecasts, **recursively-estimated (RLS) coefficients as read-only inputs**, device state | device commands only |
| **Analytics (M&V / IPMVP Option C)** | the **frozen whole-facility weather-normalised baseline** + savings attribution | the frozen baseline + metered energy | the savings log only |
| **Research** | experiments and science: thermal identification, occupant-environment studies, price-forecast shadow, phase analysis | **copies** of any data | its own research logs only |

**The load-bearing asymmetries:**
- RLS coefficients are **read-only inputs to Control**; a re-fit would **invalidate IPMVP
  attribution**, so the Analytics baseline is **frozen** and never re-reads them.
- Research-only signals are **research-only**; **no research metric enters the control objective**.
- The cost/billing model is **frozen**; the vendor tariff API is **observer-only**, the invoice
  authoritative.

## 3. Cross-contamination FMEA

| # | Failure mode (plane → plane) | How it would corrupt | Guard(s) |
|---|---|---|---|
| **CF1** | **Control → Analytics** — a control change edits the frozen baseline | every published savings number silently invalidated | baseline **byte-hashed at runtime** (the server refuses to start on a byte change) + a pre-commit exclusion + an **"M&V-firewall-safe"** review gate on every deploy |
| **CF2** | **Research → Control** — an experiment/sensor feeds a signal into the control objective | control behaviour depends on unvalidated science / a corrupt input | research signals are research-only; any price/forecast input is a **validated, typed** input with a fail-safe posture, never a raw research feed; new inputs enter **shadow-emit** (observation-only, kill-switched) before any control cutover |
| **CF3** | **Research → Analytics** — experiment-window data pooled into the frozen-baseline study | headline savings biased by a non-normal-operation regime | an **experiment-window masking invariant** (every analysis checks its window against the registry of active experiment windows and excludes contaminated data), enforced by a shared loader |
| **CF4** | **Analytics ↔ Control coupling via RLS** — an RLS re-fit leaks into the frozen baseline (or vice versa) | attribution invalidated by a moving model | coefficients are read-only to Control; the frozen baseline never reads RLS; the off-season RLS buffer freezes heating-season coefficients |
| **CF5** | **Cost/tariff drift → Analytics** — a changed API tariff silently re-bases savings | savings recomputed on an unauthorised cost model | the cost model is frozen; the tariff API is **observer-only** with a drift warning; the **invoice is authoritative** |
| **CF6** | **Vendor-API malformation → any plane** | a corrupt upstream (prices, weather, device state) silently drives control or analysis | vendor API loops **validate response shape** before processing |

## 4. The one-line test every change must pass
> *Which plane does this change WRITE to — and does it read only, never write, any other plane's
> state?*

If a change writes device commands, it is Control and must not touch the baseline (CF1) or depend on
a research feed (CF2). If it touches the baseline, that is a deliberate re-freeze ceremony, never a
side effect. If it is research, it reads copies and writes only research logs (CF3). This one-line
test is carried on every incident-register note.

## 5. Scope
This is the **methodological / M&V** firewall — distinct from the separate whole-home **resilience**
FMEA (hardware and dependency layers). Both are referenced together in the paper as the project's
firewall discipline; they are kept distinct in the source record.
