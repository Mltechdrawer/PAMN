# Autenticación con huella dactilar (BiometricPrompt)

Este ejemplo muestra un botón que, al pulsarlo, abre el diálogo del sistema para autenticar con huella (o métodos biométricos disponibles).  

## Ambos en el módulo de la aplicación

## Ejemplo android (MainActivity.kt)

```kotlin

import android.os.Bundle
import android.widget.Button
import android.widget.TextView
import androidx.appcompat.app.AppCompatActivity
import androidx.biometric.BiometricManager
import androidx.biometric.BiometricPrompt
import androidx.core.content.ContextCompat

class MainActivity : AppCompatActivity() {

    private lateinit var statusText: TextView
    private lateinit var authButton: Button

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        statusText = findViewById(R.id.statusText)
        authButton = findViewById(R.id.authButton)

        // Comprobamos si hay biometría disponible
        val biometricManager = BiometricManager.from(this)
        when (biometricManager.canAuthenticate(BiometricManager.Authenticators.BIOMETRIC_STRONG)) {
            BiometricManager.BIOMETRIC_SUCCESS ->
                statusText.text = "El dispositivo está listo para usar huella."
            BiometricManager.BIOMETRIC_ERROR_NO_HARDWARE ->
                statusText.text = "El dispositivo no tiene sensor de huellas."
            BiometricManager.BIOMETRIC_ERROR_HW_UNAVAILABLE ->
                statusText.text = "El sensor de huellas no está disponible."
            BiometricManager.BIOMETRIC_ERROR_NONE_ENROLLED ->
                statusText.text = "No hay huellas registradas."
        }

        authButton.setOnClickListener {
            authenticate()
        }
    }

    private fun authenticate() {
        val executor = ContextCompat.getMainExecutor(this)

        val promptInfo = BiometricPrompt.PromptInfo.Builder()
            .setTitle("Autenticación biométrica")
            .setSubtitle("Usa tu huella dactilar")
            .setNegativeButtonText("Cancelar")
            .build()

        val biometricPrompt = BiometricPrompt(this, executor,
            object : BiometricPrompt.AuthenticationCallback() {

                override fun onAuthenticationSucceeded(
                    result: BiometricPrompt.AuthenticationResult
                ) {
                    super.onAuthenticationSucceeded(result)
                    statusText.text = "Autenticación correcta ✔️"
                }

                override fun onAuthenticationFailed() {
                    super.onAuthenticationFailed()
                    statusText.text = "Huella no reconocida"
                }

                override fun onAuthenticationError(errorCode: Int, errString: CharSequence) {
                    super.onAuthenticationError(errorCode, errString)
                    statusText.text = "Error: $errString"
                }
            })

        biometricPrompt.authenticate(promptInfo)
    }
}

```
## XML sencillo (activity_main.xml)

```xml

<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:gravity="center"
    android:padding="24dp"
    android:orientation="vertical">

    <TextView
        android:id="@+id/statusText"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Esperando autenticación..."
        android:textSize="20sp"
        android:layout_marginBottom="20dp"/>

    <Button
        android:id="@+id/authButton"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Autenticar con huella"/>
</LinearLayout>

```

## En build.gradle (módulo app):

```gradle

implementation "androidx.biometric:biometric:1.2.0"

```