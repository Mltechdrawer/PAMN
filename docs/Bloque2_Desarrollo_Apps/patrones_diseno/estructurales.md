# Patrones de diseño estructurales

## Adapter
**Qué es**  
Permite que **dos interfaces incompatibles** trabajen juntas mediando con un adaptador.

**Cuándo usarlo en móvil**
- Integrar una API externa con el **modelo de dominio** de la app.
- Envolver SDKs de terceros para aislarlos de la UI o del *core*.
- Migraciones graduales de una interfaz antigua a otra nueva.

[Adapter](patrones.md#code-adapter)

<details>
<summary>Ejemplo breve</summary>
`UserApiAdapter` convierte la respuesta de un SDK externo a `UserDomain`. La UI solo trata con el dominio; si cambias de proveedor, sustituyes el adaptador.
</details>

---

## Decorator
**Qué es**  
Añade **funcionalidades** a un objeto **dinámicamente** sin modificar su clase.

**Cuándo usarlo en móvil**
- Extender comportamientos (p. ej., añadir *logging*, *retry*, caché) a un repositorio.
- Componer **filtros de imagen**, validaciones o *interceptors* de red.
- Personalizar componentes UI sin proliferar subclases.

[Decorator](patrones.md#code-decorator)

<details>
<summary>Ejemplo breve</summary>
`RepositoryWithCache` envuelve a `RemoteRepository` y primero consulta caché. Puedes encadenar otro decorador de *retry* sin tocar la implementación base.
</details>

---

## Facade
**Qué es**  
Proporciona una **interfaz simple** a un **subsistema complejo** (varios módulos/SDKs).

**Cuándo usarlo en móvil**
- Unificar en un punto `Auth`, base de datos y red para operaciones de usuario.
- Simplificar flujos de inicio de sesión/registro con varios proveedores.
- Reducir dependencias de la UI hacia detalles de infraestructura.

[Facade](patrones.md#code-facade)

<details>
<summary>Ejemplo breve</summary>
`UserServiceFacade` expone `login(email, pass)` y por debajo coordina `AuthSDK`, `TokenStorage` y `UserApi`. La UI consume un método único.
</details>
