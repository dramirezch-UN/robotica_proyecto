## Descripción solucion creada, proceso de alistamiento, herramientas y piezas utilizadas.
Para abordar el problema planteado, se ha desarrollado una solución que involucra el diseño de una herramienta capaz de sostener tanto la ventosa como el gancho simultáneamente, manteniéndolos a una distancia considerable para evitar interferencias. Además, se han programado las rutinas correspondientes utilizando RobotStudio y se ha llevado a cabo la implementación en el laboratorio. Durante este proceso, fue necesario realizar ajustes en los objetos de trabajo para que las rutinas programadas se adaptaran a la realidad.

El proceso de alistamiento se llevó a cabo de acuerdo con las siguientes instrucciones, tal como se describieron en el enunciado del problema:

1. Tomar el contenedor (balde) ubicado en el extremo de la banda transportadora y colocarlo en un área designada para el alistamiento.
2. Seleccionar tres piezas diferentes de la estantería de madera y colocarlas dentro del balde.
3. Una vez que el pedido esté completo (balde con las piezas), volver a ubicarlo sobre la banda transportadora.

En cuanto a las piezas utilizadas, se optó por fabricarlas a partir de cartón. Esta elección se basó en experimentos previos que demostraron que el cartón se ajustaba mejor al balde en comparación con piezas hechas de otros materiales, como MDF o PLA (impreso en 3D). Además, se eligió el cartón debido a su simplicidad y a la disponibilidad de materiales existentes que se pudieron aprovechar en el proceso.


## Herramienta portaventosa y gancho.
### Proceso de Diseño
Para el diseño de la herramienta del proyecto de la asignatura se tomó en cuenta los dos requermientos de esta. Necesita sostener la ventosa para mover las piezas y sostener el gancho para mover el balde. Se obtuvieron las medidas de la placa de montaje del IRB140 para diseñar la base de la herramienta. Se hizo un diseño por partes para facilitar la impresión en 3D de la herramienta. Además, se tomaron las medidas de la ventosa en el laboratorio para realizar un modelo que nos permita sostenerla y a su vez asegurar la menor altura de la herramienta para facilitar la entrada dentro de la estantería. Se realizaron los debidos ajustes para minimizar el gasto de plástico en la impresión. 
### Fotografías
Diseño en Inventor de la herramienta propuesta.
![Herramienta completa](https://hackmd.io/_uploads/S1FtqZzdn.png)
Partes que se imprimieron en 3D.
![Piezas impresas](https://hackmd.io/_uploads/S1PJo-MO3.png)
Ensamblaje de la herramienta con la ventosa.
![Herramienta completa real1](https://hackmd.io/_uploads/B13Wo-zO3.jpg)
![Herramienta completa real2](https://hackmd.io/_uploads/HyqNsbM_2.jpg)

### Plano
![Plano](https://hackmd.io/_uploads/S1pkvzfdh.png)


## Celda de Ensamble RobotStudio
![](https://hackmd.io/_uploads/HyXa9WGd2.png)

Para la facil localizacion de los puntos y de los objetos de trabajo, se importaron los modelos en 3D de la banda, el balde y el gabinete. Con esto, el proceso de posicionamiento de cada punto y de generacion de los paths fue mucho mas sencillo.
Adicional a esto, se determinaron 3 WorkObject para poder posicinar todos los articulos correctamente en el LabSir, pues la ubicacion en el laboratorio de cada objeto podia variar dependiendo de cuando se asistiera a realizar las pruebas.
Los WorkObject son los siguientes:
- GabineteWobj
- BaldeWobj
- BandaWobj
- WorkObject por defecto (para posicionamiento de home)

## Codigo en RAPID
A continuacion se presenta el codigo en RAPID, en donde se realizo la respectiva programacion para el funcionamiento del robot. El codigo consta de multiples funciones las cuales se listaran a continuacion:
- Funciones PathA() hasta PathF(): En total, son 6 funciones, las cuales se encargan de recoger cada una de las piezas. Estan asociadas al workobject del gabinete. Cada una contara con el envio del impulso para la activacion de la succion, con un tiempo de espera de 1 segundo entre la activacion y la desactivcion de la segunda salida digital.
- Funcion Path_Deposito(): Sera la funcion encargada de depositar las piezas en el balde. Es una unica funcion, pues el movimiento para depositar las piezas sera el mismo. Se inicia con un MoveJ, lo que permite partir desde cualquier posicion de recoleccion de pieza. Asi como la desactivacion de la ventosa por medio de un impulso con un tiempo de espera entre la activacion y desactivacion de la primera salida digital.
- Funciones PathDBB() y PathDBP(): funciones encargadas de la recoleccion del balde. Una para recogerlo del piso y otra para recogerlo de la banda.
- Funciones PathRBB() y PathRBP(): funciones encargadas del deposito del balde. Una para depositarlo en el piso y otra para depositarlo en la banda.
- Funcion HOME(): Funcion que retorna el robot a su posicion de Home, se puede ejecutar en cualquier punto del funcionamiento del codigo, pues cuenta con un MoveJ.

Tambien se cuenta con multiples entradas y salidas para complementar el funcionamiento de la planta:

ENTRADAS
- DI_01 a DI_04: Son 4 entradas para activar las 4 rutinas diferentes. Cada rutina consta de la recoleccion de 3 piezas y el posicionamiento del balde
- 
SALIDAS
- DO_01: Encargada de desactivar la succion de la ventosa
- DO_02: Encargada de activar la succion de la ventosa
- DO_03: Tetsigo que indica la activacion de la rutina

Con esto claro, se procede a explicar el modulo principal del codigo, donde se cuenta con un ciclo while para que se pueda definir el limite de veces que se quiere ejecutar la rutina.
Una vez dentro del while, se contara con 4 condicionales que determinaran cual de las 4 rutinas se desea aplicar. Todos contaran con las mismas instrucciones de picking y placing para el balde, solo cambiara la posicion de las piezas a la cual se desea ir.
Primero se ejecutara la recoleccion del balde con PathRBB(), despues se depositara con PathDBB(), con esto, se procede a la recoleccion de piezas. Cada Path de recoleccion ira acompanado de Path_Deposito(), se repetira 3 veces acorde a las piezas que se quieran recoger. Concluido el proceso de deposito, se procede a llevar el balde a la banda; primero utilizando PathRBP() y despues depositandolo en la banda con PathDBB().

El código completo se puede encontrar en los archivos del repositorio.

## Comparacion de tiempos
Para la medicion de los tiempos, se realizaron 2 comparaciones directas entre simulacion y realidad. Es de tener en cuenta que son ubicaciones diferentes, esto debido a la localizacion de los workobjects en el laboratorio. 
Es de aclarar que los tiempos entre rutinas son muy similares, pues lo unico que cambiara ligeramente, va a ser el desplazamiento a las piezas mas lejanas (que son distancias cortas). De resto, la rutina es exactamente la misma. En el video se pueden observar estas comparaciones mas a profundidad.
En la primera comparacion, validamos los tiempos de la rutina A,C,E, donde en simulacion arrojaba un tiempo de 58 segundos y en la realidad entregaba un tiempo de 108.
En la segunda comparacion, validamos los tiempos de la rutina F,C,B, donde en simulacion arrojaba un tiempo de 56 segundos y en la realidad entregaba un tiempo de 60.
Estas diferencias de tiempo se pueden justificar principalmente al cambio de ubicacion en los WorkObject, pues en el laboratio todos los objetos se encontraban mas distantes que en simulacion.
En lo que respecta a velocidad, se puede mejorar en ciertos trayectos del recorrido, pero no es viable para la recoleccion y deposito del balde, pues tiende a ladearse y sacudirse, dificultando asi su adecuado posicionamiento y deposito de piezas.
No se hizo pick and place manual.