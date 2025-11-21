# Giroscopio

El giroscopio mide la velocidad angular en rad/s sobre los ejes X, Y, Z.    

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
    private var gyroscope: Sensor? = null

    private lateinit var gyroX: TextView
    private lateinit var gyroY: TextView
    private lateinit var gyroZ: TextView

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        gyroX = findViewById(R.id.gyroX)
        gyroY = findViewById(R.id.gyroY)
        gyroZ = findViewById(R.id.gyroZ)

        sensorManager = getSystemService(Context.SENSOR_SERVICE) as SensorManager

        // Obtener el giroscopio
        gyroscope = sensorManager.getDefaultSensor(Sensor.TYPE_GYROSCOPE)

        if (gyroscope == null) {
            gyroX.text = "No hay giroscopio disponible en este dispositivo."
        }
    }

    override fun onResume() {
        super.onResume()
        gyroscope?.also {
            sensorManager.registerListener(this, it, SensorManager.SENSOR_DELAY_NORMAL)
        }
    }

    override fun onPause() {
        super.onPause()
        sensorManager.unregisterListener(this)
    }

    override fun onSensorChanged(event: SensorEvent?) {
        if (event?.sensor?.type == Sensor.TYPE_GYROSCOPE) {
            val x = event.values[0]
            val y = event.values[1]
            val z = event.values[2]

            gyroX.text = "X: $x rad/s"
            gyroY.text = "Y: $y rad/s"
            gyroZ.text = "Z: $z rad/s"
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
    android:orientation="vertical"
    android:gravity="center"
    android:padding="16dp">

    <TextView
        android:id="@+id/gyroX"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="X:"
        android:textSize="18sp"
        android:layout_marginBottom="8dp" />

    <TextView
        android:id="@+id/gyroY"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Y:"
        android:textSize="18sp"
        android:layout_marginBottom="8dp" />

    <TextView
        android:id="@+id/gyroZ"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Z:"
        android:textSize="18sp"
        android:layout_marginBottom="8dp" />

</LinearLayout>

```

