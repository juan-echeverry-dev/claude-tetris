---
name: weather
description: Obtiene el clima actual y el pronóstico para una ciudad usando wttr.in (sin API key). Úsalo cuando el usuario pida el clima, temperatura, pronóstico o condiciones climáticas de un lugar.
---

# Weather

Consulta el clima directamente desde la terminal usando el servicio público `wttr.in`, que no requiere API key ni configuración previa.

## Cuándo usar esta skill

- El usuario pregunta por el clima, temperatura, humedad, viento o pronóstico de una ciudad.
- El usuario pide un resumen rápido del clima de hoy o de los próximos días.

## Cómo obtener el clima

1. Pide o infiere la ciudad (si el usuario no la da, pregunta cuál ciudad quiere consultar).
2. Ejecuta el script correspondiente a tu shell, pasando la ciudad como argumento:

   - **PowerShell**:
     ```powershell
     .claude/skills/weather/scripts/get_weather.ps1 -City "Bogota"
     ```
   - **Bash**:
     ```bash
     bash .claude/skills/weather/scripts/get_weather.sh "Bogota"
     ```

3. El script imprime un reporte de texto plano con el clima actual y el pronóstico de 3 días.
4. Resume el resultado para el usuario en español, destacando: condición actual, temperatura, sensación térmica, y si aplica, la probabilidad de lluvia para hoy.

## Notas

- Si el nombre de la ciudad tiene espacios, pásalo entre comillas.
- Si `wttr.in` no responde (sin conexión a internet o el servicio está caído), informa al usuario que no se pudo obtener el clima y sugiere reintentar más tarde.
- Para obtener solo un dato puntual (ej. solo temperatura), se puede pasar un formato personalizado con `-Format`/`--format` (ver comentarios dentro de los scripts).
