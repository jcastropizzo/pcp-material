# guia 05a stm reservas cine york — resumen

Fuente: [guia-05a-stm-reservas-cine-york.pdf](guia-05a-stm-reservas-cine-york.pdf). [Transcripción completa](guia-05a-stm-reservas-cine-york.transcripcion.md).

Síntesis editorial para estudio; el original prevalece ante diferencias.

## Consigna central
Administrar reservas y liberaciones de butacas mediante STM. Priorizar un bloque contiguo en una fila; cuando no exista, usar butacas libres siguiendo el orden especificado. Si no alcanza la capacidad libre, esperar.

## Requisitos que no se deben perder
- Una reserva asigna todas sus butacas atómicamente, sin compartirlas con otra reserva.
- La elección debe satisfacer la política al momento de asignar, no sobre una fotografía obsoleta.
- El requisito sobre despertares exige pensar qué TVars observa cada grupo bloqueado.

## Estrategia de lectura
Distinguir el tamaño del grupo de la cantidad de grupos y verificar la terminología del original. Separar registro de un pedido, espera y asignación: un `retry` que sigue al registro en la misma transacción revierte ese registro. Una cola de pedidos con respuestas privadas permite centralizar la asignación sin despertar a todos los grupos por cada liberación.

## Límite conceptual
No interpretar el requisito de despertares como garantía de que el runtime nunca reintenta una transacción por conflictos internos.

## Soluciones separadas

[Desarrollo de los ejercicios](guia-05a-stm-reservas-cine-york_resuelto.md) — elaboración de estudio, no oficial.
