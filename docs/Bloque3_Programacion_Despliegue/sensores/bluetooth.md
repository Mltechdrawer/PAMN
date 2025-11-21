# Bluetooth

Ejemplo de programa que lista dispositivos Bluetooth emparejados.  
Este ejemplo usa el patrón estándar para listar dispositivos Bluetooth, con permisos correctos para Android 12+, y funciona en cualquier actividad.  
Hay pequeñas variaciones según fabricante, pero la lógica general es completamente válida.   

## Ambos en el módulo de la aplicación

## Ejemplo android (MainActivity.kt)

```kotlin

import android.Manifest
import android.bluetooth.BluetoothAdapter
import android.bluetooth.BluetoothDevice
import android.content.Intent
import android.content.pm.PackageManager
import android.os.Build
import android.os.Bundle
import android.widget.Button
import android.widget.TextView
import androidx.appcompat.app.AppCompatActivity
import androidx.core.app.ActivityCompat
import androidx.core.content.ContextCompat

class MainActivity : AppCompatActivity() {

    private lateinit var pairedText: TextView
    private lateinit var listButton: Button

    private val REQUEST_ENABLE_BT = 1

    private val bluetoothAdapter: BluetoothAdapter? by lazy {
        BluetoothAdapter.getDefaultAdapter()
    }

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        pairedText = findViewById(R.id.pairedText)
        listButton = findViewById(R.id.listButton)

        // Pedir permisos si es necesario
        requestBluetoothPermissions()

        // Comprobar si el dispositivo soporta Bluetooth
        if (bluetoothAdapter == null) {
            pairedText.text = "Este dispositivo no soporta Bluetooth."
            return
        }

        // Activar Bluetooth si está apagado
        if (bluetoothAdapter?.isEnabled == false) {
            val enableBtIntent = Intent(BluetoothAdapter.ACTION_REQUEST_ENABLE)
            startActivityForResult(enableBtIntent, REQUEST_ENABLE_BT)
        }

        listButton.setOnClickListener {
            showPairedDevices()
        }
    }

    private fun requestBluetoothPermissions() {
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.S) {
            val permissions = arrayOf(
                Manifest.permission.BLUETOOTH_SCAN,
                Manifest.permission.BLUETOOTH_CONNECT
            )
            ActivityCompat.requestPermissions(this, permissions, 100)
        } else {
            val permission = Manifest.permission.ACCESS_FINE_LOCATION
            if (ContextCompat.checkSelfPermission(this, permission)
                != PackageManager.PERMISSION_GRANTED
            ) {
                ActivityCompat.requestPermissions(this, arrayOf(permission), 101)
            }
        }
    }

    private fun showPairedDevices() {
        val devices: Set<BluetoothDevice>? = bluetoothAdapter?.bondedDevices

        if (devices.isNullOrEmpty()) {
            pairedText.text = "No hay dispositivos emparejados."
            return
        }

        val list = StringBuilder("Dispositivos emparejados:\n\n")
        devices.forEach {
            list.append("${it.name} - ${it.address}\n")
        }

        pairedText.text = list.toString()
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
    android:padding="20dp">

    <Button
        android:id="@+id/listButton"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Listar emparejados" />

    <TextView
        android:id="@+id/pairedText"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Pulsa el botón para ver dispositivos"
        android:textSize="18sp"
        android:padding="20dp" />
</LinearLayout>

```

## Permisos necesarios en AndroidManifest.xml

- Android 12+ (API 31+)

```xml
<uses-permission android:name="android.permission.BLUETOOTH_SCAN"/>
<uses-permission android:name="android.permission.BLUETOOTH_CONNECT"/>
```

- Android 11 y anteriores

```xml

<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>
<uses-permission android:name="android.permission.BLUETOOTH"/>
<uses-permission android:name="android.permission.BLUETOOTH_ADMIN"/>

```