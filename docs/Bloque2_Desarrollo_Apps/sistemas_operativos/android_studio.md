# Android Studio

## Introducción
**Android Studio** es el IDE oficial para el desarrollo de aplicaciones Android. Basado en IntelliJ IDEA, fue lanzado por Google en 2013 y desde entonces se ha convertido en la herramienta estándar para programar en **Kotlin** y **Java**. Ofrece un entorno completo para crear, probar y distribuir aplicaciones móviles.

## Características principales
- Editor de código inteligente con autocompletado y refactorización.  
- Simuladores de dispositivos Android para pruebas sin hardware físico.  
- Integración con el SDK de Android y librerías de Jetpack.  
- Soporte nativo para **Kotlin** y **Jetpack Compose**.  
- Herramientas de profiling para medir rendimiento, memoria y consumo energético.  
- Integración con sistemas de control de versiones como Git.  

## Estructura de un proyecto en Android Studio
Cuando se crea un nuevo proyecto, Android Studio organiza los archivos en varias carpetas. Las más importantes son:

- **app/**  
  Carpeta principal de la aplicación. Contiene el código fuente, recursos y archivos de configuración específicos.  
  - **src/**: código fuente de la aplicación.  
    - **main/**:  
      - **java/**: código en Kotlin o Java.  
      - **res/**: recursos (layouts XML, imágenes, cadenas de texto, iconos, etc.).  
      - **AndroidManifest.xml**: archivo de configuración con permisos, actividades y metadatos de la app.  
    - **test/**: pruebas unitarias.  
    - **androidTest/**: pruebas de instrumentación (en dispositivo o emulador).  

- **gradle/**  
  Archivos de configuración de *Gradle*, el sistema de construcción que compila y empaqueta la app.  

- **build/**  
  Carpeta generada automáticamente que contiene los archivos compilados.  

- **.idea/**  
  Configuración del proyecto en IntelliJ/Android Studio.  

Esta estructura permite separar claramente el código, los recursos y las configuraciones del proyecto.
