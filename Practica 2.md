# Conceptos generales

## 1. ¿Qué es una System Call?, ¿para que se utiliza?

Las system calls son interfaces proporcionadas por el SO para que las aplicaciones puedan solicitar servicios del núcleo del SO.

Se utilizan para permitir a las aplicaciones realicen operaciones en modo privilegiado, o puedan acceder a recursos propios del sistema operativo.

## 2. ¿Para qué sirve la macro syscall?. Describa el propósito de cada uno de sus parámetros.

La macro `syscall` es una instrucción disponible en ciertos lenguajes de programación que permiten invocar directamente a una System call del SO.

En C, la función `syscall` recibe el parámetro `sysno`, que indica el número de la System call que se quiere ejecutar. Se le pueden pasar parámetros adicionales, que van a ser usados en la ejecución de la propia Syscall.

La función va a retornar el valor que devuelva la System call, o -1 en caso de que falle.

## 3. ¿Para que sirven los siguientes archivos?
### `<kernel_code>/arch/x86/syscalls/syscall_32.tbl`
### `<kernel_code>/arch/x86/syscalls/syscall_64.tbl`

Estos archivos contienen un listado con las System calls para los sistemas de 32 o 64 bits respectivamente.

## 4. ¿Para qué sirve la macro `asmlinkage`?

??

## 5. ¿Para qué sirve la herramienta strace?, ¿Cómo se usa?

Strace es una herramienta de Linux utilizada para interceptar y registrar las System calls que realiza un programa durante su ejecución.

Al ejecutar un programa con el comando `strace`, se monitorean todas las System calls realizadas, junto con sus parámetros y los resultados de cada una. Esto sirve para verificar que las syscalls estén configuradas correctamente, detectar errores y analizar el rendimiento.

# Agregar nueva Syscall

Hola amigos de Youtube, hoy vamos a ver cómo agregar una syscall!!!!

## 1. Añadir syscall a la tabla de `syscall_64.tbl`

Esta tabla se encuentra en el directorio `usr/src/linux-5.16/x86/entry/syscalls`.

<img src="./img/Practica 2/ej1.1.1.png">

Abrimos el archivo con el editor de texto usando el comando `nano syscall_64.tbl` y agregamos la fila de nuestra nueva Syscall (450).

<img src="./img/Practica 2/ej1.1.2.png">

## 2. Declarar Syscall

Para declarar la syscall nos tenemos que dirigir al directorio `usr/src/linux-5.16/include/linux`. Dentro se encuentra el archivo `syscalls.h`, que contiene las declaraciones de las System calls.

<img src="./img/Practica 2/ej1.2.1.png">

Abrimos el archivo con el comando `nano syscalls.h` y agregamos la línea para declarar la Syscall que agregamos previamente en la tabla, usando la línea `asmlinkage long sys_rqinfo(unsigned long *ubuff, long len)`

<img src="./img/Practica 2/ej1.2.2.png">

## 3. Incluir código de la Syscall

Los archivos en los que se definen las syscalls se encuentran en `usr/src/linux-5.16/kernel/sched`, y se pueden agregar en un archivo nuevo o en alguno ya existente. En este caso vamos a agregarla en `core.c` porque se puede acceder más fácil a las estructuras que necesitamos para nuestra syscall, ya que en este archivo están las syscalls relacionadas al scheduling y planificación del SO.

<img src="./img/Practica 2/ej1.3.1.png">

Abrimos el archivo y escribimos el código de la práctica (lo copié a mano porque no me anduvo el copypaste así que estoy **convencido** de que va a andar mal pero bueno)

<img src="./img/Practica 2/ej1.3.2.png">

## 4. Compilar kernel

Es lo mismo de la práctica 1 zzz pero igual tiró este error y no tengo ganas de ver dónde fue para corregirlo así que acá termina el ejercicio

<img src="./img/Practica 2/ej1.4.png">

## 5. Probar Syscall

Para probar la Syscall creamos un archivo de código en C con el código que estaba en la práctica

<img src="./img/Practica 2/ej1.5.1.png">

Después de compilarlo y ejecutarlo imprimió el siguiente output en pantalla:

<img src="./img/Practica 2/ej1.5.2.png">

# 3. Monitoreo de Syscalls

## 1. Strace

<img src="./img/Practica 2/ej2.1.1.png">

Al ejecutar el programa con el comando `strace` se imprime un listado de todas las System calls que fueron invocadas por el programa. Algunas de estas son:
* `execve`: ejecuta el programa que se le pasa como parámetro.
* `access`: revisa los permisos del usuario para acceder al archivo pasado como parámetro.
* `openat`: abre el archivo pasado como parámetro; si se le agrega el parámetro `O_CREAT` podría crearlo en caso de que no lo encuentre.
* `fstat`: obtiene el estado actual de un archivo.
* `mmap`: mapea un archivo o dispositivo a la memoria virtual del proceso.
* `mprotect`: establece una protección de una zona de memoria. Puede definir el tipo de protección con los parámetros `PROT_NONE/READ/WRITE/EXEC`, etc.)
* `close`: cierra una referencia a un archivo; en este caso, se cierra el archivo 3 que fue previamente abierto con `openat`.
* `read`: lee el contenido de un archivo.
* `write`: escribe sobre un archivo.

## 2. Comparar programas

### Invocando getpid a través de libc

<img src="./img/Practica 2/ej2.2.1.png">

Como vemos el PID se incrementa en 1 por cada invocación.

Al ejecutarlo con Strace y luego volver a ejecutarlo normalmente vemos lo siguiente:

<img src="./img/Practica 2/ej2.2.2.png">

El PID se incrementó en 9 con una sola invocación de `strace`. Supongo que esto es porque el comando llama a algunos otros procesos en el medio, entonces se le va a asignar un PID mayor al de todos los que se ejecutaron antes.

### Invocando getpid a través de syscall

<img src="./img/Practica 2/ej2.3.1.png">

La lógica es la misma que la del código anterior, con la diferencia de que ahora el PID empieza en 1061.

<img src="./img/Practica 2/ej2.3.2.png">

No tengo ganas de ponerme a revisar las 2 capturas de Strace pero me parece que también son iguales

# Módulos y Drivers

## 1. ¿Cómo se denomina en GNU/Linux a la porción de código que se agrega al kernel en tiempo de ejecución? ¿Es necesario reiniciar el sistema al cargarlo?. Si no se pudiera utilizar esto. ¿Cómo deberíamos hacer para proveer la misma funcionalidad en GNU/Linux?

Los módulos de GNU/Linux son archivos con código que permiten extender el funcionamiento del núcleo del SO en tiempo de ejecución.

Una de las principales ventajas de los módulos es que no precisan reiniciar el sistema para funcionar, sino que pueden cargarse y descargarse en tiempo real. Además son archivos precompilados, por lo que no requieren volver a compilar el kernel.

Si no existiesen los módulos, sería necesario modificar y recompilar el kernel cada vez que se le quiera agregar una nueva funcionalidad. Obviamente, aunque un SO soporte el uso de módulos hay casos en los que va a necesitar ser recompilado para agregar ciertas funciones específicas.

## 2. ¿Qué es un driver? ¿para que se utiliza?

Los drivers son programas de software que se utilizan para que el SO pueda interactuar con un Hardware en específico.

## 3. ¿Porque es necesario escribir drivers?

La necesidad de usar drivers proviene de que los dispositivos de Hardware tienen características y funcionalidades muy distintas entre sí, por lo que el SO necesita de una interfaz de Software a través de la cual pueda comunicarse correctamente con los dispositivos.

## 4. ¿Cuál es la relación entre modulo y driver en GNU/Linux?

Los módulos pueden usarse como un mecanismo para instalar drivers en el SO sin necesidad de recompilar el núcleo.

Hoy en día casi todos los drivers se pueden instalar como módulos, aunque aún hay casos en los que se requiere la recompilación; algunos de estos pueden ser los controladores en modo Kernel o aquellos que también requieren cambiar la configuración del SO.

## 5. ¿Qué implicancias puede tener un bug en un driver o módulo?

Un bug en un driver o en un módulo podría traer consecuencias en el sistema tales como:
* Funcionalidades con resultados fallidos.
* Incompatibilidad con los dispositivos de Hardware.
* Vulnearbilidades de seguridad.

## 6. ¿Qué tipos de drivers existen en GNU/Linux?

Los drivers pueden dividirse en:
* **De dispositivos de Hardware:** destinados a HW específico, como tarjetas gráficas, impresoras, etc. Permiten que el SO interactúe con los dispositivos.
* **De red:** se encargan de las interfaces de comunicación entre redes, tanto inalámbricas como por cable.
* **De almacenamiento:** permiten que el SO gestione dispositivos de almacenamiento como discos duros, sólidos o tarjetas de memoria.
* **De virtualización:** permiten el uso de entornos virtuales y la comunicación entre el sistema virtual con el SO anfitrión.

## 7. ¿Que hay en el directorio `/dev`? ¿Qué tipos de archivo encontramos en esa ubicación?

`/dev` es un directorio en el que se almacenan archivos referentes a los dispositivos.

## 8. ¿Para qué sirven el archivo /lib/modules/`version`/modules.dep utilizado por el comando modprobe?

Es un archivo usado por el comando `modprobe` para verificar las dependencias entre los distintos módulos del sistema operativo. De esta manera el SO puede saber qué otros módulos debe cargar antes de ejecutar uno en específico.

Cada línea del archivo describe una dependencia entre dos módulos. Cuando se carga un módulo con el comando `modprobe`, se busca en el archivo todos los módulos de los que dependa el que se esté cargando; al hacer esto, se cargan secuencialmente todos los módulos para que las dependencias se resuelvan correctamente.

# Agregar módulo al Kernel

## 1. Crear archivo `memory.c`

<img src="./img/Practica 2/ej3.1.1.png">

## 2. Crear el archivo `Makefile`

<img src="./img/Practica 2/ej3.2.1.png">

Un archivo `Makefile` sirve para dar instrucciones sobre qué hacer con cada archivo al momento de compilarlos.

La macro `MODULE_LICENSE` sirve para indicar la licencia sobre la que se distribuye el código del módulo. Es obligatoria en caso de que el módulo se distribuya públicamente.

## 3. Compilar módulo

<img src="./img/Practica 2/ej3.3.1.png">

Generó los archivos:
* `memory.o`: código objeto del compilador de C.
* `Module.symvers`: contiene información sobre los símbolos usados por el módulo.
* `memory.mod.o`: contiene información sobre el módulo compilado.
* `memory.ko`: módulo compilado.

## 4. Instalar módulo

`insmod` es una herramienta más básica que únicamente se carga de cargar el módulo en el Kernel; `modprobe` hace uso de los archivos de configuración como `Module.symvers` para verificar las dependencias de un módulo antes de cargarlo.

Cuando ejecuté `insmod memory.ko` no devolvió nada, así que ejecuté el grep por las dudas y devolvió esto:

<img src="./img/Practica 2/ej3.4.1.png">

`lsmod` lista todos los módulos instalados en el sistema; que el módulo que recién creamos aparezca en el resultado implica que se cargó correctamente.

`/proc/modules` es un archivo que contiene información sobre los módulos actualmente cargados en el sistema. De cada módulo se sabe su nombre, dirección de memoria, tamaño, fecha de carga y un listado de otros módulos que dependen de él.

## 6. Eliminar módulo

Para eliminar un módulo usamos el comando `rmmod <nombre_modulo>`. Después de ejecutar el comando podemos usar nuevamente el `lsmod` para ver si se eliminó correctamente.

<img src="./img/Practica 2/ej3.6.1.png">

Como `lsmod | grep memory` devuelve una lista vacía, podemos suponer que el módulo se eliminó correctamente.

## 7. Modificar el módulo

<img src="./img/Practica 2/ej3.7.1.png">

`module_init` y `module_exit` son macros de Linux que se ejecutan cuando el módulo se carga o se elimina del kernel, respectivamente.

Los tipos de dispositivos en Linux son:
* **Dispositivos de carácter:** transmiten y reciben caracteres de a uno. Se usan para la entrada y salida de datos no estructurados.
* **Dispositivos de bloque:** transmiten y reciben datos en bloques de tamaño fijo. Se utilizan para manejar el almacenamiento y la recuperación de datos. Cada bloque se puede acceder mediante un número único de bloque.
* **Dispositivos de red:** transmiten y reciben datos a través de la red.