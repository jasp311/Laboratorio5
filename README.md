## Laboratorio5
Laboratorio 5 de robótica 2023-2

### Integrantes: 
- Norma Lorena Martinez Zavala
- Miguel Angel Sarmiento Cabarcas
- Jaime Andres Sanchez Peralta

## Objetivos:
• Crear todos los Joint Controllers con ROS para manipular servomotores Dynamixel AX-12 del robot Phantom X Pincher. <br>
• Manipular los tópicos de estado y comando para todos los Joint Controllers del robot Phantom X Pincher. <br>
• Manipular los servicios para todos los Joint Controllers del robot Phantom X Pincher. <br>
• Conectar el robot Phantom X Pincher con MATLAB o Python usando ROS. <br>

## Descripción de la solución planteada
El primer paso para la elaboración de este laboratorio fué tomar las medidas de los estabones del robot, para ello se mide de junta a junta, como se muestra en la siguiente imagen:
[![IMG-20231105-WA0037.jpg](https://i.postimg.cc/3wGRwpRx/IMG-20231105-WA0037.jpg)](https://postimg.cc/CZFYPRk3)

Medidas de los eslabones:
[![IMG-20231105-213426.jpg](https://i.postimg.cc/RZcyVZmz/IMG-20231105-213426.jpg)](https://postimg.cc/hXtp2ghC)

Para facilitar el hallazgo de los diferentes angulos del robot pincher se analiza el vector de la muñeca y no el vector del tool.

![image](https://github.com/jasp311/Laboratorio_5/assets/47614570/9c773d14-1cf8-4869-b974-c6a3810e9783)

Una forma de resolver este problema es aplicar la regla del paralelogramo para sumar vectores. Si dibujamos el vector tool y el vector L que representa el desplazamiento de la muñeca, podemos obtener el vector de la muñeca como la diagonal del paralelogramo formado por estos dos vectores. Así, se reconocer que el vector de la muñeca es el mismo vector tool menos un desplazamiento L en la dirección de a.

$$ P_{w} = P_{TCP} - I_{4a} $$

Para determinar el ángulo 1, se utiliza la geometría del triángulo formado por el eje del robot, el punto de contacto con el suelo y el centro de la cámara. Se aplica el teorema de Pitágoras y la ley de cosenos para obtener el valor del ángulo 1 en función de las dimensiones del robot y la distancia al objetivo.

![image](https://github.com/jasp311/Laboratorio_5/assets/47614570/25b02a56-51e3-4bae-ad36-d4b6ed4af76a)

$$ \Theta_{1} = atan_{2} (y_{w},x_{w}) $$

Se analiza la vista lateral para hallar los angulos theta2 y theta3

![image](https://github.com/jasp311/Laboratorio_5/assets/47614570/fb857897-b6a8-42c5-a6ab-ab055d124c8c)

Se halla un pw2 auxiliar para hallar estos angulos mas facilmente

$$ P_{w2} = P_{w}-L_{1}Z_{0} $$

Con este pw2 se halla el angulo Theta3 con la ley del coseno

![image](https://github.com/jasp311/Laboratorio_5/assets/47614570/5883d1f9-6073-4efe-b031-89678a3df457)

![image](https://github.com/jasp311/Laboratorio_5/assets/47614570/9522674a-280b-4500-9a01-0483ccfde006)

Se procede a calcular el ángulo theta2 a partir de la vista lateral, utilizando la ley de cosenos y los ángulos alpha y psi que se definen en la figura. Se obtiene una expresión para theta2 en función de las longitudes de los eslabones y los ángulos mencionados.


![image](https://github.com/jasp311/Laboratorio_5/assets/47614570/a31d09d6-1145-4fb1-ab3e-48f459462d09)

Ahora se obtiene Theta 2

![image](https://github.com/jasp311/Laboratorio_5/assets/47614570/07570c60-ea8d-445d-8857-e8568d9c5977)

Finalmente el angulo Theta 4


![image](https://github.com/jasp311/Laboratorio_5/assets/47614570/337c4385-825b-4323-b3c3-56132cd1fdbf)



### Desarrollo de la cinemática inversa:

Para diseñar las trayectorias, se usó Dynamixel wizard para dibujar el espacio de trabajo con las medidas correspondientes. Luego, se crearon dos círculos con esas dimensiones en Autocad, para definir los puntos de las figuras a trazar.

![image](https://github.com/jasp311/Laboratorio_5/assets/47614570/69d0457f-728d-4891-9b7d-e4a66f806489)

Los puntos se ubicaron en autocad siguiendo las trayectorias de las figuras y se extrajeron sus coordenadas. Luego se creó una lista de vectores con tres coordenadas (x,y,z) en python y se ajustó la coordenada z manualmente. Así, cuando el marcador dibujaba, z era cero y cuando había un salto, z aumentaba para evitar trazos indeseados.
   



### Codigo
Para iniciar el control de los motores del pincher, se debe modificar el archivo de configuracion de dynamixel one motor que se encuentra en el sitio web del curso, agregando los parametros correspondientes a los 5 motores que forman parte del brazo robotico. Luego, se debe escribir el codigo en python que importe las siguientes dependencias.

![image](https://github.com/misarmientoc/Robotica_lab4/assets/47614570/958b992b-c6d7-441f-a657-740494cdee87)

Para trabajar con ROS en python, se necesita importar el módulo de rospy, que proporciona las funciones y clases esenciales para interactuar con el sistema. También se utiliza el módulo de numpy, que permite realizar cálculos numéricos y operaciones matriciales de forma eficiente. Además, se requieren varios tópicos y mensajes específicos de ROS, como el tipo JointTrajectory, que representa una secuencia de posiciones y velocidades de las articulaciones.

La función Join_publisher se encarga de crear un objeto Publisher que publica en el tópico joint_trajectory, que es usado por el controlador del brazo robótico. Dentro de un bucle, mientras rospy esté activo, se envían los puntos guardados al motor. Esta función es una función residual de pruebas anteriores y no se utiliza en el código principal del programa.

![image](https://github.com/misarmientoc/Robotica_lab4/assets/47614570/ba88d020-1ab9-424c-a642-13c2025a69a1)

Este código de python usa las funciones callback y listener para trabajar con los datos de los servomotores del robot. El listener inicializa el nodo de ROS y se suscribe al tópico de los estados de las articulaciones. El callback guarda una variable global con el ángulo de cada articulación en grados, usando una conversión simple de radianes a grados 180/pi.

![image](https://github.com/misarmientoc/Robotica_lab4/assets/47614570/3d024ba0-7000-4a51-9198-30e1567ef9ab)

La función jointCommand es una función que envía un comando de dynamixel al motor especificado, usando el servicio de transmitir al motor. El comando de dynamixel tiene cuatro parámetros: el número de comando, el identificador del motor, el nombre de la dirección de memoria y el valor nuevo. La función espera a que el servicio esté disponible y luego envía el comando. Después, espera un tiempo para ROS y devuelve el resultado del comando.

![image](https://github.com/misarmientoc/Robotica_lab4/assets/47614570/33878b69-5e9c-4a71-95d5-8dc469c4f5b9)

Cuando se ejecuta este archivo desde la terminal, se inicia el nodo de ROS y se suscribe al topic de los motores para recibir sus datos. Estos datos se muestran en la terminal junto con el nombre del grupo. A continuación, se crean listas con las posiciones posibles para enviar a los motores, tanto en bits como en grados. Se hace una lista de listas con estas posiciones y se le presentan al usuario en formato de grados.

![image](https://github.com/misarmientoc/Robotica_lab4/assets/47614570/9c6f4692-7118-4b62-ac13-b16f3b355628)

El programa inicia un bucle while que se ejecuta indefinidamente, solicitando al usuario que ingrese un número de posición para controlar el pincher. Mediante un bucle for, se envían los comandos correspondientes a cada motor usando la función join_command, junto con un límite de torque por seguridad. La posición real de cada motor se muestra en la consola, junto con el error respecto a la posición deseada.

![image](https://github.com/misarmientoc/Robotica_lab4/assets/47614570/d73783f0-8dd3-49e4-95fb-688f516ae5c5)

## MATLAB

Partiendo del codigo ejemplo sync_write.m se genera un archivo que permite controlar 4  motores al tiempo y se genera un switch case para seleccionar cada una de las posiciones y para cerrar el ciclo  y la comunicacion.

![image](https://github.com/misarmientoc/Robotica_lab4/assets/47614570/6fda32ef-cdd8-42ed-a904-42a767216e08)



## Desarrollo

A continuación se muestra el pantallazo donde el robot alcanza cada una de las posiciones que son seleccionadas por el usuario:

![image](https://github.com/misarmientoc/Robotica_lab4/assets/47614570/417cc620-1d4d-4786-a331-3a7a1c268bd7)

En el siguiente video se observa el movimiento del robot

https://youtu.be/kAmjaRHJvHw?si=97m7ebpFJ07SqSqx

## Conclusiones

-El programa creado logra establecer una comunicación humano-máquina mediante la terminal, aunque la interfaz podría ser más intuitiva en próximos laboratorios. Se tiene como objetivo diseñar una interfaz más atractiva en futuras operaciones.
