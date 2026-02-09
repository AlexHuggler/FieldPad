---
layout: article
title: "Verification Test Requirements: Initial and Follow-Up Leak Testing Under Section 608 and Subpart C"
description: "Understanding EPA requirements for initial and follow-up verification leak tests after refrigerant leak repairs, including timing requirements under both Section 608 and Subpart C frameworks."
date: 2026-02-08
category: compliance
author: "FieldPad Team"
read_time: 6
summary: "After repairing a refrigerant leak on regulated equipment, EPA regulations require technicians to perform verification tests to confirm the repair was successful. An initial verification test must be conducted immediately after the repair. A follow-up verification test must be completed within 30 days under Section 608, or within 10 days under Subpart C. If the follow-up test shows the leak persists, additional repairs and verification cycles are required. FieldPad tracks repair events, schedules verification test deadlines, and sends notification reminders before deadlines expire."
keywords: ["verification test", "leak test", "follow-up verification", "EPA repair requirements", "leak repair verification", "Section 608 testing", "Subpart C testing"]
---

## What Triggers the Verification Requirement

A verification test obligation is triggered whenever a **refrigerant leak repair** is performed on regulated equipment. Under Section 608, this applies to equipment containing **50 pounds or more** of refrigerant. Under AIM Act Subpart C, the threshold drops to **15 pounds or more** for systems with a GWP above 53.

The trigger is the **repair itself**. Once you braze a joint, replace a Schrader valve core, tighten a flare fitting, or perform any action intended to stop a refrigerant leak, you must verify the repair through a structured two-stage testing process.

This process exists because some repairs that appear successful immediately can fail under operating conditions. Thermal cycling, vibration, and pressure fluctuations can re-open a leak that seemed sealed during the initial test.

## Stage 1: Initial Verification Test

The initial test must be performed **at the time of the repair**, before you leave the job site. It confirms the repair is not actively leaking under current conditions.

**Acceptable test methods include:**

- **Pressure or vacuum standing test** on the repaired section, held long enough to detect leaks at the required sensitivity
- **Electronic leak detection** meeting regulation sensitivity requirements (typically 0.1 oz/year for Section 608, with enhanced requirements under Subpart C)
- **Ultrasonic leak detection** for pressurized systems where electronic detection is impractical
- **Soap bubble testing** at the repair location as a supplementary method (generally not accepted standalone for larger systems)

**What you must record:** date and time, test method, specific location tested, result (pass or fail), and any corrective action taken on failure.

## Stage 2: Follow-Up Verification Test

The follow-up test is conducted after the system has returned to normal operation and run through multiple operating cycles. It catches repairs that hold initially but fail under real-world conditions.

**The critical difference between frameworks is the deadline:**

| Regulatory Framework | Follow-Up Deadline |
|---|---|
| **Section 608** (ODS refrigerants, systems >= 50 lbs) | Within **30 days** of repair |
| **Subpart C** (HFC refrigerants, GWP > 53, systems >= 15 lbs) | Within **10 days** of repair |

The Subpart C timeline is significantly tighter, reflecting heightened concern around high-GWP HFC emissions. For solo technicians servicing mixed equipment fleets, tracking which deadline applies to which system is one of the more challenging compliance tasks.

### Why Deadlines Matter

Missing a follow-up deadline is a **standalone violation**, separate from the underlying leak. Even if the repair was successful and the system is no longer leaking, failing to perform and document the test within the required window is a citable offense carrying the same penalty authority as substantive violations.

## What Constitutes a Passing Test

A test passes when **no leak is detected at the repair location** using the specified method. For pressure-based tests, the system must hold test pressure within tolerances for the specified duration. For electronic detection, no alarm at the calibrated sensitivity level.

**Important distinctions:**

- The test covers the **repair location specifically**, not the entire system
- The follow-up should be conducted while the system is operating or has recently operated, so thermal cycling and vibration have stressed the repair
- **Detector calibration must be current** -- an uncalibrated detector can invalidate the result during an audit

## When Follow-Up Tests Fail

If the follow-up reveals the repair location is still leaking, the compliance clock resets:

1. **Perform an additional repair** to address the persistent leak
2. **Conduct a new initial verification test** at the time of re-repair
3. **Schedule a new follow-up test** within the applicable deadline from the re-repair date
4. **Recalculate the leak rate** if additional refrigerant was added

This cycle repeats until the follow-up passes or the equipment owner decides to **retrofit or retire** the equipment. Each failed cycle increases cumulative refrigerant additions, potentially pushing equipment toward **chronic leaker** classification at 125% of full charge.

## Section 608 vs. Subpart C: Full Comparison

| Requirement | Section 608 | Subpart C |
|---|---|---|
| **Charge threshold** | >= 50 lbs | >= 15 lbs (GWP > 53) |
| **Follow-up deadline** | 30 days | 10 days |
| **Detector sensitivity** | Standard 608 requirements | Enhanced for certain HFC blends |
| **A2L safety requirements** | Not applicable | Required for mildly flammable refrigerants |

### A2L Safety During Verification

When testing systems using **A2L refrigerants** (R-32, R-454B, etc.), additional precautions apply before any leak detection work:

- Verify refrigerant sensors in the equipment space are operational
- Confirm adequate ventilation in the work area
- Use detection equipment rated for flammable refrigerants
- Follow manufacturer A2L service procedures, including de-energizing ignition sources

These safety steps must be documented as part of the compliance record alongside the test itself.

## Documentation Requirements

Every verification test requires:

- **Date and time** of the test
- **Test type**: Initial or follow-up
- **Equipment identification**: Location, type, serial number
- **Repair being verified**: Reference to the repair record
- **Test method and equipment**: Make, model, serial number, and calibration date for detectors
- **Specific location tested**
- **Result**: Pass or fail
- **Technician identification**: Name and EPA certification number
- **If failed**: Failure description and reference to subsequent repair

All documentation must be retained for a minimum of **3 years**.

## Scheduling Challenges for Solo Technicians

The follow-up test requires returning to the same job site within 10 or 30 days. For technicians with busy schedules and geographically dispersed customers, this return visit is easy to forget or deprioritize.

**Practical strategies:**

- Schedule the return visit before leaving the initial repair site
- Group follow-up visits geographically to minimize drive time
- Prioritize Subpart C follow-ups due to the tighter 10-day window
- Use automated reminders that escalate as deadlines approach

## How FieldPad Tracks Verification Deadlines

FieldPad automates verification test management:

- **Automatic deadline calculation**: Logging a repair triggers framework detection from the equipment profile and calculates the correct follow-up deadline.
- **Escalating reminders**: Notifications at 7, 3, and 1 days remaining for Section 608; at 5, 2, and 1 days for Subpart C.
- **Compliance dashboard**: Pending tests appear with color-coded urgency. Overdue tests are flagged prominently.
- **Linked records**: Initial test, follow-up test, and repair record all link to the same equipment profile for a complete audit trail.
- **Failed test workflow**: Recording a failed follow-up prompts a new repair log and creates a fresh deadline automatically.
- **A2L integration**: For A2L refrigerant systems, the safety checklist is included in the verification workflow.

## Key Takeaways

- **Every leak repair triggers two-stage verification**: initial test at repair time plus follow-up within the applicable deadline.
- **Section 608 allows 30 days; Subpart C allows only 10.** Know which framework applies to each system.
- **Missing a deadline is a standalone violation**, independent of repair success.
- **Failed follow-ups restart the cycle**: new repair, new initial test, new deadline.
- **Document every test completely**, including method, equipment, result, and technician ID.
- **A2L systems require additional safety steps** that must also be documented.
- **FieldPad automates deadline tracking**, sends escalating reminders, and links all verification records for audit-ready compliance.
