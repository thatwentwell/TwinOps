# Especificación: centro de distribución (CD Pilar)

## Etapas y salud

Cada etapa tiene una sola métrica principal que define su estado (Normal, Atención, Crítico).

| Etapa | Métrica principal | Atención | Crítico |
|---|---|---|---|
| Recepción | Camiones esperando puerta | 2 | 4 |
| Almacenamiento | Ocupación de posiciones | 85 % | 94 % |
| Picking | Backlog en minutos de trabajo | 30 min | 60 min |
| Empaque | Cola en minutos de trabajo | 15 min | 30 min |
| Despacho | Órdenes esperando vehículo | 100 | 180 |
| Última milla | Entregas a tiempo, última hora | < 95 % | < 88 % |

Los backlogs se miden en minutos de trabajo (cantidad pendiente / capacidad actual) y no en cantidad de órdenes, para que el umbral sea comparable sin importar la dotación.

## Simulación

Paso discreto de 1 minuto simulado. Las órdenes viajan como cohortes FIFO con su hora de creación, lo que permite calcular entregas tarde de forma realista.

| Parámetro | Valor |
|---|---|
| Demanda | ~3,2 órdenes/min base, picos a las 11:00 y 17:30 |
| Recepción | 3 puertas, 1 camión cada 28 min, 20 pallets, 35 min de descarga |
| Almacenamiento | 360 posiciones, 0,15 pallets por orden |
| Picking | 15 pickers × 0,36 órdenes/min |
| Empaque | 6 puestos × 1 orden/min |
| Despacho | 4 andenes, carga de 10 min |
| Flota | 18 vehículos de 48 órdenes, 2 min por entrega, 18 min de regreso |
| Promesa de entrega | 165 min desde la compra |

## Predicción

Se clona el estado actual y se corre la misma simulación 120 minutos hacia adelante (predicción *what-if*). En la versión productiva se reemplaza por un modelo de forecasting real.

## Incidentes de demo

| Incidente | Efecto |
|---|---|
| Falla en la cinta de picking | Capacidad de picking al 45 % durante 60 min |
| Ausentismo en empaque | Capacidad de empaque al 50 % durante 90 min |
| Llegan 5 camiones juntos | +5 camiones en la cola de recepción |
| Corte de tránsito en Panamericana | Entregas y regresos 2,5 veces más lentos durante 120 min |
