
name: Build Boxer Fit APK

on:
  workflow_dispatch:
  push:
    paths:
      - 'BoxerFitAndroid_Project.zip'
      - '.github/workflows/android.yml'

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: '17'

      - uses: android-actions/setup-android@v3

      - name: Install Android SDK
        run: |
          yes | sdkmanager --licenses
          sdkmanager "platforms;android-35" "build-tools;35.0.0"

      - uses: gradle/actions/setup-gradle@v4
        with:
          gradle-version: '8.9'

      - name: Unpack project
        run: unzip BoxerFitAndroid_Project.zip -d source

      - name: Build APK
        working-directory: source/BoxerFitAndroid
        run: gradle --no-daemon assembleDebug

      - name: Upload APK
        uses: actions/upload-artifact@v4
        with:
          name: Boxer-Fit-APK
          path: source/BoxerFitAndroid/app/build/outputs/apk/debug/app-debug.apk
