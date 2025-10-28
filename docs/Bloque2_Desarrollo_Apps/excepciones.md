# Excepciones

Una excepción es un mecanismo del lenguaje de programación que permite detectar y manejar errores o situaciones anómalas que ocurren durante la ejecución de un programa.

En lugar de que el programa “se rompa” o finalice abruptamente cuando algo falla, las excepciones permiten interceptar el error, entender qué ocurrió y reaccionar adecuadamente (por ejemplo, mostrando un mensaje al usuario o intentando otra acción).

> Una excepción es un evento inesperado que altera el flujo normal del programa y que el sistema puede capturar y tratar de forma controlada.

**¿Por qué se plantearon las excepciones?**

Antes de que existieran, el manejo de errores era manual y repetitivo:  
- Los programadores debían comprobar continuamente si algo salía mal (por ejemplo, si un archivo existía o si una división era válida).  
- Esto generaba código confuso y difícil de mantener, lleno de condiciones (if...else) que controlaban cada posible fallo.  
- Proyecto Android en Kotlin siguiendo una arquitectura MVVM + Clean, indicando en qué carpetas y ficheros debe ubicarse el manejo de excepciones y la lógica de red.  


## Estructura de proyecto y ubicación del manejo de excepciones en Kotlin

### Estructura general del proyecto

```bash
app/
└─ src/
   └─ main/
      ├─ AndroidManifest.xml
      └─ java/com/example/todo/
         ├─ App.kt                                  # (opcional) Application + DI (Hilt)
         ├─ core/
         │  ├─ exceptions/
         │  │  ├─ ApiException.kt
         │  │  ├─ DataFormatException.kt
         │  │  ├─ NetworkException.kt
         │  │  └─ UnknownAppException.kt
         │  └─ utils/
         │
         ├─ data/
         │  ├─ model/
         │  │  └─ TaskDto.kt
         │  ├─ network/
         │  │  ├─ ApiClient.kt
         │  │  └─ RetrofitApiClient.kt
         │  ├─ mapper/
         │  │  └─ TaskMapper.kt
         │  └─ repository/
         │     └─ TaskRepositoryImpl.kt
         │
         ├─ domain/
         │  ├─ model/
         │  │  └─ Task.kt
         │  └─ repository/
         │     └─ TaskRepository.kt
         │
         └─ ui/
            └─ tasks/
               ├─ TaskViewModel.kt
               ├─ TaskListFragment.kt
               └─ TaskListAdapter.kt

```

#### Excepciones personalizadas

Ruta: core/exceptions/*

``` kotlin
package com.example.todo.core.exceptions

class ApiException(message: String) : Exception(message)
class DataFormatException(message: String) : Exception(message)
class NetworkException(message: String, cause: Throwable? = null) : Exception(message, cause)
class UnknownAppException(message: String, cause: Throwable? = null) : Exception(message, cause)
```

#### Contrato del repositorio (dominio)

Ruta: domain/repository/TaskRepository.kt

``` kotlin
package com.example.todo.domain.repository

import com.example.todo.domain.model.Task

interface TaskRepository {
    suspend fun getTasks(): List<Task>
}
```

#### Entidad de dominio

Ruta: domain/model/Task.kt

``` kotlin
package com.example.todo.domain.model

data class Task(
    val id: String,
    val title: String,
    val completed: Boolean
)
```

### Cliente de red

Ruta (interfaz): data/network/ApiClient.kt
Ruta (implementación): data/network/RetrofitApiClient.kt

```kotlin 
package com.example.todo.data.network

import retrofit2.Response
import com.example.todo.data.model.TaskDto

interface ApiClient {
    suspend fun fetchTasks(): Response<List<TaskDto>>
}
```

```kotlin 
package com.example.todo.data.network

import retrofit2.Response
import retrofit2.http.GET
import retrofit2.Retrofit
import retrofit2.converter.moshi.MoshiConverterFactory
import com.example.todo.data.model.TaskDto

interface TaskService {
    @GET("tasks")
    suspend fun getTasks(): Response<List<TaskDto>>
}

class RetrofitApiClient(
    baseUrl: String = "https://api.example.com/"
) : ApiClient {

    private val service: TaskService = Retrofit.Builder()
        .baseUrl(baseUrl)
        .addConverterFactory(MoshiConverterFactory.create())
        .build()
        .create(TaskService::class.java)

    override suspend fun fetchTasks(): Response<List<TaskDto>> = service.getTasks()
}
```

### Repositorio (implementación del manejo de excepciones)

Ruta: data/repository/TaskRepositoryImpl.kt

```kotlin
package com.example.todo.data.repository

import com.example.todo.core.exceptions.*
import com.example.todo.data.mapper.toDomain
import com.example.todo.data.network.ApiClient
import com.example.todo.domain.model.Task
import com.example.todo.domain.repository.TaskRepository
import java.io.IOException

class TaskRepositoryImpl(
    private val apiClient: ApiClient
) : TaskRepository {

    override suspend fun getTasks(): List<Task> {
        try {
            val response = apiClient.fetchTasks()

            if (!response.isSuccessful) {
                throw ApiException("Error ${response.code()}: ${response.message()}")
            }

            val body = response.body() ?: throw DataFormatException("Respuesta vacía")
            return body.map { it.toDomain() }

        } catch (e: IOException) {
            throw NetworkException("No se pudo conectar al servidor", e)

        } catch (e: ApiException) {
            throw e

        } catch (e: DataFormatException) {
            throw e

        } catch (e: Exception) {
            throw UnknownAppException("Error inesperado", e)
        }
    }
}

```

### ViewModel (manejo de excepciones en la capa de presentación)

Ruta: ui/tasks/TaskViewModel.kt

```kotlin
package com.example.todo.ui.tasks

import androidx.lifecycle.*
import com.example.todo.core.exceptions.*
import com.example.todo.domain.repository.TaskRepository
import kotlinx.coroutines.launch

class TaskViewModel(
    private val repository: TaskRepository
) : ViewModel() {

    private val _uiState = MutableLiveData<String>()
    val uiState: LiveData<String> = _uiState

    fun loadTasks() {
        viewModelScope.launch {
            try {
                val tasks = repository.getTasks()
                _uiState.value = "Se cargaron ${tasks.size} tareas correctamente."
            } catch (e: NetworkException) {
                _uiState.value = "No hay conexión. Revisa tu red."
            } catch (e: ApiException) {
                _uiState.value = "El servidor devolvió un error."
            } catch (e: DataFormatException) {
                _uiState.value = "Error al procesar los datos."
            } catch (e: Exception) {
                _uiState.value = "Error desconocido: ${e.message}"
            }
        }
    }
}

```

### UI (Fragment o Activity)

Ruta: ui/tasks/TaskListFragment.kt

```kotlin
package com.example.todo.ui.tasks

import android.os.Bundle
import android.view.*
import androidx.fragment.app.Fragment
import androidx.fragment.app.viewModels
import androidx.lifecycle.Observer
import com.example.todo.databinding.FragmentTaskListBinding

class TaskListFragment : Fragment() {

    private var _binding: FragmentTaskListBinding? = null
    private val binding get() = _binding!!

    private val viewModel: TaskViewModel by viewModels { /* factory o Hilt */ }

    override fun onCreateView(
        inflater: LayoutInflater, container: ViewGroup?, savedInstanceState: Bundle?
    ) = FragmentTaskListBinding.inflate(inflater, container, false).also { _binding = it }.root

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        viewModel.uiState.observe(viewLifecycleOwner, Observer { msg ->
            binding.statusText.text = msg
        })
        viewModel.loadTasks()
    }

    override fun onDestroyView() {
        super.onDestroyView()
        _binding = null
    }
}

```

### Inyección de dependencias (opcional con Hilt)

Ruta: di/RepositoryModule.kt

```kotlin
@Module
@InstallIn(SingletonComponent::class)
abstract class RepositoryModule {
    @Binds
    abstract fun bindTaskRepository(impl: TaskRepositoryImpl): TaskRepository
}

```

El primer bloque (manejo de errores en red) se ubica en data/repository/TaskRepositoryImpl.kt.
El segundo bloque (gestión de UI y mensajes) se ubica en ui/tasks/TaskViewModel.kt.

Con esta estructura se logra:

* Separación de responsabilidades (Clean Architecture).  
* Código mantenible y testeable.  
* Manejo coherente de excepciones en toda la aplicación.  
