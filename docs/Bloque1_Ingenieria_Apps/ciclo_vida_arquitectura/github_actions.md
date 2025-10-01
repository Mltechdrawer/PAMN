# GitHub Actions

## ¿Qué es GitHub Actions?

**GitHub Actions** es una herramienta integrada en GitHub que permite **automatizar flujos de trabajo** directamente en los repositorios. Se basa en la idea de ejecutar tareas desencadenadas por eventos (como *push*, *pull request*, creación de *issues*, etc.) usando **workflows** definidos en ficheros YAML.

Con GitHub Actions puedes construir, probar, desplegar y mantener tu proyecto sin necesidad de servicios externos.

---

## Beneficios principales

- **Integración nativa con GitHub**: no necesitas configurar servicios adicionales.  
- **Automatización de procesos repetitivos**: pruebas, compilaciones, despliegues, etc.  
- **Soporte multiplataforma**: ejecuta jobs en Linux, Windows y macOS.  
- **Flexibilidad**: puedes crear tus propios *actions* o reutilizar los que comparte la comunidad.  
- **Escalabilidad**: ideal tanto para proyectos pequeños como para grandes entornos de desarrollo.  

---

## Rol en CI/CD

GitHub Actions se utiliza para implementar **Integración Continua (CI)** y **Despliegue Continuo (CD)**:

- **CI (Continuous Integration)**: cada vez que un desarrollador sube cambios, se ejecutan automáticamente compilaciones y pruebas para verificar que el código sigue funcionando.  
- **CD (Continuous Deployment/Delivery)**: después de pasar las pruebas, los cambios pueden desplegarse automáticamente en entornos de pruebas o producción.  

Esto garantiza una entrega más rápida y confiable del software.

---

## ¿Para qué se utiliza?

- **Compilar código automáticamente** después de cada *push*.  
- **Ejecutar tests unitarios y de integración** en distintos entornos.  
- **Generar artefactos** (ejecutables, librerías, documentación).  
- **Desplegar aplicaciones** en servidores, contenedores Docker o servicios en la nube.  
- **Automatizar tareas de mantenimiento** como cerrar *issues*, etiquetar *releases* o enviar notificaciones.  

---

## ¿Cómo se utiliza?

Los workflows de GitHub Actions se definen en ficheros YAML dentro de la carpeta:

```
.github/workflows/
```

Cada workflow está compuesto por:

- **Eventos**: disparadores que inician el flujo (ej. `push`, `pull_request`).  
- **Jobs**: tareas que se ejecutan, cada una en una máquina virtual.  
- **Steps**: pasos dentro de un *job* (instalar dependencias, ejecutar scripts, etc.).  
- **Actions**: comandos predefinidos o personalizados reutilizables.  

---

## Ejemplos prácticos

### Ejemplo 1: Compilar y ejecutar pruebas en Java

```yaml
name: Java CI

on: [push, pull_request]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v3
    - name: Set up JDK 17
      uses: actions/setup-java@v3
      with:
        java-version: '17'
        distribution: 'temurin'
    - name: Build with Maven
      run: mvn -B package --file pom.xml
    - name: Run tests
      run: mvn test
```

### Ejemplo 2: Desplegar en GitHub Pages

```yaml
name: Deploy to GitHub Pages

on:
  push:
    branches: [ main ]

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v3
    - name: Build static site
      run: |
        npm install
        npm run build
    - name: Deploy
      uses: peaceiris/actions-gh-pages@v3
      with:
        github_token: ${{ secrets.GITHUB_TOKEN }}
        publish_dir: ./dist
```

---

[Gestión de GitHub Actions](github_actions_gestion.md)

[Kotlin](github_actions_kotlin.md)

## Conclusión

GitHub Actions convierte a GitHub en una **plataforma completa de desarrollo**, donde además de almacenar el código puedes compilarlo, probarlo y desplegarlo automáticamente. Facilita la colaboración en equipo, reduce errores humanos y acelera la entrega de software gracias a la integración CI/CD.

[Despliegue de Aplicaciones Móviles](workflows_mobile_ci.md)

[Despliegue Comentados](workflows_mobile_ci_commented.md)
