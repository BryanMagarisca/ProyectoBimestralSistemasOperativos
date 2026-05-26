# Procesamiento Paralelo de Transacciones de Ventas en C utilizando Hilos y Mecanismos de Sincronización

## Introducción

El procesamiento concurrente es una de las técnicas más importantes dentro de los sistemas operativos y el desarrollo de software de alto rendimiento, ya que permite ejecutar múltiples tareas de manera simultánea para optimizar el uso de los recursos del sistema. Este proyecto implementa un sistema de procesamiento de transacciones de ventas utilizando el lenguaje C y la biblioteca pthread.h, ejecutado en un entorno Ubuntu.

El programa tiene como objetivo procesar miles de registros almacenados en un archivo CSV mediante técnicas de paralelismo con hilos. Para ello, se realiza la limpieza de datos numéricos inválidos, la imputación de categorías faltantes utilizando la moda y la normalización de valores en una escala de 0 a 1. Además, se emplean mecanismos de sincronización como mutex para evitar condiciones de carrera al trabajar con recursos compartidos.

El proyecto compara el rendimiento entre un procesamiento secuencial y uno paralelo utilizando distintos números de hilos, permitiendo analizar la eficiencia del paralelismo y medir los tiempos de ejecución mediante la función clock_gettime(). Asimismo, se utiliza un script en Python para generar automáticamente el conjunto de datos de prueba, simulando miles de transacciones reales con valores nulos y categorías vacías.

## Descripción del Problema

Las empresas y sistemas modernos generan grandes volúmenes de datos que deben procesarse rápidamente. Cuando el procesamiento se realiza de manera secuencial, el tiempo de ejecución aumenta considerablemente conforme crece la cantidad de información.
El problema planteado consiste en procesar un archivo CSV con miles de transacciones de ventas, aplicando distintas operaciones de limpieza y transformación de datos:
- Limpieza de valores nulos numéricos.
- Imputación de categorías vacías utilizando la moda. 
- Normalización de datos en una escala de 0 a 1.

Para resolver este problema, se implementó un sistema concurrente utilizando hilos en C, distribuyendo el trabajo entre varios procesos de ejecución para mejorar el rendimiento del programa.

## Explicación del Proyecto

### Generación del Dataset


Para simular un entorno real, se utilizó un script en Python que genera automáticamente un archivo transacciones.csv con 5000 registros aleatorios.

El dataset contiene:

- ID de transacción.
- Valor numérico.
- Categoría de producto.
- Valores negativos para simular datos nulos o erróneos. 
- Categorías vacías para probar la imputación de datos.
  
Las categorías que se utilizaron son:

- Electronica
- Hogar
- Ropa

## Funcionamiento del Programa

### Lectura del archivo CSV

El programa inicia en la función main, donde se abre el archivo transacciones.csv utilizando funciones de manejo de archivos en C. Cada línea del archivo es leída y separada mediante strtok(), permitiendo extraer el identificador, el valor numérico y la categoría de cada transacción. Posteriormente, los datos se almacenan en arreglos de estructuras (struct) para facilitar su procesamiento durante la ejecución del programa.

### Cálculo de la Moda

Antes de iniciar el procesamiento de los datos, el programa recorre todas las transacciones para identificar cuál es la categoría más frecuente del dataset. Para ello, se utilizan contadores que registran cuántas veces aparece cada categoría (ELECTRONICA, HOGAR y ROPA). La categoría con mayor cantidad de repeticiones se almacena en una variable global llamada moda_global, la cual será utilizada posteriormente para reemplazar categorías vacías.

### Limpieza de Datos

El programa realiza una validación de los valores numéricos de cada transacción. Cuando detecta un valor negativo, este es considerado un dato inválido o nulo, por lo que automáticamente es reemplazado por 0. Además, se incrementa un contador global que registra cuántos valores fueron limpiados durante el procesamiento de los datos.

### Imputación de Categorías

Si una transacción contiene una categoría vacía, el programa utiliza la moda calculada previamente para completar el dato faltante. Esto permite mantener consistencia en el dataset y evitar registros incompletos. Cada vez que se reemplaza una categoría vacía, también se actualiza un contador global que almacena el total de categorías imputadas.

### Normalización de Datos

Después de limpiar los datos, el programa transforma los valores numéricos a una escala entre 0 y 1 utilizando el método de normalización Min-Max. Esto permite que todos los datos tengan una misma escala y facilita futuros análisis o comparaciones.

Se Utilizo: 

<img width="318" height="85" alt="aml-normalization-minmax" src="https://github.com/user-attachments/assets/b72e370a-a5d2-4f47-a9d6-0c35dd7efa2a" />

### Procesamiento Secuencial

En el modo secuencial, todas las transacciones son procesadas utilizando únicamente el hilo principal (main). El programa recorre el dataset fila por fila ejecutando las tareas de limpieza, imputación y normalización. Este modo sirve como referencia para comparar el rendimiento frente al procesamiento paralelo.

### Procesamiento Paralelo

En el modo paralelo, el dataset se divide en bloques de filas y cada bloque es asignado a un hilo diferente. Los hilos ejecutan simultáneamente la función encargada de procesar las transacciones, permitiendo que varias tareas se realicen al mismo tiempo. El programa realiza pruebas utilizando 3 y 4 hilos para analizar cómo cambia el rendimiento al aumentar el nivel de paralelismo.

### Sincronización

Debido a que varios hilos acceden y modifican variables globales compartidas, el programa utiliza pthread_mutex_t como mecanismo de sincronización. Los mutex permiten bloquear temporalmente una sección crítica del código para evitar que dos hilos modifiquen los contadores al mismo tiempo, previniendo errores conocidos como condiciones de carrera.

### Medición de Tiempo

El programa utiliza la función clock_gettime() para medir con precisión los tiempos de ejecución. Se registra el tiempo individual de cada hilo durante el procesamiento paralelo y también el tiempo total tanto del modo secuencial como del modo paralelo. Estos resultados permiten comparar el rendimiento y demostrar la eficiencia del procesamiento concurrente.

## Instrucciones de Ejecución

1. Generar el archivo CSV mediante el script de python
2. Verificar la ruta del archivo CSV
3. Compilar el programa
4. Ejecutar el programa

## Ejecución del programa

En la ejecución del programa se muestran los siguientes resultados:

![Datos originales](datos%20originales.jpeg)

---

![Gráfico de tiempos](Tiempos%20de%20ejecución.jpeg)

---

![Datos procesados](datos%20procesados.jpeg)

## Tabla Comparativa de Rendimiento

| Modo de ejecución | Número de hilos | Tiempo de ejecución (s) | Speedup | Eficiencia |
|-------------------|----------------|--------------------------|----------|-------------|
| Secuencial        | 1              | 0.00011                  | 1.00     | 100 %       |
| Paralelo          | 3              | 0.00277                  | 0.04     | 1.3 %       |
| Paralelo          | 4              | 0.00054                  | 0.20     | 5 %         |

---

## Cálculo del Speedup

El speedup permite medir la mejora del rendimiento del procesamiento paralelo respecto al procesamiento secuencial.

$$
\
Speedup = \frac{T_{secuencial}}{T_{paralelo}}
\
$$

Donde:

- $\(T_{secuencial}\)$ = tiempo del modo secuencial
- $\(T_{paralelo}\)$ = tiempo del modo paralelo

---

### Speedup para 3 hilos

$$
\
Speedup = \frac{0.00011}{0.00277} \approx 0.04
\
$$

---

### Speedup para 4 hilos

$$
\
Speedup = \frac{0.00011}{0.00054} \approx 0.20
\
$$

## Cálculo de la Eficiencia

La eficiencia mide el aprovechamiento de los hilos utilizados.

### Fórmula

$$
Eficiencia = \frac{Speedup}{Número\ de\ hilos}
$$

---

### Eficiencia para 3 hilos

$$
Eficiencia = \frac{0.04}{3} \approx 0.013
$$

$$
Eficiencia \approx 1.3\text{ por ciento}
$$

---

### Eficiencia para 4 hilos

$$
Eficiencia = \frac{0.20}{4} \approx 0.05
$$

$$
Eficiencia \approx 5\text{ por ciento}
$$

---

El procesamiento secuencial presentó un mejor rendimiento debido a que el conjunto de datos utilizado era relativamente pequeño. En estas condiciones, el tiempo requerido para crear, administrar y sincronizar múltiples hilos supera el beneficio obtenido mediante paralelismo.

Además, operaciones como la creación de threads, división de bloques de trabajo y uso de mecanismos de sincronización introducen un overhead adicional que afecta el tiempo total de ejecución.

Por esta razón, el modo secuencial resultó más eficiente para este escenario específico. Sin embargo, en aplicaciones con datasets significativamente más grandes, el procesamiento paralelo suele ofrecer mejoras importantes de rendimiento.

## Conclusiones

- Se desarrolló correctamente un programa en lenguaje C capaz de procesar transacciones desde archivos CSV utilizando tanto procesamiento secuencial como procesamiento paralelo mediante pthreads.
- Los resultados de la ejecución mostraron que, para el tamaño actual del dataset utilizado, el procesamiento secuencial obtuvo un mejor rendimiento que las versiones paralelas de 3 y 4 hilos. Esto se debe al overhead generado por la creación, administración y sincronización de threads.
- El análisis de speedup y eficiencia permitió evidenciar que el paralelismo no siempre garantiza mejoras de rendimiento, especialmente cuando la carga de trabajo es pequeña y el costo de coordinación entre hilos supera el beneficio del procesamiento concurrente.
- El proyecto permitió reforzar los temas correspondientes de creación y manejo de hilos además de la importancia de estos en entornos multitarea.

## Bibliografía

- [1] Actian Corporation, "¿Qué es la normalización de datos?," Actian, 12 de septiembre de 2023. [En línea]. Disponible en: https://www.actian.com/es/blog/data-intelligence/what-is-data-normalization/ [Accedido: 25-may-2026].

- [2] SearchDataCenter, "What is Parallel Processing?," TechTarget. [En línea]. Disponible en: https://www.techtarget.com/searchdatacenter/definition/parallel-processing [Accedido: 23-may-2026].

- [3] GeeksforGeeks, "Introduction of Process Synchronization," GeeksforGeeks, 19 de junio de 2015. [En línea]. Disponible en: https://www.geeksforgeeks.org/operating-systems/introduction-of-process-synchronization/ [Accedido: 23-may-2026].

- [4] G. Amagua, "RPubs - Imputacion de Datos," RPubs.com, 06 de julio de 2021. [En línea]. Disponible en: https://rpubs.com/Gabriel-Amagua/788490 [Accedido: 24-may-2026].

- [5] M. Rossainz López, "Programación Concurrente y Paralela 4.1. El Factor de rendimiento SpeedUp," Facultad de Ciencias de la Computación, BUAP. [En línea]. Disponible en: https://www.cs.buap.mx/~rossainz/PCyP_Maestria/1_Apuntes/6_ProgParalela1.pdf [Accedido: 25-may-2026].

