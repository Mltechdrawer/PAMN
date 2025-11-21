# Manejo de eventos táctiles

En Android no existe un *sensor de pantalla táctil* como tal dentro de SensorManager. La pantalla táctil se gestiona mediante eventos de entrada (MotionEvent), por lo que el equivalente funcional es manejar los toques desde una vista o actividad.

## Ambos en el módulo de la aplicación

## Ejemplo android (MainActivity.kt)

```kotlin

import android.annotation.SuppressLint
import android.os.Bundle
import android.view.MotionEvent
import android.widget.TextView
import androidx.appcompat.app.AppCompatActivity

class MainActivity : AppCompatActivity() {

    private lateinit var touchInfo: TextView

    @SuppressLint("ClickableViewAccessibility")
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        touchInfo = findViewById(R.id.touchInfo)

        // Detectar eventos táctiles en toda la vista principal
        val mainView = findViewById<androidx.constraintlayout.widget.ConstraintLayout>(R.id.mainView)

        mainView.setOnTouchListener { _, event ->
            when (event.action) {
                MotionEvent.ACTION_DOWN -> {
                    touchInfo.text = "Pulsación iniciada en: x=${event.x}, y=${event.y}"
                }
                MotionEvent.ACTION_MOVE -> {
                    touchInfo.text = "Moviendo: x=${event.x}, y=${event.y}"
                }
                MotionEvent.ACTION_UP -> {
                    touchInfo.text = "Pulsación levantada"
                }
            }
            true
        }
    }
}

```
## XML sencillo (activity_main.xml)

```xml

<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/mainView"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:padding="20dp">

    <TextView
        android:id="@+id/touchInfo"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Toca la pantalla para comenzar"
        android:textSize="20sp"
        android:layout_marginTop="40dp"
        android:layout_marginStart="20dp" />

</androidx.constraintlayout.widget.ConstraintLayout>

```
