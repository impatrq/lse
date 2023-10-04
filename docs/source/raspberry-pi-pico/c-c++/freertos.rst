********************************
FreeRTOS en la Raspberry Pi Pico
********************************

En esta nota de aplicacion vamos a mostrar como usar un sistema operativo en tiempo real (``RTOS``) dinamico en la Raspberry Pi Pico clonando el kernel directamente desde GitHub.

Esta nota atiende a los proyectos donde se comience a desarrollar todo desde cero. Para proyectos que usen PlatformIO la estructura de directorios es distinta. Para los que esten familiarizados con esa extension, una version distinta de esta nota puede hallarse aqui_ en la que el kernel del sistema operativo se importa manualmente, permitiendo traerlo a PlatformIO.

.. _aqui: ./freertos-v1.html

Obtener el kernel de FreeRTOS
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Para obtener el kernel de FreeRTOS, debemos buscar el que corresponda al RP2040 que tiene nuestra Raspberry Pi Pico. Para eso, abrimos una terminal en donde prefiramos y escribimos el comando:

.. code:: bash

    git clone -b smp https://github.com/FreeRTOS/FreeRTOS-Kernel --recurse-submodules

Esto va a traernos la rama ``smp`` del kernel de FreeRTOS que es la que funciona con microcontroladores con procesadores simetricos o *Simmetric Multi-Processing*.

Luego, debemos agregar la ruta al kernel para nuestro sistema. El metodo varia de acuerdo a nuestro sistema operativo. Llamaremos a la variable de entorno ``PICO_FREERTOS_KERNEL_PATH``.

Estructura de proyecto
~~~~~~~~~~~~~~~~~~~~~~

Con un proyecto nuevo creado vamos a crear la siguiente estructura:

.. code::

    |- CMakeLists.txt
    |- FreeRTOSConfig.h
    |- main.c

Tendremos que modificar el archivo ``CMakeLists.txt`` para que referencia al kernel en la ruta que lo descargamos y al ``FreeRTOSConfig.h`` para configurar como se comportara nuestro sistema operativo en nuestro microcontrolador.

FreeRTOSConfig.h
~~~~~~~~~~~~~~~~

Cada microcontrolador que porte un sistema operativo en tiempo real debe tener un archivo de configuracion particular que describa ciertas implementaciones del sistema operativo. Para nuestro caso, vamos a tomar el siguiente ejemplo y copiarlo en nuestro *FreeRTOSConfig.h*:

.. code:: c

    #ifndef FREERTOS_CONFIG_H
    #define FREERTOS_CONFIG_H

    /* Scheduler Related */
    #define configUSE_PREEMPTION                    1
    #define configUSE_TICKLESS_IDLE                 0
    #define configUSE_IDLE_HOOK                     0
    #define configUSE_TICK_HOOK                     0
    #define configTICK_RATE_HZ                      ( ( TickType_t ) 1000 )
    #define configMAX_PRIORITIES                    32
    #define configMINIMAL_STACK_SIZE                ( configSTACK_DEPTH_TYPE ) 256
    #define configUSE_16_BIT_TICKS                  0

    #define configIDLE_SHOULD_YIELD                 1

    /* Synchronization Related */
    #define configUSE_MUTEXES                       1
    #define configUSE_RECURSIVE_MUTEXES             1
    #define configUSE_APPLICATION_TASK_TAG          0
    #define configUSE_COUNTING_SEMAPHORES           1
    #define configQUEUE_REGISTRY_SIZE               8
    #define configUSE_QUEUE_SETS                    1
    #define configUSE_TIME_SLICING                  1
    #define configUSE_NEWLIB_REENTRANT              0
    #define configENABLE_BACKWARD_COMPATIBILITY     0
    #define configNUM_THREAD_LOCAL_STORAGE_POINTERS 5

    /* System */
    #define configSTACK_DEPTH_TYPE                  uint32_t
    #define configMESSAGE_BUFFER_LENGTH_TYPE        size_t

    /* Memory allocation related definitions. */
    #define configSUPPORT_STATIC_ALLOCATION         0
    #define configSUPPORT_DYNAMIC_ALLOCATION        1
    #define configTOTAL_HEAP_SIZE                   (128*1024)
    #define configAPPLICATION_ALLOCATED_HEAP        0

    /* Hook function related definitions. */
    #define configCHECK_FOR_STACK_OVERFLOW          0
    #define configUSE_MALLOC_FAILED_HOOK            0
    #define configUSE_DAEMON_TASK_STARTUP_HOOK      0

    /* Run time and task stats gathering related definitions. */
    #define configGENERATE_RUN_TIME_STATS           0
    #define configUSE_TRACE_FACILITY                1
    #define configUSE_STATS_FORMATTING_FUNCTIONS    0

    /* Co-routine related definitions. */
    #define configUSE_CO_ROUTINES                   0
    #define configMAX_CO_ROUTINE_PRIORITIES         1

    /* Software timer related definitions. */
    #define configUSE_TIMERS                        1
    #define configTIMER_TASK_PRIORITY               ( configMAX_PRIORITIES - 1 )
    #define configTIMER_QUEUE_LENGTH                10
    #define configTIMER_TASK_STACK_DEPTH            1024

    /* Interrupt nesting behaviour configuration. */
    /*
    #define configKERNEL_INTERRUPT_PRIORITY         [dependent of processor]
    #define configMAX_SYSCALL_INTERRUPT_PRIORITY    [dependent on processor and application]
    #define configMAX_API_CALL_INTERRUPT_PRIORITY   [dependent on processor and application]
    */

    /* SMP port only */
    #define configNUM_CORES                         2
    #define configTICK_CORE                         0
    #define configRUN_MULTIPLE_PRIORITIES           1
    #define configUSE_CORE_AFFINITY                 1

    /* RP2040 specific */
    #define configSUPPORT_PICO_SYNC_INTEROP         1
    #define configSUPPORT_PICO_TIME_INTEROP         1

    #include <assert.h>
    /* Define to trap errors during development. */
    #define configASSERT(x)                         assert(x)

    /* Set the following definitions to 1 to include the API function, or zero
    to exclude the API function. */
    #define INCLUDE_vTaskPrioritySet                1
    #define INCLUDE_uxTaskPriorityGet               1
    #define INCLUDE_vTaskDelete                     1
    #define INCLUDE_vTaskSuspend                    1
    #define INCLUDE_vTaskDelayUntil                 1
    #define INCLUDE_vTaskDelay                      1
    #define INCLUDE_xTaskGetSchedulerState          1
    #define INCLUDE_xTaskGetCurrentTaskHandle       1
    #define INCLUDE_uxTaskGetStackHighWaterMark     1
    #define INCLUDE_xTaskGetIdleTaskHandle          1
    #define INCLUDE_eTaskGetState                   1
    #define INCLUDE_xTimerPendFunctionCall          1
    #define INCLUDE_xTaskAbortDelay                 1
    #define INCLUDE_xTaskGetHandle                  1
    #define INCLUDE_xTaskResumeFromISR              1
    #define INCLUDE_xQueueGetMutexHolder            1

    /* A header file that defines trace macro can be included here. */

    #endif /* FREERTOS_CONFIG_H */

Se recomienda no modificar este archivo a menos que sea absolutamente necesario para alguna aplicacion en particular y se tenga conocimiento de la macro que se esta cambiando.

CMakeLists.txt
~~~~~~~~~~~~~~

Al haber traido el kernel de FreeRTOS de GitHub y guardado la ruta, podemos directamente referenciarlo en nuestro *CMakeLists.txt*. En nuestro archivo, copiamos lo siguiente:

.. code:: cmake

    cmake_minimum_required(VERSION 3.13)

    # Pull in SDK (must be before project)
    include($ENV{PICO_SDK_PATH}/external/pico_sdk_import.cmake)

    # Pull in FreeRTOS
    include($ENV{PICO_FREERTOS_KERNEL_PATH}/portable/ThirdParty/GCC/RP2040/FreeRTOS_Kernel_import.cmake)

    project(app C CXX ASM)
    set(CMAKE_C_STANDARD 11)
    set(CMAKE_CXX_STANDARD 17)

    # Initialize the SDK
    pico_sdk_init()

    add_executable(${PROJECT_NAME} main.c)

    # Enable/disable stdio output
    pico_enable_stdio_usb(${PROJECT_NAME} 1)
    pico_enable_stdio_uart(${PROJECT_NAME} 0)

    target_include_directories(${PROJECT_NAME} PRIVATE ${CMAKE_CURRENT_LIST_DIR})
    
    # pull in common dependencies
    target_link_libraries(${PROJECT_NAME} pico_stdlib FreeRTOS-Kernel FreeRTOS-Kernel-Heap4)

    # create map/bin/hex/uf2 file etc.
    pico_add_extra_outputs(${PROJECT_NAME})

No se pretende en este apartado describir los pormenores de este archivo, para entenderlo un poco mejor puede referirse a esta nota_.

.. _nota: ./cmakelists.html

Programa de ejemplo
~~~~~~~~~~~~~~~~~~~

Finalmente, para probar que el sistema operativo funciona, podemos usar este codigo de ejemplo en nuestro ``main.c``. Cada un segundo se va a ejecutar una tarea que imprime un mensaje por consola:

.. code:: c

    #include <stdio.h>
    #include "pico/stdlib.h"

    #include <FreeRTOS.h>
    #include <task.h>

    /**
    * @brief Tarea que imprime un mensaje 
    * por consola periodicamente
    */
    void task_hello(void *params) {

        while(1) {
            // Imprimo mensaje
            puts("Hello world!");
            // Bloqueo tarea por un segundo
            vTaskDelay(1000 / portTICK_PERIOD_MS);
        }
    }

    /**
    * @brief Tarea que hace un blink del
    * LED de la Pico periodicamente
    */
    void task_blink(void *params) {

        while(1) {
            // Conmuto LED
            gpio_put(25, !gpio_get(25));
            // Bloqueo tarea por medio segundo
            vTaskDelay(500 / portTICK_PERIOD_MS);
        }
    }

    /**
    * @brief Programa principal
    */
    int main(void) {
        // Inicializacion de stdio
        stdio_init_all();

        // Habilito GPIO como salida
        gpio_init(25);
        gpio_set_dir(25, true);

        // Creo tareas 

        xTaskCreate(
            task_hello,
            "Hello",
            configMINIMAL_STACK_SIZE,
            NULL,
            tskIDLE_PRIORITY + 1UL,
            NULL
        );

        xTaskCreate(
            task_blink,
            "Blink",
            configMINIMAL_STACK_SIZE,
            NULL,
            tskIDLE_PRIORITY + 1UL,
            NULL
        );

        // Inicializo scheduler
        vTaskStartScheduler();

        while(1);
    }

El LED lo veremos parpadear a una frecuencia de 1 Hz, en el caso del mensaje, necesitaremos algun programa que nos permita ver el mensaje que la Raspberry Pi Pico esta enviando por USB. 

Para computadoras con Windows se debe usar algun programa como Teraterm_ y conectar al ``COM`` adecuado. 

.. _Teraterm: https://ttssh2.osdn.jp/index.html.en

Para sistemas operativos con Linux o MacOS se puede usar ``minicom`` con un comando como el siguiente:

.. code::

    sudo minicom -b 115200 -o -D /dev/PORT

Donde ``PORT`` debe coincidir con el de cada sistema operativo. En Linux suele ser algo como ``/dev/ttyACM0`` mientras que en MacOS suele ser ``/dev/tty.usbmodem`` seguido de algun numero.

Se puede verificar el puerto correcto en Linux y MacOS con el comando:

.. code::

    ls /dev/tty*