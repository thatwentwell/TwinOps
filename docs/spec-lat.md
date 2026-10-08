# Especificación: línea de alta tensión (LAT 500 kV Ceibal – Laguna Brava)

## Escenario

Tramo de 14 torres (T-01 a T-14) entre dos estaciones transformadoras ficticias: ET Ceibal (origen) y ET Laguna Brava (llegada). Fases R, S y T, como en la convención argentina. T-07 y T-08 son torres de cruce sobre el arroyo Las Garzas, más altas que el resto.

La historia principal de la demo es **del hallazgo del dron a la orden de trabajo**. La predicción acompaña sugiriendo la prioridad de cada orden.

## Salud

| Entidad | Métrica | Atención | Crítico |
|---|---|---|---|
| Torre | Índice de riesgo de falla (0 a 100) | 40 | 70 |
| Línea (y ET) | Carga sobre el límite térmico | 80 % | 95 % |
| Línea (y ET) | Temperatura del conductor | 80 °C | 90 °C |

Una torre sin hallazgos activos se muestra como "Sin hallazgos" (neutro), no como "Normal".

### Índice de riesgo por torre

Puntaje base por hallazgo activo, sumado:

| Hallazgo | Base |
|---|---|
| Nido cerca de un conductor | 45 |
| Nido lejos de los conductores | 18 |
| Posible nido de baja confianza | 12 |
| Aislador roto | 50 |
| Aislador fisurado | 22 |

`riesgo = base × (1 + 0,9 × lluvia) + 8 si la carga supera 85 %`, con tope en 100. La lluvia va de 0 a 1.

## Modelo eléctrico

- Límite térmico 1200 MW, tensión nominal 500 kV, factor de potencia 0,95.
- Demanda diaria con picos a las 15:00 y 21:00 y valle de madrugada (≈ 47 % a 67 % de carga).
- Temperatura del conductor = ambiente + 62 × carga² − 9 × lluvia.
- La flecha de las catenarias crece con la temperatura del conductor y se ve en el 3D.

## Clima

Un frente de tormenta cruza la línea de este a oeste a ~0,33 unidades por minuto, con intensidad 0,85. En el escenario base llega a la ET Laguna Brava a las 19:40 del día 0.

## Inspección con dron

| Parámetro | Valor |
|---|---|
| Torres del vuelo | T-08 a T-13 |
| Inicio | 09:43 |
| Por torre | 6 min de traslado + 12 min de órbita |
| Fotos por torre | 14 |
| Subida al servidor | 4 min después de la captura |
| Análisis del modelo | 7 min después de la captura |

Un hallazgo se considera detectado cuando se analiza la foto de la mitad de la órbita de su torre.

## Hallazgos preparados

| Id | Torre | Hallazgo | Confianza | Origen |
|---|---|---|---|---|
| F1 | T-03 | Nido lejos de los conductores | 91 % | Vuelo anterior |
| F2 | T-05 | Aislador fisurado, fase T | 86 % | Vuelo anterior |
| F3 | T-09 | Nido sobre la ménsula, cerca de la fase S | 94 % | Vuelo actual (10:20) |
| F4 | T-11 | Aislador roto, fase R | 88 % | Vuelo actual (10:56) |
| F5 | T-12 | Posible nido (es una bolsa) | 61 % | Vuelo actual (11:14), falso positivo |

## Órdenes de trabajo

- Estados: detectado → orden creada → cuadrilla en camino → trabajo en la torre → resuelto y verificado con foto.
- Traslado: 25 min + 0,8 min por unidad de distancia desde el depósito en ET Ceibal.
- Trabajo: 50 min para retirar un nido, 90 min para recambiar una cadena de aisladores.
- Un hallazgo se puede descartar como falso positivo (y deshacer).

## Escenarios de demo

| Escenario | Efecto |
|---|---|
| Ola de calor de 36 h | Demanda × 1,28 y ambiente + 7 °C |
| Adelantar la tormenta | El frente llega en 2,5 h con intensidad 1 |
| Reiniciar la demo | Vuelve todo al estado inicial (09:40) |

## Valores de control

Sirven para verificar que una reimplementación reproduce el comportamiento del prototipo:

- F3 (T-09) se detecta a las 10:20 del día 0.
- Sin orden de trabajo, T-09 pasa a crítico cerca de las 21:50 del día 0, con riesgo máximo ≈ 79.
- T-11 llega a ≈ 88 de riesgo cerca de las 21:40 del día 0.
- Con una orden creada a las 10:30 para T-09, la cuadrilla llega 12:14 y termina 13:04.
- Con ola de calor, la carga llega a ≈ 86 % y el conductor a ≈ 85 °C a las 15:40.
