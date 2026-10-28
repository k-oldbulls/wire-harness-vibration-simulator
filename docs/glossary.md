# Wire Harness Vibration Simulator

Simulation platform that predicts how automotive and industrial wire harnesses respond to vibration, where they rub, and how fast coverings wear, so TE engineers can rank designs and plan tests before a build.

## Wear and rub points

**Relative wear index**:
A dimensionless number from engine envelope kinematics that ranks contacts and design variants by expected wear severity. It carries no unit and never converts to depth or life.
_Avoid_: wear score, wear depth (for envelope results)

**Wear depth range**:
Predicted material loss per layer at one contact, as a range, computed only from stored core time histories at that contact.
_Avoid_: wear prediction, rub-through life (as a single value)

## Material properties

**Property basis**:
Where a property value came from: measured (a test on the item itself), computed (the property calculator from constituent records and geometry) or calibrated (computed, then corrected by a family factor fitted on bundle tests).
_Avoid_: derived, estimated, simulated (as basis labels)

**Construction family**:
A set of bundles sharing covering type, wrap pattern and insulation compound class over a stated range of wire count and diameter; the unit over which a calibration factor is fitted and may be reused.
_Avoid_: bundle type, harness family

**Counterface**:
The surface a harness rubs against (bracket, panel, fastener, another bundle), modeled as its own layer stack that wears alongside the harness.
_Avoid_: environment surface (when talking about wear), mating part

**Layer breach**:
The point at which wear removes a whole layer at a contact, on either body: covering, insulation, shield, conductor exposure on the harness side; topcoat, primer or e-coat, zinc, substrate exposure on the counterface side.
_Avoid_: rub-through (except for conductor exposure), wear-out

## Inspection

**Inspection finding**:
A deviation an inspector or scan marks between the predicted and the physical harness during AR review; it prompts a measurement and is not an acceptance result.
_Avoid_: AR measurement, AR acceptance

## Wear studies

**Index-only study**:
A wear study that ends at the relative wear index; it needs core response statistics but no contact histories, and it is the only kind a rating-only pair can run.
_Avoid_: quick wear run, screening run

**Depth study**:
A wear study that models flagged contacts in a core time-domain run and computes depth from the resulting contact histories; its D2 form skips envelope screening and models every candidate.
_Avoid_: full wear run

**Rerun request**:
A record that a contact found by screening must be modeled in a core time-domain run before any depth is computed there; bound to the input hashes it was raised on, and never satisfied by a waiver.
_Avoid_: rerun flag, waiver (as a substitute)
