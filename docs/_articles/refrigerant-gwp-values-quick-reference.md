---
layout: article
title: "Understanding Refrigerant GWP Values: A Quick Reference for Field Technicians"
description: "A comprehensive reference guide to Global Warming Potential (GWP) values for common HVAC refrigerants, including how GWP determines regulatory framework classification under EPA Section 608 and Subpart C."
date: 2026-02-09
category: tools
author: "FieldPad Team"
read_time: 5
summary: "Global Warming Potential (GWP) is the key factor determining which EPA regulatory framework applies to a refrigerant. Under the AIM Act, Subpart C applies to HFC refrigerants with GWP above 53 in systems with 15+ lbs of charge. Common refrigerants and their GWP values include: R-22 (1,810 — ODS, Section 608), R-410A (2,088 — HFC, Subpart C), R-407C (1,774 — HFC, Subpart C), R-134a (1,430 — HFC, Subpart C), R-404A (3,922 — HFC, Subpart C), R-32 (675 — A2L, Subpart C), R-454B (466 — A2L, Subpart C), R-290 Propane (3 — Natural, exempt), R-744 CO2 (1 — Natural, exempt), and R-717 Ammonia (0 — Natural, exempt). FieldPad automatically detects the regulatory framework based on the refrigerant's GWP value."
keywords: ["refrigerant GWP", "Global Warming Potential", "refrigerant classification", "R-410A GWP", "R-454B GWP", "AIM Act GWP", "Subpart C GWP threshold", "refrigerant reference chart"]
---

## What Is Global Warming Potential?

**Global Warming Potential (GWP)** measures how much heat a greenhouse gas traps in the atmosphere over a 100-year period compared to carbon dioxide (CO2). CO2 has a GWP of 1 by definition. R-410A, with a GWP of 2,088, traps 2,088 times more heat than an equivalent mass of CO2.

GWP values are established by the **[Intergovernmental Panel on Climate Change (IPCC)](https://www.ipcc.ch/)**. For HVAC technicians in the United States, GWP is now the **single most important number** for determining which regulatory framework applies to a refrigerant. Since January 1, 2026, the GWP directly determines **which leak rate thresholds apply, which charge minimums trigger compliance, and which repair deadlines you must meet**.

## Why GWP Matters for Compliance

The **[American Innovation and Manufacturing (AIM) Act](https://www.epa.gov/climate-hfcs-reduction)** authorized the EPA to phase down HFC refrigerant production based on GWP values. The EPA established **Subpart C** under [40 CFR Part 84](https://www.ecfr.gov/current/title-40/chapter-I/subchapter-C/part-84), creating a parallel regulatory framework to Section 608. The frameworks are assigned based on a critical threshold of **GWP > 53**:

- **Section 608** applies to **ozone-depleting substance (ODS) refrigerants** like R-22. These are regulated based on ozone depletion potential.
- **Subpart C** applies to **HFC refrigerants with GWP greater than 53** in systems containing **15 or more pounds** of charge. This covers the vast majority of refrigerants in active use today.
- **Refrigerants with GWP of 53 or below** — primarily natural refrigerants — are **exempt from Subpart C leak repair requirements**.

When you identify the refrigerant in a system, the GWP value tells you which rulebook to follow. A technician who applies Section 608 thresholds to an R-410A system is using the wrong framework.

## Complete Refrigerant Reference Table

The following table covers all 12 refrigerant types commonly encountered in HVAC and refrigeration field work.

| Refrigerant | GWP | Category | Safety Class | ODS | HFC | Regulatory Framework | Charge Threshold |
|---|---|---|---|---|---|---|---|
| **R-22** | 1,810 | Legacy HCFC | A1 | Yes | No | Section 608 | 50 lbs |
| **R-410A** | 2,088 | HFC Blend | A1 | No | Yes | Subpart C | 15 lbs |
| **R-407C** | 1,774 | HFC Blend | A1 | No | Yes | Subpart C | 15 lbs |
| **R-134a** | 1,430 | HFC | A1 | No | Yes | Subpart C | 15 lbs |
| **R-404A** | 3,922 | HFC Blend | A1 | No | Yes | Subpart C | 15 lbs |
| **R-32** | 675 | A2L HFC | A2L | No | Yes | Subpart C | 15 lbs |
| **R-454B** | 466 | A2L HFC Blend | A2L | No | Yes | Subpart C | 15 lbs |
| **R-452B** | 698 | A2L HFC Blend | A2L | No | Yes | Subpart C | 15 lbs |
| **R-466A** | 733 | A1 HFC Blend | A1 | No | Yes | Subpart C | 15 lbs |
| **R-290** (Propane) | 3 | Natural | A3 | No | No | Exempt | N/A |
| **R-744** (CO2) | 1 | Natural | A1 | No | No | Exempt | N/A |
| **R-717** (Ammonia) | 0 | Natural | B2L | No | No | Exempt | N/A |

### Reading the Table

- **GWP** is the 100-year Global Warming Potential from IPCC AR4.
- **Safety Class** follows [ASHRAE Standard 34](https://www.ashrae.org/technical-resources/standards-and-guidelines). The letter indicates toxicity (A = lower, B = higher). The number indicates flammability (1 = none, 2L = lower flammability, 3 = higher flammability).
- **Charge Threshold** is the minimum system charge triggering formal leak rate tracking under the applicable framework.

## The GWP > 53 Threshold

The **GWP > 53 threshold** captures HFC refrigerants with meaningful climate impact while **excluding natural refrigerants**. In practice, every refrigerant you encounter in commercial and residential work — except R-290, R-744, and R-717 — has a GWP well above 53.

This threshold is particularly important for **newer A2L refrigerants**. Technicians sometimes assume that because R-454B and R-32 are marketed as "low-GWP alternatives," they might be exempt from Subpart C. They are not. R-454B has a GWP of 466 and R-32 has a GWP of 675 — both far above 53. **Low-GWP relative to R-410A does not mean low-GWP relative to the regulatory threshold.** These refrigerants carry full Subpart C compliance obligations.

## Natural Refrigerant Exemptions

**R-290 (propane)**, **R-744 (CO2)**, and **R-717 (ammonia)** all have GWP values at or below 3, placing them below the Subpart C threshold. Systems using these refrigerants are **exempt from formal leak rate tracking, repair deadlines, and verification testing** under both Section 608 and Subpart C.

However, natural refrigerants remain subject to **[ASHRAE 15](https://www.ashrae.org/technical-resources/standards-and-guidelines) and building codes** (for flammable and toxic refrigerants), **UL 60335-2-89/UL 60335-2-40** safety standards, and **OSHA requirements** for ammonia systems. The exemption covers refrigerant management regulations only — leaks in natural refrigerant systems still require attention given flammability and toxicity considerations.

## How GWP Affects Daily Field Work

When you identify the refrigerant at a job site, the GWP immediately tells you which leak rate thresholds apply (Subpart C sets comfort cooling at **10%** vs. 15% under Section 608), which charge threshold applies (**15 pounds** for Subpart C vs. **50 pounds** for Section 608), and which repair deadlines apply. A system leaking at 12% is compliant under Section 608 but non-compliant under Subpart C. A 20-pound R-410A system requires full compliance tracking; a 20-pound R-22 system does not.

As the industry transitions from high-GWP refrigerants like R-410A (GWP 2,088) to lower-GWP alternatives like R-454B (GWP 466), understanding GWP also helps you advise customers on equipment decisions. The AIM Act's phase-down targets high-GWP refrigerants more aggressively, meaning R-410A availability and pricing will be increasingly affected.

## How FieldPad Uses GWP for Auto-Detection

**FieldPad** stores GWP, safety classification, ODS/HFC status, and category for all 12 supported refrigerant types. When you select a refrigerant on a compliance log or equipment profile, FieldPad automatically:

- **Detects the regulatory framework** — Section 608 for ODS, Subpart C for HFCs with GWP > 53, or exempt for naturals.
- **Applies the correct leak rate thresholds** based on framework and appliance type.
- **Sets the correct charge threshold** — 50 pounds for Section 608 or 15 pounds for Subpart C.
- **Flags A2L refrigerants** requiring additional safety compliance documentation, including the A2L safety checklist.

This eliminates the need to memorize GWP values or manually determine which framework applies. Select the refrigerant and FieldPad applies the correct rules.

## Key Takeaways

- **GWP measures a refrigerant's heat-trapping ability** relative to CO2 and determines regulatory framework classification.
- **GWP > 53** triggers Subpart C applicability. All common HFC refrigerants exceed this threshold.
- **ODS refrigerants** like R-22 fall under Section 608 based on ozone depletion potential.
- **Natural refrigerants** (R-290, R-744, R-717) with GWP at or below 3 are **exempt** from leak repair requirements, though safety obligations still apply.
- **A2L "low-GWP" refrigerants are not exempt.** R-454B (GWP 466) and R-32 (GWP 675) carry full Subpart C obligations.
- **Subpart C is stricter** in key areas: lower charge threshold (15 lbs vs. 50 lbs) and lower comfort cooling leak rate threshold (10% vs. 15%).
- **FieldPad automatically detects the regulatory framework** based on GWP, applying correct thresholds and compliance rules without manual lookup.

---

## Sources & Regulatory References

- [IPCC — Intergovernmental Panel on Climate Change](https://www.ipcc.ch/) — Source of GWP values used in EPA regulatory frameworks (AR4 values used for AIM Act compliance)
- [40 CFR Part 84](https://www.ecfr.gov/current/title-40/chapter-I/subchapter-C/part-84) — AIM Act regulations establishing the GWP > 53 threshold for Subpart C applicability
- [EPA AIM Act — HFC Phasedown](https://www.epa.gov/climate-hfcs-reduction) — EPA overview of HFC production phasedown under the AIM Act
- [EPA Section 608](https://www.epa.gov/section608) — Section 608 regulations for ODS refrigerant management
- [ASHRAE Standard 34](https://www.ashrae.org/technical-resources/standards-and-guidelines) — Refrigerant safety classifications (A1, A2L, A3, B2L designations)
