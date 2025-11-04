# Configuración de Ktlint como pre-commit hook


Pasos para configurar **ktlint** de tal forma que verifique el estilo del código **antes de cada commit**, evitando que se suba código con errores de formato.

---

## Objetivo
Evitar que se realicen commits si el código no cumple las reglas de estilo de **ktlint**.

---

## 1 — Crear la carpeta de hooks (si no existe)

```bash
mkdir -p .git/hooks
```

---

## 2 — Crear el hook `pre-commit`

Crea el archivo:

```
.git/hooks/pre-commit
```

Contenido del archivo:

```bash
#!/bin/bash

echo "→ Ejecutando ktlint antes del commit..."

./gradlew ktlintCheck
RESULT=$?

if [ $RESULT -ne 0 ]; then
  echo "ktlint ha encontrado problemas de estilo."
  echo "Solución rápida: ejecuta './gradlew ktlintFormat' para corregir automáticamente."
  exit 1
fi

echo "Estilo correcto. Procediendo con el commit."
exit 0
```

---

## 3 — Dar permisos de ejecución

```bash
chmod +x .git/hooks/pre-commit
```

---

## 4 — Probar la configuración

1. Introduce un error de estilo a propósito (por ejemplo, espacios de más).
2. Intenta hacer commit:

```bash
git add .
git commit -m "probando hook"
```

Si hay errores, verás:

```
ktlint ha encontrado problemas de estilo.
```

Para corregir automáticamente:

```bash
./gradlew ktlintFormat
```

Reintenta el commit:

```bash
git commit -m "estilo corregido"
```

---

## Resultado esperado

| Situación | Comportamiento |
|----------|----------------|
| Existen errores de estilo | El commit se bloquea |
| Estilo correcto | El commit continúa |
