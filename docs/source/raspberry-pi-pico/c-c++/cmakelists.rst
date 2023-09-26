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

El foco de esta guia va a estar en las instrucciones ``target_link_libraries``, ``target_include_directories`` y ``add_library``, las dos ultimas siendo que no forman parte de los proyectos que generalmente armamos de cero.

add_library
~~~~~~~~~~~

Esta primer instruccion es la responsable de generar una biblioteca a partir de un codigo fuente. Tomemos la siguiente tipica estructura de proyecto:

.. code::

    |- libs
    |--- foo.c
    |--- bar.c
    |--- inc
    |----- foo.h
    |----- bar.h
    |- main.c
    |- CMakeLists.txt

Tendremos nuestra biblioteca contenida en el directorio *libs* donde hay un directorio adicional con los headers llamado *inc*. Lo que le diremos al compilador con la instruccion *add_library* es que trate a este directorio libs como una biblioteca. La sintaxis sera algo como esto:

.. code::

    add_library(libs
        libs/foo.c
        libs/bar.c
    )

De modo que ahora cada vez que se haga referencia a *libs* en el CMakeLists se estara haciendo referencia a una biblioteca compuesta por *foo.c* y *bar.c*.

.. note::

    Deben escribirse las rutas correctas a cada uno de los archivos fuente, no los headers. Si tuviese multiples librerias, cada una estara en su directorio propio y sera referenciada con su *add_library* correspondiente.

target_include_directories
~~~~~~~~~~~~~~~~~~~~~~~~~~

Esta instruccion en particular es la que le dice al compulador donde ir a buscar los headers de la biblioteca correspondiente. Volviendo al caso anterior de la biblioteca *libs* tendriamos:

.. code::

    target_include_directories(libs PUBLIC
        libs/inc
    )

En este caso, decimos que la biblioteca *libs* tiene dependencia con los headers que se incluyen en el directorio *libs/inc*.

target_link_libraries
~~~~~~~~~~~~~~~~~~~~~

Esta ultima instruccion es la que se encarga de relacionar y declarar las dependencias entre los distintos archivos fuente y bibliotecas.

Suponiendo que yo hubiera generado una biblioteca *libs* que depende en el SDK del ADC de la Raspberry Pi Pico, tengo que declarar esa dependencia. Para ello, escribimos:

.. code::

    target_link_libraries(libs
        hardware_adc
    )

De esta forma, se declara que mi biblioteca *libs* tiene una dependencia con la biblioteca del ADC del SDK.

Finalmente, una vez que todas las dependencias de las bibliotecas estan descritas, debemos describir la relacion entre nuestro programa principal y las bibliotecas. Para ello y siguiendo con el ejemplo original, describiriamos casi al final del CMakeLists:

.. code::

    target_link_libraries(${PROJECT_NAME}
        pico_stdlib
        libs
    )

Con esta instruccion, decimos que el archivo de nuestro programa principal depende de *pico_stdlib* como originalmente lo hacia y ahora, de la biblioteca *libs*.

.. note::

    Cada vez que se produzcan cambios en el CMakeLists.txt es necesario volver a correr ``cmake ..`` dentro del directorio ``build`` para actualizar todas las dependencias. Luego de eso, podemos volver a correr ``make`` para compilar.
