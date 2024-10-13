# Blog asignatura Robótica Móvil
Este es el blog que usaré para la asignatura **Robótica Móvil**. Es este blog, iré subiendo todas las prácticas y el resultado final de cada una de ellas.

## Práctica 2 Follow Line

## Introducción
La segunda práctica consiste en hacer un sigue líneas con un controlador PID, o PD en mi caso, para que de forma reactiva pueda completar una vuelta en varios circuitos. La implementación de esta práctica se puede dividir en dos partes principales: filtro de color y controlador.

## Filtro de color
Como el propio nombre de la práctica explica, hay que seguir una línea dibujada en el suelo del circuito. La línea es de color rojo, por lo que para la implementación del filtro del color hay que tener varias ideas claras. En un principio usé el [ejemplo3](https://github.com/urjc-docencia-robotica-movil/p2-follow-line-24-25-rsanchez2021/blob/main/ejemplos/image_red_filter.py) que nos daban ya hecho para el filtro de color, pero el filtro que nos daban era en RGB y lo teníamos que hacer en HSV. Para poder realizarlo en HSV hay que tener en cuenta que el color rojo puede tener valores de H separados, es decir, de 0 a 10 y de 175 a 179 aproximadamente.

![image](https://github.com/user-attachments/assets/d6971bda-4475-4992-a938-2069973713b9)

Con ayuda del [ejemplo2](https://github.com/urjc-docencia-robotica-movil/p2-follow-line-24-25-rsanchez2021/blob/main/ejemplos/image_iteration.py) y modificándolo para que los valores estén en HSV, se puede detectar que el color exacto de la línea roja es:

![Screenshot from 2024-10-11 10-25-30](https://github.com/user-attachments/assets/386c8ba8-bf55-44fe-a03d-f373ead33934)

Por lo que para el filtro de color bastará con poner sólo un rango:

```python
lower_red = np.array([0,200,200])
upper_red = np.array([10,210,214])
```

Una vez tenemos el filtro de color realizado, podemos calcular su centroide para posteriormente calcular el error. Para ello, se han usado los momentos y siguiendo la siguiente ecuación:

![image](https://github.com/user-attachments/assets/3e0a844b-fbe7-4cab-9f8b-9b94cb1b1d5b)

Posteriormente se dibuja el círculo y el filtro quedaría de la siguiente forma:

FALTA AÑADIR IMAGEN

## Controlador
Una vez detectada la línea y su centrode, es necesario implementar un controlador **PID** que permita al coche ajustar su dirección. En mi caso, se ha usado un controlador **PD** , sin la parte integral, ya que usando simplemente la parte Proporcional para corregir el error actual y la parte derivativa para suavizar el movimiento es suficiente.

Para calcular el error, se ha establecido que el valor central esperado es el centro de la imagen (320) en el eje X, mientras que el valor del eje Y no se usa para el cálculo del error.

Tras muchos intentos de prueba y error, los valores de las ganancias usadas son:

Circuito simple:
- Kp =
- Kd =

Circuito Ackermann:
- Kp =
- Kd =

## Dificultades
La mayor dificultad sin ninguna duda ha sido encontrar los valores de las ganancias del controlador PD ya que ha sido mediante prueba y error y al empezar la práctica tarde estas pruebas las he tenido que hacer los últimos días y la platadorma ha dado varios errores.

## Vídeos

Vídeo controlador **P** a baja velocidad inestable:

[p2_movil_controladorP_sobreoscilado_y_lento.webm](https://github.com/user-attachments/assets/a607a05d-c7af-4482-87c2-9811d09fd712)


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

