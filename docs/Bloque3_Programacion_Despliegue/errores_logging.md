# Gestión de errores y logging

## Introducción a la gestión de errores y logging  

La gestión de errores y el logging son componentes esenciales en el desarrollo de aplicaciones móviles nativas. Ambos permiten detectar fallos, comprender el comportamiento de la aplicación y mejorar su estabilidad. En dispositivos móviles, donde existen limitaciones de recursos, variabilidad de conectividad y alto uso de sensores, manejar correctamente los errores es clave para garantizar una experiencia de usuario fluida.  

El logging complementa este proceso registrando información relevante sobre lo que ocurre en la aplicación: eventos, advertencias, errores y puntos clave del flujo de ejecución. Funciona como una “caja negra” que permite reconstruir lo sucedido y depurar problemas tanto en desarrollo como en producción.  

### ¿Por qué es importante manejar errores en aplicaciones móviles?  
- Evita bloqueos inesperados (crashes) que afectan la percepción de calidad.  
- Permite recuperar la aplicación ante situaciones críticas.  
- Protege la integridad de datos y procesos sensibles.  
- Facilita el diagnóstico y la resolución de problemas.  
- Ayuda al mantenimiento a largo plazo y mejora continua de la app.  

### ¿Qué es logging?  
El logging consiste en registrar eventos o información relevante durante la ejecución de la aplicación. Estos registros se utilizan para:  
- Depurar comportamientos anómalos.  
- Monitorizar el rendimiento.  
- Detectar patrones de uso.  
- Analizar fallos reportados por los usuarios.  

### Tipos de errores y tipos de logs  
**Errores:**  
- **Críticos**: Detienen la ejecución de la aplicación.  
- **Advertencias**: No detienen la app, pero reflejan comportamientos inesperados.  
- **Errores recuperables**: Permiten continuidad mediante una alternativa o fallback.  

**Logs:**  
- **Debug**: Información detallada para desarrollo.  
- **Info**: Eventos relevantes del flujo normal.  
- **Warning**: Señales de comportamiento anómalo.  
- **Error**: Fallos que requieren intervención.  
- **Fatal**: Problemas que provocan la terminación de la app.  

---

## Modelos de errores en aplicaciones móviles nativas  

Las apps móviles se ejecutan en entornos complejos donde influyen sensores, ciclo de vida, red, permisos, almacenamiento y recursos del sistema operativo. Esto genera múltiples fuentes potenciales de error.  

### Errores en tiempo de compilación vs. tiempo de ejecución  
- **Errores en tiempo de compilación**:    
  Se detectan antes de ejecutar la aplicación. Suelen deberse a problemas de sintaxis, tipos de datos, dependencias o incompatibilidades del SDK.  
- **Errores en tiempo de ejecución**:    
  Surgen mientras la app está funcionando. Incluyen accesos a recursos no disponibles, valores nulos inesperados, permisos denegados, fallos de red o falta de memoria.  

### Fallos específicos de plataformas móviles  
#### Android  
- Permisos no concedidos por parte del usuario.  
- Cambios en el ciclo de vida: interrupciones, recreaciones y destrucción de actividades.  
- Fallos producidos al rotar la pantalla.  
- Limitaciones de memoria que pueden causar cierres de la app.  
- Sensores no disponibles o devueltos con valores erráticos.  

#### iOS  
- Uso incorrecto de valores opcionales.  
- Restricciones del sandbox que impiden acceder a recursos.  
- Suspensiones por falta de memoria.  
- Cambios de estado inesperados en frameworks como CoreMotion o CoreLocation.  

### Excepciones según lenguaje  
- **Kotlin**: NullPointerException, IllegalStateException, IOException, etc.    
- **Swift**: uso del sistema de `Error`, manejo con `try`, `throws`, `do/catch`.    
- **Java**: excepciones checked y unchecked.  

---

## Estrategias de captura de errores  

Una buena estrategia de manejo de errores permite que la aplicación continúe funcionando, registre correctamente la información del fallo y ofrezca una respuesta adecuada al usuario.  

### Uso de try/catch/finally  
Los bloques `try/catch` permiten encapsular código susceptible de fallar y reaccionar de manera controlada:  
- Prevenir cierres inesperados.  
- Registrar el contexto del fallo.  
- Aplicar acciones de recuperación.  
El bloque `finally` se utiliza para liberar recursos, cerrar flujos o limpiar estados sin importar si hubo error o no.  

### Propagación y manejo global
En algunas funciones es preferible no capturar el error localmente y permitir que se propague para gestionarse a un nivel superior.    
Además, muchas apps implementan manejadores globales que capturan errores no controlados y evitan cierres silenciosos.  

Ejemplos típicos:  
- Capturadores globales de excepciones.  
- Reportes automáticos de crash.  
- Registro del estado de la aplicación antes de fallar.  

### Diseño de flujos de error  
Una estrategia bien diseñada debe definir:  
- Cómo reaccionar ante distintos tipos de fallos.  
- Qué mensaje mostrar al usuario.  
- Qué datos registrar en los logs.  
- Si es posible recuperar el proceso.  
- Qué hacer si el error ocurre repetidamente.  

### Integración con la UI  
Los usuarios no deben conocer detalles técnicos del error. Las interfaces deben mostrar:
- Mensajes claros y simples.  
- Acciones sugeridas (“Reintentar”, “Habilitar permiso”, “Conectar a Internet”).
- Alternativas cuando sea posible.  

Los mensajes deben ser coherentes, breves y no intrusivos.  

---

## Logging: buenas prácticas y niveles de log

El logging permite entender el comportamiento de la aplicación en distintos momentos y facilita la resolución de problemas. Sin embargo, debe utilizarse de manera cuidadosa.  

### Niveles habituales de logging  
- **DEBUG**: Información muy detallada, utilizada solo en desarrollo.  
- **INFO**: Eventos clave del funcionamiento normal.  
- **WARN**: Situaciones inusuales o inesperadas.  
- **ERROR**: Fallos importantes que requieren atención.  
- **FATAL**: Fallos críticos que conducen al cierre.  

### Contenido recomendado en un log  
Cada entrada debería incluir:  
- Fecha y hora.  
- Nivel del evento.  
- Mensaje descriptivo.  
- Contexto de ejecución (pantalla, módulo, sensor).  
- Identificadores anónimos para seguimiento.  
- Stack trace cuando sea necesario.  

### Evitar logs problemáticos  
- No registrar datos personales o confidenciales.  
- Evitar almacenar claves, tokens o contraseñas.  
- No generar un volumen excesivo de logs en producción.  
- Evitar logs dentro de bucles intensivos o cálculos pesados.  

### Persistencia de logs  
Los logs pueden almacenarse o transmitirse de distintas formas:  
- Archivos de texto locales.  
- Bases de datos internas.  
- Consolas de depuración.  
- Servicios remotos de análisis y reporte.  
- Herramientas de crash reporting con paneles de control.  

---

## Herramientas y librerías para gestión de errores y logging en móvil  

La gestión moderna de errores y el logging en aplicaciones móviles se apoyan en herramientas especializadas que facilitan la recolección, análisis y visualización de fallos y eventos en tiempo real. Estas herramientas permiten a los desarrolladores detectar problemas rápidamente y tomar decisiones basadas en datos.  

### Android: herramientas habituales  
- **Logcat**    
  Consola integrada que permite visualizar logs de la aplicación en tiempo real, filtrarlos por nivel o etiqueta y depurarlos durante el desarrollo.  
- **Timber**    
  Librería ligera de logging que simplifica el uso de logs en Android. Facilita estructurar mensajes, añadir formato, enviar logs a diferentes destinos y desactivar logs en producción.  
- **Firebase Crashlytics**    
  Servicio de crash-reporting que registra fallos, excepciónes no controladas y errores críticos. Agrupa crashes, muestra estadísticas y permite detectar patrones y causas comunes.  

### iOS: herramientas de logging  
- **Consola del sistema**    
  Permite ver los eventos y logs de la app durante el desarrollo, incluidos mensajes emitidos por frameworks del sistema.  
- **os_log**    
  API moderna y eficiente para registrar eventos estructurados. Permite definir categorías y niveles, y optimiza el rendimiento del logging.  
- **CocoaLumberjack**    
  Librería flexible que permite gestionar múltiples destinos de log y aplicar estrategias de rotación y persistencia.  
- **Sentry (versión iOS)**    
  Servicio orientado a la captura de fallos, métricas y errores en tiempo real, similar a Crashlytics.  

### Multiplataforma y entornos híbridos  
- Herramientas como **Sentry**, **Firebase Crashlytics**, **Bugsnag** o **Instabug** permiten unificar la captura de errores en apps nativas, híbridas o multiplataforma.  
- Ofrecen dashboards centralizados, alertas automáticas, agrupación inteligente de errores y análisis de rendimiento.    

### Comparativa funcional  
- **Alertas automáticas**: notificaciones cuando ocurre un error crítico en producción.  
- **Dashboards**: visualización clara de métricas, tendencias y frecuencia de fallos.    
- **Agrupación de errores**: detección de patrones para evitar duplicados.    
- **Informes de uso**: métricas sobre estabilidad, sesiones, dispositivos afectados, etc.  
- **Integración con CI/CD**: herramientas que permiten generar reportes de errores tras pruebas automatizadas.    

---

## Implementación práctica: caso de estudio  

Este apartado muestra un ejemplo práctico que combina manejo de errores y logging para ilustrar un flujo real en una aplicación móvil.  

### Definición del escenario
Caso ejemplo:    
**Un sensor de proximidad deja de responder y la app necesita detectar el fallo, registrar el evento y ofrecer una alternativa al usuario.** 

Aspectos a tener en cuenta:  
- Identificación del error capturado por la API del sensor.  
- Posible falta de permisos o hardware no disponible.  
- Reacción de la app para evitar un crash.  

### Código de ejemplo para captura de error y log  
Tanto Android como iOS implementan estructuras similares:    
1. Intentar acceder al sensor.   
2. Registrar el error en caso de fallo.    
3. Mostrar mensaje claro en la UI.    
4. Ejecutar un fallback (por ejemplo, desactivar la funcionalidad dependiente del sensor).  

### Envío del log a un servidor o servicio remoto
Tras capturar el fallo:  
- Se crea un registro con el timestamp, tipo de error y contexto.  
- Si la app está en producción, se envía el log a un servicio remoto como Crashlytics o Sentry.  
- El envío debe ser eficiente y no bloquear la experiencia del usuario.  

### Visualización y análisis del informe  
En el panel del servicio elegido:  
- Los errores aparecen agrupados.  
- Se visualiza el stack trace, el dispositivo afectado, la versión de la app y el contexto.  
- El desarrollador puede filtrar por versión, dispositivo, usuario anónimo o frecuencia.  

### Mejora iterativa basada en logging  
El ciclo completo:  
Detectar error → Registrar error → Analizar datos → Corregir →  Verificar reducción de fallos.  

Este proceso mejora la estabilidad con cada versión.  

---

## Políticas de logging, privacidad y rendimiento  

El logging debe implementarse respetando la privacidad del usuario, las normativas legales y limitaciones de rendimiento del dispositivo.  

### Aspectos legales y de privacidad  
- Evitar incluir datos personales identificables en los logs.    
- Anonimizar información que pueda identificar a un usuario.    
- Proteger datos sensibles relacionados con ubicaciones, salud, accesos o tokens.    
- Ofrecer opciones de consentimiento para enviar informes de fallos.  

### Efectos del logging en el rendimiento  
- Un exceso de logs puede afectar al rendimiento general de la app.    
- Generar demasiados registros consume más CPU, almacenamiento y batería.    
- El logging debe ser más reducido en producción que en desarrollo.  

### Políticas de retención de logs  
- Definir un tiempo máximo de almacenamiento de logs.    
- Borrar periódicamente los logs antiguos para evitar ocupar almacenamiento.    
- Rotar archivos de log cuando crecen en tamaño.  

### Seguridad del logging  
- No registrar secretos, contraseñas ni claves API.    
- Usar hashes en lugar de valores de texto plano cuando sea necesario.    
- Proteger los logs en tránsito si se envían a servidores externos.    
- Encriptar logs sensibles cuando se almacenan localmente.  

---

## Testing y monitoreo post-despliegue

Una aplicación estable requiere pruebas específicas orientadas a la detección de errores y un monitoreo continuo una vez puesta en producción.  

### Cómo probar el manejo de errores  
- **Fault injection**: provocar fallos controlados para verificar el comportamiento.    
- **Simulación de condiciones adversas**: sin red, batería baja, sensor desconectado, permisos revocados.    
- **Pruebas unitarias e integradas**: validar que los bloques de manejo de errores responden correctamente.    
- **Pruebas de estrés**: comprobar comportamientos bajo alta carga o múltiples errores consecutivos.  

### Monitorización del logging en producción
- Revisión de métricas clave:   
  - Número de crashes.    
  - Frecuencia de errores por sesión.    
  - Tasa de fallos por versión.    
  - Dispositivos y sistemas más afectados.    
- Detección de patrones: errores repetitivos, fallos en sensores, problemas de red.  

### Alertas automáticas  
- Configuración de umbrales para recibir alertas cuando:  
  - Aumentan los crashes.    
  - Se detecta un error nuevo crítico.    
  - Un fallo se repite en demasiados dispositivos.    
- Las alertas ayudan a reaccionar rápido ante problemas urgentes.  

### Mejores prácticas de mantenimiento  
- Revisar logs y paneles de errores de forma periódica.    
- Actualizar librerías y dependencias vulnerables o inestables.    
- Priorizar correcciones en función de impacto real en usuarios.    
- Documentar causas y soluciones para facilitar mantenimiento futuro.  

---

