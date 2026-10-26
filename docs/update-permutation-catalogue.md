# S-101 Update Permutation Catalogue

## Purpose

An exhaustive enumeration of every distinct *class* of S-101 dataset update, derived directly
from primary sources:

- **S-101 ENC Product Specification, Ed. 2.0.0, Annex B-6 "Update Dataset Structure"** (pages
  65–73) — the S-101-specific profile of Part 10a: the actual tree of records/fields legal in an
  update file, and the exact Insert/Delete/Modify support of every control subfield.
- **S-100 Ed. 5.2.1, Part 10a §10a-4.7 "Order of records"** (page 452) — the global record
  ordering and RVER-sequencing rules that apply across everything below.

Each class listed here has **at least one worked example** in this dataset.
Class IDs tag individual features/records in that dataset so coverage can be checked mechanically.

## Methodology

Every one of the seven updatable record types in the B-6 tree splits the same way:

**Add** the whole record — **Delete** the whole record — **Modify** the record.

`Add`/`Delete` are always atomic (RUIN `{1}`/`{2}`, B-6.1.9/.12/.15/.19/.23/.26/.28) — nothing else
needs to be said about them beyond "this record now exists" / "this record no longer exists".

`Modify` (RUIN `{3}`) is never atomic — it is a carrier for one or more changes to the record's
*own* substructure, and each substructure has its **own**, separate Insert/Delete/Modify control
field (this is the key structural fact in Annex B-6: the record's RUIN and a list-item's own `xUI`
are independent). So the catalogue below treats `Modify` as a **recursion point**: for each record
type, "what can change under Modify" is itself an Add/Delete/[Modify] enumeration one level down —
e.g. for a Curve, Modify decomposes into segment Add/Delete/Modify, and segment-Modify further
decomposes into vertex Add/Delete/Modify. This mirrors exactly how the trees in Annex B are
themselves nested.

The two-part split the record types fall into:

- **Part A — Geometry records** (Point, Multi Point, Curve, Composite Curve, Surface): these carry
  coordinates/component lists but no attributes of their own. `Modify` always means a change to
  that coordinate/component list (i.e. vertices, segments, rings, components) — this is where the
  Add/Delete/Modify-vertex pattern lives.
- **Part B — Object records** (Feature Type, Information Type): these carry no coordinates
  directly (they *reference* geometry) but do carry attribute trees and associations. `Modify`
  always means a change to one of the association/attribute lists.

Both parts share one control field — `INAS` (Information Association) — every record type, geometry
or object, can carry associations to Information Type records; it is listed once per record type it
appears on, not treated as a third category.

## Part A — Geometry records

### A.1 Point (host: PRID, B-6.1.12)

| Op | Class | What changes | Fields/subfields | Clause |
|---|---|---|---|---|
| **Add** | `PT-ADD` | New point exists | `PRID{RUIN=1}`, `C2IT`/`C3IT` | B-6.1.12/.13/.14 |
| **Delete** | `PT-DEL` | Point no longer exists | `PRID{RUIN=2}` | B-6.1.12 |
| **Modify** | `PT-MOD-POS` | Coordinate replaced — a Point has no coordinate *list*, so this is the one geometry type where Modify touches the coordinate directly, not via a list-edit control field | `PRID{RUIN=3}`, `C2IT`/`C3IT` (whole tuple re-supplied) | B-6.1.12/.13/.14 |
| **Modify** | `PT-MOD-INAS-ADD` / `-DEL` / `-MOD` | Point's Information Association list changes | `INAS{IUIN=1,2,3}` | B-6.1.11 |

### A.2 Multi Point (host: MRID, B-6.1.15)

Coordinate list is edited via `COCC`/`COUI` — this is the vertex-level Add/Delete/Modify the user
described, applied directly (no intermediate "segment" concept for Multi Point).

| Op | Class | What changes | Fields/subfields | Clause |
|---|---|---|---|---|
| **Add** | `MP-ADD` | New multipoint exists | `MRID{RUIN=1}`, full `C2IL`/`C3IL` | B-6.1.15/.16/.17 |
| **Delete** | `MP-DEL` | Multipoint no longer exists | `MRID{RUIN=2}` | B-6.1.15 |
| **Modify → vertex Add** | `MP-MOD-VTX-ADD` | New coordinate tuple inserted into the list | `MRID{RUIN=3}`, `COCC{COUI=1}`, `COIX`+`NCOR`, new `C2IL`/`C3IL` entries | B-6.1.18 |
| **Modify → vertex Delete** | `MP-MOD-VTX-DEL` | Coordinate tuple removed from the list | `COCC{COUI=2}`, `COIX`+`NCOR` | B-6.1.18 |
| **Modify → vertex Modify** | `MP-MOD-VTX-MOD` | Coordinate tuple's value replaced in place | `COCC{COUI=3}`, `COIX`+`NCOR`, replacement `C2IL`/`C3IL` | B-6.1.18 |
| **Modify** | `MP-MOD-INAS-ADD` / `-DEL` / `-MOD` | Information Association list changes | `INAS{IUIN=1,2,3}` | B-6.1.11 |

### A.3 Curve (host: CRID, B-6.1.19)

**Corrected 2026-08-27** (was wrong in an earlier draft of this doc): a Curve record has **exactly
one segment**, always — confirmed directly against the S-101 PS Annex B trees, not inferred. Base
tree (B-5, p.65): `SEGH` cardinality `<1>`, mandatory exactly-one. Update tree (B-6, p.74): `SEGH`
cardinality `<0..1>`, at most one (absent = curve modified without touching its geometry). There is
**no indexed list of multiple segments per curve** under the S-101 profile — `SECC`/`SEUI` in an
update record addresses *the* single segment as a unit (Insert/Delete/Modify it wholesale), never
one-of-many.

This is an S-101-specific restriction, not a universal ISO 8211 one: **S-100 Part 10a's own generic
Curve record structure (§10a-7.2.4.2, p.481) allows `<0..*>` `SEGH`** — genuinely multiple segments,
to support mixed-interpolation curves chained from line/arc (`CIPM`/`ARPM`)/spline (`SPLI`/`PSPL`)
pieces. That generic richness is exactly why `SECC`/`SEUI`/`SEIX`/`NSEG` exist as a full contiguous-
range Insert/Delete/Modify mechanism (§10a-7.2.4.2.3, p.483: "All segments to be modified with one
update record must be contiguous in the target record"). S-101 simply never exercises it.

**Never used in S-101** (see the tracking table at the bottom of this document): `SECC{SEUI=1}`
(Insert a segment) and `SECC{SEUI=2}` (Delete a segment) — there is never a second segment to insert
alongside, or one-of-several to delete. Only `SECC{SEUI=3}` (Modify — replace the sole segment
wholesale) is ever reachable for an S-101 curve. Under a different S-100 product specification that
uses the full `<0..*>` `SEGH` capability, `SEUI=1`/`{2}` would apply.

Within the one segment, its coordinate list still supports genuine vertex-level Insert/Delete/Modify
via nested `COCC`/`COUI` — this is where the "modifications can be changing vertices, deleting them
or inserting new ones" pattern actually lives for a Curve. Endpoint identity (`PTAS`, which Point
starts/ends the curve) has no `xUI` of its own — changing it requires re-supplying the whole `PTAS`
under a record-level `RUIN=Modify`.

| Op | Class | What changes | Fields/subfields | Clause |
|---|---|---|---|---|
| **Add** | `CV-ADD` | New curve exists | `CRID{RUIN=1}`, `PTAS`, `SEGH`+coords | B-6.1.19/.20/.22 |
| **Delete** | `CV-DEL` | Curve no longer exists | `CRID{RUIN=2}` | B-6.1.19 |
| **Modify → segment Modify** | `CV-MOD-SEG-MOD` | The curve's one segment replaced wholesale | `CRID{RUIN=3}`, `SECC{SEUI=3,SEIX=1,NSEG=1}`, new `SEGH`+coords | B-6.1.21/.22 |
| **Modify → vertex Add** | `CV-MOD-SEGCOORD-ADD` | New vertex inserted into the segment's coordinate list | `SECC{SEUI=3,SEIX=1}` (targets the sole segment), nested `COCC{COUI=1}`, `COIX`+`NCOR` | B-6.1.18/.21 |
| **Modify → vertex Delete** | `CV-MOD-SEGCOORD-DEL` | Vertex removed from the segment's coordinate list | nested `COCC{COUI=2}` | B-6.1.18/.21 |
| **Modify → vertex Modify** | `CV-MOD-SEGCOORD-MOD` | Vertex coordinate replaced in place | nested `COCC{COUI=3}` | B-6.1.18/.21 |
| **Modify → endpoint** | `CV-MOD-ENDPT` | Begin/end point reference changed | `CRID{RUIN=3}`, whole `PTAS` re-supplied (no own `xUI`) | B-6.1.20 |
| **Modify** | `CV-MOD-INAS-ADD` / `-DEL` / `-MOD` | Information Association list changes | `INAS{IUIN=1,2,3}` | B-6.1.11 |

### A.4 Composite Curve (host: CCID, B-6.1.23)

Component list (pointers to member Curve/CompositeCurve records + orientation) edited via
`CCOC`/`CCUI` — the same Add/Delete/Modify-of-list-item pattern, one level up from vertices (the
"vertices" here are whole curves).

| Op | Class | What changes | Fields/subfields | Clause |
|---|---|---|---|---|
| **Add** | `CC-ADD` | New composite curve exists | `CCID{RUIN=1}`, full `CUCO` list | B-6.1.23/.25 |
| **Delete** | `CC-DEL` | Composite curve no longer exists | `CCID{RUIN=2}` | B-6.1.23 |
| **Modify → component Add** | `CC-MOD-COMP-ADD` | New curve-component pointer inserted | `CCID{RUIN=3}`, `CCOC{CCUI=1}`, `CCIX`+`NCCO`, new `CUCO` | B-6.1.24/.25 |
| **Modify → component Delete** | `CC-MOD-COMP-DEL` | Curve-component pointer removed | `CCOC{CCUI=2}`, `CCIX`+`NCCO` | B-6.1.24 |
| **Modify → component Modify** | `CC-MOD-COMP-MOD` | Curve-component pointer's target or orientation (`ORNT`) changed in place | `CCOC{CCUI=3}`, replacement `CUCO` | B-6.1.24/.25 |
| **Modify** | `CC-MOD-INAS-ADD` / `-DEL` / `-MOD` | Information Association list changes | `INAS{IUIN=1,2,3}` | B-6.1.11 |

### A.5 Surface (host: SRID, B-6.1.26)

Ring list edited via `RIAS`/`RAUI` — the **one geometry list with no Modify at all**: rings are an
unordered set with no index, so a changed ring (e.g. a hole whose boundary is now different) is
always Delete-old-ring-association + Add-new-ring-association; the curve/composite-curve the ring
references is separately, independently `Modify`-able (§A.3/A.4 above).

| Op | Class | What changes | Fields/subfields | Clause |
|---|---|---|---|---|
| **Add** | `SF-ADD` | New surface exists | `SRID{RUIN=1}`, full `RIAS` set | B-6.1.26/.27 |
| **Delete** | `SF-DEL` | Surface no longer exists | `SRID{RUIN=2}` | B-6.1.26 |
| **Modify → ring Add** | `SF-MOD-RING-ADD` | New ring association added (exterior or interior/hole) | `SRID{RUIN=3}`, `RIAS{RAUI=1}` | B-6.1.27 |
| **Modify → ring Delete** | `SF-MOD-RING-DEL` | Ring association removed | `RIAS{RAUI=2}` | B-6.1.27 |
| — | *(no `SF-MOD-RING-MOD`)* | `RAUI` is `{1,2}` only — no Modify value exists | — | B-6.1.27 |
| **Modify** | `SF-MOD-INAS-ADD` / `-DEL` / `-MOD` | Information Association list changes | `INAS{IUIN=1,2,3}` | B-6.1.11 |

**Part A total: 6 (Point) + 8 (MultiPoint) + 10 (Curve) + 8 (CompositeCurve) + 7 (Surface) = 39
classes.** (Corrected 2026-08-27 — the original arithmetic here didn't match this section's own
rows; also reflects the Curve fix above, which dropped Curve from 12 to 10 by removing the invalid
`CV-MOD-SEG-ADD`/`CV-MOD-SEG-DEL` cases.)

## Part B — Object records

Object records reference geometry (via `SPAS`, Feature only) rather than containing it, and carry
attribute trees (`ATTR`/`ATIN`) and two kinds of association list — Information (`INAS`, both
record types) and, for Feature only, Feature-to-Feature (`FASC`) and masking (`MASK`).

### B.1 Feature Type (host: FRID, B-6.1.28) — five independent Modify-able lists

| Op | Class | What changes | Fields/subfields | Clause |
|---|---|---|---|---|
| **Add** | `FT-ADD` | New feature exists | `FRID{RUIN=1}`, `FOID`, initial `ATTR`/`SPAS`/etc. | B-6.1.28/.29 |
| **Delete** | `FT-DEL` | Feature no longer exists | `FRID{RUIN=2}` | B-6.1.28 |
| **Modify → attribute Add (top-level)** | `FT-MOD-ATTR-ADD` | New top-level (`PAIX`=0) attribute | `FRID{RUIN=3}`, `ATTR{ATIN=1}` | B-6.1.10 |
| **Modify → attribute Delete (top-level)** | `FT-MOD-ATTR-DEL` | Top-level attribute removed | `ATTR{ATIN=2}` | B-6.1.10 |
| **Modify → attribute Modify (top-level)** | `FT-MOD-ATTR-MOD` | Top-level attribute's `ATVL` replaced | `ATTR{ATIN=3}` | B-6.1.10 |
| **Modify → attribute Add (child)** | `FT-MOD-ATTRCHILD-ADD` | New child of an existing complex attribute — forces the parent (and its ancestors) to also be re-encoded pre-order so `PAIX` resolves | `ATTR{ATIN=1}`, `PAIX`>0 | B-6.1.10 |
| **Modify → attribute Delete (child)** | `FT-MOD-ATTRCHILD-DEL` | Child attribute removed | `ATTR{ATIN=2}`, `PAIX`>0 | B-6.1.10 |
| **Modify → attribute Modify (child)** | `FT-MOD-ATTRCHILD-MOD` | Child attribute's `ATVL` replaced | `ATTR{ATIN=3}`, `PAIX`>0 | B-6.1.10 |
| **Modify → spatial assoc. Add** | `FT-MOD-SPAS-ADD` | Feature attached to a new geometry primitive | `SPAS{SAUI=1}` | B-6.1.30 |
| **Modify → spatial assoc. Delete** | `FT-MOD-SPAS-DEL` | Feature detached from a geometry primitive | `SPAS{SAUI=2}` | B-6.1.30 |
| — | *(no `FT-MOD-SPAS-MOD`)* | `SAUI` is `{1,2}` only — changing which geometry a feature uses is Delete-old + Add-new association | — | B-6.1.30 |
| **Modify → feature assoc. Add** | `FT-MOD-FASC-ADD` | New Feature→Feature association (e.g. Aggregation/Association) | `FASC{FAUI=1}` | B-6.1.31 |
| **Modify → feature assoc. Delete** | `FT-MOD-FASC-DEL` | Association removed | `FASC{FAUI=2}` | B-6.1.31 |
| **Modify → feature assoc. Modify** | `FT-MOD-FASC-MOD` | Association's role/target changed in place | `FASC{FAUI=3}` | B-6.1.31 |
| **Modify → feature assoc. attribute Add/Delete/Modify** | `FT-MOD-FASCATTR-ADD` / `-DEL` / `-MOD` | Attribute carried *on* the association itself (nested `ATTR`/`ATIN` inside `FASC`) | `FASC` nested `ATTR{ATIN=1,2,3}` | B-6.1.31 |
| **Modify → mask Add** | `FT-MOD-MASK-ADD` | New mask on a curve/composite-curve (data-coverage-truncation or suppress-portrayal) | `MASK{MUIN=1}` | B-6.1.32 |
| **Modify → mask Delete** | `FT-MOD-MASK-DEL` | Mask removed | `MASK{MUIN=2}` | B-6.1.32 |
| — | *(no `FT-MOD-MASK-MOD`)* | `MUIN` is `{1,2}` only | — | B-6.1.32 |
| **Modify → info assoc. Add/Delete/Modify** | `FT-MOD-INAS-ADD` / `-DEL` / `-MOD` | Information Association list changes | `INAS{IUIN=1,2,3}` | B-6.1.11 |
| **Modify → info assoc. attribute Add/Delete/Modify** | `FT-MOD-INASATTR-ADD` / `-DEL` / `-MOD` | Attribute carried on an information association | `INAS` nested `ATTR{ATIN=1,2,3}` | B-6.1.11 |

**B.1 total: 2 (Add/Delete) + 6 (attr top) + 6 (attr child) + 2 (SPAS) + 3 (FASC) + 3 (FASC-attr) + 2 (MASK) + 3 (INAS) + 3 (INAS-attr) = 30 classes.**

### B.2 Information Type (host: IRID, B-6.1.9) — same attribute/association shape as Feature, minus SPAS/FASC/MASK (feature-only)

| Op | Class | What changes | Fields/subfields | Clause |
|---|---|---|---|---|
| **Add** | `IT-ADD` | New information record exists | `IRID{RUIN=1}` | B-6.1.9 |
| **Delete** | `IT-DEL` | Information record no longer exists | `IRID{RUIN=2}` | B-6.1.9 |
| **Modify → attribute Add/Delete/Modify (top-level)** | `IT-MOD-ATTR-ADD` / `-DEL` / `-MOD` | Top-level attribute changes | `ATTR{ATIN=1,2,3}` | B-6.1.10 |
| **Modify → attribute Add/Delete/Modify (child)** | `IT-MOD-ATTRCHILD-ADD` / `-DEL` / `-MOD` | Child attribute changes | `ATTR{ATIN=1,2,3}`, `PAIX`>0 | B-6.1.10 |
| **Modify → info assoc. Add/Delete/Modify** | `IT-MOD-INAS-ADD` / `-DEL` / `-MOD` | Information Association list changes | `INAS{IUIN=1,2,3}` | B-6.1.11 |
| **Modify → info assoc. attribute Add/Delete/Modify** | `IT-MOD-INASATTR-ADD` / `-DEL` / `-MOD` | Attribute on an information association | `INAS` nested `ATTR{ATIN=1,2,3}` | B-6.1.11 |

**B.2 total: 2 + 3 + 3 + 3 + 3 = 14 classes.**

**Part B total: 38 classes.** (Corrected 2026-08-27 — original arithmetic didn't match this
section's own rows.)

## Part C — Dataset-file-level classes

Structural rules spanning a whole update file (S-100 Part 10a §10a-4.7, p.452; S-101 PS B-6/B-7).
Not a third geometry/feature category — these constrain how the Part A/B records above are packaged.

| Class | Operation | Clause |
|---|---|---|
| `DS-UPDATE` | Normal update dataset: `PROF="2"`, `DSED`=`[edition].[update+1]` | B-6.1.1 |
| `DS-NEWEDITION` | New Edition: full replay as fresh base dataset (all `RUIN=Insert`), edition incremented | §11.3.3 (p.41) |
| `DS-REISSUE` | Re-issue: same edition/update numbers, no content change — differencer must emit zero records | §11.3.3 (p.41) |
| `DS-CANCEL` | Dataset Cancellation: B-7 structure, `DSID` only, `DSED="0"` | B-7 |
| `DS-MULTIVER` | One record modified more than once in one file — strictly increasing `RVER`, in `RVER` order | §10a-4.7 |
| `DS-ORDER` | Mixed Insert/Modify/Delete across record types in one file — must follow the fixed group order (reverse for Delete) | §10a-4.7 |
| `DS-DEPINSERT` | New Feature + new geometry it references, inserted together — geometry-before-feature ordering | §10a-4.7 |
| `DS-DEPDELETE` | Feature + geometry it exclusively owned, deleted together — feature-before-geometry (reverse) ordering | §10a-4.7 |
| `DS-NOOP` | Two objects that differ in identity but not in content — differencer must emit nothing | (implied by `docs/upd.md`) |

**Part C total: 9 classes.**

## Summary

| Part | Count |
|---|---|
| A — Geometry records (Point/MultiPoint/Curve/CompositeCurve/Surface) | 39 |
| B — Object records (Feature/Information Type) | 38 |
| C — Dataset-file-level | 9 |
| **Total distinct update classes** | **86** |

## Field/subfield values never used in an S-101 update

Part 10a defines these fields/values generically for any S-100 product specification; some are
never actually reachable under the S-101 profile because S-101's own Annex B trees restrict the
structure they'd apply to. Tracked here as found — not an exhaustive audit yet.

| Field.Subfield | Value(s) never used in S-101 | Why | Would apply under |
|---|---|---|---|
| `SECC.SEUI` | `{1}` Insert, `{2}` Delete | A Curve record has exactly one segment always (S-101 PS B-5 p.65: `SEGH` cardinality `<1>`; B-6 p.74: `<0..1>`) — there is never a second segment to insert alongside, or one-of-several to delete. Only `{3}` Modify (replace the sole segment wholesale) is reachable. | A product specification that uses S-100 Part 10a's full generic Curve capability (§10a-7.2.4.2, p.481: `<0..*>` `SEGH`) — e.g. mixed-interpolation curves built from chained line/arc/spline segments. |

## Notes on building the normative test dataset from this catalogue

- One class does not necessarily need one dedicated `.000` file — `DS-ORDER`/`DS-MULTIVER` in
  particular are file-level properties best demonstrated by a single update file that *also*
  exercises several Part A/B classes at once, as long as each individual class still gets its own
  clearly FOID/comment-tagged feature so it's independently checkable.
- Every class with a `Modify` variant needs its base object to exist in the "before" dataset first
  — build the base cell (all `RUIN=Insert`, per B-5) before the update file, same as a real ENC
  edition/update pair.
- The "no Modify" fields (`RAUI`, `SAUI`, `MUIN`) are the ones most worth a negative test — confirm
  the differencer never *tries* to emit a Modify for these, only Delete+Add pairs.
- `FT-MOD-ATTRCHILD-*`/`IT-MOD-ATTRCHILD-*` exercise a genuinely complex (parent+child) attribute
  (see the feature catalogue for the attribute's own structure).

## Open issue: encoding-level permutations (multiplicity ambiguity)

**Status: open research item (2026-08-27), raised during review — not yet reflected in the test
case set.**

Every field in the B-5/B-6 trees whose cardinality is `<0..*>` or `<1..*>`, and whose subfield
sequence is marked repeatable (`*`), can legally be encoded in more than one way. S-101 PS **B-3
"Fields"** (p.63) states:

> For base dataset files, some fields may be repeated (indicated by `<0..*>` or `<1..*>`) and all
> of their content may be repeated (indicated by `*`). In order to reduce the volume of data, the
> encoder should repeat the sequence of subfields, in preference to creating several fields.

This is a **recommendation** ("should"), not a requirement — both of the following are legal ISO
8211 encodings of the same logical content within one record:
1. **One field occurrence, subfield sequence repeated inside it** — the compact, recommended form.
   Every test case in this catalogue currently uses this form exclusively (e.g. one `ATTR` field
   with `NATC`/`ATIX`/`PAIX`/`ATIN`/`ATVL` repeated per attribute, one `RIAS` field with its
   subfields repeated per ring, etc. — matches the confirmed real example `dat/10100AA158002_F.yaml`).
2. **Multiple separate field occurrences**, each carrying one instance of the repeatable content —
   B-3's "several fields" alternative.

There is a further, higher-level variant beyond B-3's scope: content that repeats can also be split
across **multiple separate ISO 8211 records** entirely — e.g. several `RUIN=Modify` records against
the same target, each with its own strictly-increasing `RVER` (per §10a-4.7's rule for repeated
modifications to one record, already demonstrated narrowly by the `DS-MULTIVER` dataset-file-level
case), each record carrying only part of the repeatable content. This generalizes to any repeatable
field — `ATTR`, `INAS`, `FASC`, `SPAS`, `RIAS`, `CUCO`, coordinate lists — not just the one field
`DS-MULTIVER` happens to exercise.

**Two open questions, unresolved:**
- B-3's text is scoped literally to "base dataset files." The identical `<0..*>`/`*` notation
  appears throughout the B-6 *update* tree too (`ATTR (*5)`, `INAS (5\*5)`, `SPAS (*6)`, `FASC
  (*5)`, `RIAS (*5)`, etc.) — it is not confirmed whether B-3's stated preference is intended to
  apply to update datasets as well, or whether it's silent on that case by omission.
- Whether index/count-based update subfields (`COIX`/`NCOR`, `SEIX`/`NSEG`, `CCIX`/`NCCO`) behave
  identically regardless of which encoding style produced the *target* record's original content —
  not yet checked.

**Practical implication:** every one of this catalogue's 86 test cases currently exercises only
encoding style 1 (single record, single field occurrence, compact). None exercise style 2 (multiple
field occurrences within one record) or the record-splitting variant above. This is a real coverage
gap for any tool that must robustly read/write updates, not just a documentation nicety — a decoder
or differencer that only handles the compact form would fail against equally-valid alternative
encodings. **Do not add permutation cases for this yet** — pending further research into the two
open questions above before deciding which combinations warrant normative coverage.

## Open issue: can FOID be changed by a Modify?

**Status: spec-silent, resolved by project convention (2026-10-08) — treat as not intended, no
test case added.**

`FOID` under `FRID` is `<1>` (mandatory) in the B-5 base tree but `<0..1>` (optional) in the B-6
update tree — the same drop every other Modify-omittable field shows (base always restates
everything; update restates only what's present). Unlike `ATTR`/`SPAS`/`FASC`/`MASK`/`INAS`,
`FOID` carries no update-instruction subfield of its own (no `xUI`), so nothing in the encoding
*structurally* distinguishes it from any other plain field a Modify could legally re-supply —
and this project's own confirmed Modify semantics (whole-field replace for any field present,
leave-untouched for any field absent) would apply to it the same as anything else. Checked both
Part 10a (full text, incl. §10a-5.1 "Attribute field" family) and S-101 PS Ed. 2.0.0 Annex B for
"unchanged"/"retain"/"must not be changed"/"remains the same" near `FOID` or Modify in general —
no hits. There is no "Updating of FOID" clause anywhere, unlike `ATTR`'s dedicated §10a-5.1.2.

Arguing against ever doing it, even though nothing polices it: S-101 PS states FOID's entire
purpose is to be the *stable* identifier of "the same real-world feature" across datasets, scale
bands, and time ("FOIDs must not be repeated in a dataset... all instances [across datasets] must
be identical"), and Part 10a confirms FOID is never used to reference records *within* a dataset
(that's RRNM/RRID) — so a changed FOID wouldn't break in-file integrity, but there is also no
mechanism anywhere in Part 10a to propagate that change to the other datasets/consumers relying on
it staying put. **This catalogue already assumed immutability without saying so** — §B.1's five
independently Modify-able Feature Type lists (`ATTR`, `SPAS`, `FASC`, `MASK`, `INAS`) don't include
`FOID`, and there is no `FT-MOD-FOID` class.

Decision: don't add a test case for this. It's unlikely to be intended, and a future IHO
clarification will probably say so explicitly — revisit if/when one does.

