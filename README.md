# TwinOps

Dashboard de monitoreo que, en lugar del clásico *single pane of glass* con métricas y KPIs, representa la operación como un modelo 3D isométrico animado. Cada etapa o activo se puede seleccionar para ver su estado de salud y sus métricas, y una línea de tiempo permite ver el estado previsto de la operación.

El objetivo de este repositorio es una prueba de concepto para mostrar la idea a potenciales clientes.

## Prototipos

| Vertical | Carpeta | Spec |
|---|---|---|
| Centro de distribución y última milla (CD Pilar) | `prototipos/cd-pilar/` | [`docs/spec-cd-pilar.md`](docs/spec-cd-pilar.md) |
| Línea de alta tensión con inspección por drones (LAT 500 kV Ceibal – Laguna Brava) | `prototipos/lat-ceibal/` | [`docs/spec-lat.md`](docs/spec-lat.md) |

## Cómo verlos

Cada prototipo es un único archivo HTML sin build:

```bash
npx serve prototipos
```

Necesitan conexión a internet porque cargan three.js desde cdnjs y las tipografías desde Google Fonts.

## Para seguir el desarrollo

El contexto completo para continuar con Claude Code está en [`HANDOFF.md`](HANDOFF.md).
