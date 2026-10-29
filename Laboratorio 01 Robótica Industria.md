# Laboratorio 01: Robótica Industrial — Trayectorias, Entradas y Salidas Digitales

## Objetivos y Resultados de Aprendizaje

* **Reconocimiento y calibración de herramienta:** Reconocer los componentes principales de un robot industrial y realizar la calibración de la herramienta (`tooldata`) tanto en el manipulador real como en la plataforma virtual RobotStudio.
* **Programación e interpolación de movimientos:** Utilizar las instrucciones de movimiento de RAPID (`MoveJ`, `MoveL`, `MoveC`) para la manipulación y el seguimiento continuo de trayectorias.
* **Uso de marcos de trabajo (`wobjdata`):** Crear y reutilizar sistemas de coordenadas de trabajo para ejecutar una misma rutina de trazado en distintas ubicaciones de la celda.
* **Estructuras de programación intermedia en RAPID:** Implementar procedimientos, variables, estructuras condicionales (`IF`, `TEST/CASE`), ciclos (`WHILE`, `FOR`) y rutinas de manejo de errores.
* **Configuración de señales de E/S digitales:** Configurar y operar las entradas (`DI`) y salidas (`DO`) digitales en el controlador IRC5 para la selección de modos de trabajo y representación de estados del sistema.
* **Integración de la celda automatizada:** Coordinar el manipulador ABB IRB 140 con el control bidireccional de una banda transportadora (`FWD_Conveyor` y `BWD_Conveyor`).
* **Implementación del ciclo automático:** Ejecutar la secuencia completa de transporte del pastel, posicionado en la estación, detención de la banda, trazado de decoración y retiro automático del producto terminado.