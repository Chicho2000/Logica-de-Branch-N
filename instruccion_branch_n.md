# Documentación de Instrucción: Branch on Negative (BRN) - Arquitectura 16 bits

Este documento detalla la implementación y lógica del módulo de salto condicional por negativo dentro del entorno de simulación CircuitVerse.

## 1. Concepto Teórico

La instrucción `Branch N` (o `BMI` - Branch if Minus) permite alterar el flujo secuencial del programa basándose en el resultado de una operación previa en la ALU. En esta arquitectura de 16 bits, se utiliza el **Bit 15** del resultado como indicador de polaridad (Bit Más Significativo o MSB) bajo la convención de Complemento a 2.

- **Dato < 0:** Bit 15 = `1`
- **Dato >= 0:** Bit 15 = `0`

## 2. Implementación en CircuitVerse

Para adaptar este concepto al simulador, se diseñó un circuito lógico que valida dos condiciones simultáneas mediante una compuerta lógica **AND**.

### Componentes Utilizados:

- **Input (16-bit):** Representa el bus de salida de la ALU (`output_ALU`).
- **Splitter (1-to-16):** Utilizado para expandir el bus de datos y aislar el Bit 15 (MSB).
- **Input (1-bit):** Representa la señal de control proveniente de la Unidad de Decodificación (`control_signal`).
- **Compuerta AND:** Bloque lógico de decisión.
- **Output (1-bit):** Señal de habilitación de carga para el Program Counter (`Branch_Enable` / `Load_PC`).

## 3. Lógica de Funcionamiento

El circuito opera bajo la siguiente tabla de verdad:

| Bit 15 (Signo) | Control Signal | Salida (Load_PC) | Acción Resultante                                              |
| :------------: | :------------: | :--------------: | :------------------------------------------------------------- |
|       0        |       0        |        0         | Flujo normal (No es instrucción BRN, el dato no es negativo)   |
|       0        |       1        |        0         | Flujo normal (Es instrucción BRN, pero el dato es positivo)    |
|       1        |       0        |        0         | Flujo normal (El dato es negativo, pero no es instrucción BRN) |
|       1        |       1        |        1         | **EJECUTAR SALTO** (Es BRN y el número es negativo)            |

## 4. Consideraciones Técnicas del Diseño

En una implementación física, el estado de negatividad suele persistirse en un _Status Register_ (Registro de Banderas). Para optimizar el diseño en CircuitVerse y facilitar la comprensión visual del flujo de datos, se extrae el bit de signo directamente desde el bus de 16 bits utilizando un componente divisor (_Splitter_).

Esta aproximación permite resolver la condición de salto de manera combinacional en un solo ciclo, vinculando directamente la salida de la ALU y la Unidad de Control.
