# Privacy Policy Source Code Audit & Review

This document summarizes the technical source-code audit conducted for the **Auto Responder by Mr Rider** Privacy Policy (`privacy-policy.html`).

---

## VERIFIED DATA PRACTICES

Source-code verification across the repository confirms the following actual data handling behavior:

* **WhatsApp Notification Data**: Accessed via Android `NotificationListenerService` (`WhatsAppNotificationListener`). Read and evaluated locally on-device against rules stored in Room SQLite database.
* **On-Device Message Processing**: Incoming notification text, sender contact name/ID, and conversation title are processed 100% locally on-device. Notification text is **NEVER** transmitted off-device, uploaded to Firebase, or shared with third parties.
* **Device Telemetry & Status**: Device ID (`OF-<android_id>`), FCM push token, phone brand/model, OS/app version, business name/number, lock/subscription status, and status heartbeats are transmitted over HTTPS/TLS to Google Firebase (Firestore & FCM) for remote device management.
* **Local Data Storage**: Auto-reply rules, execution logs (`message_logs`), notification deduplication cache (`processed_notifications`), and customer contacts are saved in the local Room SQLite database inside Android's private app sandbox.

---

## LOCAL MODE

* **Workflow**: `Incoming WhatsApp Notification &rarr; WhatsAppNotificationListener &rarr; ProcessIncomingMessageUseCase &rarr; EvaluateRuleUseCase &rarr; LocalReplyHelper (RemoteInput Reply)`
* **Off-Device Transmission**: **ZERO**. Message text, notification contents, and chat logs remain strictly on the user's Android device.
* **Meta API Isolation**: Local Mode functions without Meta API credentials and makes no network calls to `graph.facebook.com`.

---

## FIREBASE / FCM

The following specific fields are transmitted over HTTPS/TLS to Google Firebase (Firestore & FCM) via `FirebaseDeviceRepository` and `DeviceManager`:

* `deviceId` / `generatedUuid` (Persistent device identifier)
* `fcmToken` (Firebase Cloud Messaging push token)
* `phoneModel`, `manufacturer`, `androidVersion`, `appVersion`, `appBuildNumber`, `installationDate`
* `userName`, `businessName`, `phoneNumber` (Configured business profile)
* `adminLocked`, `lockStatus`, `lockReason`, `subscriptionStatus`
* `lastSeen`, `lastSync` (Status heartbeat)
* `commandId`, `command`, `status`, `processedAt` (Remote command acknowledgements)

**Verification**: WhatsApp message content, notification body text, and customer conversation logs are **EXCLUDED** from all Firebase data structures.

---

## THIRD-PARTY SERVICES

The following active third-party infrastructure services are integrated into the application:

* **Google Firebase Firestore**: Remote device registration, status monitoring, and command synchronization.
* **Firebase Cloud Messaging (FCM)**: Real-time push notification delivery for administrative commands.

OrderFlow contains no advertising SDKs, third-party analytics trackers, or commercial data broker libraries.

---

## META API

OrderFlow contains preserved architecture for the Meta WhatsApp Cloud API (`MetaWhatsAppApiService`, `MetaCredentialFetchService`, `WhatsAppCloudApiRepositoryImpl`, `SendCloudReplyUseCase`, `MetaCredentialsWizard`, etc.).

* **Current Status**: Completely inactive in default Local Mode (`useCloudApi = false`).
* **Credentials**: No Meta access tokens, Phone Number IDs, or Meta credentials are required or transmitted during Local Mode operation.

---

## USER DATA DELETION

In-app controls under `Settings &rarr; Data & Device Management` execute the following code paths:

* **Clear Logs & Cache**: Calls `messageLogRepository.clearLogs()` and `processedNotificationRepository.clearAllNotifications()`, permanently deleting local `message_logs` and `processed_notifications` records from the Room SQLite database.
* **Disconnect Device**: Clears the persistent `deviceId` in `SecureStorage` (SharedPreferences) and re-initializes registration with Firebase.

---

## PUBLIC URL TO USE

Intended GitHub Pages public URL:

`https://shavinsolutions.github.io/Auto-Responder-by-Mr-Rider/privacy-policy.html`

*(Note: Mark as intended URL until GitHub Pages publishing and verification are completed).*

---

## APP CONSTANT TO UPDATE

The public Privacy Policy URL is centralized in:

`com.orderflow.autoresponder.core.util.AppConstants.PRIVACY_POLICY_URL`

