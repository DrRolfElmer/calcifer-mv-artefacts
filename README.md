# Supplementary material — M&V firewall FMEA and a pre-registration record

Supplementary deposit for the paper *"A measurement-and-verification-firewalled living lab in an
occupied single-family home"* (platform paper, "A3"; arXiv eess.SY).

## What this is
De-identified deposit versions of two project-internal documents the paper cites as evidence, so a
reader can inspect the actual artifacts rather than take them on faith:

| File | Backs paper reference | What it is |
|---|---|---|
| `deposit/three_plane_mv_firewall.md` | [3] | The failure-mode-and-effects analysis of the three-plane (Control / Analytics / Research) firewall — the six cross-plane corruption paths (CF1–CF6) and the guards that close them. |
| `deposit/preregistration_free_float.md` | [4] | A pre-registered analysis plan (eight falsifiable hypotheses with frozen decision thresholds, frozen before data collection) — the paper's example of research-plane pre-registration. |

## De-identification
Both files are **de-identified derivatives**, not the verbatim internal documents. Generalised:
house, zone, device, flow, file, and vendor names; internal cross-references and section numbers.
Preserved faithfully: the architecture and guard logic (FMEA); the freeze statement, the falsifiable
hypotheses, the frozen thresholds, and the method specification (pre-registration). **No personal or
household data appears** — the occupant-environment research plane is referred to only at the level
needed to state the firewall rule that isolates it from control.

## Not included
The controller source code (on a staged 2028 release path) and any raw data. The other internal
references the paper cites ([5] code review, [6] incident-register note, [7] deploy record) are
available on request as de-identified excerpts; they are not part of this deposit.

## Citation and license
Cite the paper as the primary reference; cite this deposit by its Zenodo DOI (assigned on upload).
Licensed CC-BY-4.0 — see `LICENSE`. Metadata in `.zenodo.json`; see `MANIFEST.md` for the bundle.
