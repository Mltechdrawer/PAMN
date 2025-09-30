# Archivos `.yml` (YAML)

## ¿Qué es un archivo `.yml`?

Un archivo `.yml` (o `.yaml`) está escrito en **YAML** (*YAML Ain’t Markup Language*), un formato de serialización de datos diseñado para ser **legible por humanos**.  
Se utiliza ampliamente en desarrollo de software para **definir configuraciones**, de forma jerárquica y estructurada, mediante **sangrías** en lugar de llaves o corchetes (a diferencia de JSON o XML).

Ejemplo básico de un `.yml`:

```yaml
nombre: "María"
edad: 25
habilidades:
  - Java
  - Python
  - SQL
activo: true
```

---

## Características principales de YAML

- Usa **sangría** para estructurar jerarquías (en lugar de `{}` o `[]`).  
- Soporta tipos de datos comunes: cadenas, números, booleanos, listas y diccionarios.  
- Es fácil de leer y escribir para humanos.  
- Es ampliamente soportado en frameworks, compiladores, intérpretes y sistemas de automatización.  

---

## Uso en GitHub Actions

En **GitHub Actions**, los archivos `.yml` definen los **workflows** dentro de `.github/workflows/`.  

Ejemplo: un flujo para compilar y ejecutar pruebas en Java:

```yaml
name: Java CI

on: [push, pull_request]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Set up JDK
        uses: actions/setup-java@v3
        with:
          java-version: '17'
      - name: Build with Maven
        run: mvn package
```

Aquí, YAML estructura el flujo de trabajo mediante claves (`jobs`, `steps`, `uses`, `run`) que GitHub interpreta para ejecutar acciones.

---

## Uso en GitHub (fuera de Actions)

Además de Actions, GitHub usa `.yml` en otros contextos, por ejemplo:

- **Dependabot** (`.github/dependabot.yml`): gestiona actualizaciones automáticas de dependencias.  
- **CodeQL** (`.github/codeql.yml`): define análisis de seguridad.  

---

## Uso en compiladores, intérpretes y otras herramientas

Los archivos `.yml` no se limitan a GitHub. Muchos sistemas los utilizan como archivos de configuración:

- **Docker Compose**: define servicios, redes y volúmenes de contenedores.  
  ```yaml
  version: '3'
  services:
    web:
      image: nginx
      ports:
        - "80:80"
  ```
- **Kubernetes**: describe despliegues y servicios en un clúster.  
- **CI/CD externos**: como Travis CI o CircleCI.  
- **Frameworks**: Django, Spring Boot y muchos otros permiten configurar variables con `.yml`.  

---

## Conclusión

Los archivos `.yml` son una herramienta esencial en el ecosistema del desarrollo moderno porque permiten describir **configuración compleja en un formato sencillo y legible**.  
En el caso de GitHub Actions, son el pilar para definir los **workflows de CI/CD**; en otros entornos, son igualmente útiles para **configuración de entornos, despliegue de aplicaciones o integración con servicios externos**.
