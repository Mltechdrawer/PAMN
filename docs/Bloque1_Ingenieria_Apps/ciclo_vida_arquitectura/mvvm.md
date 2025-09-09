# Modelo–Vista–VistaModelo (MVVM)

## 1. Crear el modelo (Model)
El modelo representa la capa de datos de la aplicación. Esta capa se encarga de manejar la lógica de negocio, interactuar con bases de datos o servicios externos y proporcionar los datos que necesita la interfaz de usuario.
   
```kotlin

// Model.kt
data class User(val id: Int, val name: String, val age: Int)

// Puede ser una clase de repositorio que maneja la obtención de datos
class UserRepository {

    private val userList = listOf(
        User(1, "John Doe", 30),
        User(2, "Jane Smith", 25)
    )

    // Simulamos la obtención de los datos
    fun getUsers(): List<User> = userList
}

```

## 2. Crear la vista modelo (ViewModel)
La VistaModelo es el intermediario entre el Modelo y la Vista. Proporciona los datos de forma que la vista pueda observar los cambios y actualizarse automáticamente cuando los datos cambien.
Utilizamos LiveData para hacer la comunicación entre el ViewModel y la Vista, de forma que la Vista observe los cambios y reaccione automáticamente.

   
```kotlin

// UserViewModel.kt
import androidx.lifecycle.LiveData
import androidx.lifecycle.MutableLiveData
import androidx.lifecycle.ViewModel

class UserViewModel : ViewModel() {

    private val userRepository = UserRepository()

    // MutableLiveData que puede ser observada por la vista
    private val _users = MutableLiveData<List<User>>()

    // Exponemos los datos como LiveData, para que la vista observe los cambios
    val users: LiveData<List<User>>
        get() = _users

    // Cargar los usuarios y actualizar el LiveData
    fun loadUsers() {
        val usersFromRepo = userRepository.getUsers()
        _users.value = usersFromRepo
    }
}

```

## 2. Crear la vista (View)
La Vista en Android puede ser una Activity, un Fragment o cualquier componente de la interfaz que observe el ViewModel. En este caso, usaremos una Activity que observará los cambios del ViewModel y reaccionará en consecuencia.
Aquí es donde entraría el uso de DataBinding para hacer el enlace de datos entre el ViewModel y la Vista.

### 1. Habilitamos Databinding en el archivo build.gradle: 

```kotlin

android {
    ...
    buildFeatures {
        dataBinding true
    }
}

```

### 2. Creamos el layout XML con el enlace de datos al ViewModel: 

```kotlin

<!-- activity_main.xml -->
<layout xmlns:android="http://schemas.android.com/apk/res/android">

    <data>
        <!-- Vinculamos el ViewModel -->
        <variable
            name="viewModel"
            type="com.example.mvvmexample.UserViewModel" />
    </data>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical">

        <TextView
            android:id="@+id/userText"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Users" />

        <ListView
            android:id="@+id/userListView"
            android:layout_width="match_parent"
            android:layout_height="wrap_content" />
    </LinearLayout>
</layout>

```

### 3. Implementamos la activity que observará el ViewModel: 

```kotlin

// MainActivity.kt
import android.os.Bundle
import androidx.activity.viewModels
import androidx.appcompat.app.AppCompatActivity
import androidx.databinding.DataBindingUtil
import androidx.lifecycle.Observer
import com.example.mvvmexample.databinding.ActivityMainBinding

class MainActivity : AppCompatActivity() {

    // Instanciamos el ViewModel usando la función por defecto
    private val userViewModel: UserViewModel by viewModels()

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        // Enlazamos el layout con DataBinding
        val binding: ActivityMainBinding =
            DataBindingUtil.setContentView(this, R.layout.activity_main)

        // Asignamos el ViewModel a la vista
        binding.viewModel = userViewModel
        binding.lifecycleOwner = this // Para que el binding observe los cambios

        // Observamos el LiveData del ViewModel
        userViewModel.users.observe(this, Observer { users ->
            // Actualizamos la interfaz cuando los datos cambian
            binding.userText.text = users.joinToString { it.name }
        })

        // Cargar los datos en el ViewModel
        userViewModel.loadUsers()
    }
}


```