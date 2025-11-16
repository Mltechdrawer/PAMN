
# Integración de Datos de Sensores

La integración de datos es el proceso mediante el cual se combinan lecturas procedentes de múltiples sensores o fuentes heterogéneas para obtener información más precisa, robusta y útil que la que cada sensor podría proporcionar por separado.

## Objetivos de la integración de datos
- Reducir ruido y mejorar la calidad de las mediciones.
- Resolver inconsistencias entre sensores.
- Sincronizar señales con diferentes frecuencias de muestreo.
- Obtener nuevas variables derivadas.
- Incrementar la fiabilidad en condiciones reales.

## Tipos de integración

### Fusión a nivel de datos
Se combinan directamente las señales crudas antes de procesarlas.

### Fusión a nivel de características
Los datos se procesan primero para extraer características y luego se combinan.

### Fusión a nivel de decisión
Cada sensor o modelo genera una predicción, y luego se fusionan decisiones.

## Sincronización temporal
Sensores diferentes muestrean a frecuencias distintas. Estrategias:
- Interpolación
- Resampling
- Normalización de timestamps

## Ejemplos de integración
### Posicionamiento híbrido
GPS + Wi-Fi + BLE

### Orientación del dispositivo
Acelerómetro + giroscopio + magnetómetro

### Bioinformática y salud móvil
Acelerómetro + PPG para limpiar artefactos de movimiento.

## Retos
- Ruido y deriva
- Latencias
- Diferente fiabilidad entre sensores
- Coste computacional

