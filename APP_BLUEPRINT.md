# FieldPad App Blueprint

## 1. Core Value Proposition

FieldPad is an iOS app built for HVAC and refrigeration field technicians. It consolidates job management, client tracking, refrigerant cylinder inventory, EPA Section 608 compliance logging, and invoicing into a single offline-first tool designed for use on job sites — including while wearing work gloves.

**Target audience:** Solo HVAC technicians and small refrigeration service companies who need to track refrigerant usage, maintain EPA compliance records, manage jobs from scheduling through invoicing, and generate professional invoices with digital signatures — all without relying on internet connectivity.

**Key differentiators:**
- **Offline-first architecture** — All data persists locally via SwiftData/SQLite with iCloud sync via CloudKit private database (`cloudKitDatabase: .private("iCloud.com.hugglerholdings.fieldpad")`). The app functions fully without network access; data syncs automatically when connectivity is available.
- **Minimal third-party dependencies** — Only RevenueCat for subscription management. All core features (UI, persistence, OCR, compliance) use native Apple frameworks exclusively.
- **Glove Mode** — Enlarged touch targets (48pt minimum vs. standard 44pt) for use with protective equipment.
- **EPA Section 608 compliance** — Built-in A2L refrigerant safety checklists, leak rate calculators, and exportable compliance logs.
- **OCR cylinder scanning** — Uses the Vision framework to read refrigerant cylinder labels and auto-populate inventory records.

---

## 2. Feature Inventory

### 2.1 Dashboard (Tab 1)

The dashboard provides at-a-glance operational metrics and quick navigation to urgent items.

| Feature | Description | Tester-Actionable Logic |
|---------|-------------|------------------------|
| Active Jobs stat | Count of jobs with status Scheduled or In Progress | Tap navigates to the job |
| Pending revenue stat | Sum of unpaid invoice totals | Tap navigates to invoices |
| Cylinder count stat | Total non-archived cylinders | Display only |
| Low stock stat | Count of cylinders where `isLow == true` | Highlighted in red |
| Today's Jobs section | Jobs whose `scheduledDate` falls on the current calendar day | Each row links to JobDetailView |
| Low Inventory Alert | Cylinders with `isLow == true` | Each row links to CylinderDetailView; section hidden if none are low |
| Pending Invoices | First 3 unpaid invoices sorted by issue date | Each row links to InvoiceDetailView |
| Sync Status indicator | Toolbar icon showing current sync state | Tapping shows sync details |

### 2.2 Jobs (Tab 2)

Full job lifecycle management with list and calendar views.

| Feature | Description | Tester-Actionable Logic |
|---------|-------------|------------------------|
| Job list | All jobs sorted by `scheduledDate` descending | Searchable by title or client name (case-insensitive) |
| Status filters | Chips: All, Active, Completed, Invoiced | Active = `.scheduled` or `.inProgress`; Completed = `.completed`; Invoiced = `.invoiced` |
| List/Schedule toggle | Toolbar menu switches between list and calendar timeline view | Schedule view shows 6 AM–8 PM hourly grid with colored job blocks |
| Schedule view | Calendar timeline with date navigation (← today →) | Job blocks are color-coded by type; tap navigates to JobDetailView; 60pt per hour height; minimum 50pt block height |
| Create job | + toolbar button opens JobFormView | Required field: title. Optional: client picker, date/time, duration (30 min / 1 hr / 2 hrs / 4 hrs / all day), address (with autocomplete), labor hours/rate, notes, description |
| Job detail | Drill-down view with status actions, client link, compliance logs, linked invoice | Displays all job fields plus computed labor total (hours × rate) |
| Start Job | Button on detail view when status = Scheduled | Sets `status = .inProgress` |
| Complete Job | SwipeToConfirmButton when status = In Progress | Calls `job.complete()`, sets status to `.completed`, records completion date |
| Create Invoice from Job | Button when status = Completed and no linked invoice | Opens InvoiceFormView in `createFromJob` mode; auto-adds labor line item if hours and rate are set; sets job status to `.invoiced` |
| Add Compliance Log | Toolbar menu action on detail view | Opens ComplianceLogFormView linked to the job |
| Swipe to delete | Swipe gesture on list rows | Deletes the job |
| A2L badge | Orange "A2L" badge on jobs with A2L compliance logs | Visual indicator only |

**Job Status Workflow:**
```
Scheduled → In Progress → Completed → Invoiced
                                    ↘ Cancelled
```

### 2.3 Clients (Tab 3)

Client directory with contact actions and job history.

| Feature | Description | Tester-Actionable Logic |
|---------|-------------|------------------------|
| Client list | All clients sorted by name | Searchable by name, company, or email (case-insensitive) |
| Avatar | Colored circle with first letter of name | Color derived from name hash for consistency |
| Job count badge | Shows number of associated jobs per client | Displayed on list row |
| Create client | + toolbar button opens ClientFormView | Required: name. Optional: type (Residential/Business), company (shown if Business), phone, email, address (with autocomplete), city, state, ZIP, notes |
| Client detail | Drill-down with contact section, recent jobs (last 5), action buttons | Shows "And X more..." if client has > 5 jobs |
| Call client | Button on detail view | Opens phone dialer via `tel:` URL; only shown if phone number exists |
| Email client | Tap email in contact section | Opens default mail client |
| Open in Maps | Tap address in contact section | Opens Apple Maps with the client's address |
| Create job for client | Button on detail view | Opens JobFormView with client pre-selected |
| Swipe to delete | Swipe gesture on list rows | Deletes the client (cascade deletes configured on relationships) |

### 2.4 Inventory (Tab 4)

Refrigerant cylinder tracking with OCR scanning and exchange workflows.

| Feature | Description | Tester-Actionable Logic |
|---------|-------------|------------------------|
| Cylinder list | Non-archived cylinders sorted by refrigerant type | Searchable; shows gauge, type, weight, A2L badge |
| Filters | Chips: All, Low Stock, A2L Only | Low Stock = `cylinder.isLow`; A2L Only = `cylinder.isA2L` |
| Summary section | Total cylinders, low stock count, A2L count | Scrollable on iPhone, horizontal on iPad |
| Scan Cylinder | Button opens ScanCylinderSheet with camera | Uses Vision framework OCR to detect refrigerant type, tare weight, gross weight from label text |
| Scan confidence | Confidence percentage displayed, color-coded | ≥ 80%: shows confirmation sheet. < 80%: warning haptic, redirects to manual form with OCR values pre-filled |
| Camera controls | Flash toggle, zoom toggle (1x/2x), tap-to-focus | Viewfinder changes from dashed (no text) to solid (text detected); scanning indicator line animates |
| Hint system | "Try turning on the flash" after 5 seconds idle | Appears only if flash is off and no scan results yet |
| Add manually | Button opens CylinderFormView | Required: tare weight > 0, gross weight > tare. Optional: serial number, manufacturer, refrigerant type, purchase date, notes |
| Cylinder detail | Drill-down with gauge visualization, quick actions, usage history | Shows heel weight (current − tare), initial charge (gross − tare), last weighed date |
| CylinderGaugeView | Visual fill indicator showing remaining refrigerant | Color transitions from green (full) to red (low) |
| Update Weight | Button on detail view opens UpdateWeightSheet | Updates `currentWeight` and `lastWeighDate` |
| Exchange Cylinder | Button on detail view opens CylinderExchangeSheet | Archives the current cylinder, creates a decommission ComplianceLog entry. Optional: add replacement cylinder via scan or manual entry |
| Swipe to delete/archive | Swipe gesture on list rows | Archives (not deletes) if cylinder has linked compliance logs |
| A2L badge and warnings | Orange badge on A2L refrigerant types | Shown on list rows, detail views, and form pickers; A2L types: R-454B, R-32, R-452B |

### 2.5 Invoicing

Invoice creation, line items, PDF generation, digital signatures, and payment tracking.

| Feature | Description | Tester-Actionable Logic |
|---------|-------------|------------------------|
| Invoice list | All invoices sorted by issue date descending | Status dot: green (paid), orange (pending), red (overdue) |
| Filters | Chips: All, Pending, Paid, Overdue | Pending = `!isPaid && !isOverdue`; Overdue = `isOverdue == true` |
| Create invoice | + toolbar button or FAB opens InvoiceFormView | Required: invoice number (auto-generated or custom). Optional: issue date, due date (defaults to +30 days if enabled), tax rate (auto-filled from AppSettings.defaultTaxRate), notes |
| Invoice detail | Drill-down with line items, totals, signature section, compliance logs | Totals section shows subtotal, tax (if rate > 0), and total |
| Add line item | + button on detail view opens LineItemFormView | Required: description, unit price > 0. Optional: quantity, category (labor/materials/parts/refrigerant/other), refrigerant type and pounds (if refrigerant toggle enabled) |
| A2L Compliance Modal | Triggered when adding a line item with an A2L refrigerant type | Requires: source cylinder selection, technician name, EPA cert number, and all 6 mandatory safety checklist items (dissipation confirmed, spark-free tools, ventilation verified, PPE verified, A2L leak detector used, equipment grounded). 1 optional item (emergency plan reviewed). 3 H3 informational items (maximum charge verified, installation location verified, recovery equipment rated). Creates a ComplianceLog on confirmation |
| Invoice totals | Computed from line items | Subtotal = sum of (quantity × unitPrice) per item. Tax = subtotal × (taxRate / 100). Total = subtotal + tax |
| Mark as Paid | SwipeToConfirmButton on detail view when unpaid | Sets `isPaid = true` and records `paidDate` |
| Collect Signature | Button on detail view opens InvoiceSignatureSheet | Uses PencilKit canvas (3pt black ink pen). Captures signer name + signature PNG + date. Shows invoice summary (number, client, total) above signature area |
| Share PDF | Toolbar menu generates PDF and opens system share sheet | PDF includes invoice details, line items, totals, and business logo if configured |
| Email Invoice | Toolbar menu opens MFMailComposeViewController | Attaches generated PDF to email |
| Swipe to delete | Swipe gesture on list rows | Deletes the invoice |

### 2.5b Estimates

Estimate creation with lifecycle tracking, signatures, and PDF sharing.

| Feature | Description | Tester-Actionable Logic |
|---------|-------------|------------------------|
| Estimate list | All estimates sorted by issue date | Filterable: All, Draft, Sent, Accepted |
| Create estimate | + toolbar button or FAB opens EstimateFormView | Auto-generates number: EST-YYYYMMDD-XXXX. Optional: client, expiration date, tax rate, notes |
| Estimate detail | Drill-down with line items, totals, signature | Totals: subtotal, tax, total |
| Add line items | EstimateLineItemFormView | Same categories as invoice line items |
| Status workflow | draft → sent → accepted / declined / expired | Auto-expires when expirationDate < now |
| Share/Email | Generates PDF via share sheet or email | Increments estimateShareCount (free limit: 10 lifetime) |
| Collect signature | PencilKit canvas, signer name, date | Same flow as invoice signatures |

### 2.5c Equipment Management

EPA-regulated equipment registration and compliance tracking.

| Feature | Description | Tester-Actionable Logic |
|---------|-------------|------------------------|
| Equipment list | All equipment sorted by updated date | Filterable by regulatory framework and appliance type |
| Register equipment | Form with appliance type, refrigerant, charge amount | Regulatory framework auto-assigned from refrigerant type |
| Equipment detail | Full specs with compliance logs, leak rate records | Shows leak rate threshold and charge threshold status |
| Inspection schedules | Per-equipment inspection tracking | Frequencies: monthly/quarterly/semiannual/annual |
| Compliance logs | Linked compliance history per equipment | All compliance actions associated with equipment |
| Net refrigerant summary | Aggregated Total Added / Total Recovered / Net Change per equipment | Per 40 CFR 82.157(b); color-coded (blue=added, green=recovered, orange/green=net) |
| Leak rate records | Historical leak rate calculations | Stored via ComplianceEngine |

### 2.5d Compliance Dashboard

Centralized EPA compliance monitoring with alerts and actions.

| Feature | Description | Tester-Actionable Logic |
|---------|-------------|------------------------|
| Critical alerts | Overdue repairs, overdue inspections | Red alert cards with severity indicators |
| Warnings | Inspections due within 7 days, pending verifications | Orange warning cards |
| Stats grid | Equipment count, overdue repairs, due inspections, pending tests | Each stat links to detailed view; Overdue Repairs and Pending Tests cards include HelpTooltips explaining Section 608 vs Subpart C regulatory differences |
| Recent activity | Last 10 compliance logs | Shows action type, refrigerant, date, leak status |
| Chronic leaker report | Generate EPA 125% threshold report | Via EPAReportGenerator |
| Audit export | Export compliance history as CSV | Filterable by date range and equipment |

### 2.5e Technician Profiles

Multi-technician support with certification tracking.

| Feature | Description | Tester-Actionable Logic |
|---------|-------------|------------------------|
| Technician list | All saved technician profiles | Managed in Settings |
| Add technician | Name, cert number, cert type, organization | Cert types: Type I, II, III, Universal |
| Default technician | isDefault flag for auto-fill | Auto-fills compliance log and A2L modal fields |
| Card on file | Track whether EPA card is on file | Informational boolean |

### 2.6 Settings (Tab 5)

App configuration, tools, and data management.

| Feature | Description | Tester-Actionable Logic |
|---------|-------------|------------------------|
| Business Logo | PhotosPicker for selecting logo image | Preview at 60×60. Remove button available. Logo appears on generated invoices |
| Theme picker | Light / Dark / Auto (system) | Changes app-wide color scheme immediately |
| Glove Mode | Toggle for enlarged touch targets | Increases minimum tap target from 44pt to 48pt across all GloveButton and GloveTextField instances |
| Technician Name | Text field bound to AppSettings | Auto-fills on ComplianceLogFormView and A2LComplianceModal |
| EPA Cert Number | Text field bound to AppSettings | Auto-fills on compliance forms; required for A2L compliance logs |
| Default Tax Rate | Decimal field bound to AppSettings | Auto-fills on new invoice creation |
| 5,000 Rule Calculator | Dedicated view for EPA leak rate calculation | Inputs: system type (Comfort Cooling, Commercial Refrigeration, Industrial, High-Pressure), capacity (lbs), refrigerant added (lbs), days since last charge. Methods: Method 1 (annualized) or Method 2. Output: leak rate percentage, threshold comparison, compliance status (COMPLIANT / NON-COMPLIANT), repair required alert |
| Refrigerant Guide | Reference list of refrigerant types | Filterable by category: A2L / Traditional / All. Shows name, GWP value, safety class, flame icon if flammable |
| Export All Data | Generates JSON or CSV file via ShareSheet | Uses DataExportService; format selectable via picker |
| Export Compliance Logs | Generates EPA-formatted export | Separate from full data export; same format options |
| Delete All Data | Destructive button with confirmation alert | Deletes all SwiftData records |
| iCloud Backup | Button to trigger manual sync | **Active** — Pro-gated UI; sync runs via SwiftData+CloudKit for all users |
| Auto-Sync toggle | Enables/disables automatic sync | **Active** — SwiftData+CloudKit automatic sync; UI gated behind Pro |
| Last Synced | Timestamp display | **Active** — displays last sync timestamp; UI gated behind Pro |
| App version | Display only | Shows version 1.0.0, build number |
| EPA Info link | Opens EPA Section 608 website | Links to https://www.epa.gov/section608 |

### 2.7 Quick Actions (Floating Action Button)

The FAB appears on MainTabView across all tabs, positioned bottom-center above the tab bar.

| Action | Destination | Notes |
|--------|-------------|-------|
| New Job | JobFormView (create mode) | Empty form |
| New Client | ClientFormView (create mode) | Empty form |
| Scan Cylinder | ScanCylinderSheet | Opens camera for OCR |
| New Invoice | InvoiceFormView (create mode) | Empty form |
| New Estimate | EstimateFormView (create mode) | Empty form |
| Compliance Log | ComplianceLogFormView | Log a refrigerant transaction |
| Register Equipment | EquipmentFormView (create mode) | Track EPA compliance for equipment |

FAB sizing: 64pt on iPad, 56pt on iPhone. Opens a QuickActionsSheet with spring animation. Haptic feedback on tap.

### 2.8 Cross-Cutting Features

| Feature | Description |
|---------|-------------|
| Haptic feedback | Used throughout: `buttonPress()` on primary actions, `selectionChanged()` on filters/toggles, `success()` on save/completion, `warning()` on delete/overdue, `error()` on failures, `tap()` on minor interactions |
| SwipeToConfirmButton | Swipe gesture for destructive/important actions (complete job, mark paid, confirm A2L compliance) |
| AddressSearchField | MapKit-based address autocomplete that populates city, state, and ZIP fields |
| SyncStatusBadge | Per-record sync indicator showing pending/synced/error/conflict states |
| A2L safety system | Mandatory safety checklists when handling A2L refrigerants (R-454B, R-32, R-452B, R-466A) across compliance logs, invoice line items, and cylinder forms |
| Responsive layout | iPad and iPhone support with adaptive sizing throughout |

---

## 3. Technical Architecture

### 3.1 Stack

| Layer | Technology |
|-------|-----------|
| UI Framework | SwiftUI (iOS 17+) |
| Architecture Pattern | Observable Services + Direct Data Binding (no ViewModel layer — views use `@Query` directly) |
| Persistence | SwiftData with local SQLite storage |
| Concurrency | Swift structured concurrency; `SWIFT_DEFAULT_ACTOR_ISOLATION = MainActor` |
| Minimum Deployment | iOS 17.0 |
| Swift Version | 5.0 |
| Third-Party Dependencies | RevenueCat (subscription management) |

### 3.2 Service Layer

Services are `@Observable @MainActor` singletons injected via SwiftUI `.environment()` modifiers at the app root.

| Service | Responsibility |
|---------|---------------|
| **ThemeManager** | Manages dark/light/auto theme, Glove Mode toggle, provides themed colors/spacing/typography |
| **SyncEngine** | Orchestrates offline sync — marks entities for sync on create/update, tracks operations (create/update/delete), manages sync queue |
| **SyncStatusManager** | Tracks per-entity sync status (pending/synced/error/conflict) |
| **NetworkMonitor** | Wraps `NWPathMonitor` to detect connectivity changes and expose online/offline state |
| **ICloudService** | CloudKit account status checking and remote notification registration for iCloud sync |
| **AppSettings** | `UserDefaults` wrapper for technician name, EPA cert, default tax rate, theme preference, business logo data |
| **CylinderScannerService** | Manages AVFoundation camera session for OCR scanning |
| **TextRecognitionEngine** | Wraps Vision framework `VNRecognizeTextRequest` for extracting text from camera frames |
| **LeakRateCalculator** | EPA Section 608 leak rate calculations (Method 1 annualized, Method 2) |
| **DataExportService** | Generates JSON, CSV, and PDF exports of app data and compliance logs |
| **DataImportService** | JSON import with preview capability and duplicate detection |
| **HapticManager** | Centralized haptic feedback with semantic methods (buttonPress, success, warning, error, etc.) |
| **ComplianceEngine** | EPA leak rate tracking (with exemption-aware `calculateAndStoreLeakRate(triggeringLog:)` overload), chronic leaker detection, inspection scheduling with escalation/de-escalation |
| **NotificationManager** | 12 notification types (Money Leak, EPA Shield, Overdue Revenue, Morning Brief, Recurring Job, A2L Safety, Low Inventory, Chronic Leaker Deadline, Chronic Leaker Early Warning, Chronic Leaker Overdue, Initial Verification Deadline, Verification Follow-Up) |
| **SubscriptionManager** | RevenueCat-integrated Pro feature gating with 16 ProFeature cases |
| **DeepLinkRouter** | Routes notification taps to specific views (job, invoice, cylinder, dashboard, compliance) |
| **FormDraftService** | UserDefaults-backed draft persistence for create-mode forms |
| **BackupService** | Auto-backup with configurable frequency (default 7 days) and rotation (max 5 backups) |
| **ReviewRequestService** | StoreKit review requests with guardrails (3 sessions min, 60-day cooldown, 3/year max) |
| **ScheduleConflictDetector** | Detects overlapping job schedules with overlap duration calculation |
| **EPAReportGenerator** | Generates chronic leaker reports and compliance history CSV exports |
| **ValidationHelper** | Form input validation (email, phone, zip code, decimal ranges) |
| **LineItemTemplateService** | 18 preset + dynamic invoice line item templates sorted by usage frequency |
| **ComplianceDeletionGuard** | Retention-aware delete/archive routing for compliance logs per EPA record retention requirements |
| **DatabaseSaveHelper** | SwiftData save with 2-attempt retry, error haptics, and toast notification |

### 3.3 Persistence

- **SwiftData ModelContainer** initialized at app launch with 17 model types
- **Schema**: SchemaV1 only (no migrations yet; empty stages in `FieldPadMigrationPlan`)
- **Reactive data** via `@Query` property wrapper in views for automatic UI updates
- **Cascade deletes** configured on parent-child relationships (e.g., Client → Jobs, Invoice → LineItems)
- **Sync tracking** via `SyncRecord` model that maps local IDs to remote IDs with operation type and timestamp
- **DatabaseErrorView** displayed if ModelContainer initialization fails

### 3.4 Native Framework Usage

| Framework | Usage |
|-----------|-------|
| SwiftUI | All UI |
| SwiftData | Persistence and model layer |
| Vision | OCR text recognition for cylinder label scanning |
| AVFoundation | Camera capture session for scanning |
| PencilKit | Digital signature capture on invoices |
| MapKit | Address search and autocomplete (inferred from AddressSearchField) |
| MessageUI | `MFMailComposeViewController` for emailing invoices with PDF attachments |
| PhotosUI | `PhotosPicker` for business logo selection |
| Network | `NWPathMonitor` for connectivity detection |
| CloudKit | iCloud sync active via SwiftData+CloudKit private database; entitlements include CloudKit, APS, and iCloud container |
| PDFKit / Core Graphics | PDF generation for invoices (inferred from PDF export functionality) |
| UIKit | `UIImpactFeedbackGenerator` for haptics, `PKCanvasView` hosting |

---

## 4. Navigation Flow

### 4.1 App Launch

```
App Launch
  → FieldPadApp.swift
    → Initialize ModelContainer (17 models)
      → Success:
        → hasCompletedOnboarding == false? → OnboardingView (5-page carousel)
        → hasCompletedOnboarding == true? → MainTabView
        → Inject: ThemeManager, SyncEngine, ICloudService, SubscriptionManager,
                   NotificationManager, ComplianceEngine
      → Failure: DatabaseErrorView
    → Configure RevenueCat (SubscriptionManager)
    → Note: Remote notification registration in AppDelegate for CloudKit sync
                   APS entitlement active; iCloud sync runs for all users
```

### 4.2 Tab Structure

```
MainTabView
├── Tab 1: DashboardView (NavigationStack)
│   ├── → JobDetailView → JobFormView (edit)
│   │                    → ComplianceLogFormView
│   │                    → InvoiceFormView (createFromJob)
│   ├── → CylinderDetailView → UpdateWeightSheet
│   │                        → CylinderExchangeSheet
│   └── → InvoiceDetailView → LineItemFormView
│                            → InvoiceSignatureSheet
│                            → ShareSheet (PDF)
│
├── Tab 2: JobListView (NavigationStack)
│   ├── → JobDetailView (same drill-down as above)
│   ├── → JobFormView (create)
│   └── → ScheduleView (alternate view mode)
│         └── → JobDetailView
│
├── Tab 3: ClientListView (NavigationStack)
│   ├── → ClientDetailView → JobDetailView
│   │                      → JobFormView (create, client pre-selected)
│   └── → ClientFormView (create)
│
├── Tab 4: InventoryView (NavigationStack)
│   ├── → CylinderDetailView (same drill-down as above)
│   ├── → CylinderFormView (create)
│   └── → ScanCylinderSheet → ScanConfirmationSheet → CylinderFormView (prefilled)
│
├── Tab 5: SettingsView (NavigationStack)
│   ├── → FiveThousandRuleView
│   ├── → RefrigerantGuideView
│   ├── → TechnicianProfilesView → TechnicianFormView
│   ├── → FreeLimitsView
│   ├── → DataImportSheet
│   └── → DataManagementSheet
│
├── EstimateListView (via navigation)
│   ├── → EstimateDetailView → EstimateLineItemFormView
│   └── → EstimateFormView (create)
│
├── EquipmentListView (via navigation)
│   ├── → EquipmentDetailView → InspectionScheduleView
│   └── → EquipmentFormView (create/edit)
│
├── ComplianceDashboardView (via navigation)
│   ├── → OverdueRepairsView
│   ├── → PendingVerificationsView → VerificationTestFormView (with follow-up deadline warning)
│   ├── → ChronicLeakerReportView
│   ├── → LeakRateView
│   ├── → RepairExtensionFormView
│   ├── → RetrofitRetirementFormView
│   └── → RefrigerantDispositionFormView
│
└── FAB (QuickActionsSheet, presented as sheet)
    ├── → JobFormView (create)
    ├── → ClientFormView (create)
    ├── → ScanCylinderSheet
    ├── → InvoiceFormView (create)
    ├── → EstimateFormView (create)
    ├── → ComplianceLogFormView
    └── → EquipmentFormView (create)
```

### 4.3 Key Workflows

**Job Lifecycle:**
Create Job → Schedule → Start Job → Complete Job → Create Invoice → Add Line Items → Collect Signature → Mark Paid

**Cylinder Lifecycle:**
Scan or Add Manually → Track Weight → Use in Compliance Logs → Exchange/Archive

**Compliance Logging:**
Open Job → Add Compliance Log → Select Action Type → Select Refrigerant/Cylinder → If A2L: Complete Safety Checklist → Save

**Invoice from Job:**
Job Detail (completed) → Create Invoice → Auto-adds labor line item → Add additional line items → If A2L refrigerant: A2L Compliance Modal → Share PDF / Email → Collect Signature → Mark Paid

---

## 5. External Integrations

### 5.1 RevenueCat (Active)

| Aspect | Detail |
|--------|--------|
| SDK | `import RevenueCat`, `import RevenueCatUI` |
| Purpose | Subscription management, purchase validation, paywall UI |
| API Key | `appl_wkEKfZhYGmdyvOBxscTy` |
| Entitlement | `"FieldPad Pro"` |
| Configuration | `Purchases.configure(withAPIKey:)` at `SubscriptionManager.swift:174` |
| Data sent | Anonymous installation ID, purchase receipts |
| Files | `SubscriptionManager.swift`, `FieldPadApp.swift`, `SettingsView.swift` |

### 5.2 Apple Frameworks (Native)

All core functionality uses native Apple frameworks: SwiftUI, SwiftData, Vision, AVFoundation, PencilKit, MapKit, MessageUI, PhotosUI, Network, CloudKit, PDFKit, UIKit, StoreKit.

### 5.3 CloudKit/iCloud Sync (Active)

iCloud sync is **active** via SwiftData+CloudKit automatic sync using `cloudKitDatabase: .private("iCloud.com.hugglerholdings.fieldpad")`. Entitlements include CloudKit, APS (`aps-environment: development`), and iCloud container (`iCloud.com.hugglerholdings.fieldpad`). `ICloudService` handles CloudKit account status checking and remote notification registration. The `SyncEngine` and `SyncRecord` infrastructure remain for local change tracking. Sync UI controls (Sync Now, Last Synced) are gated behind Pro; sync itself runs for all users.

### 5.4 External URLs

The only external URL reference is a link to the EPA Section 608 information page (`https://www.epa.gov/section608`) in Settings.

---

## 6. Data Models

### 6.1 Entity Relationship Diagram

```
Client (1) ──── (many) Job
Client (1) ──── (many) Estimate
Client (1) ──── (many) EquipmentProfile
                         │
Job (1) ──── (many) ComplianceLog
Job (1) ──── (0..1) Invoice
                         │
Invoice (1) ──── (many) InvoiceLineItem (cascade)
Invoice (1) ──── (many) ComplianceLog
                         │
Estimate (1) ──── (many) InvoiceLineItem (cascade, shared model)

RefrigerantCylinder (1) ──── (many) ComplianceLog

EquipmentProfile (1) ──── (many) ComplianceLog
EquipmentProfile (1) ──── (many) LeakRateRecord
EquipmentProfile (1) ──── (many) InspectionSchedule
EquipmentProfile (1) ──── (many) RetrofitRetirementPlan
EquipmentProfile (1) ──── (many) ChronicLeakerReport

ComplianceLog (many) ──── (1) EquipmentProfile

SyncRecord (standalone — tracks sync state for all 17 entity types)
TechnicianProfile (standalone — auto-fill source for compliance forms)
VerificationTest (standalone — linked to ComplianceLog via UUID string)
RepairExtension (standalone — linked to ComplianceLog via UUID string)
RefrigerantDisposition (standalone — linked to ComplianceLog via UUID string)
```

### 6.2 Model Definitions

**Client**
| Field | Type | Notes |
|-------|------|-------|
| name | String | Required |
| clientType | String | "residential" or "business" |
| companyName | String? | Shown if business type |
| phone | String? | Used for call action |
| email | String? | Used for email action |
| address | String? | |
| city | String? | |
| state | String? | 2-character code |
| zipCode | String? | |
| notes | String? | |
| syncStatus | SyncStatus | Default: .pending |
| jobs | [Job] | Inverse relationship, cascade delete |

**Job**
| Field | Type | Notes |
|-------|------|-------|
| title | String | Required |
| jobDescription | String? | |
| jobType | JobType | .repair, .maintenance, .inspection, .other |
| status | JobStatus | .scheduled, .inProgress, .completed, .invoiced, .cancelled |
| scheduledDate | Date | |
| completedDate | Date? | Set when status → .completed |
| estimatedDuration | TimeInterval? | |
| address | String? | |
| laborHours | Double | Default: 0 |
| laborRate | Double | Default: 0 |
| notes | String? | |
| endDate | Date? | End date for scheduled jobs |
| isRecurring | Bool | Whether job recurs on a schedule |
| recurrencePattern | RecurrencePattern? | daily, weekly, biweekly, monthly, quarterly |
| parentJobId | UUID? | Links to parent job for recurring instances |
| syncStatus | SyncStatus | Default: .pending |
| client | Client? | Optional inverse relationship |
| complianceLogs | [ComplianceLog] | Inverse relationship |
| invoice | Invoice? | Inverse relationship |

**Invoice**
| Field | Type | Notes |
|-------|------|-------|
| invoiceNumber | String | Required, auto-generated or custom |
| issueDate | Date | |
| dueDate | Date? | Defaults to issueDate + 30 days if enabled |
| taxRate | Double | Auto-filled from AppSettings |
| taxRateLabor | Double | Separate tax rate for labor items |
| taxRateMaterial | Double | Separate tax rate for material items |
| status | InvoiceStatus | draft, sent, viewed, paid, overdue, void_status |
| paymentMethod | String? | Payment method used |
| amountPaid | Double | Amount paid so far |
| sentDate | Date? | Date invoice was sent |
| notes | String? | |
| isPaid | Bool | Default: false |
| paidDate | Date? | Set when marked paid |
| isSigned | Bool | Default: false |
| signatureData | Data? | PNG image from PencilKit |
| signedByName | String? | |
| signedDate | Date? | |
| syncStatus | SyncStatus | Default: .pending |
| lineItems | [InvoiceLineItem] | Inverse relationship, cascade delete |
| job | Job? | Inverse relationship |
| complianceLogs | [ComplianceLog] | Inverse relationship |
| isOverdue | Bool | Computed: !isPaid && dueDate < now |

**InvoiceLineItem**
| Field | Type | Notes |
|-------|------|-------|
| description | String | Required |
| quantity | Double | Default: 1 |
| unitPrice | Double | Required, must be > 0 |
| category | LineItemCategory | .labor, .materials, .parts, .refrigerant, .other |
| refrigerantType | RefrigerantType? | Set if refrigerant toggle enabled |
| poundsUsed | Double? | Pounds of refrigerant used |
| sortOrder | Int | For ordering within invoice |
| invoice | Invoice | Inverse relationship |
| total | Double | Computed: quantity × unitPrice |
| isA2LRefrigerant | Bool | Computed from refrigerantType |

**RefrigerantCylinder**
| Field | Type | Notes |
|-------|------|-------|
| cylinderSerialNumber | String? | Optional |
| refrigerantType | RefrigerantType | Stored as raw value |
| manufacturer | String? | |
| tareWeight | Double | Required, must be > 0 |
| grossWeight | Double | Required, must be > tare |
| currentWeight | Double | Updated via weight tracking |
| initialCharge | Double | Initial refrigerant charge amount |
| purchaseDate | Date? | Optional tracking |
| notes | String? | |
| isArchived | Bool | Default: false; set on exchange |
| replacedById | UUID? | ID of cylinder that replaced this one |
| replacesId | UUID? | ID of cylinder this one replaces |
| recertificationDate | Date? | Next recertification date |
| condition | String? | Physical condition of cylinder |
| isOwned | Bool | Whether technician owns the cylinder |
| supplierName | String? | Supplier/vendor name |
| purchaseQuantity | Double? | Original purchase quantity |
| purchaseCertificationVerified | Bool | Whether purchase certification was verified |
| exchangeDate | Date? | Set when exchanged |
| exchangeNotes | String? | |
| lastWeighDate | Date? | Updated on weight change |
| syncStatus | SyncStatus | Default: .pending |
| usageLogs | [ComplianceLog] | Inverse relationship |
| isLow | Bool | Computed: remaining weight below threshold |
| isA2L | Bool | Computed from refrigerantType |
| heelWeight | Double | Computed: currentWeight − tareWeight |
| initialCharge | Double | Computed: grossWeight − tareWeight |

**ComplianceLog**
| Field | Type | Notes |
|-------|------|-------|
| actionType | ComplianceActionType | .recharge, .recovery, .decommission, .leakRepair |
| refrigerantType | RefrigerantType | |
| poundsRecovered | Double? | If action involves recovery |
| poundsRecharged | Double? | If action involves recharge |
| technicianName | String | Auto-filled from AppSettings |
| technicianCertNumber | String | EPA cert; required for A2L |
| leakDetected | Bool | Default: false |
| leakRepaired | Bool | |
| leakLocation | String? | If leak detected |
| systemCapacity | Double? | |
| a2lDissipationConfirmed | Bool | A2L mandatory checklist item |
| sparkFreeToolsUsed | Bool | A2L mandatory checklist item |
| ventilationVerified | Bool | A2L mandatory checklist item |
| ppeVerified | Bool | A2L mandatory checklist item |
| a2lLeakDetectorUsed | Bool | A2L mandatory checklist item |
| equipmentGrounded | Bool | A2L mandatory checklist item |
| emergencyPlanReviewed | Bool | A2L optional checklist item |
| maximumChargeVerified | Bool | H3 informational checklist item |
| installationLocationVerified | Bool | H3 informational checklist item |
| recoveryEquipmentRated | Bool | H3 informational checklist item |
| createdAt | Date | Record creation timestamp |
| isArchived | Bool | For EPA record retention |
| leakRateAtService | Double? | Leak rate at time of service |
| leakRateMethodRaw | String? | Leak rate calculation method used |
| technicianCertTypeRaw | String? | Technician certification type |
| isRetrofit | Bool | Whether service is a retrofit |
| isNewInstallation | Bool | Whether service is a new installation |
| isSeasonalVariance | Bool | Whether seasonal variance applies |
| notes | String? | |
| logDate | Date | |
| syncStatus | SyncStatus | Default: .pending |
| job | Job? | Inverse relationship |
| cylinder | RefrigerantCylinder? | Inverse relationship |
| invoice | Invoice? | Inverse relationship |
| isA2LRefrigerant | Bool | Computed from refrigerantType |

**SyncRecord**
| Field | Type | Notes |
|-------|------|-------|
| entityType | String | Model class name (17 entity types) |
| entityId | String | Local SwiftData identifier |
| operationRaw | String | create / update / delete |
| payload | Data? | Serialized entity data |
| statusRaw | String | pending / synced / failed / conflict |
| attemptCount | Int | Max 3 before marking failed |
| lastAttemptDate | Date? | |
| errorMessage | String? | |

**Estimate**
| Field | Type | Notes |
|-------|------|-------|
| estimateNumber | String | Auto-generated: EST-YYYYMMDD-XXXX |
| issueDate | Date | |
| expirationDate | Date? | Auto-expire when past |
| statusRaw | String | draft / sent / accepted / declined / expired |
| taxRate | Decimal | From AppSettings |
| signatureData | Data? | PNG from PencilKit |
| signedByName | String? | |
| signedDate | Date? | |
| client | Client? | Optional relationship |
| lineItems | [InvoiceLineItem] | Cascade delete; shared model with Invoice |

**EquipmentProfile**
| Field | Type | Notes |
|-------|------|-------|
| name | String | Required |
| location | String | |
| ownerName | String? | |
| operatorName | String? | |
| applianceType | ApplianceType | Commercial, Comfort, Industrial, Transport, Other |
| refrigerantType | RefrigerantType | Determines regulatory framework |
| fullChargeAmount | Decimal | In pounds |
| fullChargeMethod | FullChargeMethod | Nameplate / Calculation / Field Measurement |
| calculationMethod | String? | Method used for charge calculation |
| calculationMethodLockedAt | Date? | When calculation method was locked |
| safetyDocumentationUrl | String? | URL to A2L safety documentation |
| vehicleUnitId | String? | Vehicle/unit ID for transport refrigeration |
| routeDescription | String? | Route description for transport refrigeration |
| fullChargeNeedsRevalidation | Bool | Whether full charge requires revalidation |
| manufacturerName | String? | |
| modelNumber | String? | |
| serialNumber | String? | |
| installDate | Date? | |
| retirementDate | Date? | |
| regulatoryFramework | RegulatoryFramework | Section 608 / Subpart C |
| hasAutoLeakDetection | Bool | Informational only |
| client | Client? | Owner relationship |
| complianceLogs | [ComplianceLog] | Inverse relationship |
| leakRateRecords | [LeakRateRecord] | Inverse relationship |
| inspectionSchedules | [InspectionSchedule] | Inverse relationship |
| retrofitRetirementPlans | [RetrofitRetirementPlan] | Inverse relationship |
| chronicLeakerReports | [ChronicLeakerReport] | Inverse relationship |

**TechnicianProfile**
| Field | Type | Notes |
|-------|------|-------|
| name | String | |
| certNumber | String | EPA certification number |
| certType | CertificationType | Type I / II / III / Universal |
| certOrganization | String? | |
| certDate | Date? | |
| cardOnFile | Bool | EPA card on file |
| isDefault | Bool | Auto-fill source for compliance forms |

**InspectionSchedule**
| Field | Type | Notes |
|-------|------|-------|
| baseFrequency | Frequency | Default inspection interval |
| currentFrequency | Frequency | May be escalated |
| escalationReason | String? | Why frequency was increased |
| consecutiveCompliantQuarters | Int | For de-escalation tracking |
| nextInspectionDue | Date? | |
| lastInspectionDate | Date? | |
| equipmentProfile | EquipmentProfile? | Inverse relationship |

**LeakRateRecord**
| Field | Type | Notes |
|-------|------|-------|
| calculationDate | Date | |
| refrigerantAdded | Decimal | |
| fullChargeUsed | Decimal | |
| daysSinceLastCharge | Int | |
| calculatedRate | Double | Percentage |
| calculationMethod | String | Method 1 or Method 2 |
| exceedsThreshold | Bool | |
| applicableThreshold | Double | |
| framework | RegulatoryFramework | Section 608 or Subpart C |
| equipmentProfile | EquipmentProfile? | Inverse relationship |

**VerificationTest**
| Field | Type | Notes |
|-------|------|-------|
| linkedRepairId | UUID? | Links to ComplianceLog repair |
| testType | TestType | initial / followup |
| testDate | Date | |
| testMethod | String | |
| testResult | TestResult | passed / failed |
| locationsTested | String | |
| technicianName | String | |
| technicianCertNumber | String? | |

**RepairExtension**
| Field | Type | Notes |
|-------|------|-------|
| linkedRepairId | UUID? | Links to ComplianceLog repair |
| extensionType | ExtensionType | parts / weather / industrial / other |
| originalDeadline | Date | |
| requestedDeadline | Date | |
| justification | String | |
| epaNotified | Bool | |
| epaNotificationDate | Date? | |
| epaResponseReceived | Bool | |
| epaApproved | Bool | |

**RetrofitRetirementPlan**
| Field | Type | Notes |
|-------|------|-------|
| planType | PlanType | retrofit / retirement |
| triggeringLeakDate | Date? | |
| oneYearDeadline | Date? | Auto-calculated: triggeringLeakDate + 365 |
| estimatedCompletionDate | Date? | |
| actualCompletionDate | Date? | |
| targetRefrigerantType | RefrigerantType? | For retrofits |
| isCompleted | Bool | |
| equipmentProfile | EquipmentProfile? | Inverse relationship |

**ChronicLeakerReport**
| Field | Type | Notes |
|-------|------|-------|
| calendarYear | Int | Defaults to current year |
| facilityName | String | |
| facilityAddress | String | |
| applianceDescription | String | |
| fullChargeAmount | Decimal | |
| cumulativeAdditions | Decimal | |
| calculatedPercentage | Double | additions/charge × 100 |
| repairEfforts | String | |
| isSubmitted | Bool | |
| submissionDate | Date? | |
| submissionMethod | String? | |
| equipmentProfile | EquipmentProfile? | Inverse relationship |

**RefrigerantDisposition**
| Field | Type | Notes |
|-------|------|-------|
| linkedServiceId | UUID? | Links to ComplianceLog |
| dispositionType | DispositionType | reclaimed / recycled / destroyed / returned / stored |
| quantity | Decimal | Pounds |
| refrigerantType | RefrigerantType | |
| recipientName | String? | |
| recipientAddress | String? | |
| recipientCertification | String? | |
| manifestNumber | String? | Shipping manifest |
| dispositionDate | Date | |

### 6.3 Enums

| Enum | Values | Notes |
|------|--------|-------|
| JobType | installation, repair, maintenance, inspection, leakTest, recovery, recharge | 7 cases |
| JobStatus | scheduled, inProgress, completed, invoiced, cancelled | Drives status workflow and filtering |
| RefrigerantType | r22, r410A, r407C, r134a, r404A, r32, r454B, r452B, r466A, r290, r744, r717 | 12 cases; properties: `isA2L`, `gwp`, `safetyClass`, `isODS`, `isHFC`, `category`, `regulatoryFramework` |
| RefrigerantCategory | legacy, a2l, natural | Groups refrigerant types |
| ComplianceActionType | recovery, recharge, leakTest, leakRepair, installation, decommission, initialVerification, followupVerification, quarterlyInspection, annualInspection, retrofit, retirement, seasonalVariance | 13 cases |
| LineItemCategory | labor, material, refrigerant, discount, other | Used for line item categorization |
| LineItemType | labor, parts, refrigerant, service, diagnostic, other | Invoice line item types |
| InvoiceStatus | draft, sent, viewed, paid, overdue, void_status | Invoice lifecycle |
| EstimateStatus | draft, sent, accepted, declined, expired | Estimate lifecycle |
| SyncStatus | pending, synced, failed, conflict | Stored as raw value on all syncable models |
| SyncOperation | create, update, delete | Sync queue operations |
| ConnectionStatus | connected, disconnected, connecting | Network status |
| ApplianceType | commercialRefrigeration, comfortCooling, industrialProcess, refrigeratedTransport, other | EPA appliance classification |
| RegulatoryFramework | section608, subpartC | EPA framework with charge thresholds |
| CertificationType | typeI, typeII, typeIII, universal | EPA 608 technician cert types |
| DispositionType | reclaimed, recycled, destroyed, returnedToManufacturer, storedOnSite | Refrigerant disposal chain |
| FullChargeMethod | nameplate, calculation, fieldMeasurement | Equipment charge determination |
| ExportFormat | json, csv, pdf | Used in DataExportService |
| RecurrencePattern | daily, weekly, biweekly, monthly, quarterly | Job recurrence |
| InspectionSchedule.Frequency | monthly, quarterly, semiannual, annual | Nested in InspectionSchedule |
| VerificationTest.TestType | initial, followup | Nested in VerificationTest |
| VerificationTest.TestResult | passed, failed | Nested in VerificationTest |
| RepairExtension.ExtensionType | parts, weather, industrial, other | Nested in RepairExtension |
| RetrofitRetirementPlan.PlanType | retrofit, retirement | Nested in RetrofitRetirementPlan |

### 6.4 Schema Migration

- **SchemaV1** — Current schema with all 17 models
- **FieldPadMigrationPlan** — Migration plan exists with empty stages (no migrations yet)

---

## 7. System Permissions

### 7.1 Runtime Permissions

| Permission | Key | Usage | User-Facing Description |
|------------|-----|-------|------------------------|
| Camera | `NSCameraUsageDescription` | OCR scanning of refrigerant cylinder labels | "FieldPad needs camera access to scan refrigerant cylinder labels for automatic data entry." |

No other runtime permissions are requested. The app does not use location services, health data, contacts, microphone, or photo library access (PhotosPicker uses the system picker which does not require a permission prompt).

### 7.2 Entitlements

| Entitlement | Value | Status |
|-------------|-------|--------|
| `com.apple.developer.in-app-payments` | `merchant.com.hugglerholdings.fieldpad` | **Active** — in-app payment merchant capability |
| `aps-environment` | `development` | **Active** — push notifications for CloudKit sync |
| iCloud Containers | `iCloud.com.hugglerholdings.fieldpad` | **Active** — CloudKit private database |
| CloudKit | Development | **Active** — SwiftData+CloudKit automatic sync |

### 7.3 Background Modes

| Mode | Purpose |
|------|---------|
| `remote-notification` | CloudKit sync push notifications |

### 7.4 Info.plist Configuration

- **Launch screen**: Auto-generated (`UILaunchScreen_Generation = YES`); no storyboard
- **Supported orientations (iPad)**: All four orientations
- **Supported orientations (iPhone)**: Portrait, Landscape Left, Landscape Right
- **Bundle ID**: `com.hugglerholdings.fieldpad`
- **Display name**: "FieldPad: HVAC Service Pro"
