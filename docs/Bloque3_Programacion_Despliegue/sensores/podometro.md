# Podómetro

Podómetro con TYPE_STEP_COUNTER.   
Este sensor da un contador acumulado de pasos desde el último reinicio del dispositivo.  
Para que tenga sentido como “podómetro de la app”, solemos guardar el valor inicial y restarlo.   

## Ambos en el módulo de la aplicación

## Ejemplo android (MainActivity.kt)

```kotlin

import android.content.Context
import android.hardware.Sensor
import android.hardware.SensorEvent
import android.hardware.SensorEventListener
import android.hardware.SensorManager
import android.os.Bundle
import android.widget.TextView
import androidx.appcompat.app.AppCompatActivity

class MainActivity : AppCompatActivity(), SensorEventListener {

    private lateinit var sensorManager: SensorManager
    private var stepCounterSensor: Sensor? = null

    private lateinit var stepsText: TextView

    // Valor inicial del contador del sensor
    private var initialSteps: Float = -1f

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        stepsText = findViewById(R.id.stepsText)

        sensorManager = getSystemService(Context.SENSOR_SERVICE) as SensorManager

        // Obtener el sensor de contador de pasos
        stepCounterSensor = sensorManager.getDefaultSensor(Sensor.TYPE_STEP_COUNTER)

        if (stepCounterSensor == null) {
            stepsText.text = "No hay sensor de contador de pasos disponible en este dispositivo."
        }
    }

    override fun onResume() {
        super.onResume()
        stepCounterSensor?.also {
            sensorManager.registerListener(this, it, SensorManager.SENSOR_DELAY_NORMAL)
        }
    }

    override fun onPause() {
        super.onPause()
        sensorManager.unregisterListener(this)
    }

    override fun onSensorChanged(event: SensorEvent?) {
        if (event?.sensor?.type == Sensor.TYPE_STEP_COUNTER) {
            val totalSteps = event.values[0]

            // Guardamos el primer valor como referencia
            if (initialSteps < 0) {
                initialSteps = totalSteps
            }

            val stepsSinceStart = (totalSteps - initialSteps).toInt()
            stepsText.text = "Pasos: $stepsSinceStart"
        }
    }

    override fun onAccuracyChanged(sensor: Sensor?, accuracy: Int) {
        // No lo usamos en este ejemplo
    }
}

```

## XML sencillo (activity_main.xml)

```xml

<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:gravity="center"
    android:padding="16dp">

    <TextView
        android:id="@+id/stepsText"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Pasos: 0"
        android:textSize="24sp" />
</LinearLayout>

```

<details>

<summary>NOTA:</summary>
<p>En versiones modernas de Android (a partir de Android 10), la lectura de actividad física puede requerir el permiso
android.permission.ACTIVITY_RECOGNITION.</p>
<p>En una app real probablemente debemos:</p>
<ul>
    <li>Declarar en el AndroidManifest.xml:</li>
     <pre> &lt;uses-permission android:name="android.permission.ACTIVITY_RECOGNITION" /&gt;</pre>
    <li>Y en tiempo de ejecución pedirlo si la API ≥ 29.</li>
</ul>
<p>Esto es dependiente de versión; aquí hay un poco de especulación basada en la evolución de permisos de Android, pero la idea general es correcta.</p>

</details>