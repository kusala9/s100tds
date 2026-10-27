# S-101 Update Permutation Test Dataset

A normative set of S-101 ENC base cells and their corresponding update files (`.000`/`.001`),
covering every distinct class of dataset update defined by the S-101 ENC Product Specification
(Ed. 2.0.0, Annex B-6 "Update Dataset Structure") and S-100 Part 10a (§10a-4.7 "Order of
records"). Each class has at least one worked, compiled example.

## What's here

```
feature-catalogues/
  S-101_FC_2.1.0_20260327.xml     the real IHO S-101 feature catalogue, edition 2.1.0
  S-101_FC_2.10.0_20260902.xml    a test feature catalogue, edition 2.10.0 -- adds one
                                   capability beyond the real IHO catalogue (see below)
upd/
  2.1.0/
    base/UPDBASE.000               the base cell, compiled against FC 2.1.0
    cases/<ID>/                    one base+update pair per test class, compiled against FC 2.1.0
  2.10.0/
    base/UPDBASE.000               the same base cell, compiled against FC 2.10.0
    cases/<ID>/                    the same test classes, plus 6 more that need FC 2.10.0's
                                   extra capability, compiled against FC 2.10.0
docs/
  update-permutation-catalogue.md  the full class catalogue: every ID, what it tests, and the
                                   exact S-101/Part 10a clauses it's derived from
  case-index.md                    one-line summary of every class and which FC edition(s) it's
                                   available under
```

## The two feature catalogues

- **FC 2.1.0** is the real, published IHO S-101 feature catalogue.
- **FC 2.10.0** is a test catalogue, identical to the real baseline except for one addition: it
  permits a `NauticalInformation` information type to associate with another
  `NauticalInformation` information type (via `AdditionalInformation`/`theInformation`) — an
  association the real IHO catalogue doesn't define between two information types. Six test
  classes (`IT-MOD-INAS-*`, `IT-MOD-INASATTR-*`) exercise this and only exist under `2.10.0`.

Each dataset's own header (`PRSP`/`PRED` in its `DSID` field) records which catalogue it was
compiled against; use the matching file in `feature-catalogues/` to load or validate it.

## Naming

- A base cell and its update share the identical dataset name — only the extension differs
  (`.000` for the base, `.001` for the update). This is how an update is tied to its base in a
  real ENC exchange.
- Every case's base cell carries the same content as `upd/<version>/base/UPDBASE.000`, shifted to
  its own location on the grid below; it's duplicated per case (under its own dataset name) so
  each case's update can be applied to a base cell on its own, independent of every other case.
- `DS-CANCEL`, `DS-NOOP` and `DS-REISSUE` are classes with no file content to carry (a dataset
  cancellation, a no-op update, and a re-issue respectively) — see
  `docs/update-permutation-catalogue.md` for what they represent.

## Geographic layout

Each case's base+update pair sits on its own 0.22°×0.22° tile on a 10×10 geographic grid, rather
than all stacked at the same coordinates. A given case ID sits on the same tile under both FC
editions. The shared `base/UPDBASE.000` is not part of the grid and stays at its own location
(-30°, 170°). Loading the whole set into a viewer should show one populated tile per case —
a missing or empty tile marks a case that failed to load.

## Loading

Load `upd/<version>/base/UPDBASE.000` (or any case's own base `.000`) against the matching file
in `feature-catalogues/`, then apply the case's `.001` on top of it.
