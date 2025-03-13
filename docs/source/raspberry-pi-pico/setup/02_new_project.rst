**************
Nuevo Proyecto
**************

Una vez que tenemos Visual Studio Code y la extensión instalada, vamos a abrir una instancia de VS Code y buscar en la barra lateral izquierda el ícono de la extensión de Raspberry Pi Pico.

Esta extensión permite trabajar en proyectos en C/C++ y en Micropython. En esta sección y las posteriores, vamos a enfocarnos en trabajar en C.

Buscamos y clickeamos la opción de *New C/C++ Project* para crear un nuevo proyecto. También es posible crearlo desde la paleta de comandos (*Ctrl + Shift + P* o *Cmd + Shift + P* en macOS) y tipeando el comando *New Pico Project* y clickearla entre las opciones cuando aparezca.

Configuración de proyecto
*************************

En la nueva pestaña vamos a tener que elegir algunas configuraciones para el proyecto. Por ahora vamos a solamente asegurarnos de elegir un nombre para el proyecto en *Basic Settings* y que el *Board type* sea **Pico**.

.. important:: 

  Es importante para que el programa compile y pueda correr correctamente que el *Board type* coincida con la placa de desarrollo que estamos usando. Si estamos usando la Raspberry Pi Pico es simplemente Pico la opción.

Entre otras opciones para elegir dentro del menú pero que ahora no vamos a cambiar tenemos:

- *Location* para elegir el directorio por defecto donde se va a crear el proyecto.
- *Select Pico SDK version* para elegir una versión en particular del SDK de la Raspberry Pi Pico. Importante a la hora de buscar soporte para alguna función en particular. A la fecha de este documento la versión más reciente es la v2.1.1.
- *Features* nos permite ya linkear algunas bibliotecas para hardware específico que querramos usar. Esto lo podemos modificar luego.
- *Stdio support* nos permite redirigir la salida de la función printf a una consola por UART o por USB. Recomendamos por USB.
- *Code generation options* habilita algunas funciones más complejas de C++ en las que no nos vamos a enfocar.
- *Debugger* nos permite elegir por donde pretendemos debuggear el microcontrolador. Salvo que estemos usando una Raspberry Pi para compilar y debuggear, la opción de DebugProbe es la apropiada.

Una vez que tengamos la configuración lista, podemos crear el proyecto.

Estructura de proyecto
**********************

Una vez que tengamos el proyecto creado, vamos a ver en el explorador de archivo una estructura de directorios como la siguiente:

.. code-block:: shell

  .
  ├── .vscode/
  │   └── ...
  ├── build/
  │   └── ...
  ├── .gitignore
  ├── CMakeLists.txt
  ├── pico_sdk_import.cmake
  └── main.c

Veamos un poco que significan estos directorios y archivos:

- *.vscode* es un directorio oculto que genera VS Code con varios archivos JSON con datos sobre las extensiones, el proyecto y las opciones de debuggeo entre otras.
- *build* es el directorio donde van a parar los archivos objeto compilados, entre ellos el .uf2 que se graba en la flash del microcontrolador.
- *.gitignore* es el archivo que se usa para decirle a Git que archivos o directorios ignorar del repositorio.
- *CMakeLists.txt* es el archivo que describe las reglas de compilación y las dependencias entre bibliotecas.
- *pico_sdk_import.cmake* tiene las reglas para obtener y compilar el SDK de la Raspberry Pi Pico.
- *main.c* o el nombre con el que hayamos llamado el proyecto, contiene nuestro programa principal con el que vamos a trabajar.