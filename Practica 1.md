# Conceptos teóricos

## 1. ¿Qué es el kernel de GNU/Linux? ¿Cuáles son sus funciones principales dentro del Sistema Operativo?

El kernel Linux está formado por un conjunto de programas encargados de controlar la ejecución de los programas y los dispositivos de Hardware de una computadora.

Sus principales funciones son la administración de memoria principal y el control del uso que le dan los procesos al CPU.

## 2. Indique una breve reseña histórica acerca de la evolución del kernel Linux

* 1991: comienza el desarrollo de Linux. En ese mismo año, se anuncia la primera versión oficial.
* 1992: la licencia de Linux cambia a una licencia GNU.
* 1994: se lanza la primera versión "completa", la `1.0`.
* 1995: portabilidad hacia arquitecturas DEC Alpha y Sun SPARC.
* 1996: se lanza la versión `2.0`. Se define la nomenclatura de versiones.
* 2001: se lanza la versión `2.4`. Esta es la versión que impulsó a Linux como un SO estable.
* 2003: se lanza la versión `2.6`, que incluye características importantes como soporte para threads.

## 3. Explique brevemente la arquitectura del kernel Linux teniendo en cuenta: tipo de kernel, módulos, portabilidad, etc.

El kernel Linux es un kernel monolítico híbrido. Que sea monolítico significa que la mayoría de las funciones del sistema se encuentran dentro del núcleo, por lo que deben ejecutarse en modo Kernel. Por otro lado, que también sea híbrido indica que soporta la agregación de módulos que pueden interactuar con las funciones del SO a través de llamadas al sistema (*System calls*).

La arquitectura de Linux está desarrollada para ser altamente portable, pudiendo ejecutarse en varias arquitecturas diferentes. La modularización de ciertas características del SO facilita esta tarea, puesto que permite abstraer a los módulos de las implementaciones propias de la arquitectura, de las cuales se encarga el Kernel.

## 4. ¿Cuáles son los cambios que se introdujeron en el kernel a partir de la versión 3.0? ¿ Cuál fue la razón por la cual se cambió de la versión 2 a la 3? ¿Y la razón para el cambio de la versión 3 a la 4?

El principal motivo del cambio a la versión `3.0` fue el aniversario de 20 años del SO y un cambio visual para no superar el número 40 en la nomenclatura de versiones; los cambios de esta versión fueron mínimos.

## 5. ¿Cómo se define el versionado de los kernels Linux?

Antes de Linux 2.6 el formato de versiones era `X.Y.Z`:
* **X** indicaba la serie principal, en las que se modificaba funcionalidad importante.
* **Y** indicaba si era una versión de producción o de desarrollo (si era par era de producción, si era impar era de desarrollo).
* **Z** indicaba arreglos menores y corrección de bugs.

Entre 2.6 y 3.0 el formato era `A.B.C.[D]`:
* **A** denota la versión principal. Cambia con menor frecuencia.
* **B** indica revisiones mayores en la funcionalidad.
* **C** indica revisiones menores, principalmente para soportar nuevos drivers o características.
* **D** se utiliza para indicar correcciones de errores.

A partir de 3.0 el formato pasó a ser `A.B.C[-rcX]`:
* **A** denota revisión mayor.
* **B** denota revisión menor.
* **C** número de revisión en la que se encuentra la versión.
* **D** indica si son versiones de prueba.

## 6. ¿Cuáles son las razones por las cuáles los usuarios de GNU/Linux recompilan sus kernels?

El objetivo de recompilar un kernel es el de modificar la funcionalidad del SO para distintos usos. Algunos de estos pueden ser:
* Soportar nuevos dispositivos.
* Optimizar el funcionamiento, ajustándo el kernel al sistema en el que corre.
* Corrección de errores.

## 7. ¿Cuáles son las distintas opciones para realizar la configuración de opciones de compilación de un Kernel? Cite diferencias, necesidades (paquetes adicionales de software que se pueden requerir), pro y contras de cada una de ellas.

La configuración del kernel puede realizarse de manera automática o manual.

La manera automática es más rápida y sencilla para usuarios no avanzados, pero no tiene tantas opciones de personalización. Por otro lado, la configuración manual es mucho más personalizable, pero el usuario debe tener ciertos conocimientos sobre cómo configurar el kernel correctamente.

## 8. Nombre al menos 5 opciones de las más importantes que encontrará al momento de realizar la configuración de un kernel para su posterior compilación.

zzz

## 9. Indique que tarea realiza cada uno de los siguientes comandos durante la tarea de configuración/compilación del kernel:
* `make menuconfig`: muestra el menú de configuración en una interfaz gráfica.
* `make clean`: elimina todos los archivos temporales que fueron creados durante la compilación, pero que ya no son necesarios.
* `make`: ejecuta el programa compilador. La opción `-j<número>` divide la configuración en la cantidad de hilos que se le indique en el número; este número debería ser menor o igual a la cantidad de procesadores disponibles.
* `make modules`: realiza la compilación de los módulos.
* `make modules_install`: instala todos los módulos que hayan sido compilados junto con el kernel.
* `make install`: instala el kernel que haya sido compilado.

## 10. Una vez que el kernel fue compilado, ¿dónde queda ubicada su imagen? ¿dónde debería ser reubicada? ¿Existe algún comando que realice esta copia en forma automática?

El kernel compilado suele almacenarse en `/usr/src/<versión>`, pero debería ser reubicada en el directorio `/boot` para que el sistema pueda arrancar usándolo. El comando `make install` realiza esta copia automáticamente.

## 11. ¿A qué hace referencia el archivo initramfs? ¿Cuál es su funcionalidad? ¿Bajo qué condiciones puede no ser necesario?

Initramfs es un sistema de archivos que no tiene almacenamientos en caché, por lo que toda su carga de memoria desaparece cuando termina. Se usa durante el arranque del sistema.

## 12. ¿Cuál es la razón por la que una vez compilado el nuevo kernel, es necesario reconfigurar el gestor de arranque que tengamos instalado?

Es necesario actualizar el gestor de arranque porque éste no tiene manera de saber que se compiló un nuevo kernel, por lo que aunque lo tengamos configurado no vamos a verlo como una opción al momento de iniciar el sistema.

## 13. ¿Qué es un módulo del kernel? ¿Cuáles son los comandos principales para el manejo de módulos del kernel?

Los módulos del kernel son partes de Software que brindan funcionalidad al sistema, extendiendo la funcionalidad del SO. A diferencia del kernel, estos módulos pueden agregarse o eliminarse sin necesidad de recompilar el sistema.

## 14. ¿Qué es un parche del kernel? ¿Cuáles son las razones principales por las cuáles se deberían aplicar parches en el kernel? ¿A través de qué comando se realiza la aplicación de parches en el kernel?

Los parches del kernel son actualizaciones que se realizan sobre una versión mayor. El motivo de aplicar parches es realizar correcciones de errores o pequeños cambios en el kernel que no requieren una nueva versión por completo.

El comando para aplicar parches en Linux es `patch`.

# Ejercicio taller: compilación del kernel Linux

### 1. Descomprimir archivo `btrfs.image.xz`
<img src="./img/Practica 1/ej1.png">

### 2. Montar dispositivo
<img src="./img/Practica 1/ej2.png">

El error se debe a que nuestro kernel actualmente no tiene soporte para archivos `.btrfs` en su filesystem, y tampoco puede configurar dispositivos de loopback.

### 3. Descargar y aplicar parche al código fuente del kernel

Descarga del código fuente:

<img src="./img/Practica 1/ej3a.png">

Descarga del parche:

<img src="./img/Practica 1/ej3b.png">

Aplicación del parche:

<img src="./img/Practica 1/ej3d.png">

### 4. Configuración para configurar el código fuente del kernel

Copiamos el código del kernel y usamos `make olddefconfig` para actualizar la configuración con valores default.

<img src="./img/Practica 1/ej4.png">

Luego usamos `make menuconfig` para mostrar en una interfaz gráfica el menú de configuración del kernel.

<img src="./img/Practica 1/ej4menu.png">

Primero deshabilitamos la firma criptográfica, yendo a `Cryptographic API -> Certificates for Signature Checking -> Additional X.509 certificates for signature checking`.

<img src="./img/Practica 1/ej4a.1.png">

Al seleccionar esta opción tenemos que ingresar un String; en este caso lo vamos a dejar en blanco para deshabilitarlo.

<img src="./img/Practica 1/ej4a.2.png">

Luego vamos a activar el soporte para archivos `.btrfs` yendo a `File Systems -> Btrfs filesystem support`.

<img src="./img/Practica 1/ej4b.1.png">

Port último vamos a permitir también el soporte para dispositivos loopback, yendo a `Device Drivers -> Block Devices -> Loopback device support`.

<img src="./img/Practica 1/ej4c.1.png">

### 5. Compilación del kernel

Esto tardó un montón creo que porque usé `make -j1`.

### 6. Configuración de módulos

#### a.
Primero instalé los módulos con `make modules_install`, pero lo tuve que ejecutar con el usuario root porque sino no andaba.

#### b.

Usé el método recomendado

<img src="./img/Practica 1/ej6b.png">

#### d.
Actualizamos el gestor de arranque para poder usar el kernel nuevo

<img src="./img/Practica 1/ej6d.png">

## 8. Repetición del inciso 2

No sé qué pasó exactamente pero ya no tiró el error, así que supongo que esta vez lo pudo hacer bien porque configuramos el kernel para que permite loopback. Si ejecuto el comando de nuevo aparece que el archivo ya fue montado.

<img src="./img/Practica 1/ej8.png">

## 9. Entrega de ejercicio obligatorio

<img src="./img/Practica 1/ej9.png">
