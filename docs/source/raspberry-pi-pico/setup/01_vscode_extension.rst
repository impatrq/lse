***************************
Visual Studio Code
***************************

Visual Studio Code es uno de los IDEs mas populares y flexibles hoy en dia. Lo usaremos como entorno para desarrollar nuestros programas para la Raspberry Pi Pico. Desde 2024, existe una extensión oficial de Raspberry Pi que descarga y configura todas las herramientas necesarias para poder trabajar con este microcontrolador.

Es posible descargar este IDE desde este `link <https://code.visualstudio.com/>`_. Una vez instalado, abrimos el IDE y buscamos en el menú de extensiones y buscamos la extensión *Raspberry Pi Pico*. Pueden ver la página de la extensión en su `página oficial <https://marketplace.visualstudio.com/items?itemName=raspberry-pi.raspberry-pi-pico>`_.

.. attention:: 
  Depende del sistema operativo donde se vaya a trabajar, esta extensión tiene dependencias que son necesarias instalar previamente. Vean las secciones de abajo para ver las dependencias de cada sistema operativo.

Windows y Raspberry Pi OS
+++++++++++++++++++++++++

Ya sea que se instale en una computadora con Windows (Windows 10 o superior) o en una Raspberry Pi, la extensión no tiene dependencias previas.

macOS
+++++

Para usuarios de macOS, es necesario instalar Xcode con el siguiente comando:

.. code-block:: bash

  xcode-select --install

Esto instala las dependencias:

- Git
- Tar
- Compilador de C y C++ nativo

Linux
+++++

Si bien muchas distribuciones de Linux tienen ya la mayoría de las dependencias instaladas, puede ser necesario instalar algunas. En distribuciones tipo Ubuntu y Debian, ejecutamos en la terminal:

.. code-block:: bash

  sudo apt install python3 git tar build-essential