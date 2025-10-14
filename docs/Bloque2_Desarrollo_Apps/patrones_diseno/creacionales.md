# Patrones de diseño creacionales

## Singleton
**Qué es**  
Garantiza **una única instancia** de una clase y ofrece **acceso global** a ella.

**Cuándo usarlo en móvil**
- Configuración global de la app (localización, tema, *feature flags*).
- Gestores únicos: analítica, *logging*, *SessionManager* o *AuthManager*.
- Recursos compartidos que deben coordinarse (por ejemplo, un *cache* en memoria).

**Evitarlo si** necesitas testabilidad alta o múltiples contextos de ejecución; en esos casos, considera inyección de dependencias.

[Singleton](patrones.md#code-singleton)

<details>
<summary>Ejemplo breve</summary>
Un `object` en Kotlin actúa como singleton. `ConfiguracionGlobal` centraliza idioma y tema. Desde cualquier pantalla, leer/escribir esos valores refleja la **misma instancia**.
</details>

---

## Factory
**Qué es**  
Proporciona una **interfaz para crear objetos** sin acoplar el cliente a clases concretas.

**Cuándo usarlo en móvil**
- Seleccionar implementaciones según **entorno** (debug/release) o **plataforma** (diferentes proveedores de pagos, mapas, almacenamiento).
- Crear *ViewModels*, *Repositories* o conectores de red con **estrategias intercambiables**.
- Encapsular lógica de inicialización compleja (parámetros, dependencias, *feature toggles*).

**Beneficios**: desacopla, facilita pruebas (inyectas una *fake factory*), y permite ampliar comportamientos sin tocar el cliente.

[Factory](patrones.md#code-factory)

<details>
<summary>Ejemplo breve</summary>
`MetodoPagoFactory` devuelve `Tarjeta` o `PayPal` según el tipo. La UI solo conoce la **interfaz** `MetodoPago` y no depende de las clases concretas.
</details>

---

## Builder
**Qué es**  
Permite **construir objetos complejos paso a paso**, separando construcción de representación final.

**Cuándo usarlo en móvil**
- Crear entidades con **muchos opcionales** (perfil, filtros de búsqueda, parámetros de red).
- Configurar componentes UI complejos (diálogos, notificaciones) sin **constructores kilométricos**.
- Evitar errores por órdenes de parámetros y mejorar la legibilidad encadenando pasos.

[Builder](patrones.md#code-builder)

<details>
<summary>Ejemplo breve</summary>
`PerfilUsuario.Builder` permite definir `nombre`, `foto` y `bio` de forma fluida. El `build()` devuelve un objeto **coherente** y listo para usar en la capa de presentación.
</details>

---

## Prototype
**Qué es**  
Permite **clonar objetos** sin acoplarse a sus clases concretas, reutilizando el estado existente.

**Cuándo usarlo en móvil**
- Duplicar **componentes UI** o modelos con configuración costosa (estilos, formateos) para crear variantes rápidas.
- Generar elementos repetitivos en listas o editores sin recalcular estado inicial.
- Crear *placeholders* o *templates* que se adaptan con cambios mínimos.

[Prototype](patrones.md#code-prototype)

<details>
<summary>Ejemplo breve</summary>
`Boton` implementa `Widget` y se **clona** copiando el color. Cambiar el color del clon no afecta al original: útil para generar variantes de controles sin reconstruirlos desde cero.
</details>

