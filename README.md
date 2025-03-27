# TP1: Algoritmos de búsqueda en Torre de Hanoi

## Grupo de trabajo 1
**Miembros:**
- Martin Brocca <martinbrocca@gmail.com>
- Emiliano Iparraguirre <emiliano.iparraguirre22@gmail.com>
- Natalia Espector <nataliaespector@gmail.com>
- Agustin Lopez Fredes <agustin.lopezfredes@gmail.com>
- Fernando Martinez <fgmartinez1989@gmail.com>

##
En clase presentamos el problema de la torre de Hanoi. Además, vimos diferentes algoritmos de búsqueda que nos 
permitieron resolver este problema. Para este trabajo práctico, deberán implementar un método de búsqueda para 
resolver con 5 discos, del estado inicial y objetivo que se observa en la siguiente imagen:

![Torres Hanoi](./torres.png "Torres Hanoi")

## Tareas y preguntas a resolver:

1. ¿Cuáles son los PEAS de este problema? (Performance, Environment, Actuators, Sensors)
2. ¿Cuáles son las propiedades del entorno de trabajo?
3. En el contexto de este problema, establezca cuáles son los: estado, espacio de estados, árbol de búsqueda, 
nodo de búsqueda, objetivo, acción y frontera.
4. Implemente algún método de búsqueda. Puedes elegir cualquiera menos búsqueda en anchura primero (el desarrollado 
en clase). Sos libre de elegir cualquiera de los vistos en clases, o inclusive buscar nuevos.
5. ¿Qué complejidad en tiempo y memoria tiene el algoritmo elegido? 
6. A nivel implementación, ¿qué tiempo y memoria ocupa el algoritmo? (Se recomienda correr 10 veces y calcular 
promedio y desvío estándar de las métricas).
7. Si la solución óptima es $2^k - 1$ movimientos con *k* igual al número de discos. Qué tan lejos está la solución 
del algoritmo implementado de esta solución óptima (se recomienda correr al menos 10 veces y usar el promedio de 
trayecto usado). 

El entregable es, por un lado, un archivo de `txt/PDF/Word` con las respuestas y por otro, los archivos con el código 
implementado. Si además agregan los json para usar en el simulador, es mejor. El entregable tambien puede consistir 
en uno o más archivos de notebook `ipynb` con las respuestas. Aunque se da libertad para usar otros tipos de 
entregables, es importante incluir tanto el código de lo resuelto como las respuestas. Pueden subir el contenido o p
roporcionar un enlace a un repositorio público (GitHub o GitLab) con el contenido en el aula virtual. 
**No olviden especificar los autores del TP en el entregable**.

Para resolver este TP son libres de usar los recursos que crean necesarios. Pueden resolverlo en cualquier lenguaje de 
programación y de la forma que consideren apropiada. 

Pueden ahorrar tiempo usando el código ya implementado en Python que se encuentra en el path `../hanoi_tower`. 
Si usan este código, solo deben implementar el algoritmo de búsqueda, pero es importante que lean el código y 
entiendan que es cada parte.

## Resolución:

1. A continuación se listan los PEAS de este problema:

    **Performance:**
    El objetivo principal es trasladar todos los discos de un rodillo inicial a un rodillo objetivo. Los discos deben ser colocados en orden ascendente, es decir, el disco más chico en la parte superior.
    El objetivo en cuanto a performance es minimizar la cantidad total de movimientos realizados para alcanzar la solución. 

    **Entorno:**
    El entorno cuenta con los siguientes elementos:
      - Tenemos un conjunto de discos de diferente tamaño  y comúnmente los representamos mediante números enteros.
      - Reglas del entorno:
      - Solo se puede mover un disco a la vez.
      - Un disco de mayor tamaño no puede ser colocado sobre uno de menor tamaño.
      - Tres rodillos, que son las estructuras físicas sobre las que se apilan los discos.

    **Actuadores:**
    Los actuadores representan las acciones que el sistema puede ejecutar, en este caso la capacidad de mover un disco de un rodillo a otro, siguiendo las restricciones y reglas establecidas para el problema.

    **Sensores:**
    Los sensores en este contexto son responsables de recolectar información del entorno para permitir la toma de decisiones:
      - Estado actual de cada rodillo.
      - Verificaciones si los movimientos que se realizan son permitidos o no.
      - Verificar que el estado final sea exitoso o no.

2. ¿Cuáles son las propiedades del entorno de trabajo?

    Las propiedades del entorno de trabajo en Inteligencia Artificial (IA) describen las características del entorno en el que opera un agente. Estas propiedades ayudan a determinar qué tipo de estrategias y algoritmos son más adecuados para resolver el problema.

    Puntualmente para el caso de la torre de Hanoi se presentan las siguientes propiedades:
      - **Es totalmente observable:** en todo momento se conoce la posición exacta de cada disco en las tres varillas. En este caso, no hay información oculta ni estados desconocidos, por lo que el agente puede tomar decisiones basadas en información completa, sus sensores pueden sensar el estado completo del entorno.
      - **Es determinístico:** cada acción, mover un disco de una varilla a otra, tiene un único resultado predecible. No hay elementos aleatorios o factores externos que puedan cambiar el estado del problema
      - **Es secuencial:** cada movimiento presente afecta el futuro de la solución en cada paso, por lo que no se pueden hacer movimientos al azar sin seguir una estrategia, ya que cada decisión influye en los pasos siguientes hasta llegar al objetivo.
      - **Es estático:** el estado del sistema solo cambia cuando el agente realiza una acción, no antes. No hay eventos externos que alteren la posición de los discos de manera espontánea. Un estado actual y una acción  generan un cambio siempre igual.
      - **Es discreto:** tanto los estados como las acciones son finitos y bien definidos. Los discos pueden estar en una cantidad finita de posiciones dentro de las varillas, y las acciones posibles también son limitadas.
      - **Es de un agente individual:** solo hay un jugador (el algoritmo de búsqueda) tomando decisiones y moviendo los discos. No hay competencia ni colaboración con otros agentes.

    Esto resume que el entorno de la Torre de Hanoi, como se mencionó en clase, es completamente estructurado y predecible, lo que lo convierte en un problema ideal para resolver mediante algoritmos de búsqueda. Sus características mencionadas permiten que un agente pueda planificar y ejecutar una secuencia óptima de movimientos sin incertidumbre. 

    Estas propiedades facilitan la aplicación de estrategias como búsqueda en profundidad, en anchura o heurísticas, dando una solución eficiente dentro de un espacio finito bien definido.

3. En el contexto de este problema, establezca cuáles son los: estado, espacio de estados, árbol de búsqueda, nodo de búsqueda, objetivo, acción y frontera.

    1. **Estado:** el estado es una configuración específica de los discos en las tres varillas en un momento dado. Se puede hacer un ejemplo con una lista de tres pilas, donde cada pila (varilla) contiene los discos que tiene en orden:
        - Estado inicial: ([5,4,3,2,1],[],[])
        - Estado intermedio: ([5,4],[3,2],[1])
        - Estado final: ([],[],[5,4,3,2,1]) -> todos los discos en la última varilla
    2. Espacio de estados: es el conjunto de las combinaciones posibles de los discos en las varillas. Como cada uno de los 5 discos puede estar en cualquiera de las 3 varillas, el número total de estados posibles es 3^5 = 243
    3. Árbol de búsqueda: es la exploración de todas las posibles soluciones desde el estado inicial hasta el objetivo:
        - Cada nodo en el árbol es un estado del problema
        - Las ramas son las acciones posibles (mover un disco de una varilla a otra)
        - El estado inicial es la raíz del árbol
        - El estado objetivo es un nodo hoja
        - El árbol crece exponencialmente porque en cada nivel se generan nuevos estados dependiendo de los movimientos posibles.
    4. Nodo de búsqueda: es un elemento del árbol de búsqueda que contiene un estado específico, un puntero al nodo padre (estado anterior), la acción que llevó a ese estado, y el costo acumulado (movimientos realizados hasta ese momento). Cada nodo es un paso en la secuencia de solución.
    5. Objetivo: alcanzar un estado en el que todos los discos estén en la tercera varilla, en el mismo orden en el que estaban originalmente en la primera -> ([],[],[5,4,3,2,1])
    6. Acción: es la operación de mover un disco de una varilla a otra, cumpliendo las reglas del juego: solo se mueve un disco superior de una pila y no se puede colocar un disco grande sobre uno más pequeño.
    7. Frontera: es el conjunto de nodos que están en proceso de ser explorados en la búsqueda. Por ejemplo:
        - En búsqueda en anchura, la frontera es la fila de estados pendientes de ser visitados
        - En búsqueda en profundidad, la frontera es la pila de estados que se están explorando
        - En algoritmos heurísticos, la frontera almacena los estados ordenados por una función de costo


4. Para detalles de la implementación de búsqueda en profundidad (DFS), Greedy y A<sup>*</sup>, referirse a la [notebook TP1](./tp1.ipynb).

5. ¿Qué complejidad en tiempo y memoria tiene el algoritmo elegido? 
   
   Para analizar la complejidad en la implementación de búsqueda en profundidad se deben de considerar el comportamiento en función del número de discos *(n)*
     - Tiempo de ejecución: 
        - En el peor de los casos, el algoritmo de DFS debe de explorar el total de los $3^n$ estados posibles, dado que puede no encontrar la solución hasta el último estado. 
        - Cada estado require un trabajo del $O(n)$ (expansión, comparación, etc.) 
        - En consecuencia, el peor caso de complejidad  de tiempo es de $O(n \cdot 3^n)$
    - Uso de memoria:
        - DFS puede llenar la pila de *frontier* hasta la profundidad máxima del $O(3^n)$ que corresponde al peor caso.
        - DFS almacena hasta $3^n$ estados, cada estado require un espacio del $O(n)$ por cada lista de varillas.
        - En consecuencia, la complejidad de memoria es: $O(n \cdot 3^n)$
    
    
    Al realizar un análisis similar para el caso de la búsqueda Greedy, el comportamiento en el peor escenario resulta en un tiempo de $O(3^n)$. 
    En un caso óptimo, en donde la función de heurística guía al algoritmo perfectamente hacia la solución, se realiza por un camino de $2^n -1$. De esto resulta que el tiempo de ejecución es del $O(n2\cdot^2)$

    En la búsqueda estrella, al emplear una cola de prioridad para explorar los nodos, se asegura una solución optima con una complejidad de tiempo del $O(n2\cdot^2)$ mejorando significativamente a los resultados obtenidos por los metodos Greedy y Profundidad.



6. A nivel implementación, ¿qué tiempo y memoria ocupa el algoritmo?
     La tabla a continuación muestra una comparación de tiempo de ejecución y memoria usada tanto por el algoritmo de búsqueda en anchura visto en clase, búsqueda en profundidad y búsqueda en profundidad optimizado.

    | Algoritmo | Tiempo de ejecución (%%timeit) | Memoria empleada (%%memit) |
    |-----------|---------------------|------------------|
    |Búsq. en anchura | 37.5 ms ± 86.6 μs por ciclo | pico de memoria: 83.78 MiB, incremento: 2.77 MiB |
    |Búsq. en prof.   | 38 ms ± 790 μs por ciclo | pico de memoria: 84.05 MiB, incremento: 0.00 MiB |
    |Búsq. en prof. optimizado| 2.28 ms ± 38 μs por ciclo | pico de memoria: 73.42 MiB, incremento: 0.11 MiB | 
    |Búsq. Greedy| 2.25 ms ± 16.2 μs por ciclo | pico de memoria: 71.28 MiB, incremento: 0.00 MiB |
    |Búsq A* | 2.26 ms ± 19.7 μs por ciclo | pico de memoria: 71.39 MiB, incremento: 0.00 MiB| 



7. Si la solución óptima es $2^k - 1$ movimientos con *k* igual al número de discos. Qué tan lejos está la solución 
del algoritmo implementado de esta solución óptima
     La tabla a continuación muestra la longitud de caminos encontrados por los algoritmos:
     | Algoritmo | Nodos explorados | Estados visitados | Nodos en frontera | Profundidad maxima | Costo total|
     |-----------|------------------|-------------------|-------------------|--------------------|-----------|
     |Busq. en anchura | 1351 | 233| 285 | 31 | 31 |
     |Busq. en prof.   | 122 | 122 | 63 | 121 | 121 |
     |Busq. en prof. optimizado| 82 | 82 | 41 | 81 | 81 |
     |Búsq. Greedy| 377 | 231 | 51 | 31 | 31 |
     |Búsq A* | 382 | 215 | 45 | 31 | 31 |


     Entonces, para el problema de las torres de Hanoi con 5 discos la distancia óptima es de 31 movimientos. La tabla indica que el algoritmo de búsqueda en anchura encuentra la solución optima, mientras que la búsqueda en profundidad tiene una distancia de 90 mientras que la verión optimizada, en donde se controla no mover dos veces seguidas las misma pieza, tiene una distancia de 50.

## Resumen del trabajo:
- **BFS**
  
  En este trabajo práctico, se analizó el código provisto en la clase para resolver el problema de la Torre de Hanoi con un algoritmo de búsqueda primero en anchura (BFS), que consiste en utilizar una cola FIFO (First In - First Out) para definir el orden en que se exploran los nodos del árbol.
- **DFS**
  
  Tomando como base el ejemplo base, se implementó el algoritmo de búsqueda primero en profundidad (DFS). Para esto, se convirtió la cola FIFO en una cola LIFO (Last In - First Out). Esto se realizó modificando la forma en que se agregan elementos a la lista en cada iteración. En el caso de la cola FIFO se utilizaba un *insert*, que introduce el nuevo elemento al principio de la lista, y luego se utiliza *pop* para extraer el último elemento. Para convertirlo en una cola LIFO, se reemplaza el insert por *append*, que inserta el nuevo elemento al final de la lista, y se mantiene el *pop* para extraer el último elemento. 
- **DFS_Smart**
  
  Luego se realizó una modificación extra para optimizar el algoritmo DFS, al que se le llamó DFS_Smart. Se observó que en los casos en que había dos movimientos posibles para un disco, se plantean como dos estados posibles, pero luego en el siguiente nivel se vuelve a proponer ese mismo movimiento. Por esta razón, se agregó una función que evalúa si se está moviendo el mismo disco que se movió anteriormente, y se limita ese movimiento.
- **Greedy**
  
  Por otro lado, se implementó un algoritmo de búsqueda informada de tipo Greedy. Para esto, se planteó una función heurística que cuenta cuántos discos están fuera de la posición objetivo. Esta función se utilizó para crear una cola prioritaria, ordenando los elementos según el valor de la función, utilizando heapq.heappush para insertarlos en la cola prioritaria y heapq.heappush para extraerlos en el orden propuesto.
- **A\***
  
  Por último se implementó el algoritmo A*, que funciona de manera similar al algoritmo Greedy, pero la prioridad de la cola prioritaria está dada por el valor del costo más el valor de la función heurística. De todas formas, es importante tener en cuenta que en este problema todos los movimientos tienen el mismo costo.


## Conclusiones finales:

Este trabajo permitió aplicar de manera práctica los conceptos de agentes, entornos y algoritmos de búsqueda dentro del clásico problema de la Torre de Hanoi. A partir del modelo PEAS se logró identificar a los elementos clave del sistema, como el entorno estructurado, las reglas del juego y las acciones posibles. Esto permite desarrollar el problema en un en un entorno completamente observable, determinístico, discreto y de un solo agente, lo cual lo vuelve ideal para aplicar técnicas de búsqueda.


Además, al implementar distintos algoritmos de búsqueda (BFS, DFS y DFS_Smart, Greedy y A*) se observa en la práctica cómo varían en tiempo, memoria y eficiencia. Resulta interesante observar que, aunque DFS consume menos memoria, puede alejarse bastante de la solución óptima, mientras que BFS garantiza la solución más corta, pero a mayor costo computacional. DFS_Smart logró un equilibrio, reduciendo movimientos innecesarios. Se observó también que los algoritmos de búsqueda informada Greedy y A*, que incorporan el uso de la función heurística, alcanzan ambos la solución óptima de 31 movimientos al igual que BFS, pero reduciendo considerablemente la cantidad de nodos explorados.


Otro resultado alcanzado fue el de modelar formalmente un problema desde el punto de vista de IA: identificamos qué es un estado, una acción, un nodo, el objetivo y la frontera de búsqueda. Esto nos dio una visión más clara de cómo los algoritmos "piensan" o buscan la mejor manera de resolver un problema.


En resumen, este TP fue útil no solo para aplicar lo visto en clase, sino también para empezar a desarrollar una forma de pensar en términos de agentes inteligentes y planificación.
