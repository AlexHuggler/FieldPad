# Customer Journeys — FieldPad

> Audit date: 2026-02-08

---

## Journey 1: First Launch / Onboarding

**Source**: `Views/Onboarding/OnboardingView.swift`, `FieldPadApp.swift`

```
App Launch
│
├── @AppStorage("hasCompletedOnboarding") == false?
│   │
│   YES → OnboardingView (5-page carousel)
│   │   ├── Page 1: Welcome
│   │   │   "Your HVAC Service Companion"
│   │   │   icon: wrench.and.screwdriver.fill (blue)
│   │   │
│   │   ├── Page 2: Track Jobs
│   │   │   "Schedule, track, and complete service jobs"
│   │   │   icon: calendar.badge.clock (green)
│   │   │
│   │   ├── Page 3: EPA Compliance
│   │   │   "Built-in Section 608 compliance tracking"
│   │   │   icon: checkmark.seal.fill (orange)
│   │   │
│   │   ├── Page 4: Invoice & Get Paid
│   │   │   "Professional invoices with digital signatures"
│   │   │   icon: dollarsign.circle.fill (purple)
│   │   │
│   │   └── Page 5: Your Info (optional)
│   │       ├── Technician Name (GloveTextField)
│   │       ├── EPA Cert Number (GloveTextField)
│   │       ├── [Get Started] → saves to AppSettings → MainTabView
│   │       └── [Skip] → MainTabView (no info saved)
│   │
│   └── hasCompletedOnboarding = true
│
└── YES (returning user) → MainTabView directly
```

**Key decisions**: Technician info is optional. Skip saves no data. Info can be added later in Settings.

---

## Journey 2: Job Lifecycle

**Source**: `Models/Job.swift`, `Views/Jobs/`, `Views/Invoicing/`

```
Create Job
│   ├── From: JobListView [+] button
│   ├── From: ClientDetailView [Create Job]
│   ├── From: FAB → "New Job"
│   └── Opens: JobFormView (create mode)
│       Required: title
│       Optional: client, date/time, duration, address, labor, notes
│
│   ── [Save] ──→ Job.status = .scheduled
│
Schedule Phase
│   Job appears in:
│   ├── JobListView (Active filter)
│   ├── ScheduleView (calendar timeline)
│   └── DashboardView ("Today's Jobs" if scheduled today)
│
│   ── [Start Job] ──→ Job.status = .inProgress
│
In Progress Phase
│   ├── ComplianceLog can be added (toolbar menu)
│   ├── Notes can be updated
│   │
│   ── [Complete Job] (SwipeToConfirmButton) ──→
│       Job.status = .completed
│       Job.completedDate = Date()
│       │
│       ├── NotificationManager: scheduleMoneyLeakNotification (4hr)
│       └── If isRecurring: generateNextOccurrence()
│           └── New Job with .scheduled status, parentJobId linked
│
Completed Phase
│   ├── [Create Invoice] button appears
│   │   └── Opens InvoiceFormView (createFromJob mode)
│   │       ├── Auto-generates invoice number
│   │       ├── Auto-adds labor line item (hours × rate)
│   │       ├── Pre-fills client from job
│   │       └── Job.status → .invoiced on save
│   │
│   └── If no invoice created within 4hr → Money Leak notification
│
Invoiced Phase
│   └── See Journey 2b: Invoice Payment below
│
Alternative: Cancelled
│   Job.status = .cancelled (from any pre-invoiced state)
```

### Journey 2b: Invoice Payment

```
Invoice Created (.draft)
│
├── Add line items → LineItemFormView
│   ├── If A2L refrigerant → A2LComplianceModal (mandatory)
│   │   └── Requires: cylinder selection, technician, EPA cert,
│   │       6 safety checks (Dissipation Confirmed, Spark-Free Tools Used,
│   │       Ventilation Verified, PPE Verified, A2L Leak Detector Used,
│   │       Equipment Grounded) → creates ComplianceLog
│   └── Save line item
│
├── [Share PDF] → DataExportService generates PDF → Share sheet
│   └── Increments AppSettings.invoiceShareCount
│       └── If count >= 5 (free): InvoiceSharingLimitSheet
│
├── [Email Invoice] → MFMailComposeViewController with PDF
│   └── Invoice.status → .sent, Invoice.sentDate = Date()
│
├── [Collect Signature] → InvoiceSignatureSheet
│   ├── PencilKit canvas (3pt black ink)
│   ├── Signer name + date
│   └── Saves signatureData (PNG), signedByName, signedDate
│
├── [Record Payment] → RecordPaymentSheet
│   ├── invoice.amountPaid += amount
│   ├── Payment note appended
│   └── If balanceDue <= 0:
│       ├── invoice.markPaid() → status = .paid
│       └── invoice.paidDate = Date()
│
└── Overdue: dueDate < Date() && !isPaid → status = .overdue
    └── NotificationManager: scheduleOverdueRevenueNotification
```

---

## Journey 3: Compliance Log Workflow

**Source**: `Models/ComplianceLog.swift`, `Views/Jobs/ComplianceLogFormView.swift`

```
Open ComplianceLogFormView
│   ├── From: JobDetailView toolbar → "Add Compliance Log"
│   ├── From: FAB → "Compliance Log"
│   └── From: A2LComplianceModal (auto-creates on A2L invoice item)
│
Select Action Type
│   ├── Recovery → requires poundsRecovered
│   ├── Recharge → requires poundsRecharged
│   ├── Leak Test → no weight fields required
│   ├── Leak Repair → no weight fields required
│   ├── Installation → requires poundsRecharged
│   ├── Decommission → requires poundsRecovered
│   ├── Initial Verification → linked to repair
│   ├── Follow-up Verification → linked to repair
│   ├── Quarterly/Annual Inspection
│   ├── Retrofit → isRetrofit = true
│   ├── Retirement → equipment retirement
│   └── Seasonal Variance → isSeasonalVariance = true
│
Select Refrigerant Type
│   ├── Check isA2L?
│   │   ├── YES → A2L Safety Checklist appears
│   │   │   ├── ☐ Dissipation risk confirmed
│   │   │   ├── ☐ Spark-free tools verified
│   │   │   ├── ☐ Ventilation verified
│   │   │   ├── ☐ PPE verified
│   │   │   ├── ☐ A2L leak detector used
│   │   │   ├── ☐ Equipment grounded
│   │   │   └── All 6 must be ☑ before save allowed
│   │   │
│   │   └── a2lChecklistComplete = all 6 true
│   │
│   └── NO → proceed without checklist
│
Optional: Select source cylinder
│   └── Cylinder weight updated via recordUsage()
│
Auto-filled fields:
│   ├── technicianName ← AppSettings / default TechnicianProfile
│   └── technicianCertNumber ← AppSettings / default TechnicianProfile
│
[Save] → ComplianceLog persisted
│   ├── Linked to Job (if from job)
│   ├── Linked to Cylinder (if selected)
│   ├── Linked to EquipmentProfile (if applicable)
│   └── If Retrofit, New Installation, or Seasonal Variance toggle is ON:
│       └── Leak rate calculation is automatically skipped
│           per 40 CFR 82.156/84.106 (exemption guard)
```

**Exemption HelpTooltip**: The Exemptions section header in ComplianceLogFormView includes a HelpTooltip (info icon) explaining the CFR reference for why exempt logs skip leak rate calculation.

---

## Journey 4: Equipment + Compliance Dashboard

**Source**: `Models/EquipmentProfile.swift`, `Views/Equipment/`, `Views/Compliance/`

```
Register Equipment
│   ├── From: EquipmentListView [+]
│   ├── From: FAB → "Register Equipment"
│   └── Opens: EquipmentFormView
│       ├── Name, location, owner, operator
│       ├── Appliance type (Commercial/Comfort/Industrial/Transport/Other)
│       ├── Refrigerant type → determines regulatory framework
│       ├── Full charge amount + method (Nameplate/Calculation/Field)
│       ├── Manufacturer, model, serial number
│       └── Install date, auto-leak detection toggle
│
Equipment Registered
│   ├── meetsChargeThreshold? (608: 50 lbs, Subpart C: 15 lbs)
│   │   └── YES → subject to leak rate tracking
│   │
│   ├── InspectionSchedule auto-created
│   │   └── Default: quarterly (adjustable)
│   │
│   └── Equipment appears on Compliance Dashboard
│
Equipment Detail — Net Refrigerant Summary
│   ├── Shows aggregated Total Added / Total Recovered / Net Change
│   │   └── Per 40 CFR 82.157(b) — color-coded (blue/green/orange)
│   ├── Appears between Rolling 12-Month Leak Rate and Service History
│   └── Empty state if no refrigerant transactions recorded
│
Monitor via Compliance Dashboard
│   ├── Critical Alerts (red)
│   │   ├── Overdue repairs → OverdueRepairsView
│   │   └── Overdue inspections → InspectionScheduleView
│   │
│   ├── Warnings (orange)
│   │   ├── Inspections due within 7 days
│   │   └── Pending verification tests
│   │
│   ├── Stats grid → drill-down navigation
│   │   └── Overdue Repairs and Pending Tests stat cards include
│   │       HelpTooltips explaining Section 608 vs Subpart C
│   │       regulatory differences (repair deadlines, test windows)
│   │
│   └── Actions
│       ├── Chronic Leaker Report → EPAReportGenerator
│       └── Audit Export → compliance history CSV
│
Leak Rate Exceeds Threshold
│   ├── ComplianceEngine detects via calculateAndStoreLeakRate()
│   ├── Repair required within deadline (30d or 120d for industrial/608)
│   ├── InspectionSchedule escalated (e.g., annual → quarterly)
│   └── Alert appears on dashboard
│
If 125% of charge added in calendar year
│   └── Chronic leaker → ChronicLeakerReport generated
│       ├── Submission tracking (date, method, reference)
│       └── Linked to EquipmentProfile
│
If repair fails → options:
│   ├── RepairExtension → EPA notification tracking
│   ├── RetrofitRetirementPlan → 1-year deadline
│   └── RefrigerantDisposition → chain of custody
```

---

## Journey 5: Cylinder Lifecycle

**Source**: `Models/RefrigerantCylinder.swift`, `Views/Inventory/`

```
Add Cylinder
│   ├── Method A: Scan Cylinder
│   │   ├── Camera opens (ScanCylinderSheet)
│   │   ├── Vision OCR detects label text
│   │   ├── Phase machine: searching → detecting → acquiring → locked (with regression prevention)
│   │   ├── Confidence ≥ 80%: ScanConfirmationSheet
│   │   │   └── [Confirm] → CylinderFormView (pre-filled)
│   │   ├── Field-level merging: best fields kept across frames (not full-result replacement)
│   │   ├── KG weight support: automatically converts to lbs
│   │   ├── Weighted confidence: mandatory fields 1.0x, optional 0.5x
│   │   └── Confidence < 80%: warning haptic
│   │       └── Manual form with OCR values pre-filled
│   │
│   └── Method B: Add Manually
│       └── CylinderFormView (blank)
│           Required: tareWeight > 0, grossWeight > tare
│           Optional: serial, manufacturer, refrigerant type, notes
│
Cylinder Tracked
│   ├── Appears in InventoryView list
│   ├── CylinderGaugeView shows fill %
│   ├── Fill status: Good(≥50%) / Moderate(20-49%) / Low(10-19%) / Critical(<10%)
│   └── A2L badge if refrigerant is A2L type
│
Track Weight
│   ├── [Update Weight] → UpdateWeightSheet
│   │   └── Sets currentWeight, lastWeighDate
│   │
│   └── Via ComplianceLog → recordUsage(pounds:)
│       └── currentWeight = max(tareWeight, currentWeight - pounds)
│
Low Inventory Alert
│   └── If isLow → appears on Dashboard "Low Inventory Alert"
│       └── NotificationManager: scheduleLowInventoryNotification
│           └── 24hr cooldown, escalation for critical
│
Exchange Cylinder
│   ├── [Exchange] → CylinderExchangeSheet
│   │   ├── Current cylinder archived (isArchived = true)
│   │   ├── Decommission ComplianceLog created
│   │   ├── Optional: add replacement via scan or manual
│   │   └── replacedByIdString / replacesIdString linked
│   │
│   └── Old cylinder hidden from active list
│       └── Still visible in compliance history
│
Archive / Delete
│   ├── Swipe gesture on list
│   ├── If linked compliance logs → archive (not delete)
│   └── If no logs → delete
```

### What if camera permission is denied?

```
Camera Permission Denied
│   ├── User taps "Scan Cylinder" (ScanCylinderSheet)
│   ├── AVCaptureDevice.requestAccess(for: .video)
│   │
│   ├── If denied at system level:
│   │   ├── Camera preview shows blank/black
│   │   ├── No OCR scanning possible
│   │   └── User must go to iOS Settings → FieldPad → Camera → Enable
│   │
│   └── Workaround: "Add Manually" button always available
│       ├── CylinderFormView opens with blank fields
│       ├── User types refrigerant type, tare weight, gross weight
│       └── Full cylinder creation without camera
│
│   Note: PhotosPicker (for business logo) uses the system picker
│         and does NOT require a separate photo library permission.
```

---

## Journey 6: Estimate Workflow

**Source**: `Models/Estimate.swift`, `Views/Estimates/`

```
Create Estimate
│   ├── From: EstimateListView [+]
│   ├── From: FAB → "New Estimate"
│   └── Opens: EstimateFormView
│       ├── Auto-generates number: EST-YYYYMMDD-XXXX
│       ├── Client picker (optional)
│       ├── Issue date, expiration date
│       ├── Tax rate (from AppSettings.defaultTaxRate)
│       └── Notes
│
│   ── [Save] ──→ Estimate.status = .draft
│
Add Line Items
│   └── EstimateLineItemFormView
│       ├── Description, quantity, unit price
│       ├── Category selection
│       └── Save → reuses InvoiceLineItem model
│
Send Estimate
│   ├── [Share PDF] or [Email]
│   ├── Estimate.status → .sent
│   └── Increments AppSettings.estimateShareCount
│       └── If count >= 10 (free): sharing limit
│
Client Response
│   ├── [Accept] → Estimate.status = .accepted
│   │   └── Can convert to Invoice (future feature path)
│   │
│   ├── [Decline] → Estimate.status = .declined
│   │
│   └── Auto-expire: expirationDate < Date()
│       └── Estimate.status = .expired
│
Collect Signature (optional)
│   └── Same PencilKit flow as invoices
│       └── signatureData, signedByName, signedDate
```

---

## Journey 7: Data Persistence & iCloud Sync

**Source**: `Services/Sync/SyncEngine.swift`, `Services/Sync/NetworkMonitor.swift`, `Services/Sync/ICloudService.swift`, `FieldPadApp.swift`

> **Current State (2026-02-08):** iCloud sync is **active** via SwiftData+CloudKit automatic sync. SwiftData `ModelContainer` is configured with `cloudKitDatabase: .private("iCloud.com.hugglerholdings.fieldpad")`. Entitlements include CloudKit, APS, and iCloud container. Data syncs automatically across devices.

```
Normal Operation (iCloud Sync Active)
│   ├── All CRUD operations save to SwiftData (local SQLite)
│   ├── SwiftData+CloudKit automatically syncs changes to iCloud
│   │   └── Sync runs transparently in background
│   ├── Legacy SyncEngine creates SyncRecord entries (effectively no-ops)
│   └── Data also accessible on other devices signed into same iCloud account
│
Network Lost
│   ├── NetworkMonitor.isConnected → false
│   ├── SyncStatusBadge updates UI → "Offline"
│   │
│   └── User continues ALL operations normally:
│       ├── Create clients, jobs, invoices ✓
│       ├── Scan cylinders (camera, on-device OCR) ✓
│       ├── Log compliance data ✓
│       ├── Generate PDFs ✓
│       ├── Collect signatures ✓
│       └── All data persists in local SwiftData/SQLite
│
Network Restored
│   ├── NetworkMonitor detects reconnection
│   ├── SwiftData+CloudKit automatically syncs pending changes
│   └── RevenueCat subscription status may refresh
│
Data Protection
│   ├── iCloud sync: data automatically backed up to private iCloud container
│   ├── Auto-backup: BackupService runs on configurable schedule (default 7 days)
│   │   ├── JSON export to Documents/Backups/
│   │   └── Rotation: max 5 backups retained
│   └── Manual export: Settings → Export Data (JSON/CSV)
```

### What if the user is offline during compliance logging?

```
Offline Compliance Logging
│   ├── All compliance features work fully offline:
│   │   ├── ComplianceLogFormView saves to local SwiftData ✓
│   │   ├── LeakRateCalculator runs locally (no network needed) ✓
│   │   ├── A2L safety checklists enforced locally ✓
│   │   ├── ComplianceEngine escalation/de-escalation runs locally ✓
│   │   └── EPAReportGenerator creates reports from local data ✓
│   │
│   ├── Limitations while offline:
│   │   ├── Cannot email invoices/exports (MFMailComposeViewController)
│   │   ├── Address autocomplete unavailable (MapKit needs network)
│   │   ├── RevenueCat subscription status may be stale
│   │   │   └── Last known status used; may allow/block features incorrectly
│   │   └── iCloud sync resumes automatically when connectivity returns
│   │
│   └── All compliance data is immediately available for export
│       when connectivity returns
```

---

## Journey 8: Subscription Expiry / Downgrade

**Source**: `Services/SubscriptionManager.swift`

```
Active Pro Subscriber
│   ├── All features unlocked
│   ├── No UsageLimitBanner shown
│   ├── No count limits enforced
│   └── Full signature images on PDFs
│
Subscription Lapses (cancellation or non-renewal)
│   ├── RevenueCat CustomerInfo updates
│   ├── SubscriptionManager.isSubscribed → false
│   │
│   └── Downgrade behavior:
│       ├── Existing data preserved (all records remain)
│       │   └── Read access: unlimited
│       │
│       ├── Count limits re-enforced:
│       │   ├── Clients: max 5 (existing beyond limit: visible, new creation blocked)
│       │   ├── Jobs: max 10
│       │   ├── Invoices: max 10
│       │   ├── Estimates: max 5
│       │   ├── Compliance logs: max 20
│       │   ├── Cylinders: max 10
│       │   ├── A2L logs: max 5
│       │   └── Invoice shares: 5 lifetime
│       │
│       ├── Pro-only features locked:
│       │   ├── Data export → ProPaywallView
│       │   ├── Compliance log export → ProPaywallView
│       │   ├── Recurring jobs → toggle blocked
│       │   ├── Business logo → ProPaywallView
│       │   └── iCloud sync → ProPaywallView
│       │
│       ├── PDF signatures → watermark text instead of image
│       │
│       └── UsageLimitBanner appears on list views
│           ├── Normal: "X/Y used" (accent color)
│           ├── Near limit: "Only X left" (orange)
│           └── At limit: "Limit reached — Upgrade" (red, lock icon)
│
Re-subscribe
│   ├── Via any ProPaywallView → RevenueCat purchase flow
│   ├── Or Settings → Subscription → Upgrade
│   └── Or Restore Purchases (for returning subscribers)
│       └── All limits immediately removed
```

### What if subscription expires mid-use?

```
Subscription Expires While Using Pro Feature
│
│   Scenario A: Mid-form (e.g., creating 11th invoice)
│   ├── RevenueCat CustomerInfo updates asynchronously
│   ├── SubscriptionManager.isSubscribed → false
│   ├── If form is already open: save still succeeds
│   │   └── Gating is checked on creation trigger, not on save
│   └── Next creation attempt: blocked by ProPaywallView
│
│   Scenario B: Existing data beyond free limits
│   ├── All existing records remain accessible (read-only is unlimited)
│   ├── User can view, edit, and search all 50+ clients
│   ├── User CANNOT create new records beyond free limits
│   └── UsageLimitBanner shows "Limit reached — Upgrade"
│
│   Scenario C: Pro-only feature in use (e.g., data export)
│   ├── If export is already in progress: completes normally
│   ├── Next attempt: ProPaywallView shown
│   └── Recurring jobs: existing recurrences still fire
│       but toggle to create new ones is blocked
│
│   Key guarantee: No data loss on downgrade.
│   All locally stored data remains on-device regardless of subscription.
```

> **ProFeature enum (16 cases):** unlimitedClients, unlimitedJobs, unlimitedInvoices, unlimitedEstimates, unlimitedComplianceLogs, unlimitedCylinders, complianceLogExport, dataExport, pdfInvoice, ocrScanning, recurringJobs, invoiceSharing, estimateSharing, businessLogo, icloudSync, unlimitedA2LLogs

---

## Journey 9: Data Management

**Source**: `Services/DataExportService.swift`, `Services/DataImportService.swift`, `Services/BackupService.swift`

```
Export Data (Pro feature)
│   ├── Settings → Data Management → Export
│   ├── Format picker: JSON / CSV
│   ├── DataExportService generates file
│   │   └── Exports: Clients, Jobs, Invoices, Cylinders, ComplianceLogs, Estimates
│   └── Share sheet → save/send file
│
Export Compliance Logs (Pro feature)
│   ├── Settings → EPA Section 608 Records → Export
│   ├── Format: CSV (EPA-formatted)
│   └── Includes: date, action, refrigerant, weights, technician, cert
│
Import Data
│   ├── Settings → Data Management → Import
│   ├── DataImportSheet opens
│   │   ├── Select JSON file
│   │   ├── previewImport() → shows counts before committing
│   │   │   └── "Import 12 clients, 45 jobs, 20 invoices?"
│   │   ├── [Import] → importData() executes
│   │   │   ├── Duplicate detection
│   │   │   └── Returns ImportResult summary
│   │   └── [Cancel] → no changes
│   │
│   └── Supported: Clients, Jobs, Invoices, Cylinders, ComplianceLogs
│
Auto-Backup
│   ├── BackupService.checkAndPerformBackup() runs periodically
│   ├── If AppSettings.autoBackupEnabled && days since last >= frequency:
│   │   ├── performBackup() → JSON export to Documents/Backups/
│   │   ├── rotateBackups() → keeps max 5
│   │   └── AppSettings.lastBackupDate updated
│   │
│   └── Manual backup: Settings → Backup Now
│
Restore from Backup
│   ├── listBackups() → shows available backups with date, size
│   ├── Select backup → import flow
│   └── deleteBackup() available for cleanup
│
Delete All Data
│   ├── Settings → Data Management → Delete All Data
│   ├── Double confirmation required
│   └── Deletes all SwiftData records:
│       Clients, Jobs, Invoices, ComplianceLogs, Cylinders, Estimates,
│       Equipment, Technicians, etc.
```

---

## Journey 10: Notification Flows

**Source**: `Services/Notifications/NotificationManager.swift`, `NotificationDelegate.swift`

```
Permission Flow
│   ├── First trigger → requestSmartPermissions()
│   │   └── Soft-ask pre-prompt sheet
│   │       ├── [Allow] → system UNUserNotificationCenter.requestAuthorization()
│   │       │   ├── User allows → isAuthorized = true
│   │       │   └── User denies → isAuthorized = false
│   │       └── [Not Now] → no system prompt, preferences saved
│   │
│   └── Subsequent → respects prior decision
│
8 Notification Types
│
│   1. Money Leak (moneyLeak)
│   │   Trigger: Job completed, no invoice created
│   │   Timing: 4 hours after completion
│   │   Content: "Don't leave money on the table — create an invoice"
│   │   Deep link: .job(id) → JobDetailView
│   │
│   2. EPA Shield (epaShield)
│   │   Trigger: Job started, no compliance log
│   │   Timing: 2 hours after job start
│   │   Content: EPA compliance reminder
│   │   Deep link: .job(id) → JobDetailView
│   │
│   3. Overdue Revenue (overdueRevenue)
│   │   Trigger: Invoice due date reached, unpaid
│   │   Timing: 9 AM on due date
│   │   Content: "Invoice #XXX is due today"
│   │   Deep link: .invoice(id) → InvoiceDetailView
│   │
│   4. Morning Brief (morningBrief)
│   │   Trigger: Jobs scheduled for today
│   │   Timing: 30 minutes before first job
│   │   Content: "You have X jobs today"
│   │   Deep link: .dashboard → DashboardView
│   │
│   5. Recurring Job Reminder (recurringJob)
│   │   Trigger: Recurring job approaching scheduled date
│   │   Timing: Day before due date
│   │   Content: Recurring job due reminder
│   │   Deep link: .job(id) → JobDetailView
│   │
│   6. A2L Safety (a2lSafety)
│   │   Trigger: Job with A2L refrigerant starting
│   │   Timing: Time-sensitive, before job start
│   │   Content: A2L safety checklist reminder
│   │   Deep link: .job(id) → JobDetailView
│   │   Interruption level: .timeSensitive
│   │
│   7. Low Inventory (lowInventory)
│   │   Trigger: Cylinder below fill threshold
│   │   Timing: Respects work hours, 24hr cooldown
│   │   Escalation: "low" → "critical" triggers immediate
│   │   Deep link: .cylinder(id) → CylinderDetailView
│   │
│   8. Chronic Leaker Deadline (chronicLeakerDeadline)
│   │   Trigger: Equipment with 125%+ additions approaching March 1 deadline
│   │   Timing: February 15 (2 weeks before deadline)
│   │   Content: EPA chronic leaker report submission deadline reminder
│   │   Deep link: .compliance → ComplianceDashboardView
│
Notification Tap Handling
│   ├── NotificationDelegate.didReceive()
│   ├── Extracts notification type and entity ID from userInfo
│   ├── Sets DeepLinkRouter.shared.pendingDestination
│   └── MainTabView observes and navigates
│       ├── Switches to correct tab
│       └── Pushes to correct detail view
```
