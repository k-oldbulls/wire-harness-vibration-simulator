# Wire Harness Vibration Simulator: Build Plan

Oct 28, 2026 · @Keith Willett

## 1. Purpose

This build plan turns [Design Specification 0.3](design-specification-0.3.md) into work an engineer or a coding agent can pick up without asking anyone. The design specification says what the system must do and why; this plan says in what order to build it, what each piece reads and writes, and how to prove each piece works. Where the two disagree, the design specification wins and this plan is corrected.

The plan holds three things the design specification only references:

- **State descriptors** (section 4): the full contract for every new shared-state variable.
- **Benchmark and acceptance definitions** (section 5): loads, boundary conditions, reference solutions and error norms for every gate.
- **Work packages** (sections 6 to 9): dependency-ordered units of work, grouped by release.

The plan carries no staffing or duration estimates. Release order and gates set the sequence; how many agents or people run in parallel is a delivery choice outside this document. Every number marked "proposed" is a starting value for TE to confirm before the gate that uses it.

## 2. Conventions

### 2.1 Work package fields

Every work package (WP) uses the same fields, so an agent can start from the package alone.

| Field | Meaning |
| --- | --- |
| ID and title | WP-xx and a short name |
| Release | 1, 2, 3, or "gated after 3" |
| Goal | One or two sentences on what exists when the package is done |
| Depends on | Work packages that must be done first |
| Reads | Shared-state variables, library records and interfaces the package consumes |
| Writes | Shared-state variables, records and interfaces the package produces |
| Requirements | Design Specification 0.3 IDs the package satisfies |
| Acceptance | Tests from section 5 or listed in the package; each must run without a person deciding the outcome |
| Out of scope | What a reader might expect here but belongs elsewhere |

### 2.2 Definition of done

A work package is done when all of the following hold:

1. Every acceptance test passes on the CPU reference path and its output is stored in the evidence store (C-35) with a run manifest (C-33).
2. Every requirement the package lists has its verification method satisfied (Test, Analysis, Inspection, Demonstration, as in Requirements 0.3).
3. Interfaces and state variables match their descriptors in section 4 exactly, including units, frames and producer.
4. No calibration dataset was reused as validation data (VAL-03, MAT-05).
5. A reviewer other than the author, person or agent, has read the diff and the test evidence.

### 2.3 Rules for agents

- **Specs are fixed inputs.** An agent that finds a conflict between this plan and the design specification stops and reports it; it does not pick one.
- **No silent thresholds.** Any threshold not stated here or in the design specification is a profile parameter with a logged default, never a constant in code.
- **Tests first where a reference exists.** Benchmarks in section 5 are written before the code they check.
- **Credibility is never set by a package.** Decision classes come from the assessment gate (C-40) and credibility registry (C-41) only.

## 3. Dependency map and release gates

Releases follow Requirements 0.3: Must lands in release 1, Should in release 2, Could later, and a module tag such as (WR) applies a priority inside that module, which arrives in release 2. Packages inside one release can run in parallel once their dependencies are done.

| WP | Title | Release | Depends on | Feeds gate |
| --- | --- | --- | --- | --- |
| WP-01 | Library schema and records | 1 | None | 1 |
| WP-02 | Property calculator | 1 | WP-01 | 1 |
| WP-03 | Engine benchmark suite | 1 | None | 1 |
| WP-04 | Engine selection (AD-09) | 1 | WP-03 | 1 |
| WP-05 | Rod geometry and contact adapter (IF-21) | 1 | WP-01, WP-02, WP-04 | 1 |
| WP-06 | Mode A installed shape and refinement checks | 1 | WP-02, WP-05 | 1 |
| WP-07 | Mode R real-time | 1 | WP-05, WP-06 | 1 |
| WP-08 | 3D desktop viewer with compute-zone streaming | 1 | WP-06 | 1 |
| WP-09 | Computed property validation and amendment A6 evidence | 1 | WP-02, pilot step 3 data | 1 |
| WP-10 | Contact detection and candidate generation | 2 | WP-06, core snapshots | 2 |
| WP-11 | Mode E envelope kinematics | 2 | WP-10, core snapshots | 2 |
| WP-12 | Slip classification | 2 | WP-11 | 2 |
| WP-13 | Relative wear index and index-only WR manifest | 2 | WP-01, WP-11, WP-12 | 2 |
| WP-14 | Contact rerun trigger | 2 | WP-11 | 2 |
| WP-15 | Tribology data, wear-law fitting and coupon depth predictor | 2 | WP-01 | 2 |
| WP-16 | Large motion and articulation handoff | 2 | WP-06, WP-10 | 2 |
| WP-17 | Immersive review and streaming | 2 | WP-07, WP-08 | 2 |
| WP-18 | Core use of calibrated properties | 2 | WP-09 passed, TE approval of A6 | 2 |
| WP-19 | Wear validation for gate 2 | 2 | WP-13, WP-15 | 2 |
| WP-28 | Core external-contact histories | 3 | WP-14 | 3 |
| WP-20 | Wear depth from contact histories | 3 | WP-12, WP-14, WP-15, WP-28 | 3 |
| WP-21 | Wear state and surface updates | 3 | WP-20 | 3 |
| WP-22 | WR manifest extended to depth, candidate fixes | 3 | WP-13, WP-20 | 3 |
| WP-23 | AR overlay, inspection findings and scan import | 3 | WP-17 | 3 |
| WP-24 | Wear validation for gate 3 | 3 | WP-20, WP-21, WP-22 | 3 |
| WP-25 | D2 engine geometry qualification | Gated after 3 | Gate 3 passed | D2 path |
| WP-26 | Core contact reruns at every flagged site | Gated after 3 | WP-28, gate 3 passed | D2 path |
| WP-27 | D2 wear validation campaign | Gated after 3 | WP-25, WP-26 | D2 path |

WP-28 was added after the first review round and keeps its number so references stay stable; it sits with release 3 because WP-20 needs it.

### 3.1 Gates

| Gate | Closes | Passes when |
| --- | --- | --- |
| Gate 1 | Release 1 | Section 5.1 engine benchmarks pass on the selected engine; section 5.2 refinement checks pass on the pilot harnesses; section 5.4 computed-property checks pass for the pilot construction families |
| Gate 2 | Release 2 | Section 5.3 contact detection set passes; section 5.5 coupon and standard-test ranking gates pass |
| Gate 3 | Release 3 | Section 5.5 shaker-harness and blind-harness gates pass. WR results stay at D1; passing opens the D2 path (section 9) |
| D2 path | After release 3 | Section 5.6 gates pass and the TE owner decides under CRD-07 |

## 4. State descriptors

Every new shared-state variable follows the schema in Architecture 0.2 section 6.1 (name and version, quantity and units, spatial support, temporal form, resolution, producer, uncertainty, classification). This section adds two fields Architecture 0.2 leaves implicit: the coordinate frame and the consumers.

### 4.1 Common rules

- **Contact site ID.** The harness side is (C-21 segment ID, arc length s in meters from the segment's start node). The other body is a CAD feature ID plus a surface point (u, v) on that feature for a counterface, or a second (segment ID, s) for harness to harness. Each body keeps its own surface coordinates, so wear on each can be placed exactly.
- **Frames.** Positions and displacements use the vehicle frame of the harness model (C-21), right-handed, meters. The local contact frame at a site has normal n from the counterface into the harness; t1 is the bundle axis projected onto the tangent plane and normalized, or the projected slip direction when the axis lies within 10 degrees of n, or the vehicle x axis projected onto the tangent plane (the y axis if that also vanishes) when there is no slip direction; t2 = n × t1. The frame is orthonormal by construction.
- **Merging.** Sites merge only when they share contact class and material pair and lie within one bundle diameter along s; distinct pairs or patches stay separate.
- **Mesh correspondence.** The engine and the core discretize the harness differently. Each publishes a map from its own nodes to (segment ID, s), so any quantity can move between them by arc length, never by node number.
- **Classification.** Every variable inherits the strictest tag of its inputs (Architecture 0.2 section 15). No variable here lowers a tag.
- **Versioning and provenance.** A change of meaning, units or enumeration bumps the version; consumers declare the versions they accept. Every read names the snapshot it used (Architecture 0.2 section 6), and derived records such as rerun requests and wear updates carry the hashes of the inputs they depend on.

### 4.2 Descriptors

| Variable | Quantity and units | Spatial support | Temporal form and resolution | Producer | Consumers | Uncertainty |
| --- | --- | --- | --- | --- | --- | --- |
| contact.list v1 | Per site: contact class (harness to environment, harness to harness, harness to itself, harness to clip); material pair; minimum surface clearance, m; slip state (stuck, gross slip, indeterminate); estimated normal force RMS and peak, N | Contact site ID with per-body surface coordinates | Static per installed shape, with slip state per response case; one record per site | C-27: mode A writes geometry and clearance; mode E writes slip state and force estimates | C-26.6 WR, C-07 viewer, C-40 gate | Clearance bounds from the DET-04 variation study, else "not characterized" |
| contact.rerun\_request v1 | Per site: trigger that fired (force, closure fraction) and its value; status (open, satisfied, waived to index only); bound case, contact and input hashes; satisfying core run manifest | Contact site ID | Versioned with the case; reopens when any bound hash changes | C-27 mode E (WP-14); status set by C-12 when a core run satisfies it | C-12 sequence engine, C-26.6 WR (blocks depth while open or waived) | Not applicable |
| contact.history v1 | Per site: normal force, N; tangential force along t1 and t2, N; relative tangential displacement split into elastic and sliding parts, m; contact active flag | Contact site ID with per-body surface coordinates, local contact frame | Synchronized time history at the resolution WR declares (PHY-11), at least 20 samples per period of the highest frequency that carries slip (proposed) | C-25 core nonlinear time-domain stage with the contact modeled (WP-28) | C-26.6 WR (depth), C-07 viewer | Carries the core run's refinement results (section 5.2); flagged when outside the limits |
| motion.envelope v1 | Per site: relative displacement RMS and peak along n, t1, t2, m; relative tangential velocity RMS, m/s; mean crossing rate of tangential slip, Hz; estimated normal force RMS, minimum and peak, N | Contact site ID, local contact frame | Response statistics per response case; one set per site, plus a conservative excursion bound per segment used before pruning | C-27 mode E | C-26.6 WR (relative wear index), C-07 viewer | Carries the core's response-statistic bounds (DET-05a) |
| motion.rigid\_track v1 | Pose of each articulated interface: position, m, and orientation, unit quaternion | Interface ID declared by the profile (APP-04), vehicle frame | Time history; time step at most 1/20 of the period of the first harness mode (proposed) | C-27 mode A dynamic (WP-16) | C-25.1 model assembly, which superimposes vibration (Architecture 0.2 section 7.3) | Not characterized in release 2; flagged |
| wear.index v1 | Relative wear index per site and body, dimensionless, at least 0, with the W\_ref it is divided by; separate ordinal rank for rating-only sites | Contact site ID, per body | Static per study and response case | C-26.6 WR (WP-13) | C-07 viewer, C-04 report service, C-40 gate (D1 only) | Range from coefficient and response-statistic bounds |
| wear.depth v1 | Depth range per site, body and layer, m, with its midpoint | Contact site ID, per body, per layer | Accumulated snapshot per sequence step | C-26.6 WR, depth path only (WP-20) | C-26.6 WR (next step), C-07 viewer, C-04 report service | Range per WR-03 |
| wear.layer\_state v1 | Per site and body: intact layers; breached layers with the cycle range at breach. Harness: covering, insulation, shield, conductor exposed. Counterface: topcoat, primer or e-coat, zinc, substrate exposed | Contact site ID, per body | Accumulated snapshot per sequence step | C-26.6 WR (WP-20) | C-25 core and C-27 at the next step, C-07 viewer, C-04 report service (corrosion risk flag) | Breach cycle ranges per WR-03 |
| geometry.wear\_update v1 | Per site and per body: local surface recession at the site's surface coordinates, m; new outer material ID; new pair record ID; friction coefficient at the case temperature | Contact site ID, per body | Applies at the next sequence step only | C-26.6 WR (WP-21) | C-25.1 model assembly, C-27 | Taken from the central depth estimate; the depth bounds run as DET-04 uncertainty cases, because more wear is not always the conservative case |
| geometry.asbuilt v1 | As-built centerline per segment as a polyline, m, with per-point position uncertainty | C-21 segment ID, vehicle frame | Static snapshot per scan | IF-40 import (WP-23) | C-27, C-26.6 WR, C-07 viewer | Scan and registration uncertainty, required |
| geometry.engine\_map v1 | Map from engine rod nodes to (segment ID, s) | C-21 segment ID | Static per engine model revision | C-27 | C-26.6 WR, C-07 viewer, comparison runs | Not applicable |
| geometry.core\_map v1 | Map from core FE nodes to (segment ID, s) | C-21 segment ID | Static per core model revision | C-25.1 model assembly (small addition; see Design Specification section 10.1) | C-27 mode E, C-26.6 WR | Not applicable |

## 5. Benchmark and acceptance definitions

Every test here is written before the code it checks (section 2.3), stores its reference solution with a citation in the test file, and reports the error norm named in its row. Pass criteria marked "proposed" are for TE to confirm before the gate that uses them.

### 5.1 Engine benchmarks (PE-10)

Common rod unless a row says otherwise: length L = 1.0 m, bending stiffness EI = 0.02 N·m² in both axes, torsional stiffness GJ = 0.015 N·m², axial stiffness EA = 2,000 N, line density 0.1 kg/m, no damping in static cases.

| ID | Case | Loads and boundary conditions | Reference solution | Error norm and pass (proposed) |
| --- | --- | --- | --- | --- |
| B-01 | Small-deflection cantilever | Clamped at s = 0, tip point load P with tip deflection under 1 percent of L, no gravity | δ = PL³ / (3EI) | Tip deflection error under 0.5 percent |
| B-02 | Large-deflection cantilever, tip load | Clamped at s = 0, vertical tip load that keeps its direction, with PL²/EI = 2, no gravity | Independent shooting solution of the planar elastica, θ″ + (P/EI) cos θ = 0, θ(0) = 0, θ′(L) = 0, solved to 10⁻⁹; cross-checked against Bisshopp and Drucker (1945) | Tip position error under 1 percent of L |
| B-03 | Large-deflection cantilever under gravity | Clamped at s = 0, own weight only, weight per unit length q set so that qL³/EI = 6 | Independent shooting solution of the planar elastica with distributed load, solved to 10⁻⁹ | Tip position error under 1 percent of L |
| B-04 | Euler buckling | Clamped at s = 0, free tip, axial compressive tip load on a perfect rod, stepped in increments of 0.5 percent of P\_cr near the expected value | P\_cr = π²EI / (4L²) | The load at which the smallest eigenvalue of the mode A tangent stiffness changes sign, interpolated between steps, within 2 percent |
| B-05 | Pure torsion | Clamped at s = 0, end torque T giving under 0.2 rad of twist | φ = TL / GJ | Twist error under 1 percent; rod stays straight to 10⁻⁶ L |
| B-06 | Rest curvature | Free rod with uniform natural curvature 2π/L, no loads | Closed circle of radius L / 2π | Radius error under 0.5 percent |
| B-07 | Large end-moment bending | Clamped at s = 0, end moment M with ML/EI = 2π, no gravity | Exact: the rod closes into a circle of radius EI/M = L/2π and the tip returns to the clamp | Tip position error under 0.5 percent of L |
| B-08 | Clip breakaway | Rod through a compliant clip, normal preload 10 N, μ = 0.3, slow axial pull ramp | Coulomb breakaway force μN = 3 N | Breakaway force within 5 percent |
| B-09 | Mode A against core FE | USCAR-2 fixture harness (Requirements pilot step 2), gravity and clip snap-in | Core stage 3 implicit FE result on the same harness, with its stated convergence uncertainty | Node positions within 2 mm after mapping by (segment, s) |
| B-10 | Mode A against physical scans | One formboard harness and one vehicle-buck harness, scanned | Scan centerline (geometry.asbuilt) with its stated uncertainty | Mounting datums exact by construction; harness centerline deviation at the 95th percentile under 10 mm |
| B-11 | Shear on short segments | Shearable formulation, cantilevers with L/D = 5 and L/D = 10, tip load in the linear range | Timoshenko tip deflection δ = PL³/(3EI) + PL/(κGA), with κ the shear coefficient of the section | Shearable error under 1 percent; the unshearable error at L/D = 10 is reported and must be under 2 percent for the slenderness rule in Design Specification section 5.2 to stand |
| B-12 | Twisted closed ring | A straight rod closed into a ring with periodic director closure and an imposed total twist of one full turn (2π rad), below the instability threshold that WP-03 confirms from the cited reference before the case gates | Exact: the ring stays circular with radius L/2π and uniform twist density of 2π/L rad per meter | Radius error under 0.5 percent; twist density uniform within 1 percent |

B-10 also sets a usage rule: a clearance decision from mode A is valid only where the clearance margin exceeds 10 mm plus the scan uncertainty at 95 percent, until a tighter B-10 result is shown.

### 5.2 Refinement checks (PE-12)

Every accurate-mode result, and every core contact history used for depth, is refined three ways, one at a time: segment length halved, contact discretization refined, and time step halved for dynamic runs. A combined run with all three then confirms. The result is invalid when any single or combined refinement changes a quantity by more than these limits, which follow the under-5-percent rule for each DCQ in Requirements 14.3 (proposed values):

| Quantity | Allowed change |
| --- | --- |
| Node position | 1 mm. Clearance decisions are separate: a clearance result is valid only where its margin exceeds the position uncertainty, including this refinement change (see B-10), and a violation always stays reportable |
| Clip load | 2 percent |
| Contact normal force | 5 percent |
| Sliding work at a contact | 5 percent |
| Wear depth range bounds | 5 percent |

### 5.3 Contact detection set (PE-07, PE-13)

- **Domain.** Rod radius 1 to 15 mm; panel thickness at least 0.5 mm; edge radius at least 0.2 mm; relative speed up to 10 m/s per engine step in mode R and per envelope sample in mode E. Contacts outside this domain are flagged "outside validated domain" (VAL-05).
- **Cases.** 10,000 randomized trajectories from a fixed seed, covering thin panels, sharp edges, crossing bundles and fast motion.
- **Reference.** An independent exact swept-capsule against triangle and capsule intersection test in double precision.
- **Pass.** Zero missed contacts, zero tunneling, minimum surface clearance error under 0.1 mm.
- **Candidate generation.** For a known envelope, every site whose static gap is smaller than the peak relative excursion plus its uncertainty appears in contact.list. Pass: zero misses on the 10,000 cases.

### 5.4 Computed property checks (MAT-09, Design Specification section 11.4)

Each construction family is checked on bundles not used to fit its calibration factors, with at least 3 specimens per condition (VAL-03a).

| Property | Test | Pass (proposed) |
| --- | --- | --- |
| Axial stiffness | Held-out bundle tensile test | Within 5 percent |
| Bending and torsion stiffness | Held-out bundle bend and twist tests at 3 temperatures | Within 15 percent at each temperature |
| Damping | Held-out bundle shaker test at 2 amplitudes | Equivalent loss factor within 30 percent at each amplitude |
| Clip stiffness | Clip pull and push test | Within 15 percent |
| Model bounds | Every measured bundle | A measured value outside the computed model bounds opens a review of the bound assumptions and the data; it does not fail the gate on its own |

### 5.5 Wear gates for releases 2 and 3 (WR stays at D1)

| Gate | Test | Pass (proposed) |
| --- | --- | --- |
| Gate 2, coupon | Three datasets per pair with distinct specimens: fitting (5 or more per condition), law selection, and validation (3 or more per held-out condition). Predicted depth is the midpoint of the predicted range | On the validation set: median predicted-to-measured ratio between 0.5 and 2; at least 80 percent of specimens within a factor of 3; median upper-to-lower range ratio no more than 4 |
| Gate 2, standard-test ranking | Relative wear index against ISO 6722-1 scrape and LV 312 results on at least 8 materials | Spearman rank correlation of at least 0.8 |
| Gate 3, shaker harness | Harness with instrumented rub sites (edges of known radius at known clearance), USCAR-2 or ICT profile, then teardown. Bands frozen before testing: high at an index of 0.3 or more, medium 0.05 to 0.3, low below 0.05; worn means above a 10 µm detection limit; a layer not breached by the end of the test is censored | Candidate list no longer than 2 times the instrumented sites plus 5; no worn site in the low band or off the list; at most 50 percent of high-band sites unworn; Spearman correlation of index to measured depth of at least 0.7; depth ranges contain measured depth at 80 percent or more of worn sites, with a median upper-to-lower ratio no more than 4 |
| Gate 3, blind harness | Same as the shaker harness on a changed routing, with the parameter set frozen | Same criteria |

### 5.6 D2 path gates (after release 3)

These are fixed now so the D2 decision cannot drift toward whatever the data shows.

- **Engine geometry.** B-10 repeated on at least 3 harness designs, with the 95th-percentile centerline deviation under the clearance tolerance of the decisions claimed.
- **Core contact runs.** Every candidate site from the contact sweep in the validation harnesses, flagged or not, was rerun in the core with its contact modeled and no mode E step (WP-26), and its contact.history passes the section 5.2 refinement limits.
- **Breach prediction.** On at least 2 harness designs with 3 specimens each, the predicted breach-cycle ranges contain at least 80 percent of observed layer breaches on both bodies, with unbreached layers treated as censored and a median upper-to-lower range ratio no more than 3.
- **Depth accuracy.** Median ratio of predicted midpoint to measured depth between 0.67 and 1.5 at worn sites.
- **Decision.** The TE owner decides on D2 under CRD-07 with these results; meeting the numbers makes D2 possible, not automatic.

### 5.7 Visual inspection acceptance

| Test | Pass (proposed) |
| --- | --- |
| Immersive frame rate, 20-branch harness with environment | 90 frames per second, no dropped frames over a 10-minute session |
| AR registration on a fixture, against coordinate measuring machine points | 5 mm or less at 1 m, measured and reported per fixture, with any bias found corrected before use |
| Dimensional acceptance by AR or scan (VIS-11) | Allowed only where the tolerance is at least 3 times the combined uncertainty at 95 percent (registration and scan combined by root sum of squares when independent). Accept when deviation plus uncertainty is within tolerance; reject when deviation minus uncertainty is outside; otherwise indeterminate, measured conventionally |
| Device storage after a session with a classified scene, on a workstation and a headset | No scene geometry, frames or recordings persisted outside the evidence store |
| Traceability, random sample of 20 screenshots | 100 percent resolve to a run manifest |

## 6. Work packages: release 1

### WP-01 Library schema and records

| Field | Content |
| --- | --- |
| Goal | A versioned store for material records, tribology pair records (both bodies, contact class, law selection, coefficients, validity, evidence), counterface layer stacks and property basis, loaded with datasheet records for the pilot materials |
| Depends on | None |
| Reads | Supplier datasheets; C-20 component IDs; Architecture 0.2 section 15 tags |
| Writes | C-23 records and schema version |
| Requirements | MAT-01, MAT-02, MAT-03, MAT-06, MAT-07, MAT-12 (schema) |
| Acceptance | Schema tests reject any record missing units, temperature range, uncertainty or "not characterized", provenance, specimen count, basis or classification tag; records round-trip by ID and revision; a pair with only an abrasion rating is flagged "rating only" automatically |
| Out of scope | Wear coefficients (WP-15); computed values (WP-02) |

### WP-02 Property calculator

| Field | Content |
| --- | --- |
| Goal | C-23.1 computes bundle axial, bending, torsion and shear stiffness and polymer damping from constituent records and construction geometry, with model bounds, stated assumptions, family calibration factors and propagated uncertainty |
| Depends on | WP-01 |
| Reads | C-23 material records; C-20 construction data (wire count, gauges, insulation, covering, wrap type and pitch); C-34 uncertainty service |
| Writes | Property records with basis "computed" or "calibrated", method and version, input record IDs, bounds and uncertainty |
| Requirements | MAT-07, MAT-08, MAT-11 |
| Acceptance | A single solid wire returns its exact EA, EI and GJ; n identical free wires return n times one wire at the free-slip bound; the bonded bound equals the modulus-weighted sum of the wires' own second moments and their parallel-axis terms; propagated standard deviation matches a 10,000-sample Monte Carlo run (fixed seed) within 5 percent; every output passes the WP-01 schema |
| Out of scope | Friction and wear coefficients, which are never computed (MAT-10) |

### WP-03 Engine benchmark suite

| Field | Content |
| --- | --- |
| Goal | Executable versions of B-01 to B-12 (section 5.1), the refinement harness (section 5.2) and the contact detection set (section 5.3), with reference solutions written and cited first, driven through IF-21 so any engine can be tested |
| Depends on | None |
| Reads | Core stage 3 result for B-09; scans for B-10 |
| Writes | Test suite, reference files, results in C-35 |
| Requirements | PE-10, PE-12 |
| Acceptance | Each analytic or independent numerical reference reproduces to a relative 10⁻⁶, while B-09 and B-10 carry their stated reference uncertainty; extraction algorithms (such as the B-04 eigenvalue sign change) are fixed in the test files before any engine runs; the suite runs headless and gives identical results on two runs |
| Out of scope | Choosing an engine (WP-04) |

### WP-04 Engine selection (AD-09)

| Field | Content |
| --- | --- |
| Goal | A scored comparison of candidate engines (IPS Cable Simulation, any harness tool TE already licenses, an open GPU base such as Newton, and an in-house prototype) against the AD-09 criteria in Design Specification section 5.6, ending in a selection the TE owner signs |
| Depends on | WP-03 |
| Reads | Vendor documentation, trial licenses |
| Writes | Selection report and AD-09 decision record in C-35 |
| Requirements | AD-09 |
| Acceptance | Every candidate runs B-01 to B-08 through a thin harness and the results are recorded; every criterion is scored with cited evidence; the TE owner signs the report |
| Out of scope | Full integration (WP-05) |

### WP-05 Rod geometry and contact adapter (IF-21)

| Field | Content |
| --- | --- |
| Goal | The capability-limited adapter class: supported operations (static shape, large motion, contact query, envelope kinematics), declared unsupported operations (modal, harmonic, PSD, vibration transient) that return a typed "unsupported" error, a determinism declaration and manifest fields, implemented for the selected engine |
| Depends on | WP-01, WP-02, WP-04 |
| Reads | C-21 harness model, C-20, C-23 and C-23.1 records |
| Writes | IF-21 contract, adapter, geometry.engine\_map |
| Requirements | PE-10, PE-11, PE-14 |
| Acceptance | Every unsupported operation returns the typed error; two runs on the CPU reference path agree within the DET-01 tolerance; B-01 to B-08 pass through the adapter |
| Out of scope | Vibration response of any kind (AD-10) |

### WP-06 Mode A installed shape and refinement checks

| Field | Content |
| --- | --- |
| Goal | Quasi-static installed shape under gravity, slack, clip snap-in and assembly tension, with clip constraints, convergence reporting, automatic refinement checks and a seed export for core stage 3 |
| Depends on | WP-02, WP-05 |
| Reads | C-21, C-20, C-23 and C-23.1 records |
| Writes | Installed-shape snapshot, clip loads, geometry.engine\_map |
| Requirements | PE-01, PE-02, PE-04, PE-05, PE-12, PE-14 |
| Acceptance | B-09 and B-10 pass; section 5.2 checks pass on the pilot harnesses; a deliberately unsolvable case is reported invalid, never as a shape |
| Out of scope | Contact detection (WP-10) |

### WP-07 Mode R real-time

| Field | Content |
| --- | --- |
| Goal | Real-time solve of the same model file and parameter set as mode A, labeled "visual only" everywhere it appears |
| Depends on | WP-05, WP-06 |
| Reads | Same as WP-06 |
| Writes | Scene state only, never a released result |
| Requirements | PE-02, PE-03 |
| Acceptance | The same model file loads in modes R and A without edits; a mode R result submitted to C-40 is refused; mode R matches mode A static shape within 10 mm on the pilot harnesses (proposed), so the picture is not misleading |
| Out of scope | Headset frame rate (WP-17) |

### WP-08 3D desktop viewer

| Field | Content |
| --- | --- |
| Goal | OpenUSD scenes from the harness model, engine state and results, rendered in the TE compute zone and streamed to workstations when classified (the desktop path of IF-38), with run manifest IDs, exaggeration factors, gate-status colors and the outside-domain banner |
| Depends on | WP-06 |
| Reads | C-21, engine snapshots, C-40 statuses, C-33 manifests |
| Writes | Scenes; proposed variants when a user moves a clip or route |
| Requirements | VIS-01, VIS-02, VIS-03, VIS-04, VIS-06 (workstations), VIS-09, VIS-12 (glTF rule) |
| Acceptance | Section 5.7 traceability test; exaggeration factor visible on every animated scene; banner appears on a seeded outside-domain case; moving a clip creates a proposed variant and leaves the approved model unchanged; glTF export refused for a classified scene |
| Out of scope | Headsets (WP-17) |

### WP-09 Computed property validation and amendment A6 evidence

| Field | Content |
| --- | --- |
| Goal | Section 5.4 checks for each pilot construction family, using pilot step 3 data for calibration and separate held-out bundles for validation, packaged as the evidence TE needs to approve amendment A6 |
| Depends on | WP-02; pilot step 3 measurements |
| Reads | C-35 test data, C-23.1 outputs |
| Writes | Validation results per family; A6 evidence pack |
| Requirements | MAT-05, MAT-09, MAT-11 |
| Acceptance | Section 5.4 passes for each pilot family, or the family is recorded as "computed values limited to D1"; an automated check confirms no specimen appears in both calibration and validation sets |
| Out of scope | Changing the core's input path (WP-18) |

## 7. Work packages: release 2

### WP-10 Contact detection and candidate generation

| Field | Content |
| --- | --- |
| Goal | Broad phase (bounding volume hierarchy over swept capsules and environment triangles), narrow phase (capsule to capsule, capsule to triangle), continuous collision in mode R and dynamic mode A, the four contact classes, minimum surface clearance, and candidate sites wherever the static gap is smaller than a conservative excursion bound, computed for every segment from core response statistics before any pruning, plus its uncertainty |
| Depends on | WP-06 |
| Reads | Installed shape; environment CAD with edge radius and counterface layer-stack ID per feature; core response statistics for the excursion bound; motion.envelope for refinement once WP-11 exists |
| Writes | contact.list (geometry and clearance) |
| Requirements | PE-06, PE-07, PE-13 |
| Acceptance | Section 5.3 passes; on a coarse mesh of a sharp edge, the edge radius comes from the feature attribute, not the mesh |
| Out of scope | Slip state (WP-12) |

### WP-11 Mode E envelope kinematics

| Field | Content |
| --- | --- |
| Goal | From core snapshots (mode shapes with response statistics, or stored time histories), compute motion.envelope at each candidate site through geometry.core\_map, re-check candidates against the envelope, and estimate normal force from contact compliance and the excursion beyond the gap, for screening only |
| Depends on | WP-10; core response snapshots in C-30 |
| Reads | Core snapshots, geometry.core\_map, contact.list |
| Writes | motion.envelope; contact.list force estimates |
| Requirements | PE-03, PE-08 |
| Acceptance | On a two-mode analytic beam under a known PSD, RMS relative displacement matches analytic spectral integration within 2 percent; a static check finds no call from mode E to any vibration solve; every output carries the "screening" label |
| Out of scope | Depth or life of any kind |

### WP-12 Slip classification

| Field | Content |
| --- | --- |
| Goal | Screening classification of each site as stuck, gross slip or indeterminate. The quantity compared is the peak relative tangential displacement of the two surfaces; the stick limit is μ times the minimum normal force while the contact is closed, divided by k\_t, the tangential contact stiffness for the pair class; a contact that opens during the cycle is intermittent and indeterminate. Stuck at or below the limit, gross slip at 3 times it or more (proposed), indeterminate in between |
| Depends on | WP-11 |
| Reads | motion.envelope, contact.list, C-23 pair records |
| Writes | Slip state in contact.list |
| Requirements | WR-01 |
| Acceptance | Synthetic cases with known force and displacement histories land on the analytic boundaries; against core nonlinear contact histories on at least 20 independent cases, fewer than 10 percent (proposed) are classified stuck when they slide or gross slip when they stick; no wear law runs on a stuck or indeterminate site |
| Out of scope | Patch-level partial-slip mechanics; indeterminate sites are reported, not modeled |

### WP-13 Relative wear index

| Field | Content |
| --- | --- |
| Goal | The relative wear index per site and body, as defined in Design Specification section 7.2, divided by the reference W\_ref of the study's baseline variant, carried as a range |
| Depends on | WP-01, WP-11, WP-12 |
| Reads | motion.envelope, contact.list, C-23 pair records (rating-only pairs feed a separate ordinal rank, never the index) |
| Writes | wear.index |
| Requirements | MAT-04, WR-03, WR-06, WR-07, WR-08, WR-09, WR-12 (for index results) |
| Acceptance | The index is unchanged by a change of units; an Archard pair and its equivalent energy pair give the same index; it rises with each factor that should raise it; a zero W\_ref falls back as Design Specification section 7.2 states; rating-only sites get a separate rank and are never merged; the output schema has no unit, depth, cycle or life field; W\_ref is named in every output. The index-only WR manifest is in force: a request above D1 is refused, a forbidden output is rejected, every report carries the mandatory statements, and every result links the WR credibility record; a candidate fix (clip, covering class, edge protector, clearance) creates a variant whose index is recomputed |
| Out of scope | Depth, cycles to breach, life |

### WP-14 Contact rerun trigger

| Field | Content |
| --- | --- |
| Goal | Write contact.rerun\_request when a discovered site's estimated peak normal force exceeds F\_trig (proposed default 1 N) or it closes in more than p\_trig of envelope samples (proposed default 1 percent); both are profile parameters. The sequence engine then plans a core time-domain step with those contacts modeled (the PHY-05a path); WR depth stays blocked until a core run manifest that models the contact satisfies the request. Requests are bound to the case, contact and input hashes and reopen when any of them change. An owner waiver keeps the site at index only and never unlocks depth |
| Depends on | WP-11 |
| Reads | contact.list, motion.envelope, profile |
| Writes | contact.rerun\_request |
| Requirements | PE-15 |
| Acceptance | A seeded case with one site above threshold produces exactly one open request; WR depth refuses to run while it is open; a core run with that contact modeled closes it; a waiver without an owner ID and reason is rejected, and a waived site still gets no depth; changing the route or a material reopens a satisfied request |
| Out of scope | The core's nonlinear solve itself (core team) |

### WP-15 Tribology data import and wear-law fitting

| Field | Content |
| --- | --- |
| Goal | IF-39 import of reciprocating-sliding test data with wear measured on both bodies, layer by layer; fit Archard and energy laws with temperature-dependent coefficients on the fitting set; choose the law with the lower error (RMS of log ratio) on a separate selection set, picking the energy law when the two differ by less than 10 percent or are equivalent on the data; and provide the coupon depth predictor (layered contact pressure and V(h), Design Specification section 7.2) that gate 2 validates and WP-20 reuses |
| Depends on | WP-01 |
| Reads | Lab files through IF-39; C-35 |
| Writes | Pair records with law, coefficients, uncertainty and evidence |
| Requirements | MAT-05, MAT-10, MAT-13, WR-04 |
| Acceptance | Within each law, synthetic noisy data returns its generating coefficients within the noise; on synthetic data where friction changes at one temperature (third body or humidity), or where the coefficient follows a smooth temperature model, the tool picks the generating law; on data with constant friction it reports the laws as equivalent; a coefficient marked usable for depth needs at least 3 temperatures and 5 specimens per condition; fitting, selection and validation sets never share a specimen; the coupon predictor reproduces the closed-form V(h) for axial slip |
| Out of scope | Running the tests (TE lab) |

### WP-16 Large motion and articulation handoff

| Field | Content |
| --- | --- |
| Goal | Mode A dynamic: implicit integration with Kelvin-Voigt damping and regularized Coulomb friction for motion below the first harness mode, with Kelvin-Voigt coefficients fitted per temperature from bundle bending-oscillation decay in that frequency range; writes motion.rigid\_track for the core to superimpose vibration (APP-04); reports contacts during large motion |
| Depends on | WP-06, WP-10 |
| Reads | Profile articulation definitions, C-21, C-23 |
| Writes | motion.rigid\_track; contact.list entries tagged "large motion" |
| Requirements | PE-09, PE-16 |
| Acceptance | Rigid pendulum period within 1 percent of analytic; Kelvin-Voigt decay of a single-mode case within 2 percent of the analytic damped oscillator; with damping off, energy drift under 1 percent over 10 periods, reported as numerical dissipation; core consumes the rigid track in one demonstration case |
| Out of scope | Vibration superposition (core, Architecture 0.2 section 7.3) |

### WP-17 Immersive review and streaming

| Field | Content |
| --- | --- |
| Goal | IF-38 streaming from the TE compute zone; OpenXR client, plus a native visionOS client where object tracking needs it; live mode R link; the tiered device data rule |
| Depends on | WP-07, WP-08 |
| Reads | Scenes, C-60 classification tags |
| Writes | Streams; proposed variants; screenshots and recordings only into C-35 |
| Requirements | VIS-05, VIS-06 |
| Acceptance | Section 5.7 frame-rate and device-storage tests; a screenshot or recording attempt on a classified scene is blocked or routed to C-35 |
| Out of scope | AR overlay (WP-23) |

### WP-18 Core use of calibrated properties

| Field | Content |
| --- | --- |
| Goal | After TE approves amendment A6, the core's input path accepts calibrated bundle stiffness and damping for validated families and applies the MAT-09 range check |
| Depends on | WP-09 passed; TE approval of A6 |
| Reads | C-23.1 records, C-41 registry |
| Writes | Core input records with basis and range check result |
| Requirements | MAT-09 |
| Acceptance | A run with a calibrated value inside a validated family carries no ceiling cut from that input; outside the family the gate caps the result at D1; the audit trail links the A6 approval |
| Out of scope | Release 1 core runs, which keep measured inputs |

### WP-19 Wear validation for gate 2

| Field | Content |
| --- | --- |
| Goal | Run the gate 2 coupon and standard-test ranking checks in section 5.5 |
| Depends on | WP-13, WP-15 |
| Reads | C-35 test data, pair records, wear.index |
| Writes | Gate 2 evidence in C-35 |
| Requirements | WR-04, MAT-05 |
| Acceptance | Section 5.5 gate 2 criteria on the validation set only; a rating-to-coefficient conversion is fitted on one subset of pairs that have both a rating and a measured coefficient, checked on the rest, and reported as evidence only, never used by WP-13 in releases 1 to 3; on a failure, the report lists the pairs to retest |
| Out of scope | Harness-level validation (WP-24) |

## 8. Work packages: release 3

### WP-20 Wear depth from time histories

| Field | Content |
| --- | --- |
| Goal | Depth ranges and cycles to each layer breach on both bodies, only from contact.history at the site: frictional work as the integral of tangential force over actual slip (energy law) or sliding distance and pressure (Archard), the pair's selected law, a layered contact model on the edge radius for pressure, and V(h) for volume to depth, both from the WP-15 coupon predictor |
| Depends on | WP-12, WP-14, WP-15, WP-28 |
| Reads | contact.history (WP-28), contact.list, contact.rerun\_request, pair records |
| Writes | wear.depth, wear.layer\_state; corrosion risk flag when the substrate is exposed |
| Requirements | WR-02, WR-03, WR-04, WR-10, WR-11 |
| Acceptance | Constant force and slip histories give the closed-form depth; layers switch at their exact thickness; each body uses its own pair coefficients; the module refuses to run while a rerun request is open, and refuses depth for a rating-only pair; "substrate exposed" raises the corrosion risk flag to its owner |
| Out of scope | Corrosion prediction (AD-17) |

### WP-21 Wear state and geometry update

| Field | Content |
| --- | --- |
| Goal | Write wear state at each sequence step (SEQ-03) and, on a layer breach, geometry.wear\_update per body (local surface recession at the site's surface coordinates, new outer material, new pair record, friction) from the central depth estimate, with the depth bounds run as DET-04 uncertainty cases, for the core and engine to use at the next step |
| Depends on | WP-20 |
| Reads | wear.depth, wear.layer\_state, C-23 records |
| Writes | geometry.wear\_update |
| Requirements | WR-05 |
| Acceptance | In a two-step sequence where step 1 breaches a covering, step 2 model assembly reads the new radius and pair record; the plan check refuses a depth sequence without contact.history for every depth site, and an index-only sequence without response statistics |
| Out of scope | Updating bundle stiffness for wear, which stays a logged limitation until data shows it matters |

### WP-22 WR ceiling, outputs and candidate fixes

| Field | Content |
| --- | --- |
| Goal | The WR IF-10 manifest from WP-13 extended to depth outputs: allowed and forbidden outputs, mandatory statements, a D1 ceiling through release 3, and candidate fixes (add or move a clip, raise the covering class, add an edge protector, increase clearance) runnable as variants |
| Depends on | WP-13, WP-20 |
| Reads | Gate requests, wear outputs |
| Writes | Reports; proposed variants |
| Requirements | WR-06, WR-07, WR-08, WR-12 (extended to depth results) |
| Acceptance | A request for a WR result above D1 is refused with its reason; the report service rejects a no-rub-through guarantee or a field life in years; a candidate fix creates a variant that runs through WP-06, WP-10, WP-11 and WP-13 |
| Out of scope | Any change to the gate's own logic |

### WP-23 AR overlay, inspection findings and scan import

| Field | Content |
| --- | --- |
| Goal | AR anchored on TE-owned rigid references (fixtures, buck datums) through CAD model targets or visionOS object tracking; registration error measured and shown before anything is recorded; deviations stored as inspection findings; IF-40 scan import to geometry.asbuilt; dimensional acceptance only under VIS-11 |
| Depends on | WP-17 |
| Reads | Scenes, reference CAD of TE-owned fixtures, scans |
| Writes | Inspection findings in C-35, geometry.asbuilt |
| Requirements | VIS-07, VIS-08, VIS-10, VIS-11, VIS-13 |
| Acceptance | Section 5.7 registration and dimensional-acceptance tests; no deviation can be recorded before the registration error is shown; records are typed "inspection finding" unless the VIS-11 check passed for that fixture; a tracking-model export of non-TE-owned or classified geometry is refused |
| Out of scope | Tracking the flexible harness itself |

### WP-24 Wear validation for gate 3

| Field | Content |
| --- | --- |
| Goal | Run the gate 3 shaker-harness and blind-harness checks in section 5.5 |
| Depends on | WP-20, WP-21, WP-22 |
| Reads | C-35 test and teardown data, WR outputs |
| Writes | Gate 3 evidence |
| Requirements | WR-04, WR-12 |
| Acceptance | Section 5.5 gate 3 criteria; the blind harness runs with no retuning, enforced by freezing the parameter set's hash before the test |
| Out of scope | D2 decisions (section 9) |

### WP-28 Core external-contact histories

| Field | Content |
| --- | --- |
| Goal | The core's nonlinear time-domain stage models the external rub contacts named in contact.rerun\_request, with the pair's friction and tangential stiffness, and writes contact.history per site |
| Depends on | WP-14 |
| Reads | contact.rerun\_request, contact.list, geometry.core\_map, C-23 pair records |
| Writes | contact.history; satisfied rerun requests with the satisfying run manifest |
| Requirements | PE-15, WR-10; PHY-05a, PHY-11 |
| Acceptance | A single-degree-of-freedom oscillator rubbing a flat under harmonic drive reproduces Den Hartog's closed-form steady state for continuous sliding (slip amplitude and frictional work per cycle) within 2 percent; samples are synchronized at the declared resolution; section 5.2 refinement limits hold; each history links the request it satisfies |
| Out of scope | Wear calculation (WP-20) |

## 9. Gated after release 3: wear D2 path

Nothing in this section starts until gate 3 passes. These packages exist now so the D2 path is fixed before results arrive.

### WP-25 D2 engine geometry qualification

| Field | Content |
| --- | --- |
| Goal | A credibility record for engine geometry outputs (installed shape, clearance, contact sites) at D2, based on B-10 repeated on at least 3 harness designs |
| Depends on | Gate 3 passed |
| Requirements | Design Specification section 7.5 |
| Acceptance | Section 5.6 engine geometry criterion; the record states the clearance tolerance it supports |

### WP-26 Core contact reruns at every flagged site

| Field | Content |
| --- | --- |
| Goal | Core nonlinear time-domain runs with every candidate contact modeled, flagged or not, on the D2 validation harnesses, with force and slip histories stored per site |
| Depends on | WP-28; gate 3 passed |
| Requirements | Design Specification section 7.5; PHY-05a |
| Acceptance | Every flagged site has a satisfied rerun request and stored histories; the section 5.2 refinement limits hold for sliding work |

### WP-27 D2 wear validation campaign

| Field | Content |
| --- | --- |
| Goal | Run the section 5.6 breach and depth gates on at least 2 harness designs with 3 specimens each and package the evidence for the TE owner's CRD-07 decision |
| Depends on | WP-25, WP-26 |
| Requirements | Design Specification section 7.5; CRD-07 |
| Acceptance | Section 5.6 criteria met or failed with the record kept either way; a pass makes D2 eligible, and the owner decides |

## 10. Open items and change history

### 10.1 Open items for TE

- [ ] Confirm or replace every "proposed" threshold: F\_trig and p\_trig (WP-14), the slip band factor (WP-12), refinement limits (section 5.2), and the wear, computed-property and visual gates (sections 5.4 to 5.7).
- [ ] Name the construction families that cover most harness volume, so WP-09 calibrates the right ones first.
- [ ] Supply DMA and tensile data for insulation, jacket and tape compounds from minus 55 to plus 125 degrees C, or approve a test order.
- [ ] Name the owner who receives the "substrate exposed" corrosion risk flag.
- [ ] List the TE-owned fixtures and buck datums that may be released as on-device tracking models.
- [ ] Provide the governing LV 312 and ISO 6722-1 editions for the gate 2 ranking test.
- [ ] Arrange trial licenses for the WP-04 candidates and agree the core team's support for WP-14 and WP-26 reruns.
- [ ] Choose a twist-bend instability benchmark (such as the onset of Michell's twisted-ring instability) with a verified reference; B-12 covers only the stable coupled state.

### 10.2 Change history

| Draft | Date | Change |
| --- | --- | --- |
| 0.1 | October 28, 2026 | First build plan, split from Design Specification 0.3: state descriptors, benchmark and acceptance definitions, and 28 work packages across three releases and the gated D2 path, revised after a second Codex review round (contact histories and WP-28, index manifest in release 2, three-way dataset split, executable benchmarks, frozen gate bands, baseline refinement limits) |
