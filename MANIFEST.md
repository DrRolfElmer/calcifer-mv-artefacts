# Deposit manifest — platform-paper supplementary

Unlike the skills deposit (which assembles verbatim from canonical sources), these two files are
**de-identified derivatives** authored specifically for public release — so they are committed here
directly and ARE the canonical public form. The private internal originals are not released.

| In bundle as | Repo source (public derivative) | Backs ref |
|---|---|---|
| `deposit/three_plane_mv_firewall.md` | `docs/drafts/paper9a_platform/supplementary/deposit/three_plane_mv_firewall.md` | [3] |
| `deposit/preregistration_free_float.md` | `docs/drafts/paper9a_platform/supplementary/deposit/preregistration_free_float.md` | [4] |
| `README.md` | `.../supplementary/README.md` | — |
| `LICENSE` | `.../supplementary/LICENSE` | — |
| `.zenodo.json` (metadata) | `.../supplementary/.zenodo.json` | — |

## Assembly (run from the repo root)
```
mkdir -p build/a3_supplementary/deposit
cp docs/drafts/paper9a_platform/supplementary/deposit/*.md build/a3_supplementary/deposit/
cp docs/drafts/paper9a_platform/supplementary/README.md    build/a3_supplementary/README.md
cp docs/drafts/paper9a_platform/supplementary/LICENSE      build/a3_supplementary/LICENSE
cp docs/drafts/paper9a_platform/supplementary/.zenodo.json build/a3_supplementary/.zenodo.json
( cd build && zip -r a3_supplementary_v1.zip a3_supplementary )
```

## Pre-upload checklist
- [ ] Re-read both deposit `.md` files with a public eye: confirm no residual project-internal
      identifier (host, path, device/flow id, vendor, section number) and no occupant/PHI detail —
      the occupant-environment plane may appear ONLY as the firewall rule that isolates it.
- [ ] `.zenodo.json` `related_identifiers` updated with the paper's arXiv id once assigned.
- [ ] Author ORCID/affiliation confirmed; co-authors added if any.
- [ ] License consistent across README, LICENSE, and `.zenodo.json` (CC-BY-4.0).
- [ ] Cross-check the FMEA's CF1–CF6 against the paper's §2 guard list (same six, same labels).
- [ ] Cross-check the pre-registration's eight hypotheses against the paper's pre-registration
      description (§2 / §3.5 evidence).
