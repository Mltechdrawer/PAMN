# Diseño de interfaces a nivel técnico: frameworks y librerías en Android e iOS

Además de los principios conceptuales de diseño, es fundamental comprender **cómo se materializan técnicamente** las interfaces en cada plataforma nativa.  
Tanto Android como iOS han evolucionado desde modelos imperativos hacia **paradigmas declarativos** más modernos, centrados en la sincronización automática entre el estado y la interfaz.

---

## Android: Jetpack Compose

**Jetpack Compose** es el framework moderno de Android para la creación de interfaces declarativas.  
Reemplaza al sistema tradicional basado en XML, permitiendo definir la UI con código Kotlin y funciones reactivas.

### Principios técnicos
- Las interfaces se definen con funciones anotadas con `@Composable`.  
- La UI se actualiza automáticamente cuando cambian los estados observables.  
- Utiliza un modelo de *recomposición*: solo se vuelven a renderizar los elementos afectados por el cambio.

```kotlin
@Composable
fun CounterScreen() {
    var count by remember { mutableStateOf(0) }
    Column(horizontalAlignment = Alignment.CenterHorizontally) {
        Text("Contador: $count")
        Button(onClick = { count++ }) {
            Text("Incrementar")
        }
    }
}
```

### Arquitectura y patrones
- Patrones comunes: **MVVM** (Model–View–ViewModel) y **Unidirectional Data Flow**.  
- Integración nativa con `ViewModel` y `StateFlow` / `LiveData` para gestión del estado.  
- Composición modular de UI con componentes reutilizables (*composables*).

### Librerías y herramientas clave
- **Material Design 3**: componentes visuales oficiales (`Button`, `Card`, `NavigationBar`, etc.).  
- **Accompanist**: extensiones para Compose (imágenes, animaciones, paginación, etc.).  
- **Showkase**: catálogo visual de componentes UI para desarrollo colaborativo.  
- **Voyager / Navigation Compose**: navegación declarativa simplificada.  
- **Katalog**: explorador visual de componentes, útil en proyectos grandes.  
- **Interoperabilidad:** permite integrar `Compose` con vistas XML clásicas mediante `ComposeView` o `AndroidView`.

Recurso oficial: [Jetpack Compose – Android Developers](https://developer.android.com/compose)

---

## iOS: UIKit y SwiftUI

En iOS existen dos principales enfoques para construir interfaces:

### 1. UIKit (modelo tradicional)
- Framework imperativo basado en clases `UIView` y `UIViewController`.  
- Las interfaces se definen en *storyboards* o mediante código.  
- Se actualiza la UI manualmente en respuesta a eventos.  
- Patrones comunes: **MVC**, **MVP**, **MVVM**, **VIPER**, **Coordinator**.  

### 2. SwiftUI (modelo declarativo moderno)
Introducido por Apple en 2019, **SwiftUI** permite definir la interfaz como una función del estado actual de la aplicación, simplificando el desarrollo y mejorando la coherencia visual.

```swift
struct ContentView: View {
    @State private var counter = 0

    var body: some View {
        VStack {
            Text("Contador: \(counter)")
            Button("Incrementar") {
                counter += 1
            }
        }
    }
}
```

### Arquitectura y patrones en SwiftUI
- Patrón recomendado: **MVVM** (Model–View–ViewModel).  
- `@State`, `@Published` y `@ObservableObject` gestionan el flujo de datos reactivo.  
- Integración con `Combine` o `async/await` para sincronizar datos asíncronos.  
- También se utiliza la **Clean Architecture** adaptada a SwiftUI para proyectos grandes.  

### Librerías y utilidades relevantes
- **SwiftUIX**: amplía los componentes estándar de SwiftUI.  
- **The Composable Architecture (TCA)**: arquitectura modular basada en estados, acciones y efectos.  
- **Lottie-iOS**: animaciones vectoriales en formato JSON.  
- **Charts / Swift Charts**: creación de gráficos nativos.  
- **Interoperabilidad:** se puede integrar UIKit y SwiftUI en la misma app (`UIViewRepresentable` y `UIHostingController`).

📘 Recurso oficial: [Apple Developer – SwiftUI](https://developer.apple.com/xcode/swiftui/)

---

## Comparativa técnica

| Aspecto | Android (Jetpack Compose) | iOS (SwiftUI) |
|----------|---------------------------|----------------|
| **Paradigma UI** | Declarativo | Declarativo |
| **Lenguaje** | Kotlin | Swift |
| **Gestión del estado** | `State`, `MutableState`, `StateFlow`, `ViewModel` | `@State`, `@Published`, `ObservableObject` |
| **Componentes nativos** | Composables de Material Design | Vistas SwiftUI nativas |
| **Estilos y temas** | Material Theme, color dinámico, modo oscuro automático | Light/Dark mode, Dynamic Type, system accent colors |
| **Animaciones** | `animate*AsState`, `Transition`, `AnimatedVisibility` | `.animation()`, `withAnimation`, transiciones implícitas |
| **Arquitectura recomendada** | MVVM + Unidirectional Data Flow | MVVM + Combine / Async Await |
| **Interoperabilidad** | Compatibilidad con vistas XML | Compatibilidad con UIKit |
| **Librerías destacadas** | Accompanist, Showkase, Voyager, Katalog | SwiftUIX, TCA, Lottie, Charts |

---

## Conclusión técnica
- Tanto **Jetpack Compose** como **SwiftUI** consolidan la tendencia hacia el diseño declarativo, centrado en el estado y en la reactividad.  
- Ambos frameworks integran los principios de **Material Design** y **Human Interface Guidelines** dentro de sus librerías nativas.  
- El conocimiento de sus **componentes, patrones y librerías** es esencial para diseñar interfaces modernas, eficientes y coherentes con cada plataforma.

---
