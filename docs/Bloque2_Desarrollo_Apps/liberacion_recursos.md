# Ejemplos de liberación de recursos en Android (Kotlin)

Estos ejemplos muestran cómo liberar correctamente recursos (como cámaras, sensores o *view bindings*) en los métodos adecuados del ciclo de vida de un componente Android.

---

## Ejemplo 1: Liberación de *binding* y sensores en un Fragment

```kotlin
class CameraFragment : Fragment() {

    private var _binding: FragmentCameraBinding? = null
    private val binding get() = _binding!!

    private var camera: Camera? = null
    private var sensorManager: SensorManager? = null
    private var lightSensor: Sensor? = null

    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View {
        _binding = FragmentCameraBinding.inflate(inflater, container, false)
        return binding.root
    }

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)

        // Inicializar sensores
        sensorManager = requireContext().getSystemService(Context.SENSOR_SERVICE) as SensorManager
        lightSensor = sensorManager?.getDefaultSensor(Sensor.TYPE_LIGHT)

        // Inicializar cámara (ejemplo simplificado)
        camera = Camera.open()
    }

    override fun onPause() {
        super.onPause()
        // Liberar la cámara si el fragment pierde el foco
        camera?.release()
        camera = null
    }

    override fun onDestroyView() {
        super.onDestroyView()
        // Eliminar referencias a vistas para evitar fugas de memoria
        _binding = null

        // Anular sensores
        sensorManager?.unregisterListener(null)
        sensorManager = null
    }
}
```

### Explicación
- `onCreateView()` → se crea la vista y el *binding*.  
- `onViewCreated()` → se inicializan recursos (cámara, sensores).  
- `onPause()` → se pausa el acceso a la cámara.  
- `onDestroyView()` → se libera el *binding* y se anulan las referencias a sensores.  

---

## Ejemplo 2: Liberación de recursos en una Activity

```kotlin
class CameraActivity : AppCompatActivity() {

    private var camera: Camera? = null

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_camera)

        // Inicializar cámara
        camera = Camera.open()
    }

    override fun onStop() {
        super.onStop()
        // Liberar la cámara cuando la Activity deja de ser visible
        camera?.release()
        camera = null
    }
}
```

### Explicación
- `onCreate()` → inicializa la cámara.  
- `onStop()` → libera el recurso porque la Activity deja de estar visible.  
  (Si esperas volver rápido, podrías hacerlo en `onPause()` en lugar de `onStop()`).

---

## Buenas prácticas

- `onDestroyView()` → liberar *bindings* y vistas en **Fragments**.  
- `onStop()` → liberar recursos visuales o de hardware en **Activities**.  
- `onDestroy()` → limpieza final si el componente ya no se reanudará.  

---
