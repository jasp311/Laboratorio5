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


   


### Codigo

## MATLAB

Partiendo del codigo ejemplo sync_write.m se genera un archivo que permite controlar 4  motores al tiempo y se genera un switch case para seleccionar cada una de las posiciones y para cerrar el ciclo  y la comunicacion.

![image](https://github.com/jasp311/Laboratorio_5/assets/47614570/c480ffdf-c4a4-4537-9d21-1f0abd7e185f)

El codigo se anexa 


## Desarrollo

Para medir los dibujos del robot, se puede usar una regla de proporción entre la longitud real y la longitud en pixeles. Si la tabla mide 50 cm, y la imagen tiene 273750 pixeles de largo, entonces cada 10 pixeles equivalen a 1.826 mm. Usando el teorema de Pitágoras y el programa Paint, se puede calcular la distancia en pixeles entre dos puntos de la imagen, y luego convertirla a milímetros usando la regla de proporción.

![image](https://github.com/jasp311/Laboratorio_5/assets/47614570/3f2b81b9-9e1b-49ce-b8fd-b59d42e947c2)


En los siguientes videos se observa el movimiento del robot

https://youtu.be/SBnDFwNGcdg

https://youtu.be/VgBCShoadL4

## Conclusiones
