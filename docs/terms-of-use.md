---
layout: page
title: "Terms of Use"
description: "FieldPad Terms of Use — Subscription terms, free tier limits, EPA data accuracy disclaimer, and liability limitations for the FieldPad HVAC field service app."
last_updated: "February 9, 2026"
permalink: /terms-of-use/
---

These Terms of Use ("Terms") govern your use of the FieldPad mobile application ("App") provided by Huggler Holdings LLC ("we", "us", "our"). By downloading, installing, or using the App, you agree to these Terms.

You must be at least 18 years old, or the age of majority in your jurisdiction, to use this App.

---

## 1. Subscription Terms

### 1.1 Free Tier

FieldPad offers a free tier with the following usage limits:

| Feature | Free Limit |
|---------|-----------|
| Clients | 5 |
| Jobs | 10 |
| Invoices | 10 |
| Estimates | 5 |
| Compliance Logs | 20 |
| Cylinders | 10 |
| A2L Compliance Logs | 5 |
| Invoice Shares | 5 (lifetime) |
| Estimate Shares | 10 (lifetime) |
| PDF Invoice Generation | 8 (lifetime) |
| OCR Cylinder Scans | 15 (lifetime) |

### 1.2 FieldPad Pro Subscription

FieldPad Pro removes all count limits and unlocks additional features:
- Unlimited clients, jobs, invoices, estimates, compliance logs, and cylinders
- Unlimited invoice and estimate sharing
- Unlimited PDF generation and OCR scanning
- Data export (JSON/CSV)
- EPA compliance log export
- Recurring job scheduling
- Business logo on invoices
- Full digital signature images on PDFs (no watermark)

**Note**: iCloud sync runs automatically for all users via SwiftData+CloudKit — your data syncs to your private iCloud container regardless of subscription tier. FieldPad Pro unlocks Sync Status & Controls (sync status monitoring, last synced timestamp, and manual sync trigger), which provide visibility into sync activity but do not affect the underlying sync behavior.

### 1.3 Billing

- Subscriptions are billed through Apple's App Store
- Subscription auto-renews unless cancelled at least 24 hours before the end of the current period
- Payment is charged to your Apple ID account at confirmation of purchase
- Subscription management and cancellation are handled through your App Store account settings

### 1.4 Refunds

Refund requests must be directed to Apple per App Store policies.

---

## 2. Free Tier Behavior

- Count-limited features display usage progress and block new record creation when limits are reached
- Pro-only features (data export, compliance log export, recurring jobs, business logo) are unavailable on the free tier
- Free users' invoice PDFs display watermark text where signatures would appear on Pro PDFs
- All locally stored data remains accessible regardless of subscription status — downgrading does not delete your data
- Existing records beyond free limits remain viewable and editable; only creation of new records beyond the limit is blocked

### 2.1 Subscription Expiry

If your Pro subscription expires or is cancelled:
- **All existing data is preserved** — no records are deleted
- **Read and edit access**: You can continue to view and edit all existing records, including those beyond free tier limits
- **Creation limits re-enforced**: New record creation is subject to free tier limits
- **Pro-only features locked**: Data export, compliance log export, recurring job creation, and business logo features require re-subscription
- **In-progress operations**: If a Pro feature is actively in use when the subscription expires, the current operation completes normally; subsequent attempts will prompt for re-subscription

---

## 3. EPA Data Accuracy and Compliance Disclaimer

**IMPORTANT — PLEASE READ CAREFULLY**

### 3.1 Record-Keeping Tool Only

FieldPad is a **record-keeping and calculation tool**. It is **not** a substitute for professional legal, regulatory, or compliance advice. The App does not certify, guarantee, or verify EPA compliance.

### 3.2 User Responsibility for Data Entry

**All compliance data is manually entered by you.** The App performs calculations based on the data you provide but does not independently verify the accuracy of your input. Specifically:

- Refrigerant weights (pounds recovered, recharged) are entered by you
- Equipment charge amounts and specifications are entered by you
- Leak test results and repair records are entered by you
- Technician certification information is entered by you

**You are solely responsible for the accuracy of all data entered into the App.**

### 3.3 Leak Rate Calculations

The App calculates leak rates using EPA Section 608 and AIM Act Subpart C formulas applied to data you manually enter. These calculations:

- Are performed **on-demand** when you log a recharge event — they are **not** real-time sensor-based monitoring
- Use Method 1 (annualized) or Method 2 (screening) formulas as defined by EPA regulations
- Compare results against applicable EPA thresholds (e.g., 20% for commercial refrigeration; 15% for comfort cooling under Section 608, 10% under Subpart C)
- Are only as accurate as the data you enter

**The App does not connect to any sensors, IoT devices, or monitoring equipment.** Leak rate "detection" in the App means calculation from your manually entered service data, not automated environmental sensing.

### 3.4 Notification Timing

The App schedules local notifications (e.g., repair deadline reminders, inspection due alerts, invoice reminders) based on time-based scheduling, **not real-time monitoring**. Notifications:

- Are scheduled using iOS `UNUserNotificationCenter` at the time you create or update records
- May be delayed by iOS power management, Do Not Disturb mode, or device restart
- Are not guaranteed to arrive at the exact scheduled time
- Should not be relied upon as your sole reminder system for EPA compliance deadlines

### 3.5 A2L Safety Checklists

A2L refrigerant safety checklists in the App are informational aids. The App tracks six mandatory safety items for A2L refrigerant service: dissipation confirmed, spark-free tools verified, ventilation verified, PPE verified, A2L-rated leak detector used, and equipment grounded. An optional seventh item — emergency plan reviewed — may also be recorded. These checklists:

- Do not replace proper safety training
- Do not replace manufacturer-specific handling guidelines
- Do not substitute for compliance with ASHRAE 34 safety classifications
- Are provided to assist your professional judgment, not to replace it

### 3.6 Verification Test Deadline Warnings

The App displays informational warnings when follow-up verification tests are recorded past the regulatory deadline window (10 days for Subpart C, 30 days for Section 608). These warnings are **informational only** and do not block test submission. They are provided to assist your awareness of regulatory timelines but do not constitute legal advice about compliance status.

### 3.7 Compliance Recommendations

Leak rate threshold comparisons, repair deadline calculations, inspection scheduling, chronic leaker detection, and all other compliance features are provided as aids to your professional judgment as a certified technician. Inspection frequency may be automatically escalated (made more frequent) when leak rates exceed regulatory thresholds, and de-escalated after four consecutive compliant quarterly inspections. These scheduling adjustments are recommendations only and do not replace your obligation to follow EPA-mandated inspection intervals. **Always verify compliance requirements with the EPA, your state regulatory agency, or a qualified compliance professional.**

---

## 4. Service Limitations

### 4.1 Data Storage & Sync

- FieldPad stores all data locally on your device using SwiftData, with automatic iCloud sync via CloudKit
- Your data syncs to your private iCloud container when connectivity is available, providing cloud backup and cross-device access
- If your device is lost, damaged, or reset, your data can be recovered from iCloud on a new device
- Local data availability depends on your device storage and condition
- We recommend periodic data exports via the App's built-in export feature for additional backup

### 4.2 Network-Dependent Features

The following features require an internet connection:
- Subscription purchase and validation (via RevenueCat / Apple App Store)
- Address autocomplete suggestions (via Apple MapKit)
- Sending emails with PDF attachments (via iOS Mail)

All other features — including job management, invoicing, compliance logging, OCR scanning, PDF generation, and leak rate calculations — work fully offline.

### 4.3 No Warranty

THE APP IS PROVIDED "AS IS" WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE, OR NON-INFRINGEMENT. WE DO NOT WARRANT THAT THE APP WILL BE UNINTERRUPTED, ERROR-FREE, OR FREE OF HARMFUL COMPONENTS.

---

## 5. Intellectual Property

- FieldPad and all associated trademarks, logos, and content are the property of Huggler Holdings LLC
- You are granted a limited, non-exclusive, non-transferable license to use the App for personal or business purposes
- You may not reverse engineer, decompile, or disassemble the App

---

## 6. Limitation of Liability

TO THE MAXIMUM EXTENT PERMITTED BY APPLICABLE LAW:

- We are not liable for any indirect, incidental, special, consequential, or punitive damages arising from your use of the App
- We are not liable for any loss of data, revenue, or profits
- **We are not liable for any EPA fines, penalties, enforcement actions, or compliance failures**, whether resulting from inaccurate data entry, calculation errors, missed notifications, or any other use of the App
- We are not liable for any injuries, property damage, or safety incidents related to refrigerant handling, even if the App's A2L safety checklists were completed
- Our total liability shall not exceed the amount you paid for the App in the 12 months preceding the claim
- We are not liable for any issues arising from Apple's App Store services
- We are not liable for data loss resulting from device failure, loss, or damage
- We are not liable for data loss or data inconsistencies resulting from iCloud sync failures, sync conflicts, Apple service outages, or iCloud storage limitations

---

## 7. Indemnification

You agree to indemnify and hold harmless Huggler Holdings LLC from any claims, damages, losses, or expenses (including reasonable attorney fees) arising from:
- Your use of the App
- Your violation of these Terms
- Your violation of any EPA regulation or other applicable law
- Inaccurate data you enter into the App

---

## 8. Termination

- You may stop using the App at any time by deleting it from your device
- We may terminate your access if you violate these Terms
- Upon termination, your locally stored data remains on your device and your iCloud data remains in your iCloud account
- Subscription cancellation does not delete your data

---

## 9. Governing Law

These Terms shall be governed by and construed in accordance with the laws of the State of Texas, without regard to its conflict of law provisions.

---

## 10. Dispute Resolution

Any dispute arising from or relating to these Terms or your use of the App shall be resolved through binding arbitration administered under the rules of the American Arbitration Association, conducted in Dallas County, Texas. You agree to waive any right to participate in a class action lawsuit or class-wide arbitration. Either party may seek injunctive or equitable relief in any court of competent jurisdiction. Claims eligible for small claims court may be filed there instead of arbitration. You may opt out of this arbitration provision by sending written notice to fieldpad@fieldpadpro.com within 30 days of first downloading the App.

---

## 11. Severability

If any provision of these Terms is found to be unenforceable, the remaining provisions will continue in full force and effect.

---

## 12. Changes to These Terms

We may update these Terms periodically. We will notify you of material changes by updating the "Last Updated" date. Continued use of the App after changes constitutes acceptance of the updated Terms.

---

## 13. Contact

For questions about these Terms, contact us at: **fieldpad@fieldpadpro.com**
