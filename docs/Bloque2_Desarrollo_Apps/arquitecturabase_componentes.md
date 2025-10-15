# Arquitectura base y componentes del sistema Android

## Introducción
Las aplicaciones Android están formadas por un conjunto de componentes que trabajan de manera coordinada bajo la gestión del sistema operativo. Cada componente cumple una función específica y tiene un ciclo de vida propio, gestionado por el sistema según las necesidades de recursos y contexto.

Comprender esta arquitectura es esencial para diseñar aplicaciones eficientes, seguras y escalables. En este tema exploraremos cómo Android organiza las aplicaciones, qué papel desempeña cada componente y cómo se comunican entre ellos.

---

## Arquitectura de una aplicación Android
Una aplicación Android se compone de varias capas interdependientes:

1. **Aplicación** → Las Activities, Fragments, Services, etc. que construyen la funcionalidad visible y la lógica de la app.
2. **Framework de Android** → Proporciona las APIs principales que permiten interactuar con el sistema operativo.
3. **Bibliotecas del sistema** → Conjunto de librerías C/C++ que implementan componentes fundamentales (gráficos, base de datos, multimedia).  
4. **Kernel de Linux** → Gestiona memoria, procesos, seguridad y comunicación entre hardware y software.

Android gestiona los procesos de las aplicaciones de forma dinámica. Cada app se ejecuta en su propio proceso, con su propio espacio de memoria, y el sistema puede pausar o destruir componentes para liberar recursos sin afectar a otras apps.

---

## Componentes fundamentales de una aplicación Android
Los seis componentes principales son las piezas clave que el sistema utiliza para ejecutar, mostrar información, comunicarse y compartir datos entre aplicaciones.

### Activity
**Definición:** Una Activity representa una pantalla con la que el usuario puede interactuar directamente. Es el punto de entrada más habitual de una aplicación.

**Funciones principales:**
- Gestionar la interfaz de usuario (UI) y las interacciones del usuario.
- Coordinar el ciclo de vida de la pantalla y los recursos asociados.

**Métodos clave del ciclo de vida:**  
- `onCreate()` → Inicializa la actividad y configura la interfaz.  
- `onStart()` → Hace visible la Activity.  
- `onResume()` → La Activity pasa a primer plano y es interactiva.    
- `onPause()` → Se pausa cuando otra Activity toma el foco.  
- `onStop()` → Deja de ser visible.  
- `onDestroy()` → Se libera la memoria asociada.  

**Ejemplo:**   Pantalla de inicio de sesión que contiene campos de usuario y contraseña. Cuando el usuario rota el dispositivo, el estado debe guardarse para restaurarse al volver a `onResume()`.

**Buenas prácticas:**  
- Evitar operaciones pesadas en `onCreate()` o `onResume()`.  
- Utilizar `ViewModel` y `LiveData` para preservar el estado.  
- Dividir la lógica de UI en Fragments para modularidad.  

---

### Fragment
**Definición:** Un Fragment es un componente reutilizable que forma parte de la interfaz de una Activity. Permite dividir una pantalla en secciones modulares.

**Funciones principales:**  
- Facilitar interfaces dinámicas y adaptables (por ejemplo, para tablets).  
- Compartir la lógica entre diferentes pantallas.  

**Ciclo de vida simplificado:**  
- `onAttach()` → El fragment se asocia a la Activity.  
- `onCreateView()` → Se infla el diseño (layout) del fragment.  
- `onViewCreated()` → Se inicializa la lógica de la interfaz.  
- `onPause()` / `onStop()` / `onDestroyView()` → Se limpian los recursos visuales.  

**Ejemplo:** Una app de noticias donde un fragment muestra la lista de artículos y otro el detalle del artículo seleccionado.

**Buenas prácticas:**  
- Evitar dependencias directas entre Fragments: usar `ViewModel` compartido o el componente de navegación (`NavController`).  
- No mantener referencias a vistas fuera de su ciclo de vida (`onDestroyView()`).  

---

### Service
**Definición:** Un Service es un componente que ejecuta tareas en segundo plano sin interfaz de usuario.

**Tipos de servicios:**  
- **Started Service:** Inicia una tarea que se ejecuta indefinidamente (por ejemplo, descarga de archivos).  
- **Bound Service:** Permite que otras aplicaciones o componentes se vinculen a él para interactuar (por ejemplo, un reproductor de música).  
- **Foreground Service:** Muestra una notificación persistente para indicar al usuario que está activo.  

**Métodos clave:**  
- `onCreate()` → Inicializa el servicio.  
- `onStartCommand()` → Inicia la tarea principal.  
- `onBind()` → Permite vincular otros componentes.  
- `onDestroy()` → Libera recursos.  

**Ejemplo:** Servicio que gestiona la reproducción de música o la sincronización de datos en segundo plano.

**Buenas prácticas:**
- Evitar servicios permanentes sin notificación.
- Utilizar `WorkManager` o `JobScheduler` para tareas periódicas o dependientes de red.

---

### Broadcast Receiver
**Definición:** Los Broadcast Receivers reaccionan a eventos globales del sistema o de otras aplicaciones. No tienen interfaz de usuario y su ciclo de vida es muy breve.

**Ejemplos de eventos:**  
- Dispositivo conectado al cargador.  
- Cambio de conectividad de red.  
- Recepción de un SMS o notificación push.  

**Método principal:**  
- `onReceive(Context, Intent)` → Se ejecuta cuando se detecta el evento.

**Ejemplo:** Un receptor que detecta cuando el teléfono entra en modo avión y muestra un mensaje al usuario.

**Buenas prácticas:**  
- Registrar receptores dinámicos solo cuando sean necesarios.  
- Evitar operaciones pesadas dentro de `onReceive()`.  
- Usar `WorkManager` para procesamientos prolongados.  

---

### Content Provider
**Definición:** Un Content Provider permite compartir datos entre diferentes aplicaciones mediante URIs estructuradas.

**Método principal:**  
- `onCreate()` → Se ejecuta al inicializar el proveedor y se configuran los recursos (por ejemplo, una base de datos SQLite).  

**Métodos CRUD:**  
- `query()` → Recupera datos.  
- `insert()` → Agrega nuevos datos.  
- `update()` → Modifica datos existentes.  
- `delete()` → Elimina datos.  
- `getType()` → Devuelve el tipo MIME asociado a una URI.  

**Ejemplo:** Un ContentProvider que ofrece acceso a los contactos o fotos del dispositivo.

**Buenas prácticas:**
- Usar contratos (`Contract classes`) para definir URIs y columnas.
- Implementar seguridad en los permisos de acceso.

---

### Application
**Definición:** Representa el estado global de la app y su punto de inicialización. Contiene la lógica que debe ejecutarse antes de que se creen otros componentes.

**Método clave:**  
- `onCreate()` → Se ejecuta una vez al inicio del proceso de la aplicación.  

**Usos comunes:**  
- Inicializar librerías, gestores de dependencias o configuraciones globales.  
- Mantener un estado compartido entre componentes.  

**Ejemplo:** Una clase `MyApp` que extiende `Application` e inicializa `Hilt` para la inyección de dependencias.

**Buenas prácticas:**  
- Evitar lógica compleja en `onCreate()`.  
- Delegar la inicialización a gestores o componentes específicos.  

---

## Comunicación entre componentes
La comunicación en Android se basa en el uso de **Intents**, que son mensajes asíncronos que permiten a los componentes solicitar acciones o intercambiar datos.

**Tipos:**  
- **Intents explícitos:** Se dirigen a un componente concreto dentro de la app.  
- **Intents implícitos:** Permiten que el sistema seleccione el componente adecuado (por ejemplo, abrir la cámara o el navegador).  

**Ejemplo:**
```kotlin
val intent = Intent(Intent.ACTION_VIEW, Uri.parse("https://www.ulpgc.es"))
startActivity(intent)
```

**Buenas prácticas:**  
- Usar `IntentFilter` para definir qué acciones puede manejar un componente.  
- Validar los datos recibidos para evitar vulnerabilidades.  

---

## Ciclo de vida global de la aplicación
El sistema Android controla el ciclo de vida de cada componente para optimizar recursos. Comprender este flujo permite evitar fugas de memoria y mejorar la experiencia del usuario.

**Flujo general:**  
1. El usuario inicia una Activity.  
2. Esta puede crear Fragments y Services según necesidad.  
3. El sistema puede pausar, detener o destruir componentes cuando cambian las condiciones (por ejemplo, al rotar la pantalla o minimizar la app).  
4. Los BroadcastReceivers y ContentProviders se activan solo cuando son requeridos.  

**Importancia del manifiesto (`AndroidManifest.xml`):**  
- Declara todos los componentes y sus permisos.  
- Define qué Activity actúa como punto de entrada (`intent-filter` con `MAIN` y `LAUNCHER`).  

---

## Buenas prácticas y patrones de diseño
- Aplicar el patrón **MVVM** (Model-View-ViewModel) para separar lógica de negocio y UI.
- Usar **ViewModel** y **LiveData** para gestionar el ciclo de vida de datos.
- Delegar tareas de segundo plano a **WorkManager** o **Coroutines**.
- Evitar dependencias directas entre Activities y Fragments.
- Seguir principios **Clean Architecture** para modularidad y testabilidad.

---

## Conclusión
Los componentes del sistema Android trabajan de forma orquestada para ofrecer una experiencia fluida al usuario. Comprender su papel y ciclo de vida es fundamental para crear aplicaciones robustas, escalables y eficientes. En los siguientes temas profundizaremos en el **ciclo de vida** de cada componente y en la **gestión de estados** durante la ejecución de la aplicación.

