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

# 4. Módulos y Drivers