# Emission Calculator — portfolio demo

> **What this is.** A sanitized demo of an industrial-emissions lab calculation pipeline: from a tablet-filled sampling act, through a live Excel calculator, to a protocol-ready Word document — with expanded measurement uncertainty at P = 0.95.
>
> Company name, instrument serial numbers, and signatures are **fictional**. The real client files stayed inside the lab; this repository is the engineering fingerprint of the logic.

**Author:** Daniil Glotov · [github.com/proggdd](https://github.com/proggdd) · [LinkedIn](https://www.linkedin.com/in/daniil-glotov/)
**Русская версия:** см. [README.md](README.md)

---

## Why this is published

I worked at an accredited atmospheric-emissions lab maintaining an Excel engine that implemented GOST 33007‑2014, GOST 17.2.4.06‑90 / 17.2.4.07‑90, and RMG 62‑2003. The system processed ~820 samples / 82 clients / zero formula errors; per-protocol turnaround dropped from a 40-minute manual recalculation to 4 minutes.

This repository is an anonymized miniature of that system. It is not meant to run a lab — it exists to **publicly show the engineering approach**: how to turn dense metrological standards into a traceable, testable, re-runnable artifact.

## What's inside

```
emission-calc-demo/
├── calculator.xlsx        — main file: 4 sheets, live formulas, colour legend
├── sampling_act.xlsx      — electronic sampling act (tablet-style layout)
├── protocol.docx          — lab test protocol (Word)
├── protocol.pdf           — same as PDF, client-deliverable
└── docs/
    └── methodology.md     — brief on methods and formulas (EN)
```

## End-to-end data flow

```
┌────────────────────┐     ┌────────────────────┐     ┌────────────────────┐
│ sampling_act.xlsx  │ ──▶ │ calculator.xlsx    │ ──▶ │ protocol.docx/pdf  │
│  tablet-filled     │     │  4 sheets, live    │     │  client deliverable│
│  on-site           │     │  recalculation     │     │                    │
└────────────────────┘     └────────────────────┘     └────────────────────┘
```

## `calculator.xlsx` — how to read it

The file is structured like a tiny ETL inside Excel: inputs, constants, computation, and output live on separate sheets.

| Sheet | Purpose | Colour code |
| --- | --- | --- |
| `СИ_Справочник` | instrument calibration tables (K_tube, δ%) | 🟢 green — immutable constants |
| `Данные_отбора` | values from the sampling act (P_dyn, t_gas, Δm, τ …) | 🟡 yellow — manual input |
| `Расчёт` | main worksheet with all formulas | 🟠 orange — computed |
| `Итог_в_протокол` | protocol-ready result table | 🟢 bright green — final results |

### Core formulas (on `Расчёт` sheet)

```
ρ_actual = 1.293 · (P_atm·133.3 + P_static)/101325 · 273/(273+t_gas)
W        = K_tube · √(2·P_dyn / ρ_actual)                 (GOST 17.2.4.06)
Q_act    = S · W                                          (volumetric flow, actual cond.)
Q_norm   = Q_act · 273 · (P_atm·133.3+P_static) / (760·133.3·(273+t_gas))
V_sample = W_rot · τ · (P_r·1000+P_atm·133.3) · 273 / ((273+t_r)·760·133.3)
Z        = Δm · 10⁶ / V_sample                            (dust concentration, norm.)
Ф        = Z · Q_norm / 1000                              (mass emission, g/s)

ΔX / X   = 2 · √( Σ (δ_i / 2)² )    at P = 0.95, k = 2    (RMG 62-2003)
```

Every formula is live: change any yellow cell on `Данные_отбора`, and the final table on `Итог_в_протокол` recomputes.

### Cross-check with the original

With the demo inputs, `Расчёт → B26 (Dust concentration)` comes out to exactly **35.5 mg/m³** — the same value the original lab system produced for a similar source. Confirms this anonymized version preserves the metrology, not just the appearance.

## `sampling_act.xlsx` — why Excel, not Word

Samplers work from a tablet in the field. A docx on a 12″ screen is awkward; a grid-layout xlsx with dropdowns and yellow input highlighting is not. The layout is built so:

- cells are large enough for tablet touch input;
- each value lives in its own cell, so sync with the calculator is by coordinates, no OCR;
- the cross-section area formula `S = π·D²/4` runs in the act itself — the sampler sanity-checks geometry on-site;
- signature blocks are sized for tablet-signature capture.

## `protocol.docx` — output format

Generated via a Node `docx-js` script. Layout follows a real protocol template: lab header → protocol meta → results table (7 flow parameters + 5 gas concentrations) → instruments table → "Opinions and interpretation" section → signatures.

Numbers in the table are the same values the calculator computes.

## Connection to my Python work

In the production lab version I kept hitting two Excel limits:

1. **No traceability** between sampling act and protocol — if a client asks a year later where a number came from, you're opening three files.
2. **No validation** — a bad cell lives until the first manual review.

The fix is staged:
- here, in the demo — colour coding + strict sheet separation as discipline;
- in the production version I'm building now — migration to FastAPI + SQLAlchemy + PostgreSQL, where the sampling act and protocol are database rows linked by `protocol.sampling_act_id`, and formulas are rewritten as testable Python functions under pytest.

See [life-tracking-bot-python-public](https://github.com/proggdd/life-tracking-bot-python-public) for the same pattern applied to a different system: porting a working Apps Script engine to Python with strict schemas and Alembic migrations.

## Contacts

- [LinkedIn](https://www.linkedin.com/in/daniil-glotov/)
- [Telegram](https://t.me/glotov_daniil)
- gddviet@gmail.com

## License

MIT. Reuse the structure, formulas, and colour coding freely — just don't paste the demo numbers into a real protocol 🙂
