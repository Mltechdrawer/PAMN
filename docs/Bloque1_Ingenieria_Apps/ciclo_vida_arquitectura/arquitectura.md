# Arquitectura de Aplicaciones Móviles

La arquitectura de una aplicación móvil define la organización interna de sus componentes y cómo se comunican entre sí. Constituye el esqueleto que sostiene el desarrollo y mantenimiento de la app, asegurando escalabilidad, calidad y facilidad de evolución. En el contexto de PAMN, comprender la arquitectura es fundamental para formar profesionales capaces de crear aplicaciones robustas y alineadas con las buenas prácticas de la industria.

## Objetivos de la arquitectura

- Proporcionar un marco estructurado para el desarrollo.  
- Asegurar la separación de responsabilidades.  
- Facilitar la escalabilidad y la mantenibilidad.  
- Simplificar la integración de nuevas funcionalidades.  
- Reforzar la seguridad y el control del proyecto.  

## ¿Por qué es importante?

Una buena arquitectura:

- **Estructura**: Una arquitectura sólida proporciona un esqueleto robusto para el Proyecto, asegurando que cada componente encaje de manera coherente y eficiente. 

![Estructura](arq_estructura.png "Estructura")

- **Planificación**: Facilita la toma de decisiones anticipadas, permitiendo evitar posibles obstáculos y puntos críticos en el desarrollo. 

![Planificación](arq_planificacion.png "Planificación")

- **Control**: Con una arquitectura bien definida, es  más sencillo monitorizar el progreso, identificar desviaciones  y garantizar que el Proyecto se mantenga en el camino correcto.

![Control](arq_control.png "Control") 

- **Optimización del proceso**: Una  buena arquitectura simplifica la integración de nuevas características y facilita las actualizaciones, asegurando la escalabilidad y adaptabilidad del Proyecto a largo plazo. 

![Optimización](arq_optimizacion.png "Optimización") 

- **Separación de capas**: Consiste en dividir un sistema en capas o módulos independientes, cada uno con una responsabilidad específica. Podemos distinguir entre presentación, lógica de negocio y gestión de datos.

![Separación de capas](arq_capas.png "Capas") 


## Modelos de arquitectura más utilizados

### 1. MVC (Modelo–Vista–Controlador)

Separa la app en tres componentes principales: modelo, vista y controlador. 

- **Modelo**: Datos y lógica de negocio.  
- **Vista**: Interfaz gráfica.  
- **Controlador**: Actúa como intermediario, manejando la entrada del usuario y actualizando tanto la vista como el modelo.  

Ventajas: La separación clara entre la lógica de la interfaz de usuario y la lógica de negocio.  
Inconvenientes: El controlador tiende a sobrecargarse en proyectos grandes. La vista y el controlador suelen estar más acoplados en en otras arquitecturas. 

![MVC](arq_mvc.png)

### 2. MVP (Modelo–Vista–Presentador)

Separa la lógica de presentación de la lógica de negocio.

- **Modelo**: Mantiene datos y reglas de negocio.  
- **Vista**: Muestra información, delega lógica.  
- **Presentador**: Gestiona la lógica de negocio y actualiza la vista.  

Ventajas: La separación clara entre la lógica de la interfaz de usuario y la lógica de negocio. Facilita una alta testabilidad.  
Inconvenientes: Puede generar código más complejo.  

![MVP](arq_mvp.png)

### 3. MVVM (Modelo–Vista–VistaModelo)

Arquitectura que utiliza un modelo de vista para gestionar la lógica de la aplicación.

- **Modelo**: Gestiona datos y lógica de negocio.  
- **Vista**: Muestra la interfaz y se comunica con la vista modelo.  
- **VistaModelo**: Maneja la lógica de presentación y comunica vista y modelo.  

Ventajas: menor acoplamiento y compatible con programación reactiva.  

![MVVM](arq_mvvm.png)

 **[Modelo–Vista–VistaModelo (MVVM)](mvvm.md)**.

<details>
<summary> Diferencias entre MVC, MVP y MVVM</summary>
<p><b>1. MVC (Model–View–Controller)</b></p>
<ul>
  <li><b>Controlador:</b> actúa como intermediario entre la vista y el modelo.</li>
  <li>Recibe la interacción del usuario, decide qué hacer y actualiza el modelo o la vista.</li>
  <li>La vista suele tener cierta lógica, por lo que no está totalmente aislada.</li>
  <li><i>Problema:</i> en apps complejas, el controlador tiende a crecer demasiado (Massive View Controller).</li>
</ul>
<p><b>2. MVP (Model–View–Presenter)</b></p>
<ul>
  <li><b>Presentador:</b> sustituye al controlador de MVC.</li>
  <li>Se encarga de la lógica de presentación: recibe eventos de la vista, consulta/actualiza el modelo y devuelve datos preparados.</li>
  <li>La vista queda más pasiva, mostrando únicamente lo que el presentador indica.</li>
  <li><i>Ventaja:</i> mejor separación de responsabilidades, vista más simple.</li>
</ul>
<p><b>3. MVVM (Model–View–ViewModel)</b></p>
<ul>
  <li><b>Vista-Modelo:</b> no conoce directamente la vista; expone datos observables (LiveData, StateFlow, etc.).</li>
  <li>La vista se suscribe a esos datos y se actualiza automáticamente cuando cambian.</li>
  <li>La lógica de negocio de la UI se coloca en el ViewModel, y la vista solo reacciona.</li>
  <li><i>Ventaja:</i> fuerte desacoplamiento y arquitectura reactiva, ideal para data binding.</li>
</ul>
<p><b> Diferencia clave:</b></p>
<ul>
  <li><b>MVC:</b> el controlador actualiza vista y modelo directamente (más acoplamiento).</li>
  <li><b>MVP:</b> el presentador orquesta y la vista solo pinta (vista pasiva).</li>
  <li><b>MVVM:</b> el ViewModel expone estado observable y la vista reacciona automáticamente (desacoplamiento máximo).</li>
</ul>
</details>


### 4. MVI (Modelo–Vista–Intención)

Se centra en la intención del usuario y utiliza un flujo de datos unidireccional.

- **Modelo**: Datos y lógica de negocio.  
- **Vista**: Muestra el estado actual de la aplicación.  
- **Intenciones**: Representa las intenciones o acciones del usuario.   

Ventaja principal: Enfoque cíclico y reactivo que maneja el flujo de datos y eventos.  

![MVI](arq_mvi.png)

### 5. Arquitectura Hexagonal

Divide la app en capas:

- **Núcleo**: Dominio que representa la lógica de negocio.  
- **Aplicación**: Maneja los casos de uso.  
- **Infraestructura**: Maneja los adaptadores de infraestructura, incluyendo los controladores (adaptadores que manejan la interfaz de usuario).  

Los adaptadores se conectan a los puertos.
El diagrama sigue el principio de desacoplamiento de la arquitectura hexagonal. La lógica de negocio está completamente aislada de los detalles de implementación externa (infraestructura, tecnologías). Las interacciones entre la aplicación y el mundo exterior se realizan a través de puertos y adaptadores, lo que garantiza que el núcleo del sistema sea independiente de la infraestructura.
Este patrón facilita el cambio de tecnologías externas sin modificar el núcleo, promueve la testabilidad y permite que cada componente sea mantenido y desarrollado de manera independiente.

![Arquitectura Hexagonal](arq_hex.png)

## Factores para elegir la arquitectura. Consideraciones clave.

- **Requerimientos**: Entender las necesidades específicas y objetivos del proyecto para determinar qué arquitectura se alinea mejor.
- **Rendimiento**: Evaluar cómo cada arquitectura puede influir en la velocidad, respuesta y eficiencia del sistema.
- **Integración con otros Sistemas**: Si el proyecto necesita interactuar con otros sistemas o aplicaciones, la arquitectura debe facilitar estas integraciones.
- **Recursos**: Evaluar la inversión necesaria en términos de tiempo, dinero y recursos humanos para implementar y mantener
- **Seguridad**: Garantizar que la arquitectura elegida ofrezca las medidas de seguridad adecuadas para proteger datos y procesos.
- **Facilidad de mantenimiento**: La facilidad con la que se pueden hacer cambios, correcciones o mejoras en el sistema a lo largo del tiempo.

## Estructurando aplicaciones.

![Tabla estructura](arq_estr1.png)

![Tabla estructura](arq_estr2.png)


## Relación con DevOps

La arquitectura móvil está alineada con la filosofía DevOps, ya que:  

- Favorece la automatización de pruebas e integración continua.  
- Permite despliegues más rápidos y seguros.  
- Mejora la observabilidad en producción.  
- Facilita la escalabilidad de las aplicaciones.  

## Conclusiones

- La arquitectura es el pilar central de una app móvil.  
- Existen múltiples patrones (MVC, MVP, MVVM, MVI, Hexagonal), cada uno con pros y contras.  
- La elección depende de los objetivos, recursos y contexto del proyecto.  
- Una arquitectura alineada con DevOps asegura calidad y entrega continua.  