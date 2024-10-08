# Blog asignatura Robótica Móvil
Este es el blog que usaré para la asignatura **Robótica Móvil**. Es este blog, iré subiendo todas las prácticas y el resultado final de cada una de ellas.

## Práctica 1 Vacuum Cleaner

## Introducción
La primera práctica consiste en desarrollar un algoritmo básico de navegación para un robot aspirador autónomo, con el objetivo de recorrer la mayor superficie posible de una casa. El algoritmo de navegación se basa en la aleatoridad del mismo, usando una máquina de estados, un láser y bumpers para detectar los obstáculos.

## Objetivo del código

El objetivo es realizar un algoritmo de navegación que sea reactivo y lo más eficiente posible. Para ello, se utilian tres estados o movimientos básicos:

1. **FORWARD**: el robot avanza en línea recta un tiempo aleatorio o hasta que detecte un obstáculo cercano, momento en el que cambia al siguiente estado.
2. **TURN**: el robot cambia de dirección un tiempo aleatorio o hasta que se choca con un obstáculo.
3. **SPIRAL**: el robot realiza un movimiento en espiral hasta que se choca.

## Funcionamiento del Algoritmo

### Detección de Obstáculos

El robot usa dos tipos de sensores: bumpers y láser. La función **check_bumpers** como su propio nombre indica, verifica los tres bumpers y dependiendo de cuál haya sido golpeado tiene un sentido de giro u otro. Por otra parte, la función **get_laser_data** analiza los datos del láser y cambia al estado *TURN* si detecta una distancia menor a la espedificada.

### Movimiento hacia delante

En el estado *FORWARD*, el robot avanza e línea recta hasta que detecta un obstáculo o se activa un bumper, sin embargo, cuando el tiempo de movimiento supera un tiempo random **random.uniform(3,5)** sin detectar obstáculos, se asume que el robot está en un área amplia y cambia al estado *SPIRAL*.

### Giro y Navegación Aleatoria

Cuando el robot entra en estado *TURN*, comienza a girar. La dirección del giro depende de la ubicación del obstáculo detectado:
- Si se activa el bumper izquierdo o central, el robot gira en sentido antihorario.
- Si se activa el bumper derecho, el giro es en sentido horario.

Además, el tiempo de giro es aleatorio entre 1 a 2 segundos.

### Movimiento en Espiral

La fórmula utilizada para incrementar el radio de la espiral es sencillo: se aumenta gradualmente la velocidad lineal, lo que genera un patrón en espiral, manteniendo la velocidad angular constante. 

### Grafo

![p1_movil](https://github.com/user-attachments/assets/bacb8324-6db4-4b35-a0af-c727caf8205c)


## Dificultades

A la hora de hacer la práctica, he tenido dos dificultades principalmente. Una de ellas fue el uso de variables globales para el cambio de estado de la máquina de estados, ya que al querer encapsular el código en funciones no lo tuve en cuenta inicialmente.

Otra dificultad que tuve fue la optimización de tiempos aleatorios ya que, por ejemplo, si establecía un tiempo de giro muy largo, el robot no podía salir de una sala, lo que afectaba su eficiencia en la cobertura del área.

## Vídeo final

Robot lanzado durante 5 minutos:

![WhatsApp Image 2024-09-28 at 12 37 31](https://github.com/user-attachments/assets/35c3cc7b-1ea9-4691-b063-0f53ef57b2df)


Robot lanzado durante 15 minutos:

![WhatsApp Image 2024-09-28 at 13 00 39](https://github.com/user-attachments/assets/f2cf608f-06c1-48fe-86d7-b3b0442b96f5)


Como se puede ver en ambas imágenes superiores, pese a dejarlo más tiempo, el área limpia es apenas un poco más grande, esto se debe a una de las dificultades comentadas anteriormente. Una vez realizado una optimización de tiempos, el resultado final es el siguiente (15 minutos):

![WhatsApp Image 2024-09-28 at 14 23 00](https://github.com/user-attachments/assets/fef829de-ca47-4eb8-bcec-0bb7c528dc97)


Vídeo de 5 minutos:

https://github.com/user-attachments/assets/19bc4d9d-6ea9-4109-a2d1-ef55ff16093b

Para ver más pruebas y el vídeo anterior en mejor calidad, pincha en el siguiente [enlace](https://youtu.be/_ddD5BQwkCw)

