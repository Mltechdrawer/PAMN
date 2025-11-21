# RadioFM

El sensor o API de Radio FM es un caso especial en Android porque:
Android NO incluye una API oficial para Radio FM.
Esto significa que no existe un SensorManager.TYPE_FM_RADIO, ni una clase estándar como en NFC, Bluetooth o WiFi.

La radio FM depende totalmente del fabricante (Samsung, LG, Motorola, Sony…), y cada uno expone APIs privadas o librerías nativas diferentes, o directamente no expone nada.  

De este ejemplo docente, debemos destacar que solo funciona en dispositivos que expongan la API FM del fabricante, normalmente Qualcomm FM o Samsung FM Service.  
En otros, simplemente no funcionará.  

## Ambos en el módulo de la aplicación

## Ejemplo android (MainActivity.kt)

```kotlin

import android.content.ComponentName
import android.content.Intent
import android.content.ServiceConnection
import android.os.Bundle
import android.os.IBinder
import android.widget.Button
import android.widget.TextView
import androidx.appcompat.app.AppCompatActivity

class MainActivity : AppCompatActivity() {

    private var fmService: IFmRadioService? = null
    private lateinit var statusText: TextView
    private lateinit var toggleButton: Button

    private var isPlaying = false

    private val connection = object : ServiceConnection {
        override fun onServiceConnected(name: ComponentName?, service: IBinder?) {
            fmService = IFmRadioService.Stub.asInterface(service)
            statusText.text = "Servicio de Radio FM conectado"
        }

        override fun onServiceDisconnected(name: ComponentName?) {
            fmService = null
            statusText.text = "Servicio desconectado"
        }
    }

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        statusText = findViewById(R.id.statusText)
        toggleButton = findViewById(R.id.toggleButton)

        // Intent típico para dispositivos Qualcomm
        val intent = Intent("com.qualcomm.fmradio.FM_RADIO_SERVICE")
        intent.setPackage("com.qualcomm.fmradio")

        bindService(intent, connection, BIND_AUTO_CREATE)

        toggleButton.setOnClickListener {
            if (fmService != null) {
                if (!isPlaying) {
                    fmService?.fmOn()
                    fmService?.tune(101900) // Ejemplo: 101.9 MHz
                    statusText.text = "Radio FM encendida (101.9 MHz)"
                } else {
                    fmService?.fmOff()
                    statusText.text = "Radio FM apagada"
                }
                isPlaying = !isPlaying
            } else {
                statusText.text = "El dispositivo no soporta Radio FM o API no disponible."
            }
        }
    }

    override fun onDestroy() {
        super.onDestroy()
        try {
            unbindService(connection)
        } catch (_: Exception) {}
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
    android:padding="24dp">

    <TextView
        android:id="@+id/statusText"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Listo para Radio FM"
        android:textSize="20sp"
        android:padding="16dp" />

    <Button
        android:id="@+id/toggleButton"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Encender / Apagar Radio" />
</LinearLayout>

```

## Aspectos a destacar:

No existe API oficial FM en Android.  
Se puede debatir sobre:    

- APIs privadas
- Fragmentación del ecosistema Android
- Librerías HAL de fabricantes
- Hardware accesible solo por aplicaciones del sistema

Cada fabricante usa su propia API  
Ejemplos típicos:  

- Qualcomm FM Radio → *com.qualcomm.fmradio*
- Samsung FM Service → *com.sec.android.app.fm*
- Motorola FM → *com.motorola.fmradio*

Muchas marcas han eliminado la radio FM

En este aspecto podemos debatir sobre la arquitectura de hardware y decisiones de mercado.

```