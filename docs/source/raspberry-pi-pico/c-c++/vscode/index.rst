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

Python
~~~~~~

Git
~~~

Pico SDK
~~~~~~~~

Pico Project Generator
~~~~~~~~~~~~~~~~~~~~~~