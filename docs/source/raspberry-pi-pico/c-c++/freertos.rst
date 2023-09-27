********************************
FreeRTOS en la Raspberry Pi Pico
********************************

En esta nota de aplicacion vamos a mostrar como usar un sistema operativo en tiempo real (``RTOS``) dinamico en la Raspberry Pi Pico.

Esta nota atiende a los proyectos donde se comience a desarrollar todo desde cero. Para proyectos que usen PlatformIO la estructura de directorios es distinta.

Estructura de proyecto
~~~~~~~~~~~~~~~~~~~~~~

Con un proyecto nuevo creado vamos a crear la siguiente estructura de directorios:

.. code::

    |- freertos
    |--- include
    |- CMakeLists.txt
    |- main.c

Dentro del directorio *freertos* vamos a poner los archivos de codigo fuente y en el directorio *include* todos los headers.

Luego, vamos a ir a la pagina de FreeRTOS_ y descargar la ultima version. Una vez que lo descomprimamos, vamos a copiar los siguientes archivos:

.. _FreeRTOS: https://www.freertos.org/index.html

.. list-table:: 
    :header-rows: 1

    * - Ruta de FreeRTOS 
      - Archivos 
      - Destino en el proyecto 
      
    * - *FreeRTOS/Source*
      - *Todos los .c*
      - *freertos*

    * - *FreeRTOS/Source/include*
      - *Todos los .h*
      - *freertos/include*

    * - *FreeRTOS/Source/portable/GCC/ARM_CM0*
      - *port.c*
      - *freertos*

    * - *FreeRTOS/Source/portable/GCC/ARM_CM0*
      - *portmacro.h*
      - *freertos/include*

    * - *FreeRTOS/Source/portable/MemMang*
      - *heap_4.c*
      - *freertos*