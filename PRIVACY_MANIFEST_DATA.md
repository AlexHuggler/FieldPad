# Privacy Manifest & Data Collection — FieldPad

> Audit date: 2026-02-08

---

## 1. Data Collection Mapping

### Contact Info

| Data | Storage | Source File | Purpose |
|---|---|---|---|
| Business name | UserDefaults (`businessName`) | `Services/AppSettings.swift` | App Functionality |
| Business address | UserDefaults (`businessAddress`) | `Services/AppSettings.swift` | App Functionality |
| Business phone | UserDefaults (`businessPhone`) | `Services/AppSettings.swift` | App Functionality |
| Business email | UserDefaults (`businessEmail`) | `Services/AppSettings.swift` | App Functionality |
| Tax ID | UserDefaults (`taxId`) | `Services/AppSettings.swift` | App Functionality |
| Client name | SwiftData (`Client.name`) | `Models/Client.swift` | App Functionality |
| Client company | SwiftData (`Client.companyName`) | `Models/Client.swift` | App Functionality |
| Client email | SwiftData (`Client.email`) | `Models/Client.swift` | App Functionality |
| Client phone | SwiftData (`Client.phone`) | `Models/Client.swift` | App Functionality |
| Client address | SwiftData (`Client.address/city/state/zipCode`) | `Models/Client.swift` | App Functionality |

### Financial Info

| Data | Storage | Source File | Purpose |
|---|---|---|---|
| Invoice amounts | SwiftData (`Invoice.total`, line items) | `Models/Invoice.swift` | App Functionality |
| Tax rates | SwiftData + UserDefaults | `Invoice.swift`, `AppSettings.swift` | App Functionality |
| Payment info | SwiftData (`Invoice.amountPaid`, `paymentMethod`) | `Models/Invoice.swift` | App Functionality |
| Labor rates | SwiftData (`Job.laborRate`, `laborHours`) | `Models/Job.swift` | App Functionality |
| Estimate amounts | SwiftData (`Estimate` + line items) | `Models/Estimate.swift` | App Functionality |

### Health & Fitness
**None collected.**

### Location
**None collected.** All addresses are manual text entry — no GPS, no CoreLocation, no geofencing. `MapKit` is used only for address autocomplete suggestions (queries sent to Apple servers).

### Identifiers

| Identifier | Collected? |
|---|---|
| IDFA (Advertising) | No |
| identifierForVendor | No |
| Device fingerprinting | No |
| Custom user ID | No (RevenueCat generates anonymous ID) |

### Usage Data

| Data | Storage | Transmitted? | Purpose |
|---|---|---|---|
| Session count | UserDefaults (`sessionCount`) | No | App Functionality |
| Review request count | UserDefaults (`reviewRequestCount`) | No | App Functionality |
| Last review request date | UserDefaults (`lastReviewRequestDate`) | No | App Functionality |
| First launch date | UserDefaults (`firstLaunchDate`) | No | App Functionality |
| Invoice share count | UserDefaults (`invoiceShareCount`) | No | Paywall gate tracking |
| Estimate share count | UserDefaults (`estimateShareCount`) | No | Paywall gate tracking |
| Notification preferences | UserDefaults (`NotificationPreferences`) | No | App Functionality |
| Form drafts | UserDefaults (`formDraft.*` keys) | No | App Functionality |

### Sensitive Data

| Data | Storage | Purpose |
|---|---|---|
| EPA cert numbers | UserDefaults (`epaCertNumber`) | App Functionality |
| EPA cert expiration | UserDefaults (`epaCertExpirationDate`) | App Functionality |
| Technician names | SwiftData (`ComplianceLog.technicianName`) | App Functionality |
| Digital signatures | SwiftData (`Invoice.signatureData` — PNG blob) | App Functionality |
| Signer names | SwiftData (`Invoice.signedByName`) | App Functionality |
| A2L safety checklist | SwiftData (6 mandatory + 1 optional + 3 informational booleans per ComplianceLog) | App Functionality |

---

## 2. Camera & Photos

### Camera (AVFoundation + Vision)

| Aspect | Detail |
|---|---|
| Permission key | `NSCameraUsageDescription` in Info.plist |
| Description | "FieldPad requires camera access to attach photos of equipment and job sites to service reports." |
| Usage | OCR scanning of refrigerant cylinder labels |
| **Plist Accuracy** | **Inaccurate** — see note below |
| Processing | On-device only via Apple Vision framework |
| Frames stored | **No** — live preview only, no frames saved to disk |
| Data transmitted | **No** — all OCR processing is local |
| Files | `Services/Vision/CylinderScannerService.swift`, `Services/Vision/TextRecognitionEngine.swift`, `Views/Inventory/ScanCylinderSheet.swift` |

> **Note:** The current `NSCameraUsageDescription` text is inaccurate. The camera is used exclusively for OCR scanning of cylinder labels, not for attaching photos to service reports. Recommend updating to: *"FieldPad uses the camera to scan refrigerant cylinder labels for inventory tracking."*

### Photos (PhotosUI)

| Aspect | Detail |
|---|---|
| Permission key | **MISSING** — `NSPhotoLibraryUsageDescription` not in Info.plist |
| Usage | Business logo selection only |
| Processing | Image loaded, resized to 500x500 max, converted to PNG |
| Storage | `{DocumentsDirectory}/business_logo.png` (max 2 MB) |
| Files | `Views/Settings/SettingsView.swift:117-120` |

### Backup Files (BackupService)

| Aspect | Detail |
|---|---|
| Usage | Automatic and manual data backups |
| Processing | JSON export of all SwiftData records |
| Storage | `{DocumentsDirectory}/Backups/*.json` (max 5 files, auto-rotated) |
| Frequency | Configurable via `AppSettings.backupFrequencyDays` (default: 7 days) |
| Files | `Services/BackupService.swift` |

---

## 3. Third-Party SDKs

### RevenueCat (`import RevenueCat`, `import RevenueCatUI`)

| Aspect | Detail |
|---|---|
| Purpose | Subscription management, purchase validation |
| API Key | `appl_wkEKfZhYGmdyvOBxscTy` |
| Data sent | App identifier, installation ID, purchase receipts |
| Data received | Subscription status, entitlements, offerings |
| RevenueCat collects | Device identifiers, purchase receipts, IP address (per their privacy policy) |
| Files | `Services/SubscriptionManager.swift`, `FieldPadApp.swift`, `Views/Settings/SettingsView.swift` |

### StoreKit

| Aspect | Detail |
|---|---|
| Purpose | App review requests only |
| Usage | `AppStore.requestReview` equivalent |
| File | `Services/ReviewRequestService.swift` |

### CloudKit (Active)

| Aspect | Detail |
|---|---|
| Purpose | iCloud data sync via SwiftData+CloudKit automatic sync |
| Container | `iCloud.com.hugglerholdings.fieldpad` |
| Data synced | All SwiftData models (clients, jobs, invoices, compliance logs, cylinders, equipment, estimates, technician profiles) |
| Status | **Active**: SwiftData configured with `cloudKitDatabase: .private("iCloud.com.hugglerholdings.fieldpad")`, CloudKit entitlements present, `aps-environment: development` in entitlements |
| File | `Services/Sync/ICloudService.swift`, `FieldPadApp.swift` |
| Privacy impact | Data synced to user's private iCloud container; encrypted by Apple CloudKit protocols |

### MapKit

| Aspect | Detail |
|---|---|
| Purpose | Address autocomplete/search |
| Data sent | Address search queries (to Apple servers) |
| File | `DesignSystem/AddressSearchField.swift` |

### MessageUI

| Aspect | Detail |
|---|---|
| Purpose | Native email composer for invoice/client emails |
| Data flow | Delegated to iOS Mail — app has no SMTP access |
| Files | `Views/Invoicing/InvoiceDetailView.swift`, `Views/Clients/ClientDetailView.swift` |

### Not Present

| SDK | Status |
|---|---|
| Firebase / Crashlytics | Not used |
| Google Analytics | Not used |
| Sentry | Not used |
| Mixpanel / Amplitude | Not used |
| Facebook SDK | Not used |
| AdMob / Ad networks | Not used |
| Segment / mParticle | Not used |
| AppsFlyer / Adjust / Branch | Not used |

---

## 4. Background Modes

**Status (2026-02-08):** `remote-notification` background mode is active for CloudKit sync push notifications. `aps-environment: development` entitlement is present.

**AppDelegate** (`FieldPadApp.swift`):
- `AppDelegate` contains `didReceiveRemoteNotification` handler for CloudKit sync notifications
- `registerForRemoteNotifications()` registers for push notifications at app launch
- Push notifications trigger CloudKit sync when remote changes are detected

---

## 5. Privacy Manifest File

### Status: **MISSING**

No `PrivacyInfo.xcprivacy` file exists in the project.

**Required since**: Spring 2024 (Apple requirement for App Store submissions)

### Required API Declarations

The following privacy-impacting APIs are used and must be declared:

| API Category | API Used | Reason |
|---|---|---|
| File timestamp APIs | `Date()` for record timestamps | App Functionality (C617.1) |
| User defaults APIs | `UserDefaults.standard` | App Functionality (CA92.1) |
| System boot time APIs | None | N/A |
| Disk space APIs | None | N/A |
| Active keyboard APIs | None | N/A |

### Recommended Privacy Manifest Contents

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN"
  "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>NSPrivacyTracking</key>
    <false/>
    <key>NSPrivacyTrackingDomains</key>
    <array/>
    <key>NSPrivacyCollectedDataTypes</key>
    <array>
        <!-- Contact Info -->
        <dict>
            <key>NSPrivacyCollectedDataType</key>
            <string>NSPrivacyCollectedDataTypeName</string>
            <key>NSPrivacyCollectedDataTypeLinked</key>
            <false/>
            <key>NSPrivacyCollectedDataTypeTracking</key>
            <false/>
            <key>NSPrivacyCollectedDataTypePurposes</key>
            <array>
                <string>NSPrivacyCollectedDataTypePurposeAppFunctionality</string>
            </array>
        </dict>
        <!-- Purchase History (RevenueCat) -->
        <dict>
            <key>NSPrivacyCollectedDataType</key>
            <string>NSPrivacyCollectedDataTypePurchaseHistory</string>
            <key>NSPrivacyCollectedDataTypeLinked</key>
            <false/>
            <key>NSPrivacyCollectedDataTypeTracking</key>
            <false/>
            <key>NSPrivacyCollectedDataTypePurposes</key>
            <array>
                <string>NSPrivacyCollectedDataTypePurposeAppFunctionality</string>
            </array>
        </dict>
    </array>
    <key>NSPrivacyAccessedAPITypes</key>
    <array>
        <dict>
            <key>NSPrivacyAccessedAPIType</key>
            <string>NSPrivacyAccessedAPICategoryUserDefaults</string>
            <key>NSPrivacyAccessedAPITypeReasons</key>
            <array>
                <string>CA92.1</string>
            </array>
        </dict>
    </array>
</dict>
</plist>
```

---

## 6. Info.plist Audit

### Current Entries

| Key | Value | Status |
|---|---|---|
| `CFBundleIdentifier` | (empty) | **Must be set** |
| `NSCameraUsageDescription` | "FieldPad requires camera access..." | OK |
| `UIBackgroundModes` | `remote-notification` (CloudKit sync) | OK — required for CloudKit sync push notifications |

### Missing Entries

| Key | Needed For | Priority |
|---|---|---|
| `NSPhotoLibraryUsageDescription` | Business logo picker (PhotosUI) | **Required** |
| `CFBundleIdentifier` | App Store submission | **Required** (should be set in build settings) |

---

## 7. Apple Privacy Nutrition Labels

Based on the code audit, these App Store privacy label declarations are needed:

### Data Used to Track You
**None** — no tracking SDKs, no IDFA, `NSPrivacyTracking = false`

### Data Linked to You
**None** — RevenueCat uses anonymous IDs; no account system

### Data Not Linked to You

| Category | Data Type | Purpose |
|---|---|---|
| Contact Info | Name, Email Address, Phone Number, Physical Address | App Functionality |
| Financial Info | Other Financial Info (invoice amounts, tax rates) | App Functionality |
| Purchases | Purchase History (via RevenueCat) | App Functionality |
| Sensitive Info | Other Sensitive Data (EPA cert numbers) | App Functionality |

### Data Not Collected
- Health & Fitness
- Location (precise or coarse)
- Browsing History
- Search History
- Identifiers (device ID, user ID)
- Usage Data (beyond local counters)
- Diagnostics

---

## 8. Recommendations

1. **Create `PrivacyInfo.xcprivacy`** — required for App Store submission (missing since Spring 2024 requirement)
2. **Add `NSPhotoLibraryUsageDescription`** to Info.plist for logo picker
3. **Set `CFBundleIdentifier`** in Info.plist or verify it's set in build settings
4. **Document RevenueCat SDK** in privacy manifest's collected data types
5. **Include RevenueCat's own `PrivacyInfo.xcprivacy`** — RevenueCat distributes one with their SDK; verify it's bundled
6. **Add privacy policy URL** to App Store Connect metadata (standalone `PRIVACY_POLICY.md` created at project root)
7. **Review MapKit queries** — address autocomplete queries are sent to Apple; noted in privacy policy
8. **CloudKit code is active** — `AppDelegate` `registerForRemoteNotifications()` and `didReceiveRemoteNotification` handler are active and required for CloudKit sync
9. **Update `NSCameraUsageDescription`** text — currently says "attach photos of equipment and job sites to service reports" but camera is only used for OCR cylinder label scanning
