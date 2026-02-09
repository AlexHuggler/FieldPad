# Functional Scope — FieldPad

> Audit date: 2026-02-08

---

## 1. Invoicing End-to-End

### Flow
1. Complete a Job (status -> `.completed`)
2. Create Invoice from `JobDetailView.swift:364` (checks `.unlimitedInvoices`)
3. Auto-generates invoice number: `PREFIX-YYYYMMDD-XXXX` (`Invoice.swift:168-177`)
4. Auto-adds labor line item from job's `laborHours * laborRate`
5. Add additional line items (labor / material / refrigerant / other categories)
6. Split tax applied (labor rate vs material rate)
7. Collect signature via `SignaturePadView` (PencilKit canvas -> PNG)
8. Generate PDF via `DataExportService` (PDFKit)
9. Share/Email via `MFMailComposeViewController` or share sheet
10. Record payment (partial or full) via `RecordPaymentSheet`
11. Auto-mark paid when `balanceDue <= 0`

### Invoice Number Generation (`Invoice.swift:168-177`)
```
Format: {prefix}-{YYYYMMDD}-{0001}
Default prefix: "INV" (configurable in Settings)
Sequence: auto-incrementing integer stored in AppSettings.nextInvoiceSequence
Example: INV-20260203-0001
```

### Tax Formulas (`Invoice.swift:97-133`)

| Calculation | Formula | Source |
|---|---|---|
| Labor subtotal | `lineItems.filter { $0.category.isLaborType }.reduce(0) { $0 + $1.total }` | `:101` |
| Material subtotal | `lineItems.filter { !$0.category.isLaborType }.reduce(0) { $0 + $1.total }` | `:105` |
| Labor tax | `laborSubtotal * taxRateLabor / 100` | `:109` |
| Material tax | `materialSubtotal * taxRateMaterial / 100` | `:113` |
| Total tax (split) | `laborTax + materialTax` (when `taxRateMaterial > 0`) | `:118` |
| Total tax (single) | `subtotal * taxRate / 100` (fallback when no material rate) | `:120` |
| Total | `subtotal + taxAmount` | `:125` |
| Balance due | `total - amountPaid` | `:93` |

### Line Item Total (`InvoiceLineItem.swift:57-59`)
```
total = quantity * unitPrice
```

### Categories
- **Labor types**: Labor, Service, Diagnostic (`isLaborType = true`)
- **Non-labor**: Material, Refrigerant, Other
- Refrigerant items track `refrigerantType` and `poundsUsed`

### Payment Recording (`RecordPaymentSheet.swift:106-131`)
1. `invoice.amountPaid += amount`
2. If `balanceDue <= 0`: `invoice.markPaid()`, status -> `.paid`
3. Payment note auto-appended to `invoice.notes`
4. Entity marked for sync via `markUpdated()`

### Status Workflow (`Invoice.swift:180-222`)
`draft` -> `sent` -> `viewed` -> `paid` (or `overdue` / `void`)

---

## 2. EPA Compliance Tracking

### ComplianceLog Model (`Models/ComplianceLog.swift`)

**Action Types** (`:115-155`):

| Action | Requires |
|---|---|
| Recovery | `poundsRecovered` |
| Recharge | `poundsRecharged` |
| Leak Test | — |
| Leak Repair | — |
| Installation | `poundsRecharged` |
| Decommission | `poundsRecovered` |
| Initial Verification | — |
| Follow-up Verification | — |
| Quarterly Inspection | — |
| Annual Inspection | — |
| Retrofit | `poundsRecovered` |
| Retirement | `poundsRecovered` |
| Seasonal Variance | — |

**A2L Safety Checklist** (`:20-30, :94-103`):
- `a2lDissipationConfirmed` — confirmed refrigerant dissipation risk
- `sparkFreeToolsUsed` — spark-free tools verified
- `ventilationVerified` — adequate ventilation confirmed
- `ppeVerified` — personal protective equipment verified
- `a2lLeakDetectorUsed` — A2L-rated leak detector used
- `equipmentGrounded` — equipment properly grounded
- `emergencyPlanReviewed` — emergency plan reviewed (optional)
- `a2lChecklistComplete` requires **6 mandatory** booleans all `true` (`emergencyPlanReviewed` is optional and not required for completeness)

**Computed Properties** (`:99-107`):

| Property | Formula |
|---|---|
| Net refrigerant change | `poundsRecharged - poundsRecovered` |
| Leak rate % | `(poundsRecovered / systemCapacity) * 100` (screening method) |

**Export**: CSV, JSON, or PDF via `SettingsView.swift:1069` (Pro feature)

---

## 3. Leak Rate Calculator

**Source**: `Services/Compliance/LeakRateCalculator.swift`

### EPA Section 608 Thresholds (`:9-16`)

| System Type | Threshold |
|---|---|
| Commercial Refrigeration | 20% |
| Comfort Cooling (AC) | 15% |
| Industrial Process | 30% |

### EPA Subpart C (AIM Act) Thresholds (`:18-33`)

| System Type | Threshold |
|---|---|
| Comfort Cooling | 10% |
| Commercial Refrigeration | 20% |
| Industrial Process | 30% |
| Refrigerated Transport | 10% |
| Other | 10% |

### Charge Thresholds for Regulatory Applicability (`RegulatoryFramework.swift:22-27`)

| Framework | Minimum Charge (lbs) |
|---|---|
| Section 608 (ODS) | 50 lbs |
| Subpart C (HFC/AIM Act) | 15 lbs |

### Repair Deadlines (`:128-140`)

| Framework | Appliance Type | Deadline |
|---|---|---|
| Section 608 | Industrial Process | 120 days |
| Section 608 | All others | 30 days |
| Subpart C | All types | 30 days |

### Calculation Methods

**Method 1 — Annualizing** (`:146-186`):
```
rate = (refrigerant_added / system_capacity) * (365 / days_since_last_charge) * 100
```
- Accounts for time elapsed since last charge
- Default `days_since_last_charge`: 365 if not specified
- More accurate for recent repairs

**Method 2 — Screening** (`:190-229`):
```
rate = (refrigerant_added / system_capacity) * 100
```
- Simple initial assessment
- Does not factor in time

### Severity Levels (`:113-139`)

| Severity | Condition |
|---|---|
| Normal | `rate < threshold * 0.8` |
| Warning | `rate >= threshold * 0.8 AND rate < threshold` |
| Elevated | `rate >= threshold AND rate < threshold * 2` |
| Critical | `rate >= threshold * 2` |

### Repair Deadline (`:128-140`)
- **30 days** default for non-compliant systems
- **120 days** for Industrial Process under Section 608
- Deadline varies by appliance type and regulatory framework (see `repairDeadlineDays(for:framework:)`)

### Result Structure (`:169-239`)
```swift
struct LeakRateResult {
    let rate: Double
    let method: CalculationMethod
    let systemType: SystemType
    let threshold: Double
    let isCompliant: Bool          // rate <= threshold
    let requiresRepair: Bool       // !isCompliant
    let repairDeadlineDays: Int?   // 30 or 120 depending on type/framework
    let framework: RegulatoryFramework  // .section608 or .subpartC
}
```

---

## 4. 5,000 Rule

**Source**: `Views/Tools/FiveThousandRuleView.swift`

### Formula (`:12-18`)
```
result = unitAge * repairCost
```

| Result | Recommendation |
|---|---|
| `<= 5,000` | **REPAIR** the unit (green) |
| `> 5,000` | **REPLACE** the unit (red) |

### Input Ranges (`:182-200`)
- Unit Age: 1–20 years (1-year increments)
- Repair Cost: $100–$1,000 ($50 increments)

### Visualization (`:88-160`)
- Interactive chart with green (repair) and red (replace) zones
- Threshold line at 5,000
- Current decision point plotted as interactive marker

---

## 5. Cylinder Weight Tracking

**Source**: `Models/RefrigerantCylinder.swift`

### Weight Formulas

| Property | Formula | Lines |
|---|---|---|
| Heel weight | `max(0, currentWeight - tareWeight)` | `:92-94` |
| Initial charge | `grossWeight - tareWeight` | set at creation |
| Fill percentage | `(heelWeight / initialCharge) * 100` | `:97-100` |

### Status Thresholds (`:103-125`)

| Status | Fill % Range | Color |
|---|---|---|
| Good | >= 50% | Green |
| Moderate | 20%–49% | Yellow |
| Low | 10%–19% | Orange |
| Critical | < 10% | Red |

### Weight Update Methods (`:182-197`)

**`updateWeight(_ newWeight:)`**: Sets `currentWeight`, updates `lastWeighDate`

**`recordUsage(pounds:)`**: Decrements weight with floor at tare weight:
```swift
currentWeight = max(tareWeight, currentWeight - pounds)
```

### Exchange Tracking (`:22-26, :158-180`)
- `replacedByIdString` / `replacesIdString`: link exchanged cylinders
- `exchangeDate`, `exchangeNotes`: record exchange metadata
- `wasExchanged: Bool` / `isReplacement: Bool`: computed properties

---

## 6. iCloud Sync (Active — SwiftData+CloudKit)

> **Current State (2026-02-08):** iCloud sync is **active** via SwiftData+CloudKit automatic sync. The SwiftData `ModelContainer` is configured with `cloudKitDatabase: .private("iCloud.com.hugglerholdings.fieldpad")`. Entitlements include CloudKit, APS (`aps-environment: development`), and iCloud container (`iCloud.com.hugglerholdings.fieldpad`).

### How Sync Works

SwiftData+CloudKit provides automatic, transparent sync:
- All SwiftData model changes are automatically synced to the user's private CloudKit database
- Sync runs in the background whenever the device has connectivity
- No manual sync operations are needed — SwiftData handles conflict resolution
- `ICloudService` handles CloudKit account status checking and remote notification registration

### SyncEngine (`Services/Sync/SyncEngine.swift`)

The legacy SyncEngine infrastructure remains in code and continues to create `SyncRecord` entries for local change tracking. Views still call `markXForSync()` but these are effectively no-ops — actual sync is handled by SwiftData+CloudKit automatically.

**Network Monitoring** via `NetworkMonitor.swift`:
- `NWPathMonitor` on dedicated dispatch queue
- Detects: WiFi, Cellular, Ethernet, Unknown
- Used for UI status display

### Sync UI Controls (Pro-Gated)

Sync UI controls in Settings (Sync Now button, Last Synced timestamp, Auto-Sync toggle) are gated behind the `.icloudSync` ProFeature. However, the underlying SwiftData+CloudKit sync runs for all users regardless of subscription tier.

### Entitlements (`FieldPad.entitlements`)
- `com.apple.developer.in-app-payments` — Active
- `aps-environment: development` — Active (CloudKit sync notifications)
- iCloud Containers: `iCloud.com.hugglerholdings.fieldpad` — Active
- CloudKit: Development — Active

---

## 7. Recurring Jobs

**Source**: `Models/Job.swift:160-183`, `Models/RecurrencePattern.swift`

### Recurrence Patterns

| Pattern | Calendar Addition |
|---|---|
| Daily | +1 day |
| Weekly | +1 week |
| Biweekly | +2 weeks |
| Monthly | +1 month |
| Quarterly | +3 months |

### Next Occurrence Generation (`Job.swift:160-183`)
1. Base date = `scheduledDate ?? completedDate ?? Date()`
2. Next date = `pattern.nextDate(from: baseDate)`
3. New Job created with same title, description, type, address, notes, labor config
4. Status set to `.scheduled`
5. `parentJobId` links back to original job for traceability
6. `isRecurring = true` and `recurrencePattern` preserved

---

## 8. Edge Cases to Test

### 8.1 Technician loses signal mid-invoice creation
- **Expected**: Data persists locally in SwiftData. SyncRecord queued with status `.pending`. On reconnect, SyncEngine auto-triggers sync.
- **Source**: `SyncEngine.swift` auto-sync on `NWPathMonitor` reconnect

### 8.2 Cylinder weight update during compliance log
- **Expected**: `recordUsage(pounds:)` floors weight at tare weight (`max(tareWeight, currentWeight - pounds)`)
- **Source**: `RefrigerantCylinder.swift:189`

### 8.3 A2L refrigerant on invoice without completing safety checklist
- **Expected**: Blocked. `a2lChecklistComplete` must be `true` for A2L refrigerants. `A2LComplianceModal` enforces all 6 mandatory booleans before allowing submission.
- **Source**: `ComplianceLog.swift:94-97`, `A2LComplianceModal.swift`

### 8.4 Invoice PDF generation with no line items
- **Expected**: Valid PDF generated with $0 subtotal, $0 tax, $0 total. All structural elements (header, FROM/BILL TO, footer) still render.
- **Source**: `DataExportService.swift` PDF generation

### 8.5 Recurring job completed -> next occurrence auto-generated
- **Expected**: `generateNextOccurrence()` creates new Job with correct next date based on recurrence pattern. Parent-child link via `parentJobId`.
- **Source**: `Job.swift:160-183`

---

## 9. Estimates

**Source**: `Models/Estimate.swift`, `Views/Estimates/`

### Status Workflow
`draft` → `sent` → `accepted` / `declined` / `expired`

### Estimate Number Generation
```
Format: EST-{YYYYMMDD}-{XXXX}
Sequence: auto-incrementing via AppSettings.nextEstimateSequence
```

### Computed Totals
| Property | Formula |
|---|---|
| Subtotal | Sum of `lineItems.quantity * lineItems.unitPrice` |
| Tax | `subtotal * taxRate / 100` |
| Total | `subtotal + taxAmount` |
| `isExpired` | `expirationDate < Date()` and status != accepted |

### Signature Support
- `signatureData: Data?` (PNG blob from PencilKit)
- `signedByName: String?`, `signedDate: Date?`
- `isSigned: Bool` computed from `signatureData != nil`

### Sharing Limits
- Free tier: 10 lifetime estimate shares (`AppSettings.estimateShareCount`)
- Pro: unlimited

---

## 10. Equipment Profile Management

**Source**: `Models/EquipmentProfile.swift`, `Views/Equipment/`

### Model Fields
- `name`, `location`, `ownerName`, `operatorName`
- `applianceType` (ApplianceType enum: Commercial, Comfort Cooling, Industrial, Refrigerated Transport, Other)
- `refrigerantType` (RefrigerantType enum)
- `fullChargeAmount` (Decimal), `fullChargeMethod` (Nameplate / Calculation / Field Measurement)
- `manufacturerName`, `modelNumber`, `serialNumber`
- `installDate`, `retirementDate`
- `regulatoryFramework` (Section 608 / Subpart C)
- `hasAutoLeakDetection: Bool`

### Relationships
- `client: Client?`
- `complianceLogs: [ComplianceLog]?`
- `leakRateRecords: [LeakRateRecord]?`
- `inspectionSchedules: [InspectionSchedule]?`
- `retrofitRetirementPlans: [RetrofitRetirementPlan]?`
- `chronicLeakerReports: [ChronicLeakerReport]?`

### Net Refrigerant Summary
- Aggregates `poundsRecharged` and `poundsRecovered` across all linked compliance logs
- Displayed on `EquipmentDetailView` between Rolling 12-Month and Service History sections
- **Total Added** (blue): sum of `poundsRecharged` across all recharge logs
- **Total Recovered** (green): sum of `poundsRecovered` across all recovery logs
- **Net Change**: Total Added − Total Recovered; positive values shown in orange, negative in green
- Empty state: "No refrigerant transactions recorded" when equipment has no compliance logs with weight data
- Weight formatting uses `FieldPadFormatters.weight()`

### Computed Properties
- `leakRateThreshold` — from `LeakRateCalculator.threshold(for:framework:)`
- `meetsChargeThreshold` — checks against framework minimum charge (608: 50 lbs, Subpart C: 15 lbs)
- `isRetired` — `retirementDate != nil`

### Regulatory Framework Assignment
- ODS refrigerants (R-22) → Section 608
- HFC refrigerants (R-410A, R-407C, R-134a, R-404A) → Subpart C (AIM Act)
- A2L refrigerants (R-454B, R-32, R-452B) → Subpart C
- Natural refrigerants (R-290, R-744, R-717) → Subpart C (assigned framework, but **exempt** via `isSubpartCExempt` since GWP ≤ 53)

---

## 11. Inspection Schedules

**Source**: `Models/InspectionSchedule.swift`, `Views/Compliance/InspectionScheduleView.swift`

### Frequencies
| Frequency | Days Between |
|---|---|
| Monthly | 30 |
| Quarterly | 90 |
| Semi-annual | 180 |
| Annual | 365 |

### Escalation Logic (`ComplianceEngine.escalateInspectionFrequency`)
- When leak detected or threshold exceeded: frequency increases one level
- annual → semiannual → quarterly → monthly
- `escalationReason` recorded

### De-escalation Logic (`ComplianceEngine.deescalateInspectionFrequency`)
- After consecutive compliant quarters (`consecutiveCompliantQuarters`)
- Frequency decreases one level toward `baseFrequency`
- Cannot go below base frequency

### Computed Properties
- `isOverdue: Bool` — `nextInspectionDue < Date()`
- `daysUntilDue: Int?` — days from now until next inspection
- `scheduleNext()` — advances `nextInspectionDue` by current frequency

---

## 12. Chronic Leaker Reporting

**Source**: `Models/ChronicLeakerReport.swift`, `Services/Compliance/EPAReportGenerator.swift`

### Threshold
- Equipment exceeding **125%** of full charge in cumulative additions per calendar year
- Formula: `(cumulativeAdditions / fullChargeAmount) * 100`
- When `calculatedPercentage > 125.0` → chronic leaker

### Report Fields
- `facilityName`, `facilityAddress`, `applianceDescription`
- `fullChargeAmount`, `cumulativeAdditions`, `calculatedPercentage`
- `repairEfforts` (text summary)
- `calendarYear` (defaults to current year)

### Submission Tracking
- `isSubmitted: Bool`, `submissionDate: Date?`
- `submissionMethod: String?`, `submissionReference: String?`

### Generation
`EPAReportGenerator.generateChronicLeakerReport(for:year:modelContext:)` aggregates compliance logs for the equipment in the given year.

---

## 13. Verification Tests

**Source**: `Models/VerificationTest.swift`, `Views/Compliance/VerificationTestFormView.swift`

### Test Types
| Type | Description |
|---|---|
| Initial | First verification after repair |
| Follow-up | Subsequent verification test |

### Test Results
| Result | Icon |
|---|---|
| Passed | checkmark.circle.fill |
| Failed | xmark.circle.fill |

### Fields
- `linkedRepairId` — UUID linking to original ComplianceLog repair
- `testDate`, `testMethod`, `locationsTested`
- `technicianName`, `technicianCertNumber`

### Follow-Up Deadline Warning
- When test type is `.followup`, the form displays an orange warning if the test date exceeds the regulatory deadline window
- **Subpart C**: 10-day follow-up window from initial test date
- **Section 608**: 30-day follow-up window from initial test date
- Warning shows the calculated deadline date
- No warning for `.initial` test type regardless of date

---

## 14. Repair Extensions

**Source**: `Models/RepairExtension.swift`, `Views/Compliance/RepairExtensionFormView.swift`

### Extension Types
- Parts (unavailable parts)
- Weather (adverse conditions)
- Industrial (industrial process exemption)
- Other

### Deadline Management
- `originalDeadline: Date` — original EPA repair deadline
- `requestedDeadline: Date` — proposed new deadline
- `isExpired: Bool` — `requestedDeadline < Date()`
- `daysRemaining: Int?` — days until deadline

### EPA Notification Tracking
- `epaNotified: Bool`, `epaNotificationDate: Date?`
- `epaResponseReceived: Bool`, `epaApproved: Bool`
- `epaResponseDate: Date?`, `epaResponseNotes: String?`

---

## 15. Retrofit/Retirement Plans

**Source**: `Models/RetrofitRetirementPlan.swift`, `Views/Compliance/RetrofitRetirementFormView.swift`

### Plan Types
| Type | Description |
|---|---|
| Retrofit | Convert equipment to new refrigerant |
| Retirement | Permanently decommission equipment |

### 1-Year Deadline
- `oneYearDeadline` auto-calculated from `triggeringLeakDate + 365 days`
- `isOverdue: Bool` — deadline passed and `!isCompleted`
- `daysRemaining: Int?` — days until deadline

### Retrofit Fields
- `targetRefrigerantType` — the new refrigerant to convert to
- `retrofitDescription` — scope of work

### Retirement Fields
- `retirementReason`, `disposalMethod`

### Completion
- `estimatedCompletionDate`, `actualCompletionDate`
- `isCompleted: Bool`, `milestoneNotes: String?`

---

## 16. Refrigerant Disposition

**Source**: `Models/RefrigerantDisposition.swift`, `Views/Compliance/RefrigerantDispositionFormView.swift`

### Disposition Types
| Type | Description |
|---|---|
| Reclaimed | Sent to EPA-certified reclaimer for AHRI 700 reprocessing |
| Recycled | Cleaned using recovery/recycling equipment for reuse |
| Destroyed | Sent to approved destruction facility |
| Returned to Manufacturer | Returned to refrigerant manufacturer |
| Stored On-Site | Stored in approved recovery cylinder |

### Chain of Custody Fields
- `recipientName`, `recipientAddress`, `recipientCertification`
- `manifestNumber` — shipping manifest tracking number
- `linkedServiceId` — UUID linking to originating ComplianceLog

### Tracked Data
- `dispositionType`, `quantity` (Decimal), `refrigerantType`
- `dispositionDate`, `notes`

---

## 17. Technician Profiles

**Source**: `Models/TechnicianProfile.swift`, `Views/Settings/TechnicianProfilesView.swift`

### Certification Types
| Type | Description |
|---|---|
| Type I | Small appliances (5 lbs or less) |
| Type II | High-pressure or very high-pressure appliances |
| Type III | Low-pressure appliances |
| Universal | All types of equipment |

### Fields
- `name`, `certNumber`, `certType` (CertificationType)
- `certOrganization`, `certDate`
- `cardOnFile: Bool` — EPA card on file
- `isDefault: Bool` — default technician for auto-fill

### Auto-Fill Behavior
- Default technician's name and cert number auto-fill:
  - `ComplianceLogFormView` technician fields
  - `A2LComplianceModal` technician fields
  - `VerificationTestFormView` technician fields
- Settings also stores `technicianName` and `epaCertNumber` in `AppSettings` for backward compat

---

## 18. Compliance Dashboard

**Source**: `Views/Compliance/ComplianceDashboardView.swift`, `ComplianceDashboardContentView.swift`

### Alert Categories

**Critical Alerts (red):**
- Overdue repairs — unrepaired leaks past repair deadline
- Overdue inspections — inspections past `nextInspectionDue`

**Warnings (orange):**
- Inspections due within 7 days
- Pending verification tests (first 5 shown)

### Stats Grid (2-column LazyVGrid)
| Stat | Links To |
|---|---|
| Equipment count | EquipmentListContentView |
| Overdue repairs | OverdueRepairsView |
| Due inspections | InspectionScheduleView |
| Pending tests | PendingVerificationsView |

### HelpTooltips on Stats
- **Overdue Repairs** stat card includes a HelpTooltip explaining repair deadline differences between Section 608 (30 days, 120 days for industrial) and Subpart C (30 days for all types)
- **Pending Tests** stat card includes a HelpTooltip explaining verification test window differences (Section 608: 30 days, Subpart C: 10 days)

### Actions
- Chronic Leaker Report — generates via `EPAReportGenerator`
- Audit Export — exports compliance history as CSV

### Recent Activity
- Last 10 compliance logs with action type, refrigerant, date, leak status

---

## 19. ComplianceEngine Service

**Source**: `Services/Compliance/ComplianceEngine.swift`

### Key Methods

| Method | Returns | Description |
|---|---|---|
| `calculateAndStoreLeakRate(for:refrigerantAdded:daysSinceLastCharge:method:)` | `LeakRateRecord?` | Calculates and persists leak rate |
| `calculateAndStoreLeakRate(for:refrigerantAdded:daysSinceLastCharge:method:triggeringLog:)` | `LeakRateRecord?` | Exemption-aware overload — returns nil (skips calculation) for retrofit, new installation, or seasonal variance logs per 40 CFR 82.156/84.106 |
| `checkChronicLeaker(for:year:)` | `Bool` | Checks 125% threshold |
| `getRepairDeadline(for:)` | `Int` | Returns deadline days for equipment |
| `getUpcomingInspections(within:)` | `[InspectionSchedule]` | Inspections due within N days |
| `getOverdueRepairs()` | `[ComplianceLog]` | Unrepaired leaks past deadline |
| `getPendingVerifications()` | `[ComplianceLog]` | Repairs needing verification tests |
| `escalateInspectionFrequency(for:)` | `Void` | Increases inspection frequency |
| `deescalateInspectionFrequency(for:)` | `Void` | Decreases after compliant quarters |

### Dashboard Properties
- `overdueRepairCount`, `urgentInspectionCount`, `pendingVerificationCount`
- `totalAlertCount` = sum of above

---

## 20. ComplianceDeletionGuard

**Source**: `Services/ComplianceDeletionGuard.swift`

### Purpose

Centralized retention-lock enforcement for EPA compliance logs. Routes delete requests to archive (if within retention period) or permanent delete (if retention expired).

### Methods

| Method | Returns | Description |
|---|---|---|
| `attemptDelete(_:context:)` | `DeletionResult` | Archives if within retention period, deletes if expired |
| `deleteAllDeletable(context:)` | `Int` | Bulk operation — deletes expired logs, archives retention-locked logs; returns count of retained (archived) logs |

### Referenced By
- `JobDetailView` — compliance log delete action
- `SettingsView` — Delete All Data flow

---

## 21. Notification System

**Source**: `Services/Notifications/NotificationManager.swift`

### Notification Types (12)

| Type | Trigger | Timing | Deep Link |
|---|---|---|---|
| Money Leak | Job completed without invoice | 4 hours after completion | `.job(id)` |
| EPA Shield | Job started without compliance log | 2 hours after start | `.job(id)` |
| Overdue Revenue | Invoice due date reached | 9 AM on due date | `.invoice(id)` |
| Morning Brief | Jobs scheduled today | 30 min before first job | `.dashboard` |
| Recurring Job | Recurring job coming due | Day before due date | `.job(id)` |
| A2L Safety | A2L refrigerant job starting | Time-sensitive, before start | `.job(id)` |
| Low Inventory | Cylinder below threshold | 24hr cooldown, escalation for critical | `.cylinder(id)` |
| Chronic Leaker Deadline | Equipment with 125%+ additions approaching March 1 deadline | February 15 (2 weeks before deadline) | `.compliance` |
| Chronic Leaker Early Warning | Equipment approaching 125% threshold | February 1 (4 weeks before deadline) | `.compliance` |
| Chronic Leaker Overdue | Equipment past March 1 deadline without submission | After March 1 | `.compliance` |
| Initial Verification Deadline | Unverified repairs approaching deadline | Before verification deadline | `.compliance` |
| Verification Follow-Up | Pending follow-up verification tests | Before follow-up deadline | `.compliance` |

### Permission Flow
1. `requestSmartPermissions()` — shows soft-ask pre-prompt
2. User accepts → `requestAuthorization()` → system permission dialog
3. User declines soft-ask → no system prompt shown

### Work Hours
- Low inventory alerts respect work hours schedule
- Outside hours → scheduled for next work hours morning

---

## 21. Deep Linking

**Source**: `Services/Notifications/NotificationDelegate.swift`

### Destinations
```swift
enum DeepLinkDestination {
    case job(id)
    case invoice(id)
    case cylinder(id)
    case dashboard
    case compliance
}
```

### Notification Type → Destination Mapping (13 cases)
| Notification Type ID | Destination |
|---|---|
| `moneyLeak` | `.job(id)` |
| `epaShield` | `.job(id)` |
| `lowInventory` | `.cylinder(id)` |
| `overdueRevenue` | `.invoice(id)` |
| `morningBrief` | `.dashboard` |
| `a2lSafety` | `.job(id)` |
| `complianceAlert` | `.compliance` |
| `recurringJob` | `.job(id)` |
| `chronicLeakerDeadline` | `.compliance` |
| `chronicLeakerEarlyWarning` | `.compliance` |
| `chronicLeakerOverdue` | `.compliance` |
| `initialVerificationDeadline` | `.compliance` |
| `verificationFollowUp` | `.compliance` |

### Router
- `DeepLinkRouter.shared.pendingDestination` set by `NotificationDelegate`
- `MainTabView` observes and navigates accordingly

---

## 22. Schedule Conflict Detection

**Source**: `Services/ScheduleConflictDetector.swift`

### Overlap Logic
```
conflict exists when: start1 < end2 AND start2 < end1
```

### Method
`findConflicts(for startDate:, duration:, endDate:, excluding jobId:, in context:) -> [ScheduleConflict]`

- Fetches scheduled/in-progress jobs (limit: 200)
- Calculates end from `duration` minutes or explicit `endDate`
- Returns `ScheduleConflict` structs with `existingJob` and `overlapMinutes`
- Excludes specified job ID (for edit mode)

---

## 23. Form Draft Service

**Source**: `Services/FormDraftService.swift`

### Storage
- UserDefaults-backed with `"formDraft."` key prefix
- Namespaced per form type (e.g., `"formDraft.jobForm.title"`)

### Methods
| Method | Description |
|---|---|
| `get(_ key: String) -> String` | Retrieve draft value |
| `set(_ key: String, value: String)` | Store draft; removes if empty |
| `clearDrafts(for namespace: String)` | Clear all drafts for a form |

### Behavior
- Only used in `.create` mode (edit mode pre-populates from record)
- Drafts cleared on successful save

---

## 24. Backup Service

**Source**: `Services/BackupService.swift`

### Auto-Backup
- Configurable via `AppSettings.autoBackupEnabled` (default: enabled)
- Frequency: `AppSettings.backupFrequencyDays` (default: 7 days)
- `checkAndPerformBackup(modelContext:)` — checks if due, performs if needed

### Rotation
- Maximum retained backups: **5**
- `rotateBackups()` deletes oldest when exceeding max
- Backups stored in `Documents/Backups/` directory

### Backup Info
```swift
struct BackupInfo: Identifiable {
    let url: URL
    let date: Date
    let sizeBytes: Int64
    var formattedSize: String  // computed
}
```

### Methods
| Method | Description |
|---|---|
| `performBackup(modelContext:)` | Execute backup now (JSON export) |
| `listBackups() -> [BackupInfo]` | List backups sorted by date |
| `deleteBackup(_ backup:)` | Remove specific backup |

---

## 25. Review Request Service

**Source**: `Services/ReviewRequestService.swift`

### Triggers
| Trigger | When |
|---|---|
| `invoiceEmailed` | After emailing an invoice |
| `dataExported` | After exporting data |
| `cylinderScanMilestone` | After scanning milestone |
| `jobMilestone` | After job milestone |

### Guardrails
| Rule | Value |
|---|---|
| Minimum sessions | 3 before first request |
| Cooldown | 60 days between requests |
| Max per year | 3 requests per calendar year |
| Delay | 1.5 seconds before showing prompt |

---

## Summary of Key Formulas

| Calculation | Formula | File:Line |
|---|---|---|
| Invoice total | `subtotal + taxAmount` | `Invoice.swift:125` |
| Labor tax | `laborSubtotal * taxRateLabor / 100` | `Invoice.swift:109` |
| Material tax | `materialSubtotal * taxRateMaterial / 100` | `Invoice.swift:113` |
| Balance due | `total - amountPaid` | `Invoice.swift:93` |
| Line item total | `quantity * unitPrice` | `InvoiceLineItem.swift:57` |
| Heel weight | `max(0, currentWeight - tareWeight)` | `RefrigerantCylinder.swift:92` |
| Fill % | `(heelWeight / initialCharge) * 100` | `RefrigerantCylinder.swift:97` |
| Net change | `poundsRecharged - poundsRecovered` | `ComplianceLog.swift:99` |
| Leak rate (screening) | `(recovered / capacity) * 100` | `ComplianceLog.swift:103` |
| Leak rate (annualizing) | `(added / capacity) * (365 / days) * 100` | `LeakRateCalculator.swift:146` |
| 5,000 Rule | `unitAge * repairCost` | `FiveThousandRuleView.swift:12` |
