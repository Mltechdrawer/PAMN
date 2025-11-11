# Ejemplos

### Ejemplo1: aplicación de lista de tareas (ToDo App)

Imaginemos una aplicación de lista de tareas, donde el usuario puede crear, editar y marcar tareas como completadas.  
Si la información se almacenara solo en memoria, al cerrar la app o reiniciar el móvil todas las tareas se perderían.  

Para evitarlo, la app debe **persistir los datos** de las tareas en una base de datos local (por ejemplo, usando **Room** o **SQLite**), de modo que al volver a abrir la aplicación las tareas sigan allí, incluso sin conexión a internet.

En este caso:

- El **modelo de datos** incluye atributos como `id`, `título`, `descripción` y `estado`.  
- El **DAO** gestiona operaciones como insertar, actualizar o eliminar tareas.  
- El **ViewModel** expone la lista de tareas a la interfaz mediante `LiveData`.  
- Y la **base de datos Room** garantiza que la información persista en el dispositivo.

Este tipo de persistencia es fundamental en casi todas las apps: desde un gestor de notas hasta un calendario o una aplicación de seguimiento de hábitos.

### Ejemplo práctico: ToDo App con Room + MVVM

A continuación se muestra un esqueleto mínimo para persistir tareas localmente con Room e integrarlo en una arquitectura MVVM.

<div class="admonition info inline end">
<p class="admonition-title">Entity</p>
<p>Definimos una entidad de base de datos en Room, concretamente: una data class de Kotlin anotada con @Entity, que representa una tabla de la base de datos Room.</p>
<p>Cada propiedad de la clase corresponde a una columna de la tabla, y cada instancia de la clase será una fila (registro).</p>
</div>

#### 1) Entidad (`Task`)
```kotlin
import androidx.room.Entity
import androidx.room.PrimaryKey

@Entity(tableName = "tasks")
data class Task(
    @PrimaryKey(autoGenerate = true) val id: Long = 0L,
    val title: String,
    val description: String = "",
    val done: Boolean = false,
    val createdAt: Long = System.currentTimeMillis()
)
```
#### 2) DAO (TaskDao)

<details>
<summary>Data Access Object</summary>
<p>Un DAO es una interfaz o clase que define métodos de acceso a la base de datos: consultas, inserciones, actualizaciones y eliminaciones.</p>
<p>El DAO (Data Access Object) es un componente clave del patrón Room —y, en general, de cualquier arquitectura que gestione bases de datos—.</p>
<p>Su propósito es aislar la lógica de acceso a los datos del resto de la aplicación, haciendo que el código sea más limpio, mantenible y comprobable.</p>
<p>En Room, los DAO se marcan con la anotación @Dao y Room se encarga automáticamente de generar la implementación real de esa interfaz en tiempo de compilación.</p>
<ul>
  <li>Define qué operaciones se pueden hacer sobre la base de datos.</li>
  <li>Encapsula todas las consultas SQL y evita que estén repartidas por la app.</li>
  <li>Permite que los datos se obtengan de forma asíncrona (con <code>suspend</code>, <code>Flow</code> o <code>LiveData</code>).</li>
  <li>Room valida las consultas y sus tipos en tiempo de compilación —si hay un error en el SQL, el IDE lo detecta antes de ejecutar la app.</li>
</ul>
</details>

<div class="admonition info inline end">
<p class="admonition-title">TaskDAO</p>
<p>Definimos qué operaciones se pueden hacer sobre los datos (consultar, insertar, actualizar, borrar…)..</p>
<ul>
<li>@Query para Ejecutar consultas SQL.</li>
<li>@Insert, @Update, @Delete para Operaciones CRUD.</li>
<li>Flow para implementar Reactividad → actualiza la UI cuando cambian los datos.</li>
<li>suspend para ejecutar sin bloquear el hilo principal.</li>
</ul>
</div>

```kotlin
import androidx.room.*
import kotlinx.coroutines.flow.Flow

@Dao
interface TaskDao {

    @Query("SELECT * FROM tasks ORDER BY done ASC, createdAt DESC")
    fun observeAll(): Flow<List<Task>>

    @Query("SELECT * FROM tasks WHERE id = :id")
    suspend fun getById(id: Long): Task?

    @Insert(onConflict = OnConflictStrategy.REPLACE)
    suspend fun upsert(task: Task): Long

    @Update
    suspend fun update(task: Task)

    @Delete
    suspend fun delete(task: Task)

    @Query("DELETE FROM tasks WHERE done = 1")
    suspend fun deleteCompleted()
}
```
#### 3) Base de datos (AppDatabase)

<div class="admonition info inline end">
<p class="admonition-title">AppDatabase</p>
<p>Definimos la base de datos  que va a utilizar Room</p>
<ul>
<li>@Database(...) -> describe la base de datos de Room.</li>
<li>entities = [Task::class] -> tablas que forman la BD. </li>
<li>exportSchema = true -> Permite exportar el esquema para control de versiones</li>
<li>AppDatabase : RoomDatabase() -> Hereda de RoomDatabase</li>
<li>abstract fun taskDao() -> Room generará la implementación real y esta función será el punto de acceso para usar el DAO..</li>
</ul>
</div>

```kotlin
import androidx.room.Database
import androidx.room.RoomDatabase


@Database(entities = [Task::class], version = 1, exportSchema = true)
abstract class AppDatabase : RoomDatabase() {
    abstract fun taskDao(): TaskDao
}


```

> Inicialización típica (p. ej., en un Module de Hilt o en una clase App):

```kotlin
import android.content.Context
import androidx.room.Room


fun provideDatabase(context: Context): AppDatabase =
    Room.databaseBuilder(context, AppDatabase::class.java, "todo.db")
        .fallbackToDestructiveMigrationOnDowngrade() // Solo para desarrollo, evita en producción
        .build()
```

#### 4) Repositorio (TaskRepository)

<div class="admonition info inline end">
<p class="admonition-title">TaskRepository</p>
<p>Clase que encapsula el acceso a los datos.</p>
<ul>
<li>Recibe el TaskDao por inyección de dependencias.</li>
<li>Devuelve un Flow con la lista de tareas y actualiza UI.</li>
<li>Crea una nueva tarea que guarda o actualiza</li>
<li>ToggleDone -> Busca una tarea</li>
<li>remove-clearCompleted -> Borra tarea o todas las terminadas.</li>
</ul>
</div>

```kotlin
import kotlinx.coroutines.flow.Flow

class TaskRepository(private val dao: TaskDao) {

    fun observeTasks(): Flow<List<Task>> = dao.observeAll()

    suspend fun add(title: String, description: String = ""): Long =
        dao.upsert(Task(title = title, description = description))

    suspend fun toggleDone(id: Long) {
        val t = dao.getById(id) ?: return
        dao.update(t.copy(done = !t.done))
    }

    suspend fun remove(task: Task) = dao.delete(task)

    suspend fun clearCompleted() = dao.deleteCompleted()
}
```

#### 5) ViewModel (TaskViewModel)

<div class="admonition info inline end">
<p class="admonition-title">TaskViewModel</p>
<p>ViewModel vive mientras la pantalla está visible y sobrevive a rotaciones. Mantiene el estado de la UI. Llama al repositorio. Ejecuta corrutinas sin bloquear la UI.</p>
<ul>
<li>repository.observeTasks() -> Devuelve un Flow-List-Task desde Room.</li>
<li>.stateIn(...) -> Convierte el Flow en un StateFlow, algo que la UI puede observar fácilmente y que mantiene el último valor.</li>
<li>CviewModelScope -> Contexto de corrutinas del ViewModel — se cancela al destruirse.</li>
<li>SharingStarted.Lazily -> El flujo se empezará a observar sólo cuando alguien lo use.</li>
<li>emptyList()	-> Valor inicial mientras se carga.</li>
</ul>
</div>

```kotlin
import androidx.lifecycle.ViewModel
import androidx.lifecycle.viewModelScope
import kotlinx.coroutines.flow.SharingStarted
import kotlinx.coroutines.flow.stateIn
import kotlinx.coroutines.launch

// Inyección de dependencias para recibir el respositorio
class TaskViewModel(private val repository: TaskRepository) : ViewModel() {

    // Estado observable por la UI (Flow -> StateFlow)
    val tasks = repository.observeTasks()
        .stateIn(viewModelScope, SharingStarted.Lazily, emptyList())

    fun addTask(title: String, description: String = "") =
        viewModelScope.launch { repository.add(title, description) }

    fun toggle(id: Long) =
        viewModelScope.launch { repository.toggleDone(id) }

    fun delete(task: Task) =
        viewModelScope.launch { repository.remove(task) }

    fun clearCompleted() =
        viewModelScope.launch { repository.clearCompleted() }
}
```

#### 6) Conexión con la UI (idea general)

<div class="admonition info inline end">
<p class="admonition-title">Conexión con la UI</p>
<p>Idea general. Flujo completo.</p>
<ul>
<li>viewLifecycleOwner.lifecycleScope -> Scope ligado al ciclo de vida del Fragment</li>
<li>launchWhenStarted -> Colecciona si la UI está visible.</li>
<li>viewModel.tasks.collect -> list -> adapter.submitList(list) - Cuando la lista cambia, el RecyclerView se vuelve a renderizar.</li>
</ul>
</div>

- La Activity o Fragment colecciona tasks (StateFlow) y renderiza la lista.

- Las acciones del usuario (añadir, marcar, borrar) llaman a métodos del ViewModel.

```kotlin
// En un Fragment (ejemplo con lifecycleScope)
viewLifecycleOwner.lifecycleScope.launchWhenStarted {
    viewModel.tasks.collect { list ->
        adapter.submitList(list) // Renderizar lista
    }
}

// Acciones
addButton.setOnClickListener { viewModel.addTask(inputTitle.text.toString()) }
```
> Esto hace que la lista se actualice automáticamente cuando la BD cambia.
> Sin que la UI tenga que "preguntar" cada vez.


#### Ventajas principales

- Menos código repetitivo: genera automáticamente las implementaciones de los DAO.
- Validación en tiempo de compilación: detecta errores en las consultas SQL antes de ejecutar la app.
- Integración moderna: compatible con [Coroutines](bd_ejemplos.md#corrutinas), [Flow](bd_ejemplos.md#flow), [LiveData](bd_ejemplos.md#livedata) y [ViewModel](bd_ejemplos.md#viewmodel).
- Soporte para migraciones: permite cambiar el esquema de la base de datos sin perder datos.
- Compatible con arquitectura MVVM o Clean Architecture.

---

#### Corrutinas

Son la forma ligera y eficiente de realizar programación asíncrona y concurrente sin necesidad de manejar directamente hilos (threads). En lugar de bloquear un hilo mientras se espera una operación (por ejemplo, una consulta a base de datos o una petición HTTP), una corrutina suspende su ejecución y el hilo queda libre para hacer otras tareas.   

Cuando la operación termina, la corrutina retoma exactamente donde lo dejó.

<details>
<summary>Ejemplo</summary>

```kotlin
import kotlinx.coroutines.*

fun main() = runBlocking {
    launch {
        delay(1000)
        println("Hola desde la corrutina")
    }
    println("Hola desde el hilo principal")
}

```
</details>
---



#### Flow

Es una API de flujo de datos asíncronos pensada para emitir múltiples valores a lo largo del tiempo, de forma reactiva y no bloqueante.  
Se usa cuando tenemos datos que cambian con el tiempo, por ejemplo:
- Lecturas continuas desde una base de datos.
- Eventos de UI.
- Streams de sensores.
- Resultados de peticiones que se actualizan periódicamente.


<details>
<summary>Ejemplo</summary>

```kotlin
import kotlinx.coroutines.*
import kotlinx.coroutines.flow.*

fun main() = runBlocking {
    val numbersFlow = flow {
        for (i in 1..5) {
            delay(500)
            emit(i) // Emitimos valores
        }
    }

    numbersFlow.collect { value ->
        println("Recibido: $value")
    }
}

```
</details>
---

#### LiveData

Es una clase de Android (parte de Android Jetpack) que permite almacenar y observar datos de forma reactiva. Su objetivo principal es que la interfaz de usuario (UI) se actualice automáticamente cuando los datos cambian, respetando el ciclo de vida de las actividades y fragmentos.

Cuando trabajamos con Activities/Fragments, estos pueden:  
- Girar (cambio de orientación)  
- Entrar en background  
- Ser destruidos y recreados  

Si la UI observa datos directamente sin tener en cuenta el ciclo de vida, puedes tener:  
- Fugas de memoria  
- Llamadas duplicadas  
- Código difícil de mantener  

LiveData se encarga de evitar eso automáticamente.

<details>
<summary>Ejemplo</summary>

```kotlin
class MiViewModel : ViewModel() {
    val contador = MutableLiveData<Int>()

    fun incrementar() {
        val valorActual = contador.value ?: 0
        contador.value = valorActual + 1
    }
}

```

Y en la Activity/Fragment:

```kotlin
miViewModel.contador.observe(this) { valor ->
    textView.text = valor.toString()
}

```

</details>

---

#### ViewModel

Componente encargado de mantener y gestionar los datos que la interfaz de usuario (UI) necesita, asegurándose de que esos datos sobrevivan a cambios de configuración (como la rotación de pantalla). No es parte de Room en sí, es uno de los componentes principales que se usa junto con Room en la arquitectura recomendada (MVVM).

UI (Activity/Fragment)  <--->  ViewModel  <--->  Repository  <--->  Room (DAO, Database)

<details>
<summary>Ejemplo</summary>

```kotlin
class UsuarioViewModel(private val repo: UsuarioRepository) : ViewModel() {
    val listaUsuarios = repo.obtenerUsuarios() // Flow<List<Usuario>>

    fun insertar(usuario: Usuario) = viewModelScope.launch {
        repo.insertar(usuario)
    }
}

```

Y en Fragment:

```kotlin
viewLifecycleOwner.lifecycleScope.launch {
    viewModel.listaUsuarios.collect { usuarios ->
        adapter.submitList(usuarios)
    }
}

```

</details>
---
