# Contexto del proyecto

TwinOps es una PoC de un dashboard de monitoreo representado como modelo 3D isométrico de la operación, con salud por etapa y predicciones. Dos verticales prototipadas: centro de distribución (`prototipos/cd-pilar`) y línea de alta tensión con inspección por drones (`prototipos/lat-ceibal`).

**Leé `HANDOFF.md` antes de empezar:** tiene objetivo, decisiones, restricciones, estado y próximos pasos.

## Principios que no se negocian

- La simulación vive separada del render; el 3D y el panel solo leen el estado.
- Una sola métrica principal define la salud de cada etapa o activo.
- La predicción se muestra siempre en violeta (`--future`).
- Interfaz en español rioplatense, con tema claro y oscuro, responsive y con `prefers-reduced-motion`.
- Los prototipos son referencia de comportamiento, no base de código.

## Sobre el dueño

Experto en cloud y datos, principiante en diseño y 3D: explicá las decisiones visuales en términos simples.
