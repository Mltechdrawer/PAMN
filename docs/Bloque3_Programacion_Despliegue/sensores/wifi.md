# Wifi

Ejemplo de programa que muestra el SSID de la Wi-Fi actual.  
El patrón general (usar ConnectivityManager + WifiManager + permisos de localización) es el recomendado y está alineado con la evolución de Android. Lo que sí cambia ligeramente entre versiones son las restricciones exactas para obtener el SSID (especialmente en Android 10+). Este es un ejemplo docente.   

## Ambos en el módulo de la aplicación

## Ejemplo android (MainActivity.kt)

```kotlin

import android.Manifest
import android.content.Context
import android.content.pm.PackageManager
import android.net.ConnectivityManager
import android.net.NetworkCapabilities
import android.net.wifi.WifiManager
import android.os.Build
import android.os.Bundle
import android.widget.Button
import android.widget.TextView
import androidx.appcompat.app.AppCompatActivity
import androidx.core.app.ActivityCompat
import androidx.core.content.ContextCompat

class MainActivity : AppCompatActivity() {

    private lateinit var wifiInfoText: TextView
    private lateinit var refreshButton: Button

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        wifiInfoText = findViewById(R.id.wifiInfoText)
        refreshButton = findViewById(R.id.refreshButton)

        // Pedir permisos de localización si hace falta (necesarios para obtener el SSID en muchas versiones)
        requestLocationPermissionIfNeeded()

        refreshButton.setOnClickListener {
            showWifiInfo()
        }

        // Mostrar info inicial
        showWifiInfo()
    }

    private fun requestLocationPermissionIfNeeded() {
        val permission = Manifest.permission.ACCESS_FINE_LOCATION
        if (ContextCompat.checkSelfPermission(this, permission)
            != PackageManager.PERMISSION_GRANTED
        ) {
            ActivityCompat.requestPermissions(this, arrayOf(permission), 100)
        }
    }

    private fun showWifiInfo() {
        val connectivityManager =
            getSystemService(Context.CONNECTIVITY_SERVICE) as ConnectivityManager

        val activeNetwork = connectivityManager.activeNetwork
        if (activeNetwork == null) {
            wifiInfoText.text = "No hay ninguna red activa."
            return
        }

        val capabilities = connectivityManager.getNetworkCapabilities(activeNetwork)
        if (capabilities == null ||
            !capabilities.hasTransport(NetworkCapabilities.TRANSPORT_WIFI)
        ) {
            wifiInfoText.text = "No estás conectado por Wi-Fi."
            return
        }

        // A partir de aquí sabemos que estamos en Wi-Fi
        val wifiManager = applicationContext.getSystemService(Context.WIFI_SERVICE) as WifiManager

        // El SSID puede venir entre comillas, lo limpiamos un poco
        val ssid = if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.Q) {
            // En Android 10+ hay más restricciones, pero si los permisos y condiciones se cumplen,
            // aún se puede obtener el SSID así:
            wifiManager.connectionInfo.ssid
        } else {
            wifiManager.connectionInfo.ssid
        }

        wifiInfoText.text = "Conectado a Wi-Fi\nSSID: $ssid"
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

    <Button
        android:id="@+id/refreshButton"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Actualizar Wi-Fi" />

    <TextView
        android:id="@+id/wifiInfoText"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Sin datos de Wi-Fi aún"
        android:textSize="18sp"
        android:padding="16dp" />
</LinearLayout>

```

## Permisos necesarios en AndroidManifest.xml

Para poder obtener el SSID en muchas versiones de Android, hace falta permiso de localización:

```xml
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
```

- En versiones antiguas también se usaba:

```xml

<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />

```