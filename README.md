# CalculadoraFPGA
Repositorio para proyecto de calculadora realizado en FPGA
# Proyecto Final - Calculadora en FPGA
## Sistemas Digitales
### Universidad Nacional de Colombia

## Autores
- Juan Sebastian Carrascal Acosta - CC. 1005221054
- Jose Manuel Rodríguez Sandoval - CC. 1052384337 
- Nicolas Coronado Perez - CC. 1002551574

## Descripción del Proyecto

Este proyecto implementa una calculadora digital en FPGA utilizando la metodología de diseño de sistemas digitales presentada en el Capítulo 1 del libro "Síntesis de Sistemas Digitales". La calculadora realiza operaciones aritméticas básicas mediante periféricos especializados que se comunican con un procesador RISC-V a través de buses de datos, dirección y control.

## Niveles de Abstracción Implementados

### 1. Nivel Arquitectura
El sistema se conceptualiza como un SoC (System on Chip) que integra:
- Procesador RISC-V como unidad central de procesamiento
- Periféricos especializados: Multiplicador, Divisor, Convertidor Binario-BCD
- Memoria RAM para programa y datos
- Buses de comunicación: Datos (32 bits), Dirección (32 bits), Control

### 2. Nivel Algoritmo
Cada periférico implementa algoritmos específicos:
- Multiplicador: Algoritmo de productos parciales
- Divisor: Algoritmo de división binaria por corrimientos
- Binario-BCD: Algoritmo Double Dabble

### 3. Nivel Bloque Funcional
Implementación usando lógica de transferencia de registros:
- Registros para almacenamiento de operandos y resultados
- Unidades Aritméticas: ALU, sumadores, comparadores
- Unidad de Control: Máquinas de estado algorítmicas (ASM)

## Especificaciones Técnicas

### Arquitectura del Sistema
CPU RISC-V + Periféricos + Memoria
- Procesador FemtoRV32 (RV32I)
- Memoria de Programa: 0x0000 - 0x3FFFFF
- Periféricos Mapeados en Memoria:
  - Multiplicador: 0x420000 - 0x42FFFF
  - Divisor: 0x430000 - 0x43FFFF
  - Bin2BCD: 0x440000 - 0x44FFFF
  - UART: 0x400000 - 0x40FFFF
- Buses: Datos (32b), Dirección (32b), Control

### Operaciones Soportadas
- Multiplicación: 16 bits × 16 bits → 32 bits
- División: 16 bits ÷ 16 bits → 32 bits (cociente + residuo)
- Suma/Resta: 16 bits + 16 bits → 17 bits
- Conversión: Binario (16b) → BCD (4 dígitos)

### Características de los Periféricos

#### Multiplicador
- Algoritmo: Productos parciales con corrimientos
- Entradas: A[15:0], B[15:0], INIT
- Salidas: PP[31:0], DONE
- Tiempo: 16 ciclos de reloj máximo
- Registros mapeados:
  - 0x04: Operando A
  - 0x08: Operando B 
  - 0x0C: Iniciar (INIT)
  - 0x10: Resultado (PP[31:0])
  - 0x14: Estado (DONE)

#### Divisor
- Algoritmo: División binaria por sustracciones sucesivas
- Entradas: DV[15:0], DR[15:0], INIT
- Salidas: Result[31:0], DONE
- Registros similares al multiplicador

#### Convertidor Binario-BCD
- Algoritmo: Double Dabble
- Entradas: Binario[15:0]
- Salidas: BCD[19:0] (4 dígitos)
- Rango: 0 - 9999

## Metodología de Diseño

### 1. Síntesis de Alto Nivel
Se utilizó el flujo Y de Gajski-Kuhn para transformar descripciones comportamentales a estructurales:

Comportamental → Estructural → Físico
- Especificaciones → Diagramas de flujo → RTL Verilog
- ASM → Camino de datos + Control → Síntesis FPGA

### 2. Máquinas de Estado Algorítmicas (ASM)
Cada periférico se implementa como:
- Camino de Datos: Registros + Unidades Aritméticas
- Unidad de Control: Máquina de estados finitos
- Comunicación: Señales de control y banderas de estado

### 3. Comunicación CPU-Periféricos
Ejemplo de escritura al multiplicador:
sw a0, MULT_OP_A(gp)    // Escribir operando A
sw a1, MULT_OP_B(gp)    // Escribir operando B
li a0, 1
sw a0, MULT_INIT(gp)    // Iniciar operación

// Esperar completación
.L0:
  lw t1, MULT_DONE(gp)
  and t1, t1, t0
  beqz t1, .L0

// Leer resultado
lw a0, MULT_RESULT(gp)

## Flujo de Desarrollo

### 1. Diseño Hardware
1. Especificación de algoritmos (diagramas de flujo)
2. Diseño de camino de datos (registros, ALU)
3. Diseño de unidad de control (diagramas de estados)
4. Implementación RTL en Verilog
5. Síntesis y place & route

### 2. Diseño Software
1. Programación en ensamblador RISC-V
2. Control de periféricos vía memory-mapped I/O
3. Compilación y generación de firmware.hex
4. Carga en memoria de programa

### 3. Verificación
- Simulación funcional: Comportamiento ideal
- Simulación post-síntesis: Lógica sintetizada 
- Simulación post-ruteo: Con retardos reales
- Pruebas en FPGA: Verificación hardware

## Ventajas de la Metodología

### Separación de Concerns
- Humanos: Diseño algorítmico, especificaciones
- Herramientas CAD: Síntesis, optimización, ruteo

### Reutilización
- Periféricos como componentes independientes
- Interface estándar memory-mapped
- Fácil integración en diferentes SoCs

### Escalabilidad
- Adición de nuevos periféricos sin modificar CPU
- Flexibilidad en asignación de direcciones
- Soporte para diferentes algoritmos

## Resultados Esperados

- Funcionalidad: Operaciones aritméticas correctas
- Desempeño: Operaciones en hardware vs software
- Eficiencia: Uso de recursos FPGA optimizado
- Video de prueba: https://youtu.be/ZudS9m8f1rI


