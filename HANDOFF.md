# Handoff para Claude Code: TwinOps

## Objetivo

Construir TwinOps, una prueba de concepto de un **dashboard de monitoreo representado como un modelo 3D isométrico de la operación**, para mostrar a potenciales clientes. Reemplaza el clásico *single pane of glass* de métricas y KPIs por una maqueta animada donde:

- Se navega con una vista isométrica y se hace zoom en cada etapa o activo.
- Al hacer click en un elemento se ven su estado de salud y sus métricas.
- Además de observabilidad, la visualización muestra **predicciones**: una línea de tiempo permite ver el estado previsto de la operación.

Hay dos verticales prototipadas: un centro de distribución con última milla y una línea de transmisión eléctrica con inspección por drones.

## Sobre el dueño del proyecto

Fabri es Solutions Architect con mucha experiencia en cloud (AWS principalmente), datos y ML. **Es su primer proyecto con diseño y gráficos 3D**: explicá las decisiones visuales y de 3D en términos simples y no des por sabidos conceptos como glTF, cámaras ortográficas o instancing. No hace falta explicar fundamentos de cloud.

## Decisiones tomadas y por qué

**Una sola métrica principal define la salud de cada etapa o activo.** Con diez números nadie entiende qué está mal. Las demás métricas son contexto en el panel.

**Los backlogs se miden en minutos de trabajo, no en cantidad.** 300 órdenes pendientes pueden ser poco o mucho según la dotación; "una hora de atraso" se entiende sin contexto.

**La simulación vive separada del render.** El motor escribe el estado y la escena 3D y el panel solo lo leen. Así la fuente de datos se reemplaza por datos reales sin tocar lo visual. Es la decisión de arquitectura más importante del proyecto.

**La predicción del PoC es la misma simulación corrida hacia adelante** (*what-if*). Es honesta, coherente con lo que se ve y no necesita un modelo entrenado. En la versión productiva se reemplaza por forecasting real (por ejemplo, Chronos en SageMaker JumpStart).

**Hay dos estilos de motor de simulación, a propósito:**
- CD Pilar usa pasos discretos de 1 minuto con colas FIFO de cohortes (cada grupo de órdenes guarda su hora de creación), para calcular entregas tarde de forma realista. Predecir es clonar el estado y correr 120 pasos.
- LAT es una función pura del tiempo: el estado solo guarda decisiones y escenarios (órdenes de trabajo, descartes, ola de calor, tormenta). Predecir es evaluar el futuro, y la historia se calcula con el conocimiento disponible en cada momento (un hallazgo no "existe" antes de que el dron lo detecte).
- Para la PoC real conviene unificar detrás de una interfaz común (ver próximos pasos).

**En LAT, la historia principal es del hallazgo del dron a la orden de trabajo.** Fabri la eligió sobre la historia de riesgo climático. La predicción queda como soporte: sugiere la prioridad de cada orden.

**Hay un falso positivo deliberado (F5, 61 % de confianza).** Muestra que una persona valida lo que detecta el modelo de visión artificial.

**Ambas verticales comparten el mismo sistema visual.** Así se leen como dos módulos de un mismo producto:
- Tipografía Barlow (inspirada en señalética vial) y Barlow Semi Condensed para números.
- Tokens de color en `:root` con variantes clara y oscura.
- Salud en verde, ámbar y rojo; la predicción siempre en violeta (`--future`).
- Al mirar el futuro, la escena se tiñe de violeta y aparece un banner.

**La cámara es ortográfica con dirección (1, 1, 1).** Eso da la vista isométrica clásica, sin perspectiva, con look de maqueta. El zoom a una etapa usa un "fit to box" que encuadra sus límites proyectados.

**Stack elegido para la PoC real:**
- React + Vite + TypeScript.
- react-three-fiber y @react-three/drei: describe la escena como componentes y es lo que mejor escribe Claude.
- @react-three/postprocessing para resaltar selecciones.
- zustand como store único.
- Tailwind + shadcn/ui + Recharts para el panel.
- Se descartaron Babylon.js (menos ecosistema React), Unity WebGL (pesado) y Spline (corto para lógica de datos).

**Los assets serán low-poly CC0 de un solo autor.** Candidatos: Kenney, Quaternius o KayKit. Mezclar estilos es lo que más rápido hace que algo se vea amateur. Los prototipos usan primitivas geométricas.

## Restricciones

- **Los datos son simulados.** No hay integración con sistemas reales todavía.
- **Los prototipos son referencia de comportamiento, no base de código.** Están en three.js r128 sin framework, en un solo HTML cada uno.
- **Interfaz en español rioplatense** (vos, "probá", "tocá").
- **Accesibilidad y responsive son requisitos, no extras:** tema claro y oscuro, `prefers-reduced-motion`, layout para mobile (panel abajo), foco visible y controles con teclado.
- **Las licencias de assets importan:** CC0 no requiere atribución; CC-BY sí. Si se usa algo CC-BY, hay que registrar la atribución.
- **Rendimiento:** usar instancing para todo lo repetido (pallets, cajas, platos de aisladores, árboles, lluvia).
- **Las líneas WebGL tienen 1 px de grosor.** Las torres reticuladas y los conductores de LAT se ven finos de lejos. Si molesta, pasar a tubos o a `Line2` de three.js.

## Estado actual

- Dos prototipos funcionando en `prototipos/`. Ambos están publicados como artefactos privados de claude.ai.
- Se verificaron en Node: sintaxis y simulación en los dos; LAT además corrió completo en jsdom con three r128 real y un renderer simulado, recorriendo el flujo de la demo sin errores.
- **Se probaron en Chromium headless (2026-10-08)**, en claro y oscuro, desktop (1440×900) y mobile (390×844), con y sin `prefers-reduced-motion`: carga, selección de etapa, línea de tiempo al futuro, incidentes y navegación con teclado. Sin errores de consola. Se corrigió:
  - Etiquetas superpuestas (CD en mobile) o cortadas en el borde (LAT en mobile). Ahora se ubican por prioridad (seleccionada, crítica, atención, normal); si una choca, queda solo su punto de salud y se expande con hover o foco.
  - Piso de la escena chico: en pantallas verticales se veía el fondo en las esquinas.
  - En LAT, "Sin alertas previstas" se partía en una palabra por línea.
- **Falta medir rendimiento con GPU real:** el headless renderiza por software, así que sus FPS no sirven de referencia.
- La fase de diseño de la interfaz 2D en Claude Design todavía no se hizo.
- El repo es público y GitHub Pages publica `prototipos/` en https://thatwentwell.github.io/TwinOps/ con cada push a `main` que toque esa carpeta.

## Próximos pasos

1. **Probar ambos prototipos en una notebook con GPU real** (`npx serve prototipos`): rendimiento y legibilidad de las líneas finas de LAT de lejos. Lo visual básico ya se revisó en headless.
2. **Esperar las decisiones de Claude Design para el panel 2D**, si Fabri las trae. Si no, mantener el sistema visual actual.
3. **Crear `app/` con Vite + React + TS + R3F.** Separar un núcleo común de las verticales:
   - Núcleo: cámara isométrica y navegación, etiquetas flotantes, línea de tiempo, panel, sistema de salud, banner de predicción.
   - Cada vertical: escena, entidades, métricas y motor de simulación.
   - Esta separación es una propuesta para validar con Fabri.
4. **Portar los motores de simulación a módulos TypeScript con tests (Vitest).** Usar los "valores de control" de `docs/spec-lat.md` y los escenarios de `docs/spec-cd-pilar.md` como casos de prueba.
5. **Definir una interfaz de fuente de datos.** El simulador debe ser una implementación más; la siguiente sería un adaptador a datos reales (por ejemplo, un WebSocket).
6. **Reemplazar primitivas por assets glTF de un solo pack,** convertidos con gltfjsx.
7. **Deploy estático:** S3 + CloudFront con acceso restringido para mostrar a clientes, o GitHub Pages si puede ser público.

## Archivos relevantes

```
HANDOFF.md                     Este documento
CLAUDE.md                      Contexto corto que Claude Code carga siempre
README.md                      Descripción general y cómo correr los prototipos
docs/spec-cd-pilar.md          Etapas, umbrales, parámetros e incidentes del centro de distribución
docs/spec-lat.md               Riesgo, modelo eléctrico, clima, dron, hallazgos, órdenes y valores de control de la línea
prototipos/index.html          Página índice de los prototipos
prototipos/cd-pilar/index.html Prototipo del centro de distribución
prototipos/lat-ceibal/index.html Prototipo de la línea de alta tensión
.github/workflows/pages.yml    Publicación de prototipos/ en GitHub Pages
```

Dentro de cada prototipo, el motor de simulación está delimitado entre los comentarios `// ===SIM START===` y `// ===SIM END===`. Así se puede extraer y probar por separado.
