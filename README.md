# SprintClock — Android project

A native Android prototype for two-phone sprint timing. The project is offline-first and does not request Internet access.

## Current flow

### 1. Choose a role
- **START PHONE**: runs the random start cue and timer.
- **STOP PHONE**: records the finish line and lets you select the finish frame.

### 2. Calibrate the clocks once, while phones are together
1. On STOP PHONE, tap **CALIBRATE PHONES**. It displays a sync QR.
2. On START PHONE, tap **SYNC WITH STOP PHONE** and scan that QR.
3. START PHONE displays a response QR.
4. On STOP PHONE, tap **SCAN START RESPONSE** and scan it.

The STOP phone estimates the clock offset from the four timestamps (an NTP-style midpoint calculation) and saves it locally. No clapping or Internet connection is used.

### 3. Time the sprint
1. On STOP PHONE, tap **START TIMING** before the runner starts. The camera opens and video recording begins.
2. On START PHONE, tap **START TIMING**. A white screen displays **SET**. After a random delay from 0 to 6 seconds, the phone plays the included bang sound, flashes its torch briefly, and starts the timer.
3. On STOP PHONE, tap the glass check button after the runner crosses the finish line. Recording stops.
4. Review the circular video window. Use PREV/NEXT or the slider to move through frames. The displayed timestamp is relative to the beginning of the video. Choose the frame where the runner's chest first reaches the white line.
5. On START PHONE, tap the glass check button below the timer to show the start-event QR. Bring the phones together and scan it from the STOP phone using **SCAN START QR** on the review screen or **IMPORT START QR** on its home screen.
6. The STOP phone calculates the sprint time using the selected frame time, imported start timestamp, and saved clock offset.

## Build an APK

This environment did not have the Android SDK or Gradle installed, and its build dependency hosts were unreachable. **No installable APK was compiled here.** The files in this folder are the Android Studio project source, not an APK.

To build it on a computer with Android Studio:

1. Install Android Studio with Android SDK Platform 35 and JDK 17.
2. Use Android Gradle Plugin 8.7.3 with Gradle 8.9 (AGP 8.7 requires Gradle 8.9).
3. Open this `SprintClockAndroid` folder in Android Studio.
4. Allow Gradle sync to download the AndroidX CameraX, AppCompat, and ZXing dependencies.
5. Select **Build → Build Bundle(s) / APK(s) → Build APK(s)**.
6. Android Studio places the debug APK under `app/build/outputs/apk/debug/app-debug.apk`.

The generated debug APK can be installed on both Android phones. The app needs Camera permission for video, QR scanning, and the start-phone flash.

## Accuracy and limitations

- The timer display uses Android's monotonic elapsed-realtime clock. The event is also stored as a local wall-clock timestamp so it can be transferred between phones.
- QR calibration uses a two-message timestamp exchange. Its displayed uncertainty is an estimate based on round-trip delay; human scan delay can be asymmetric, so it is not a laboratory-grade clock synchronization protocol.
- Finish selection is video-frame based. At 30 fps, adjacent frames are about 33 ms apart; camera exposure, rolling shutter, video encoding, and the exact captured frame rate also affect the result. This is not equivalent to certified photo-finish equipment or timing gates.
- Since the workflow is fully offline, QR codes can only be scanned when the phones are physically close. Calibrate before placing the phones at opposite ends; after the run, bring the phones together to transfer the START event QR. A true remote transfer would need a separate Bluetooth or local Wi-Fi communication feature.
- Use the phone's rear camera on a stable support, frame the finish line before recording, and make sure the line stays visible. Do not hand-hold the STOP phone while trying to follow the runner.
- The app does not override the phone's media volume. Raise the volume before using the start bang.

## Build an APK automatically (no Android Studio required)

1. Create a new empty GitHub repository.
2. Upload the contents of this project ZIP to the repository (keep the `.github/workflows/build-apk.yml` file).
3. Open the repository's **Actions** tab and select **Build SprintClock APK**. Choose **Run workflow** if it has not started automatically.
4. When the run finishes, open it and download the **SprintClock-debug-apk** artifact. Inside it is `app-debug.apk`.
5. Transfer that APK to each Android phone and install it. Android may ask you to allow installation from that source.

This produces a debug APK for testing, not a Play Store release-signed APK.
