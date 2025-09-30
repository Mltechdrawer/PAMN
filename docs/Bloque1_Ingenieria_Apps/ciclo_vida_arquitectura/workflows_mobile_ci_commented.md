# Workflows CI/CD para Apps Nativas — Versiones **comentadas**


## Android (Kotlin, Gradle) — build, tests, Firebase App Distribution y Google Play (tracks)

> Guarda este contenido como: `.github/workflows/android-ci.yml`

```yaml
# =========================
#  Android CI — Workflow
#  Build, tests, distribución a testers (Firebase) y publicación en Google Play
# =========================

name: Android CI  # Nombre visible del workflow en la UI de GitHub

on:
  push:
    branches: [ main ]   # Ejecuta en pushes a la rama main; ajusta si usas "develop" u otras
  pull_request:          # Ejecuta también en PRs para validar cambios antes de fusionarlos

jobs:
  # -------------------------
  # 1) Compilación y tests unitarios (rápidos, sin emulador)
  # -------------------------
  build-and-unit-tests:
    runs-on: ubuntu-latest             # Runner Linux (rápido y económico); suficiente para unit tests
    permissions:
      contents: read                   # Permiso mínimo necesario para hacer checkout del código

    steps:
      - name: Checkout
        uses: actions/checkout@v4      # Descarga el repo en el workspace del runner

      - name: Set up Java 17
        uses: actions/setup-java@v4
        with:
          distribution: temurin        # JDK Temurin (Adoptium), estable y recomendado
          java-version: 17             # JDK versión 17 (frecuente en Android/Gradle actuales)

      - name: Setup Gradle
        uses: gradle/gradle-build-action@v3   # Configura Gradle y activa caché de dependencias automáticamente

      - name: Setup Android SDK
        uses: android-actions/setup-android@v3  # Instala herramientas del Android SDK según tu build
        # Si necesitas paquetes/SDK específicos, puedes añadirlos vía 'packages:' (ver docs de la action)

      - name: Build (assemble + unit tests)
        run: ./gradlew clean assembleDebug testDebugUnitTest --stacktrace
        # 'assembleDebug' genera el APK debug; 'testDebugUnitTest' ejecuta tests unitarios (JVM)
        # '--stacktrace' facilita el diagnóstico al fallar la build

  # -------------------------
  # 2) Tests instrumentados (UI) en emulador Android
  #    Nota: macOS suele ser más estable para emuladores x86_64
  # -------------------------
  android-instrumented-tests:
    runs-on: macos-latest              # Ejecuta en macOS para mayor estabilidad del emulador
    needs: build-and-unit-tests        # Solo corre si el job anterior pasó correctamente

    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: 17
      - name: Setup Gradle
        uses: gradle/gradle-build-action@v3

      - name: Run UI tests on emulator
        uses: reactivecircus/android-emulator-runner@v2
        with:
          api-level: 34                # Selecciona la API a probar (puedes usar matrices si quieres varias)
          arch: x86_64                 # Arquitectura del emulador
          profile: pixel_6             # Perfil de dispositivo (opcional, ayuda a reproducibilidad)
          script: ./gradlew connectedDebugAndroidTest
          # 'connectedDebugAndroidTest' lanza tests instrumentados contra el emulador iniciado por la action

  # -------------------------
  # 3) Distribución a testers con Firebase App Distribution (rama main)
  # -------------------------
  distribute-firebase:
    runs-on: ubuntu-latest
    needs: build-and-unit-tests        # Requiere que la compilación y tests unitarios se hayan completado
    if: github.ref == 'refs/heads/main'  # Solo en pushes a main (evita distribuir builds de PRs/ramas)

    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: 17
      - uses: gradle/gradle-build-action@v3

      - name: Build release
        run: ./gradlew clean bundleRelease
        # Genera un Android App Bundle (AAB) de release; ideal para testers y para Play

      - name: Upload to Firebase App Distribution
        env:
          FIREBASE_TOKEN: ${{ secrets.FIREBASE_TOKEN }}
          # Alternativa: usar credenciales de servicio JSON y configurar el plugin Gradle de App Distribution
        run: |
          ./gradlew appDistributionUploadRelease             -PfirebaseServiceCredentialsFile=$GITHUB_WORKSPACE/fastlane/firebase-service-account.json             -PfirebaseTesters="${{ vars.FIREBASE_TESTERS }}"             -PfirebaseReleaseNotes="CI upload from $GITHUB_SHA"
        # Notas:
        # - Define testers (emails/grupos) en 'FIREBASE_TESTERS' o en build.gradle según prefieras.
        # - 'FIREBASE_TOKEN' o credenciales de servicio son necesarios para autenticar.

  # -------------------------
  # 4) Publicación en Google Play (track internal) al crear un tag 'v*'
  #    Usamos Gradle Play Publisher (GPP) con cuenta de servicio JSON
  # -------------------------
  publish-play-internal:
    runs-on: ubuntu-latest
    needs: build-and-unit-tests            # Asegura que la build pasó antes de publicar
    if: startsWith(github.ref, 'refs/tags/v')  # Solo al publicar una etiqueta de release (ej: v1.2.3)

    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: 17
      - uses: gradle/gradle-build-action@v3

      - name: Build AAB
        run: ./gradlew clean bundleRelease
        # Vuelve a generar el AAB de release, asegurando artefacto fresco para Play

      - name: Publish to Play (internal track) via GPP
        env:
          PLAY_SERVICE_ACCOUNT_JSON: ${{ secrets.PLAY_SERVICE_ACCOUNT_JSON }}  # Contenido JSON (base64 o texto)
        run: |
          # Escribimos el JSON de la cuenta de servicio a un fichero para usarlo con GPP
          echo '${{ secrets.PLAY_SERVICE_ACCOUNT_JSON }}' > play-sa.json
          # Ejecutamos publicación con track 'internal' (puedes usar 'alpha', 'beta', 'production')
          ./gradlew publishRelease             -Pandroid.injected.signing.store.file=$GITHUB_WORKSPACE/keystore.jks             -Pandroid.injected.signing.store.password=${{ secrets.SIGNING_STORE_PASSWORD }}             -Pandroid.injected.signing.key.alias=${{ secrets.SIGNING_KEY_ALIAS }}             -Pandroid.injected.signing.key.password=${{ secrets.SIGNING_KEY_PASSWORD }}             -Pplay.serviceAccountCredentials=$GITHUB_WORKSPACE/play-sa.json             -Pplay.track=internal
        # Requisitos:
        # - Keystore y passwords en Secrets (SIGNING_*).
        # - Cuenta de servicio con permisos en Play Console (rol 'Release Manager' o similar).
        # - El plugin GPP configurado en build.gradle (applicationId, etc.).
```

---

## iOS (Swift o Kotlin Multiplatform iOS) — build + unit tests + TestFlight

> Guarda este contenido como: `.github/workflows/ios-ci.yml`

```yaml
# =========================
#  iOS CI — Workflow
#  Build, tests en simulador y subida a TestFlight (App Store Connect)
# =========================

name: iOS CI  # Nombre del workflow

on:
  push:
    branches: [ main ]        # Ejecutar en pushes a main
  pull_request:               # Validar también en PRs
  workflow_dispatch:          # Permite lanzarlo manualmente desde la UI de Actions

jobs:
  build-test-upload:
    runs-on: macos-latest     # Requisito para Xcode y simuladores iOS
    timeout-minutes: 60       # Evita jobs colgados; ajusta según tiempos de tu proyecto

    steps:
      - name: Checkout
        uses: actions/checkout@v4   # Descarga el repo

      - name: Show Xcode version
        run: xcodebuild -version    # Útil para logs y depuración (muestra versión de Xcode usada)

      - name: Set up Ruby & Fastlane
        run: |
          # Fastlane es la herramienta de facto para automatizar iOS (build, firma y distribución)
          gem install bundler:2.5.10
          gem install fastlane --no-document

      - name: Build & unit tests (Simulator)
        run: |
          # Compila y ejecuta tests en simulador (no requiere firma)
          # Ajusta el esquema ("App") y el destino (dispositivo/OS) a tu proyecto
          xcodebuild             -scheme "App"             -sdk iphonesimulator             -destination 'platform=iOS Simulator,name=iPhone 15,OS=17.5'             clean test | xcpretty
        # Consejo: usa 'xcpretty' para mejorar la legibilidad del log

      - name: Build .ipa with gym (fastlane)
        env:
          MATCH_KEYCHAIN_NAME: build.keychain   # (Opcional) si usas match / keychains custom
        run: |
          # 'gym' genera el .ipa listo para distribución (requiere firma)
          # --export_method app-store: orientado a TestFlight / App Store
          fastlane gym             --scheme "App"             --configuration Release             --export_method app-store
        # Requisitos de firma:
        # - Certificados y perfiles de aprovisionamiento válidos.
        # - Puedes gestionarlos con fastlane match o subirlos como Secrets y configurarlos en el runner.

      - name: Upload to TestFlight (fastlane pilot)
        env:
          # API Key de App Store Connect (recomendado frente a Apple ID + 2FA)
          APP_STORE_CONNECT_API_KEY_ID: ${{ secrets.APPSTORE_KEY_ID }}
          APP_STORE_CONNECT_API_KEY_ISSUER_ID: ${{ secrets.APPSTORE_ISSUER_ID }}
          APP_STORE_CONNECT_API_KEY_CONTENT: ${{ secrets.APPSTORE_KEY_CONTENT }}  # Contenido del .p8
        run: |
          # Construimos el JSON de la API Key en caliente y lo pasamos a fastlane pilot
          fastlane pilot upload             --api_key_path <(cat <<EOF
          {
            "key_id": "${APP_STORE_CONNECT_API_KEY_ID}",
            "issuer_id": "${APP_STORE_CONNECT_API_KEY_ISSUER_ID}",
            "key": "${APP_STORE_CONNECT_API_KEY_CONTENT}",
            "in_house": false
          }
          EOF
          )             --skip_waiting_for_build_processing true
        # Notas:
        # - 'skip_waiting_for_build_processing' devuelve el control antes de que Apple finalice el procesado.
        # - Asegúrate de que los metadatos de la app están configurados en App Store Connect para TestFlight.
```
