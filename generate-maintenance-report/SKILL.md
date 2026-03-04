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
1. Header corporativo con logo Blixel AI + datos del cliente
2. Barra de resumen con KPIs (completadas, en curso, pendientes)
3. Sección "Objetivo del mantenimiento" (3 párrafos mínimo)
4. Sección "Principales acciones realizadas" (párrafo resumen + tabla)
5. Tabla de tareas completadas (Título | Descripción)
6. Tabla de trabajos en curso
7. Tabla de trabajos pendientes
8. Footer corporativo con logo
```

## Recursos Corporativos

### Logo Blixel AI
- **URL**: `https://blixel.ai/wp-content/uploads/2025/08/blixel.ai-logo-1000_500-2.png`
- **Uso en header**: ancho 140px, alineado a la izquierda
- **Uso en footer**: ancho 90px, centrado, opacidad 0.6
- **Fondo**: el logo es azul (#4F6BF6) sobre fondo transparente, usar sobre fondo blanco o muy claro

### Paleta de Colores Corporativa
- **Azul primario**: `#4F6BF6` (logo, encabezados, botones)
- **Azul oscuro**: `#1a1a2e` (títulos principales)
- **Gris texto**: `#374151` (cuerpo de texto)
- **Gris claro**: `#6B7280` (texto secundario)
- **Fondo page**: `#f8fafc`
- **Fondo card**: `#ffffff`
- **Borde sutil**: `#e5e7eb`
- **Accent success**: `#10b981` (indicadores positivos)
- **Accent warning**: `#f59e0b` (en curso)
- **Accent info**: `#6366f1` (pendientes)

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
      color: #374151;
      background: #f8fafc;
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
      background: linear-gradient(135deg, #1a1a2e 0%, #2d2d5e 100%);
      padding: 40px 48px;
      display: flex;
      justify-content: space-between;
      align-items: flex-start;
    }
    .header-left { flex: 1; }
    .header-logo {
      width: 140px;
      margin-bottom: 20px;
      filter: brightness(0) invert(1);
    }
    .header h1 {
      color: #ffffff;
      font-size: 1.15rem;
      font-weight: 700;
      letter-spacing: 0.3px;
      margin-bottom: 6px;
    }
    .header-subtitle {
      color: rgba(255,255,255,0.7);
      font-size: 0.85rem;
      font-weight: 400;
    }
    .header-right {
      text-align: right;
      color: rgba(255,255,255,0.85);
      font-size: 0.82rem;
      line-height: 1.8;
    }
    .header-right strong {
      color: #fff;
      font-size: 0.85rem;
    }
    .header-badge {
      display: inline-block;
      background: rgba(79,107,246,0.3);
      border: 1px solid rgba(79,107,246,0.5);
      color: #a5b4fc;
      padding: 3px 12px;
      border-radius: 20px;
      font-size: 0.75rem;
      font-weight: 600;
      letter-spacing: 0.5px;
      margin-top: 8px;
    }

    /* ── KPI BAR ── */
    .kpi-bar {
      display: flex;
      border-bottom: 1px solid #e5e7eb;
    }
    .kpi-item {
      flex: 1;
      text-align: center;
      padding: 20px 16px;
      border-right: 1px solid #e5e7eb;
    }
    .kpi-item:last-child { border-right: none; }
    .kpi-number {
      font-size: 1.8rem;
      font-weight: 800;
      line-height: 1;
      margin-bottom: 4px;
    }
    .kpi-number.green { color: #10b981; }
    .kpi-number.amber { color: #f59e0b; }
    .kpi-number.blue { color: #6366f1; }
    .kpi-label {
      font-size: 0.72rem;
      text-transform: uppercase;
      letter-spacing: 1px;
      color: #6B7280;
      font-weight: 600;
    }

    /* ── CONTENT ── */
    .content { padding: 40px 48px; }

    h2 {
      color: #1a1a2e;
      font-size: 1rem;
      font-weight: 700;
      text-transform: uppercase;
      letter-spacing: 0.8px;
      padding-bottom: 10px;
      margin-top: 36px;
      margin-bottom: 16px;
      border-bottom: 2px solid #4F6BF6;
      display: flex;
      align-items: center;
      gap: 8px;
    }
    h2 .section-num {
      background: #4F6BF6;
      color: #fff;
      width: 26px;
      height: 26px;
      border-radius: 50%;
      display: inline-flex;
      align-items: center;
      justify-content: center;
      font-size: 0.75rem;
      font-weight: 700;
      flex-shrink: 0;
    }
    h2:first-child { margin-top: 0; }

    p {
      margin: 12px 0;
      font-size: 0.92rem;
      line-height: 1.75;
      color: #374151;
    }

    /* ── TABLES ── */
    table {
      width: 100%;
      border-collapse: collapse;
      margin-top: 16px;
      margin-bottom: 24px;
      font-size: 0.88rem;
      border-radius: 8px;
      overflow: hidden;
      border: 1px solid #e5e7eb;
    }
    th {
      background: #1a1a2e;
      color: #fff;
      padding: 12px 16px;
      text-align: left;
      font-weight: 600;
      font-size: 0.8rem;
      text-transform: uppercase;
      letter-spacing: 0.5px;
    }
    td {
      padding: 11px 16px;
      border-bottom: 1px solid #f0f0f0;
      vertical-align: top;
      color: #374151;
    }
    tr:nth-child(even) td { background: #f8fafc; }
    tr:last-child td { border-bottom: none; }
    td:first-child { font-weight: 600; color: #1a1a2e; }

    /* ── STATUS BADGES ── */
    .table-status {
      display: inline-block;
      padding: 2px 10px;
      border-radius: 12px;
      font-size: 0.72rem;
      font-weight: 600;
      text-transform: uppercase;
      letter-spacing: 0.3px;
    }
    .status-progress {
      background: #fef3c7;
      color: #92400e;
    }
    .status-pending {
      background: #ede9fe;
      color: #5b21b6;
    }

    /* ── FOOTER ── */
    .footer {
      background: #f8fafc;
      border-top: 1px solid #e5e7eb;
      padding: 28px 48px;
      text-align: center;
    }
    .footer-logo {
      width: 90px;
      opacity: 0.5;
      margin-bottom: 10px;
    }
    .footer-text {
      font-size: 0.75rem;
      color: #9ca3af;
      line-height: 1.6;
    }
    .footer-divider {
      width: 40px;
      height: 2px;
      background: #e5e7eb;
      margin: 10px auto;
    }
  </style>
</head>
<body>
<div class="page">

  <!-- HEADER CORPORATIVO -->
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
      <span class="header-badge">[VERSION]</span>
    </div>
  </div>

  <!-- BARRA KPI -->
  <div class="kpi-bar">
    <div class="kpi-item">
      <div class="kpi-number green">[totalCompletadas]</div>
      <div class="kpi-label">Completadas</div>
    </div>
    <div class="kpi-item">
      <div class="kpi-number amber">[totalEnCurso]</div>
      <div class="kpi-label">En curso</div>
    </div>
    <div class="kpi-item">
      <div class="kpi-number blue">[totalPendientes]</div>
      <div class="kpi-label">Pendientes</div>
    </div>
  </div>

  <!-- CONTENIDO -->
  <div class="content">

    <h2><span class="section-num">1</span> Objetivo del mantenimiento</h2>
    <!-- Párrafo 1: Resumen ejecutivo (mín. 4 frases) -->
    <!-- Párrafo 2: Proyección y continuidad (mín. 3 frases) -->
    <!-- Párrafo 3: Próximos desarrollos (mín. 2 frases) -->

    <h2><span class="section-num">2</span> Principales acciones realizadas</h2>
    <!-- Párrafo resumen de 3-4 líneas -->

    <table>
      <thead><tr><th style="width:35%">Acción</th><th>Detalle</th></tr></thead>
      <tbody>
        <!-- htmlCompletadas -->
      </tbody>
    </table>

    <h2><span class="section-num">3</span> Trabajos en curso</h2>
    <table>
      <thead><tr><th>Descripción</th></tr></thead>
      <tbody>
        <!-- htmlEnCurso -->
      </tbody>
    </table>

    <h2><span class="section-num">4</span> Trabajos pendientes</h2>
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
    <div class="footer-divider"></div>
    <div class="footer-text">
      Documento elaborado por Blixel AI<br>
      Area de Automatizacion y Mantenimiento de Sistemas<br>
      blixel.ai
    </div>
  </div>

</div>
</body>
</html>
```

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
  <td style="width:35%;font-weight:600;vertical-align:top;padding:11px 16px;border-bottom:1px solid #f0f0f0;color:#1a1a2e">
    1. Título de la tarea
  </td>
  <td style="vertical-align:top;padding:11px 16px;border-bottom:1px solid #f0f0f0;color:#374151">
    Descripción o comentarios
  </td>
</tr>
```

### Trabajos en curso / pendientes (1 columna)
```html
<tr>
  <td style="vertical-align:top;padding:11px 16px;border-bottom:1px solid #f0f0f0;color:#374151">
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

Asegurar que el HTML sea autocontenido (todos los estilos en `<style>`) ya que Gotenberg no tiene acceso a recursos externos excepto imágenes por URL.

**IMPORTANTE para el logo**: Gotenberg SÍ puede cargar imágenes por URL externa, por lo que el logo de `blixel.ai` se renderizará correctamente en el PDF.

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
