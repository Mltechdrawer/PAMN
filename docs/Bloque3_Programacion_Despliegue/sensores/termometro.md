# Termómetro

Este sensor mide la temperatura ambiental en ºC. Debemos tener en cuenta que muchos dispositivos móviles no incluyen este sensor.   

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
    private var thermometer: Sensor? = null

    private lateinit var tempText: TextView

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        tempText = findViewById(R.id.tempText)

        sensorManager = getSystemService(Context.SENSOR_SERVICE) as SensorManager

        // Obtener el termómetro ambiental
        thermometer = sensorManager.getDefaultSensor(Sensor.TYPE_AMBIENT_TEMPERATURE)

        if (thermometer == null) {
            tempText.text = "No hay termómetro ambiental disponible en este dispositivo."
        }
    }

    override fun onResume() {
        super.onResume()
        thermometer?.also {
            sensorManager.registerListener(this, it, SensorManager.SENSOR_DELAY_NORMAL)
        }
    }

    override fun onPause() {
        super.onPause()
        sensorManager.unregisterListener(this)
    }

    override fun onSensorChanged(event: SensorEvent?) {
        if (event?.sensor?.type == Sensor.TYPE_AMBIENT_TEMPERATURE) {
            val temp = event.values[0]  // en ºC
            tempText.text = "Temperatura: $temp ºC"
        }
    }

    override fun onAccuracyChanged(sensor: Sensor?, accuracy: Int) {
        // No usado
    }
}

```

## XML sencillo (activity_main.xml)

```xml

<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:gravity="center"
    android:padding="16dp"
    android:orientation="vertical">

    <TextView
        android:id="@+id/tempText"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Temperatura:"
        android:textSize="24sp" />
</LinearLayout>

```
