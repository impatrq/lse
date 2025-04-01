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

Luego de la instalación, es necesario agregar al usuario actual al grupo de `dialout` para poder leer la consola. Eso es posible con:

.. code-block:: bash

  sudo adduser USER dialout

Reemplazando `USER` por el nombre de usuario correspondiente.

Luego, hay que agregar una regla para que podamos grabar programas en la Raspberry Pi Pico. Vamos a crear un archivo de reglas con el comando:

.. code-block:: bash

  sudo nano /etc/udev/rules.d/99-pico.rules

En el contenido, vamos a escribir:

.. code-block::

  # /etc/udev/rules.d/99-pico.rules

  # Make an RP2040 in BOOTSEL mode writable by all users, so you can `picotool`
  # without `sudo`. 
  SUBSYSTEM=="usb", ATTRS{idVendor}=="2e8a", ATTRS{idProduct}=="0003", MODE="0666"

  # Symlink an RP2040 running MicroPython from /dev/pico.
  #
  # Then you can `mpr connect $(realpath /dev/pico)`.
  SUBSYSTEM=="tty", ATTRS{idVendor}=="2e8a", ATTRS{idProduct}=="0005", SYMLINK+="pico"

Lo guardamos y luego escribimos:

.. code-block:: bash
  
  sudo udevadm control --reload-rules && sudo udevadm trigger