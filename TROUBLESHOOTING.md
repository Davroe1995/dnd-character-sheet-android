# Android Build Troubleshooting Guide

## Common Build Errors and Solutions

### 1. "No matching variant of project :capacitor-android was found"

**Error Message:**
```
No matching variant of project :capacitor-android was found. The consumer was configured to find a library for use during compile-time, preferably optimized for Android, as well as attribute 'com.android.build.api.attributes.AgpVersionAttr' with value '8.7.2'
```

**Cause:** Version incompatibility between Android Gradle Plugin and Capacitor.

**Solution:** ✅ **FIXED** - Updated to compatible versions:
- Android Gradle Plugin: `8.2.2` (was `8.7.2`)
- Gradle Wrapper: `8.2.1` (was `8.11.1`)

**Files Modified:**
- `android/build.gradle` - Updated AGP version
- `android/gradle/wrapper/gradle-wrapper.properties` - Updated Gradle version

### 2. "SDK location not found"

**Error Message:**
```
SDK location not found. Define a valid SDK location with an ANDROID_HOME environment variable or by setting the sdk.dir path in your project's local properties file
```

**Solutions:**

#### Option A: Set Environment Variable
```bash
# On Mac/Linux
export ANDROID_HOME=~/Android/Sdk
export PATH=$PATH:$ANDROID_HOME/tools:$ANDROID_HOME/platform-tools

# On Windows
set ANDROID_HOME=C:\Users\[username]\AppData\Local\Android\Sdk
set PATH=%PATH%;%ANDROID_HOME%\tools;%ANDROID_HOME%\platform-tools
```

#### Option B: Create local.properties file
Create `android/local.properties`:
```properties
sdk.dir=/path/to/your/Android/Sdk
```

#### Option C: Configure in Android Studio
1. Open Android Studio
2. Go to **File > Project Structure**
3. Click **SDK Location**
4. Set **Android SDK Location**

### 3. "Gradle sync failed"

**Solutions:**
1. **Clean and rebuild:**
   ```bash
   cd android
   ./gradlew clean
   ./gradlew build
   ```

2. **Invalidate caches in Android Studio:**
   - **File > Invalidate Caches and Restart**

3. **Check internet connection** - Gradle needs to download dependencies

### 4. "App crashes on startup"

**Common Causes:**
1. Web assets not properly synced
2. Incorrect Capacitor configuration
3. Missing permissions

**Solutions:**
1. **Rebuild and sync:**
   ```bash
   npm run build:android
   npx cap sync android
   ```

2. **Check capacitor.config.ts:**
   ```typescript
   const config = {
     appId: "com.example.dndcharactersheet",
     appName: "DNDCharacterSheet",
     webDir: "out",  // Make sure this matches your build output
     bundledWebRuntime: false
   };
   ```

3. **Check Android logs:**
   - Open **Logcat** in Android Studio
   - Filter by your app package name
   - Look for error messages

### 5. "White screen on app launch"

**Cause:** Web assets not found or not properly built.

**Solutions:**
1. **Verify build output:**
   ```bash
   # Check if out directory exists and has content
   ls -la out/
   ```

2. **Rebuild web assets:**
   ```bash
   npm run export
   npx cap copy android
   ```

3. **Check asset location:**
   - Verify files exist in `android/app/src/main/assets/public/`

### 6. "Build tools version not supported"

**Error:** Build tools version X.X.X is not supported.

**Solution:**
1. **Update Android Studio and SDK**
2. **Or downgrade build tools version in `android/variables.gradle`:**
   ```gradle
   ext {
       compileSdkVersion = 34  // Try lower version if needed
       targetSdkVersion = 34
   }
   ```

### 7. "Keystore errors during signing"

**Common Issues:**
- Wrong keystore path
- Incorrect passwords
- Keystore file corrupted

**Solutions:**
1. **Verify keystore path is absolute:**
   ```gradle
   signingConfigs {
       release {
           storeFile file('/absolute/path/to/keystore.jks')
       }
   }
   ```

2. **Test keystore:**
   ```bash
   keytool -list -v -keystore /path/to/keystore.jks
   ```

3. **Create new keystore if needed:**
   ```bash
   keytool -genkey -v -keystore my-release-key.jks -keyalg RSA -keysize 2048 -validity 10000 -alias my-alias
   ```

## Version Compatibility Matrix

| Capacitor | Android Gradle Plugin | Gradle | Android API |
|-----------|----------------------|--------|-------------|
| 6.x       | 8.2.x               | 8.2.x  | 34          |
| 5.x       | 8.0.x               | 8.0.x  | 33          |
| 4.x       | 7.4.x               | 7.6.x  | 32          |

## Quick Diagnostic Commands

```bash
# Check Capacitor version
npx cap --version

# Check Android project status
npx cap doctor android

# List available Android targets
npx cap run android --list

# Build with verbose output
cd android && ./gradlew assembleDebug --info

# Check Gradle version
cd android && ./gradlew --version
```

## Getting Help

1. **Check Capacitor docs:** https://capacitorjs.com/docs
2. **Android developer docs:** https://developer.android.com
3. **Stack Overflow:** Search for specific error messages
4. **Capacitor GitHub issues:** https://github.com/ionic-team/capacitor/issues

## Prevention Tips

1. **Keep versions compatible** - Don't mix latest versions without checking compatibility
2. **Test builds regularly** - Don't wait until deployment to test
3. **Backup keystores** - Store them securely and separately
4. **Document your setup** - Note working version combinations
5. **Use version control** - Commit working configurations before changes
