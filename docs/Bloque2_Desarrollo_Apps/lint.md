# Android Lint

## ¿Qué es Lint?

**Android Lint** es una herramienta de **análisis estático** integrada en **Android Studio** y en el **Android Gradle Plugin**.  
Su función es **detectar automáticamente problemas en el código fuente y en los recursos XML de una aplicación Android**, sin necesidad de ejecutar la app.

Lint analiza tanto el código Java y Kotlin como los archivos de manifiesto, layouts, strings y recursos, ayudando a los desarrolladores a mantener un código **seguro, eficiente, accesible y coherente** con las buenas prácticas de Android.

---

## ¿Para qué se utiliza?

Lint identifica posibles errores, malas prácticas o inconsistencias antes de compilar el proyecto.  
Sus análisis cubren áreas clave del desarrollo Android, como:

- **Rendimiento:** uso ineficiente de memoria o CPU, layouts anidados, operaciones en el hilo principal.  
- **Seguridad:** uso inseguro de criptografía, claves API visibles o permisos mal definidos.  
- **Accesibilidad:** textos sin contraste, elementos sin etiquetas (`contentDescription`).  
- **Internacionalización:** cadenas de texto no traducidas o mal definidas.  
- **Usabilidad:** tamaños de fuente no escalables, componentes mal alineados.  
- **Compatibilidad:** uso de APIs no disponibles en versiones anteriores de Android.

En definitiva, Lint es el **sistema de control de calidad integrado en Android Studio**, que advierte de los errores antes de que lleguen a producción.

---

## Ejemplo de problemas que puede detectar

| Categoría | Ejemplo | Riesgo |
|------------|----------|--------|
| **Rendimiento** | Layouts anidados innecesariamente (`Nested layouts`) | Aumenta el tiempo de renderizado y consumo de CPU. |
| **Seguridad** | Claves API visibles en el código fuente | Exposición de credenciales. |
| **Accesibilidad** | Falta de `contentDescription` en imágenes | Dificulta el uso por personas con lectores de pantalla. |
| **Internacionalización** | Texto fijo en el código (`"Hello"`) | No se traduce a otros idiomas. |
| **Compatibilidad** | Uso de métodos no disponibles en versiones antiguas | Fallos en dispositivos con API más baja. |

---

## Cómo se integra en un proyecto Android

Lint viene **incluido por defecto en Android Studio**, pero también puede ejecutarse manualmente desde Gradle o la línea de comandos.

### Ejecución desde Android Studio
Lint se ejecuta automáticamente cada vez que compilas el proyecto o puedes forzar un análisis manual:
- Menú: **Analyze → Inspect Code**
- O desde **Build → Analyze APK**

### Ejecución desde consola o CI/CD

```bash
./gradlew lint
```

Esto genera informes en varios formatos (HTML, XML, SARIF) ubicados normalmente en:

```bash
app/build/reports/lint-results.html
```

## Configuración

Puedes configurar las reglas de análisis mediante el archivo lint.xml en la raíz del módulo:

```xml
<lint>
    <issue id="ObsoleteLayoutParam" severity="ignore" />
    <issue id="HardcodedText" severity="error" />
    <issue id="ContentDescription" severity="warning" />
</lint>
```

Cada issue tiene un identificador y un nivel de severidad (ignore, warning, error), lo que permite adaptar el análisis a las necesidades del proyecto.

## En resumen  

> **Android Lint** es el analizador de calidad integrado en el ecosistema Android.  
> Permite detectar errores de rendimiento, seguridad, accesibilidad y compatibilidad antes de ejecutar la app.  
> Su uso reduce errores en producción, mejora la experiencia del usuario y facilita el mantenimiento del código.  
