# Workflows CI/CD para Apps Nativas (Android & iOS)

Para una app nativa tiene **sentido usar CI con GitHub Actions** porque asegura que cada cambio en el código se compile, se pruebe y se valide automáticamente, evitando errores antes de llegar a los testers o a la tienda, y garantizando entregas rápidas y confiables.
Es evidente que este es el motivo por el que cualquier aplicación debería seguir estos pasos. En una App móvil hay que tener en cuenta que: **las tiendas (Google Play / App Store)** son muy estrictas, y cada build debe estar **firmada, validada y generada con las herramientas oficiales**. Con CI (p. ej. GitHub Actions) puedes **automatizar esa preparación (firma, empaquetado, subida a tracks o TestFlight)**, algo que sería tedioso y propenso a fallos si se hiciera manualmente.

Es decir, además de compilar y testear, en móvil CI resuelve el cuello de botella del despliegue en tiendas.


------------------------------------------------------------------------

## Android (Kotlin, Gradle) --- build, tests, Firebase App Distribution y Google Play (tracks)

``` yaml
name: Android CI

on:
  push:
    branches: [ main ]
  pull_request:

jobs:
  build-and-unit-tests:
    runs-on: ubuntu-latest
    permissions:
      contents: read
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Set up Java 17
        uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: 17

      - name: Setup Gradle
        uses: gradle/gradle-build-action@v3

      - name: Setup Android SDK
        uses: android-actions/setup-android@v3

      - name: Build (assemble + unit tests)
        run: ./gradlew clean assembleDebug testDebugUnitTest --stacktrace

  android-instrumented-tests:
    runs-on: macos-latest
    needs: build-and-unit-tests
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
          api-level: 34
          arch: x86_64
          profile: pixel_6
          script: ./gradlew connectedDebugAndroidTest

  distribute-firebase:
    runs-on: ubuntu-latest
    needs: build-and-unit-tests
    if: github.ref == 'refs/heads/main'
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: 17
      - uses: gradle/gradle-build-action@v3
      - name: Build release
        run: ./gradlew clean bundleRelease
      - name: Upload to Firebase App Distribution
        env:
          FIREBASE_TOKEN: ${{ secrets.FIREBASE_TOKEN }}
        run: |
          ./gradlew appDistributionUploadRelease             -PfirebaseServiceCredentialsFile=$GITHUB_WORKSPACE/fastlane/firebase-service-account.json             -PfirebaseTesters="${{ vars.FIREBASE_TESTERS }}"             -PfirebaseReleaseNotes="CI upload from $GITHUB_SHA"

  publish-play-internal:
    runs-on: ubuntu-latest
    needs: build-and-unit-tests
    if: startsWith(github.ref, 'refs/tags/v')
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: 17
      - uses: gradle/gradle-build-action@v3
      - name: Build AAB
        run: ./gradlew clean bundleRelease
      - name: Publish to Play (internal track) via GPP
        env:
          PLAY_SERVICE_ACCOUNT_JSON: ${{ secrets.PLAY_SERVICE_ACCOUNT_JSON }}
        run: |
          ./gradlew publishRelease             -Pandroid.injected.signing.store.file=$GITHUB_WORKSPACE/keystore.jks             -Pandroid.injected.signing.store.password=${{ secrets.SIGNING_STORE_PASSWORD }}             -Pandroid.injected.signing.key.alias=${{ secrets.SIGNING_KEY_ALIAS }}             -Pandroid.injected.signing.key.password=${{ secrets.SIGNING_KEY_PASSWORD }}             -Pplay.serviceAccountCredentials=$GITHUB_WORKSPACE/play-sa.json             -Pplay.track=internal
          echo '${{ secrets.PLAY_SERVICE_ACCOUNT_JSON }}' > play-sa.json
```

------------------------------------------------------------------------

## iOS (Swift o Kotlin Multiplatform iOS) --- build + unit tests + TestFlight

``` yaml
name: iOS CI

on:
  push:
    branches: [ main ]
  pull_request:
  workflow_dispatch:

jobs:
  build-test-upload:
    runs-on: macos-latest
    timeout-minutes: 60

    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Show Xcode version
        run: xcodebuild -version

      - name: Set up Ruby & Bundler
        run: |
          gem install bundler:2.5.10
          gem install fastlane --no-document

      - name: Build & unit tests
        run: |
          xcodebuild             -scheme "App"             -sdk iphonesimulator             -destination 'platform=iOS Simulator,name=iPhone 15,OS=17.5'             clean test | xcpretty

      - name: Build .ipa with gym (fastlane)
        env:
          MATCH_KEYCHAIN_NAME: build.keychain
        run: |
          fastlane gym             --scheme "App"             --configuration Release             --export_method app-store

      - name: Upload to TestFlight (fastlane pilot)
        env:
          APP_STORE_CONNECT_API_KEY_ID: ${{ secrets.APPSTORE_KEY_ID }}
          APP_STORE_CONNECT_API_KEY_ISSUER_ID: ${{ secrets.APPSTORE_ISSUER_ID }}
          APP_STORE_CONNECT_API_KEY_CONTENT: ${{ secrets.APPSTORE_KEY_CONTENT }}
        run: |
          fastlane pilot upload             --api_key_path <(cat <<EOF
          {
            "key_id": "${APP_STORE_CONNECT_API_KEY_ID}",
            "issuer_id": "${APP_STORE_CONNECT_API_KEY_ISSUER_ID}",
            "key": "${APP_STORE_CONNECT_API_KEY_CONTENT}",
            "in_house": false
          }
          EOF
          )             --skip_waiting_for_build_processing true
```


<details>
<summary>Checklist: CI/CD en Apps Nativas</summary>
<p>En apps móviles nativas (Android/iOS) el uso de CI/CD no solo asegura compilación y tests, sino que añade valor específico por la forma en que se distribuyen:</p>
<p>- Compilación con SDKs oficiales (Android SDK, Xcode).</p>
<p>- Firma automática de builds (keystore en Android, certificados y perfiles en iOS).</p>
<p>- Generación de artefactos válidos para tiendas (AAB/APK en Google Play, IPA en App Store).</p>
<p>- Distribución automatizada a testers (Firebase App Distribution, TestFlight).</p>
<p>- Publicación directa en tiendas (Google Play tracks, App Store Connect).</p>
<p>- Reducción de errores manuales en procesos de empaquetado y despliegue.</p>
<p>Con CI/CD en apps nativas, se resuelve el mayor cuello de botella: preparar, firmar y distribuir builds de forma segura y repetible.</p>
</details>


### Opciones avanzadas en CI/CD para apps nativas

**Caché de dependencias más agresivo**  

- Implica cachear no solo Gradle wrapper y dependencias, sino también el Android build cache.  
- Ventaja: builds mucho más rápidas.  
- Riesgo: si el cache se corrompe o queda desincronizado, puede ser necesario limpiar manualmente.  

[Cache de Dependencias](androd_ci_cache_.yml)

**Matrices de versiones**

- Implica definir un job que se ejecute en varias combinaciones de JDK, API level de Android, o incluso distintos sistemas operativos.  
- Ventaja: pruebas de compatibilidad amplia sin duplicar código del workflow.  
- Riesgo: tiempos de ejecución más largos, porque cada combinación lanza un job independiente.  

[Matrices de Versiones](androd_ci_matrix_.yml)

**Firma avanzada**

- Automatizar el uso de keystore (Android) y certificados/provisioning (iOS) desde GitHub Secrets o Environments.  
- Ventaja: builds firmadas automáticamente listas para subir a Play/App Store.  
- Riesgo: mayor complejidad en la gestión de secretos; se deben proteger bien.

[Firma Avanzada](signing.yml)
