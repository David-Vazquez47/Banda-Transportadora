# Proyecto: Retrofitting de Banda Transportadora y UR3e

# Retrofitting de Banda Transportadora Electroneumática — Celda Colaborativa UR3e

**Institución:** Universidad Modelo — Escuela de Ingeniería Mecatrónica  
**Asignatura:** Proyectos VII  
---

## Descripción del Proyecto

El presente repositorio documenta el desarrollo iterativo, el diseño conceptual y la implementación técnica para el retrofitting de una banda transportadora electroneumática y su integración colaborativa con un brazo robótico Universal Robots (UR3e), utilizando un controlador lógico programable Delta.

La propuesta sustituye la arquitectura original basada en un PLC Siemens S7-1200 y una pantalla HMI KTP700 Basic por un sistema abierto compuesto por un PLC Delta DVP-12SA2, módulos de expansión digital (DVP-08SM y DVP-08SN) y una pantalla HMI Delta DOP-103BQ, coordinando tareas de transporte, sensado, retención neumática y manipulación de piezas (Pick & Place).

## Estructura Actual del Repositorio

En esta fase del proyecto, el repositorio se compone de los siguientes elementos y directorios:

* `/Banda Entradas y Salidas/` : Directorio destinado a las pruebas preliminares de hardware. Contiene rutinas básicas para la validación física y el mapeo de las entradas y salidas del PLC. No contiene el código oficial ni la lógica de control final de la secuencia de la banda.
* `/HMI ADAPTER/` : Directorio correspondiente al diseño mecánico asistido por computadora (CAD). Contiene los archivos y modelos 3D (.STEP / .STL) para la fabricación del marco adaptador destinado a la instalación de la pantalla HMI DOP-103BQ (4.3") en el gabinete.
* `/Eplan Retrofit-Banda.edb/` y `Eplan Retrofit-Banda.elk` : Base de datos y archivo de acceso del proyecto esquemático en EPLAN Electric P8 & Fluid (v2026). Contiene el diseño completo de potencia (220 VAC / 24 VDC), sinóptico de hardware, conexionado de entradas/salidas y circuito electroneumático bajo normativa internacional (IEC 81346 / ISO 1219).
* `Planos_Electroneumaticos_Banda_v1.0_20Oct26.pdf` : Expediente técnico oficial de planos en formato PDF vectorial interactivo. Incluye portada institucional, índice automatizado, diagramas de conexionado del PLC y actuadores con referencias cruzadas activas.
* `README.md` : Documentación técnica principal y guía general del repositorio.

## Mapeo de Entradas y Salidas (I/O Map)

La asignación de variables físicas y lógicas del sistema se distribuye de la siguiente manera:

* **Entradas Digitales (CPU `-PLC1` y Módulo `-PLC2`):**
  * `X0`: Paro de Emergencia local (`-S1`, contacto NC a 0 VDC)
  * `X1`: Botón de Paro general (`-S2`, contacto NC a 0 VDC)
  * `X2`: Botón de Arranque / Start (`-S3`, pulsador NA `SWB` a 0 VDC)
  * `X3`: Sensor capacitivo LJC (`-B1`, señal NPN — Presencia inicial)
  * `X4`: Sensor inductivo (`-B2`, señal NPN — Detección de metal)
  * `X5`: Sensor fotoeléctrico Omron E3Z (`-B3`, señal NPN — Trayectoria)
  * `X6`: Sensor fotoeléctrico Omron E3Z (`-B4`, señal NPN — Fin de ciclo)
  * `X20`: Paro redundante desde UR3e Canal 1 (`UR_CO0`)
  * `X21`: Paro redundante desde UR3e Canal 2 (`UR_CO1`)
  * `X22`: Bandera de Handshake *Brazo Libre* (`UR_DO0_FREE` desde salida DO0 de UR3e)

* **Salidas Digitales (CPU `-PLC1` y Módulo `-PLC3`):**
  * `Y0`: Comando marcha adelante variador JSCC (`JSCC_FWD`)
  * `Y1`: Comando marcha reversa variador JSCC (`JSCC_REV`)
  * `Y20`: Paro redundante hacia UR3e Canal 1 (`UR_CI0`)
  * `Y21`: Paro redundante hacia UR3e Canal 2 (`UR_CI1`)
  * `Y22`: Bandera de Handshake *Pieza Lista* (`UR_DI0_READY` hacia entrada DI0 de UR3e)
  * `Y23`: Solenoide Cilindro A1 Retención Inicial (`-MB1`, electroválvula 5/2 monoestable)
  * `Y24`: Solenoide Cilindro A2 Retención Lectura (`-MB2`, electroválvula 5/2 monoestable)
  * `Y25`: Solenoide Cilindro A3 Clasificador Avance (`-MB3A`, electroválvula 5/3 centros cerrados)
  * `Y26`: Solenoide Cilindro A3 Clasificador Retroceso (`-MB3B`, electroválvula 5/3 centros cerrados)

## Trazabilidad

El registro de cambios de este repositorio servirá como bitácora técnica para evidenciar la evolución del diseño, la corrección de errores durante las simulaciones y la justificación de las decisiones de ingeniería tomadas a lo largo de las distintas fases de evaluación.
