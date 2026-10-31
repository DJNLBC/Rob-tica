## Objetivos y resultados de aprendizaje

* **Reconocimiento y calibración de herramienta:** Reconocer los componentes principales de un robot industrial y realizar la calibración de la herramienta (`tooldata`) tanto en el manipulador real como en la plataforma virtual RobotStudio.
* **Programación e interpolación de movimientos:** Utilizar las instrucciones de movimiento de RAPID (`MoveJ`, `MoveL`, `MoveC`) para la manipulación y el seguimiento continuo de trayectorias.
* **Uso de marcos de trabajo (`wobjdata`):** Crear y reutilizar sistemas de coordenadas de trabajo para ejecutar una misma rutina de trazado en distintas ubicaciones de la celda.
* **Estructuras de programación intermedia en RAPID:** Implementar procedimientos, variables, estructuras condicionales (`IF`, `TEST/CASE`), ciclos (`WHILE`, `FOR`) y rutinas de manejo de errores.
* **Configuración de señales de E/S digitales:** Configurar y operar las entradas (`DI`) y salidas (`DO`) digitales en el controlador IRC5 para la selección de modos de trabajo y representación de estados del sistema.
* **Integración de la celda automatizada:** Coordinar el manipulador ABB IRB 140 con el control bidireccional de una banda transportadora (`FWD_Conveyor` y `BWD_Conveyor`).
* **Implementación del ciclo automático:** Ejecutar la secuencia completa de transporte del pastel, posicionado en la estación, detención de la banda, trazado de decoración y retiro automático del producto terminado.

## Procedimiento y metodología del laboratorio

### 1. Diseño de la herramienta en CAD
Se diseñó un soporte a medida para fijar un marcador o plumón al *flange* del robot ABB IRB 140.
* **Inclinación de 30° para Evitar Singularidades:** Siguiendo la recomendación del laboratorista, la herramienta se diseñó con un ángulo de inclinación de **30°** respecto al eje del *flange*. Esta geometría es crucial para evitar que las articulaciones del muñón del robot (ejes 4 y 6) se alienen colinealmente (*singularidad de muñeca*), garantizando el rango cinemático adecuado para trazar las trayectorias sobre el plano sin bloquear los ejes.

<img width="1366" height="768" alt="Captura de pantalla 2026-10-30 205216" src="https://github.com/user-attachments/assets/0d74e2da-4096-4921-915b-2977bb41e2a8" />


### 2. Calibración de la herramienta (`tooldata`)
* **Entorno Virtual (RobotStudio):** Se importó el modelo CAD de la herramienta y se definió el marco de herramienta local (`MyNewTool`), configurando las coordenadas del TCP a $X = 7.434\text{ mm}$, $Y = 0.864\text{ mm}$, $Z = 121.853\text{ mm}$ y su orientación en cuaterniones.
* **Procedimiento Teórico (Robot Real):** En la celda presencial se contempla el **método de 4 Puntos** (*4-Point Method*) en el FlexPendant: aproximar la punta del marcador a un punto fijo de referencia (*spike*) desde cuatro orientaciones marcadamente distintas para que el controlador calcule por intersección matemática la ubicación exacta del TCP.

https://github.com/user-attachments/assets/628421d2-cc70-49dc-9c80-766e9e4d7f5a

### 3. Creación de Targets, Paths, Trayectorias y Rutinas
* **WorkObject (`cake`) y Configuración de Puntos:** Se definió un sistema de coordenadas de trabajo (`wobjdata cake`) sobre la superficie de la torta. Todos los puntos (`robtarget`) se crearon referenciados a este marco local con la orientación necesaria para mantener la herramienta perpendicular a la superficie.
* **Interpolación de Movimientos:**
  * `MoveJ`: Movimientos en vacío a alta velocidad hacia posiciones de seguridad (`HOME`, Mantenimiento).
  * `MoveL`: Trazado de líneas rectas continuas para letras y contornos rectos (`v100`).
  * `MoveC`: Trazado de arcos y trayectorias curvas redondeadas.
* **Manejo de Alturas:**
  * $Z = 20\text{ mm}$ (zona `fine`): Nivel aéreo de aproximación y transición entre letras.
  * $Z = 1\text{ mm}$ (zona `z10`): Nivel de contacto continuo sobre la superficie para el dibujo.
* **Estructura de Rutinas:**
  * `Path_D`: Trazado del nombre **Diego** en la sección superior.
  * `Path_Kanby`: Trazado de **Vending Mouth Kirby** (*Kirby and the Forgotten Land*) en la sección central.
  * `Path_M`: Trazado del nombre **Miguel** en la sección inferior.

### 4. Configuración de Señales Digitales (E/S)
En el controlador IRC5 (módulo **DSQC 652**) se configuraron e interconectaron las señales necesarias:
* **Entradas Digitales (`DI`):** `DI_01` (Ciclo de decoración automático), `DI_02` (Pose de mantenimiento) y `DI_03` (Rutina *Farmear Aura*).
* **Salidas Digitales (`DO`):** `DO_01`, `DO_02` y `DO_03` como banderas de estado, e interconexiones de potencia `FWD_Conveyor` / `BWD_Conveyor` para accionar el Smart Component de la banda.

### 5. Sincronización de la estación y lógica de la banda transportadora
* **Ajuste Empírico del Tiempo de Tránsito (`WaitTime`):** Al no disponer de la curva o gráfica de velocidad contra tiempo ($v$ vs. $t$) del sistema de la banda, la sincronización de posición se determinó experimentalmente:
  1. Se posicionó el objeto (torta) en el extremo inicial de la banda.
  2. Se mantuvo encendida la banda mediante la señal `FWD_Conveyor`, variando progresivamente el tiempo de retardo (`WaitTime`).
  3. Se ajustó el parámetro hasta comprobar que un tiempo exacto de **4.3 segundos** permitía desplazar el objeto los $629\text{ mm}$ necesarios para dejarlo perfectamente ubicado frente al área de trabajo del robot.
* **Secuencia de Operación:** La lógica en RAPID asegura que la banda permanezca completamente detenida durante la fase de decoración, y no retoma el movimiento para retirar el producto hasta que el robot ha regresado completamente a su posición segura `HOME`.
