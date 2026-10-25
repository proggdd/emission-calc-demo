# Methodology notes

Brief technical background for readers who aren't familiar with industrial-emissions lab work.

## Problem domain

An accredited atmospheric-emissions lab measures what comes out of industrial chimneys: dust, combustion gases (CO, NOx, SO₂), velocity, temperature, humidity. The results go into protocols that feed regulatory reporting, environmental permits, and enforcement actions, so every number is audited.

## Regulatory standards implemented in this demo

| Code | Scope |
| --- | --- |
| GOST 17.2.4.06-90  | Gas flow velocity determination via dynamic-pressure (Pitot) method |
| GOST 17.2.4.07-90  | Determination of static pressure, temperature, and moisture in gas flows |
| GOST 33007-2014    | Gravimetric determination of particulate matter (dust) in emissions |
| RMG 62-2003        | Estimation of combined / expanded uncertainty at P = 0.95 |

## Measurement chain (dust)

1. **Velocity**: Pitot tube → dynamic pressure P_dyn → `W = K · √(2·P_dyn / ρ)`.
2. **Flow rate**: cross-section area × velocity, then normalised to standard conditions (273 K, 101.325 kPa).
3. **Dust mass**: sample drawn through a pre-weighed filter (cartridge) at isokinetic rate. Mass gain Δm = m₁ − m₀.
4. **Concentration**: Δm divided by the sampled gas volume at normal conditions.
5. **Emission**: concentration × volumetric flow at normal conditions.
6. **Uncertainty**: combined in quadrature from component uncertainties (instruments, time, mass, geometry), expanded by k = 2.

## Why Excel was the right tool, and where it breaks

**Right:** every lab already uses Excel; auditors read Excel; formula changes are immediately visible in cell inspection; the team can work without deploying anything.

**Breaks at:**

- **Provenance.** A cell containing `0.0149` in the protocol doesn't point back to the sampling-act row it came from. You know the method, you don't know the pipeline.
- **Validation.** A stray `=B10*B11` where `*B12` was intended produces plausible-looking numbers for months.
- **Multi-user.** Two samplers can't edit the same act concurrently without overwriting each other.
- **Audit trail.** Undo stack lives in RAM; once the file is saved, you can't answer "who changed what, when."

The staged fix: keep Excel as the human-facing UI for field work, but move the calculation engine and the protocol-generation layer into Python with a proper schema. Excel becomes the input form and the preview, not the source of truth.

## What I kept and what I simplified in this demo

**Kept exactly** (from the original system):
- sheet separation (reference / inputs / calculation / output);
- colour coding of cell semantics (input / constant / computed / result);
- the velocity → flow → concentration → emission chain with the same normalisation to standard conditions;
- combined uncertainty via sum-of-squares at k = 2.

**Simplified** (for readability in a public demo):
- the original `Выход 1` sheet supports 10 dust-collection lines (for multi-stack ISA installations); this demo collapses to 1 line to keep the math visible;
- the original pressure-saturation table for condensation-method moisture has 86 rows; here the condensation branch is omitted; the demo path is the dry-gas Pitot chain only;
- the original has separate branches for rectangular vs. circular ducts with different geometry uncertainty propagation; the demo covers the circular-duct branch only.

The simplification is cosmetic: nothing load-bearing was removed. Anyone familiar with the standards will recognise the structure immediately; anyone not familiar will find it easier to read than the 109×55-cell original.
