# Brújula

Este ejemplo muestra el ejemplo de uso de acelerómetro+magnetómetro para mostrar el rumbo en grados y rotar una imagen como si fuera una brújula.    

## Ambos en el módulo de la aplicación

## Ejemplo android (MainActivity.kt)

```kotlin

import android.content.Context
import android.hardware.Sensor
import android.hardware.SensorEvent
import android.hardware.SensorEventListener
import android.hardware.SensorManager
import android.os.Bundle
import android.widget.ImageView
import android.widget.TextView
import androidx.appcompat.app.AppCompatActivity
import kotlin.math.toDegrees

class MainActivity : AppCompatActivity(), SensorEventListener {

    private lateinit var sensorManager: SensorManager
    private var accelerometer: Sensor? = null
    private var magnetometer: Sensor? = null

    private lateinit var azimuthText: TextView
    private lateinit var compassImage: ImageView

    private val gravity = FloatArray(3)
    private val geomagnetic = FloatArray(3)
    private var haveGravity = false
    private var haveMagnetometer = false

    private val rotationMatrix = FloatArray(9)
    private val inclinationMatrix = FloatArray(9)
    private val orientation = FloatArray(3)

    private var currentAzimuth = 0f

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        azimuthText = findViewById(R.id.azimuthText)
        compassImage = findViewById(R.id.compassImage)

        sensorManager = getSystemService(Context.SENSOR_SERVICE) as SensorManager

        accelerometer = sensorManager.getDefaultSensor(Sensor.TYPE_ACCELEROMETER)
        magnetometer = sensorManager.getDefaultSensor(Sensor.TYPE_MAGNETIC_FIELD)

        if (accelerometer == null || magnetometer == null) {
            azimuthText.text = "Este dispositivo no tiene los sensores necesarios (acelerómetro + magnetómetro)."
        }
    }

    override fun onResume() {
        super.onResume()
        accelerometer?.also {
            sensorManager.registerListener(this, it, SensorManager.SENSOR_DELAY_GAME)
        }
        magnetometer?.also {
            sensorManager.registerListener(this, it, SensorManager.SENSOR_DELAY_GAME)
        }
    }

    override fun onPause() {
        super.onPause()
        sensorManager.unregisterListener(this)
    }

    override fun onSensorChanged(event: SensorEvent?) {
        if (event == null) return

        when (event.sensor.type) {
            Sensor.TYPE_ACCELEROMETER -> {
                // Copiamos valores de acelerómetro
                System.arraycopy(event.values, 0, gravity, 0, 3)
                haveGravity = true
            }
            Sensor.TYPE_MAGNETIC_FIELD -> {
                // Copiamos valores de magnetómetro
                System.arraycopy(event.values, 0, geomagnetic, 0, 3)
                haveMagnetometer = true
            }
        }

        if (haveGravity && haveMagnetometer) {
            val success = SensorManager.getRotationMatrix(
                rotationMatrix,
                inclinationMatrix,
                gravity,
                geomagnetic
            )

            if (success) {
                SensorManager.getOrientation(rotationMatrix, orientation)
                val azimuthRad = orientation[0].toDouble()
                var azimuthDeg = toDegrees(azimuthRad).toFloat()

                // Normalizar a 0–360°
                if (azimuthDeg < 0) {
                    azimuthDeg += 360f
                }

                azimuthText.text = "Rumbo: %.1f°".format(azimuthDeg)

                // Rotamos la imagen en sentido contrario para que apunte al norte
                compassImage.rotation = -azimuthDeg

                currentAzimuth = azimuthDeg
            }
        }
    }

    override fun onAccuracyChanged(sensor: Sensor?, accuracy: Int) {
        // No lo usamos en este ejemplo
    }
}

```

## XML sencillo (activity_main.xml)

Colocamos una imagen (por ejemplo, un dibujo de una brújula) y debajo el texto con el ángulo:

```xml

<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:gravity="center"
    android:padding="16dp">

    <ImageView
        android:id="@+id/compassImage"
        android:layout_width="200dp"
        android:layout_height="200dp"
        android:src="@drawable/compass" />

    <TextView
        android:id="@+id/azimuthText"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Rumbo: --°"
        android:textSize="20sp"
        android:layout_marginTop="24dp" />

</LinearLayout>

```

Debemos incluir un recurso en res/drawable/compass.png (o similar) con la imagen de la brújula.
