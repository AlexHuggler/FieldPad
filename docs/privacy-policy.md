---
layout: page
title: "Privacy Policy"
description: "FieldPad Privacy Policy — Learn how FieldPad protects your data with local-only storage and iCloud sync. No servers, no tracking, no ads."
last_updated: "February 9, 2026"
permalink: /privacy-policy/
---

FieldPad ("we", "us", "our") operates the FieldPad mobile application (the "App"). This Privacy Policy describes how we collect, use, and protect your information.

---

## 1. Information We Collect

### 1.1 Business & Contact Information

We collect the following information that you enter directly into the App:

- Business name, address, phone number, and email
- Tax identification number
- Client names, company names, email addresses, phone numbers, and physical addresses
- Technician names and EPA certification numbers
- Job site addresses (entered as text — no GPS tracking)

### 1.2 Financial Information

- Invoice amounts, line item details, tax rates, and payment records
- Estimate amounts and line items
- Labor rates and hours

### 1.3 EPA Compliance Data

- EPA Section 608 compliance logs (refrigerant recovery/recharge amounts, leak detection records, repair tracking)
- AIM Act Subpart C compliance records for HFC refrigerants
- A2L safety checklist completions (dissipation confirmation, spark-free tool verification, ventilation verification, PPE verification, leak detector usage, equipment grounding)
- A2L informational items (maximum charge verified, installation location verified, recovery equipment rated) — these are tracked for reference but are not required for checklist completion
- Refrigerant cylinder inventory data (serial numbers, weights, manufacturers)
- Equipment profiles (appliance type, refrigerant type, charge amounts, inspection schedules)
- Leak rate calculation history

**EPA Data Retention**: Compliance logs are retained for a minimum of 3 years from the date of creation or from the date of equipment retirement, whichever is later, in accordance with EPA record-keeping requirements. Records within this retention period cannot be deleted from the App.

### 1.4 Signatures

- Digital signatures captured on-device as images using Apple's PencilKit framework
- Signer printed names and signature dates

### 1.5 Camera Data

- The App uses your device camera solely for OCR (optical character recognition) scanning of refrigerant cylinder labels
- Camera frames are processed entirely on-device using Apple's Vision framework
- **No camera images are stored, saved to disk, or transmitted** — frames are processed in real-time and immediately discarded
- The camera is not used for photos, videos, or any purpose other than cylinder label scanning

### 1.6 Photos

- You may optionally select a photo from your device to use as your business logo on invoices
- The selected image is stored locally on your device only (in the App's Documents directory)
- The image is resized to a maximum of 500x500 pixels and stored as PNG (max 2 MB)

### 1.7 Usage Data (Local Only — Not Transmitted by the App)

The following data is stored locally on your device in UserDefaults and is **not transmitted by the App** to any server:

- Session count and first launch date
- Invoice share count, estimate share count, PDF generation count, OCR scan count (used for free tier limit tracking)
- App review request tracking (session count, last request date)
- Notification preferences (notification type toggles, work hours schedule)
- Form drafts (temporary field values for in-progress forms)

---

## 2. How We Use Your Information

All data collected serves a single purpose: **providing the App's functionality**.

We use your information to:
- Create and manage jobs, clients, invoices, estimates, and compliance logs
- Generate PDF invoices and estimates with your business information
- Track refrigerant cylinder inventory and EPA compliance records
- Calculate leak rates using EPA Section 608 and Subpart C formulas
- Generate the 5,000 Rule repair/replace recommendations
- Generate chronic leaker reports and compliance history exports
- Manage your subscription status
- Schedule local notifications for job reminders, invoice due dates, and compliance alerts

We do **not** use your data for:
- Advertising or marketing
- Analytics or behavioral tracking
- User profiling
- Sale to third parties
- Training machine learning models

---

## 3. Third-Party Services

### 3.1 RevenueCat (Subscription Management)

We use RevenueCat to manage in-app subscriptions. RevenueCat receives:
- An anonymous installation identifier (not linked to your identity)
- Purchase receipts from Apple
- Subscription status information
- Your IP address (per RevenueCat's standard infrastructure)

RevenueCat does **not** receive your business data, client information, compliance records, or any data you enter into the App. See [RevenueCat's Privacy Policy](https://www.revenuecat.com/privacy) for details.

### 3.2 Apple MapKit (Address Autocomplete)

When you search for addresses in the App (e.g., client addresses, job site locations), search queries are sent to Apple's servers for address autocomplete suggestions. Apple processes these queries per their standard privacy practices. See [Apple's Privacy Policy](https://www.apple.com/privacy/).

### 3.3 Apple iCloud (CloudKit)

iCloud sync is **active**. Your data is synced to your private iCloud container (`iCloud.com.hugglerholdings.fieldpad`) using Apple's CloudKit framework via SwiftData automatic sync. Data synced includes:

- Business data (clients, jobs, invoices, compliance logs, cylinders, equipment profiles, estimates, technician profiles)
- All data stored in SwiftData

Your data is encrypted by Apple's CloudKit protocols and stored in your personal iCloud account. Only you can access your data — we have no access to your iCloud container. Sync runs automatically when your device has connectivity. Sync UI controls (manual sync, last synced timestamp) are available to Pro subscribers.

See [Apple's Privacy Policy](https://www.apple.com/privacy/) for details on iCloud data handling.

### 3.4 No Other Third-Party Services

We do not use Firebase, Google Analytics, Crashlytics, Sentry, Facebook SDK, advertising networks, or any other third-party analytics, crash reporting, or tracking services.

---

## 4. Data Storage

### 4.1 Local Storage

Your data is stored on your device using:
- **SwiftData** (SQLite database) for all business data (clients, jobs, invoices, compliance logs, cylinders, equipment profiles, estimates, technician profiles)
- **UserDefaults** for app settings, preferences, notification preferences, and form drafts
- **Local file storage** for your business logo image
- **Local file storage** for automatic backups (JSON exports in Documents/Backups/, maximum 5 files, auto-rotated)

### 4.2 iCloud Storage

Your data is automatically synced to your private iCloud container via CloudKit. This provides cloud backup and cross-device access for your data. If your device is lost, damaged, or reset, your data can be recovered from iCloud on a new device. You can also export your data manually using the App's export feature.

iCloud storage is subject to your Apple iCloud storage plan limits. Data remains in your private iCloud container and is not accessible by us.

You can manage iCloud sync for FieldPad in iOS Settings > Apple ID > iCloud > Apps Using iCloud.

If iCloud is unavailable or your iCloud storage is full, data remains on your device only until sync can resume.

### 4.3 No Server-Side Storage

We do not operate any servers. Your data is stored locally on your device and synced to your private Apple iCloud container. Beyond RevenueCat (subscription management), Apple iCloud (data sync), and Apple MapKit (address autocomplete), no data leaves your device.

---

## 5. Data Retention and Deletion

### 5.1 Local Data

Your data remains on your device until you delete it. You can delete data at any time:
- **Settings > Data Management > Delete All Data** — removes all records with double confirmation
- Individual records can be deleted from their respective list views
- **Exception**: EPA compliance logs within the 3-year retention period are protected from deletion to maintain regulatory compliance. After the retention period expires, these records can be deleted or archived.

### 5.2 Subscription Data

To delete your subscription data from RevenueCat:
- Cancel your subscription via the App Store
- Email fieldpad@fieldpadpro.com to request deletion of your anonymous RevenueCat customer record — we will process your request within 30 days

### 5.3 iCloud Data

Data synced to iCloud can be managed through:
- **iOS Settings > Apple ID > iCloud > Manage Storage** — view and delete FieldPad iCloud data
- **Deleting data in the App** — removes data locally and from iCloud on next sync
- **Deleting the App** — local data is removed; iCloud data may persist until manually deleted from iCloud settings
- **Signing out of iCloud** — synced data remains in your iCloud account and will re-sync when you sign back in on any device

### 5.4 Backup Data

Automatic local backups can be managed in Settings:
- View or delete local backups
- Maximum 5 backup files retained; oldest are automatically removed

---

## 6. Children's Privacy

FieldPad is a professional HVAC field service tool designed for licensed EPA-certified technicians. It is not directed at children under 13 and we do not knowingly collect information from children under 13.

---

## 7. Camera and Photos Usage

- **Camera**: Used exclusively for OCR scanning of refrigerant cylinder labels. Camera frames are processed on-device only using Apple's Vision framework. No images are stored, saved, or transmitted. The camera is never used for photos or video recording.
- **Photos**: Used exclusively to select a business logo image from your photo library. The selected image is stored locally on your device only. No images are uploaded or transmitted.

---

## 8. Security

Your data is protected by:
- iOS app container sandboxing (your data is isolated from other apps)
- Apple's iOS data protection (hardware encryption at rest)
- HTTPS for all network communications (RevenueCat subscription checks, MapKit address queries)
- No remote access to your data — data is stored on your device and in your private iCloud container, both protected by Apple's encryption

---

## 9. Apple App Privacy Requirements

### Privacy Manifest (`PrivacyInfo.xcprivacy`)

The App declares the following in its privacy manifest (`PrivacyInfo.xcprivacy`):
- **Tracking**: No tracking (`NSPrivacyTracking = false`)
- **Tracking Domains**: None
- **Privacy-Impacting APIs**: UserDefaults (App Functionality), File Timestamp (access backup file creation dates within app container)
- **Collected Data Types**: Contact Info (Name, Email, Phone, Address), Financial Info, Purchase History (via RevenueCat), Sensitive Info (EPA certification numbers)
- All collected data: **Not linked to identity**, **Not used for tracking**

---

## 10. Your Privacy Rights (California)

If you are a California resident (CCPA/CPRA), you have the following rights regarding your personal data:

- **Right to Know**: You may request a summary of the personal data we process. Since all your data is stored on your device and in your private iCloud container, you already have direct access to all of it.
- **Right to Delete**: You may delete your data at any time using Settings > Data Management > Delete All Data, or by deleting individual records. EPA compliance logs within the 3-year retention period are protected per regulatory requirements.
- **Right to Data Portability**: You may export all your data in JSON or CSV format using Settings > Data Management > Export All Data (Pro feature).
- **Right to Opt Out of Sale**: We do not sell your personal data to any third party.

To exercise any of these rights, contact us at: fieldpad@fieldpadpro.com

---

## 11. Changes to This Policy

We may update this Privacy Policy periodically. We will notify you of material changes by updating the "Last Updated" date. Continued use of the App after changes constitutes acceptance of the updated policy.

---

## 12. Contact Us

If you have questions about this Privacy Policy, contact us at: fieldpad@fieldpadpro.com

---

*This privacy policy was last audited against the FieldPad codebase on 2026-02-09.*
