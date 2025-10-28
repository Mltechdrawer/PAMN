# Detekt

## ¿Qué es Detekt?

**Detekt** es una herramienta de **análisis estático de código** diseñada **específicamente para proyectos Kotlin**.  
Sirve para **evaluar la calidad interna del código fuente** sin necesidad de ejecutarlo, detectando problemas de estilo, errores potenciales y *code smells* (malos olores de diseño).

Es, en resumen, el **“linter y analizador de calidad” oficial de Kotlin**, equivalente a **ESLint** en JavaScript o **Pylint** en Python.

---

## ¿Para qué se utiliza?

Detekt analiza el código y genera un informe con:

- Violaciones de estilo y convenciones de Kotlin.  
- Código duplicado o demasiado complejo.  
- Clases o funciones con demasiadas responsabilidades.  
- Variables, imports o parámetros sin usar.  
- Problemas de legibilidad o mantenibilidad.

En la práctica, se usa para **mantener la calidad y consistencia del código Kotlin**, especialmente en proyectos colaborativos y en **pipelines de integración continua (CI/CD)**.

---

## Ejemplo de problemas que puede detectar

| Tipo de problema | Ejemplo | Riesgo |
|------------------|----------|--------|
| **Complejidad excesiva** | Función con más de 10 condicionales anidadas. | Difícil de mantener. |
| **Código muerto** | Variables o funciones declaradas pero nunca usadas. | Ocupa memoria y confunde. |
| **Funciones largas** | Método con más de 100 líneas. | Violación del principio SRP. |
| **Nombres poco descriptivos** | Variable `a` o `temp`. | Pérdida de legibilidad. |
| **Código duplicado** | Misma lógica en varios archivos. | Riesgo de errores al modificar. |
| **Uso incorrecto de coroutines o null-safety** | Falta de control en `launch` o `!!`. | Posibles excepciones en tiempo de ejecución. |

---

## Cómo se integra en un proyecto Android o Kotlin

Detekt se ejecuta normalmente como parte del proceso de **build con Gradle**.  
Se instala añadiendo un plugin al archivo `build.gradle`:

```kotlin
plugins {
    id("io.gitlab.arturbosch.detekt") version "1.23.1"
}
```

## En resumen  

> Android Lint es el analizador de calidad integrado en el ecosistema Android.  
> Permite detectar errores de rendimiento, seguridad, accesibilidad y compatibilidad antes de ejecutar la app.  
> Su uso reduce errores en producción, mejora la experiencia del usuario y facilita el mantenimiento del código.  