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

## Recursos Corporativos

### Logo Blixel AI
- **URL**: `https://blixel.ai/wp-content/uploads/2025/08/blixel.ai-logo-1000_500-2.png`
- **Uso en header**: ancho 130px, con `filter: brightness(0) invert(1)` para que se vea blanco sobre fondo oscuro
- **Uso en footer**: ancho 80px, con `opacity: 0.4` para que sea sutil

### Paleta de Colores (Monocromática)
- **Negro principal**: `#111827` (header, títulos fuertes)
- **Gris oscuro**: `#1f2937` (texto principal)
- **Gris medio**: `#4b5563` (texto secundario, subtítulos)
- **Gris claro**: `#9ca3af` (metadatos, footer)
- **Gris borde**: `#e5e7eb` (líneas, separadores)
- **Fondo alterno**: `#f9fafb` (filas pares de tabla)
- **Fondo page**: `#ffffff`

## Plantilla HTML Base

```html
<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Informe Mensual de Mantenimiento</title>
  <style>
    * { margin: 0; padding: 0; box-sizing: border-box; }

    body {
      font-family: 'Segoe UI', system-ui, -apple-system, sans-serif;
      color: #1f2937;
      background: #fff;
      line-height: 1.7;
      font-size: 14px;
    }

    .page {
      max-width: 860px;
      margin: 0 auto;
      background: #fff;
    }

    /* ── HEADER ── */
    .header {
      background: #111827;
      padding: 36px 48px;
      display: flex;
      justify-content: space-between;
      align-items: flex-start;
    }
    .header-left { flex: 1; }
    .header-logo {
      width: 130px;
      margin-bottom: 18px;
      filter: brightness(0) invert(1);
    }
    .header h1 {
      color: #ffffff;
      font-size: 1.05rem;
      font-weight: 600;
      letter-spacing: 0.2px;
      margin-bottom: 4px;
    }
    .header-subtitle {
      color: rgba(255,255,255,0.5);
      font-size: 0.8rem;
      font-weight: 400;
    }
    .header-right {
      text-align: right;
      color: rgba(255,255,255,0.6);
      font-size: 0.8rem;
      line-height: 1.8;
    }
    .header-right strong {
      color: #fff;
      font-size: 0.85rem;
    }
    .header-version {
      display: inline-block;
      border: 1px solid rgba(255,255,255,0.2);
      color: rgba(255,255,255,0.5);
      padding: 2px 10px;
      border-radius: 3px;
      font-size: 0.7rem;
      font-weight: 500;
      letter-spacing: 0.5px;
      margin-top: 6px;
    }

    /* ── KPI BAR ── */
    .kpi-bar {
      display: flex;
      border-bottom: 1px solid #e5e7eb;
    }
    .kpi-item {
      flex: 1;
      text-align: center;
      padding: 18px 16px;
      border-right: 1px solid #e5e7eb;
    }
    .kpi-item:last-child { border-right: none; }
    .kpi-number {
      font-size: 2rem;
      font-weight: 800;
      color: #111827;
      line-height: 1;
      margin-bottom: 4px;
    }
    .kpi-label {
      font-size: 0.68rem;
      text-transform: uppercase;
      letter-spacing: 1.2px;
      color: #9ca3af;
      font-weight: 600;
    }

    /* ── CONTENT ── */
    .content { padding: 36px 48px; }

    h2 {
      color: #111827;
      font-size: 0.85rem;
      font-weight: 700;
      text-transform: uppercase;
      letter-spacing: 1px;
      padding-bottom: 8px;
      margin-top: 32px;
      margin-bottom: 14px;
      border-bottom: 1px solid #e5e7eb;
    }
    h2:first-child { margin-top: 0; }

    p {
      margin: 10px 0;
      font-size: 0.9rem;
      line-height: 1.75;
      color: #4b5563;
    }

    /* ── TABLES ── */
    table {
      width: 100%;
      border-collapse: collapse;
      margin-top: 14px;
      margin-bottom: 20px;
      font-size: 0.85rem;
      border: 1px solid #e5e7eb;
    }
    th {
      background: #111827;
      color: #fff;
      padding: 10px 16px;
      text-align: left;
      font-weight: 600;
      font-size: 0.75rem;
      text-transform: uppercase;
      letter-spacing: 0.5px;
    }
    td {
      padding: 10px 16px;
      border-bottom: 1px solid #f0f0f0;
      vertical-align: top;
      color: #4b5563;
    }
    tr:nth-child(even) td { background: #f9fafb; }
    tr:last-child td { border-bottom: none; }
    td:first-child { font-weight: 600; color: #1f2937; }

    /* ── FOOTER ── */
    .footer {
      border-top: 1px solid #e5e7eb;
      padding: 24px 48px;
      text-align: center;
    }
    .footer-logo {
      width: 80px;
      opacity: 0.4;
      margin-bottom: 8px;
    }
    .footer-text {
      font-size: 0.72rem;
      color: #9ca3af;
      line-height: 1.6;
    }
  </style>
</head>
<body>
<div class="page">

  <!-- HEADER -->
  <div class="header">
    <div class="header-left">
      <img src="https://blixel.ai/wp-content/uploads/2025/08/blixel.ai-logo-1000_500-2.png"
           alt="Blixel AI" class="header-logo">
      <h1>INFORME MENSUAL DE MANTENIMIENTO</h1>
      <div class="header-subtitle">Sistema Automatizado de Cobros con IA</div>
    </div>
    <div class="header-right">
      <strong>[NOMBRE CLIENTE]</strong><br>
      [Mes Año]<br>
      <span class="header-version">[VERSION]</span>
    </div>
  </div>

  <!-- KPI BAR -->
  <div class="kpi-bar">
    <div class="kpi-item">
      <div class="kpi-number">[totalCompletadas]</div>
      <div class="kpi-label">Completadas</div>
    </div>
    <div class="kpi-item">
      <div class="kpi-number">[totalEnCurso]</div>
      <div class="kpi-label">En curso</div>
    </div>
    <div class="kpi-item">
      <div class="kpi-number">[totalPendientes]</div>
      <div class="kpi-label">Pendientes</div>
    </div>
  </div>

  <!-- CONTENIDO -->
  <div class="content">

    <h2>1. Resumen del mantenimiento</h2>
    <!-- Párrafo único: resumen ejecutivo breve (4-5 frases, máx. 5 líneas) -->

    <h2>2. Acciones realizadas</h2>
    <table>
      <thead><tr><th style="width:35%">Acción</th><th>Detalle</th></tr></thead>
      <tbody>
        <!-- htmlCompletadas -->
      </tbody>
    </table>

    <h2>3. Trabajos en curso</h2>
    <table>
      <thead><tr><th>Descripción</th></tr></thead>
      <tbody>
        <!-- htmlEnCurso -->
      </tbody>
    </table>

    <h2>4. Trabajos pendientes</h2>
    <table>
      <thead><tr><th>Descripción</th></tr></thead>
      <tbody>
        <!-- htmlPendientes -->
      </tbody>
    </table>

  </div>

  <!-- FOOTER -->
  <div class="footer">
    <img src="https://blixel.ai/wp-content/uploads/2025/08/blixel.ai-logo-1000_500-2.png"
         alt="Blixel AI" class="footer-logo">
    <div class="footer-text">
      Documento elaborado por Blixel AI &mdash; Area de Automatizacion y Mantenimiento de Sistemas
    </div>
  </div>

</div>
</body>
</html>
```

## Datos de Entrada

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
5. Reemplazar `[totalCompletadas]`, `[totalEnCurso]`, `[totalPendientes]` con los números reales en la barra KPI
6. Mencionar el número total de tareas completadas en el resumen
7. Destacar las tareas principales en la sección de acciones realizadas
8. Lenguaje técnico y profesional, español peninsular
9. Centrarse SOLO en: recordatorios de pago, informes de gestión internos y chatbot de consultas

### PROHIBIDO
1. Bloques de código markdown
2. Texto introductorio o explicativo fuera del HTML ("Aquí tienes...", "He generado...")
3. Mencionar horas ni tiempos de trabajo
4. Mencionar facturación automatizada, sistemas contables/ERP, análisis predictivo
5. Mencionar procesamiento de facturas ni integraciones contables
6. Emojis en el documento
7. Columnas de fecha en las tablas
8. Modificar la URL del logo ni los estilos del header/footer
9. Añadir colores llamativos — usar SOLO la paleta monocromática definida

## Sección 1: Resumen del Mantenimiento (Guía de Redacción)

**Un solo párrafo de 4-5 frases (máximo 5 líneas). Debe caber antes de la tabla sin pasar de página.**

- Mencionar el número total de tareas completadas en el mes
- Destacar las áreas principales (usar `tareasDestacadas`)
- Indicar brevemente que hay trabajos en curso y pendientes para próximos ciclos
- Tono técnico pero accesible
- NO repetir lo que ya dicen las tablas

## Formato de Filas HTML para Tablas

### Tareas completadas (2 columnas)
```html
<tr>
  <td style="width:35%;font-weight:600;vertical-align:top;padding:10px 16px;border-bottom:1px solid #f0f0f0;color:#1f2937">
    1. Título de la tarea
  </td>
  <td style="vertical-align:top;padding:10px 16px;border-bottom:1px solid #f0f0f0;color:#4b5563">
    Descripción o comentarios
  </td>
</tr>
```

### Trabajos en curso / pendientes (1 columna)
```html
<tr>
  <td style="vertical-align:top;padding:10px 16px;border-bottom:1px solid #f0f0f0;color:#4b5563">
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

El HTML debe ser autocontenido (todos los estilos en `<style>`). Gotenberg SÍ carga imágenes por URL externa (el logo se renderiza correctamente).

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
- **OBLIGATORIO**: El asunto (`subject`) debe ser SIEMPRE exactamente el mismo formato, sin variaciones:
  `Factura e informe de mantenimiento - [Mes Año] | [NOMBRE CLIENTE]`
  Ejemplo: `Factura e informe de mantenimiento - Marzo 2026 | PORTES BISBAL SL`
- **PROHIBIDO**: Cambiar el formato del asunto, añadir palabras extra, reordenar elementos o usar otro patrón

### Estructura del email
```json
{
  "subject": "Factura e informe de mantenimiento - [Mes Año] | [NOMBRE CLIENTE]",
  "body": "<div style=\"font-family: 'Segoe UI', Arial, sans-serif; font-size: 14px; color: #333; line-height: 1.6;\"><p>Hola [nombre],</p><p>[contenido]</p><p>Un saludo cordial,</p></div>"
}
```

### Firma (se añade automáticamente, NO incluir en el body)
La firma de Noa se concatena automáticamente después del body del email en el nodo de construcción MIME.

## Identidad del Asistente

- **Nombre**: Noa
- **Rol**: Asistente IA de Gestión
- **Empresa**: Blixel AI (especialistas en automatización e IA)
- En comunicaciones externas: mencionar que es IA una vez al inicio, de forma breve y natural
