# Test case index

Every class in the permutation catalogue, what it demonstrates, and where its files
live under `upd/<FC-version>/cases/<ID>/`. `DS-CANCEL`, `DS-NOOP` and `DS-REISSUE` are
illustrative classes with no compiled files (see the catalogue doc for why).

| ID | What it demonstrates | 2.1.0 | 2.10.0 |
|---|---|---|---|
| `CC-ADD` | Inserts two new Points and two new component Curves joined into a new Composite Curve | ✓ | ✓ |
| `CC-DEL` | Deletes the unreferenced spare Composite Curve CC2_SPARE | ✓ | ✓ |
| `CC-MOD-COMP-ADD` | Modifies the spare Composite Curve CC2_SPARE by inserting a new closed component curve via CCOC positional control kept contiguous with the ring | ✓ | ✓ |
| `CC-MOD-COMP-DEL` | Modifies the spare Composite Curve CC2_SPARE by deleting its 2nd component curve via CCOC positional control leaving a single valid component | ✓ | ✓ |
| `CC-MOD-COMP-MOD` | Modifies the spare Composite Curve CC2_SPARE by flipping both component curves' orientation in place via CCOC positional control to reverse the ring's winding direction | ✓ | ✓ |
| `CV-ADD` | Inserts two new Points and a new open Curve connecting them | ✓ | ✓ |
| `CV-DEL` | Deletes the unreferenced spare closed Curve C6_SPARE | ✓ | ✓ |
| `CV-MOD-ENDPT` | Modifies the spare open Curve C7_SPARE by re-pointing one PTAS endpoint from P_C7B to P_SPARE | ✓ | ✓ |
| `CV-MOD-INAS-ADD` | Modifies C1 by adding a new INAS association to IT2 | ✓ | ✓ |
| `CV-MOD-INAS-DEL` | Modifies C1 by deleting its existing INAS association to IT1 | ✓ | ✓ |
| `CV-MOD-INAS-MOD` | Modifies C1 by re-pointing its existing INAS association from IT1 to IT2 | ✓ | ✓ |
| `CV-MOD-SEG-MOD` | Modifies C2's sole segment via SECC replacing its coordinate list with a reshaped ring | ✓ | ✓ |
| `CV-MOD-SEGCOORD-ADD` | Modifies C2's segment by inserting a new coordinate via COCC positional control | ✓ | ✓ |
| `CV-MOD-SEGCOORD-DEL` | Modifies C2's segment by deleting an existing coordinate via COCC positional control | ✓ | ✓ |
| `CV-MOD-SEGCOORD-MOD` | Modifies C2's segment by replacing an existing coordinate via COCC positional control | ✓ | ✓ |
| `DS-CANCEL` | Illustrates the B-7 Dataset Cancellation structure DSID only with DSED zero -- YAML only not compiled | (no files) | (no files) |
| `DS-DEPDELETE` | Deletes a Feature before the Surface it exclusively used in the same update file reverse dependency order | ✓ | ✓ |
| `DS-DEPINSERT` | Inserts a new Point together with a new Feature that depends on it in the same update file | ✓ | ✓ |
| `DS-MULTIVER` | Carries two successive Modify records for Feature F3 at RVER 2 then RVER 3 in one update file | ✓ | ✓ |
| `DS-NEWEDITION` | Replays the entire base cell as a fresh Base dataset profile with Edition bumped from 1 to 2 | ✓ | ✓ |
| `DS-NOOP` | Deliberately empty -- illustrates the no-op case where a differencer must emit nothing when A equals B | (no files) | (no files) |
| `DS-ORDER` | Exercises the mandated Insert then Modify then Delete record ordering within one update file | ✓ | ✓ |
| `DS-REISSUE` | Deliberately empty -- illustrates a Re-issue where content is unchanged and only administrative metadata differs | (no files) | (no files) |
| `DS-UPDATE` | Illustrates an ordinary single-change Update dataset reusing FT-MOD-ATTR-MOD's content | ✓ | ✓ |
| `FT-ADD` | Inserts a new BuoyLateral Feature pointing at the spare Point P_SPARE | ✓ | ✓ |
| `FT-DEL` | Deletes the UnsurveyedArea Feature F5 | ✓ | ✓ |
| `FT-MOD-ATTR-ADD` | Modifies F5 by adding a new top-level attribute interoperabilityIdentifier via ATTR | ✓ | ✓ |
| `FT-MOD-ATTR-DEL` | Modifies F3 (DepthArea) by deleting its depthRangeMinimumValue attribute via ATTR | ✓ | ✓ |
| `FT-MOD-ATTR-MOD` | Modifies F3 by changing the value of its depthRangeMaximumValue attribute via ATTR | ✓ | ✓ |
| `FT-MOD-ATTRCHILD-ADD` | Modifies F4 by adding a new child attribute nameUsage under its complex featureName attribute via ATTR PAIX indexing | ✓ | ✓ |
| `FT-MOD-ATTRCHILD-DEL` | Modifies F4 (LandArea) by deleting a child attribute under its complex name attribute | ✓ | ✓ |
| `FT-MOD-ATTRCHILD-MOD` | Modifies F4 by changing the value of a child attribute under its complex name attribute | ✓ | ✓ |
| `FT-MOD-FASC-ADD` | Modifies F1 by adding a new UpdatedInformation feature association to the UpdateInformation feature F_UPD1 | ✓ | ✓ |
| `FT-MOD-FASC-DEL` | Modifies F3 by deleting its existing UpdatedInformation feature association to F_UPD1 | ✓ | ✓ |
| `FT-MOD-FASC-MOD` | Modifies F3 by re-pointing its existing UpdatedInformation feature association from F_UPD1 to F_UPD2 | ✓ | ✓ |
| `FT-MOD-FASCATTR-ADD` | Modifies F3 by adding a new attribute categoryOfTemporalVariation onto its FASC association to F_UPD1 | ✓ | ✓ |
| `FT-MOD-FASCATTR-DEL` | Modifies F3 by deleting the categoryOfTemporalVariation attribute on its FASC association to F_UPD1 | ✓ | ✓ |
| `FT-MOD-FASCATTR-MOD` | Modifies F3 by changing the categoryOfTemporalVariation attribute value on its FASC association to F_UPD1 | ✓ | ✓ |
| `FT-MOD-INAS-ADD` | Modifies F3 by adding a new INAS association to IT2 | ✓ | ✓ |
| `FT-MOD-INAS-DEL` | Modifies F4 by deleting its existing INAS association to IT2 | ✓ | ✓ |
| `FT-MOD-INAS-MOD` | Modifies F4 by changing the association role of its existing INAS link to IT2 | ✓ | ✓ |
| `FT-MOD-INASATTR-ADD` | Modifies F4 by adding a new attribute onto its existing INAS association to IT2 | ✓ | ✓ |
| `FT-MOD-INASATTR-DEL` | Modifies F4 by deleting an attribute on its existing INAS association to IT2 | ✓ | ✓ |
| `FT-MOD-INASATTR-MOD` | Modifies F4 by changing the value of an attribute on its existing INAS association to IT2 | ✓ | ✓ |
| `FT-MOD-MASK-ADD` | Modifies F4 by adding a new masking reference MASK to Curve C2 | ✓ | ✓ |
| `FT-MOD-MASK-DEL` | Modifies F3 by deleting its existing masking reference MASK to Curve C1 | ✓ | ✓ |
| `FT-MOD-SPAS-ADD` | Modifies F4 by adding a second spatial pointer SPAS to the spare Surface S4_SPARE | ✓ | ✓ |
| `FT-MOD-SPAS-DEL` | Modifies F1 (Light) by removing its spatial pointer to P_LIGHT via SAUI Delete | ✓ | ✓ |
| `IT-ADD` | Inserts a new SpatialQuality Information Type | ✓ | ✓ |
| `IT-DEL` | Deletes the Information Type IT2 | ✓ | ✓ |
| `IT-MOD-ATTR-ADD` | Modifies IT2 by adding a new top-level attribute spatialAccuracy | ✓ | ✓ |
| `IT-MOD-ATTR-DEL` | Modifies IT2 by deleting its qualityOfHorizontalMeasurement attribute | ✓ | ✓ |
| `IT-MOD-ATTR-MOD` | Modifies IT2 by changing the value of its qualityOfHorizontalMeasurement attribute | ✓ | ✓ |
| `IT-MOD-ATTRCHILD-ADD` | Modifies IT1 by adding a new child attribute under its complex attribute chain | ✓ | ✓ |
| `IT-MOD-ATTRCHILD-DEL` | Modifies IT1 by deleting a child attribute under its complex attribute chain | ✓ | ✓ |
| `IT-MOD-ATTRCHILD-MOD` | Modifies IT1 by changing the value of a child attribute under its complex attribute chain | ✓ | ✓ |
| `IT-MOD-INAS-ADD` | Modifies IT1 by adding a new INAS association to IT2 | — | ✓ |
| `IT-MOD-INAS-DEL` | Modifies IT1 by deleting its existing INAS association to IT2 | — | ✓ |
| `IT-MOD-INAS-MOD` | Modifies IT1 by changing the association role of its existing INAS link to IT2 | — | ✓ |
| `IT-MOD-INASATTR-ADD` | Modifies IT1 by adding a new attribute onto its existing INAS association to IT2 | — | ✓ |
| `IT-MOD-INASATTR-DEL` | Modifies IT1 by deleting an attribute on its existing INAS association to IT2 | — | ✓ |
| `IT-MOD-INASATTR-MOD` | Modifies IT1 by changing the value of an attribute on its existing INAS association to IT2 | — | ✓ |
| `MP-ADD` | Inserts a new Multi Point with two vertices | ✓ | ✓ |
| `MP-DEL` | Deletes the unreferenced spare Multi Point MP_SPARE | ✓ | ✓ |
| `MP-MOD-INAS-ADD` | Modifies the sounding array MP_SOUND by adding a new INAS association to IT1 | ✓ | ✓ |
| `MP-MOD-INAS-DEL` | Modifies MP_SOUND by deleting its existing INAS association to IT2 | ✓ | ✓ |
| `MP-MOD-INAS-MOD` | Modifies MP_SOUND by re-pointing its existing INAS association from IT2 to IT1 | ✓ | ✓ |
| `MP-MOD-VTX-ADD` | Modifies MP_SPARE by inserting a new vertex via COCC positional control | ✓ | ✓ |
| `MP-MOD-VTX-DEL` | Modifies MP_SPARE by deleting an existing vertex via COCC positional control | ✓ | ✓ |
| `MP-MOD-VTX-MOD` | Modifies MP_SPARE by replacing an existing vertex's coordinates via COCC positional control | ✓ | ✓ |
| `PT-ADD` | Inserts a new isolated Point with fresh coordinates | ✓ | ✓ |
| `PT-DEL` | Deletes the unreferenced spare Point P_SPARE | ✓ | ✓ |
| `PT-MOD-INAS-ADD` | Modifies P_LIGHT by adding a new INAS association to IT2 | ✓ | ✓ |
| `PT-MOD-INAS-DEL` | Modifies P_LIGHT by deleting its existing INAS association to IT1 | ✓ | ✓ |
| `PT-MOD-INAS-MOD` | Modifies P_LIGHT by re-pointing its existing INAS association from IT1 to IT2 | ✓ | ✓ |
| `PT-MOD-POS` | Modifies P_SPARE by changing its coordinate position | ✓ | ✓ |
| `SF-ADD` | Inserts a new Surface bounded by the spare Curve C7_SPARE | ✓ | ✓ |
| `SF-DEL` | Deletes the unreferenced spare Surface S4_SPARE | ✓ | ✓ |
| `SF-MOD-RING-ADD` | Modifies S1 by adding a new interior ring hole bounded by C6_SPARE via RIAS | ✓ | ✓ |
| `SF-MOD-RING-DEL` | Modifies S1 by deleting its existing interior ring C5_HOLE via RIAS | ✓ | ✓ |
