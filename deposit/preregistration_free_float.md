# Pre-registration: 168-hour unoccupied free-float thermal-identification experiment

*De-identified deposit version accompanying the paper "A measurement-and-verification-firewalled
living lab in an occupied single-family home" (reference [4]). This is the pre-registered analysis
plan cited as the paper's example of research-plane pre-registration. Project-internal identifiers
(house, zone, device, file, and vendor names) have been generalised; the freeze statement, the
falsifiable hypotheses, the frozen decision thresholds, and the method specification are reproduced
faithfully. No personal or household data appears.*

> **Pre-registration statement.** This plan was frozen **before data collection**. The git-commit
> timestamp of the source file is the scientific record of when it was frozen; no analysis decision
> in the sections below could be changed after data collection began. Exploratory follow-on tracks
> are explicitly out of scope of this pre-registration and carry their own separate documents.

## 1. Goal
Identify, for each of ten facade thermal zones (denoted F1–F10) of an occupied single-family house
during a 168 h **unoccupied free-float** experiment (control suspended, the house released to its
passive baseline): the per-zone solar-gain coefficient `θ_solar`, the external thermal resistance
`R_ext`, and a rain-modulated drying coefficient `θ_rain`. Deliverables: a validated `θ_solar`
artefact to seed/cross-check the online RLS learner; a heat-loss (`R_ext`) ranking to drive
retrofit prioritisation; a quantified evaporative-cooling signal from any rain events; and a
reproducible framework for a spring reference-float cross-check.

## 2. Pre-registered hypotheses
All hypotheses are falsifiable, each tested against a frozen decision rule (Section 4). Two zones
are designated *control zones* (C1, C2) for the null tests. "BV-zones" denotes the seven
cellar-adjacent zones.

| # | Hypothesis | Rationale |
|---|---|---|
| **H1** | `R_ext(C1) ≥ 2 × median{R_ext(other 9 facade-zones)}` | C1 is the designated well-insulated reference zone (operator domain knowledge) |
| **H2** | Zone rank-order by `R_ext` is robust: ≥ 4 adjacent rank-pairs have non-overlapping ±1 SE intervals | the retrofit-priority list is publishable |
| **H3** | `θ_rain` (coefficient on `rain_active`, τ = 6 h) is significant (`|t| > 2`) for ≥ 1 facade-zone, conditional on ≥ 1 rain event of ≥ 0.5 mm cumulative in the window | evaporative drying tail observable |
| **H4** | Diurnal residual power ratio (FFT power at the 24 h period) < 0.10 for ≥ 7 of 10 facade-zones | a 1R1C model is sufficient at first tier |
| **H5** | LR-test χ² for adding a cellar-floor coupling term is `< 3.84` (p < 0.05, 1 dof) for ≥ 6 of 7 BV-zones | cellar coupling physically negligible (operator domain knowledge) |
| **H6** | `median{ \|θ_sat − θ_om\| / θ_sat }` across facade-zones is `< 0.15` | satellite and reanalysis-forecast solar sources are interchangeable |
| **H7** | Per-day stability `SD_7days(θ_solar) / mean(θ_solar) < 0.20` for ≥ 8 of 10 facade-zones | no latent confounders (shading, sensor drift) over the week |
| **H8** | AS-IF `θ_solar` fit on the control zones (C1, C2, with an arbitrary façade orientation as a placebo) has `\|t\| < 2` | no spurious solar leakage via inter-zone coupling |

## 3. Method specification (frozen)
**Pre-processing.** Filter to the valid free-float phase with no abort flags; a 5-point
Savitzky-Golay analytic derivative kernel; trim 2 boundary rows each end; a rain-decay state
`rain_active[t] = max(rain_active[t−1]·exp(−Δt/τ), 1 if rain>0.1 mm/h else 0)` with τ = 6 h
pre-fixed; an integer day index 0..6.

**Two-step identification** (night/day separation, after Bacher & Madsen 2011 §4):
- *Step 1 — night fit for R coefficients.* Mask to low-solar rows (`GHI < 20 W/m²`); design columns
  `[(T_out − T), (T_cellar − T optional per zone), rain_active]`; weighted least squares; outputs
  `1/R_ext`, optional `1/R_floor`, and `θ_rain` with standard errors.
- *Step 2 — day fit for `θ_solar` with R fixed.* Mask to solar rows (`GHI ≥ 20 W/m²`); regress the
  R-explained residual of `dT/dt` on the facade insolation column; output `θ_solar` with SE.
- A one-step simultaneous fit is run in parallel; the two-step vs one-step β difference is reported
  as a multicollinearity-bias estimate (expected `< 0.20` for well-conditioned zones).

**Weights.** Rows during a hot-water draw are down-weighted (0.3, as it disturbs the cellar); rain
rows are kept at full weight (rain is the signal); otherwise 1.0.

**Frozen sub-tests (each with a pre-fixed threshold):**
- *Cellar-floor LR test (H5):* per BV-zone, reduced vs full night-only model, `χ² = −2(L_red −
  L_full)`, keep the floor term if `χ² > 3.84`; H5 passes if ≥ 6 of 7 zones reject it.
- *Per-day `θ_solar` (H7):* fit Step 2 per day with R held fixed; statistic `SD_d/mean_d`.
- *Hold-out CV:* train on the first ~5 days, forward-Euler predict the last 2; pass if
  `RMSE_test/RMSE_train < 1.5`.
- *Diurnal residual FFT (H4):* per-zone residual FFT, diurnal-bin power ratio `< 0.10`; aggregate
  pass if ≥ 7 of 10 zones.
- *AS-IF null (H8):* add a placebo façade column on the control zones; require `|t| < 2`.
- *Source-equivalence (H6):* compare satellite vs reanalysis-forecast solar drivers per zone.

## 4. Decision rules
Each hypothesis maps to exactly one of the frozen thresholds above; a hypothesis is recorded as
supported or refuted by that rule, with no post-hoc threshold changes. Null and refuted results are
reported, not dropped. A logged data-availability caveat records which series are frozen vs still
logging during the control-suspended window, so no analysis silently pools non-representative data.

## 5. Pre-registration integrity
The plan was committed before the data-collection start; the version-control timestamp is the freeze
record. The hypotheses, thresholds, and method above were fixed at that commit. This deposit is the
de-identified form of that frozen record.
