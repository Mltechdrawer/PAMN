# nfc

Ejemplo de Lectura de NFC (NDEF Tag / Tag ID).   

<details>
<summary>NDEF NFC Data Exchange Format</summary>
<p>Los datos escritos en el tag</p>
<p>Es un formato estándar para guardar datos reales dentro de una etiqueta NFC.</p>
<p>Ejemplos de datos en NDEF:</p>
<ul>
<li>Un texto ("Hola Marilola")</li>
<li>Una URL</li>
<li>Un contacto</li>
<li>Un comando</li>
<li>Un número de teléfono</li>
<li>Datos personalizados de tu app</li>
</ul>
</details>

<details>
<summary>Tag Id</summary>
<p>Número único del hardware. Es el identificador único que tiene una etiqueta NFC.</p>
<ul>
<li>Lo fabrica el fabricante del chip.</li>
<li>Es como una matrícula.</li>
<li>Cada vez que acercas un tag NFC, Android puede leer ese ID</li>
<li>Se suele mostrar en hexadecimal, ej.: 04A224F15C2980</li>
<p>Se usa para:</p>
<li>Identificar un objeto</li>
<li>Control de acceso</li>
<li>Sistemas de entrada</li>
<li>Autenticación básica (aunque NO es seguro por sí solo)</li>
</ul>
</details>

## Ambos en el módulo de la aplicación

## Ejemplo android (MainActivity.kt)

```kotlin

import android.app.PendingIntent
import android.content.Intent
import android.content.IntentFilter
import android.nfc.NfcAdapter
import android.nfc.Tag
import android.os.Bundle
import android.widget.TextView
import androidx.appcompat.app.AppCompatActivity

class MainActivity : AppCompatActivity() {

    private var nfcAdapter: NfcAdapter? = null
    private lateinit var infoText: TextView

    private lateinit var pendingIntent: PendingIntent
    private lateinit var filters: Array<IntentFilter>

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        infoText = findViewById(R.id.infoText)

        // Comprobar si el dispositivo tiene NFC
        nfcAdapter = NfcAdapter.getDefaultAdapter(this)
        if (nfcAdapter == null) {
            infoText.text = "NFC no disponible en este dispositivo."
            return
        }

        // Intent para capturar eventos NFC cuando la app está en foreground
        pendingIntent = PendingIntent.getActivity(
            this, 0,
            Intent(this, javaClass).addFlags(Intent.FLAG_ACTIVITY_SINGLE_TOP),
            PendingIntent.FLAG_MUTABLE
        )

        // Filtro para cualquier tipo de tag
        val tagDetected = IntentFilter(NfcAdapter.ACTION_TAG_DISCOVERED)
        filters = arrayOf(tagDetected)
    }

    override fun onResume() {
        super.onResume()
        nfcAdapter?.enableForegroundDispatch(this, pendingIntent, filters, null)
    }

    override fun onPause() {
        super.onPause()
        nfcAdapter?.disableForegroundDispatch(this)
    }

    override fun onNewIntent(intent: Intent?) {
        super.onNewIntent(intent)

        if (intent?.action == NfcAdapter.ACTION_TAG_DISCOVERED) {
            val tag: Tag? = intent.getParcelableExtra(NfcAdapter.EXTRA_TAG)
            val id = tag?.id

            if (id != null) {
                val hexId = id.joinToString(separator = "") { "%02X".format(it) }
                infoText.text = "Tag NFC detectado\nID: $hexId"
            } else {
                infoText.text = "Tag detectado pero sin ID."
            }
        }
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
        android:id="@+id/infoText"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Acerca un tag NFC..."
        android:textSize="22sp" />
</LinearLayout>

```

## Permisos y soporte NFC en AndroidManifest.xml

```xml
<uses-permission android:name="android.permission.NFC" />
<uses-feature android:name="android.hardware.nfc" android:required="true" />
```

- Adicionalmente debemos incluir intent-filters si queremos abrir la app desde una etiqueta:

```xml

<intent-filter>
    <action android:name="android.nfc.action.TAG_DISCOVERED"/>
    <category android:name="android.intent.category.DEFAULT"/>
</intent-filter>

```