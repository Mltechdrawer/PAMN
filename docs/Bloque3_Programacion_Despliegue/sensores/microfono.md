# Lectura del micrófono con AudioRecord

Android no expone el micrófono como sensor dentro de SensorManager; se accede a él mediante AudioRecord o MediaRecorder.
Este ejemplo sencillo  captura el nivel de audio en tiempo real usando AudioRecord (muy útil para actividades prácticas de sensores acústicos).  

## Ambos en el módulo de la aplicación

## Ejemplo android (MainActivity.kt)

```kotlin

import android.Manifest
import android.content.pm.PackageManager
import android.media.AudioFormat
import android.media.AudioRecord
import android.media.MediaRecorder
import android.os.Bundle
import android.widget.TextView
import androidx.appcompat.app.AppCompatActivity
import androidx.core.app.ActivityCompat
import androidx.core.content.ContextCompat
import kotlin.concurrent.thread
import kotlin.math.log10
import kotlin.math.sqrt

class MainActivity : AppCompatActivity() {

    private lateinit var audioLevelText: TextView
    private var isRecording = false

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        audioLevelText = findViewById(R.id.audioLevelText)

        // Solicitar permisos de micrófono
        if (ContextCompat.checkSelfPermission(this, Manifest.permission.RECORD_AUDIO)
            != PackageManager.PERMISSION_GRANTED) {

            ActivityCompat.requestPermissions(
                this,
                arrayOf(Manifest.permission.RECORD_AUDIO),
                100
            )
        } else {
            startMicrophone()
        }
    }

    override fun onRequestPermissionsResult(
        requestCode: Int,
        permissions: Array<out String>,
        grantResults: IntArray
    ) {
        super.onRequestPermissionsResult(requestCode, permissions, grantResults)
        if (requestCode == 100 && grantResults.isNotEmpty() &&
            grantResults[0] == PackageManager.PERMISSION_GRANTED) {
            startMicrophone()
        } else {
            audioLevelText.text = "Permiso de micrófono denegado."
        }
    }

    private fun startMicrophone() {
        val sampleRate = 44100
        val bufferSize = AudioRecord.getMinBufferSize(
            sampleRate,
            AudioFormat.CHANNEL_IN_MONO,
            AudioFormat.ENCODING_PCM_16BIT
        )

        val recorder = AudioRecord(
            MediaRecorder.AudioSource.MIC,
            sampleRate,
            AudioFormat.CHANNEL_IN_MONO,
            AudioFormat.ENCODING_PCM_16BIT,
            bufferSize
        )

        val buffer = ShortArray(bufferSize)

        isRecording = true
        recorder.startRecording()

        thread {
            while (isRecording) {
                val read = recorder.read(buffer, 0, buffer.size)
                if (read > 0) {
                    val rms = sqrt(buffer.take(read).map { it * it }.average())
                    val db = 20 * log10(rms / Short.MAX_VALUE)
                    runOnUiThread {
                        audioLevelText.text = "Nivel de sonido: %.2f dB".format(db)
                    }
                }
            }
            recorder.stop()
            recorder.release()
        }
    }

    override fun onDestroy() {
        super.onDestroy()
        isRecording = false
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
        android:id="@+id/audioLevelText"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Esperando datos del micrófono..."
        android:textSize="20sp"/>
</LinearLayout>

```

## Permisos necesarios en AndroidManifest.xml

```xml

<uses-permission android:name="android.permission.RECORD_AUDIO" />
```
