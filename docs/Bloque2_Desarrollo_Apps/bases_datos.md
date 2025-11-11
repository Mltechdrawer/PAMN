# Bases de datos para aplicaciones móviles nativas

## Introducción: persistencia de datos en apps móviles

En el desarrollo de aplicaciones móviles, la **persistencia de datos** se refiere a la capacidad de una app para **almacenar información de forma permanente**, incluso después de cerrarse o reiniciarse el dispositivo.  
Es un componente esencial en casi cualquier aplicación: desde guardar las preferencias del usuario hasta mantener datos complejos como listas de tareas, notas o registros offline.

### Ejemplos de uso
- Guardar la sesión del usuario (token, configuración).  
- Almacenar contenido descargado (mensajes, fotos, notas).  
- Mantener datos disponibles sin conexión.  

La persistencia se diferencia del almacenamiento temporal (por ejemplo, variables en memoria), ya que los datos **se conservan en disco** hasta que se eliminen explícitamente.

[Ejemplo ToDoApp](bd_ejemplos.md#ejemplo1-aplicación-de-lista-de-tareas-todo-app)

### Criterios de selección
Elegir un mecanismo de almacenamiento depende de:
- El **volumen** y la **estructura** de los datos.  
- La **necesidad de sincronización** con servidores remotos.  
- Los **requisitos de seguridad y rendimiento**.  
- La **complejidad del modelo de datos**.

---

## Tipos de almacenamiento en Android

Android ofrece varios mecanismos de almacenamiento, cada uno con su propósito y nivel de complejidad.

### a) SharedPreferences / DataStore
- Almacenamiento de pares **clave-valor**.  
- Ideal para configuraciones simples (tema oscuro, idioma, autenticación).  
- Sustituido progresivamente por **DataStore**, basado en *coroutines* y *Flow*.

### b) Archivos internos y externos
- Permiten guardar ficheros binarios o de texto (JSON, XML, imágenes).  
- **Internos:** privados de la app.  
- **Externos:** visibles por el usuario o compartidos con otras apps (requieren permisos).

### c) Bases de datos relacionales
- Estructura basada en **tablas, filas y columnas**.  
- SQLite es el motor nativo de Android.  
- **Room**, el framework recomendado de Jetpack, simplifica su uso.

### d) Bases de datos no relacionales
- Almacenan datos en formato **documento u objeto**.  
- Ejemplos: **Realm**, **ObjectBox**, **Firebase Realtime Database** y **Firestore**.  
- Ventaja: sincronización automática y estructuras dinámicas.

| Tipo | Ejemplo | Uso recomendado |
|------|----------|----------------|
| Clave-valor | SharedPreferences / DataStore | Configuraciones simples |
| Ficheros | JSON, imágenes, logs | Datos personalizados |
| Relacional | SQLite / Room | Datos estructurados y persistentes |
| No relacional | Realm / Firestore | Sincronización y estructura flexible |

---

## SQLite: el motor relacional integrado en Android

**SQLite** es un motor de base de datos relacional ligero, integrado en el sistema operativo Android.  
No requiere configuración de servidor y almacena la información en un solo archivo local.

### Características principales
- Basado en SQL estándar (tablas, relaciones, consultas, índices).  
- Muy eficiente en rendimiento y espacio.  
- Soporta transacciones y constraints (clave primaria, foránea, etc.).

### Uso en Android
Antes de Room, el acceso se realizaba directamente mediante la clase `SQLiteOpenHelper`:

```kotlin
class UserDatabaseHelper(context: Context) :
    SQLiteOpenHelper(context, "users.db", null, 1) {

    override fun onCreate(db: SQLiteDatabase) {
        db.execSQL("CREATE TABLE users (id INTEGER PRIMARY KEY, name TEXT, email TEXT)")
    }

    override fun onUpgrade(db: SQLiteDatabase, oldVersion: Int, newVersion: Int) {
        db.execSQL("DROP TABLE IF EXISTS users")
        onCreate(db)
    }
}
```

### Limitaciones

- Mucho código *boilerplate* (SQL manual).  
- Sin comprobación en tiempo de compilación.  
- Dificultad de mantenimiento en proyectos grandes.

---

## Room: la capa de abstracción de SQLite recomendada por Google

**Room** es una librería de Jetpack que actúa como una **capa de abstracción sobre SQLite**.  
Permite trabajar con objetos Kotlin en lugar de consultas SQL directas, aumentando la productividad y seguridad del código.

### Componentes principales

- **Entity** – Representa una tabla en la base de datos.  
- **DAO (Data Access Object)** – Define las operaciones CRUD (*Create, Read, Update, Delete*).  
- **Database** – Punto de acceso general que enlaza las entidades y DAOs.

```kotlin
@Entity(tableName = "users")
data class User(
    @PrimaryKey(autoGenerate = true) val id: Int = 0,
    val name: String,
    val email: String
)

@Dao
interface UserDao {
    @Query("SELECT * FROM users")
    suspend fun getAll(): List<User>

    @Insert
    suspend fun insert(user: User)
}

@Database(entities = [User::class], version = 1)
abstract class AppDatabase : RoomDatabase() {
    abstract fun userDao(): UserDao
}

```

### Ventajas

- Validación de consultas en tiempo de compilación.  
- Integración con *coroutines*, *Flow* y *LiveData*.  
- Reducción del código repetitivo.  
- Fácil migración entre versiones.

---

## Arquitectura recomendada con Room

**Room** se integra perfectamente en la arquitectura **MVVM (Model–View–ViewModel)**.

### Flujo recomendado

> **Flujo recomendado:**  
> ViewModel → Repository → DAO → Entity → SQLite

### Ventajas

- Separación de responsabilidades.
- Facilita las pruebas unitarias.
- Mejora la mantenibilidad.

## Migraciones y mantenimiento de la base de datos

Cuando se cambia el esquema (por ejemplo, añadir una columna o tabla nueva), es necesario realizar una migración para conservar los datos existentes.

```kotlin
val migration_1_2 = object : Migration(1, 2) {
    override fun migrate(database: SupportSQLiteDatabase) {
        database.execSQL("ALTER TABLE users ADD COLUMN age INTEGER NOT NULL DEFAULT 0")
    }
}

val db = Room.databaseBuilder(context, AppDatabase::class.java, "app.db")
    .addMigrations(migration_1_2)
    .build()

```

### Buenas prácticas

- Versionar el esquema con `exportSchema = true`.  
- Evitar `fallbackToDestructiveMigration()` (elimina los datos).  
- Probar las migraciones con tests automáticos.

---

## Sincronización y persistencia híbrida

Muchas apps combinan almacenamiento local con sincronización remota.  
Esto se conoce como **persistencia híbrida** o enfoque *offline-first*.

### Estrategias comunes

- **Cache local + API REST:** los datos se guardan localmente y se sincronizan con el servidor.  
- **Colas de operaciones pendientes:** cuando no hay conexión, las operaciones se registran y se ejecutan más tarde.  
- **Conflict resolution:** reglas para resolver diferencias entre los datos locales y remotos.

```kotlin
class TaskRepository(
    private val dao: TaskDao,
    private val api: TaskService
) {
    val tasks = dao.getAll()

    suspend fun sync() {
        val remoteTasks = api.getTasks()
        dao.insertAll(remoteTasks)
    }
}

```

## Bases de datos no relacionales en apps móviles

Además de SQLite y Room, existen soluciones **no relacionales** diseñadas para mejorar el rendimiento y ofrecer sincronización en tiempo real.

### Realm
- Base de datos orientada a objetos.  
- No requiere SQL ni DAOs: los objetos se guardan directamente.  
- Sincronización automática con la nube (opcional).

### ObjectBox
- Muy rápida (optimizaciones nativas para Android).  
- Usa anotaciones y consultas tipo objeto.  
- Ideal para apps con gran volumen de datos locales.

### Firebase Realtime Database / Firestore
- Bases de datos en la nube de Google.  
- Sincronización en tiempo real entre usuarios y dispositivos.  
- Soporte *offline* automático.  
- Estructura basada en documentos (*NoSQL*).

| Solución | Tipo | Características principales |
|-----------|------|-----------------------------|
| Realm | No relacional (objetos) | Alta velocidad, persistencia automática |
| ObjectBox | No relacional (objetos) | Muy eficiente en lectura/escritura |
| Firestore | Documental (nube) | Sincronización automática y escalabilidad |

## Seguridad y buenas prácticas en bases de datos móviles

La seguridad de los datos locales es fundamental para proteger la información del usuario.

### Recomendaciones

- Encriptar la base de datos: usar **SQLCipher** o **EncryptedSharedPreferences**.  
- Proteger el acceso: validar identidad antes de leer o escribir.  
- Evitar almacenar contraseñas o tokens sin cifrar.  
- Eliminar datos sensibles al cerrar sesión o desinstalar la app.

### Optimización y rendimiento

- Usar índices para acelerar consultas.  
- Evitar operaciones pesadas en el hilo principal.  
- Reutilizar conexiones y cerrar cursores.  
- Limitar la cantidad de registros devueltos.

---

## Ejemplo práctico: app de notas con Room

**Objetivo:** crear una aplicación de notas que guarde y muestre datos de manera local.

### Componentes

- `Note` (Entity)  
- `NoteDao` (DAO)  
- `AppDatabase` (Base de datos)  
- `NoteRepository` (Capa de datos)  
- `NoteViewModel` (Capa de presentación)

```kotlin
@Entity
data class Note(
    @PrimaryKey(autoGenerate = true) val id: Int = 0,
    val title: String,
    val content: String
)

@Dao
interface NoteDao {
    @Query("SELECT * FROM Note ORDER BY id DESC")
    fun getAll(): Flow<List<Note>>

    @Insert
    suspend fun insert(note: Note)

    @Delete
    suspend fun delete(note: Note)
}

@Database(entities = [Note::class], version = 1)
abstract class AppDatabase : RoomDatabase() {
    abstract fun noteDao(): NoteDao
}

class NoteRepository(private val dao: NoteDao) {
    val notes = dao.getAll()
    suspend fun add(note: Note) = dao.insert(note)
}

class NoteViewModel(private val repository: NoteRepository) : ViewModel() {
    val notes = repository.notes.asLiveData()
    fun add(note: Note) = viewModelScope.launch { repository.add(note) }
}

```
La interfaz (`Activity` o `Fragment`) se conecta al `ViewModel` y observa los cambios de `LiveData` para actualizar la interfaz de usuario en tiempo real.

---

## Conclusión

La persistencia de datos es un pilar esencial en las aplicaciones móviles nativas.  
Android ofrece múltiples opciones, pero **Room** se ha consolidado como la herramienta estándar por su facilidad de uso, seguridad y compatibilidad con la arquitectura moderna de Android.

### Puntos clave

- Usar **Room** para bases de datos estructuradas locales.  
- Sincronizar datos con repositorios remotos cuando sea necesario.  
- Proteger la información mediante cifrado y buenas prácticas.  
- Priorizar la arquitectura limpia y mantenible.

> Una app sin un modelo sólido de persistencia es frágil e inconsistente: la base de datos es la memoria a largo plazo de toda aplicación.

**Referencias:**  
- [Documentación oficial de Room](https://developer.android.com/training/data-storage/room)  
- [Documentación de SQLite](https://www.sqlite.org/docs.html)
