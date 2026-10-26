# Comparación de interfaces Android: XML y Jetpack Compose

En las cuatro aplicaciones, la lógica de los patrones de diseño se mantiene. Lo que cambia es cómo se define la interfaz y cómo se refleja su estado: con XML se crean vistas que se consultan o modifican desde Kotlin; con Jetpack Compose se describe la interfaz mediante funciones Kotlin que dependen de un estado observable.

| Aplicación | Funcionalidad comparada | Diferencia principal |
|---|---|---|
| `01_GestorTareas` | Actualización de la lista de tareas | Actualización explícita de vistas frente a recomposición |
| `02_Pedidos` | Selección de leche como complemento | Consulta del estado de un `CheckBox` frente a estado gestionado en Kotlin |
| `03_Notas` | Selección del criterio de búsqueda | `Spinner` frente a selección controlada por estado |
| `04_Registro` | Presentación del resultado del registro | Modificación de un `TextView` frente a `Text` dependiente del estado |

## 1. Gestor de tareas: actualizar la lista

### Interfaz con XML y Kotlin

La actividad recibe las notificaciones de `AlmacenTareas` y actualiza explícitamente un elemento visual:

```xml
<!-- activity_main.xml (fragmento ilustrativo) -->
<TextView
    android:id="@+id/listaTareas"
    android:layout_width="match_parent"
    android:layout_height="wrap_content" />
```

```kotlin
// MainActivity.kt (fragmento ilustrativo)
val listaTareas = findViewById<TextView>(R.id.listaTareas)

val observador = ObservadorTareas { tareas ->
    listaTareas.text = tareas.joinToString("\n") { it.titulo }
}

almacen.suscribir(observador)
```

### Interfaz con Jetpack Compose

El observador modifica un estado observable que utiliza la interfaz:

```kotlin
@Composable
fun PantallaTareas(almacen: AlmacenTareas) {
    var tareas by remember { mutableStateOf(emptyList<Tarea>()) }

    DisposableEffect(almacen) {
        val observador = ObservadorTareas { nuevasTareas ->
            tareas = nuevasTareas
        }
        almacen.suscribir(observador)
        onDispose { almacen.cancelar(observador) }
    }

    Column {
        Text("Total: ${tareas.size}")
        tareas.forEach { tarea -> Text(tarea.titulo) }
    }
}
```

**Diferencia:** en XML se asigna un nuevo texto a la vista; en Compose cambia el estado `tareas` y se recompone la interfaz que lo utiliza. El patrón **Observer** permanece en ambos casos.

## 2. Pedidos: seleccionar leche

### Interfaz con XML y Kotlin

```xml
<!-- activity_main.xml (fragmento) -->
<CheckBox
    android:id="@+id/leche"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="Añadir leche" />
```

```kotlin
// MainActivity.kt (fragmento)
val leche = findViewById<CheckBox>(R.id.leche)

if (leche.isChecked) {
    bebida = ConLeche(bebida)
}
```

### Interfaz con Jetpack Compose

```kotlin
@Composable
fun OpcionLeche() {
    var leche by remember { mutableStateOf(false) }

    Row(verticalAlignment = Alignment.CenterVertically) {
        Checkbox(
            checked = leche,
            onCheckedChange = { leche = it }
        )
        Text("Añadir leche")
    }

    // Al preparar el pedido, se usa el valor de `leche`:
    // if (leche) bebida = ConLeche(bebida)
}
```

**Diferencia:** en XML la actividad consulta `leche.isChecked`; en Compose la casilla refleja la variable `leche` y la modifica mediante `onCheckedChange`. El patrón **Decorator** (`ConLeche`) no cambia.

## 3. Notas: elegir el criterio de búsqueda

### Interfaz con XML y Kotlin

```xml
<!-- activity_main.xml (fragmento) -->
<Spinner
    android:id="@+id/criterio"
    android:layout_width="match_parent"
    android:layout_height="wrap_content" />
```

```kotlin
// MainActivity.kt (fragmento)
val spinner = findViewById<Spinner>(R.id.criterio)

val estrategia: EstrategiaBusqueda =
    if (spinner.selectedItemPosition == 0) BuscarTitulo()
    else BuscarContenido()

val resultados = Buscador(estrategia).buscar(notas, texto)
```

### Interfaz con Jetpack Compose

```kotlin
@Composable
fun SelectorBusqueda(
    notas: List<Nota>,
    texto: String
) {
    var porTitulo by remember { mutableStateOf(true) }

    Row(verticalAlignment = Alignment.CenterVertically) {
        RadioButton(
            selected = porTitulo,
            onClick = { porTitulo = true }
        )
        Text("Título")
        RadioButton(
            selected = !porTitulo,
            onClick = { porTitulo = false }
        )
        Text("Contenido")
    }

    Button(onClick = {
        val estrategia: EstrategiaBusqueda =
            if (porTitulo) BuscarTitulo() else BuscarContenido()
        val resultados = Buscador(estrategia).buscar(notas, texto)
        // Los resultados se guardarían en un estado observable para mostrarlos.
    }) {
        Text("Buscar")
    }
}
```

**Diferencia:** el `Spinner` tradicional se consulta para averiguar la opción; en Compose, los controles de selección están vinculados a `porTitulo`. En ambos casos, **Strategy** elige el algoritmo de búsqueda. Aquí se emplean botones de opción en Compose para simplificar el ejemplo, en lugar de un menú desplegable.

## 4. Registro: mostrar el resultado

### Interfaz con XML y Kotlin

```xml
<!-- activity_main.xml (fragmento) -->
<TextView
    android:id="@+id/resultado"
    android:layout_width="match_parent"
    android:layout_height="wrap_content" />
```

```kotlin
// MainActivity.kt (fragmento)
val resultado = findViewById<TextView>(R.id.resultado)

botonRegistrar.setOnClickListener {
    resultado.text = servicio.registrar(email, clave)
}
```

### Interfaz con Jetpack Compose

```kotlin
@Composable
fun PantallaRegistro(
    servicio: ServicioUsuarios,
    email: String,
    clave: String
) {
    var mensaje by remember { mutableStateOf("") }

    Button(onClick = {
        mensaje = servicio.registrar(email, clave)
    }) {
        Text("Registrar")
    }

    Text(mensaje)
}
```

**Diferencia:** en XML se cambia el contenido de `TextView` explícitamente; en Compose `Text` depende de `mensaje` y refleja sus cambios. El patrón **Facade** (`ServicioUsuarios`) sigue siendo el mismo.

---

**Idea común:** en ambos enfoques la lógica de las aplicaciones puede mantenerse independiente de la interfaz. En XML la actividad actualiza directamente las vistas; en Compose la interfaz se declara a partir del estado observable y se recompone cuando este cambia.

*Los fragmentos son ejemplos comparativos simplificados y no sustituyen el código completo de los proyectos.*
