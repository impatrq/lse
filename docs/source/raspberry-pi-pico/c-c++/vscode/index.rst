*************************************
Raspberry Pico con Visual Studio Code
*************************************

Visual Studio Code es uno de los IDEs mas populares y flexibles hoy en dia. Lo usaremos como entorno para desarrollar nuestros programas para la Raspberry Pi Pico, pero, lamentablemente, existe un conjunto de herramientas que debemos instalar junto con este IDE para poder programar nuestro microcontrolador.

Este proceso es bastante mas complejo en computadoras que corran Windows, por lo que esta guia cubre especialmente esos casos.

Requisitos
~~~~~~~~~~

- Tener instalado Visual Studio Code
- Una Raspberry Pi Pico
- Cable micro USB

MinGW GCC Tools
~~~~~~~~~~~~~~~

El compilador de C/C++ es lo primero que debemos instalar. Para eso, vamos a la pagina de MinGW_ y descargamos la ultima version de GCC que corresponda a nuestro procesador (``i386`` para procesadores de 32 bits y ``x86_64`` para procesadores de 64 bits).

.. _MinGW: https://sourceforge.net/projects/mingw-w64/files/

Una vez descargado, descomprimirlo o instalarlo en el raiz del sistema (disco C para Windows). Deberia aparecer un directorio nuevo llamado ``mingw64`` o similar.

Luego, debemos decirle a Windows donde hallar este compilador, por lo que debemos agregar la ruta ``C:\mingw64\bin`` a las `variables de entorno del sistema`_.

.. _variables de entorno del sistema: https://www.architectryan.com/2018/03/17/add-to-the-path-on-windows-10/

.. note::
    Si el directorio ``mingw64`` tuviese otro nombre, ese es el que debe ir en el comando.

Por ultimo, abrir una consola de comandos de Windows y escribir lo siguiente:

.. code::

    echo mingw32-make %* > C:\mingw64\bin\make.bat

Esto va a hacer que el comando ``mingw32-make`` sea asociado con el comando mas corto ``make``.

GNU Arm Embedded Toolchain
~~~~~~~~~~~~~~~~~~~~~~~~~~

Lo siguiente es conseguir un compilador de Arm para generar codigo que pueda ser interpretado por el RP2040. Para eso vamos a la pagina de descargas de Arm_ y buscamos la ultima version disponible de ``gcc-arm-none-eabi`` en su version ejecutable. La descargamos y luego la instalamos normalmente.

.. _Arm: https://developer.arm.com/downloads/-/gnu-rm

.. note::

    En el proceso de instalacion, hacer click en las opciones *Launch gccvar.bar*, *Add path to environment variable* y *Add registry information*.

CMake
~~~~~

La siguiente herramienta es la que se encarga de automatizar el proceso de compilacion. Vamos a la pagina de descarga de CMake_ y buscamos el instalador de la version mas reciente. Luego descargamos e instalamos tildando la opcion de agregar CMake al PATH.

.. _CMake: https://cmake.org/download/

Python
~~~~~~

El SDK de la Pico hace uso de Python para automatizar algunas tareas por lo que va a ser necesario que tengamos Python instalado. Vamos a la pagina de descargas de Python_ y descargamos la version mas reciente, luego instalamos, tildando la opcion de agregar al PATH.

.. _Python: https://www.python.org/downloads/

Git
~~~

Como ultima herramienta, necesitamos Git que nos dara control de versiones sobre nuestros proyectos. Descargamos de la pagina de Git_ y luego instalamos normalmente con todas las opciones por defecto.

.. _Git: https://git-scm.com/downloads

Pico SDK
~~~~~~~~

Ya con todas las herramientas instaladas, podemos descargar el SDK de la Raspberry Pi Pico que no es mas que un conjunto de software que nos va a facilitar programar los perifericos del RP2040. Para ello, abrimos una consola de comandos de Windows, noten que se abre en la ruta ``C:\Usuarios\USUARIO``. Luego, escribimos:

.. code::

    git clone -b master https://github.com/raspberrypi/pico-sdk.git
    cd pico-sdk 
    git submodule update --init

Como ultimo paso para poder usar el SDK, tenemos que setear la variable de entorno ``PICO_SDK_PATH`` en nuestro sistema para que apunte a ``C:\Usuarios\USUARIO\pico-sdk``.

.. note::
    El directorio ``USUARIO`` en el comando debe corresponder con el nombre de usuario de la computadora.

Pico Project Generator (opcional)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Con todo esto listo, podemos hacer un ultimo paso que es descargar un creador de proyectos para facilitarnos el inicializar uno cada vez que necesitemos programar la Raspberry Pi Pico. Para eso, abrimos la consola de comandos y escribimos:

.. code::

    git clone https://github.com/raspberrypi/pico-project-generator.git
    echo python C:\Usuarios\USUARIO\pico-project-generator\pico_project.py --gui >> C:\Usuarios\USUARIO\pico-project-generator\run.cmd

Esto nos va a crear un directorio llamado ``pico-project-generator` con un archivo llamado ``run.cmd`` dentro. Al ejecutarlo, va a abrirse una ventana que nos va a permitir configurar un nuevo proyecto para la Raspberry Pi Pico sin dificultades.