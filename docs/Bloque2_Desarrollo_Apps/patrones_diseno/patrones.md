# Anexos de código

## Índice

- [Singleton (Creacional)](#code-singleton)
- [Factory (Creacional)](#code-factory)
- [Builder (Creacional)](#code-builder)
- [Prototype (Creacional)](#code-prototype)
- [Adapter (Estructural)](#code-adapter)
- [Decorator (Estructural)](#code-decorator)
- [Facade (Estructural)](#code-facade)
- [Observer (Comportamiento)](#code-observer)
- [Chain of Responsibility (Comportamiento)](#code-chain)
- [Command (Comportamiento)](#code-command)
- [State (Comportamiento)](#code-state)
- [Strategy (Comportamiento)](#code-strategy)

---


### <a id="code-singleton"></a>Singleton (Creacional)

```kotlin
// Definimos la clase Singleton en Kotlin
object ConfiguracionGlobal {
    var idioma: String = "Español"
    var temaOscuro: Boolean = false

    fun mostrarConfiguracion() {
        println("Idioma: $idioma, Tema Oscuro: $temaOscuro")
    }
}

// Uso del Singleton
fun main() {
    // Accedemos a la configuración global y cambiamos valores
    ConfiguracionGlobal.idioma = "Inglés"
    ConfiguracionGlobal.temaOscuro = true

    // Mostramos la configuración, reflejando los cambios
    ConfiguracionGlobal.mostrarConfiguracion()

    // Si accedemos a ConfiguracionGlobal desde otro lugar en la aplicación,
    // seguirá siendo la misma instancia y reflejará estos valores.
}
```

---


### <a id="code-factory"></a>Factory (Creacional)

```kotlin
interface MetodoPago {
    fun pagar(cantidad: Double)
}

class Tarjeta : MetodoPago {
    override fun pagar(cantidad: Double) { println("Pagando $cantidad con tarjeta") }
}

class PayPal : MetodoPago {
    override fun pagar(cantidad: Double) { println("Pagando $cantidad con PayPal") }
}

class MetodoPagoFactory {
    fun crearMetodoPago(tipo: String): MetodoPago = when(tipo) {
        "tarjeta" -> Tarjeta()
        "paypal" -> PayPal()
        else -> throw IllegalArgumentException("Método de pago no soportado")
    }
}

fun main() {
    // Uso del patrón Factory
    val factory = MetodoPagoFactory()
    val metodo = factory.crearMetodoPago("tarjeta")
    metodo.pagar(100.0)
}
```

---


### <a id="code-builder"></a>Builder (Creacional)

```kotlin
//Builder

class PerfilUsuario private constructor(val nombre: String, val foto: String?, val bio: String?) {
    companion object Builder {
        private var nombre: String = ""
        private var foto: String? = null
        private var bio: String? = null

        fun setNombre(nombre: String) = apply { this.nombre = nombre }
        fun setFoto(foto: String) = apply { this.foto = foto }
        fun setBio(bio: String) = apply { this.bio = bio }

        fun build() = PerfilUsuario(nombre, foto, bio)
    }
}

fun main() {
    // Uso del patrón Builder
    val perfil = PerfilUsuario.Builder
        .setNombre("Juan")
        .setFoto("url_foto")
        .setBio("Amante de la programación")
        .build()

    // Imprimir el perfil para verificar el resultado
    println("Nombre: ${perfil.nombre}")
    println("Foto: ${perfil.foto}")
    println("Bio: ${perfil.bio}")
}
```

---


### <a id="code-prototype"></a>Prototype (Creacional)

```kotlin
// Prototype

// Interfaz para el patrón Prototype
interface Widget {
    fun clone(): Widget
}

// Clase concreta que implementa Widget
class Boton(var color: String) : Widget {
    override fun clone(): Boton {
        return Boton(this.color)
    }

    fun mostrarInfo() {
        println("Botón con color: $color")
    }
}

// Uso del patrón Prototype
fun main() {
    val botonOriginal = Boton("Rojo")
    botonOriginal.mostrarInfo()

    // Clonamos el botón
    val botonClonado = botonOriginal.clone()
    botonClonado.color = "Azul"
    botonClonado.mostrarInfo() // Salida: Botón con color: Azul
    botonOriginal.mostrarInfo() // Salida: Botón con color: Rojo
}
```

---


### <a id="code-adapter"></a>Adapter (Estructural)

```kotlin
interface Traductor {
    fun traducir(texto: String): String
}

class GoogleTranslateAPI {
    fun translateText(text: String): String = "Texto traducido por Google"
}

class GoogleTranslateAdapter(val api: GoogleTranslateAPI) : Traductor {
    override fun traducir(texto: String): String = api.translateText(texto)
}

// Uso del patrón Adapter
val googleAPI = GoogleTranslateAPI()
val adapter = GoogleTranslateAdapter(googleAPI)
println(adapter.traducir("Hola"))
```

---


### <a id="code-decorator"></a>Decorator (Estructural)

```kotlin
//Decorador
//
// Interfaz de Componente
interface Mensaje {
    fun enviar(): String
}

// Clase concreta que implementa el Componente base
class MensajeBasico : Mensaje {
    override fun enviar(): String = "Mensaje enviado"
}

// Clase Decorador base
abstract class MensajeDecorador(private val mensaje: Mensaje) : Mensaje {
    override fun enviar(): String = mensaje.enviar()
}

// Decorador concreto que añade encriptación
class MensajeEncriptado(mensaje: Mensaje) : MensajeDecorador(mensaje) {
    override fun enviar(): String = "🔒 ${super.enviar()} (Encriptado)"
}

// Decorador concreto que añade compresión
class MensajeComprimido(mensaje: Mensaje) : MensajeDecorador(mensaje) {
    override fun enviar(): String = "📦 ${super.enviar()} (Comprimido)"
}

// Uso del patrón Decorator
fun main() {
    // Mensaje básico
    val mensaje = MensajeBasico()
    println(mensaje.enviar()) // Salida: "Mensaje enviado"

    // Mensaje encriptado
    val mensajeEncriptado = MensajeEncriptado(mensaje)
    println(mensajeEncriptado.enviar()) // Salida: "🔒 Mensaje enviado (Encriptado)"

    // Mensaje encriptado y comprimido
    val mensajeEncriptadoYComprimido = MensajeComprimido(mensajeEncriptado)
    println(mensajeEncriptadoYComprimido.enviar()) // Salida: "📦 🔒 Mensaje enviado (Encriptado) (Comprimido)"
}
```

---


### <a id="code-facade"></a>Facade (Estructural)

```kotlin
// Facade

// Sistema complejo
class SistemaAudio {
    fun reproducirAudio() = println("Reproduciendo audio")
}

class SistemaDescarga {
    fun descargarCancion(cancion: String) = println("Descargando $cancion")
}

class SistemaRecomendaciones {
    fun obtenerRecomendaciones() = println("Mostrando recomendaciones")
}

// Clase Facade que simplifica el uso del sistema
class FacadeMusica {
    private val audio = SistemaAudio()
    private val descarga = SistemaDescarga()
    private val recomendaciones = SistemaRecomendaciones()

    fun reproducir() {
        audio.reproducirAudio()
    }

    fun descargar(cancion: String) {
        descarga.descargarCancion(cancion)
    }

    fun mostrarRecomendaciones() {
        recomendaciones.obtenerRecomendaciones()
    }
}

// Uso del patrón Facade
fun main() {
    val appMusica = FacadeMusica()
    appMusica.reproducir()
    appMusica.descargar("Canción Favorita")
    appMusica.mostrarRecomendaciones()
}
```

---


### <a id="code-observer"></a>Observer (Comportamiento)

```kotlin
// Interfaz que representa un Observador
interface Observador {
    fun actualizar(noticia: String)
}
// Clase Observado (Publisher)
class CanalDeNoticias {
    private val observadores = mutableListOf<Observador>()
    fun agregarObservador(observador: Observador) {
        observadores.add(observador)
    }
    fun eliminarObservador(observador: Observador) {
        observadores.remove(observador)
    }
    fun nuevaNoticia(noticia: String) {
        println("Nueva noticia publicada: $noticia")
        notificarObservadores(noticia)
    }
    private fun notificarObservadores(noticia: String) {
        for (observador in observadores) {
            observador.actualizar(noticia)
        }
    }
}
// Clase Observadora concreta
class UsuarioApp(val nombre: String) : Observador {
    override fun actualizar(noticia: String) {
        println("$nombre recibió la noticia: $noticia")
    }
}
// Uso del patrón Observer
fun main() {
    val canalDeNoticias = CanalDeNoticias()
    val usuario1 = UsuarioApp("Juan")
    val usuario2 = UsuarioApp("Ana")
    canalDeNoticias.agregarObservador(usuario1)
    canalDeNoticias.agregarObservador(usuario2)
    // Publicamos una nueva noticia, todos los observadores reciben la actualización
    canalDeNoticias.nuevaNoticia("¡Actualización importante en el mercado!")
}
```

---


### <a id="code-chain"></a>Chain of Responsibility (Comportamiento)

```kotlin
// Clase abstracta Handler, representa cada paso de la cadena
abstract class AuthHandler {
    var nextHandler: AuthHandler? = null

    fun setNext(handler: AuthHandler): AuthHandler {
        this.nextHandler = handler
        return handler
    }

    abstract fun handle(request: AuthRequest): Boolean
}

// Clase para representar la solicitud de autenticación
data class AuthRequest(val username: String, val password: String, val isAccountActive: Boolean, val has2FA: Boolean)

// Verificar credenciales
class CredentialsHandler : AuthHandler() {
    override fun handle(request: AuthRequest): Boolean {
        if (request.username == "user" && request.password == "password") {
            println("Credentials verified.")
            return nextHandler?.handle(request) ?: true
        } else {
            println("Invalid credentials.")
            return false
        }
    }
}

// Verificar el estado de la cuenta
class AccountStatusHandler : AuthHandler() {
    override fun handle(request: AuthRequest): Boolean {
        if (request.isAccountActive) {
            println("Account is active.")
            return nextHandler?.handle(request) ?: true
        } else {
            println("Account is inactive.")
            return false
        }
    }
}

// Verificar autenticación de dos factores (2FA)
class TwoFactorAuthHandler : AuthHandler() {
    override fun handle(request: AuthRequest): Boolean {
        if (request.has2FA) {
            println("2FA is enabled and verified.")
            return nextHandler?.handle(request) ?: true
        } else {
            println("2FA is not enabled.")
            return false
        }
    }
}

// Uso del patrón Chain of Responsibility en la aplicación de autenticación
fun main() {
    // Crear y configurar la cadena de responsabilidad
    val authChain = CredentialsHandler()
    authChain.setNext(AccountStatusHandler()).setNext(TwoFactorAuthHandler())

    // Crear solicitud de autenticación
    val authRequest = AuthRequest("user", "password", isAccountActive = true, has2FA = true)
    
    // Procesar la solicitud a través de la cadena
    val isAuthenticated = authChain.handle(authRequest)
    println("Authentication success: $isAuthenticated")
}
```

---


### <a id="code-command"></a>Command (Comportamiento)

```kotlin
// Interfaz Command
interface Comando {
    fun ejecutar()
}

// Comandos concretos
class EncenderLuz : Comando {
    override fun ejecutar() = println("La luz está encendida")
}

class ApagarLuz : Comando {
    override fun ejecutar() = println("La luz está apagada")
}

// Invocador
class ControlRemoto {
    private lateinit var comando: Comando

    fun setComando(comando: Comando) {
        this.comando = comando
    }

    fun presionarBoton() {
        comando.ejecutar()
    }
}

// Uso del patrón Command
fun main() {
    val control = ControlRemoto()
    
    val encender = EncenderLuz()
    val apagar = ApagarLuz()

    control.setComando(encender)
    control.presionarBoton()  // La luz está encendida

    control.setComando(apagar)
    control.presionarBoton()  // La luz está apagada
}
```

---


### <a id="code-state"></a>State (Comportamiento)

```kotlin
// Interfaz de Estado
interface Estado {
    fun presionarBotonPlay()
}
// Estado concreto: Reproduciendo
class Reproduciendo : Estado {
    override fun presionarBotonPlay() {
        println("El reproductor está en reproducción.")
    }
}
// Estado concreto: Pausado
class Pausado : Estado {
    override fun presionarBotonPlay() {
        println("El reproductor está en pausa.")
    }
}
// Contexto que mantiene el estado actual
class Reproductor {
    private var estado: Estado = Pausado()

    fun setEstado(estado: Estado) {
        this.estado = estado
    }

    fun presionarBotonPlay() {
        estado.presionarBotonPlay()
    }
}
// Uso del patrón State
fun main() {
    val reproductor = Reproductor()

    // Cambia el estado a Reproduciendo
    reproductor.setEstado(Reproduciendo())
    reproductor.presionarBotonPlay() // Salida: "El reproductor está en reproducción."

    // Cambia el estado a Pausado
    reproductor.setEstado(Pausado())
    reproductor.presionarBotonPlay() // Salida: "El reproductor está en pausa."
}
```

---


### <a id="code-strategy"></a>Strategy (Comportamiento)

```kotlin
// Definimos una interfaz para la estrategia de compresión
interface CompressionStrategy {
    fun compress(fileName: String)
}

// Implementación de la compresión JPEG
class JPEGCompression : CompressionStrategy {
    override fun compress(fileName: String) {
        println("Compressing $fileName as JPEG")
    }
}

// Implementación de la compresión PNG
class PNGCompression : CompressionStrategy {
    override fun compress(fileName: String) {
        println("Compressing $fileName as PNG")
    }
}

// Contexto de la aplicación de fotos
class PhotoApp {
    private var strategy: CompressionStrategy? = null

    fun setCompressionStrategy(strategy: CompressionStrategy) {
        this.strategy = strategy
    }

    fun savePhoto(fileName: String) {
        strategy?.compress(fileName) ?: println("No compression strategy selected")
    }
}

// Uso del patrón Strategy en la aplicación de fotos
fun main() {
    val photoApp = PhotoApp()
    
    // Seleccionar y aplicar la compresión JPEG
    photoApp.setCompressionStrategy(JPEGCompression())
    photoApp.savePhoto("photo1.jpg")
    
    // Cambiar a compresión PNG
    photoApp.setCompressionStrategy(PNGCompression())
    photoApp.savePhoto("photo2.png")
}
```

---
