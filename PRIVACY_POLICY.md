
Huggler Holdings LLC operates FieldPad. Contact **fieldpad@fieldpadpro.com** with privacy questions or requests.

## Version and feature scope

The public App Store app is **1.0.1**. **FieldPad 1.1 is under validation.** The current candidate has FieldPad Cloud/Firebase, Stripe, QuickBooks Online and product-analytics transmission disabled. The descriptions below distinguish current service providers from planned optional connections. A future connection is not active merely because it is described here.

## Records you enter

FieldPad stores customer and business contact details, job and equipment history, technician/certification information, estimates, invoice and payment records, refrigerant service evidence, signatures, and settings needed to provide the functions you use. These records may contain personal information about you and your customers. Use only information you are authorized to record.

Local operational records are stored in the app’s device storage. The local/private-iCloud mode can synchronize supported records through your private Apple CloudKit account when iCloud is available. iCloud availability and storage limits affect synchronization; synchronization is not a guarantee that every record is backed up. Keep and verify independent exports for records you need to retain.

## Current external services

- **Apple and RevenueCat:** process App Store purchases, receipts, subscription status and app-user identifiers. RevenueCat may process IP addresses and device/app information needed to provide its service. Local-only installations use an installation identity; planned managed-account billing uses an opaque identity associated with the account. An opaque identifier is not a claim that all purchase information is unlinked to a person. Customer job and refrigerant records are not sent as subscription purchase data. [RevenueCat privacy](https://www.revenuecat.com/privacy).
- **Apple MapKit:** address-search text is sent to Apple when you use address autocomplete. [Apple privacy](https://www.apple.com/privacy/).
- **Apple iCloud:** supported operational records synchronize to the user’s private CloudKit account when that mode is enabled and available. FieldPad’s operator does not have administrative access to your private iCloud container.
- **Sharing and support:** when you export or share a document, you choose its destination. If you email support, we receive the message and attachments you send; avoid including unnecessary customer records or secrets.

## Camera, selected images and contacts

Cylinder OCR processes camera frames on the device. Scan results you confirm become records and may be included in your sync or exports. Selected business logos and captured signatures are saved as app data and can appear in invoices or archives you share. Contact import uses the contacts you choose. These distinctions matter: an on-device camera does not mean the text you save or a PDF you send can never leave the device.

## Planned optional connected features in 1.1

These flows remain disabled in the current candidate and need acceptance before release:

- **FieldPad Cloud on Google Cloud/Firebase:** account identifiers, membership/organization information, selected operational records, sync changes and uploaded archives would be processed in FieldPad’s managed backend. This is a separate data path from private iCloud. App Check and authentication protect access; managed records can require online authorization on cold launch.
- **QuickBooks Online/Intuit:** an authorized connection would exchange supported customer, item, estimate/invoice and reconciliation information with the connected company. Exact supported operations will be shown at release. Disconnecting stops future authorized synchronization; it does not erase records already held in QuickBooks.
- **Stripe:** enabled payment connections would process hosted checkout, connected-account and payment-status information. Payment details entered in Stripe’s hosted flow are handled by Stripe. Disconnecting does not erase payment records retained by Stripe or reverse transactions.
- **Optional product usage sharing:** the candidate includes a default-off consent setting and a first-party PostHog transport, currently unconfigured. If enabled in a future build and opted into, it sends allowlisted product events with an installation/consent identifier, rather than customer names, document contents or refrigerant evidence. Turning sharing off stops this event transport. Local counters can still support app functions.

[Google privacy](https://policies.google.com/privacy), [Intuit privacy](https://www.intuit.com/privacy/statement/), [Stripe privacy](https://stripe.com/privacy), and [PostHog privacy](https://posthog.com/privacy) explain those providers’ practices. Revisit this policy and the app’s connection screens before enabling a new service.

## Retention, deletion and recovery

Keep records for the periods applicable to the record and equipment. Many federal refrigerant records require at least three years; certain foundational equipment records must remain through three years after retirement. A single blanket expiry does not fit every record. App retention protections are aids and do not determine every legal obligation.

Local deletion and export options depend on the installed build and retention state. Deleting the app can remove local records; private iCloud copies, independent exports and documents sent to others can remain. Manage private iCloud data through Apple settings. Cancelling a subscription does not itself delete records or erase provider-held purchase information. Contact us for requests concerning support or subscription data we control.

Managed-cloud deletion and restore procedures are still in acceptance for 1.1. They will be disclosed with the enabled release; no managed-cloud deletion completion or fixed retention period is promised by this preview. Preserve legally required records before deleting any copy. Provider retention obligations can differ.

## Security and your choices

FieldPad uses iOS app storage protections and HTTPS for supported network communication. No system can guarantee complete security or recovery. Protect your device and Apple account, review shared exports, and verify backups.

We do not sell your customer records or use them to train machine-learning models. The reviewed candidate declares no cross-app advertising tracking. Privacy labels for a released build must reflect that build’s enabled services; this page does not assert that all categories are “not linked to you.”

Depending on your location, you may have rights to access, correct, delete or obtain a copy of personal information we control. Contact us to make a request. We may need to verify the request and retain information when required by law. Private iCloud data is managed through your Apple account.

FieldPad is a professional service tool and is not directed at children under 13. We will update this policy when material data practices change.
