# Retrofitting de Banda Transportadora Electroneumática — Celda Colaborativa UR3e

**Institución:** Universidad Modelo — Escuela de Ingeniería Campus Mérida  
**Carrera:** Ingeniería Mecatrónica  
**Asignatura:** Proyectos VII (Modelo D+i — Ciclo AD26)  
**Proyecto:** Proyecto 06 (06 Banda)  
**Docente Titular / Evaluador:** Dr. Rodrigo Daniel Solís Ortega (`rodrigo.solis@modelo.edu.mx`)  
**Autor y Responsable:** Angel David Vázquez Mayo (Matrícula: 15222200)  
**Estado del Proyecto:** Punto de Control 4 (PC4) — Implementación Física *As-Built* y Primera Prueba Funcional  
**Fecha de Corte:** Noviembre 2026  

---

## Enlaces de Acceso Rápido y Auditoría

* **Repositorio Oficial en GitHub:** <https://github.com/David-Vazquez47/Banda-Transportadora>
* **Video Demostrativo de la Primera Prueba Funcional (OneDrive Institucional):** <https://unimodelo-my.sharepoint.com/:f:/g/personal/15222200_modelo_edu_mx/IgBXGgjEDKdsRJDtmtSQftZdAWVhwc_SwbMox4_IU4dAshE?e=babSxk>
* **Expediente de Planos As-Built (PDF Vectorial):** `/EPLAN As-Built/Planos_Electroneumaticos_AsBuilt_v1.0.pdf`

---

## 1. Descripción del Proyecto

El presente repositorio documenta el desarrollo iterativo, la reingeniería física y lógica (*retrofitting*), y el comisionamiento en taller de una celda de manufactura colaborativa integrada por una banda transportadora electroneumática didáctica y un brazo robótico industrial **Universal Robots UR3e**.

La solución sustituye la arquitectura cerrada Siemens previa (PLC SIMATIC S7-1200 CPU 1215 y pantalla táctil KTP700 Basic de 7") por un ecosistema abierto, modular y accesible gobernado por tecnología **Delta Electronics** (PLC Delta DVP-12SA2, módulos de expansión de la serie DVP-S y pantalla HMI Delta DOP-103BQ de 4.3"). La estación coordina tareas de transporte continuo, sensado y clasificación de probetas cilíndricas (metálicas y plásticas), retención electroneumática a 6 bar, protocolo de intercambio determinista de banderas (*Handshake* a 24 VDC) y rutinas de recolección cinemática (*Pick & Place*).

---

## 2. Arquitectura de Hardware y Control As-Built

El sistema físico en banco de pruebas opera bajo las siguientes especificaciones técnicas:

* **Unidad Central de Procesamiento (CPU):** Delta DVP-12SA211T (`-PLC1`) montada en riel DIN, con 8 entradas digitales configuradas en modo SINK (+24 VDC en común `S/S`) y salidas a transistor NPN de alta velocidad.
* **Módulo de Expansión de Entradas:** Delta DVP-08SM11N (`-PLC2`) acoplado por bus lateral DVP-S, configurado en modo SOURCE (común `S/S` a 0 VDC) para recepción directa de señales PNP a 24 VDC emitidas por el robot UR3e.
* **Módulo de Expansión de Salidas:** Delta DVP-08SN11T (`-PLC3`) acoplado por bus lateral DVP-S, con salidas a transistor a 24 VDC para gobierno de electroválvulas y emisión de banderas de estado hacia el UR3e.
* **Interfaz Hombre-Máquina (HMI):** Delta DOP-103BQ (`-HMI1`, pantalla TFT de 4.3", 480 × 272 px), empotrada en el panel mediante un marco adaptador de MDF de 3 mm cortado por láser y comunicada vía RS-232 por el puerto COM1 (9600 bps, 7-E-1).
* **Tracción y Accionamiento de Potencia:** Variador electrónico monofásico JSCC serie YT comandado directamente a colector abierto NPN (`JSCC_FWD`, `JSCC_REV` referenciados al común de 0 VDC, sin relés mecánicos intermedios para supresión de arco y ruido EMI) acoplado a motorreductor de inducción YT (`+M1-M1`).
* **Alimentación y Protecciones:** Fuente conmutada regulada Siemens LOGO!Power (24 VDC / 1.3 A, `-G1`), estabilizada en 24.00 VDC bajo carga máxima simultánea, protegida aguas arriba por disyuntor termomagnético general (`-F1`).
* **Actuadores Neumáticos (Alimentación a 6 bar):**
  * **Cilindro A1 (Retención inicial):** Cilindro de doble efecto comandado por electroválvula 5/2 monoestable con retorno por resorte (`-MB1`).
  * **Cilindro A2 (Lectura de material):** Cilindro de doble efecto comandado por electroválvula 5/2 monoestable con retorno por resorte (`-MB2`).
  * **Cilindro A3 (Clasificador):** Cilindro de doble efecto gobernado por electroválvula 5/3 de centros cerrados con doble solenoide (`-MB3A` avance / `-MB3B` retroceso).

---

## 3. Estructura del Repositorio (Regla Institucional CACEI / D+i)

El repositorio mantiene una estructura estricta de archivos fuente editables, suprimiendo carpetas vacías (0 KB), archivos comprimidos monolíticos (`.zip`/`.rar`) o tablas de hashes simuladas:

```text
Banda-Transportadora/
│
├── .gitignore
├── README.md
│
├── Banda Entradas y Salidas/          # Software y lógica de control del autómata
│   ├── Retrofit_Banda_DVP.project     # Proyecto fuente editable en Delta ISPSoft v3.18
│   ├── COMMGR_Config.cfg              # Configuración de drivers serie RS-232 en COMMGR
│   └── Device_Comment_List.csv        # Mapeo y documentación de variables X, Y, M, T
│
├── HMI DOP-103BQ/                     # Interfaz Hombre-Máquina
│   ├── Interfaz_Banda_UR3e.dop        # Proyecto fuente editable en Delta DOPSoft v4.x
│   └── Screens_Overview.pdf           # Memoria de diseño de las 4 pantallas operativas
│
├── HMI ADAPTER/                       # Ingeniería mecánica de adaptación dimensional
│   ├── Marco_HMI_DOP103BQ.step        # Modelo 3D volumétrico paramétrico neutral
│   ├── Marco_HMI_DOP103BQ.stl         # Malla poligonal para manufactura aditiva
│   ├── Vector_Corte_Laser_3mm.dxf     # Archivo vectorial 2D para corte láser CNC
│   └── Marco_del_HMI_Plano.pdf        # Plano mecánico acotado bajo norma con tolerancias
│
├── EPLAN As-Built/                    # Documentación de ingeniería eléctrica y fluidos
│   ├── Eplan Retrofit-Banda.edb/      # Base de datos y proyecto nativo en EPLAN 2026
│   ├── Eplan Retrofit-Banda.elk       # Archivo maestro de enlace EPLAN Electric P8 & Fluid
│   └── Planos_Electroneumaticos_AsBuilt_v1.0.pdf  # Expediente completo vectorial de láminas As-Built
│
└── UR3e Scripts/                      # Rutinas de control para celda colaborativa
    ├── Rutina_Pick_and_Place.urp      # Programa nativo en Universal Robots PolyScope
    └── Handshake_Logic.script         # Script de control de waypoints e interbloqueos
```

---

## 4. Mapeo de Entradas y Salidas As-Built (I/O Map)

El direccionamiento se encuentra estructurado bajo la notación octal nativa del autómata Delta DVP:

### Entradas Digitales (CPU `-PLC1` y Módulo `-PLC2`)

| Dirección | Etiqueta / Tag | Dispositivo Asociado | Modo / Lógica | Función en el Proceso |
| :---: | :--- | :--- | :---: | :--- |
| **X0** | `-S1` | Botón Paro de Emergencia | SINK (0 VDC) | Apertura de seguridad local de la celda (NC). |
| **X1** | `-S2` | Pulsador de Paro General | SINK (0 VDC) | Paro de ciclo en curso al concluir secuencia (NC). |
| **X2** | `-S3` | Pulsador de Arranque | SINK (0 VDC) | Puesta en marcha del ciclo de transporte (NA). |
| **X3** | `-B1` | Sensor Capacitivo LJC | SINK (NPN) | Detección inicial de pieza en tolva de admisión. |
| **X4** | `-B2` | Sensor Inductivo | SINK (NPN) | Discriminación de material (1: Metal / 0: No Metal). |
| **X5** | `-B3` | Fotocelda Omron E3Z | SINK (NPN) | Verificación óptica en trayectoria de transporte. |
| **X6** | `-B4` | Fotocelda Omron E3Z | SINK (NPN) | Detección de pieza posicionada en rampa final. |
| **X7** | *RESERVA* | Borne Libre | — | Reserva técnica en bornera de CPU. |
| **X20** | `UR_CO0` | Salida Configurable UR3e | SOURCE (+24 VDC) | Canal 1 de Paro Redundante desde robot UR3e. |
| **X21** | `UR_CO1` | Salida Configurable UR3e | SOURCE (+24 VDC) | Canal 2 de Paro Redundante desde robot UR3e. |
| **X22** | `UR_DO0_FREE` | Salida Digital DO0 UR3e | SOURCE (+24 VDC) | Bandera Handshake: Brazo en reposo / zona despejada. |

### Salidas Digitales (CPU `-PLC1` y Módulo `-PLC3`)

| Dirección | Etiqueta / Tag | Dispositivo Asociado | Conexión / Nivel | Función en el Proceso |
| :---: | :--- | :--- | :---: | :--- |
| **Y0** | `JSCC_FWD` | Variador de Velocidad | Transistor NPN (0 V) | Marcha adelante motorreductor de banda YT. |
| **Y1** | `JSCC_REV` | Variador de Velocidad | Transistor NPN (0 V) | Marcha reversa motorreductor de banda YT. |
| **Y20** | `UR_CI0` | Entrada Configurable UR3e | Transistor (+24 VDC) | Canal 1 de Paro Redundante hacia robot UR3e. |
| **Y21** | `UR_CI1` | Entrada Configurable UR3e | Transistor (+24 VDC) | Canal 2 de Paro Redundante hacia robot UR3e. |
| **Y22** | `UR_DI0_READY` | Entrada Digital DI0 UR3e | Transistor (+24 VDC) | Bandera Handshake: Pieza lista para recolección. |
| **Y23** | `-MB1` | Solenoide Cilindro A1 | Transistor (+24 VDC) | Retención de admisión de pieza (Válvula 5/2). |
| **Y24** | `-MB2` | Solenoide Cilindro A2 | Transistor (+24 VDC) | Retención en estación de sensado (Válvula 5/2). |
| **Y25** | `-MB3A` | Solenoide Cilindro A3 | Transistor (+24 VDC) | Avance vástago clasificador (Válvula 5/3). |
| **Y26** | `-MB3B` | Solenoide Cilindro A3 | Transistor (+24 VDC) | Retroceso vástago clasificador (Válvula 5/3). |
| **Y27** | *RESERVA* | Borne Libre | — | Reserva técnica en módulo DVP-08SN. |

---

## 5. Protocolo de Sincronización Handshake y Filosofía Failsafe

La comunicación colaborativa entre la banda transportadora y el brazo UR3e se rige por una máquina de estados determinista a 24 VDC:

1. **Condición de Espera:** El robot UR3e mantiene su salida `DO0` en nivel alto (`UR_DO0_FREE` = 1), indicando que su efector final se encuentra en posición de reposo (`WP_Home`) fuera de la trayectoria del transportador.
2. **Petición de Recolección:** Al arribar una probeta al sensor final `-B4` (`X6`), el PLC detiene el avance de la banda y activa la salida `Y22` (`UR_DI0_READY` = 1).
3. **Bloqueo de Zona:** El robot UR3e recibe la bandera en `DI0`, transiciona su salida `DO0` a nivel bajo (`UR_DO0_FREE` = 0) para bloquear el área e inicia su trayectoria cinemática hacia `WP_Pick`.
4. **Liberación y Rearme:** Tras recoger la pieza y elevarse a cota segura (`WP_Lift`), el robot conmuta nuevamente `DO0` a nivel alto (`UR_DO0_FREE` = 1), señal que reinicia la máquina de estados en el PLC para admitir un nuevo ciclo.
5. **Comportamiento Failsafe (Timeout T3):** Si tras emitirse `UR_DI0_READY` el manipulador no responde en un lapso de **15.0 segundos** ($K150$ en ISPSoft), o si la probeta es retirada manualmente de la rampa, el temporizador $T3$ cancela la salida $Y22$, detiene el sistema y dispara la alarma `ALM_01` en la HMI Delta DOP-103BQ, impidiendo bloqueos indefinidos (*deadlocks*).

---

## 6. Estrategia de Ramas y Registro de Commits

En cumplimiento de las buenas prácticas de ingeniería de software y control de versiones, el desarrollo se gestionó mediante ramas temáticas segregadas consolidadas progresivamente hacia la rama `main` a través de *Pull Requests*:

* `feature/hardware-retrofit`: Reingeniería de esquemas EPLAN, diseño CAD de adaptador HMI y ruteo físico.
* `feature/handshake-logic`: Implementación en ISPSoft de etapas GRAFCET, direccionamiento octal y timeout $T3$.
* `feature/hmi-screens`: Diseño gráfico en DOPSoft v4.x de las pantallas de Dashboard, Auto, Manual y Alarmas.
* `feature/ur3e-polyscope`: Programación de comandos `MoveJ`/`MoveL` y lógica de interbloqueo en PolyScope.

Los mensajes de commit se registran bajo el estándar **Conventional Commits** (`feat:`, `fix:`, `docs:`, `chore:`), evidenciando un desarrollo continuo y cronológico a lo largo de las semanas lectivas del ciclo AD26.

---

## 7. Herramientas de Ingeniería y Normativas Aplicadas

* **Diseño Eléctrico y Neumático:** EPLAN Electric P8 & EPLAN Fluid (v2026).
* **Control Lógico y Comunicaciones:** Delta ISPSoft v3.18 y gestor Delta COMMGR v1.x.
* **Desarrollo de Interfaz de Operador:** Delta DOPSoft v4.x (Resolución 480 × 272 px).
* **Control Cinemático Robótico:** Universal Robots PolyScope (UR3e e-Series).
* **Modelado Mecánico CAD:** Software paramétrico 3D para mecanizado de placa adaptadora.
* **Normativas de Cumplimiento:**
  * **NOM-001-SEDE-2018:** Instalaciones eléctricas (utilización y canalizaciones).
  * **IEC 60204-1:** Seguridad de las máquinas — Equipo eléctrico de las máquinas.
  * **ISO 1219:** Sistemas de transmisiones hidráulicas y neumáticas — Símbolos gráficos y esquemas de circuito.
  * **IEC 81346:** Estructuración y designación de sistemas industriales (`=BANDA+A1` / `=BANDA+M1`).