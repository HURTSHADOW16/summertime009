# summertime009
name: Construir APK

on:
  push:
    branches: [main]
  workflow_dispatch:

jobs:
  apk:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 22
      - uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: 21
      - name: Preparar proyecto
        run: |
          mkdir www
          cp index.html manifest.webmanifest sw.js icon.svg icon-192.png icon-512.png icon-maskable-512.png www/
          npm init -y
          npm install @capacitor/core@7 @capacitor/cli@7 @capacitor/android@7
          echo '{"appId":"com.summertime.musica","appName":"Summertime","webDir":"www"}' > capacitor.config.json
      - name: Crear proyecto Android
        run: |
          npx cap add android
          npx cap sync android
      - name: Compilar APK
        working-directory: android
        run: |
          chmod +x gradlew
          ./gradlew assembleDebug
      - name: Guardar APK
        uses: actions/upload-artifact@v4
        with:
          name: Summertime-apk
          path: android/app/build/outputs/apk/debug/app-debug.apk
