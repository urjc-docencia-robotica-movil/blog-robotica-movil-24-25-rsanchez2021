# Blog asignatura Robótica Móvil
Este es el blog que usaré para la asignatura **Robótica Móvil**. Es este blog, iré subiendo todas las prácticas y el resultado final de cada una de ellas.

## Cambios enero
- P2 añadido apartado Modificaciones y mejoras
- P4 modificado apartado expansión de obstáculos
- P4 añadido apartado de Modificación y mejoras

# Práctica 2 Follow Line (Junio)

## Introducción
La segunda práctica consiste en hacer un sigue líneas con un controlador PID, o PD en mi caso, para que de forma reactiva pueda completar una vuelta en varios circuitos. La implementación de esta práctica se puede dividir en dos partes principales: filtro de color y controlador.

## Filtro de color
Para poder conseguir el centroide de la línea roja he realizado varios cambios. En la primera entrega hacía un filtro de color rojo y luego el centroide, pero quedaba muy cerca del coche y no era efectivo. La modificación consiste en, desde una línea a 250 píxeles, calcular el punto medio de aquellos píexeles que sean rojos. De esta forma, se puede colocar la línea base en la altura más óptima.

Otra modificación que he tenído que haceer es aumentar el rango de color rojo, ya que, después de alguna actualización de docker, el color rojo no era el mismo. Para ello he tenido que realizar dos máscaras para los rangos de H separados del color rojo.

```
lower_red = np.array([0, 100, 100])
upper_red = np.array([10, 255, 255])

lower_red2 = np.array([160, 100, 100])
upper_red2 = np.array([180, 255, 255])

```
Como última modificación, en caso de perder la línea, el centroide se dibuja de color rojo, sino de color azul. De esta forma pude hacer prueba y error para la altura de la línea. 

## Controlador

Para poder calcular las constantes Kp y Kd seguí el siguiente razonamiento

1. Calcular un Kp funcinal a velocidad 10
Empecé con un Kd = 0 hasta encontrar un Kd que sea capaz de dar una vuelta. Fui probando desde Kp=0.003 aumentando de poco en poco ya que los giros no eran suficientes, hasta que con un Kp = 0.01 era capaz de dar una vuelta entera.

v10 kp 0.003

https://github.com/user-attachments/assets/6feaf922-7730-404c-a9a3-6e908429ff5c


v10 kp 0.006

https://github.com/user-attachments/assets/c314b7f6-2721-4b51-9667-554df2284b77


v10 kp 0.007

https://github.com/user-attachments/assets/73ee0579-642a-4ee3-8cc9-ca93e12c9949


v10 kp 0.008

https://github.com/user-attachments/assets/c6809b4b-663b-4db7-baea-bf2db458921f

v10 kp 0.009

https://github.com/user-attachments/assets/8757285f-5e7e-4fb0-a8de-f6ed3f69211c


v10 kp 0.01

https://github.com/user-attachments/assets/669f7aab-2b21-4c85-bcb3-286fe62cca10


Una vez dió la vuelta, aumenté la velocidad para encontrar la máxima velocidad a la que era estable.

v11 kp 0.01

https://github.com/user-attachments/assets/eadedc25-a448-421e-b77c-56ad42495fb3


v12 kp 0.01



https://github.com/user-attachments/assets/a4ad76fa-c099-43ea-ba59-70535114fbce



v15 kp 0.01



https://github.com/user-attachments/assets/9c250d16-ea50-45f8-b22f-fc008ad22f22

El resultado fue que la velocidad máxima era 12.

2. Calcular un Kd estable a velocidad 12
Empecé con un Kd bastante pequeño, de 0.000001. El problema era claramente visible, en las curvas muy cerradas se separaba mucho de la línea central. Seguí aumentando la Kd en 0.001, mejoraba en las curvas cerradas, pero no era suficiente. Finalmente, con un kd de 0.001 no se separaba tanto de la línea y seguía siendo estable, ya que si lo aumentaba más, empezaba a oscilar en las rectas o curvas poco cerradas.

v12 kp 0.01 kd 0.000001

https://github.com/user-attachments/assets/bc3564b4-1af1-4169-b96a-dcdda7e4deef


v12 kp 0.01 kd 0.0001


https://github.com/user-attachments/assets/8381f56e-fb34-4ba9-9a81-8d4884fed42b


v12 kp 0.01 kp 0.001

https://github.com/user-attachments/assets/cfda952e-ab40-46ed-a55f-ef7a8b934171

3. Ajustar Kp y Kd
Para reducir de forma significativa el error en las curvas cerradas, aumenté la Kp de nuevo. Con un Kp = 0.02 y Kd = 0.0015 el coche reaccionaba mejor en las curvas cerradas pero oscilaba mucho en las rectas. Si bajaba la Kp a 0.0017 el coche no oscilaba tanto, pero era más lento en las curvas. Finalmente, con un Kp = 0.0018-0.0019 y Kd = 0.0015 el coche reacciona mejor en las curvas cerradas sin oscilar.

V12 kp 0.02 kd 0.0015


https://github.com/user-attachments/assets/ee14467a-a820-464d-adde-935ba76cbb2f


V12 kp 0.017 kd 0.0015


https://github.com/user-attachments/assets/cfc255a9-72b8-41d6-be04-1daca4c0a502




V12 kp 0.018-0.019 kd 0.0015



https://github.com/user-attachments/assets/2d377eeb-249b-4c63-889c-174201d39685



Como última prueba, quería aumentar la velocidad a 15. haciendo un par de ajustes en la parte proporcional, el coche es capaz de dar la vuelta con Kp = 0.021 y kd = 0.0015 pero el aumento de la veocidad hace perder estabilidad.

V12 kp 0.019 kd 0.0015 


https://github.com/user-attachments/assets/a4a22350-691b-4c78-a0e2-7c6ee878826c


V12 kp 0.021 kd 0.0015


https://github.com/user-attachments/assets/a929b8b4-2d7a-4985-bd22-f530e033e7fa


## Resumen cambios
- Aumento rango H
- Color centroide
- Altura línea base
- Valores Kp y Kd

## Dificultades

Para hacer las modificaciones he tenido dos problemas. El primero fue el desconocimiento de cambio de características de la práctica, como puede ser el color rojo de la línea, pero era fácil de solucionar. El segundo prblema fue que durante la prueba-error para calcular el PID el simulador se desconectaba constantemente y tenía que parar y reiniciar el docker para cada prueba. Esta dificultad se dió especialmente al inicio del cálculo de los valores para el PID, ya que iba robando de poco en poco, pero una vez dí con los valores capaces de dar vueltas, no necesitaba reiniciar.


## Vídeos finales

Sigue líneas óptimo:
- Velocidad 12
- KP = 0.019
- KD = 0.0015

Sigue líneas rápido
- Velocidad 15
- KP = 0.021
- KD = 0.0015


## Práctica 5 Monte Carlo Laser Localization
El objetivo de la práctica es crear un algoritmo de localización para una aspiradores basado en el algoritmo de Monte Carlo usando un láser instalado en la aspiradora y el mapa. PAra explicar la implementación, voy a dividir la práctica en: inicialización de las partículas, propagación de las partículas, láser virtual, cálculo de los pesos, remuestreo de las partículas y funciones extras.

## Inicialización de las partículas
La primera idea que se me vino a la cabeza fue generar las partículas de forma aleatoria y luego comprobar si se encintraban dentro del mapa o no, y en caso de no estarlo generarla otra vez. Pero había varios problemas, había partículas que tenía que regenerarla varias veces y era muy poco óptimo. Por ello, la idea final fue primero coger las celdas libres (valor= 255) y luego a partir de ellas generar las partículas. De esta forma, sólo tenía que generarlas una vez. Además, se generaba un ángulo aleatorio entre -pi y pi y se asignaba a cada partícula. 

Ejemplos con 50, 100 y 300 partículas:

![movil_p5_50_particles](https://github.com/user-attachments/assets/8b072b18-6dbd-4f47-8e52-f349aeaf162b)

![movil_p5_100_particles](https://github.com/user-attachments/assets/8b5f3552-0a1d-4c18-a800-5a9de3683ed2)

![movil_p5_300_particles](https://github.com/user-attachments/assets/bed84898-ff1b-4e0b-9f13-cc58c4be11ad)



## Propagación de las partículas
Siguiendo el ejemplo [particle_propagation_example.py]() se ha calculado el tiempo que tarda entre iteraciones y con ello el movimiento del robot. Una vez calculado esto, se le ha sumado a todas las partículas, tanto X Y como YAW. Esto se puede hacer ya que el movimiento de mi aspiradora es contante tando lineal como angular, y no es necesario calcularlo de cada partícula, sino en global. 

Ejemplo con 5 partículas:

[movil_p5_mov_particulas.webm](https://github.com/user-attachments/assets/7109c183-a5c0-4a56-95cf-8eac11010396)

Ejemplo con 50 partículas:

[p5_movil_50_particulas.webm](https://github.com/user-attachments/assets/235d0e6e-e608-41d3-9143-d2141529d104)



## Láser virtual
Para calcular el láser vistual, me he basado en el ejemplo [raytracing_example.py]() pero he aumentado el número de rayos ya que sino no convergía en la posición adecuada. Comentar que el láser virtual sólo se calcula en las partículas que están dentro del mapa, sino se ponen los valores máximos. Como mejora se puede hacer también que si una partícula se encuentra en un obstáculo también se pongan los valores máximos de láser teórico.

## Cálculo de los pesos
El cálculo de los pesos depende de la similitud que tenga con el láser real. Se puede dividir en dos la asignación de pesos: peso nulo o peso no nulo. El peso nulo se le asigna a aquellas partículas que no se encuentren en el mapa o que sean un obstáculo, además, se ha tenidoq ue añadir que si hay un mal cálculo del láser teórico el peso sea de 0 ya que sino saltaban errores. Para los pesos no nulos, se calcula la diferencia entre el laser teórico y el laser real y siguiendo una función gaussiana se les establece unos pesos. Además, se normalizan todos los pesos para que el remuestreo sea más fácil.

## Remuestreo de las partículas
El remuestreo se basa en elegir partículas random pero cuanto mayor peso tengan mayor probabilidad de que salgan tienen. Como hay particulas repetidas en el remuestreo, se ha añadido ruido ya que sino coincidian unas encimas de otras. De esta forma, siempre se crea un pequeño área.

Ejemplo de un mal remuestreo

![movil_p5_muchas_par_fuera](https://github.com/user-attachments/assets/6a8563fa-f3b8-44ca-8ed4-3063b10a1188)

Ejemplos sin ruido:

[p5_movil_converge_despacio_mal.webm](https://github.com/user-attachments/assets/e081bc6f-3ccb-46c3-9cd1-d8972788e82d)


[p5_movil_converge_mal_rapido.webm](https://github.com/user-attachments/assets/ec1d4492-128b-4252-8cdf-cbca726cd8a4)


[p5_movil_converge_mal_rapido_2.webm](https://github.com/user-attachments/assets/8ad4512b-2c49-4d4b-b4a3-e7da00366da4)


[p5_movil_converge_mal_rapido_300.webm](https://github.com/user-attachments/assets/dc0559f5-cdff-45cd-8757-e2a2a604e138)

Ejemplos con ruido pero poco preciso:

[p5_movil_convergen_mal.webm](https://github.com/user-attachments/assets/55912305-d140-411c-8587-26dbb130548f)

[p5_movil_no_mejora_mucho_pero_algo.webm](https://github.com/user-attachments/assets/2dcf2e7e-5c9b-4cec-8b07-8b4049892884)



## Funciones extras
Algunas funciones también implementadas en el código son: get:map() para poder tener el mapa útil; parse_laser_data() para el láser real del robot; is_within_map_bound() para comprobar que las partículas estñan dentro del mapa; compute_estimated_pose() para calcular la media de las partículas y poder proyectar el robot en la posición calculada. 

## Valores optimizados
Finalmente, los mejores valores son:
- N_PARTICLES = 300 (se ha probado también con 500)
- VEL_V = 0.2 y VEL_W = 0.2 (si se aumenta mucho las velocidades no funciona)
- MAX_LASER_DISTANCE = 10 (máximo 20)
- LASER_NUM_BEAMS = 16 (mínimo 8)
- RAYTRACING_SKIP_STEPS = 2 (mínimo 1)

## Dificultades
Durante esta práctica he tenido dos grandes dificultades. Una de ellas es la que suelo tener en la mayoría de las prácticas que es el paso de coordenadas del mapa al mundo, pero tras un par de ejemplos lo pude comprender. Otra dificultad fue también llegar a ciertos valores para que el algoritmo funcionase. No hay muchas dificultades ya que estaba todo bien organizado con ejemplos y

## Vídeos



[p5_moil_bueno_300_16_2.webm](https://github.com/user-attachments/assets/b62ce440-96da-418b-90c3-2fed4549a4d2)



[video bueno 2](https://github.com/user-attachments/assets/f5f5cb5c-9814-4c67-8190-c1111c2917ae)





[video bueno 3](https://github.com/user-attachments/assets/7b071af7-dbfa-4bf6-86a1-7e0d33559545)





## Práctica 4 Global Navigation

## Introducción

El objetivo de la cuarta práctica es realizar un algoritmo de "Gradient Path Planning" para que un taxi sea capaz de navegar hasta un target que nosotros queramos. Para ello, se han tenido que implementar principalmente tres cosas: un algoritmo Wave Front para poder así sacar un mapa de costes, el path hasta el target y la navegación.

## Wave Front Algorithm

Antes de poder hacer el mapa de costes hay que tener en cuenta varios detalles:
- Las coordenadas en el mundo no son las mismas que las del grid, por ejemplo, para el mundo el taxi inicialmente está en el (200,200) pero para el grid inicialmente está en (0,0).
- En versiones anteriorres del docker, el mapa tenía pixeles grises que eran necesarios quitar, pero que actualmente no existen.
- Los vecinos seleccionados para cada celda son los 8 vecinos inmediatos de cada una, dentro de los límites del mapa.

Una vez se tiene esto en cuenta, para poder calcular el mapa de costes, hay que ir siguiendo la cola de vecinos, que cada vez va en aumento, mientras que se van sumando costes, desde el target (con coste inicial 0) hasta el taxi.

A contnuación, un vidoe de cómo se va realizando el mapa de costes:

https://github.com/user-attachments/assets/700a7e3e-45c9-4cf9-b1e4-5248c45ba5f4

![8_vecinos](https://github.com/user-attachments/assets/1b0f6090-c7d2-41ed-8cd3-ab1524ab9972)


## Expansión de obstáclos (Modificado)

La expansión de obstáculos la realizo posteriormente al mapa de costes inicial. Para ello, compruebo los vecinos más cercanos a los obstáculos y les pongo un peso fijo de 255. En un principio, esto lo hacía seguido a la hora de calcular el mapa de costes, pero había saltos muy bruscos y el mapa no quedaba bien:


![Screenshot from 2025-01-06 20-35-38](https://github.com/user-attachments/assets/f0fa7503-1959-4fa3-99e3-a76d22339424)


Finalmente, haciendo la expansión de obstáculos posteriormente el mapa quedaría:
Con 8 vecinos:

![Screenshot from 2025-01-06 20-54-21](https://github.com/user-attachments/assets/64524b3c-9143-46ba-b2a4-82d0040c5959)


[p4_mapa_bien.webm](https://github.com/user-attachments/assets/d0bca10b-5486-4dff-ba2f-bcb32c82d230)


Con más vecinos:

![Screenshot from 2025-01-06 20-29-18](https://github.com/user-attachments/assets/3f887610-823f-4301-88eb-eaedb0678944)



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


## Vídeo final

https://github.com/user-attachments/assets/cf8c8568-fe0f-4a69-9bab-ad56fa26a0de

Si quieres ver el video entero a la velocidad corresta pincha [aqui](https://youtu.be/npI1mvtc9Jg)

## Modificaciones y mejoras

Además del cambio de la expansión de obstáculos (ver apartado) también he quitado el path planning e implementado un descenso por gradiente. Para ello, primero buscaba dentro de una vecindad de gran tamaño la celdilla con el menor coste posible, después, esa celdilla se pasaba la posición al mundo y se navegaba hasta él. En caso de que no queden celdillas sin visitar, salta una excepción, evitando así posibles errores. Una de las mejoras más considerables es que al mover el robot va más fluido ya que he aumentado los umbrales, que junto con la vecindad mayor, mejora considerablemente la navegación. 

Una de las dificultades que he tenido ha sido el paso de path planning a gradiente, ya que no conseguía hacer que el taxi avanzase y se quedaba siempre dando vueltas en un mismo sitio:


[p4_error_navegacion.webm](https://github.com/user-attachments/assets/b71c73a9-1106-4fd8-9ffd-db41d5d64c1e)


[p4_error_navegar.webm](https://github.com/user-attachments/assets/9edf3e80-1719-4b7c-b418-51d59d365a86)

Vídeos finales:

Enlace vídeo primer ejemplo: https://youtu.be/BUudMGBW3sM

Enlace vídeo segundo ejemplo: https://youtu.be/a_miCsGpFIQ


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

## Modificaciones y mejoras

Respecto al filtro de color, se ha dejado igual los valores. Pero, en vez de calcular el centroide, usando el ejemplo **image_red_filter.py** he sacado una línea en una posición exacta, extraido los píxeles de color rojo de la línea y calculado el centro, consiguiendo así que no pierda la línea en ningún momento y teniendo una "visión" más profunda.

![p2_movil_linea_centro](https://github.com/user-attachments/assets/f785058d-12ec-4256-880f-ec61f3613044)

En caso del PID, finalmente es sólo un PD. He arreglado el problema con la actualización del error anterior y cambiado la diferencia de tiempo fija por la real. 

Los valore utilizados son:
- Kp = 0.0049
- Kd = 0.000001

Dificultades al mejorar la práctica no he tenido muchos, lo más complicado ha sido buscar los valores de las ganancias y velocidades para que el coche pueda recorrer el circuito varias veces sin chocarse y evitando que oscile.

Vídeos de prueba:

https://github.com/user-attachments/assets/34655898-3d6c-44b0-b64f-af8582f491f2


Videos finales:

Enlace al vídeo de 1 vuelta: https://youtu.be/ng2vDjb-cxo

Enlace al vídeo con varias vueltas: https://youtu.be/suDZou1zNHU

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

