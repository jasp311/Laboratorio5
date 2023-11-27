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

$$ P^{2}_{W2} $$




### Desarrollo de la cinemática directa:
  Se hizo uso de SerialLink para la construcción del robot a partir de los parámetros DH estandar, también se calculó la matriz de transformación homogenea que relaciona el sistema de coordenadas de World con el sistema de coordendas del TCP (H0TCP).

El diagrama que describe el robot pincher es el siguiente

![image](https://github.com/misarmientoc/Robotica_lab4/assets/47614570/6162ab67-abc2-4dc4-8b96-21609a8b636e)

Los marcos de referencia para describir el robot a través de los parámetros DH con base en el diagrama del robot pincher y las distancias de las articulaciones ya medidas son:

![image](https://github.com/misarmientoc/Robotica_lab4/assets/47614570/c49ea090-709e-4f3c-8de1-5d870d302a63)

   
PhantomII = 
 
PhantomII:: 4 axis, RRRR, stdDH, slowRNE    

![image](https://github.com/misarmientoc/Robotica_lab4/assets/66492359/843327aa-e4e1-46a6-a2d1-cabc713166d0)

tool:    t = (0, 0, 0.065), RPY/xyz = (0, 0, 0) deg              
H0TCP = 4×4    
         0         0    1.0000    0.1700
         0   -1.0000         0         0
    1.0000         0         0    0.1850
         0         0         0    1.0000
![image](https://github.com/misarmientoc/Robotica_lab4/assets/66492359/ff1c7bb9-377d-452d-9310-5b32cccaa4ff)

q1 = 1×4    
    0.4363    0.4363    0.3491   -0.3491

![image](https://github.com/misarmientoc/Robotica_lab4/assets/66492359/e0d103f8-926c-4bb7-a70d-9254cd78d199)

q2 = 1×4    
   -0.6109    0.6109   -0.5236    0.5236

![image](https://github.com/misarmientoc/Robotica_lab4/assets/66492359/237de157-f726-470d-93ea-dac8150d4ea1)

q3 = 1×4    
    1.4835   -0.3491    0.9599    0.4363

![image](https://github.com/misarmientoc/Robotica_lab4/assets/66492359/680b1cc3-031e-4e9c-a35e-d972ffa991c5)

q4 = 1×4    
    1.3963   -0.6109    0.9599   -0.7854

![image](https://github.com/misarmientoc/Robotica_lab4/assets/66492359/eaf9adae-992d-462c-97e7-76bd1df93aa4)

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
