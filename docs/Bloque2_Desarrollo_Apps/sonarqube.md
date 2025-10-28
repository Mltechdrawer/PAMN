# SonarQube

## ¿Qué es SonarQube?

**SonarQube** es una plataforma de **análisis estático de código** y **gestión de la calidad del software** que permite inspeccionar proyectos desarrollados en múltiples lenguajes (como Kotlin, Java, Python, C++, JavaScript, etc.).  
Analiza el código fuente y genera métricas que ayudan a mantener la **fiabilidad, mantenibilidad, seguridad y cobertura de pruebas** del software.

A diferencia de herramientas específicas como *Detekt* o *Lint*, SonarQube actúa como un **sistema centralizado de control de calidad**, integrando los resultados de análisis de distintos módulos y proyectos en un único panel visual.

---

## ¿Para qué se utiliza?

SonarQube se usa para **monitorizar y mejorar la calidad del código a lo largo del tiempo**.  
Su objetivo es ayudar a los equipos a escribir código **limpio, seguro y mantenible** mediante la detección de problemas y la definición de estándares de calidad.

Detecta y clasifica:

- **Bugs:** errores que podrían provocar fallos en tiempo de ejecución.  
- **Code smells:** malas prácticas o diseños ineficientes.  
- **Vulnerabilidades:** riesgos de seguridad en el código.  
- **Duplicaciones:** fragmentos de código repetido.  
- **Cobertura de pruebas:** porcentaje de código cubierto por tests automatizados.  
- **Deuda técnica:** esfuerzo estimado para corregir los problemas detectados.

---

## Ejemplo de problemas que puede detectar

| Tipo de problema | Ejemplo | Riesgo |
|------------------|----------|--------|
| **Bug** | División por cero o acceso nulo (`NullPointerException`) | Error en tiempo de ejecución. |
| **Code smell** | Métodos con demasiadas líneas o parámetros | Dificulta la comprensión y mantenimiento. |
| **Vulnerabilidad** | Uso inseguro de criptografía o SQL sin parametrizar | Riesgo de ataques (inyección o fuga de datos). |
| **Duplicación** | Copiar y pegar lógica en varias clases | Riesgo de errores al modificar. |
| **Falta de cobertura** | Funciones sin pruebas unitarias | Posibles errores no detectados. |

---

## Cómo se integra en un proyecto Android o Kotlin

SonarQube puede instalarse localmente o usarse en la nube (por ejemplo, **SonarCloud**).  
El análisis se realiza a través del **plugin de Gradle** o mediante **CI/CD pipelines** como GitHub Actions o GitLab CI.

### Integración básica con Gradle

1. Añadir el plugin en `build.gradle`:
    ```kotlin
    plugins {
        id("org.sonarqube") version "4.4.1.3373"
    }
    ```

2. Configurar los parámetros del proyecto (en `build.gradle` o `gradle.properties`):
    ```properties
    sonar.projectKey=com.example.todo
    sonar.organization=nombre-organizacion
    sonar.host.url=https://sonarcloud.io
    sonar.login=token_de_autenticacion
    ```

3. Ejecutar el análisis:
    ```bash
    ./gradlew sonarqube
    ```

4. Consultar los resultados en el panel web de SonarQube o SonarCloud:
    - Métricas generales del proyecto.
    - Evolución de la deuda técnica.
    - Alertas de calidad (*Quality Gates*).

---

## Quality Gates y deuda técnica

Uno de los conceptos clave de SonarQube son los **Quality Gates**:  
reglas que definen cuándo un proyecto cumple los estándares de calidad mínimos para poder desplegarse.

Por ejemplo, un *Quality Gate* puede exigir:

- 0 bugs críticos o vulnerabilidades abiertas.  
- Cobertura de tests > 80 %.  
- Duplicación de código < 5 %.  
- Complejidad ciclomática por debajo de un umbral definido.

Si alguna condición no se cumple, el pipeline CI/CD puede **bloquear el despliegue automático** hasta que se resuelva.

La **deuda técnica** se expresa como el tiempo estimado para corregir todos los problemas detectados (por ejemplo, “2 días 4 horas”), lo que permite planificar refactorizaciones.

---

## Seguridad y cumplimiento

SonarQube incluye más de **5.000 reglas de seguridad**, alineadas con estándares internacionales como **OWASP Top 10** y **CWE**.  
Esto lo convierte en una herramienta esencial para asegurar el cumplimiento normativo y la protección de datos en proyectos empresariales o públicos.

---

## En resumen

> **SonarQube** es una plataforma integral de control de calidad del código.  
>  
> Permite detectar errores, vulnerabilidades, duplicaciones y medir la cobertura de pruebas,  
> ofreciendo una visión global del estado del proyecto y de su evolución en el tiempo.  
>  
> Se integra fácilmente en los flujos DevOps y se utiliza para garantizar la **calidad continua** del software.

