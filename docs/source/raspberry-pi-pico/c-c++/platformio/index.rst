*********************************
Platformio para Raspberry Pi Pico
*********************************

PlatformIO es una extension muy popular de Visual Studio Code para programar una variedad de microcontroladores en el mismo IDE.

Teniendo Visual Studio Code y PlatformIO instalado previamente, debemos hacer algunos cambios a un proyecto que generemos para poder usar el SDK de la Raspberry Pi Pico.

Con la extension de PlatformIO, creamos un proyecto para la placa ``Raspberry Pi Pico`` con el framework de ``Arduino`` que es el unico que esta extension ofrece por defecto.

Una vez que tengamos el proyecto creado, debemos hacer los siguientes cambios al archivo ``platformio.ini``:

.. code::

    [env:raspberry-pi-pico]
    platform = https://github.com/maxgerhardt/wizio-pico.git
    board = raspberry-pi-pico
    framework = baremetal
    build_flags =
        -D LIB_PICO_STDIO_USB ; for printf() via USB serial, not UART
        -D PICO_USB ; activate tinyusb

Esto va a descargar un repositorio de un desarrollador en GitHub llamado *Wiz-IO* que luego el usuario *maxgerhardt* ajusto para que podamos usar el SDK de la Raspberry Pi Pico en PlatformIO.

Con esos cambios, ya podemos modificar el ``main.cpp`` de acuerdo a lo que necesitemos en nuestro proyecto, compilar y flashear a gusto.