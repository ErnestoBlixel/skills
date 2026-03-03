# Blixel AI Skills

Repositorio de skills para el agente de IA de Blixel AI. Cada skill contiene instrucciones especializadas que el agente carga dinámicamente para completar tareas específicas.

## Skills disponibles

| Skill | Descripción |
|-------|-------------|
| `generate-maintenance-report` | Genera informes mensuales de mantenimiento en HTML profesional para clientes |

## Estructura

```
skills/
  nombre-del-skill/
    SKILL.md          # Instrucciones del skill (obligatorio)
```

## Uso

Estos skills se consumen desde el workflow de n8n **Skills Agent**, que lista los archivos del repo y permite al agente buscar y leer los `SKILL.md` relevantes según la petición del usuario.
