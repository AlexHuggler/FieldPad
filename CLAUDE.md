# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## FieldPad Project Guide

## Build & Development Commands

- **Open project**: `open FieldPad/FieldPad/FieldPad.xcodeproj`
- **Build**: Cmd+B in Xcode
- **Run**: Cmd+R in Xcode
- **Clean**: Cmd+Shift+K
- No CLI build tools (pure Xcode project)

## Dependencies (SPM)

| Package | Version | Purpose |
|---------|---------|---------|
| [purchases-ios](https://github.com/RevenueCat/purchases-ios) | >= 5.56.1 | In-app purchase & subscription management (RevenueCat) |
| [swift-algorithms](https://github.com/apple/swift-algorithms) | >= 1.2.1 | Apple collection algorithms (declared, not currently imported) |
| swift-numerics | >= 1.1.1 | Transitive dependency of swift-algorithms |

No CocoaPods or Carthage.

## Architecture Overview

- **Framework**: SwiftUI + SwiftData (iOS 17+)
- **Pattern**: Observable Services + Direct Data Binding (no ViewModel layer — views use `@Query` directly)
- **Persistence**: SwiftData with iCloud sync via CloudKit private database
- **State**: Environment-injected services (SyncEngine, ThemeManager, ComplianceEngine, NotificationManager, SubscriptionManager, DeepLinkRouter)

## Project Structure

```
FieldPad/FieldPad/FieldPad/FieldPad/
├── FieldPadApp.swift                  # Entry point, SwiftData container setup
├── Models/
│   ├── Client.swift                   # Client records
│   ├── Job.swift                      # Service jobs with recurring support
│   ├── Invoice.swift                  # Invoices with signatures, split tax
│   ├── InvoiceLineItem.swift          # Line items shared by invoices & estimates
│   ├── Estimate.swift                 # Service estimates
│   ├── RefrigerantCylinder.swift      # Cylinder tracking with exchange support
│   ├── ComplianceLog.swift            # EPA refrigerant service logs + A2L checklist
│   ├── SyncRecord.swift               # Offline sync change tracking
│   ├── EquipmentProfile.swift         # Equipment with regulatory framework detection
│   ├── LeakRateRecord.swift           # Leak rate calculation history
│   ├── VerificationTest.swift         # Initial/follow-up leak test records
│   ├── RepairExtension.swift          # EPA repair deadline extensions
│   ├── RetrofitRetirementPlan.swift   # Equipment retrofit/retirement tracking
│   ├── InspectionSchedule.swift       # Quarterly/annual inspection scheduling
│   ├── ChronicLeakerReport.swift      # 125% annual leak rate reporting
│   ├── RefrigerantDisposition.swift   # Refrigerant chain-of-custody
│   ├── TechnicianProfile.swift        # EPA certification profiles
│   ├── Enums/                         # 9 enum files (ApplianceType, CertificationType,
│   │                                  #   DispositionType, FullChargeMethod, JobStatus,
│   │                                  #   RecurrencePattern, RefrigerantType,
│   │                                  #   RegulatoryFramework, SyncStatus)
│   └── Schema/
│       ├── SchemaV1.swift             # Current versioned schema (all 17 models)
│       └── FieldPadMigrationPlan.swift # Migration plan (empty stages)
├── Views/
│   ├── Navigation/MainTabView.swift   # 5-tab root + FAB
│   ├── Dashboard/DashboardView.swift
│   ├── Jobs/                          # JobList, JobDetail, JobForm, Schedule,
│   │                                  #   ComplianceLogForm, ComplianceLogJobPicker
│   ├── Clients/                       # ClientList, ClientDetail, ClientForm
│   ├── Equipment/                     # EquipmentList, EquipmentDetail, EquipmentForm,
│   │                                  #   InspectionSchedule
│   ├── Inventory/                     # Inventory, CylinderDetail, CylinderForm,
│   │                                  #   ScanCylinderSheet, CylinderExchangeSheet
│   ├── Invoicing/                     # InvoiceList, InvoiceDetail, InvoiceForm,
│   │                                  #   SignaturePad, RecordPayment, A2LComplianceModal
│   ├── Estimates/                     # EstimateList, EstimateDetail, EstimateForm,
│   │                                  #   EstimateLineItemForm
│   ├── Compliance/                    # ComplianceDashboard, LeakRate, ChronicLeaker,
│   │                                  #   AuditExport, OverdueRepairs, PendingVerifications,
│   │                                  #   DispositionForm, RepairExtensionForm,
│   │                                  #   RetrofitRetirementForm, VerificationTestForm
│   ├── Settings/                      # Settings, TechnicianProfiles, TechnicianForm,
│   │                                  #   DataImportSheet, FreeLimitsView
│   ├── Onboarding/OnboardingView.swift
│   ├── Tools/FiveThousandRuleView.swift
│   └── Errors/DatabaseErrorView.swift
├── Services/
│   ├── AppSettings.swift              # UserDefaults wrapper
│   ├── AppError.swift                 # Centralized error types
│   ├── SubscriptionManager.swift      # RevenueCat, ProFeature enum, paywall
│   ├── DatabaseSaveHelper.swift       # SwiftData save with retry
│   ├── BackupService.swift            # Auto-backup with rotation
│   ├── DataExportService.swift        # PDF/CSV/JSON export
│   ├── DataImportService.swift        # JSON import with A2L validation
│   ├── FormDraftService.swift         # Form field draft persistence
│   ├── ReviewRequestService.swift     # StoreKit review prompts
│   ├── ComplianceDeletionGuard.swift   # EPA retention-aware delete/archive routing
│   ├── ScheduleConflictDetector.swift # Job overlap detection
│   ├── ValidationHelper.swift         # Email/phone/zip validation
│   ├── LineItemTemplateService.swift   # Invoice line item templates
│   ├── Compliance/
│   │   ├── ComplianceEngine.swift     # Leak rate calc, chronic leaker, inspections
│   │   ├── LeakRateCalculator.swift   # Section 608 + Subpart C thresholds
│   │   └── EPAReportGenerator.swift   # Chronic leaker & compliance reports
│   ├── Notifications/
│   │   ├── NotificationManager.swift  # 12 notification types, soft-ask flow
│   │   ├── NotificationDelegate.swift # Deep link routing (DeepLinkRouter)
│   │   └── CylinderNotificationHandler.swift # Low inventory alerts
│   ├── Sync/
│   │   ├── SyncEngine.swift           # Offline sync coordination
│   │   ├── NetworkMonitor.swift       # NWPathMonitor wrapper
│   │   ├── SyncStatusManager.swift    # Aggregate sync status
│   │   └── ICloudService.swift        # CloudKit account status + remote notification registration
│   ├── Utilities/
│   │   └── HapticManager.swift        # UIKit haptic feedback
│   └── Vision/
│       ├── CylinderScannerService.swift    # AVCapture camera + scanning
│       └── TextRecognitionEngine.swift     # Vision OCR + parsing
├── DesignSystem/                      # 24 reusable UI components
│   ├── GloveButton.swift, GloveTextField.swift, GloveTextAreaField.swift,
│   │   GloveDatePickerField.swift     # Core Glove Mode inputs
│   ├── StatCard.swift, DetailRow.swift, SectionHeader.swift, EmptyStateCard.swift
│   ├── CylinderGaugeView.swift, FilterChip.swift, SyncStatusBadge.swift
│   ├── AddressSearchField.swift       # MapKit autocomplete
│   ├── SwipeToConfirmButton.swift, ConfirmationDialog.swift
│   ├── SaveSuccessOverlay.swift, LoadingOverlay.swift, ToastView.swift
│   ├── FormValidationMessage.swift, HelpTooltip.swift, UsageLimitBanner.swift
│   ├── StaggeredAppearance.swift, UnsavedChangesModifier.swift
│   └── Formatters.swift, USStates.swift
└── Theme/
    ├── ThemeManager.swift             # Design tokens
    ├── ThemeColors.swift, ThemeTypography.swift, ThemeSpacing.swift
```

## Domain Context

- HVAC/refrigeration field service app
- EPA Section 608 compliance tracking (ODS refrigerants)
- AIM Act Subpart C compliance (HFC refrigerants, effective Jan 1, 2026)
- Dual regulatory framework detection based on refrigerant GWP
- Offline-first architecture (works without connectivity)
- "Glove Mode" for enlarged touch targets
- Freemium model with RevenueCat subscription gating (16 ProFeature cases)

## Key Patterns

- Views use `@Query` for reactive SwiftData fetching
- Services are `@Observable @MainActor` singletons
- Theme/sync/compliance passed via `.environment()` modifiers
- Cascade deletes configured on relationships (InvoiceLineItems)
- `DatabaseSaveHelper.save(context)` for centralized saving with retry
- `HapticManager.shared` for haptic feedback
- Enum-wrapped properties use `*Raw: String` storage with computed accessors
- `ComplianceDeletionGuard.attemptDelete()` routes delete→archive for retention-locked compliance logs

## Schema Migrations

When modifying SwiftData or CoreData schema migrations, always check for duplicate version checksums before finalizing. Run a build and verify the app launches without freezing after any migration change. Never introduce a new schema version without confirming it doesn't conflict with existing version identifiers.

Current state: SchemaV1 only, with empty migration stages in FieldPadMigrationPlan.

## Regulatory Compliance (EPA Section 608 + Subpart C)

FieldPad implements EPA Section 608 compliance features including HFC leak repair requirements and A2L refrigerant safety. When implementing compliance features, cross-reference the existing compliance models and enums before creating new ones to avoid duplication. Compliance changes typically span multiple files (models, views, validation logic, documentation).

Key thresholds in code (LeakRateCalculator.swift):
- Section 608: Commercial 20%, Comfort Cooling 15%, Industrial 30%
- Subpart C: Commercial 20%, Comfort Cooling 10%, Industrial 30%, Transport 10%
- Charge thresholds: Section 608 >= 50 lbs, Subpart C >= 15 lbs (GWP > 53)

## Quality Checks

When making multi-file changes, verify the app builds and runs successfully before considering a task complete. For SwiftData model changes specifically, test that the app launches without freezing.

## Tests

- **Unit tests** (18): FieldPadTests, InvoiceEmailTests, LeakRateCalculatorTests, ModelTests, RefrigerantTypeTests, RecurrencePatternTests, JobStatusTests, ValidationHelperTests, ChronicLeakerTests, CylinderEdgeCaseTests, InvoiceTaxEdgeCaseTests, ScheduleConflictTests, RecurrencePatternEdgeCaseTests, ComplianceEngineEdgeCaseTests, DataImportEdgeCaseTests, SubscriptionGatingTests, TextRecognitionEngineTests, RetentionLockTests
- **UI tests** (2): FieldPadUITests, FieldPadUITestsLaunchTests

## Current Limitations

- iCloud sync active via SwiftData+CloudKit (Pro-gated UI controls; sync runs for all users)
- Tests exist but are growing (20 total)
- swift-algorithms declared as dependency but not imported anywhere
