# Despliegue de Aplicaciones  

---

# 1. Introducción al Despliegue de Aplicaciones

El despliegue es la fase del ciclo de vida del software en la que una aplicación, tras pasar por desarrollo y pruebas, se prepara para ser distribuida a los usuarios finales. En el contexto móvil, desplegar una aplicación implica empaquetarla, firmarla digitalmente y distribuirla a través de mecanismos oficiales (Google Play, App Store) o privados.

### 1.1. Build, Release y Deployment
- **Build**: proceso técnico de compilación y generación del paquete (APK, AAB, IPA).  
- **Release**: versión de la aplicación que se prepara para distribuir.  
- **Deployment (despliegue)**: acción de poner la aplicación disponible para los usuarios.

### 1.2. Retos del despliegue móvil
- Fragmentación de dispositivos.  
- Permisos sensibles y políticas estrictas.  
- Procesos de revisión en tiendas.  
- Compatibilidad entre versiones del sistema operativo.  
- Necesidad de automatización mediante CI/CD.

---

# 2. Modelos de despliegue en aplicaciones móviles

## 2.1. Despliegue local (sideloading)
Utilizado en fases de desarrollo y pruebas internas.

Características:
- Instalación directa mediante USB o servicios internos.
- No requiere revisión de tiendas.
- Ideal para QA, demos internas o pruebas rápidas.

Limitaciones:
- Instalación restringida en iOS sin perfiles adecuados.
- No apto para usuarios finales.

---

## 2.2. Despliegue mediante tiendas oficiales

### Android – Google Play Store
- Revisión técnica rápida.  
- Control de versiones y etapas de despliegue.  
- Analíticas integradas.

### iOS – Apple App Store
- Revisión manual estricta.  
- Políticas muy detalladas.  
- Uso obligatorio para distribución pública.

Ventajas:
- Seguridad para los usuarios.  
- Canal oficial de actualización.  
- Estadísticas, pruebas y control de errores integrados.

---

## 2.3. Despliegue corporativo o privado
Útil para empresas o instituciones.

Modalidades:
- MDM (Mobile Device Management).  
- Distribución Ad-Hoc.  
- Distribución Enterprise.

Ventajas:
- Control total sobre quién instala la app.  
- No requiere publicación pública.

---

# 3. Construcción y empaquetado de aplicaciones móviles

## 3.1. Proceso de build
Incluye:
- Compilación del código.  
- Procesamiento de recursos.  
- Ofuscación y optimización.  
- Generación del paquete final.

Tipos de build:
- **Debug**: más rápido, logs habilitados.  
- **Release**: optimizado, firmado, apto para distribución.

---

## 3.2. Firmas digitales
Las aplicaciones móviles deben estar firmadas digitalmente.

### Android
- Firma con **keystore** (clave privada).
- Distintas claves para desarrollo y producción.

### iOS
- Necesita certificados emitidos por Apple.
- Perfiles de aprovisionamiento que autorizan dispositivos o distribución.

Propósitos:
- Seguridad.  
- Identidad del desarrollador.  
- Garantía de integridad.

---

## 3.3. Versionado
Es esencial para organizar actualizaciones y compatibilidad.

Elementos:
- **Nombre de versión** (visible para el usuario).  
- **Código de versión** (entero incremental).  

Estrategias:
- Versionado semántico (major.minor.patch).  
- Versiones alfa, beta y estables.

---

# 4. Despliegue en Android

## 4.1. Google Play Console
La plataforma oficial de publicación en Android.

Funcionalidades:
- Subida de paquetes AAB.  
- Gestión de testers internos, cerrados y abiertos.  
- Publicaciones escalonadas.  
- Estadísticas de rendimiento y errores.

---

## 4.2. Formatos de distribución

### APK
Paquete clásico de Android.  
Se puede instalar directamente.

### AAB (Android App Bundle)
Formato moderno recomendado:
- Reduce tamaño.  
- Google genera APKs específicos por dispositivo.  
- Requisito obligatorio para Play Store.

---

## 4.3. Revisión y políticas
Causas frecuentes de rechazo:
- Permisos sensibles sin justificación.  
- Uso indebido de datos personales.  
- Contenido inapropiado o no declarado.  
- Problemas técnicos de estabilidad.

---

# 5. Despliegue en iOS

## 5.1. Certificados y perfiles de aprovisionamiento

Tipos de certificados:
- Desarrollo  
- Distribución  

Tipos de perfiles:
- Development  
- Ad Hoc  
- Enterprise  
- App Store  

Controlan dónde puede instalarse la app.

---

## 5.2. TestFlight
Sistema oficial de pruebas.

Permite:
- Testing interno y externo.  
- Recopilar feedback.  
- Gestionar versiones antes de publicar.

---

## 5.3. App Store Connect
Portal de publicación en iOS.

Incluye:
- Subida del binario IPA.  
- Gestión de metadatos, capturas y descripciones.  
- Proceso de revisión manual.  
- Estadísticas y estado de la publicación.

Motivos comunes de rechazo:
- Incumplimiento de normas de privacidad.  
- Bugs, errores o cierres inesperados.  
- Navegación confusa o contenido poco claro.

---

# 6. Automatización del despliegue

## 6.1. CI/CD en aplicaciones móviles
Automatizar el ciclo de desarrollo reduce errores y agiliza publicaciones.

Fases típicas:
1. Compilación automática.  
2. Ejecución de tests.  
3. Generación de artefactos.  
4. Firma automática.  
5. Publicación en tiendas o TestFlight/Play Console.

---

## 6.2. Herramientas comunes
- **Fastlane**: firma, capturas, publicación automatizada.  
- **GitHub Actions**: pipelines simples y flexibles.  
- **GitLab CI**: integración completa.  
- **Bitrise / Codemagic**: especializadas en apps móviles.  
- **Jenkins**: pipelines personalizables.

---

# 7. Estrategias de despliegue y publicación

## 7.1. Publicación escalonada
Permite liberar la app a un porcentaje de usuarios progresivamente.

Ventajas:
- Detección temprana de fallos.  
- Reducción del impacto en caso de errores.

---

## 7.2. Canary releases
La app se despliega para un grupo reducido muy específico.  
Se usa para validar nuevas funciones en entornos reales.

---

## 7.3. Feature flags
Permiten activar o desactivar funcionalidades sin publicar una nueva versión.

Usos:
- A/B testing.  
- Activación gradual de funciones.  
- Control de fallos temporal.

---

# 8. Monitorización, analítica y validación post-despliegue

## 8.1. Monitorización
Se analiza el comportamiento real de la app mediante:
- Rendimiento.  
- Consumo de memoria.  
- Cierres inesperados.

---

## 8.2. Analítica
Información sobre:
- Pantallas más usadas.  
- Flujo de navegación.  
- Retención de usuarios.  
- Efecto de nuevas características.

---

## 8.3. Validación tras publicar
Incluye:
- Pruebas rápidas en dispositivos reales.  
- Comprobación de permisos y seguridad.  
- Revisión de logs y errores.  
- Plan de rollback si es necesario.

---

# 9. Seguridad en el proceso de despliegue

Aspectos clave:
- Proteger claves de firma (almacenamiento seguro).  
- Aislar secretos en pipelines CI/CD.  
- Evitar exponer credenciales o APIs.  
- Minimizar riesgo de ingeniería inversa mediante ofuscación.

---

# 10. Buenas prácticas y errores comunes

### Buenas prácticas:
- Usar entornos separados: desarrollo, pruebas, producción.  
- Documentar cifras de cambio (*release notes*).  
- Aplicar despliegues progresivos.  
- Mantener control de versiones.

### Errores comunes:
- Publicar sin pruebas reales.  
- Mezclar certificados de desarrollo y producción.  
- No controlar permisos sensibles.  
- No monitorizar después del despliegue.

---

