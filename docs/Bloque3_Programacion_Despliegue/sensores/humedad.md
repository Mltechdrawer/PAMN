# Humedad

Este sensor mide la humedad relativa en %.
Como ocurre con el termómetro, muchos dispositivos no lo incluyen, pero Android sí soporta el sensor.   

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
    private var humiditySensor: Sensor? = null

    private lateinit var humidityText: TextView

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        humidityText = findViewById(R.id.humidityText)

        sensorManager = getSystemService(Context.SENSOR_SERVICE) as SensorManager

        // Obtener sensor de humedad relativa
        humiditySensor = sensorManager.getDefaultSensor(Sensor.TYPE_RELATIVE_HUMIDITY)

        if (humiditySensor == null) {
            humidityText.text = "No hay sensor de humedad disponible en este dispositivo."
        }
    }

    override fun onResume() {
        super.onResume()
        humiditySensor?.also {
            sensorManager.registerListener(this, it, SensorManager.SENSOR_DELAY_NORMAL)
        }
    }

    override fun onPause() {
        super.onPause()
        sensorManager.unregisterListener(this)
    }

    override fun onSensorChanged(event: SensorEvent?) {
        if (event?.sensor?.type == Sensor.TYPE_RELATIVE_HUMIDITY) {
            val humidity = event.values[0]  // en %
            humidityText.text = "Humedad: $humidity %"
        }
    }

    override fun onAccuracyChanged(sensor: Sensor?, accuracy: Int) {
        // No usado en este ejemplo
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
        android:id="@+id/humidityText"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Humedad:"
        android:textSize="24sp" />

</LinearLayout>

```
