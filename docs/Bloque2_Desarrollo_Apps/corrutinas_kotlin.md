# Corrutinas y Dispatchers en Kotlin

## Introducción

En el desarrollo de aplicaciones Android, la concurrencia y el paralelismo son esenciales para mantener una experiencia de usuario fluida, evitando bloqueos en la interfaz mientras se ejecutan tareas complejas. Kotlin introduce las corrutinas como una solución ligera y estructurada para manejar operaciones asíncronas, permitiendo ejecutar múltiples tareas sin saturar los recursos del sistema. Los dispatchers, por su parte, controlan en qué hilos se ejecutan esas corrutinas, optimizando la distribución del trabajo entre tareas de CPU, entrada/salida o actualización de la interfaz.

- Una corrutina es como una “tarea” o función que puede suspenderse.
- Un dispatcher es el “motor” o “carretera” por donde circula esa tarea.

## Corrutinas

Las **corrutinas** en Kotlin son una herramienta poderosa para manejar la **concurrencia** de manera sencilla y eficiente.  
Permiten ejecutar múltiples tareas simultáneamente sin bloquear el hilo principal, lo que es esencial en el desarrollo de **aplicaciones móviles nativas**.  

A diferencia de los hilos tradicionales, las corrutinas son **ligeras** y pueden **suspenderse** sin bloquear el hilo, retomando su ejecución más adelante.  
Gracias a esta capacidad, se pueden ejecutar miles de corrutinas dentro de un solo hilo sin un gran consumo de recursos.

<details>
<summary> Patrón </summary>
<p>Una <strong>corrutina</strong> es un <strong>patrón de diseño de simultaneidad</strong> que puedes usar en Android para simplificar el código que se ejecuta de forma asíncrona
</p>
</details>

---

## Conceptos Fundamentales

### Ligereza y suspensión

Una corrutina es más ligera que un hilo porque puede **suspender su ejecución** sin bloquear el hilo en el que se encuentra.  
Esto permite lanzar muchas corrutinas al mismo tiempo, algo impensable con hilos tradicionales.

```kotlin
import kotlinx.coroutines.*

fun main() = runBlocking {
    repeat(1000) { i ->
        launch {
            delay(1000L)
            println("Corrutina $i completada")
        }
    }
}
```

En este ejemplo, se lanzan mil corrutinas que se suspenden durante un segundo sin bloquear el hilo principal.  
El sistema gestiona las suspensiones de manera eficiente, permitiendo la ejecución concurrente.

---

### Evita fugas de memoria

Las corrutinas están diseñadas para evitar **fugas de memoria**.  
Cuando se cancelan o completan, liberan automáticamente los recursos asociados, evitando mantener referencias innecesarias.

```kotlin
import kotlinx.coroutines.*

fun main() = runBlocking {
    val job = launch {
        try {
            repeat(1000) { i ->
                println("Trabajando $i ...")
                delay(500L)
            }
        } finally {
            println("Liberando recursos...")
        }
    }
    delay(1300L)
    println("Cancelando corrutina...")
    job.cancelAndJoin()
    println("Corrutina cancelada correctamente.")
}
```

---

### Compatibilidad con la cancelación

Una de las grandes ventajas de las corrutinas es su **soporte integrado para la cancelación**.  
Cuando se cancela una corrutina, la cancelación se propaga automáticamente a todas las corrutinas hijas en ejecución.

```kotlin
import kotlinx.coroutines.*

fun main() = runBlocking {
    val padre = launch {
        launch {
            try {
                delay(Long.MAX_VALUE)
            } finally {
                println("Corrutina hija cancelada")
            }
        }
        delay(100)
        println("Cancelando corrutina padre")
        this.cancel()
    }
    padre.join()
    println("Fin del programa")
}
```

---

[Kotlilang](https://play.kotlinlang.org/#eyJ2ZXJzaW9uIjoiMi4wLjIxIiwicGxhdGZvcm0iOiJqYXZhIiwiYXJncyI6IiIsIm5vbmVNYXJrZXJzIjp0cnVlLCJ0aGVtZSI6ImlkZWEiLCJjb2RlIjoiZnVuIG1haW4oKSB7XG4gICAgdmFsIGtvdGxpbiA9IFwi8J+ZglwiXG4gICAgcHJpbnRsbihrb3RsaW4pXG59In0= "Kotlilang")

---

### Integración con Jetpack

Muchas librerías de **Jetpack** incluyen extensiones que funcionan de forma nativa con corrutinas.  
Por ejemplo, `ViewModelScope` o `lifecycleScope` permiten lanzar tareas que se cancelan automáticamente cuando el componente deja de estar activo.

```kotlin
class MiViewModel : ViewModel() {
    fun cargarDatos() {
        viewModelScope.launch {
            val resultado = obtenerDatosDeRed()
            actualizarUI(resultado)
        }
    }
}
```

---

## Dispatchers

Los **Dispatchers** determinan **en qué hilo o hilos** se ejecutará una corrutina.  
Seleccionar el dispatcher adecuado es esencial para un rendimiento óptimo y para evitar bloquear la interfaz de usuario.

---

### Dispatchers.Default

Se usa para tareas que requieren un alto uso de CPU, como cálculos intensivos o procesamiento de datos.  
Utiliza un grupo de hilos optimizado en función del número de núcleos disponibles.

```kotlin
import kotlinx.coroutines.*

fun main() = runBlocking {
    launch(Dispatchers.Default) {
        val resultado = (1..1_000_000).sum()
        println("Suma completada: $resultado en ${Thread.currentThread().name}")
    }.join()
}
```

Este es el Dispatcher por defecto si no se especifica uno explícito al lanzar una corrutina.

---

### Dispatchers.IO

Diseñado para operaciones de **entrada/salida** que pueden bloquear el hilo (lectura de archivos, red, bases de datos).  
Cuenta con un grupo de hilos más grande para manejar múltiples operaciones simultáneas.  
Optimiza las corrutinas para tareas de E/S al ofrecer un grupo de hilos más grande que Dispatchers.Default, ya que muchas de estas operaciones pueden bloquear el hilo.  
Ayuda a evitar el bloqueo de la interfaz de usuario al delegar estas tareas en hilos secundarios.  


```kotlin
import kotlinx.coroutines.*

fun main() = runBlocking {
    withContext(Dispatchers.IO) {
        val texto: String = List(1000) { 'A' }.joinToString("")
        println("Contenido leído: ${texto.take(100)}")
    }
}
```

---

### Dispatchers.Main

Usado en Android para ejecutar tareas en el **hilo principal**, ideal para actualizaciones de la interfaz de usuario.  
Debe emplearse únicamente para operaciones rápidas, evitando bloqueos.  
Se usa para la ejecución en el hilo principal, ideal para tareas relacionadas con la interfaz de usuario, como actualizaciones de la UI en aplicaciones Android.  
Dado que el hilo principal controla la UI, las operaciones que se ejecuten en este Dispatcher deben ser rápidas y no bloquear la ejecución, para evitar que la aplicación se congele o tenga retrasos.  

```kotlin
class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        lifecycleScope.launch(Dispatchers.Main) {
            val datos = obtenerDatosDeRed()
            actualizarUI(datos)
        }
    }
}
```

---

### Dispatchers.Unconfined

No está asociado a ningún hilo específico.  
Comienza en el hilo actual y puede continuar en otro después de la primera suspensión.  
Su uso se recomienda solo en pruebas o situaciones controladas.

```kotlin
fun main() = runBlocking {
    launch(Dispatchers.Unconfined) {
        println("Inicio en hilo: ${Thread.currentThread().name}")
        delay(1000L)
        println("Reanudado en hilo: ${Thread.currentThread().name}")
    }
}
```

---

## Importancia de los Dispatchers

El uso correcto de los dispatchers permite aprovechar al máximo la concurrencia y mantener las aplicaciones **ágiles y fluidas**.  
Asignar cada tarea al dispatcher adecuado evita bloqueos, mejora la experiencia del usuario y garantiza una interfaz reactiva.

---

```kotlin
// 5 Dispatchers

import kotlinx.coroutines.*
import kotlin.random.Random

fun main() = runBlocking {
    // Default Dispatcher: para una simulación de cálculos intensivos en CPU
    val job1 = launch(Dispatchers.Default) {
        val result = heavyComputation()
        println("Default Dispatcher (cálculo intensivo): Resultado = \$result en hilo: ${Thread.currentThread().name}")
    }

    // IO Dispatcher: Simulamos una operación de entrada/salida sin usar archivos debido a restricciones del playground
    val job2 = launch(Dispatchers.IO) {
        simulateIOOperation()
        println("IO Dispatcher (simulación de operación I/O): Se ha completado la operación en hilo: ${Thread.currentThread().name}")
    }

    // Unconfined Dispatcher: para una tarea que no tiene un contexto fijo y comienza en el hilo principal
    val job3 = launch(Dispatchers.Unconfined) {
        println("Unconfined Dispatcher: Ejecutando en hilo: ${Thread.currentThread().name}")
        delay(500L) // simula una pequeña pausa
        println("Unconfined Dispatcher (después del delay): Ejecutando en hilo: ${Thread.currentThread().name}")
    }

    joinAll(job1, job2, job3) // Esperar a que terminen todos los trabajos
}

// Función que simula una operación de cálculo intensivo en la CPU
fun heavyComputation(): Int {
    // Simular cálculos complejos generando un número aleatorio después de un bucle de trabajo
    var result = 0
    for (i in 1..1_000_000) {
        result += Random.nextInt(0, 100)
    }
    return result
}

// Función que simula una operación de entrada/salida sin acceso al sistema de archivos
suspend fun simulateIOOperation() {
    delay(1000L) // Simulamos una operación que lleva tiempo, como leer datos de una base de datos o de una API
}
```

---

**En resumen:**  
- Las tareas intensivas deben ir a `Dispatchers.Default`.    
- Las operaciones de E/S deben usar `Dispatchers.IO`.    
- Las actualizaciones de interfaz deben realizarse en `Dispatchers.Main`.    

El dominio de las corrutinas y sus dispatchers es fundamental para desarrollar aplicaciones Android modernas, eficientes y libres de bloqueos.

---
