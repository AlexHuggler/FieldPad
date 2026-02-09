# FieldPad Project Map

## Architecture Summary

| Aspect | Detail |
|--------|--------|
| **Framework** | Pure SwiftUI + SwiftData (iOS 17+) |
| **Pattern** | Observable Services + @Query Views (no ViewModels) |
| **Persistence** | SwiftData with iCloud sync via CloudKit private database |
| **Networking** | No backend API — 100% offline-first, local-only |
| **Dependencies** | RevenueCat (subscriptions) — only third-party dep, managed via Xcode project |
| **State Management** | `@Observable @MainActor` singletons injected via `.environment()` |
| **Target** | iOS 17+ (iPhone + iPad) |

---

## Entry Point Flow

```
FieldPadApp.swift (@main)
  |
  +-- AppDelegate (UIApplicationDelegate)
  |     +-- Registers for remote notifications
  |     +-- Sets UNUserNotificationCenter delegate
  |     +-- Forwards CloudKit notifications to ICloudService
  |
  +-- Creates ModelContainer (17 SwiftData models)
  |     Schema: Client, Job, Invoice, InvoiceLineItem, RefrigerantCylinder,
  |             ComplianceLog, SyncRecord, Estimate, EquipmentProfile,
  |             LeakRateRecord, VerificationTest, RepairExtension,
  |             RetrofitRetirementPlan, InspectionSchedule, ChronicLeakerReport,
  |             RefrigerantDisposition, TechnicianProfile
  |     Config: isStoredInMemoryOnly=false, allowsSave=true, no CloudKit
  |
  +-- Initializes 7 service singletons as @State
  |     ThemeManager, SyncEngine, ICloudService, NotificationManager,
  |     DeepLinkRouter, SubscriptionManager, ComplianceEngine
  |
  +-- Routes to:
  |     IF !hasCompletedOnboarding -> OnboardingView
  |     ELSE -> MainTabView
  |
  +-- .task {} configures services with modelContext
  |     subscriptionManager.configure()
  |     syncEngine.configure(with: container.mainContext)
  |     iCloudService.configure(with: syncEngine)
  |     notificationManager.configure(with: container.mainContext)
  |     complianceEngine.configure(with: container.mainContext)
  |     BackupService.shared.checkAndPerformBackup(...)
  |
  +-- .environment() injects all services into view hierarchy
  +-- .applyTheme(themeManager) applies theming
  +-- .withToastOverlay() adds toast notification system
  +-- .onChange(scenePhase) tracks sessions, refreshes notifications
  +-- .onReceive(databaseSaveError) shows alert on save failures
```

---

## Module Dependency Map

```
Views (50+ files, 15 feature directories)
  +-- @Query -> SwiftData Models (17 entities)
  +-- @Environment -> Services (7 singletons)
  +-- Uses -> DesignSystem (25+ components) + Theme (4 files)

Services (25+ files)
  +-- Uses -> SwiftData ModelContext
  +-- Uses -> AppSettings (UserDefaults wrapper)
  +-- ComplianceEngine -> LeakRateCalculator, EPAReportGenerator
  +-- SyncEngine -> NetworkMonitor, SyncStatusManager
  +-- NotificationManager -> NotificationDelegate
  +-- CylinderScannerService -> TextRecognitionEngine
  +-- SubscriptionManager -> RevenueCat SDK

Models (17 @Model classes + 10 enum files)
  +-- SwiftData @Model classes with relationships
  +-- Enums stored as raw strings (SwiftData limitation)
  +-- Computed properties for enum get/set conversions
```

---

## SwiftData Models (17 entities)

### Core Business Entities

| Model | Key Relationships | Cascade Rules |
|-------|-------------------|---------------|
| **Client** | -> jobs: [Job], -> estimates: [Estimate], -> equipmentProfiles: [EquipmentProfile] | jobs: `.cascade`, estimates: `.cascade`, equipment: `.nullify` |
| **Job** | -> client: Client?, -> invoice: Invoice?, -> complianceLogs: [ComplianceLog] | invoice: `.cascade`, complianceLogs: `.cascade` |
| **Invoice** | -> job: Job?, -> lineItems: [InvoiceLineItem], -> complianceLogs: [ComplianceLog] | lineItems: `.cascade`, complianceLogs: default |
| **InvoiceLineItem** | -> invoice: Invoice?, -> estimate: Estimate? | — |
| **Estimate** | -> client: Client?, -> lineItems: [InvoiceLineItem] | lineItems: `.cascade` |

### EPA Compliance Entities

| Model | Key Relationships |
|-------|-------------------|
| **ComplianceLog** | -> job, -> invoice, -> cylinder, -> equipmentProfile (all optional) |
| **EquipmentProfile** | -> client, -> complianceLogs, -> leakRateRecords, -> inspectionSchedules, -> retrofitRetirementPlans, -> chronicLeakerReports |
| **LeakRateRecord** | -> equipmentProfile |
| **VerificationTest** | linkedRepairIdString (UUID string ref to ComplianceLog) |
| **RepairExtension** | linkedRepairIdString (UUID string ref to ComplianceLog) |
| **RetrofitRetirementPlan** | -> equipmentProfile |
| **InspectionSchedule** | -> equipmentProfile |
| **ChronicLeakerReport** | -> equipmentProfile |
| **RefrigerantDisposition** | linkedServiceIdString (UUID string ref to ComplianceLog) |

### Infrastructure Entities

| Model | Purpose |
|-------|---------|
| **RefrigerantCylinder** | Inventory tracking with OCR scan, weight tracking, archival |
| **TechnicianProfile** | EPA certification info, auto-fill for compliance logs |
| **SyncRecord** | Offline sync queue (entity type + operation + retry state) |

---

## Services (25+ files)

### Environment-Injected Singletons (7)

| Service | File | Responsibility |
|---------|------|----------------|
| **ThemeManager** | `Theme/ThemeManager.swift` | 3 themes (system, OLED black, neon yellow), Glove Mode toggle |
| **SyncEngine** | `Services/Sync/SyncEngine.swift` | Offline sync queue, retry with exponential backoff, network monitoring |
| **ICloudService** | `Services/Sync/ICloudService.swift` | CloudKit account status + remote notification registration (active) |
| **NotificationManager** | `Services/Notifications/NotificationManager.swift` | 8 notification types, debouncing, cooldowns, work hours |
| **SubscriptionManager** | `Services/SubscriptionManager.swift` | RevenueCat integration, feature gating, paywall UI |
| **ComplianceEngine** | `Services/Compliance/ComplianceEngine.swift` | Leak rate calculations, chronic leaker checks, inspection scheduling |
| **DeepLinkRouter** | (shared singleton) | Deep link / quick action routing |

### Static/Shared Singletons

| Service | File | Responsibility |
|---------|------|----------------|
| **AppSettings** | `Services/AppSettings.swift` | UserDefaults wrapper for all app preferences |
| **NetworkMonitor** | `Services/Sync/NetworkMonitor.swift` | NWPathMonitor wrapper, connection status |
| **SyncStatusManager** | `Services/Sync/SyncStatusManager.swift` | Aggregate sync status reporting |
| **BackupService** | `Services/BackupService.swift` | Auto/manual JSON backups with rotation (max 5) |
| **ReviewRequestService** | `Services/ReviewRequestService.swift` | App Store review prompt tracking |
| **HapticManager** | `Services/Utilities/HapticManager.swift` | Haptic feedback utilities |

### Pure Logic / Utility Services

| Service | File | Responsibility |
|---------|------|----------------|
| **LeakRateCalculator** | `Services/Compliance/LeakRateCalculator.swift` | EPA Section 608 & Subpart C calculations |
| **EPAReportGenerator** | `Services/Compliance/EPAReportGenerator.swift` | EPA compliance report generation |
| **DatabaseSaveHelper** | `Services/DatabaseSaveHelper.swift` | Centralized SwiftData save with error handling |
| **ValidationHelper** | `Services/ValidationHelper.swift` | Form validation utilities |
| **DataExportService** | `Services/DataExportService.swift` | Export all data to JSON/CSV |
| **DataImportService** | `Services/DataImportService.swift` | Import data from external sources |
| **CylinderScannerService** | `Services/Vision/CylinderScannerService.swift` | AVFoundation camera + OCR scanning |
| **TextRecognitionEngine** | `Services/Vision/TextRecognitionEngine.swift` | Vision framework text extraction |
| **FormDraftService** | `Services/FormDraftService.swift` | Unsaved form draft persistence |
| **LineItemTemplateService** | `Services/LineItemTemplateService.swift` | Reusable invoice line item templates |
| **ScheduleConflictDetector** | `Services/ScheduleConflictDetector.swift` | Job scheduling overlap detection |

---

## Views (50+ files, 15 feature directories)

### Navigation
- `Views/Navigation/MainTabView.swift` — 5-tab root (Dashboard, Jobs, Clients, Inventory, Settings) + floating action button

### Dashboard (1 file)
- `Views/Dashboard/DashboardView.swift` — Stats overview, active jobs, unpaid invoices, active estimates

### Jobs (6 files)
- `JobListView.swift` — Job list with filter/search, list/schedule toggle
- `JobDetailView.swift` — Job details with compliance logs, invoice link
- `JobFormView.swift` — Create/edit job form
- `ComplianceLogFormView.swift` — EPA compliance log entry
- `ComplianceLogJobPickerView.swift` — Job picker for compliance logs
- `ScheduleView.swift` — Calendar schedule view

### Clients (3 files)
- `ClientListView.swift` — Client list with search, swipe actions (call/SMS/email)
- `ClientDetailView.swift` — Client details with jobs/estimates
- `ClientFormView.swift` — Create/edit client form

### Inventory (5 files)
- `InventoryView.swift` — Cylinder list with summary cards, low stock alerts
- `CylinderFormView.swift` — Create/edit cylinder
- `CylinderDetailView.swift` — Cylinder details with usage history
- `CylinderExchangeSheet.swift` — Cylinder exchange workflow
- `ScanCylinderSheet.swift` — OCR camera scanning sheet

### Compliance (10 files)
- `ComplianceDashboardView.swift` — Compliance overview with alerts
- `PendingVerificationsView.swift` — Repairs awaiting verification test
- `OverdueRepairsView.swift` — Overdue leak repairs
- `LeakRateView.swift` — Leak rate calculator UI
- `VerificationTestFormView.swift` — Verification test entry
- `RefrigerantDispositionFormView.swift` — Refrigerant disposition tracking
- `RepairExtensionFormView.swift` — EPA repair extension request
- `RetrofitRetirementFormView.swift` — Retrofit/retirement plan
- `ChronicLeakerReportView.swift` — Chronic leaker annual report
- `ComplianceAuditExportView.swift` — Audit export

### Invoicing (7 files)
- `InvoiceListView.swift`, `InvoiceDetailView.swift`, `InvoiceFormView.swift`
- `InvoiceSignatureSheet.swift`, `RecordPaymentSheet.swift`
- `SignaturePadView.swift` — Digital signature capture
- `A2LComplianceModal.swift` — A2L refrigerant safety checklist

### Estimates (4 files)
- `EstimateListView.swift`, `EstimateDetailView.swift`, `EstimateFormView.swift`
- `EstimateLineItemFormView.swift`

### Equipment (4 files)
- `EquipmentListView.swift`, `EquipmentDetailView.swift`, `EquipmentFormView.swift`
- `InspectionScheduleView.swift`

### Settings (5 files)
- `SettingsView.swift` — Main settings with theme, glove mode, notifications, backup
- `TechnicianProfilesView.swift`, `TechnicianFormView.swift`
- `FreeLimitsView.swift` — Subscription usage dashboard
- `DataImportSheet.swift`

### Other
- `Views/Onboarding/OnboardingView.swift`
- `Views/Tools/FiveThousandRuleView.swift` — EPA 5,000 lb rule calculator
- `Views/Errors/DatabaseErrorView.swift`

---

## Design System (25+ components)

### Form Inputs
- `GloveButton.swift` — Primary button (4 styles: primary, secondary, destructive, ghost)
- `GloveTextField.swift` — Text input with icon + Glove Mode sizing
- `GloveTextAreaField.swift` — Multiline text area
- `GloveDatePickerField.swift` — Date picker
- `AddressSearchField.swift` — MapKit address autocomplete with geocoding

### Display Components
- `StatCard.swift` — Statistics card with icon/value/title
- `DetailRow.swift` — Key-value pair display
- `SectionHeader.swift` — Section header with icon
- `FilterChip.swift` — Selectable filter with count badge
- `EmptyStateCard.swift` — Empty state placeholder with action
- `CylinderGaugeView.swift` — Visual fill gauge for cylinders
- `UsageLimitBanner.swift` — Subscription usage indicator
- `SyncStatusBadge.swift` — Sync status badge

### Overlays & Modals
- `ToastView.swift` — Toast notification overlay
- `SaveSuccessOverlay.swift` — Save success animation
- `LoadingOverlay.swift` — Loading spinner overlay
- `ConfirmationDialog.swift` — Confirmation dialog
- `HelpTooltip.swift` — Help tooltip popover

### Utilities
- `SwipeToConfirmButton.swift` — Swipe gesture confirmation
- `UnsavedChangesModifier.swift` — Unsaved changes warning
- `StaggeredAppearance.swift` — Staggered list animation
- `FormValidationMessage.swift` — Validation error display
- `Formatters.swift` — FieldPadFormatters utility struct
- `USStates.swift` — US state abbreviations enum

---

## Theme System (4 files)

| File | Content |
|------|---------|
| `ThemeManager.swift` | `@Observable` singleton: currentTheme, isGloveModeEnabled, colors/spacing/typography |
| `ThemeColors.swift` | 3 palettes: system (iOS defaults), OLED black (cyan accent), neon yellow (high contrast) |
| `ThemeSpacing.swift` | 2 presets: standard (44pt tap target) vs glove mode (60pt tap target) |
| `ThemeTypography.swift` | 2 presets: standard (system fonts) vs glove mode (20pt body, enlarged headings) |

---

## Enum Files (10 files in Models/Enums/)

| File | Enums Defined |
|------|---------------|
| `JobStatus.swift` | JobStatus (scheduled/inProgress/completed/invoiced/cancelled), JobType (7 types) |
| `ApplianceType.swift` | ApplianceType (5 types with regulatory framework mapping) |
| `RefrigerantType.swift` | RefrigerantType (12 refrigerants with GWP, safety class, ODS/HFC flags) |
| `DispositionType.swift` | DispositionType (5 disposition methods) |
| `SyncStatus.swift` | SyncStatus (4 states), SyncOperation (create/update/delete), ConnectionStatus |
| `CertificationType.swift` | CertificationType (Type I/II/III/Universal EPA 608) |
| `RegulatoryFramework.swift` | RegulatoryFramework (section608, subpartC) with charge thresholds |
| `RecurrencePattern.swift` | RecurrencePattern (daily/weekly/biweekly/monthly/quarterly) |
| `FullChargeMethod.swift` | FullChargeMethod (nameplate/calculation/fieldMeasurement) |

---

## Test Files (18 files)

### Unit Tests (16 files in FieldPadTests/)
- `FieldPadTests.swift` — Core app tests
- `ModelTests.swift` — Model validation
- `ValidationHelperTests.swift` — Form validation
- `ChronicLeakerTests.swift` — Chronic leaker detection
- `LeakRateCalculatorTests.swift` — EPA leak rate calculations
- `JobStatusTests.swift` — Job status state machine
- `RecurrencePatternTests.swift` — Recurring job date calculations
- `RefrigerantTypeTests.swift` — Refrigerant properties
- `InvoiceEmailTests.swift` — Invoice email generation
- `CylinderEdgeCaseTests.swift` — Cylinder edge cases
- `InvoiceTaxEdgeCaseTests.swift` — Invoice tax edge cases
- `ScheduleConflictTests.swift` — Schedule conflict detection
- `RecurrencePatternEdgeCaseTests.swift` — Recurrence pattern edge cases
- `ComplianceEngineEdgeCaseTests.swift` — Compliance engine edge cases
- `DataImportEdgeCaseTests.swift` — Data import edge cases
- `SubscriptionGatingTests.swift` — Subscription gating logic

### UI Tests (2 files in FieldPadUITests/)
- `FieldPadUITests.swift` — UI automation
- `FieldPadUITestsLaunchTests.swift` — Launch performance
