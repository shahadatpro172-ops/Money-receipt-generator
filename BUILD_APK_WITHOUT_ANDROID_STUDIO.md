# Build Money Receipt Generator APK without Android Studio

This project is already a native Android application (Kotlin + Jetpack Compose), not a web-only project.

## Cloud build using GitHub Actions
1. Create/sign in to a GitHub account.
2. Create a new **private** repository.
3. Upload the contents of this project to the repository root (not the ZIP file itself).
4. Open the repository's **Actions** tab.
5. If prompted, enable GitHub Actions.
6. Open **Build Money Receipt Generator APK** and click **Run workflow** (or push to `main`).
7. Wait for the workflow to finish. If it succeeds, open the completed run and download the artifact named `money-receipt-generator-debug-apk`.
8. Extract the artifact ZIP. It contains `app-debug.apk`, which can be installed on an Android phone.

## Important notes
- This workflow builds a **debug APK** for testing. It is not a Play Store release.
- The project currently has no `google-services.json` and no `.env` file. Google Services is configured to warn if the Firebase file is absent. Any feature that requires Firebase or a Gemini API key may need additional setup.
- The build may reveal dependency or Android SDK compatibility issues. If the GitHub Actions run fails, share the failed log so the exact issue can be fixed.
- Keep the repository private because the project source may contain application logic or configuration.
- Do not upload private API keys, passwords, or signing keystores to a public repository.
