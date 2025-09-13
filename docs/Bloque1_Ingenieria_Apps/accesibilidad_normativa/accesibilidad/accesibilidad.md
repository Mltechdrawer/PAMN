# Accesibilidad en Aplicaciones Web y Móviles

La accesibilidad es un aspecto fundamental en el desarrollo de aplicaciones web y móviles. No solo garantiza la inclusión de personas con diversidad funcional, sino que también mejora la usabilidad general de los sistemas y amplía su alcance a un público más diverso. Diseñar con criterios de accesibilidad implica cumplir con principios universales que faciliten la interacción para todos los usuarios.

![Accesibilidad web y móvil](accesibilidad.png "Accesibilidad web y móvil")

## Ben Shneiderman y las Ocho Reglas de Oro

Ben Shneiderman es un informático estadounidense. Su investigación principal está relacionada con la Interacción Persona-ordenador. Destaca por ser un pionero en el campo de la interacción persona–ordenador (HCI). Su trabajo ha sido una referencia clave en el diseño de interfaces centradas en el usuario. En colaboración con Catherine Plaisant, desarrolló un marco conceptual que ayuda a guiar la construcción de interfaces más usables y accesibles. Una de sus contribuciones más destacadas son las **Ocho Reglas de Oro del Diseño de Interfaces**.

### 1. Esforzarse por la consistencia
Los elementos de la interfaz deben mantener un comportamiento uniforme en toda la aplicación, facilitando el aprendizaje y reduciendo errores.
<details>
<summary> Detalles</summary>
<p>❌ Una aplicación de banca móvil en la que el botón de “Aceptar” aparece a veces en verde en la esquina inferior derecha y en otras pantallas en rojo en la parte superior. El usuario nunca sabe dónde buscarlo ni qué significa el color</p>
<p>✔ En Gmail, los botones de acción (Redactar, Enviar, Eliminar) mantienen siempre el mismo diseño, color y posición, lo que facilita que los usuarios sepan dónde encontrarlos sin pensar.</p>
</details>
  
<!-- Imagen sugerida: ejemplo de menús o botones consistentes -->

### 2. Permitir atajos para usuarios frecuentes
El sistema debe ofrecer caminos rápidos para usuarios expertos (como atajos de teclado o gestos) sin sacrificar la facilidad de uso para principiantes.
<details>
<summary> Detalles</summary>
<p>❌ Un editor de texto que solo permite copiar y pegar desde el menú desplegable, sin admitir atajos de teclado como Ctrl+C / Ctrl+V. Los usuarios avanzados pierden tiempo constantemente. </p>
<p>✔ En Microsoft Word, además de usar los menús, los usuarios avanzados pueden utilizar atajos de teclado como Ctrl+B para poner en negrita o Ctrl+Z para deshacer, acelerando mucho su trabajo.</p>
</details>

<!-- Imagen sugerida: ilustración de atajos de teclado o gestos táctiles -->

### 3. Ofrecer retroalimentación informativa
Cada acción del usuario debe tener una respuesta clara e inmediata que confirme su ejecución. 

<details>
<summary> Detalles</summary>
<p>❌ Un formulario online en el que, al pulsar “Enviar”, no aparece ningún mensaje ni indicador de carga. El usuario no sabe si el sistema está procesando los datos, si hubo un error o si ya se enviaron </p>
<p>✔ En WhatsApp, cuando envías un mensaje aparece primero un check (enviado), luego dos checks (recibido) y más tarde en azul (leído). El usuario sabe en todo momento qué ha pasado con su mensaje.</p>
</details>
 
<!-- Imagen sugerida: mensaje de confirmación tras una acción -->

### 4. Diseñar diálogos que produzcan cierre
Las interacciones deben estructurarse con un inicio, un desarrollo y un fin reconocible, dando al usuario sensación de control y finalización. 

<details>
<summary> Detalles</summary>
<p>❌ Un proceso de compra en línea que finaliza con un botón “Continuar” pero nunca muestra una pantalla de confirmación del pedido. El usuario queda con la duda de si la compra fue exitosa. </p>
<p>✔ En Amazon, después de finalizar la compra, aparece una página de confirmación con el número de pedido y un correo electrónico de resumen. El usuario queda seguro de que su acción terminó con éxito.</p>
</details>
 
<!-- Imagen sugerida: ejemplo de asistente paso a paso (wizard) -->

### 5. Ofrecer prevención y manejo de errores simples
El sistema debe minimizar la posibilidad de errores y, si ocurren, ofrecer mecanismos claros para resolverlos. 

<details>
<summary> Detalles</summary>
<p>❌ Un sistema que obliga a introducir la fecha de nacimiento en formato “DD/MM/AAAA” sin validación, y al escribir “1/1/24” muestra un error genérico: “Formato inválido”, sin explicar qué está mal ni cómo corregirlo. </p>
<p>✔ En Google Forms, si intentas enviar un formulario sin rellenar un campo obligatorio, aparece un aviso rojo bajo ese campo explicando qué falta, y no permite continuar hasta solucionarlo.</p>
</details>
 
<!-- Imagen sugerida: cuadro de error con mensaje explicativo y solución -->

### 6. Permitir deshacer acciones
Es fundamental que los usuarios tengan la posibilidad de corregir sus acciones mediante funciones de “deshacer” o “cancelar”.

<details>
<summary> Detalles</summary>
<p>❌ Una aplicación de edición de imágenes que, al aplicar un filtro, reemplaza la foto original sin opción de “deshacer” ni recuperar la versión previa. </p>
<p>✔ En Gmail, al borrar un correo aparece durante unos segundos el botón “Deshacer”, que permite recuperar de inmediato el mensaje eliminado por error.</p>
</details>
  
<!-- Imagen sugerida: botón “deshacer” en un editor -->

### 7. Apoyar el control interno del usuario
El sistema debe dar la sensación de que el usuario controla la interacción, evitando procesos automáticos inesperados.

<details>
<summary> Detalles</summary>
<p>❌ Una web que automáticamente abre un vídeo a pantalla completa con sonido sin pedir permiso al usuario, interrumpiendo lo que estaba haciendo.  </p>
<p>✔ En YouTube, si inicias un vídeo, siempre puedes pausarlo, saltar hacia adelante, ajustar el volumen o desactivar la reproducción automática. El usuario decide cómo y cuándo interactuar..</p>
</details>

 
<!-- Imagen sugerida: pantalla con opción de confirmación antes de ejecutar -->

### 8. Reducir la carga de memoria a corto plazo
La interfaz debe apoyarse en la memoria de reconocimiento antes que en la de recuerdo, utilizando menús, íconos y ayudas visuales para reducir el esfuerzo cognitivo. 

<details>
<summary> Detalles</summary>
<p>❌ Un sistema de reservas que pide al usuario memorizar un código de 12 dígitos mostrado en una pantalla, y luego escribirlo manualmente en la siguiente sin ofrecer copiar/pegar ni mostrarlo de nuevo.  </p>
<p>✔ En los navegadores web, al introducir un nombre de usuario, aparece la lista de autocompletado con los datos previamente guardados. Así el usuario no necesita memorizar ni volver a escribirlos cada vez.</p>
</details>

<!-- Imagen sugerida: menú con íconos fácilmente reconocibles -->

Las Ocho Reglas de Oro de Ben Shneiderman aparecen en su libro “Designing the User Interface: Strategies for Effective Human-Computer Interaction”.

![Designing the User Interface](designing.png "Designing the User Interface: Strategies for Effective Human-Computer Interaction")

A lo largo de las ediciones posteriores (ya van por la 6ª edición, 2016, junto con Catherine Plaisant, Maxine Cohen y Steven Jacobs) las reglas se han mantenido como un marco básico de usabilidad, aunque complementadas con nuevos principios de diseño, accesibilidad y experiencia de usuario.
