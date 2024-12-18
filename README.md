# Blog asignatura Robótica Móvil
Este es el blog que usaré para la asignatura **Robótica Móvil**. Es este blog, iré subiendo todas las prácticas y el resultado final de cada una de ellas.

## Práctica 4 Global Navigation

## Introducción

El objetivo de la cuarta práctica es realizar un algoritmo de "Gradient Path Planning" para que un taxi sea capaz de navegar hasta un target que nosotros queramos. Para ello, se han tenido que implementar principalmente tres cosas: un algoritmo Wave Front para poder así sacar un mapa de costes, el path hasta el target y la navegación.

## Wave Front Algorithm

Antes de poder hacer el mapa de costes hay que tener en cuenta varios detalles:
- Las coordenadas en el mundo no son las mismas que las del grid, por ejemplo, para el mundo el taxi inicialmente está en el (200,200) pero para el grid inicialmente está en (0,0).
- En versiones anteriorres del docker, el mapa tenía pixeles grises que eran necesarios quitar, pero que actualmente no existen.
- Los vecinos seleccionados para cada celda son los 8 vecinos inmediatos de cada una, dentro de los límites del mapa.

Una vez se tiene esto en cuenta, para poder calcular el mapa de costes, hay que ir siguiendo la cosa de vecinos, que cada vez va en aumento, mientras que e van sumando costes, desde el target (con coste inicial 0) hasta el taxi.

A contnuación, un vidoe de cómo se va realizando el mapa de costes:

https://github.com/user-attachments/assets/700a7e3e-45c9-4cf9-b1e4-5248c45ba5f4

![8_vecinos](https://github.com/user-attachments/assets/1b0f6090-c7d2-41ed-8cd3-ab1524ab9972)


## Expansión de obstáclos

La expansión de obstáculos no termina de funcionar del todo bien en mi algoritmo. Lo que haga para poder expandirlo es buscar los vecinos de las celdas obstáculos ( que ya eran vecinos de otra celda) y sumarles un coste muy alto. Pero haciendo esto, el mapa final termina con puntos blancos cerca de los obstáculos y por eso funciona parcialmente, pero está previsto mejorar esta parte del código.

Posible arreglo: crear un primer mapa de costes, y en base a ese recorrer sólo las celdas obstáculos y poner los costes de sus vecinos muy altos o como si fuesen también obstáculos.

## Path Planning

Una vez hecho el mapa de costes, el path lo conseguiremos usando waypoints de la siguiente forma:
1. Comenzar desde el taxi
2. Buscar los vecinos de la celda actual
3. Dentro de esos vecinos, solo los que son **alcanzables**, comprobamos el de menor coste.
4. Guardamos en el path el vecino de menor coste y volvemos al paso 2 hasta alcanzar el target.

una vez realizado esto, podemos obtener el path y dibujarlo en la imagen:

![Screenshot from 2024-11-24 17-10-38](https://github.com/user-attachments/assets/f6cc2c78-715c-4557-927d-2968593d63c6)


## Navigation

Teniendo ya el path realizado y en coordenadas del mundo, podemos empezar a navegar. Para ellos, vamos a ir recorriendo todos los waypoints del path hasta llegar al objetivo. Al igual que en la práctica 3 (que también había waypoints) tenemos que darle valores a la velocidad linea y angular. En un inicio intenté poner ambas al mismo tiempo, calculando el angulo que difiere la orientación actual del taxi con la del siguiente waypoint, pero no fue muy buena idea:

https://github.com/user-attachments/assets/dc10de6f-373a-4a44-884f-a053a4871469

la solución fue separar las velocidades, es decir, primero orientar el taxi hacia el waypoint sin moverme linealmente, y cuando esté (dentro de un umbral) orientado, avanzar recto hacia el waypoint.

Algo a tener en cuenta son los umbrales que hay que usar, al igual que se hizo en la práctica 3, es muy difícil quedar justo en una posición, por ello siempre hay que dejar un umbral para poder pasar al siguiente waypoint, en mi caso, lo mido en función de la distancia en cada eje.

Ejemplo de no poner un umbral:

https://github.com/user-attachments/assets/869c476a-75ac-4268-93d1-5a08ae4e619e



## Dificultades

He tenido dos dificultades principales, la expansión de costes (que no funciona muy bien) y la navegación.

Como ya he explicado, la expansión de obstáculos solo genera pequeños puntos de coste máximo cerca de las paredes, suficientes para que el coche no se choque. 

En la navegación, la principal dificultad fue llegar a la solución de separar primero la orientación y después avanzar.


## vídeo final

https://github.com/user-attachments/assets/cf8c8568-fe0f-4a69-9bab-ad56fa26a0de

Si quieres ver el video entero a la velocidad corresta pincha [aqui](https://youtu.be/npI1mvtc9Jg)


## Práctica 3 Obstacle detected

## Introducción

La tercera práctica consiste en elaborar un algoritmo **Virtual Force Field (VFF)** que sea capaz de recorrer el circuito entero esquivando los obstáculos y sin chocarse con las paredes. Para ello, deberá generar un vector de fuerza atractiva, otro de fuerza repulsiva y otro de fuerza resultante.

## Fuerza atractiva
Para generar el vector de la fuerza atractiva, basta con coger la posición del target, pero hay que tener en cunenta que hay que pasar dichas coordenadas a la posición relativa, es decir, respecto al robot. Es importante tener en cuenta que el target es muy dificil acercarse de forma exacta, por ello, hay que dejar un distáncia al target.

A continuación, un vídeo donde aparecen las diferentes pruebas para el cálculo de la fuerza atractiva. Al principio, cuando sólo tenía la fuerza atractiva, esta la normalicé para que al grficar el vector quede dentro del visor, pero finalmente, no la normalizo. 



https://github.com/user-attachments/assets/acddd5e8-7a75-4b79-a92d-b5a38e56c346



## Fuerza repulsiva

Para calcular la fuerza repulsiva hay que tener en cuenta varias cosas. El vector fuerza repulsiva tiene que ser la suma de todos los objetos detectados. Ademñas, dependiendo de la distancia a la que se encuentre el objeto tienen mayor influencia que uno que está lejos. También hay que tener en cuenta, que hay que cambiarle el signo dado que el vector que se calcula es hacia el objeto, y necesito el contrario.

## Fuerza resultante

La fuerza resultante es la suma de la fuerza repulsiva y la fuerza atractiva parametrizadas, es decir, 

![image](https://github.com/user-attachments/assets/655f5b9d-b02a-4139-a07e-120b41629d31)

En este caso, los parámetros finales para completar una vuelta son:

**Vuelta 1**
- Ka = 0.43
- Kr = 0.09

**Vuelta 2**
- Ka = 0.4
- Kr = 0.11

## Mínimos locales

Uno de los principales problemas que he tenido a la hora de calcular las fuerzas es la aparición de mínimos locales. Esto se daba debido a que normalizaba los vectores, facilitando la aparición de estos mínimos. Para solucionarlo hice dos cosas, no normalizar las fuerzas y establecer siempre una velocidad mínima para que el coche esté constantemente en movimiento.


[p3_movil_minimo_local.webm](https://github.com/user-attachments/assets/ed6bcae4-aabb-4fdd-bdc5-e968106c73b2)



## Dificultades

Además de la dificultad del mínimo local he tenido otro gran problema con la fuerza repulsiva, ya que no terminaba con dar la forma de que el vector final sea el conjunto de todos los obstáculos. 

## Vídeo

Vídeo final con pruebas y dos vueltas completas.

https://youtu.be/-vslRJUO0D0


## Práctica 2 Follow Line

## Introducción
La segunda práctica consiste en hacer un sigue líneas con un controlador PID, o PD en mi caso, para que de forma reactiva pueda completar una vuelta en varios circuitos. La implementación de esta práctica se puede dividir en dos partes principales: filtro de color y controlador.

## Filtro de color
Como el propio nombre de la práctica explica, hay que seguir una línea dibujada en el suelo del circuito. La línea es de color rojo, por lo que para la implementación del filtro del color hay que tener en cuenta varias consideraciones. En un principio usé el [ejemplo3](https://github.com/urjc-docencia-robotica-movil/p2-follow-line-24-25-rsanchez2021/blob/main/ejemplos/image_red_filter.py) que nos daban ya hecho para el filtro de color, pero el filtro que nos daban era en RGB y lo teníamos que hacer en HSV. Para poder realizarlo en HSV hay que tener en cuenta que el color rojo puede tener valores de H separados, es decir, de 0 a 10 y de 175 a 179 aproximadamente, que se encuentran es extremos opuestos del espectro HSV.

![image](https://github.com/user-attachments/assets/d6971bda-4475-4992-a938-2069973713b9)

Con ayuda del [ejemplo2](https://github.com/urjc-docencia-robotica-movil/p2-follow-line-24-25-rsanchez2021/blob/main/ejemplos/image_iteration.py) y modificándolo para que los valores estén en HSV, se puede detectar que el color exacto de la línea roja es:

![Screenshot from 2024-10-11 10-25-30](https://github.com/user-attachments/assets/386c8ba8-bf55-44fe-a03d-f373ead33934)

Para el filtro de color, podemos usar el siguiente rango simplificado:

```python
lower_red = np.array([0,200,200])
upper_red = np.array([10,210,214])
```

Una vez tenemos el filtro de color realizado, podemos calcular su centroide para posteriormente calcular el error. Para ello, se han usado los momentos, utilizando la siguiente ecuación:

![image](https://github.com/user-attachments/assets/3e0a844b-fbe7-4cab-9f8b-9b94cb1b1d5b)

Posteriormente se dibuja el círculo y el filtro quedaría de la siguiente forma:

![image](https://github.com/user-attachments/assets/2157948f-62f9-4c96-a90e-f7b58cb6d3d7)

## Controlador
Una vez detectada la línea y su centroide, es necesario implementar un controlador **PID** que permita al coche ajustar su dirección. En mi caso, se ha usado un controlador **PD** , sin la parte integral, ya que usando simplemente la parte Proporcional para corregir el error actual y la parte derivativa para suavizar el movimiento es suficiente.

Para calcular el error, se ha establecido que el valor central esperado es el centro de la imagen (320) en el eje X, , ya que el eje Y no influye en el cálculo del error direccional.

Tras muchos intentos de prueba y error, los valores de las ganancias usadas son:

Circuito simple:
- Kp = 0.005
- Kd = 0.000009

Circuito Ackermann:
- Kp = 0.0042
- Kd = 0.0000071

## Dificultades
La mayor dificultad sin ninguna duda ha sido encontrar los valores de las ganancias del controlador PD ya que ha sido mediante prueba y error y al empezar la práctica tarde estas pruebas las he tenido que hacer los últimos días y la plataforma ha dado varios errores.

## Vídeos

Vídeo controlador **P** a baja velocidad inestable:

[p2_movil_controladorP_sobreoscilado_y_lento.webm](https://github.com/user-attachments/assets/a607a05d-c7af-4482-87c2-9811d09fd712)

Vídeo circuito Ackermann: tiene mucha más oscilación pero los giros son más controlados

https://github.com/user-attachments/assets/b4f284de-4a4f-409b-b3a9-900bfd75aff3

Vídeo circuito simple:
Para poder ver el vídeo pincha en el enlace.

[Vídeo final](https://youtube.com/shorts/F-GUy2nogWc?feature=share)

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

