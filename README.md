# Interacción Humano-Robot (Máster en Robótica e Inteligencia Artificial)

## Laboratorio 02 (semana 13/05/2024)

## Objetivos

- Comprender la estructura y desarrollo de paquetes en ROS 2.
- Familiarizarse con la interpretación y procesamiento de datos del sensor láser.
- Familiarizarse con el uso de las [TFs](https://docs.ros.org/en/humble/Tutorials/Intermediate/Tf2/Introduction-To-Tf2.html).
- Integrar sistemas existentes en proyectos de ROS 2.
- Implementar la detección de personas utilizando el sensor láser y el paquete PeTra.
- Desarrollar un nodo en ROS 2 para el seguimiento de personas y navegación social.

## Requisitos

- [ROS 2 Humble](https://docs.ros.org/en/humble/Installation.html)
- [Nav2](https://navigation.ros.org/)
- [Simulador RB1](https://github.com/igonzf/ros2_rb1)
- [TensorFlow v2.16.1](https://www.tensorflow.org/install?hl=es-419)
- [PeTra (People Tracking)](https://github.com/ClaudiaAlvarezAparicio/petra/tree/ros2)

## Enunciado

Desarrolla un paquete en ROS 2 que permita detectar y seguir a una persona utilizando el sensor láser para poder llevarle el equipaje a la persona hasta el lugar de destino en la prueba 'Carry My Luggage' de la [RoboCup](https://github.com/RoboCupAtHome/RuleBook).

## Ejercicio 1: Creación de un paquete ROS 2

Desarrolla un paquete ROS 2 en Python denominado `follow_ros` para seguir a la persona que se encuentre delante del robot y sea detectada por el láser. Este paquete contendrá un nodo encargado de obtener las detecciones de la persona y navegar hasta ella manteniendo una distancia segura.

1. [**Creación del paquete**](https://docs.ros.org/en/humble/How-To-Guides/Developing-a-ROS-2-Package.html):
   - Crea un nuevo paquete ROS 2 en Python llamado `follow_ros`.
2. **Desarrollo del nodo de seguimiento**:
   - En el directorio `follow_ros/follow_ros`, crea un nodo llamado `follow_person_node.py` que llevará a cabo la función de seguir a la persona.
3. **Configuración del paquete**:
   - Añade el nodo recién creado al archivo `setup.py` como un entry point para poder ejecutarlo.

## Ejercicio 2: Integración de PeTra (People Tracking)

Integra el paquete PeTra en el workspace de ROS 2 y comprende su funcionamiento. PeTra es un paquete encargado de detectar personas utilizando el sensor láser, específicamente reconociendo las piernas de las personas.

1. **Integración de PeTra**:
   - Clona el repositorio de PeTra (la rama ros2) en tu workspace de ROS 2.
   - Asegúrate de comprender cómo se publican los datos de detección de personas por parte de PeTra.

## Ejercicio 3: Desarrollo del nodo `follow_person_node`

Desarrolla un nodo en ROS 2 llamado `follow_node` dentro del paquete `follow_ros` que utilice los datos proporcionados por PeTra para seguir a una persona a una distancia segura utilizando Nav2.

Los pasos a seguir para llevarlo a cabo son los siguientes:

1. **Obtención de los marcadores**

   - Obtén los marcadores de las detecciones de PeTra que se correspondan con las personas (aquellos denominados `person`).

2. **Transforma el punto de la persona al mapa**

   En este ejercicio, se requiere la transformación de la pose de la persona detectada al sistema de coordenadas del mapa. Esto es importante para que el robot pueda seguir a la persona, ya que necesita conocer la posición de la persona en el mapa.

   Para lograr esto, utilizaremos las transformaciones en ROS 2. Las transformaciones (tfs) permiten representar y modificar la posición y orientación de objetos en diferentes sistemas de coordenadas.

   - Se necesita obtener la transformación necesaria entre dos puntos de referencia. Esta transformación contiene información sobre la traslación y la rotación necesarias para transformar puntos del sistema de referencia de origen (la detección de la persona) al sistema de referencia de destino (el mapa). Se hará uso de la biblioteca `tf2` para obtener la transformación y mediante la función `lookup_transform` se obtendrá la transformación entre el sistema de coordenadas del mapa y el sistema de coordenadas del marcador que representa a la persona detectada.

   - Una vez obtenida la transformación se aplicará a la pose de la persona detectada utilizando la función `do_transform_pose` de `tf2_geometry_msgs`.

3. **Comprobar la distancia de la persona**

- Comprobar si el punto de la persona detectada obtenido en el sistema de referencia del mapa se encuentra a una distancia mayor de la establecida como distancia de seguridad para seguirla.
- Si la condición se cumple, entonces se enviará un `goal` a la acción `navigate_to_pose` de Nav2, lo que permitirá al robot navegar hacia un punto en el mapa, manteniendo una distancia segura respecto a la persona detectada.

## Ejercicio 4: Pruebas

- Utiliza el simulador de RB1 para simular el movimiento de una persona (puedes mover el actor a otro punto más alejado del robot pero que sea perceptible por el láser) y verifica que el robot pueda acercarse correctamente.
- Ajusta los parámetros de seguimiento para garantizar una interacción social adecuada, teniendo en cuenta la distancia a la que las personas se sienten cómodas.
