# Contaminant thresholds — how to read a number against more than one rule

A measured contaminant level means nothing on its own. It means something only against a
threshold, and **the thresholds disagree with each other by design.** When a warning or a test
result comes up, find the number and then report how each regime treats it. Disagreement
between regulators is the finding, not a problem to resolve.

## The four kinds of threshold, which are not comparable

| Kind | Example | What it actually says |
|---|---|---|
| **Exposure-based warning trigger** | California Prop 65, lead: **0.5 µg per day** | Above this you must *warn*. Not a safety limit, and not a ban. Deliberately low. |
| **Hard concentration ceiling** | EU maximum levels, Reg (EU) 2023/915 | Above this the product **may not be placed on the market**. A border check fails it. |
| **National limit elsewhere** | Chinese GB standards, US FDA action levels | Same shape as the EU ceiling, different number. |
| **Risk-assessment output** | THQ / TTHQ, TCR | Whether the amount a person *actually eats* reaches a level of concern. THQ < 1 and TCR < 10⁻⁴ are the conventional acceptability marks. |

**Never collapse these into "safe" or "unsafe."** A product can trigger a California warning,
fail an EU ceiling, pass its home country's limit, and still have a hazard quotient below 1.
That is an ordinary result, and reporting all four is more useful than picking one.

## Lead in dried spices — EU maximum levels

Commission Regulation (EU) 2021/1317, consolidated into (EU) 2023/915. These are the ones that
come up most, because spices concentrate soil metals:

| Category | Lead, mg/kg |
|---|---|
| **Fruit spices** — *Capsicum* dried fruits, whole or ground: chillies, chilli powder, cayenne, paprika | **0.60** |
| Seed spices | 0.90 |
| Root and rhizome spices (turmeric, ginger) | 1.50 |
| Bud and flower pistil spices | 1.0 |
| Bark spices (cinnamon) | 2.0 |

## California Prop 65

- Lead warning trigger: **0.5 µg/day of exposure**, which is low enough that a large share of
  the spice and chilli category carries a label.
- The list is maintained by OEHHA. Check whether the specific ingredient is actually listed
  before asserting a warning is "about" it.
- Many manufacturers print the warning on national packaging rather than run a separate
  California run, so its presence says less about a specific maker than it looks like it does.

## Worked example — why this file exists

Dried chilli from Guizhou, China. A 2024 study in *Scientific Reports* sampled 130 batches
across nine Guizhou prefectures (2023 sampling) and measured mean lead at **0.8949 mg/kg**.

- **California**: warn. Far above the 0.5 µg/day exposure trigger at normal serving sizes.
- **EU**: a batch at that mean **fails** the 0.60 mg/kg ceiling for fruit spices.
- **China**: national limits exceeded in **11.11%** of samples — so ~89% pass.
- **Risk assessment**: THQ and TTHQ **below 1** in all nine regions; TCR **below 10⁻⁴**. The
  amount actually eaten does not reach a level of concern.

Four rules, one chilli, four answers. The correct report gives all four and does not pretend
they resolve. It also names which prefectures failed (Qiandongnan, Anshun, Qianxinan) and which
did not, because **origin resolution matters more than the provincial average.**

## The rule this file enforces

**A category-wide warning is not "nothing."** It is weak evidence about the *category*, which
still bears on the product in front of you. Do not dismiss a Prop 65 label as boilerplate and
stop there. Go find whether anyone has measured the commodity, and report the measurement
against the thresholds above. "Most chilli products carry this label" and "there is real lead
in chilli" are both true, and only the second one is informative.
