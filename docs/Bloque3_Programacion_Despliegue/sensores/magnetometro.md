# Magnetómetro

Este sensor devuelve la intensidad del campo magnético en microteslas (µT) en los ejes X, Y, Z.    

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
    private var magnetometer: Sensor? = null

    private lateinit var magX: TextView
    private lateinit var magY: TextView
    private lateinit var magZ: TextView

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        magX = findViewById(R.id.magX)
        magY = findViewById(R.id.magY)
        magZ = findViewById(R.id.magZ)

        sensorManager = getSystemService(Context.SENSOR_SERVICE) as SensorManager

        // Obtener el magnetómetro
        magnetometer = sensorManager.getDefaultSensor(Sensor.TYPE_MAGNETIC_FIELD)

        if (magnetometer == null) {
            magX.text = "No hay magnetómetro disponible en este dispositivo."
        }
    }

    override fun onResume() {
        super.onResume()
        magnetometer?.also {
            sensorManager.registerListener(this, it, SensorManager.SENSOR_DELAY_NORMAL)
        }
    }

    override fun onPause() {
        super.onPause()
        sensorManager.unregisterListener(this)
    }

    override fun onSensorChanged(event: SensorEvent?) {
        if (event?.sensor?.type == Sensor.TYPE_MAGNETIC_FIELD) {
            val x = event.values[0]
            val y = event.values[1]
            val z = event.values[2]

            magX.text = "X: $x µT"
            magY.text = "Y: $y µT"
            magZ.text = "Z: $z µT"
        }
    }

    override fun onAccuracyChanged(sensor: Sensor?, accuracy: Int) {
        // No lo usamos aquí
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
        android:id="@+id/magX"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="X:"
        android:textSize="18sp"
        android:layout_marginBottom="8dp" />

    <TextView
        android:id="@+id/magY"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Y:"
        android:textSize="18sp"
        android:layout_marginBottom="8dp" />

    <TextView
        android:id="@+id/magZ"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Z:"
        android:textSize="18sp"
        android:layout_marginBottom="8dp" />
</LinearLayout>

```

[Brújula](brujula.md "Brújula")

