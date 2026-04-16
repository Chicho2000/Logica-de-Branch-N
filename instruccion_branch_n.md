# Documentación de Instrucción: Branch on Negative (BRN)

Este documento detalla la implementación y lógica del módulo de salto condicional por negativo dentro del entorno de simulación CircuitVerse.

## 1. Concepto Teórico

La instrucción `Branch N` (o `BMI` - Branch if Minus) permite alterar el flujo secuencial del programa basándose en el resultado de una operación previa. En la arquitectura de 8 bits, se utiliza el **Bit 7** del resultado como indicador de signo (MSB) bajo la convención de Complemento a 2.

- **Dato < 0:** Bit 7 = `1`
- **Dato >= 0:** Bit 7 = `0`

## 2. Implementación en CircuitVerse

Para adaptar este concepto al simulador, se diseñó un circuito lógico que valida dos condiciones simultáneas mediante una compuerta **AND**.

### Componentes Utilizados:

- **Input (8-bit):** Representa el bus de salida de la ALU (`output_ALU`).
- **Splitter (1-to-8):** Utilizado para aislar el Bit 7 (MSB) del bus de datos.
- **Input (1-bit):** Representa la señal de control proveniente de la Unidad de Decodificación (`control_signal`).
- **Compuerta AND:** Bloque lógico de decisión.
- **Output (1-bit):** Señal de habilitación de carga para el Program Counter (`flag_N` / `Load_PC`).

## 3. Lógica de Funcionamiento

El circuito opera bajo la siguiente tabla de verdad simplificada:

| Bit 7 (Signo) | Control Signal | Salida (Salto) | Acción                                                 |
| :-----------: | :------------: | :------------: | :----------------------------------------------------- |
|       0       |       0        |       0        | Flujo normal (No es BRN, No es negativo)               |
|       0       |       1        |       0        | Flujo normal (Es BRN, pero número positivo)            |
|       1       |       0        |       0        | Flujo normal (Es negativo, pero no es instrucción BRN) |
|       1       |       1        |       1        | **EJECUTAR SALTO** (Es BRN y el número es negativo)    |

## 4. Adaptación Técnica

En un procesador real, la detección del negativo suele almacenarse en un flip-flop de estado (Status Register). En esta implementación de CircuitVerse, hemos optado por una **extracción directa del bus** mediante el Splitter.

Esta adaptación es más eficiente para la visualización educativa, ya que permite observar en tiempo real cómo el cambio en el bit más significativo activa la lógica de salto sin depender de ciclos de reloj adicionales para actualizar un registro de banderas intermedio.

---
