# Complete Guide: Android Studio Setup and App Bundle Creation

## Prerequisites

1. **Download and Install Android Studio**
   - Go to https://developer.android.com/studio
   - Download Android Studio for your operating system
   - Install with default settings

2. **Install Android SDK**
   - During Android Studio installation, it will prompt to install Android SDK
   - Accept all default SDK components
   - Note the SDK location (usually `~/Android/Sdk` on Mac/Linux or `C:\Users\[username]\AppData\Local\Android\Sdk` on Windows)

## Step 1: Open Your Project in Android Studio

### Method 1: Using Capacitor Command (Recommended)
```bash
# Navigate to your project directory
cd /path/to/your/dnd-character-sheet-project

# This will automatically open Android Studio with your project
npx cap open android
```

### Method 2: Manual Opening
1. Open Android Studio
2. Click "Open an Existing Project"
3. Navigate to your project folder
4. Select the `android` folder (NOT the root project folder)
5. Click "Open"

## Step 2: Initial Android Studio Setup

### Configure SDK Location (if needed)
1. If you see "SDK location not found" error:
   - Go to **File > Project Structure** (or **Android Studio > Preferences** on Mac)
   - Click **SDK Location** in the left panel
   - Set **Android SDK Location** to your SDK path
   - Click **Apply** and **OK**

### Sync Project
1. Android Studio should automatically start syncing
2. If not, click **File > Sync Project with Gradle Files**
3. Wait for sync to complete (may take several minutes first time)

## Step 3: Configure App Signing for Release

### Create a Keystore (First Time Only)
1. Go to **Build > Generate Signed Bundle/APK**
2. Select **Android App Bundle**
3. Click **Next**
4. Click **Create new...** next to Key store path
5. Fill in the keystore information:
   ```
   Key store path: /path/to/your/keystore.jks
   Password: [create a strong password]
   Confirm: [repeat password]
   Alias: dnd-character-sheet
   Password: [create alias password]
   Confirm: [repeat alias password]
   Validity (years): 25
   Certificate:
     First and Last Name: Your Name
     Organizational Unit: [optional]
     Organization: Your Company
     City or Locality: Your City
     State or Province: Your State
     Country Code: US (or your country)
   ```
6. Click **OK**
7. **IMPORTANT**: Save your keystore file and passwords securely!

### Configure Signing in build.gradle (Alternative Method)
1. Open `android/app/build.gradle`
2. Add signing configuration:
```gradle
android {
    ...
    signingConfigs {
        release {
            storeFile file('/path/to/your/keystore.jks')
            storePassword 'your_keystore_password'
            keyAlias 'dnd-character-sheet'
            keyPassword 'your_key_password'
        }
    }
    buildTypes {
        release {
            signingConfig signingConfigs.release
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), 'proguard-rules.pro'
        }
    }
}
```

## Step 4: Generate App Bundle (AAB)

### Method 1: Using Android Studio UI (Recommended)
1. Go to **Build > Generate Signed Bundle/APK**
2. Select **Android App Bundle**
3. Click **Next**
4. Select your keystore file or use existing configuration
5. Enter keystore and key passwords
6. Click **Next**
7. Select **release** build variant
8. Choose destination folder (default is fine)
9. Click **Finish**

### Method 2: Using Gradle Command
```bash
# Navigate to android folder
cd android

# Generate release AAB
./gradlew bundleRelease
```

## Step 5: Locate Your App Bundle

The generated AAB file will be located at:
```
android/app/build/outputs/bundle/release/app-release.aab
```

## Step 6: Test Your App Bundle

### Install on Device/Emulator for Testing
1. **Create APK from AAB for testing**:
   ```bash
   # Install bundletool if not already installed
   # Download from: https://github.com/google/bundletool/releases
   
   # Generate APKs from AAB
   java -jar bundletool.jar build-apks --bundle=app-release.aab --output=app.apks
   
   # Install on connected device
   java -jar bundletool.jar install-apks --apks=app.apks
   ```

2. **Or use Android Studio**:
   - Go to **Build > Build Bundle(s)/APK(s) > Build APK(s)**
   - Install the generated APK on your device for testing

## Step 7: Upload to Google Play Console

1. **Create Google Play Console Account**
   - Go to https://play.google.com/console
   - Pay the $25 one-time registration fee
   - Complete account setup

2. **Create New App**
   - Click **Create app**
   - Fill in app details:
     - App name: "D&D Character Sheet" (or your preferred name)
     - Default language: English (US)
     - App or game: App
     - Free or paid: Free (or Paid)
   - Accept Play Console Developer Policy
   - Click **Create app**

3. **Upload App Bundle**
   - Go to **Release > Production**
   - Click **Create new release**
   - Upload your `app-release.aab` file
   - Add release notes
   - Click **Save** then **Review release**
   - Click **Start rollout to production**

## Troubleshooting Common Issues

### Build Errors
1. **"SDK location not found"**
   - Set ANDROID_HOME environment variable
   - Or configure in Android Studio Project Structure

2. **"Gradle sync failed"**
   - Check internet connection
   - Try **File > Invalidate Caches and Restart**

3. **"Signing config not found"**
   - Verify keystore path is correct
   - Check passwords are correct

### App Issues
1. **App crashes on startup**
   - Check that web assets were properly synced
   - Verify `capacitor.config.ts` settings
   - Check Android logs in **Logcat**

2. **White screen on app launch**
   - Ensure `out` directory contains built web assets
   - Run `npm run build:android` to rebuild and sync

## Important Notes

### Security
- **Never commit keystore files to version control**
- **Store keystore and passwords securely**
- **Backup your keystore file** - you cannot update your app without it

### App Updates
When updating your app:
1. Make changes to your Next.js code
2. Run `npm run build:android`
3. Generate new signed AAB
4. Upload to Google Play Console as new release

### Version Management
- Update version in `android/app/build.gradle`:
```gradle
android {
    defaultConfig {
        versionCode 2        // Increment for each release
        versionName "1.1"    // User-visible version
    }
}
```

## Quick Reference Commands

```bash
# Build and sync web assets
npm run build:android

# Open in Android Studio
npx cap open android

# Generate release AAB (from android folder)
./gradlew bundleRelease

# Sync after code changes
npx cap sync android
```

This guide will help you successfully upload your D&D Character Sheet app to Android Studio and generate an App Bundle for Google Play Store submission.
