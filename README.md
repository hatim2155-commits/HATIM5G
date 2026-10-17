name: Build Debug APK

on:
  push:
    branches: [ main, master ]
  workflow_dispatch:

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Extract any project .zip found at repo root
        run: |
          set -e
          ZIP_FILE=$(find . -maxdepth 1 -iname "*.zip" | head -n 1)
          if [ -n "$ZIP_FILE" ]; then
            echo "Found zip: $ZIP_FILE — extracting..."
            unzip -o "$ZIP_FILE" -d extracted_project
          else
            echo "No zip found at repo root; assuming project files are already unpacked."
          fi

      - name: Locate the actual Gradle project directory
        id: locate
        run: |
          set -e
          PROJECT_DIR=$(find . -maxdepth 6 -name "settings.gradle.kts" -exec dirname {} \; | head -n 1)
          if [ -z "$PROJECT_DIR" ]; then
            echo "::error::Could not find settings.gradle.kts anywhere in the repo or extracted zip."
            exit 1
          fi
          echo "Project directory: $PROJECT_DIR"
          echo "project_dir=$PROJECT_DIR" >> "$GITHUB_OUTPUT"

      - name: Set up JDK 17
        uses: actions/setup-java@v4
        with:
          distribution: 'temurin'
          java-version: '17'

      - name: Set up Android SDK
        uses: android-actions/setup-android@v3

      - name: Set up Gradle
        uses: gradle/actions/setup-gradle@v3
        with:
          gradle-version: '8.7'

      - name: Build debug APK
        working-directory: ${{ steps.locate.outputs.project_dir }}
        run: gradle assembleDebug --no-daemon

      - name: Upload APK artifact
        uses: actions/upload-artifact@v4
        with:
          name: HatimApp-debug-apk
          path: ${{ steps.locate.outputs.project_dir }}/app/build/outputs/apk/debug/app-debug.apk
          if-no-files-found: error
          
