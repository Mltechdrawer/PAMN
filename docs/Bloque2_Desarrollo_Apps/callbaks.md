# Callbacks del sistema en Android (resumen práctico)

> Basado en el documento de arquitectura base y componentes del sistema Android.

> Leyenda:  
**Obligatorio** = debes implementarlo para que el componente haga “algo” en tu caso de uso.    
**Opcional** = solo si necesitas lógica en esa transición.  
**Dependiente** = solo si usas ese modo/operación concreta.

## Application

| Callback | ¿Quién lo invoca? | ¿Cuándo? | ¿Obligatorio? | Uso recomendado / Notas |
|---|---|---|---|---|
| `onCreate()` | Sistema | Al crear el proceso de la app | Opcional | Inicializar librerías/DI/config global mínima. Evita trabajo pesado. |
| `onLowMemory()` | Sistema | Memoria del sistema muy baja | Opcional | Libera cachés no críticos. |
| `onTrimMemory(level)` | Sistema | Diferentes niveles de presión de memoria | Opcional | Ajusta liberación según `level`. |
| `onTerminate()` | Sistema | Cierre del proceso (normalmente solo en emulador) | Opcional | No confíes en que se llame en producción. |

## Activity

| Callback | ¿Quién lo invoca? | ¿Cuándo? | ¿Obligatorio? | Uso recomendado / Notas |
|---|---|---|---|---|
| `onCreate()` | Sistema | Primera creación | **Dependiente** | Suele implementarse para inflar vistas e iniciar estado. |
| `onStart()` | Sistema | Al hacerse visible | Opcional | Preparar UI ligera/registrar observadores. |
| `onResume()` | Sistema | Al ganar foco (interacción) | Opcional | Reanudar sensores/animaciones/flujo. |
| `onPause()` | Sistema | Al perder foco parcial | Opcional | Persistir estado efímero, pausar tareas. |
| `onStop()` | Sistema | Deja de ser visible | Opcional | Liberar recursos pesados (cámara/GPS). |
| `onRestart()` | Sistema | Tras `onStop()`, antes de volver | Opcional | Re-preparar UI si aplica. |
| `onDestroy()` | Sistema | Antes de destrucción | Opcional | Limpieza final; no confíes para persistencia crítica. |

## Fragment

| Callback | ¿Quién lo invoca? | ¿Cuándo? | ¿Obligatorio? | Uso recomendado / Notas |
|---|---|---|---|---|
| `onAttach()` | Sistema | Al asociarse a la Activity | Opcional | Obtener context/dependencias. |
| `onCreate()` | Sistema | Inicialización lógica (sin vista) | Opcional | Preparar estado no-UI. |
| `onCreateView()` | Sistema | Inflar layout | **Dependiente** | Necesario si el fragment **tiene UI**. |
| `onViewCreated()` | Sistema | Tras crear la vista | Opcional | Enlazar vistas/observadores/adaptadores. |
| `onStart()` / `onResume()` | Sistema | Visible / interactivo | Opcional | Reanudar UI y listeners. |
| `onPause()` / `onStop()` | Sistema | Pierde foco / no visible | Opcional | Guardar estado/pausar trabajo. |
| `onDestroyView()` | Sistema | Se destruye la **vista** | Opcional | Anular binding y referencias a vistas. |
| `onDestroy()` / `onDetach()` | Sistema | Fin del fragment / separación | Opcional | Liberar recursos y referencias a Activity. |

## Service

| Callback | ¿Quién lo invoca? | ¿Cuándo? | ¿Obligatorio? | Uso recomendado / Notas |
|---|---|---|---|---|
| `onCreate()` | Sistema | Inicialización del servicio | Opcional | Preparar recursos compartidos. |
| `onStartCommand()` | Sistema | Al arrancar como **started service** | **Dependiente** | Implementar si usas started service (trabajo en bg). |
| `onBind()` | Sistema | Al enlazarse como **bound service** | **Dependiente** | Devuelve `IBinder` si expones API a clientes. |
| `onUnbind()` / `onRebind()` | Sistema | Al desenlazar/relanzar enlace | Dependiente | Gestionar suscriptores dinámicos. |
| `onDestroy()` | Sistema | Antes de terminar | Opcional | Parar hilos, liberar recursos. |

## BroadcastReceiver

| Callback | ¿Quién lo invoca? | ¿Cuándo? | ¿Obligatorio? | Uso recomendado / Notas |
|---|---|---|---|---|
| `onReceive(Context, Intent)` | Sistema | Al recibirse el broadcast | **Obligatorio** | Trabajo **rápido**; delega tareas largas a `WorkManager`. |

## ContentProvider

| Callback / Método | ¿Quién lo invoca? | ¿Cuándo? | ¿Obligatorio? | Uso recomendado / Notas |
|---|---|---|---|---|
| `onCreate()` | Sistema | Inicialización del provider | **Dependiente** | Configurar DB/recursos. |
| `query()` | Sistema/Clientes | Lectura de datos | **Dependiente** | Implementa si expones lectura. |
| `insert()` | Sistema/Clientes | Inserción | **Dependiente** | Valida entrada/URIs. |
| `update()` | Sistema/Clientes | Actualización | **Dependiente** | Control de concurrencia. |
| `delete()` | Sistema/Clientes | Borrado | **Dependiente** | Maneja cascadas/relaciones. |
| `getType()` | Sistema/Clientes | MIME por URI | **Dependiente** | Útil para `ContentResolver`. |

---

### Notas generales

- **No es necesario sobrescribir todos los callbacks**: solo aquellos en los que tu componente necesita reaccionar.  
- **Evita trabajo pesado en UI callbacks** (`onCreate`, `onResume`): usa `ViewModel`, corrutinas o `WorkManager` cuando corresponda.  
- **Liberación de recursos**: cámaras, sensores, receptores y bindings deben limpiarse en `onStop()`, `onDestroyView()` o `onDestroy()` según el componente.  

---
**Ejemplos de liberación de recursos:**   

[Liberación de recursos](liberacion_recursos.md "Liberación de recursos")

