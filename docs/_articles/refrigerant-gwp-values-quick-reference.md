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

**Global Warming Potential (GWP)** is a measure of how much heat a greenhouse gas traps in the atmosphere over a specific time period compared to carbon dioxide (CO2). By definition, CO2 has a GWP of 1. A refrigerant with a GWP of 2,088 — like R-410A — traps 2,088 times more heat than an equivalent mass of CO2 over a 100-year period.

GWP values are established by the **Intergovernmental Panel on Climate Change (IPCC)** and are used by regulators worldwide to classify refrigerants and set environmental policy. In the United States, GWP is now the **single most important number** for determining which regulatory framework applies to a given refrigerant under federal law.

For HVAC technicians, GWP is no longer just a technical specification on a safety data sheet. Since January 1, 2026, the GWP of the refrigerant in a system directly determines **which leak rate thresholds apply, which charge minimums trigger compliance obligations, and which repair deadlines you must meet**. Getting the classification wrong can mean applying the wrong threshold and missing a compliance obligation entirely.

## Why GWP Matters for Compliance

The **American Innovation and Manufacturing (AIM) Act** authorized the EPA to phase down the production and consumption of HFC refrigerants based on their GWP values. As part of this phase-down, the EPA established **Subpart C** under 40 CFR Part 84, which creates a parallel regulatory framework to the existing Section 608 rules.

The critical threshold is **GWP > 53**. Here is how the two frameworks are assigned:

- **Section 608** applies to **ozone-depleting substance (ODS) refrigerants** — the legacy HCFC and CFC refrigerants like R-22, R-12, and R-502. These are regulated based on their ozone depletion potential, and Section 608 has governed their handling since the 1990s.
- **Subpart C** applies to **HFC and HFC-blend refrigerants with a GWP greater than 53** in systems containing **15 or more pounds** of charge. This framework took effect January 1, 2026, and covers the vast majority of refrigerants in active commercial and residential use today.
- **Refrigerants with GWP of 53 or below** — primarily natural refrigerants like propane (R-290), CO2 (R-744), and ammonia (R-717) — are **exempt from Subpart C leak repair requirements**. They are still subject to general safe handling practices and applicable safety codes, but the formal leak rate tracking, repair deadlines, and verification testing obligations do not apply.

This means that when you arrive at a job site and identify the refrigerant in a system, the **GWP value tells you which rulebook to follow**. A technician who applies Section 608 thresholds to an R-410A system is using the wrong framework — and may not realize a compliance obligation has been triggered.

## Complete Refrigerant Reference Table

The following table covers all 12 refrigerant types commonly encountered in HVAC and refrigeration field work, including the properties that matter for regulatory compliance.

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

- **GWP** is the 100-year Global Warming Potential from IPCC AR4 (the assessment report referenced by EPA regulations).
- **Safety Class** follows ASHRAE Standard 34 designations. The letter indicates toxicity (A = lower toxicity, B = higher toxicity). The number indicates flammability (1 = no flame propagation, 2L = lower flammability, 2 = flammable, 3 = higher flammability).
- **Charge Threshold** is the minimum system charge that triggers formal leak rate tracking and repair obligations under the applicable framework.

## The GWP > 53 Threshold Explained

The **GWP > 53 threshold** is the dividing line for Subpart C applicability. This number was not chosen arbitrarily. It was set to capture the HFC refrigerants that contribute meaningfully to climate warming while **excluding natural refrigerants** that have negligible warming impact.

In practical terms, every refrigerant you encounter in commercial and residential HVAC work — except for R-290 (propane), R-744 (CO2), and R-717 (ammonia) — has a GWP well above 53 and falls under either Section 608 or Subpart C.

The threshold matters most for **newer A2L refrigerants**. Technicians sometimes assume that because R-454B and R-32 are "low-GWP alternatives" promoted by equipment manufacturers, they might be exempt from Subpart C. They are not. R-454B has a GWP of 466 and R-32 has a GWP of 675 — both are far above the 53 threshold. **Low-GWP relative to R-410A does not mean low-GWP relative to the regulatory threshold.** These refrigerants carry the full Subpart C compliance obligation.

## Natural Refrigerant Exemptions

The three natural refrigerants — **R-290 (propane)**, **R-744 (CO2)**, and **R-717 (ammonia)** — all have GWP values at or below 3, placing them well below the Subpart C threshold. Systems using these refrigerants are **exempt from the formal leak rate tracking, repair deadline, and verification testing requirements** of both Section 608 and Subpart C.

However, this exemption is limited to the refrigerant management regulations. Natural refrigerants are still subject to:

- **ASHRAE 15 and applicable building codes** governing the safe use of flammable (R-290) and toxic (R-717) refrigerants.
- **UL 60335-2-89 and UL 60335-2-40** safety standards for equipment design and charge limits.
- **OSHA workplace safety requirements** for ammonia systems, including Process Safety Management (PSM) for systems containing 10,000 pounds or more.
- **General good practice** in refrigerant handling, recovery, and leak repair.

The exemption means you do not need to calculate annualized leak rates, track repair deadlines, or perform formal verification tests for natural refrigerant systems. It does not mean that leaks in these systems can be ignored — particularly given the flammability and toxicity considerations.

## How GWP Affects Daily Field Work

Understanding GWP values changes how you approach a service call in several concrete ways:

### Framework Identification

When you arrive at a job site and identify the refrigerant, the GWP immediately tells you:

1. **Which leak rate thresholds apply.** Subpart C sets the comfort cooling threshold at **10%** (vs. 15% under Section 608). A system leaking at 12% is compliant under Section 608 but non-compliant under Subpart C.
2. **Which charge threshold applies.** Section 608 requires leak rate tracking at **50 pounds** of charge. Subpart C drops this to **15 pounds** for HFCs with GWP > 53. A 20-pound R-410A system requires full compliance tracking; a 20-pound R-22 system does not.
3. **Which repair deadlines apply.** Both frameworks require 30-day repair for commercial and comfort cooling, but Subpart C adds specific timelines for transport refrigeration (not present in Section 608).

### Equipment Profile Setup

When creating an equipment profile for a system you will service repeatedly, the refrigerant's GWP determines which regulatory framework the system falls under. This classification affects every subsequent compliance record associated with that equipment. Getting it right at setup prevents cascading errors across all future service records.

### Transition Planning

As the industry transitions from high-GWP refrigerants like R-410A (GWP 2,088) to lower-GWP alternatives like R-454B (GWP 466), understanding GWP helps you advise customers on equipment replacement decisions. While both refrigerants fall under Subpart C, the AIM Act's production phase-down targets high-GWP refrigerants more aggressively, meaning R-410A availability and pricing will be increasingly affected in coming years.

## How FieldPad Uses GWP for Auto-Detection

**FieldPad** stores the GWP value, safety classification, ODS/HFC status, and category for all 12 supported refrigerant types as built-in properties. When you select a refrigerant type on a compliance log or equipment profile, FieldPad automatically:

- **Detects the regulatory framework** — Section 608 for ODS refrigerants, Subpart C for HFCs with GWP > 53, or exempt status for natural refrigerants.
- **Applies the correct leak rate thresholds** based on the detected framework and the appliance type (commercial, comfort cooling, industrial).
- **Sets the correct charge threshold** — 50 pounds for Section 608 or 15 pounds for Subpart C — determining whether formal leak rate tracking is required.
- **Flags A2L refrigerants** that require additional safety compliance documentation, including the A2L safety checklist on applicable compliance logs.

This automatic detection eliminates the need to memorize GWP values or manually determine which framework applies. You select the refrigerant, and FieldPad applies the correct rules. If a technician accidentally selects the wrong refrigerant type, the mismatch between the expected and actual framework often becomes apparent through threshold comparisons, providing a built-in error check.

## Key Takeaways

- **GWP (Global Warming Potential)** measures a refrigerant's heat-trapping ability relative to CO2 and is the key factor for regulatory framework classification.
- **GWP > 53** is the threshold for Subpart C applicability. All common HFC refrigerants (R-410A, R-407C, R-134a, R-404A, R-32, R-454B, R-452B, R-466A) exceed this threshold.
- **ODS refrigerants** like R-22 fall under Section 608 regardless of GWP, based on their ozone depletion potential.
- **Natural refrigerants** (R-290, R-744, R-717) have GWP values at or below 3 and are **exempt** from Subpart C leak repair requirements, though safety and building code obligations still apply.
- **A2L "low-GWP" refrigerants are not exempt.** R-454B (GWP 466) and R-32 (GWP 675) are well above the 53 threshold and carry full Subpart C compliance obligations.
- **Subpart C has stricter requirements** than Section 608 in key areas: lower charge threshold (15 lbs vs. 50 lbs) and lower comfort cooling leak rate threshold (10% vs. 15%).
- **FieldPad automatically detects the regulatory framework** based on the refrigerant's GWP value, applying the correct thresholds, charge minimums, and compliance rules without manual lookup.
