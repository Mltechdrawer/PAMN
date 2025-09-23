# Xcode

## Introducción
**Xcode** es el IDE oficial de Apple para el desarrollo en sus plataformas: iOS, macOS, watchOS y tvOS. Es la herramienta imprescindible para programar en **Swift** y **Objective-C**, con la que se pueden crear, depurar y desplegar aplicaciones directamente en dispositivos Apple o en simuladores.

## Características principales
- Editor de código con autocompletado y refactorización.  
- Interfaz visual para diseñar pantallas con Storyboard o SwiftUI.  
- Simuladores de todos los dispositivos Apple.  
- Integración con Git y con la App Store para distribución.  
- Soporte de pruebas unitarias y de interfaz.  
- Herramientas de profiling (Instruments) para rendimiento, memoria y energía.  

## Estructura de un proyecto en Xcode
Un proyecto en Xcode genera varias carpetas y archivos clave:

- **NombreProyecto.xcodeproj**  
  Archivo principal del proyecto. Contiene la configuración y debe abrirse con Xcode.  

- **AppDelegate.swift**  
  Archivo de entrada que gestiona el ciclo de vida de la aplicación.  

- **SceneDelegate.swift** (en proyectos modernos con SwiftUI o múltiples escenas)  
  Controla las distintas escenas o ventanas de la app.  

- **ViewController.swift**  
  Controladores de vista que gestionan la lógica de cada pantalla.  

- **Main.storyboard** (o **ContentView.swift** en SwiftUI)  
  Define la interfaz gráfica, ya sea de forma visual (Storyboard) o declarativa (SwiftUI).  

- **Assets.xcassets**  
  Carpeta que contiene los recursos gráficos: iconos, imágenes, colores, etc.  

- **Info.plist**  
  Archivo de configuración con información clave: nombre de la app, permisos de acceso (cámara, red, ubicación), entre otros.  

- **Tests/**  
  Carpeta con pruebas unitarias.  

- **UITests/**  
  Carpeta con pruebas de interfaz.  

Esta organización permite separar de forma clara la lógica, la interfaz, los recursos y la configuración de la app.
