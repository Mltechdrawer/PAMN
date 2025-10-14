# Anexos de código
> Fragmentos de ejemplo **Kotlin** enlazables desde `creacionales.md`, `estructurales.md` y `comportamiento.md`.  
> Cada bloque incluye **comentarios** explicativos línea a línea para facilitar su lectura docente.

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
// Un Singleton en Kotlin se define naturalmente con 'object'.
// Garantiza una única instancia y acceso global en toda la app.
object ConfiguracionGlobal {
    // Estado compartido de la aplicación (ej.: preferencias, flags de características)
    var idioma: String = "Español"
    var temaOscuro: Boolean = false

    // Función de utilidad para depurar o mostrar el estado actual
    fun mostrarConfiguracion() {
        println("Idioma: $idioma, Tema Oscuro: $temaOscuro")
    }
}

fun main() {
    // Acceso global a la misma instancia (no se usa 'new')
    ConfiguracionGlobal.idioma = "Inglés"
    ConfiguracionGlobal.temaOscuro = true
    ConfiguracionGlobal.mostrarConfiguracion()

    // En cualquier otra parte de la app, sigue siendo la misma instancia.
}
```

---

### <a id="code-factory"></a>Factory (Creacional)
```kotlin
// Interfaz común para los productos creados por la fábrica.
// La UI/cliente depende de la interfaz, no de clases concretas.
interface MetodoPago {
    fun pagar(cantidad: Double)
}

// Productos concretos: distintas implementaciones de la interfaz
class Tarjeta : MetodoPago {
    override fun pagar(cantidad: Double) { println("Pagando $cantidad con tarjeta") }
}

class PayPal : MetodoPago {
    override fun pagar(cantidad: Double) { println("Pagando $cantidad con PayPal") }
}

// La Factory encapsula la lógica de creación y selección de implementación.
class MetodoPagoFactory {
    fun crearMetodoPago(tipo: String): MetodoPago = when (tipo.lowercase()) {
        "tarjeta" -> Tarjeta()
        "paypal"  -> PayPal()
        else      -> throw IllegalArgumentException("Método de pago no soportado: $tipo")
    }
}

fun main() {
    val factory = MetodoPagoFactory()
    val metodo = factory.crearMetodoPago("tarjeta")
    metodo.pagar(100.0) // El cliente no conoce la clase concreta.
}
```

---

### <a id="code-builder"></a>Builder (Creacional)
```kotlin
// Ejemplo de Builder para construir objetos con muchos opcionales.
// Aquí usamos un 'companion object' como builder fluido por brevedad docente.
// Nota: en producción, un 'class Builder' anidado puede ser más seguro (thread-safe).
class PerfilUsuario private constructor(
    val nombre: String,
    val foto: String?,
    val bio: String?
) {
    companion object Builder {
        // Estado interno del builder (mutable solo durante la construcción)
        private var nombre: String = ""
        private var foto: String? = null
        private var bio: String? = null

        // Métodos 'setter' encadenables (fluentes)
        fun setNombre(nombre: String) = apply { this.nombre = nombre }
        fun setFoto(foto: String)     = apply { this.foto = foto }
        fun setBio(bio: String)       = apply { this.bio = bio }

        // Construye la instancia inmutable final
        fun build(): PerfilUsuario = PerfilUsuario(nombre, foto, bio)
    }
}

fun main() {
    // Construcción paso a paso y legible
    val perfil = PerfilUsuario.Builder
        .setNombre("Juan")
        .setFoto("https://example.com/foto.jpg")
        .setBio("Amante de la programación")
        .build()

    println("Nombre: ${perfil.nombre}")
    println("Foto: ${perfil.foto}")
    println("Bio: ${perfil.bio}")
}
```

---

### <a id="code-prototype"></a>Prototype (Creacional)
```kotlin
// Prototype: clonar objetos sin acoplarse a su clase concreta.
// Útil cuando la creación es costosa y queremos duplicar estado.

// Interfaz con operación de clonación
interface Widget {
    fun clone(): Widget
}

// Implementación concreta clonable
class Boton(var color: String) : Widget {
    // Clonado superficial suficiente para el ejemplo
    override fun clone(): Boton = Boton(this.color)

    fun mostrarInfo() { println("Botón con color: $color") }
}

fun main() {
    val original = Boton("Rojo")
    original.mostrarInfo()

    // Clon: parte del mismo estado inicial, pero evoluciona de forma independiente
    val clon = original.clone()
    clon.color = "Azul"
    clon.mostrarInfo()
    original.mostrarInfo() // El original no se ve afectado por cambios en el clon
}
```

---

### <a id="code-adapter"></a>Adapter (Estructural)
```kotlin
// Adapter: hace compatibles dos interfaces que no coinciden.
// El cliente usa la interfaz objetivo 'Traductor',
// mientras el adaptador envuelve la API existente (GoogleTranslateAPI).

// Interfaz objetivo esperada por el cliente
interface Traductor {
    fun traducir(texto: String): String
}

// API existente con interfaz incompatible
class GoogleTranslateAPI {
    fun translateText(text: String): String = "Texto traducido por Google: $text"
}

// Adaptador que convierte llamadas de Traductor a la API real
class GoogleTranslateAdapter(private val api: GoogleTranslateAPI) : Traductor {
    override fun traducir(texto: String): String = api.translateText(texto)
}

fun main() {
    val api = GoogleTranslateAPI()
    val adapter: Traductor = GoogleTranslateAdapter(api)

    println(adapter.traducir("Hola"))
}
```

---

### <a id="code-decorator"></a>Decorator (Estructural)
```kotlin
// Decorator: añade responsabilidades a un objeto en tiempo de ejecución
// sin modificar su clase. Permite componer comportamientos (capa sobre capa).

// Componente base
interface Mensaje {
    fun enviar(): String
}

// Componente concreto
class MensajeBasico : Mensaje {
    override fun enviar(): String = "Mensaje enviado"
}

// Decorador base: mantiene una referencia al componente
abstract class MensajeDecorador(private val mensaje: Mensaje) : Mensaje {
    // Delegación por defecto
    override fun enviar(): String = mensaje.enviar()
}

// Decorador concreto: añade "encriptado"
class MensajeEncriptado(mensaje: Mensaje) : MensajeDecorador(mensaje) {
    override fun enviar(): String = "🔒 " + super.enviar() + " (Encriptado)"
}

// Decorador concreto: añade "comprimido"
class MensajeComprimido(mensaje: Mensaje) : MensajeDecorador(mensaje) {
    override fun enviar(): String = "📦 " + super.enviar() + " (Comprimido)"
}

fun main() {
    val base: Mensaje = MensajeBasico()
    println(base.enviar())

    // Añadimos encriptado sin tocar la implementación base
    val encriptado: Mensaje = MensajeEncriptado(base)
    println(encriptado.enviar())

    // Encadenamos otro decorador (comprimir + encriptar + base)
    val combo: Mensaje = MensajeComprimido(encriptado)
    println(combo.enviar())
}
```

---

### <a id="code-facade"></a>Facade (Estructural)
```kotlin
// Facade: ofrece una interfaz simple a un subsistema complejo,
// reduciendo el acoplamiento de los clientes.

// Subsistemas internos
class SistemaAudio { fun reproducirAudio() = println("Reproduciendo audio") }
class SistemaDescarga { fun descargarCancion(cancion: String) = println("Descargando $cancion") }
class SistemaRecomendaciones { fun obtenerRecomendaciones() = println("Mostrando recomendaciones") }

// Fachada que orquesta llamadas a los subsistemas
class FacadeMusica(
    private val audio: SistemaAudio = SistemaAudio(),
    private val descarga: SistemaDescarga = SistemaDescarga(),
    private val recomendaciones: SistemaRecomendaciones = SistemaRecomendaciones()
) {
    fun reproducir() = audio.reproducirAudio()
    fun descargar(cancion: String) = descarga.descargarCancion(cancion)
    fun mostrarRecomendaciones() = recomendaciones.obtenerRecomendaciones()
}

fun main() {
    val app = FacadeMusica()
    app.reproducir()
    app.descargar("Canción Favorita")
    app.mostrarRecomendaciones()
}
```

---

### <a id="code-observer"></a>Observer (Comportamiento)
```kotlin
// Observer: relación publicador-suscriptores para notificar cambios de estado.
// Varios observadores reaccionan al mismo evento sin acoplarse entre sí.

// Contrato del observador
interface Observador {
    fun actualizar(noticia: String)
}

// Sujeto observable
class CanalDeNoticias {
    private val observadores = mutableListOf<Observador>()

    fun agregarObservador(obs: Observador) { observadores.add(obs) }
    fun eliminarObservador(obs: Observador) { observadores.remove(obs) }

    fun nuevaNoticia(noticia: String) {
        println("Nueva noticia publicada: $noticia")
        notificarObservadores(noticia)
    }

    private fun notificarObservadores(noticia: String) {
        observadores.forEach { it.actualizar(noticia) }
    }
}

// Observador concreto
class UsuarioApp(private val nombre: String) : Observador {
    override fun actualizar(noticia: String) {
        println("$nombre recibió la noticia: $noticia")
    }
}

fun main() {
    val canal = CanalDeNoticias()
    val juan = UsuarioApp("Juan")
    val ana = UsuarioApp("Ana")

    canal.agregarObservador(juan)
    canal.agregarObservador(ana)

    canal.nuevaNoticia("¡Actualización importante en el mercado!")
}
```

---

### <a id="code-chain"></a>Chain of Responsibility (Comportamiento)
```kotlin
// Chain of Responsibility: encadena manejadores; cada uno decide
// procesar la petición o delegarla al siguiente.

// Solicitud de autenticación (datos de entrada)
data class AuthRequest(
    val username: String,
    val password: String,
    val isAccountActive: Boolean,
    val has2FA: Boolean
)

// Manejador base con enlace al siguiente
abstract class AuthHandler {
    private var next: AuthHandler? = null

    fun setNext(handler: AuthHandler): AuthHandler {
        next = handler
        return handler // permite encadenar setNext()
    }

    fun next(request: AuthRequest): Boolean = next?.handle(request) ?: true
    abstract fun handle(request: AuthRequest): Boolean
}

// Manejadores concretos (reglas)
class CredentialsHandler : AuthHandler() {
    override fun handle(request: AuthRequest): Boolean =
        if (request.username == "user" && request.password == "password") {
            println("Credentials OK.")
            next(request) // delega al siguiente
        } else {
            println("Invalid credentials.")
            false
        }
}

class AccountStatusHandler : AuthHandler() {
    override fun handle(request: AuthRequest): Boolean =
        if (request.isAccountActive) {
            println("Account active.")
            next(request)
        } else {
            println("Account is inactive.")
            false
        }
}

class TwoFactorAuthHandler : AuthHandler() {
    override fun handle(request: AuthRequest): Boolean =
        if (request.has2FA) {
            println("2FA verified.")
            next(request)
        } else {
            println("2FA not enabled.")
            false
        }
}

fun main() {
    // Construcción de la cadena: Credenciales -> Estado -> 2FA
    val chain = CredentialsHandler()
        .apply { setNext(AccountStatusHandler()).setNext(TwoFactorAuthHandler()) }

    val request = AuthRequest("user", "password", isAccountActive = true, has2FA = true)
    val ok = chain.handle(request)
    println("Authentication success: $ok")
}
```

---

### <a id="code-command"></a>Command (Comportamiento)
```kotlin
// Command: encapsula una petición como objeto.
// Permite desacoplar emisores (botones/gestos) de los receptores y soportar undo/redo, colas, logs.

// Contrato del comando
interface Comando { fun ejecutar() }

// Comandos concretos
class EncenderLuz : Comando { override fun ejecutar() = println("La luz está encendida") }
class ApagarLuz   : Comando { override fun ejecutar() = println("La luz está apagada") }

// Invocador que mantiene y dispara el comando
class ControlRemoto {
    private var comando: Comando? = null
    fun setComando(c: Comando) { comando = c }
    fun presionarBoton() { comando?.ejecutar() ?: println("Sin comando") }
}

fun main() {
    val control = ControlRemoto()
    val encender = EncenderLuz()
    val apagar = ApagarLuz()

    control.setComando(encender); control.presionarBoton()
    control.setComando(apagar);   control.presionarBoton()
}
```

---

### <a id="code-state"></a>State (Comportamiento)
```kotlin
// State: el comportamiento cambia en función del estado interno, evitando condicionales extensos.

// Interfaz Estado
interface Estado { fun presionarBotonPlay(contexto: Reproductor) }

// Estados concretos implementan reacciones específicas
class Reproduciendo : Estado {
    override fun presionarBotonPlay(contexto: Reproductor) {
        println("Pausando reproducción…")
        contexto.setEstado(Pausado())
    }
}

class Pausado : Estado {
    override fun presionarBotonPlay(contexto: Reproductor) {
        println("Reanudando reproducción…")
        contexto.setEstado(Reproduciendo())
    }
}

// Contexto que mantiene una referencia al estado actual
class Reproductor {
    private var estado: Estado = Pausado()
    fun setEstado(nuevo: Estado) { estado = nuevo }
    fun presionarBotonPlay() = estado.presionarBotonPlay(this)
}

fun main() {
    val reproductor = Reproductor()
    reproductor.presionarBotonPlay() // Pasa de Pausado -> Reproduciendo
    reproductor.presionarBotonPlay() // De Reproduciendo -> Pausado
}
```

---

### <a id="code-strategy"></a>Strategy (Comportamiento)
```kotlin
// Strategy: familia de algoritmos intercambiables en tiempo de ejecución.
// El contexto delega en la estrategia seleccionada sin conocer detalles de su implementación.

// Contrato de las estrategias
interface CompressionStrategy { fun compress(fileName: String) }

// Estrategias concretas
class JPEGCompression : CompressionStrategy {
    override fun compress(fileName: String) { println("Compressing $fileName as JPEG") }
}

class PNGCompression : CompressionStrategy {
    override fun compress(fileName: String) { println("Compressing $fileName as PNG") }
}

// Contexto que usa una estrategia configurable
class PhotoApp {
    private var strategy: CompressionStrategy = JPEGCompression() // por defecto
    fun setCompressionStrategy(s: CompressionStrategy) { strategy = s }
    fun savePhoto(fileName: String) { strategy.compress(fileName) }
}

fun main() {
    val app = PhotoApp()
    app.setCompressionStrategy(JPEGCompression()); app.savePhoto("photo1.jpg")
    app.setCompressionStrategy(PNGCompression());  app.savePhoto("photo2.png")
}
```
