***************************************************
CMakeLists.txt y como agregar librerias de terceros
***************************************************

A medida que comenzamos a trabajar en un proyecto y este comienza a volverse mas complejo, probablemente tendremos que recurrir a generar biblitecas propias o buscar algunas desarrolladas por otros para facilitarnos el trabajo. Estas pueden incluir bibliotecas particulares para nuestra aplicacion, para comunicarnos con sensores o incluso otros microcontroladores.

El proposito de esta nota de aplicacion es entender a grandes rasgos como funciona el archivo ``CMakeLists.txt`` que normalmente encontraremos en nuestros proyectos y como podemos modificarlo para usar bibliotecas nuevas.

.. note::
    Esta nota es valida para aquellos proyectos que se generen desde cero o con el Pico Proyect Generator. El proceso para incluir bibliotecas en PlatformIO es distinto.

CMakeLists.txt
~~~~~~~~~~~~~~

Este archivo cumple el rol de automatizar un poco el proceso de compilacion. Cuando escribimos un programa en C/C++, normalmente debe haber un proceso de compilacion que junta todo nuestro codigo que tenemos en distintos archivos fuente o source (``.c`` / ``.cpp``) y lo traduce a binario para poder flashear nuestro microcontrolador.

En el proceso de compilacion, se deben relacionar los distintos headers y sources para producir un unico archivo final. La relacion entre los distintos archivos y sus dependencias es lo que tenemos declarado en el ``CMakeLists.txt``.

Normalmente este archivo se va a encontrar en el raiz de nuestro proyecto junto con nuestro ``main.c`` y va a verse parecido a esto:

.. code::

    cmake_minimum_required(VERSION 3.13)

    include($ENV{PICO_SDK_PATH}/external/pico_sdk_import.cmake)

    project(main C CXX ASM)

    set(CMAKE_C_STANDARD 11)
    set(CMAKE_CXX_STANDARD 17)

    if (PICO_SDK_VERSION_STRING VERSION_LESS "1.3.0")
        message(FATAL_ERROR "Raspberry Pi Pico SDK version 1.3.0 (or later) required. Your version is ${PICO_SDK_VERSION_STRING}")
    endif()

    pico_sdk_init()

    add_executable(${PROJECT_NAME}
        main.c
    )

    pico_enable_stdio_usb(${PROJECT_NAME} 1)
    pico_enable_stdio_uart(${PROJECT_NAME} 0)

    target_link_libraries(${PROJECT_NAME} 
        pico_stdlib
    )

    pico_add_extra_outputs(${PROJECT_NAME})

