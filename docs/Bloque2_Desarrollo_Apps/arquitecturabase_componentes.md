# Arquitectura base y componentes

Una aplicación móvil nativa combina una interfaz, un estado y operaciones que se ejecutan bajo las reglas de su sistema operativo. Android e iOS resuelven estas responsabilidades mediante mecanismos diferentes.

En los siguientes temas estudiaremos cómo se organiza cada plataforma, cómo se construye la interfaz, cómo se comunican sus piezas y qué sucede cuando el usuario interrumpe una aplicación o el sistema necesita recursos.

## Elige la plataforma

| Tema | Contenidos principales |
|---|---|
| [Arquitectura base y componentes de Android](arquitecturabase_componentes_android.md) | Activities, Fragments, Services, Broadcast Receivers, Content Providers y Application. |
| [Arquitectura base y componentes de iOS](arquitecturabase_componentes_ios.md) | App y escenas, SwiftUI, UIKit, tareas en segundo plano, notificaciones y datos. |

Para comparar ambas plataformas, conviene identificar primero la responsabilidad que queremos resolver. Una semejanza funcional no implica que existan clases equivalentes ni ciclos de vida idénticos.

## Preguntas comunes

¿Qué parte de la aplicación representa la interfaz? ¿Quién conserva los datos? ¿Qué cambia cuando deja de verse una pantalla? ¿Puede continuar una operación si la app pasa a segundo plano? ¿Cómo accede una app a contenidos de otra?

Estas preguntas servirán de guía para leer ambos temas y conectar los ejemplos con una aplicación real.
