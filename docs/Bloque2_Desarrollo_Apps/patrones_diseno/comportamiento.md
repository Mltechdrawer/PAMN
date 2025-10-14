# Patrones de diseño de comportamiento

## Observer
**Qué es**  
Establece una relación **publicador → suscriptores** para notificar **cambios de estado**.

**Cuándo usarlo en móvil**  
- Actualizar UI cuando cambian datos (flows, LiveData).  
- Notificar eventos del sistema (conectividad, sesión, permisos).  
- Modularizar reacciones múltiples ante un mismo evento.  

[Observer](patrones.md#code-observer)

<details>
<summary>Ejemplo breve</summary>
`SessionObservable` avisa a varias pantallas cuando el usuario inicia/cierra sesión; cada observador decide qué actualizar.
</details>

---

## Chain of Responsibility
**Qué es**  
Pasa una petición por una **cadena de manejadores** hasta que uno la procesa.

**Cuándo usarlo en móvil**  
- Validaciones de formularios con varias reglas encadenadas.  
- Procesado de eventos de UI o *middleware* de red.  
- Filtros secuenciales (compresión, cifrado, firma) en archivos.  

[Chain of Responsibility](patrones.md#code-chain)

<details>
<summary>Ejemplo breve</summary>
Una cadena `NoEmpty -> EmailValido -> PasswordFuerte` valida el registro. El flujo se detiene en la primera regla que falle.
</details>

---

## Command
**Qué es**  
Encapsula una **solicitud** como objeto, permitiendo **deshacer**, **cola** y **registro**.

**Cuándo usarlo en móvil**  
- Acciones con *undo/redo* (editores, listas con swipe).  
- Programar tareas diferidas/offline y reintentos.  
- Desacoplar botones/gestos de la lógica ejecutada.  

[Command](patrones.md#code-command)

<details>
<summary>Ejemplo breve</summary>
`DeleteItemCommand` ejecuta y registra la operación; `undo()` restaura el elemento si el usuario deshace.
</details>

---

## State
**Qué es**  
Permite que un objeto **cambie su comportamiento** cuando cambia su **estado interno**.

**Cuándo usarlo en móvil**  
- Flujos con estados claros (player: *Playing*, *Paused*, *Stopped*).  
- Máquinas de estados para permisos, login o *onboarding*.  
- Reducir condicionales anidados en la UI.  

[State](patrones.md#code-state)

<details>
<summary>Ejemplo breve</summary>
`PlayerContext` delega en estados `Playing`/`Paused` la respuesta a `play()`/`pause()`. Cambiar de estado cambia el comportamiento sin `if` complejos.
</details>

---

## Strategy
**Qué es**  
Define una **familia de algoritmos** intercambiables en **tiempo de ejecución**.

**Cuándo usarlo en móvil**  
- Seleccionar compresión, ordenación o *cache policy* según contexto.  
- Elegir proveedor de pago/mapas según país del usuario.  
- Reutilizar la UI con lógicas alternativas sin duplicar código.  

[Strategy](patrones.md#code-strategy)

<details>
<summary>Ejemplo breve</summary>
`BusquedaContext` recibe una `BusquedaStrategy`: por nombre, por proximidad o por popularidad. Cambiar la estrategia no afecta a la UI.
</details>
