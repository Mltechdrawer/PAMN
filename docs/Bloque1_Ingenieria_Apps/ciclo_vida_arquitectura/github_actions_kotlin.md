# GitHub Actions (Kotlin con comentarios)

## Ejemplos prácticos

### Ejemplo 1: Compilar y ejecutar pruebas en Kotlin (Gradle)

``` yaml
# Nombre del workflow
name: Kotlin CI

# Eventos que disparan el workflow: cada push o pull request
on: [push, pull_request]

jobs:
  build:
    # El job se ejecuta en un runner de Ubuntu (máquina virtual proporcionada por GitHub)
    runs-on: ubuntu-latest

    steps:
      # Paso 1: Descargar el código fuente del repositorio
      - uses: actions/checkout@v4

      # Paso 2: Configurar JDK 17 con la distribución Temurin
      - name: Set up JDK 17
        uses: actions/setup-java@v4
        with:
          java-version: '17'
          distribution: 'temurin'

      # Paso 3: Configurar Gradle y habilitar caché automática de dependencias
      - name: Setup Gradle
        uses: gradle/actions/setup-gradle@v3

      # Paso 4: Compilar el proyecto con Gradle
      - name: Build with Gradle
        run: ./gradlew build --stacktrace

      # Paso 5: Ejecutar los tests definidos en el proyecto
      - name: Run tests
        run: ./gradlew test
```

------------------------------------------------------------------------

### Ejemplo 2: Desplegar en GitHub Pages (Kotlin/JS o Dokka)

Este ejemplo asume que el proyecto genera un sitio estático con Gradle
en la carpeta `build/distributions` (para Kotlin/JS) o
`build/dokka/html` (para documentación con Dokka). Ajusta `publish_dir`
según corresponda.

``` yaml
# Nombre del workflow
name: Deploy Kotlin site to GitHub Pages

# Evento: se ejecuta al hacer push en la rama "main"
on:
  push:
    branches: [ main ]

jobs:
  deploy:
    # El job se ejecuta en un runner Ubuntu
    runs-on: ubuntu-latest

    steps:
      # Paso 1: Descargar el código fuente
      - uses: actions/checkout@v4

      # Paso 2: Configurar JDK 17 (necesario para ejecutar Gradle)
      - name: Set up JDK 17
        uses: actions/setup-java@v4
        with:
          java-version: '17'
          distribution: 'temurin'

      # Paso 3: Configurar Gradle y habilitar caché automática
      - name: Setup Gradle
        uses: gradle/actions/setup-gradle@v3

      # Paso 4: Compilar/generar el sitio estático con Gradle
      - name: Build static site
        run: ./gradlew build

      # Paso 5: Publicar el contenido generado en GitHub Pages
      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        with:
          # Token de autenticación proporcionado automáticamente por GitHub
          github_token: ${{ secrets.GITHUB_TOKEN }}
          # Directorio con el sitio estático generado
          publish_dir: ./build/distributions
```
