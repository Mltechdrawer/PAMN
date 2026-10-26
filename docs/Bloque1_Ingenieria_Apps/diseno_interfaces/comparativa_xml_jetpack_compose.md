# Interfaces Android: XML frente a Jetpack Compose

Los siguientes fragmentos son **ejemplos comparativos simplificados** de las cuatro aplicaciones. Se muestra el recorrido completo de una interacción: **acción → cambio de datos o estado → actualización visible**. Las líneas señaladas con `// CAMBIO` son aquellas donde ocurre la modificación relevante. Los fragmentos aíslan la funcionalidad estudiada y no son transcripciones completas de los proyectos.

## 1. `01_GestorTareas`: añadir una tarea y actualizar la lista

### Con XML + Kotlin

La vista se define en `activity_main.xml`:

```xml
<TextView
    android:id="@+id/listaTareas"
    android:layout_width="match_parent"
    android:layout_height="wrap_content" />

<Button
    android:id="@+id/anadir"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="Añadir tarea" />
```

La actividad conecta las acciones y el observador:

```kotlin
// MainActivity.kt (dentro de onCreate, tras setContentView)
val listaTareas = findViewById<TextView>(R.id.listaTareas)
val boton = findViewById<Button>(R.id.anadir)

val observador = ObservadorTareas { tareas ->
    // CAMBIO VISIBLE: aquí se asigna el NUEVO TEXTO al TextView.
    listaTareas.text = tareas.joinToString("\n") { it.titulo }
}
almacen.suscribir(observador)

boton.setOnClickListener {
    almacen.agregar(Tarea(/* identificador, título y estado */))
    // agregar() modifica el almacén y llama a notificar().
    // La notificación ejecuta el observador de arriba.
}
```

### Con Jetpack Compose

```kotlin
@Composable
fun PantallaTareas(almacen: AlmacenTareas) {
    var tareas by remember { mutableStateOf(emptyList<Tarea>()) }

    DisposableEffect(almacen) {
        val observador = ObservadorTareas { nuevasTareas ->
            // CAMBIO DE ESTADO: aquí se guarda la nueva lista.
            tareas = nuevasTareas
        }
        almacen.suscribir(observador)
        onDispose { almacen.cancelar(observador) }
    }

    Column {
        Button(onClick = {
            almacen.agregar(Tarea(/* identificador, título y estado */))
        }) {
            Text("Añadir tarea")
        }

        // RESULTADO VISIBLE: estos Text leen «tareas».
        Text("Total: ${tareas.size}")
        tareas.forEach { tarea -> Text(tarea.titulo) }
    }
}
```

**Comparación:** XML cambia explícitamente una vista; Compose cambia el **estado** del que depende la interfaz. El patrón **Observer** sigue notificando en ambas versiones.

## 2. `02_Pedidos`: marcar «Leche extra» y preparar el pedido

### Con XML + Kotlin

```xml
<CheckBox
    android:id="@+id/leche"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="Leche extra" />

<Button
    android:id="@+id/preparar"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="Preparar pedido" />

<TextView
    android:id="@+id/detalle"
    android:layout_width="match_parent"
    android:layout_height="wrap_content" />
```

```kotlin
val leche = findViewById<CheckBox>(R.id.leche)
val detalle = findViewById<TextView>(R.id.detalle)
val preparar = findViewById<Button>(R.id.preparar)

preparar.setOnClickListener {
    var bebida: Bebida = BebidaFactory.crear("Café")

    // CONSULTA: se lee el estado actual de la vista XML.
    if (leche.isChecked) bebida = ConLeche(bebida)

    // CAMBIO VISIBLE: se asigna explícitamente el texto del resumen.
    detalle.text = "${bebida.descripcion()} — ${bebida.precioCentimos()} céntimos"
}
```

### Con Jetpack Compose

```kotlin
@Composable
fun PedidoConLeche() {
    var leche by remember { mutableStateOf(false) }
    var resumen by remember { mutableStateOf("") }

    Column {
        Row(verticalAlignment = Alignment.CenterVertically) {
            Checkbox(
                checked = leche, // ESTADO LEÍDO: determina si aparece marcada.
                onCheckedChange = { seleccionado ->
                    leche = seleccionado // CAMBIO DE ESTADO al pulsar la casilla.
                }
            )
            Text("Leche extra")
        }

        Button(onClick = {
            var bebida: Bebida = BebidaFactory.crear("Café")
            if (leche) bebida = ConLeche(bebida)
            resumen = "${bebida.descripcion()} — ${bebida.precioCentimos()} céntimos"
            // CAMBIO DE ESTADO del resultado.
        }) {
            Text("Preparar pedido")
        }

        Text(resumen) // RESULTADO VISIBLE: lee el nuevo «resumen».
    }
}
```

## 3. `03_Notas`: seleccionar «Título» o «Contenido» y buscar

### Con XML + Kotlin

```xml
<Spinner
    android:id="@+id/criterio"
    android:layout_width="match_parent"
    android:layout_height="wrap_content" />

<Button
    android:id="@+id/buscar"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="Buscar" />

<TextView
    android:id="@+id/resultados"
    android:layout_width="match_parent"
    android:layout_height="wrap_content" />
```

```kotlin
val criterio = findViewById<Spinner>(R.id.criterio)
val buscar = findViewById<Button>(R.id.buscar)
val resultadosVista = findViewById<TextView>(R.id.resultados)

// Se supone que el Spinner ya contiene: «Título», «Contenido».
buscar.setOnClickListener {
    // CONSULTA: posición seleccionada en el Spinner.
    val estrategia: EstrategiaBusqueda =
        if (criterio.selectedItemPosition == 0) BuscarTitulo()
        else BuscarContenido()

    val encontrados = Buscador(estrategia).buscar(notas, texto)

    // CAMBIO VISIBLE: reemplaza el texto del TextView.
    resultadosVista.text = encontrados.joinToString("\n") { it.titulo }
}
```

### Con Jetpack Compose

En este ejemplo Compose se utilizan **RadioButton** para hacer visible el cambio de selección; representan las mismas dos opciones que el `Spinner`.

```kotlin
@Composable
fun BuscarNotas(notas: List<Nota>, texto: String) {
    var porTitulo by remember { mutableStateOf(true) }
    var encontrados by remember { mutableStateOf(emptyList<Nota>()) }

    Column {
        Row {
            RadioButton(
                selected = porTitulo, // ESTADO LEÍDO.
                onClick = { porTitulo = true } // CAMBIO DE SELECCIÓN.
            )
            Text("Título")
            RadioButton(
                selected = !porTitulo,
                onClick = { porTitulo = false } // CAMBIO DE SELECCIÓN.
            )
            Text("Contenido")
        }

        Button(onClick = {
            val estrategia: EstrategiaBusqueda =
                if (porTitulo) BuscarTitulo() else BuscarContenido()
            encontrados = Buscador(estrategia).buscar(notas, texto)
            // CAMBIO DE ESTADO: guarda la nueva lista de resultados.
        }) {
            Text("Buscar")
        }

        // RESULTADO VISIBLE: se obtiene de «encontrados».
        encontrados.forEach { nota -> Text(nota.titulo) }
    }
}
```

## 4. `04_Registro`: pulsar «Registrar» y mostrar el mensaje

### Con XML + Kotlin

```xml
<Button
    android:id="@+id/registrar"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="Registrar" />

<TextView
    android:id="@+id/resultado"
    android:layout_width="match_parent"
    android:layout_height="wrap_content" />
```

```kotlin
val registrar = findViewById<Button>(R.id.registrar)
val resultado = findViewById<TextView>(R.id.resultado)

registrar.setOnClickListener {
    val mensaje = servicio.registrar(email, clave)
    // CAMBIO VISIBLE: asigna el mensaje al TextView existente.
    resultado.text = mensaje
}
```

### Con Jetpack Compose

```kotlin
@Composable
fun PantallaRegistro(servicio: ServicioUsuarios, email: String, clave: String) {
    var mensaje by remember { mutableStateOf("") }

    Column {
        Button(onClick = {
            // CAMBIO DE ESTADO: almacena el resultado del registro.
            mensaje = servicio.registrar(email, clave)
        }) {
            Text("Registrar")
        }

        Text(mensaje) // RESULTADO VISIBLE: depende de «mensaje».
    }
}
```

---

## Síntesis

| Aplicación | XML: línea que actualiza la vista | Compose: cambio de estado → lectura en interfaz |
|---|---|---|
| GestorTareas | `listaTareas.text = ...` | `tareas = nuevasTareas` → `Text(tarea.titulo)` |
| Pedidos | `detalle.text = ...` | `resumen = ...` → `Text(resumen)` |
| Notas | `resultadosVista.text = ...` | `encontrados = ...` → `Text(nota.titulo)` |
| Registro | `resultado.text = mensaje` | `mensaje = ...` → `Text(mensaje)` |

**En XML** la actividad modifica explícitamente una vista. **En Compose** se modifica un estado observable y las funciones `@Composable` describen lo que debe mostrarse a partir de ese estado.
