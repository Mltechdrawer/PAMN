# Integración de Ktlint, CI y Gradle

## ¿Qué es *Ktlint + CI + Gradle*?
Ktlint es una herramienta que analiza el código Kotlin para asegurarse de que sigue las normas de estilo oficiales. Si el código no cumple las reglas: avisa (o falla la build).

[Reglas ktlint](https://github.com/pinterest/ktlint)  
[Reglas ktlint web](https://pinterest.github.io/ktlint/1.7.1/rules/standard/)

Gradle es el sistema de construcción que usa Android (compilar, ejecutar tests, gestionar dependencias). Ktlint se integra como plugin dentro de Gradle.

[Gradle](https://docs.gradle.org/)

CI (Continuous Integration) es un proceso automático que se ejecuta en cada push o pull request en GitHub para verificar:  
- Si el proyecto compila  
- Si los tests pasan  
- Si el estilo es correcto (`ktlint`)  

En este caso se usa [GitHub Actions](https://docs.github.com/en/actions)

> En resumen: Ktlint asegura estilo → Gradle lo ejecuta → GitHub Actions lo comprueba automáticamente.

<details>
<summary>Ventajas de la solución ktlint+CI+Gradle</summary>
<ul>
  <li>
    <strong>Integración nativa con GitHub:</strong> checks en PR, required status, anotaciones en diff,
    permisos y secretos integrados. Menos fricción y cero servidores que mantener.<br>
    <a href="https://docs.github.com/actions" target="_blank">https://docs.github.com/actions</a>
  </li>

  <li>
    <strong>Flujo estándar de Android:</strong> todo pasa por Gradle/AGP, que ya usas para compilar,
    testear y ejecutar Android Lint. Menos herramientas “paralelas”, más reproducibilidad.<br>
    <a href="https://developer.android.com/build" target="_blank">https://developer.android.com/build</a>
  </li>

  <li>
    <strong>Estilo Kotlin “oficial” sin configuración:</strong> ktlint aplica las convenciones de Kotlin
    out-of-the-box; fácil de adoptar por equipos y estudiantes.<br>
    <a href="https://github.com/pinterest/ktlint" target="_blank">https://github.com/pinterest/ktlint</a><br>
    <a href="https://pinterest.github.io/ktlint/latest/rules/" target="_blank">https://pinterest.github.io/ktlint/latest/rules/</a>
  </li>

  <li>
    <strong>Escalable y extensible:</strong> a esto le añades cuando quieras detekt, JaCoCo, SonarCloud/SonarQube,
    pruebas en Firebase Test Lab, etc., sin rehacer el pipeline.<br>
    • Detekt: <a href="https://detekt.dev/" target="_blank">https://detekt.dev/</a><br>
    • JaCoCo: <a href="https://www.jacoco.org/" target="_blank">https://www.jacoco.org/</a><br>
    • SonarCloud: <a href="https://docs.sonarcloud.io/" target="_blank">https://docs.sonarcloud.io/</a><br>
    • Test Lab: <a href="https://firebase.google.com/docs/test-lab" target="_blank">https://firebase.google.com/docs/test-lab</a>
  </li>
</ul>
</details>

---

## Pasos a seguir

### 1. Creación del repositorio
- Crear un repositorio en GitHub para el proyecto Android.
- Añadir el código base y realizar el commit inicial.

### 2. Añadir workflow de CI básico 

Archivo creado: `.github/workflows/android-ci.yml`
Configuración incluida:
- JDK
- SDK Android
- build y tests automáticos

### 3. Añadir plugin Ktlint en Gradle

En `build.gradle.kts` raíz:

```kotlin
plugins {
    id("com.android.application") version "8.1.2" apply false
    id("org.jetbrains.kotlin.android") version "1.9.0" apply false
    id("org.jlleitschuh.gradle.ktlint") version "12.1.1" apply false
}
```
En `app/build.gradle.kts`:

```kotlin
plugins {
    id("com.android.application")
    id("org.jetbrains.kotlin.android")
    id("org.jlleitschuh.gradle.ktlint")
}
```

### 4. Configurar reglas Ktlint

```kotlin
ktlint {
    android.set(true)
    ignoreFailures.set(false)
    reporters {
        reporter(ReporterType.PLAIN)
        reporter(ReporterType.CHECKSTYLE)
        reporter(ReporterType.SARIF)
    }
    filter {
        exclude("**/generated/**")
    }
}
```

### 5. Añadir ejecución de Ktlint al workflow

```yaml
- name: Run ktlint
  run: ./gradlew ktlintCheck
```

### 6. Resolver errores de estilo
Se corrigieron errores de:
- Orden de imports
- Líneas en blanco incorrectas
- Espacios innecesarios

### 7. Confirmación: CI Verde 
El pipeline se ejecuta correctamente:  
- Compila automáticamente  
- Verifica estilo con **ktlint**  
- Ejecuta todo en **GitHub Actions**  
- Falla si hay estilo incorrecto (calidad asegurada)  

---

## Estado final
El proyecto ahora tiene:
- Ktlint configurado
- CI con validación automática
- Calidad de código controlada

> Pipeline:
> 1. Sincronizamos y compilamos (Gradle) --> Gradle avisa cómo ha ido antes de hacer commit  
> 2. CI (Github Actions), en este caso es PR el que inicia y ejecuta Gradle desde Github Actions  

Configuración de ktlint: [Ejemplo práctico 2](calidad_ejemplo2.md "Ejemplo práctico")


>-
>-
>-
>-
>-






<div class="tabbed-set tabbed-alternate" data-tabs="4:2"><input checked="checked" id="__tabbed_4_1" name="__tabbed_4" type="radio" /><input id="__tabbed_4_2" name="__tabbed_4" type="radio" /><div class="tabbed-labels"><label for="__tabbed_4_1"><a href="#"><span class="twemoji"></span></a> Uno</label><label for="__tabbed_4_2"><a href="#"><span class="twemoji"></span></a> Dos</label></div>
<div class="tabbed-content">
<div class="tabbed-block">
<div class="highlight"><pre><span></span><code><a id="__codelineno-15-1" name="__codelineno-15-1" href="#__codelineno-15-1"></a><span class="cm">/*</span>
<a id="__codelineno-15-2" name="__codelineno-15-2" href="#__codelineno-15-2"></a><span class="cm"> * Uno dentro tabla.</span>
<a id="__codelineno-15-3" name="__codelineno-15-3" href="#__codelineno-15-3"></a><span class="cm"> */</span>
</code></pre></div>
</div>
<div class="tabbed-block">
<div class="highlight"><pre><span></span><code><a id="__codelineno-16-1" name="__codelineno-16-1" href="#__codelineno-16-1"></a><span class="cm">/*</span>
<a id="__codelineno-16-2" name="__codelineno-16-2" href="#__codelineno-16-2"></a><span class="cm">      * Dos dentro tabla.</span>
<a id="__codelineno-16-3" name="__codelineno-16-3" href="#__codelineno-16-3"></a><span class="cm">    */</span>
</code></pre></div>
</div>