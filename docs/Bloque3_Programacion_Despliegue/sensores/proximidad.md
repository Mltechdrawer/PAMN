# Sensor de proximidad

El sensor de proximidad suele indicar distancia en cm o simplemente valores tipo “cerca / lejos” dependiendo del dispositivo.  

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
    private var proximitySensor: Sensor? = null
    private lateinit var proximityText: TextView

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        proximityText = findViewById(R.id.proximityText)

        // Obtener el servicio de sensores
        sensorManager = getSystemService(Context.SENSOR_SERVICE) as SensorManager

        // Obtener el sensor de proximidad
        proximitySensor = sensorManager.getDefaultSensor(Sensor.TYPE_PROXIMITY)

        if (proximitySensor == null) {
            proximityText.text = "No hay sensor de proximidad disponible en este dispositivo."
        }
    }

    override fun onResume() {
        super.onResume()
        proximitySensor?.also {
            sensorManager.registerListener(this, it, SensorManager.SENSOR_DELAY_NORMAL)
        }
    }

    override fun onPause() {
        super.onPause()
        sensorManager.unregisterListener(this)
    }

    override fun onSensorChanged(event: SensorEvent?) {
        if (event?.sensor?.type == Sensor.TYPE_PROXIMITY) {
            val distance = event.values[0]
            proximityText.text = "Distancia detectada: $distance cm"
        }
    }

    override fun onAccuracyChanged(sensor: Sensor?, accuracy: Int) { /* No usado */ }
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
        android:id="@+id/proximityText"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Esperando datos del sensor..."
        android:textSize="20sp"/>
</LinearLayout>

```
