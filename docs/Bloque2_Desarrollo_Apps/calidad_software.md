# Calidad del software en el desarrollo de aplicaciones móviles nativas

## Introducción: la calidad como eje del desarrollo profesional

La **calidad del software** es un pilar esencial en el desarrollo de aplicaciones móviles nativas. No basta con que una app “funcione”; debe hacerlo de forma **eficiente, segura, estable y sostenible**. Una aplicación de calidad mejora la experiencia del usuario, fortalece la confianza en la marca y reduce costes de mantenimiento.

En el contexto móvil, los fallos o un mal rendimiento pueden implicar pérdidas económicas, desinstalaciones o incluso la eliminación de la app de las tiendas oficiales. Por ello, la calidad debe abordarse desde el primer día.

Bajo el enfoque **DevOps**, la calidad se concibe como un proceso **continuo y automatizado**, que acompaña todo el ciclo de vida del software: planificación, desarrollo, pruebas, despliegue y monitorización. Cada versión debe superar controles de calidad antes de llegar al usuario.

---
<details>
  <summary><strong>Ejemplos de pérdidas económicas por crashes y errores en producción</strong></summary>
  <h3>1. Pérdida directa de usuarios y reputación</h3>
  <p>En el ecosistema móvil, un <em>crash</em> frecuente provoca desinstalaciones inmediatas y reseñas negativas.</p>

  <p><strong>Snapchat (2015):</strong> una actualización defectuosa en iOS provocaba cierres al abrir la cámara frontal.<br>
  → Millones de usuarios reportaron el fallo y la app perdió posición en la App Store en menos de 48 h.<br>
  → Coste estimado: cientos de miles de dólares en ingresos publicitarios perdidos y deterioro de la marca.</p>

  <p><strong>Amazon Shopping (2018):</strong> un bug en la versión Android provocaba cierres al iniciar sesión.<br>
  → En el “Prime Day”, durante una de las campañas más rentables del año, se registró una caída temporal de conversión del 7 %.<br>
  → A escala global, eso equivale a <strong>más de 20 millones de dólares perdidos en pocas horas.</strong></p>

  <h3>Impacto en el negocio por errores en producción</h3>
  <p>Cuando un fallo llega a producción, no solo se pierden ingresos: el coste de arreglarlo se multiplica.</p>

  <p><strong>Toyota (2005, software embebido):</strong> una excepción no controlada en el sistema de aceleración causó fallos críticos.<br>
  → El defecto costó a la compañía más de <strong>1.200 millones de dólares</strong> en demandas y retirada de vehículos.<br>
  → Aunque no era móvil, el principio es el mismo: una excepción sin control = fallo catastrófico.</p>

  <p><strong>Bank of America (2019):</strong> una actualización de la app móvil generó cierres cuando los usuarios cambiaban de pestaña mientras se actualizaba la base de datos local.<br>
  → Miles de usuarios no pudieron acceder a su cuenta durante más de 24 h.<br>
  → Impacto estimado: <strong>decenas de millones</strong> en atención al cliente y pérdida de confianza.</p>

  <h3>Coste de mantenimiento y recuperación de errores</h3>
  <p>Un <em>crash</em> no controlado suele requerir una revisión de emergencia, interrumpiendo el ciclo DevOps normal.
  Esto implica horas de trabajo no planificadas, <em>hotfixes</em>, revisión de QA y pérdida de productividad.</p>

  <p>Según el informe de IBM “Cost of a Bug” (2016),<br>
  <strong>arreglar un error en producción puede costar hasta 100 veces más que detectarlo en la fase de desarrollo.</strong></p>

  <p><strong>Ejemplo numérico:</strong><br>
  Detectarlo en desarrollo: ~100 €.<br>
  Detectarlo tras despliegue: ~10.000 € (por test, <em>rollback</em>, atención a usuarios, etc.).</p>

  <h3>Riesgos legales y de seguridad</h3>
  <p>Un <em>crash</em> mal gestionado puede derivar en exposición de datos o incumplimiento de normativas.</p>

  <p><strong>Equifax (2017):</strong> una excepción no gestionada correctamente en un componente web permitió la explotación de una vulnerabilidad.<br>
  → Filtración de datos de 143 millones de usuarios.<br>
  → Multas superiores a <strong>700 millones de dólares</strong> por incumplir la legislación de protección de datos.</p>

  <h3>Impacto en métricas de calidad y posicionamiento</h3>
  <p>En las tiendas de apps, las métricas de <em>stability</em> son criterio de ranking:</p>

  <ul>
    <li>Google Play y App Store penalizan apps con tasas de cierre superiores al <strong>1 %</strong>.</li>
    <li>Una app con <em>crash rate</em> alto baja posiciones en las búsquedas, lo que reduce descargas y visibilidad.</li>
    <li>En algunos casos, Google llega a <strong>retirar temporalmente la app</strong> por considerarla inestable.</li>
  </ul>

  <p><strong>Ejemplo:</strong><br>
  Una startup con 500.000 usuarios activos mensuales puede perder fácilmente entre <strong>50.000 y 100.000 € al mes</strong>
  si su app cae del top 10 al top 100 en descargas.</p>
</details>

---
[Ejemplo](excepciones.md "Ejemplo de excepciones")

---

## Dimensiones de la calidad en aplicaciones móviles

La calidad del software puede analizarse desde distintas perspectivas. En las apps nativas, algunas dimensiones son especialmente relevantes:

### Rendimiento y eficiencia energética
El rendimiento afecta directamente la satisfacción del usuario. Optimizar el uso de CPU, memoria y red, así como minimizar el consumo de batería, es clave para que la app sea rápida y eficiente.

### Seguridad y privacidad
Los datos personales deben protegerse mediante buenas prácticas: cifrado, autenticación segura y manejo correcto de permisos. Un fallo de seguridad puede comprometer tanto al usuario como a la organización.

### Usabilidad y accesibilidad
La interfaz debe ser intuitiva, atractiva y accesible para todos. Cumplir las guías de diseño de Android (Material Design) o iOS (HIG) mejora la experiencia del usuario y la coherencia visual.

### Compatibilidad y portabilidad
Las apps deben funcionar correctamente en una gran variedad de dispositivos, tamaños de pantalla y versiones del sistema operativo. Las pruebas en nubes de dispositivos ayudan a garantizarlo.

### Fiabilidad y estabilidad
Una app debe comportarse de manera predecible, gestionando errores, reconexiones o cierres inesperados sin afectar la experiencia del usuario.

### Mantenibilidad y escalabilidad
Un código modular, limpio y bien documentado permite que el proyecto evolucione sin dificultades. Las arquitecturas **MVVM** o **Clean Architecture** favorecen la mantenibilidad a largo plazo.

---

## Integración de la calidad en el ciclo DevOps

En DevOps, la calidad no se “verifica al final”, sino que se **integra de forma continua** en cada etapa del ciclo de vida. Cada cambio en el código activa una serie de pasos automáticos que validan su calidad antes del despliegue.

Un pipeline típico incluye:  
- **Análisis de código estático** para detectar errores o vulnerabilidades.  
- **Ejecución de pruebas automáticas** en cada commit.  
- **Evaluación de métricas de calidad** antes de desplegar.  
- **Supervisión post-despliegue** para detectar incidencias en tiempo real.

El objetivo es detectar errores **lo antes posible**, reducir el riesgo y mantener un flujo de entrega constante y confiable.

---

## Herramientas para la gestión y evaluación de la calidad

Existen múltiples herramientas que ayudan a garantizar la calidad del código, las pruebas y la monitorización de las aplicaciones móviles. A continuación, se presentan agrupadas por su función principal.

### Análisis estático y calidad del código

Permiten inspeccionar automáticamente el código fuente para encontrar errores, malas prácticas o problemas de estilo.

- **SonarQube / SonarCloud** → Analiza *code smells*, duplicaciones, deuda técnica y vulnerabilidades. [SonarQube](sonarqube.md "SonarQube") 
- **Detekt (Kotlin)** → Especializado en código Kotlin, detecta violaciones de estilo y complejidad. [Detekt](detekt.md "Detekt")    
- **Android Lint** → Incluido en Android Studio, revisa el rendimiento, accesibilidad y consistencia. [Lint](lint.md "Lint")   
- **PMD / Checkstyle** → Garantizan convenciones de codificación en proyectos colaborativos.

*Consejo:* Configura estas herramientas en tu pipeline CI/CD para bloquear la integración de código defectuoso.

<details>
<summary><strong>Análisis Estático</strong></summary>
<p><strong>Detekt</strong> analiza el estilo y las buenas prácticas del código Kotlin, revisa, por tanto el estilo del código Kotlin.</p>
<p><strong>Android Lint</strong> revisa el proyecto completo de Android: desde el código hasta los layouts, los recursos y el manifiesto. Es la primera línea de defensa para garantizar la calidad y la coherencia en las aplicaciones móviles nativas.</p>
<p><strong>SonarQube</strong> integra todo ese análisis en un panel unificado que mide la calidad global, la deuda técnica y las métricas de cobertura del proyecto.</p> 
<p>En los entornos DevOps, <strong>SonarQube</strong> actúa como el <strong> control central de calidad continua</strong>.</p>
</details>

---

### Gestión de versiones y revisión colaborativa

El control de versiones y las revisiones entre pares fomentan la calidad colectiva del código.

- **GitHub / GitLab / Bitbucket** → Facilitan revisiones (*pull requests*) con comentarios y aprobaciones.  
- **GitHub Actions / GitLab CI** → Permiten automatizar pruebas y análisis en cada commit.  
- **CodeClimate** → Evalúa la mantenibilidad del código y muestra métricas en tiempo real.

*Consejo:* Las revisiones entre compañeros son uno de los mecanismos más efectivos para detectar errores y mejorar el diseño.

---

### Pruebas automatizadas y cobertura

Las pruebas automáticas garantizan que el software sigue funcionando correctamente tras cada cambio.

- **JUnit / Espresso / Mockito** → Para pruebas unitarias, de integración y de interfaz.  
- **Robolectric** → Permite ejecutar pruebas sin emuladores.  
- **Jacoco / Codecov** → Calculan la cobertura de código y generan informes.  
- **Firebase Test Lab** → Ejecuta pruebas en dispositivos reales en la nube.

*Consejo:* automatiza la ejecución de pruebas en cada push para detectar regresiones de manera temprana.

---

### Monitoreo y observabilidad

Las herramientas de observabilidad permiten conocer cómo se comporta la app una vez desplegada.

- **Firebase Crashlytics** → Informa sobre fallos y excepciones en tiempo real.  
- **Firebase Performance Monitoring** → Mide latencias, tiempos de carga y consumo de recursos.  
- **Datadog / New Relic / Sentry** → Integran métricas, logs y alertas en un mismo panel.

*Consejo:* Conocer el comportamiento en producción es clave para priorizar correcciones y optimizaciones.

---

### Métricas y auditoría continua

Las métricas ayudan a medir el progreso y la salud del proyecto.

- **Complejidad ciclomática**: mide la dificultad de entender o mantener el código.  
- **Duplicación de código**: indica redundancia y riesgo de errores.  
- **Deuda técnica**: tiempo estimado para resolver deficiencias estructurales.  
- **Acoplamiento y cohesión**: evalúan la modularidad del software.

Estas métricas se visualizan en paneles como **SonarQube**, **CodeClimate** o **GitHub Insights**, permitiendo hacer seguimiento del estado del proyecto.

---

## Estrategias para asegurar la calidad durante el desarrollo

Garantizar la calidad es responsabilidad de todo el equipo. Algunas prácticas efectivas son:

- Realizar **code reviews** para detectar errores y mejorar el estilo.  
- Usar **linters** antes de cada commit.  
- Aplicar **TDD (Test Driven Development)** para guiar el desarrollo mediante pruebas.  
- Automatizar *builds* con **Gradle**.  
- Probar en entornos de **staging** antes del despliegue.  
- Realizar **despliegues canarios**, liberando la app a un grupo reducido antes del lanzamiento general.

*Consejo:* La clave es detectar los errores en las primeras fases del desarrollo, no en producción.

---

## Evaluación continua en proyectos móviles nativos

La evaluación continua permite asegurar la calidad de cada versión antes de su entrega. Para ello, los pipelines integran distintas etapas automáticas:

1. **Análisis estático del código** → (SonarCloud, Detekt).  
2. **Ejecución de pruebas** → (JUnit, Jacoco).  
3. **Validación en dispositivos reales** → (Firebase Test Lab).  
4. **Publicación de reportes** → visibles en GitHub o Slack.  

Cada etapa del pipeline genera retroalimentación inmediata, permitiendo al equipo actuar con rapidez ante cualquier fallo.

---

## Buenas prácticas y cultura de calidad

La calidad del software no depende solo de herramientas, sino de la **cultura de trabajo del equipo**. Algunas recomendaciones clave:

- Promover la **responsabilidad compartida**: todos cuidan la calidad.  
- Incorporar los resultados de análisis y tests en las reuniones de sprint.  
- Planificar **refactorización periódica**.  
- Centrarse en indicadores relevantes (fallos por versión, deuda técnica, cobertura real).  
- Fomentar una actitud de mejora continua: la calidad no se alcanza, se mantiene.

---

## Conclusión

Una aplicación móvil de calidad combina **buen diseño, pruebas constantes, análisis de código y cultura de equipo**.  
Integrar la calidad dentro del ciclo **DevOps** permite entregar software confiable y evolutivo, reduciendo costes y mejorando la satisfacción del usuario.

*Nota:* La calidad no es una fase final del desarrollo, sino una práctica continua que acompaña cada línea de código.

---

[Ejemplo práctico 1](calidad_ejemplo1.md "Ejemplo práctico")
