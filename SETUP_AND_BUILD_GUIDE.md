# ReceiptPro — Setup, Firebase, Google Drive & Signed APK Guide

## 1. Architecture & Project Structure
- **Package / Application ID**: `com.aistudio.receiptpro.mrxkqv` (Namespace: `com.example`)
- **UI**: Jetpack Compose + Material 3 + Navigation Compose (`@Serializable` routes) + Adaptive `NavigationBar` / `NavigationRail` / `ModalNavigationDrawer`
- **Database**: Room (`AppDatabase`) with `users`, `companies`, `receipts`, `receipt_snapshots`, `backup_records`, and `app_settings`
- **Security**: Android Keystore AES-256-GCM (`CryptoManager`), PBKDF2-HMAC-SHA256 salted hashing, `BiometricPrompt` fingerprint + PIN fallback, configurable auto-lock (`ProcessLifecycleOwner`)
- **Printing & PDF**: Native Android `PdfDocument` (A4 `595x842pt`, excludes `Received By`) & `PrintDocumentAdapter` via `PrintManager` (A4, includes `Received By` with company signature)
- **Background Tasks**: WorkManager (`RecycleBinCleanupWorker`, `DailyBackupWorker`, `PendingDriveUploadWorker`)

## 2. Firebase Email/Password Setup (Android Studio / Firebase Console)
Because AI Studio's automated provisioning supports Google Sign-In only and does not enable Firebase Email/Password Auth, the app includes a dual-mode `AuthRepository`:
- Out of the box, registration/verification/login/reset work offline-first via salted PBKDF2-HMAC-SHA256 + Keystore session.
- To activate live Firebase Email/Password verification & password-reset emails:
  1. Open the [Firebase Console](https://console.firebase.google.com/) and create or select a project.
  2. Go to **Authentication -> Sign-in method -> Email/Password** and enable it.
  3. Add an Android app with package name `com.aistudio.receiptpro.mrxkqv`.
  4. Register your debug & release SHA-1 / SHA-256 certificates.
  5. Download `google-services.json` and place it in `app/google-services.json`.
  6. Rebuild in Android Studio — `AuthRepository` automatically detects `FirebaseApp` and switches to live Firebase Email/Password authentication while maintaining an encrypted offline login cache.

## 3. Google Drive OAuth Backup Setup (Google Cloud Console)
1. Open the [Google Cloud Console](https://console.cloud.google.com/).
2. Enable the **Google Drive API**.
3. Configure the **OAuth consent screen** and add the `https://www.googleapis.com/auth/drive.file` scope.
4. Create an **OAuth 2.0 Client ID** of type **Android**:
   - Package name: `com.aistudio.receiptpro.mrxkqv`
   - SHA-1 fingerprint: extract via `keytool -list -v -keystore <keystore> -alias <alias>`
5. Ensure the same Google Cloud project is linked if using `google-services.json`.

## 4. Building & Generating a Signed APK in Android Studio
1. Open the exported project in **Android Studio Ladybug or newer** (AGP 9.1.1 / JDK 17+).
2. Sync Gradle and run on a physical device or emulator (**Run -> Run 'app'**).
3. To generate a signed APK / App Bundle:
   - Go to **Build -> Generate Signed App Bundle / APK...**
   - Choose **APK** (or **Android App Bundle** for Google Play).
   - Select or create your `.jks` upload keystore, enter store/key passwords and alias, and select the **release** build variant.
   - Android Studio outputs the signed APK to `app/release/app-release.apk`.
