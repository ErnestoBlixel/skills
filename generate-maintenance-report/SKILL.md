---
name: generate-maintenance-report
description: Genera informes mensuales de mantenimiento en HTML profesional para clientes de Blixel AI. Incluye secciones de tareas completadas, en curso y pendientes. El informe se genera a partir de datos de Notion y se convierte a PDF. Usar cuando el usuario pida generar un informe, reporte o resumen de mantenimiento mensual.
---

# Generador de Informes de Mantenimiento

Skill para generar informes mensuales de mantenimiento técnico en formato HTML autocontenido, optimizado para conversión a PDF via Gotenberg.

## Contexto

Este skill se usa dentro del workflow automatizado de Blixel AI que:
1. Extrae tareas de Notion (completadas, en curso, pendientes)
2. Obtiene facturas pendientes de Holded
3. Genera el informe HTML con IA
4. Convierte a PDF con Gotenberg
5. Crea borrador en Gmail con factura + informe adjuntos
6. Envía notificación interna para revisión

## Estructura del Informe HTML

El informe sigue esta estructura exacta:

```
1. Encabezado con título, fecha y cliente
2. Sección "Objetivo del mantenimiento" (3 párrafos mínimo)
3. Sección "Principales acciones realizadas" (párrafo resumen + tabla)
4. Tabla de tareas completadas (Título | Descripción)
5. Tabla de trabajos en curso
6. Tabla de trabajos pendientes
7. Footer corporativo
```

## Plantilla HTML Base

```html
<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Informe Mensual de Mantenimiento</title>
  <style>
    body { font-family: system-ui, sans-serif; color: #333; background: #f9f9f9; margin: 0; padding: 0; }
    .container { max-width: 860px; margin: 40px auto; background: #fff; border: 1px solid #e0e0e0; border-radius: 6px; padding: 48px; }
    h1 { color: #1a1a2e; font-size: 1.5rem; margin-bottom: 4px; }
    .meta { color: #555; font-size: 0.9rem; margin-bottom: 6px; }
    .cliente { font-weight: 600; color: #333; margin-bottom: 32px; font-size: 0.95rem; }
    h2 { color: #4a90e2; font-size: 1.05rem; border-bottom: 1px solid #e8e8e8; padding-bottom: 6px; margin-top: 36px; }
    p { line-height: 1.75; font-size: 0.94rem; margin: 10px 0; }
    table { width: 100%; border-collapse: collapse; margin-top: 16px; font-size: 0.9rem; }
    th { background: #4a90e2; color: #fff; padding: 10px 12px; text-align: left; }
    td { padding: 9px 12px; border-bottom: 1px solid #ebebeb; vertical-align: top; }
    tr:nth-child(even) td { background: #f5f8fd; }
    .footer { text-align: center; margin-top: 48px; font-size: 0.8rem; color: #999; border-top: 1px solid #e8e8e8; padding-top: 20px; }
  </style>
</head>
<body>
<div class="container">
  <h1>INFORME MENSUAL DE MANTENIMIENTO: [NOMBRE DEL SISTEMA] — [VERSION]</h1>
  <p class="meta">Anexo técnico correspondiente a la factura de mantenimiento — [Mes Año]</p>
  <p class="cliente">Cliente: [NOMBRE CLIENTE]</p>

  <h2>1. Objetivo del mantenimiento</h2>
  <!-- Párrafo 1: Resumen ejecutivo (mín. 4 frases) -->
  <!-- Párrafo 2: Proyección y continuidad (mín. 3 frases) -->
  <!-- Párrafo 3: Próximos desarrollos (mín. 2 frases) -->

  <h2>2. Principales acciones realizadas</h2>
  <!-- Párrafo resumen de 3-4 líneas -->

  <table>
    <thead><tr><th>Título</th><th>Descripción</th></tr></thead>
    <tbody>
      <!-- Filas de tareas completadas -->
    </tbody>
  </table>

  <table>
    <thead><tr><th>Trabajos en curso</th></tr></thead>
    <tbody>
      <!-- Filas de trabajos en curso -->
    </tbody>
  </table>

  <table>
    <thead><tr><th>Trabajos pendientes</th></tr></thead>
    <tbody>
      <!-- Filas de trabajos pendientes -->
    </tbody>
  </table>

  <div class="footer">Documento elaborado por Blixel AI — Área de Automatización y Mantenimiento de Sistemas</div>
</div>
</body>
</html>
```

## Reglas de Estilo CSS

- **Paleta**: Grises neutros + azul corporativo `#4a90e2`
- **Tipografía**: `system-ui, sans-serif`
- **Encabezados h2**: Color `#4a90e2`, borde inferior sutil
- **Tablas**: Header azul `#4a90e2` con texto blanco, filas alternas con fondo `#f5f8fd`
- **Footer**: Centrado, texto gris `#999`, separado por borde superior
- **Espaciados**: Uniformes y limpios, sin elementos recargados

## Datos de Entrada

El informe recibe datos ya procesados desde Notion con estas variables:

| Variable | Contenido | Formato |
|----------|-----------|---------|
| `tareasCompletadas` | Tabla markdown de tareas completadas | `\| Nº \| Título \| Comentarios \|` |
| `tareasEnCurso` | Tabla markdown de trabajos en curso | `\| Nº \| Descripción \|` |
| `tareasPendientes` | Tabla markdown de trabajos pendientes | `\| Nº \| Descripción \|` |
| `htmlCompletadas` | Filas HTML pre-formateadas | `<tr><td>...</td></tr>` |
| `htmlEnCurso` | Filas HTML pre-formateadas | `<tr><td>...</td></tr>` |
| `htmlPendientes` | Filas HTML pre-formateadas | `<tr><td>...</td></tr>` |
| `totalCompletadas` | Número total de tareas completadas | Entero |
| `totalEnCurso` | Número de trabajos en curso | Entero |
| `totalPendientes` | Número de trabajos pendientes | Entero |
| `tareasDestacadas` | Las 3 tareas principales (por tiempo) | String separado por comas |

## Reglas de Redacción

### OBLIGATORIO
1. Responder ÚNICAMENTE con el HTML completo, empezando por `<!DOCTYPE html>`
2. Redactar todos los párrafos marcados con corchetes `[]` en la plantilla
3. Cada párrafo debe cumplir el mínimo de frases indicado
4. Las tablas HTML vienen pre-formateadas: insertarlas tal cual, NO modificarlas
5. Mencionar el número total de tareas completadas en el resumen
6. Destacar las tareas principales en la sección de acciones realizadas
7. Lenguaje técnico y profesional, español peninsular
8. Centrarse SOLO en: recordatorios de pago, informes de gestión internos y chatbot de consultas

### PROHIBIDO
1. Bloques de código markdown (```)
2. Texto introductorio o explicativo fuera del HTML ("Aquí tienes...", "He generado...")
3. Mencionar horas ni tiempos de trabajo
4. Mencionar facturación automatizada, sistemas contables/ERP, análisis predictivo
5. Mencionar procesamiento de facturas ni integraciones contables
6. Emojis en el documento
7. Columnas de fecha en las tablas

## Sección 1: Objetivo del Mantenimiento (Guía de Redacción)

### Párrafo 1 — Resumen ejecutivo (mín. 4 frases)
- Explicar que las tareas abarcan: mantenimiento correctivo, mejoras solicitadas, actualizaciones de seguridad, optimización de rendimiento, nuevas funcionalidades
- Destacar las áreas principales (usar `tareasDestacadas`)
- Tono técnico pero accesible

### Párrafo 2 — Proyección y continuidad (mín. 3 frases)
- Mencionar los trabajos en curso y su estado de avance
- Explicar que aseguran la evolución continua del sistema
- Adaptar a las necesidades cambiantes de la empresa

### Párrafo 3 — Próximos desarrollos (mín. 2 frases)
- Indicar las tareas pendientes para próximos ciclos
- Mencionar mejoras programadas a corto plazo

## Sección 2: Principales Acciones Realizadas (Guía de Redacción)

- Párrafo de 3-4 líneas resumiendo las acciones más destacadas
- Mencionar el número total de tareas completadas
- Destacar las 3 tareas principales (de `tareasDestacadas`)
- NO describir cada tarea individualmente en el párrafo (para eso están las tablas)

## Formato de Filas HTML para Tablas

### Tareas completadas (2 columnas)
```html
<tr>
  <td style="width:35%;font-weight:600;vertical-align:top;padding:9px 12px;border-bottom:1px solid #ebebeb">
    1. Título de la tarea
  </td>
  <td style="vertical-align:top;padding:9px 12px;border-bottom:1px solid #ebebeb">
    Descripción o comentarios
  </td>
</tr>
```

### Trabajos en curso / pendientes (1 columna)
```html
<tr>
  <td style="vertical-align:top;padding:9px 12px;border-bottom:1px solid #ebebeb">
    1. Descripción del trabajo
  </td>
</tr>
```

## Conversión a PDF

El HTML generado se convierte a PDF usando Gotenberg con estos márgenes:
- Top: 0.5
- Bottom: 0.5
- Left: 0.5
- Right: 0.5

Asegurar que el HTML sea autocontenido (todos los estilos inline o en `<style>`) ya que Gotenberg no tiene acceso a recursos externos.

## Ejemplo de Uso en n8n

El prompt del agente `NOA - INFORME` recibe los datos formateados y genera el HTML completo. El output se convierte a binary (`Buffer.from(htmlContent, 'utf-8')`) y se envía a Gotenberg para la conversión PDF.

### Flujo simplificado:
```
Notion (tareas) → Formatear datos → Agente IA (genera HTML) → Buffer binary → Gotenberg (PDF) → Gmail (borrador)
```

## Generación de Email Acompañante

Junto al informe se genera un email con estas características:

### Reglas del email
- Tono cordial y profesional
- Máximo 100 palabras
- Mencionar importe de factura y número de tareas
- NO describir tareas individuales
- NO mencionar horas ni tiempos
- Usar "vosotros"
- Formato: JSON con campos `subject` y `body` (HTML con estilos inline)

### Estructura del email
```json
{
  "subject": "[Asunto del email]",
  "body": "<div style=\"font-family: 'Segoe UI', Arial, sans-serif; font-size: 14px; color: #333; line-height: 1.6;\"><p>Hola [nombre],</p><p>[contenido]</p><p>Un saludo cordial,</p></div>"
}
```

### Firma (se añade automáticamente, NO incluir en el body)
La firma de Noa (Asistente IA de Gestión, Blixel AI) se concatena automáticamente después del body del email en el nodo de construcción MIME.

## Identidad del Asistente

- **Nombre**: Noa
- **Rol**: Asistente IA de Gestión
- **Empresa**: Blixel AI (especialistas en automatización e IA)
- En comunicaciones externas: mencionar que es IA una vez al inicio, de forma breve y natural
