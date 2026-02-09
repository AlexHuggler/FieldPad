# Feature Inventory — FieldPad

> Audit date: 2026-02-08

---

## 1. @Model Classes (17)

| # | Model | File | Fields | Relationships | Key Enums |
|---|---|---|---|---|---|
| 1 | `Client` | `Models/Client.swift` | 13 | jobs, estimates, equipmentProfiles | — |
| 2 | `Job` | `Models/Job.swift` | 19 | client, invoice, complianceLogs | JobType (7), JobStatus (5), RecurrencePattern (5) |
| 3 | `Invoice` | `Models/Invoice.swift` | 20 | job, lineItems (cascade), complianceLogs | InvoiceStatus (6) |
| 4 | `InvoiceLineItem` | `Models/InvoiceLineItem.swift` | 10 | invoice, estimate | LineItemType (6), LineItemCategory (5) |
| 5 | `Estimate` | `Models/Estimate.swift` | 13 | client, lineItems (cascade) | EstimateStatus (5) |
| 6 | `RefrigerantCylinder` | `Models/RefrigerantCylinder.swift` | 26 | usageLogs | FillStatus (4), CylinderCondition (4) |
| 7 | `ComplianceLog` | `Models/ComplianceLog.swift` | 30 | job, invoice, cylinder, equipmentProfile | ComplianceActionType (13) |
| 8 | `SyncRecord` | `Models/SyncRecord.swift` | 11 | — | SyncEntityType (17) |
| 9 | `EquipmentProfile` | `Models/EquipmentProfile.swift` | 20 | client, complianceLogs, leakRateRecords, inspectionSchedules, retrofitRetirementPlans, chronicLeakerReports | — |
| 10 | `TechnicianProfile` | `Models/TechnicianProfile.swift` | 12 | — | — |
| 11 | `InspectionSchedule` | `Models/InspectionSchedule.swift` | 11 | equipmentProfile | Frequency (4) |
| 12 | `LeakRateRecord` | `Models/LeakRateRecord.swift` | 12 | equipmentProfile | — |
| 13 | `VerificationTest` | `Models/VerificationTest.swift` | 12 | — | TestType (2), TestResult (2) |
| 14 | `RepairExtension` | `Models/RepairExtension.swift` | 14 | — | ExtensionType (4) |
| 15 | `RetrofitRetirementPlan` | `Models/RetrofitRetirementPlan.swift` | 16 | equipmentProfile | PlanType (2) |
| 16 | `ChronicLeakerReport` | `Models/ChronicLeakerReport.swift` | 16 | equipmentProfile | — |
| 17 | `RefrigerantDisposition` | `Models/RefrigerantDisposition.swift` | 13 | — | — |

---

## 2. Service Classes (26)

### Observable Services (15)

| # | Service | File | Pattern | Responsibility |
|---|---|---|---|---|
| 1 | `SyncEngine` | `Services/Sync/SyncEngine.swift` | @Observable @MainActor | Offline-first sync with network monitoring, exponential backoff |
| 2 | `ComplianceEngine` | `Services/Compliance/ComplianceEngine.swift` | @Observable @MainActor | EPA leak rate (exemption-aware leak rate calculation), inspection tracking, chronic leaker detection |
| 3 | `NotificationManager` | `Services/Notifications/NotificationManager.swift` | @Observable @MainActor | 12 notification types with scheduling, permissions, deep linking |
| 4 | `SubscriptionManager` | `Services/SubscriptionManager.swift` | @Observable @MainActor | RevenueCat Pro feature gating (16 ProFeature cases) |
| 5 | `FormDraftService` | `Services/FormDraftService.swift` | @Observable @MainActor singleton | UserDefaults form draft persistence |
| 6 | `BackupService` | `Services/BackupService.swift` | @Observable @MainActor singleton | Auto-backup with rotation (max 5, default 7-day interval) |
| 7 | `ReviewRequestService` | `Services/ReviewRequestService.swift` | @Observable @MainActor singleton | StoreKit review requests with 60-day cooldown |
| 8 | `AppSettings` | `Services/AppSettings.swift` | @Observable @MainActor singleton | Central UserDefaults config (business, tech, notifications) |
| 9 | `CylinderScannerService` | `Services/Vision/CylinderScannerService.swift` | @Observable @MainActor | AVFoundation OCR scanner with phase machine |
| 10 | `NetworkMonitor` | `Services/Sync/NetworkMonitor.swift` | @Observable @MainActor singleton | NWPathMonitor connectivity status |
| 11 | `SyncStatusManager` | `Services/Sync/SyncStatusManager.swift` | @Observable @MainActor | Sync status aggregation for UI display |
| 12 | `LineItemTemplateService` | `Services/LineItemTemplateService.swift` | @Observable @MainActor singleton | 18 preset + dynamic invoice line item templates |
| 13 | `ThemeManager` | `Theme/ThemeManager.swift` | @Observable | 3 themes + Glove Mode (60pt min targets) |
| 14 | `DeepLinkRouter` | `Services/Notifications/NotificationDelegate.swift` | @Observable @MainActor singleton | Notification tap → destination routing |
| 15 | `ICloudService` | `Services/Sync/ICloudService.swift` | @Observable @MainActor | CloudKit account status + remote notification registration (active) |

### Utility Services (11)

| # | Service | File | Pattern | Responsibility |
|---|---|---|---|---|
| 16 | `ScheduleConflictDetector` | `Services/ScheduleConflictDetector.swift` | Static struct | Job scheduling overlap detection |
| 17 | `EPAReportGenerator` | `Services/Compliance/EPAReportGenerator.swift` | Static struct | Chronic leaker reports, compliance CSV exports |
| 18 | `LeakRateCalculator` | `Services/Compliance/LeakRateCalculator.swift` | Static struct | EPA 608 & AIM Act leak rate calculations |
| 19 | `ValidationHelper` | `Services/ValidationHelper.swift` | Static enum | Email, phone, zip, decimal validation |
| 20 | `DatabaseSaveHelper` | `Services/DatabaseSaveHelper.swift` | Static enum | SwiftData save with 2-attempt retry |
| 21 | `HapticManager` | `Services/Utilities/HapticManager.swift` | Singleton class | UIKit haptic feedback (12 methods) |
| 22 | `CylinderNotificationHandler` | `Services/Notifications/CylinderNotificationHandler.swift` | @MainActor class | Inventory notification with 24hr cooldown |
| 23 | `DataExportService` | `Services/DataExportService.swift` | @MainActor class | JSON/CSV/PDF export for all entities |
| 24 | `DataImportService` | `Services/DataImportService.swift` | @MainActor class | JSON import with preview and duplicate detection |
| 25 | `TextRecognitionEngine` | `Services/Vision/TextRecognitionEngine.swift` | Class | Vision framework OCR for cylinder labels |
| 26 | `ComplianceDeletionGuard` | `Services/ComplianceDeletionGuard.swift` | Static enum | Retention-aware delete/archive routing for compliance logs |
| 27 | `NotificationDelegate` | `Services/Notifications/NotificationDelegate.swift` | NSObject delegate | UNUserNotificationCenter delegate + deep linking |

---

## 3. Enums (Standalone Files)

| # | Enum | File | Cases |
|---|---|---|---|
| 1 | `RefrigerantType` | `Models/Enums/RefrigerantType.swift` | 12 (r22, r410A, r407C, r134a, r404A, r32, r454B, r452B, r466A, r290, r744, r717) |
| 2 | `RefrigerantCategory` | `Models/Enums/RefrigerantType.swift` | 3 (legacy, a2l, natural) |
| 3 | `SyncStatus` | `Models/Enums/SyncStatus.swift` | 4 (pending, synced, failed, conflict) |
| 4 | `SyncOperation` | `Models/Enums/SyncStatus.swift` | 3 (create, update, delete) |
| 5 | `ConnectionStatus` | `Models/Enums/SyncStatus.swift` | 3 (connected, disconnected, connecting) |
| 6 | `ApplianceType` | `Models/Enums/ApplianceType.swift` | 5 (commercialRefrigeration, comfortCooling, industrialProcess, refrigeratedTransport, other) |
| 7 | `RegulatoryFramework` | `Models/Enums/RegulatoryFramework.swift` | 2 (section608, subpartC) |
| 8 | `CertificationType` | `Models/Enums/CertificationType.swift` | 4 (typeI, typeII, typeIII, universal) |
| 9 | `DispositionType` | `Models/Enums/DispositionType.swift` | 5 (reclaimed, recycled, destroyed, returnedToManufacturer, storedOnSite) |
| 10 | `FullChargeMethod` | `Models/Enums/FullChargeMethod.swift` | 3 (nameplate, calculation, fieldMeasurement) |

---

## 4. View Files (49)

### Navigation (1)
| File | Description |
|---|---|
| `MainTabView.swift` | Root 5-tab container + FAB with 7 quick actions |

### Dashboard (1)
| File | Description |
|---|---|
| `DashboardView.swift` | At-a-glance metrics: active jobs, revenue, cylinder counts, low stock |

### Jobs (6)
| File | Description |
|---|---|
| `JobListView.swift` | Job list with search, status filters, list/schedule toggle |
| `JobDetailView.swift` | Job detail with status actions, client link, compliance logs |
| `JobFormView.swift` | Create/edit job with validation, recurring toggle, address autocomplete |
| `ScheduleView.swift` | Calendar timeline view (6 AM–8 PM hourly grid) |
| `ComplianceLogFormView.swift` | EPA compliance log entry form with exemption HelpTooltip, triggeringLog integration |
| `ComplianceLogJobPickerView.swift` | Job picker for compliance log association |

### Clients (3)
| File | Description |
|---|---|
| `ClientListView.swift` | Client directory with search, usage limit banner |
| `ClientDetailView.swift` | Client detail with contact actions, recent jobs |
| `ClientFormView.swift` | Create/edit client with type picker, address autocomplete |

### Inventory (5)
| File | Description |
|---|---|
| `InventoryView.swift` | Cylinder list with filters (All, Low Stock, A2L) |
| `CylinderDetailView.swift` | Cylinder detail with gauge, quick actions, usage history |
| `CylinderFormView.swift` | Create/edit cylinder with weight validation |
| `ScanCylinderSheet.swift` | Camera OCR scanning with flash, zoom, focus |
| `CylinderExchangeSheet.swift` | Cylinder exchange workflow |

### Compliance (10)
| File | Description |
|---|---|
| `ComplianceDashboardView.swift` | Dashboard wrapper with NavigationStack |
| `ComplianceDashboardContentView.swift` | Alerts, warnings, stats with regulatory HelpTooltips on stat cards, recent activity |
| `VerificationTestFormView.swift` | Post-repair verification test entry with follow-up deadline warning |
| `RepairExtensionFormView.swift` | EPA repair deadline extension request |
| `RefrigerantDispositionFormView.swift` | Refrigerant disposal chain of custody |
| `RetrofitRetirementFormView.swift` | Equipment retrofit/retirement planning |
| `OverdueRepairsView.swift` | List of overdue leak repairs |
| `PendingVerificationsView.swift` | List of pending verification tests |
| `ChronicLeakerReportView.swift` | 125% chronic leaker report view |
| `LeakRateView.swift` | Leak rate analysis over time |

### Estimates (4)
| File | Description |
|---|---|
| `EstimateListView.swift` | Estimate list with filtering (All, Draft, Sent, Accepted) |
| `EstimateDetailView.swift` | Estimate detail with totals and signature |
| `EstimateFormView.swift` | Create/edit estimate |
| `EstimateLineItemFormView.swift` | Add line items to estimates |

### Invoicing (7)
| File | Description |
|---|---|
| `InvoiceListView.swift` | Invoice list with status dots and filtering |
| `InvoiceDetailView.swift` | Invoice detail with line items, totals, signature |
| `InvoiceFormView.swift` | Create/edit invoice |
| `InvoiceSignatureSheet.swift` | PencilKit signature capture |
| `SignaturePadView.swift` | Drawing canvas for signatures |
| `RecordPaymentSheet.swift` | Payment recording with partial/full support |
| `A2LComplianceModal.swift` | A2L safety checklist for refrigerant line items |

### Equipment (4)
| File | Description |
|---|---|
| `EquipmentListView.swift` | Equipment list with framework/appliance type filters |
| `EquipmentDetailView.swift` | Equipment detail with specifications, net refrigerant summary section |
| `EquipmentFormView.swift` | Register/edit equipment |
| `InspectionScheduleView.swift` | Inspection schedule management |

### Settings (5)
| File | Description |
|---|---|
| `SettingsView.swift` | Settings hub: business profile, tools, data management, subscription |
| `TechnicianProfilesView.swift` | Multi-technician profile management |
| `TechnicianFormView.swift` | Add/edit technician profiles |
| `DataImportSheet.swift` | JSON data import with preview |
| `FreeLimitsView.swift` | Free tier usage limits display |

### Onboarding (1)
| File | Description |
|---|---|
| `OnboardingView.swift` | 5-page onboarding carousel with technician info collection |

### Tools (1)
| File | Description |
|---|---|
| `FiveThousandRuleView.swift` | 5,000 Rule calculator with interactive chart |

### Error Handling (1)
| File | Description |
|---|---|
| `DatabaseErrorView.swift` | ModelContainer initialization failure display |

---

## 5. Design System Components (24)

### Input Components (4)
| Component | Description |
|---|---|
| `GloveButton` | Large tap-target button (primary/secondary/destructive/ghost) |
| `GloveTextField` | Enlarged text field with icon, keyboard type, required indicator |
| `GloveDatePickerField` | Date picker wrapped for Glove Mode |
| `GloveTextAreaField` | Multi-line text area for gloved use |

### Visual Components (6)
| Component | Description |
|---|---|
| `CylinderGaugeView` | Circular gauge (small/medium/large) for cylinder fill % |
| `StatCard` | Dashboard stat card with icon and value |
| `DetailRow` | Key-value row for detail views |
| `SectionHeader` | Section header with optional action |
| `FilterChip` | Selectable filter chip for list views |
| `EmptyStateCard` | Placeholder with icon, title, subtitle, action button |

### Feedback & Status (6)
| Component | Description |
|---|---|
| `SyncStatusBadge` | Sync status indicator (pending/synced/error/conflict) |
| `ToastView` | Temporary message notification |
| `SaveSuccessOverlay` | Full-screen save confirmation |
| `LoadingOverlay` | Loading spinner overlay |
| `UsageLimitBanner` | Subscription limit warning with progress bar |
| `ConfirmationDialog` | Destructive action confirmation |

### Utility Components (5)
| Component | Description |
|---|---|
| `SwipeToConfirmButton` | Swipe gesture for destructive/important actions |
| `HelpTooltip` | Contextual help tooltip |
| `UnsavedChangesModifier` | Navigation guard for unsaved changes |
| `StaggeredAppearance` | List item animation modifier |
| `AddressSearchField` | MapKit address autocomplete |

### Data Utilities (3)
| Component | Description |
|---|---|
| `Formatters` | Currency, date, and number formatting |
| `USStates` | US state abbreviation lookup |
| `ScaleButtonStyle` | Spring-animated button press effect |

---

## 6. Third-Party Dependencies

| SDK | Import | Purpose | Files |
|---|---|---|---|
| RevenueCat | `import RevenueCat` | Subscription management, purchase validation | `SubscriptionManager.swift`, `FieldPadApp.swift` |
| RevenueCatUI | `import RevenueCatUI` | Pre-built paywall UI | `SubscriptionManager.swift`, `SettingsView.swift` |

**No other third-party dependencies.** No SPM packages, CocoaPods, or Carthage.

---

## 7. System Permissions & Entitlements

### Runtime Permissions
| Permission | Info.plist Key | Usage |
|---|---|---|
| Camera | `NSCameraUsageDescription` | OCR cylinder label scanning |
| Photos | `NSPhotoLibraryUsageDescription` | **MISSING** — needed for logo picker |

> **Action Required:** `NSPhotoLibraryUsageDescription` is still missing from Info.plist. The business logo picker in Settings requires photo library access. App Store review may reject without this key if the logo picker triggers a photo library prompt.

### Entitlements
| Entitlement | Value | Status |
|---|---|---|
| In-App Payments | `com.apple.developer.in-app-payments` | Active |
| Push Notifications | `aps-environment: development` | Active |
| iCloud Containers | `iCloud.com.hugglerholdings.fieldpad` | Active |
| CloudKit | Development | Active |

> **Note:** All entitlements are active. CloudKit, push notifications, and iCloud containers support SwiftData+CloudKit automatic sync.

### Background Modes
| Mode | Purpose |
|---|---|
| `remote-notification` | CloudKit sync push notifications |

---

## 8. Summary Statistics

| Category | Count |
|---|---|
| Total Swift files | 133 |
| @Model classes | 17 |
| Service classes | 26 |
| Standalone enum files | 9 |
| View files | 49 |
| Design System components | 24 |
| Third-party SDKs | 1 (RevenueCat) |
| ProFeature cases | 16 |
| FAB quick actions | 7 |
| Notification types | 12 |
| Supported themes | 3 |
