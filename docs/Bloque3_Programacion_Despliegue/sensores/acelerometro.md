# Acelerómetro

Lee los valores de aceleración en los ejes X, Y, Z y los muestra en pantalla.  

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
import kotlin.math.sqrt

class MainActivity : AppCompatActivity(), SensorEventListener {

    private lateinit var sensorManager: SensorManager
    private var accelerometer: Sensor? = null

    private lateinit var accelTextX: TextView
    private lateinit var accelTextY: TextView
    private lateinit var accelTextZ: TextView
    private lateinit var accelMagnitude: TextView

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        accelTextX = findViewById(R.id.accelX)
        accelTextY = findViewById(R.id.accelY)
        accelTextZ = findViewById(R.id.accelZ)
        accelMagnitude = findViewById(R.id.accelMag)

        sensorManager = getSystemService(Context.SENSOR_SERVICE) as SensorManager

        // Obtener el acelerómetro
        accelerometer = sensorManager.getDefaultSensor(Sensor.TYPE_ACCELEROMETER)

        if (accelerometer == null) {
            accelTextX.text = "No hay acelerómetro disponible en este dispositivo."
        }
    }

    override fun onResume() {
        super.onResume()
        accelerometer?.also {
            sensorManager.registerListener(this, it, SensorManager.SENSOR_DELAY_NORMAL)
        }
    }

    override fun onPause() {
        super.onPause()
        sensorManager.unregisterListener(this)
    }

    override fun onSensorChanged(event: SensorEvent?) {
        if (event?.sensor?.type == Sensor.TYPE_ACCELEROMETER) {
            val x = event.values[0]
            val y = event.values[1]
            val z = event.values[2]

            accelTextX.text = "X: $x m/s²"
            accelTextY.text = "Y: $y m/s²"
            accelTextZ.text = "Z: $z m/s²"

            val mag = sqrt(x * x + y * y + z * z)
            accelMagnitude.text = "Módulo: %.2f m/s²".format(mag)
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
        android:id="@+id/accelX"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="X:"
        android:textSize="18sp"
        android:layout_marginBottom="8dp" />

    <TextView
        android:id="@+id/accelY"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Y:"
        android:textSize="18sp"
        android:layout_marginBottom="8dp" />

    <TextView
        android:id="@+id/accelZ"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Z:"
        android:textSize="18sp"
        android:layout_marginBottom="16dp" />

    <TextView
        android:id="@+id/accelMag"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Módulo:"
        android:textSize="20sp" />
</LinearLayout>

```

[Brújula](brujula.md "Brújula")

