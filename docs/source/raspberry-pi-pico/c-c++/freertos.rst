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

FreeRTOSConfig.h
~~~~~~~~~~~~~~~~

Cada microcontrolador que porte un sistema operativo en tiempo real debe tener un archivo de configuracion particular que describa ciertas implementaciones del sistema operativo. Para nuestro caso, vamos a tomar el siguiente ejemplo y copiarlo en un nuevo archivo llamado ``FreeRTOSConfig.h`` en el directorio *freertos/include* de nuestro proyecto.

.. code:: C

    #ifndef FREERTOS_CONFIG_H
    #define FREERTOS_CONFIG_H

    /* Use Pico SDK ISR handlers */
    #define vPortSVCHandler         isr_svcall
    #define xPortPendSVHandler      isr_pendsv
    #define xPortSysTickHandler     isr_systick

    #define configUSE_PREEMPTION                    1
    #define configUSE_PORT_OPTIMISED_TASK_SELECTION 0
    #define configUSE_TICKLESS_IDLE                 0
    #define configCPU_CLOCK_HZ                      125000000
    #define configTICK_RATE_HZ                      1000
    #define configMAX_PRIORITIES                    5
    #define configMINIMAL_STACK_SIZE                128
    #define configMAX_TASK_NAME_LEN                 16
    #define configUSE_16_BIT_TICKS                  0
    #define configIDLE_SHOULD_YIELD                 1
    #define configUSE_TASK_NOTIFICATIONS            1
    #define configTASK_NOTIFICATION_ARRAY_ENTRIES   3
    #define configUSE_MUTEXES                       0
    #define configUSE_RECURSIVE_MUTEXES             0
    #define configUSE_COUNTING_SEMAPHORES           0
    #define configQUEUE_REGISTRY_SIZE               10
    #define configUSE_QUEUE_SETS                    0
    #define configUSE_TIME_SLICING                  0
    #define configUSE_NEWLIB_REENTRANT              0
    #define configENABLE_BACKWARD_COMPATIBILITY     0
    #define configNUM_THREAD_LOCAL_STORAGE_POINTERS 5
    #define configSTACK_DEPTH_TYPE                  uint16_t
    #define configMESSAGE_BUFFER_LENGTH_TYPE        size_t

    /* Memory allocation related definitions. */
    #define configSUPPORT_STATIC_ALLOCATION         0
    #define configSUPPORT_DYNAMIC_ALLOCATION        1
    #define configAPPLICATION_ALLOCATED_HEAP        1

    /* Hook function related definitions. */
    #define configUSE_IDLE_HOOK                     0
    #define configUSE_TICK_HOOK                     0
    #define configCHECK_FOR_STACK_OVERFLOW          0
    #define configUSE_MALLOC_FAILED_HOOK            0
    #define configUSE_DAEMON_TASK_STARTUP_HOOK      0

    /* Run time and task stats gathering related definitions. */
    #define configGENERATE_RUN_TIME_STATS           0
    #define configUSE_TRACE_FACILITY                0
    #define configUSE_STATS_FORMATTING_FUNCTIONS    0

    /* Co-routine related definitions. */
    #define configUSE_CO_ROUTINES                   0
    #define configMAX_CO_ROUTINE_PRIORITIES         1

    /* Software timer related definitions. */
    #define configUSE_TIMERS                        1
    #define configTIMER_TASK_PRIORITY               3
    #define configTIMER_QUEUE_LENGTH                10
    #define configTIMER_TASK_STACK_DEPTH            configMINIMAL_STACK_SIZE

    /* Define to trap errors during development. */
    #define configASSERT( x )

    /* Optional functions - most linkers will remove unused functions anyway. */
    #define INCLUDE_vTaskPrioritySet                1
    #define INCLUDE_uxTaskPriorityGet               1
    #define INCLUDE_vTaskDelete                     1
    #define INCLUDE_vTaskSuspend                    1
    #define INCLUDE_xResumeFromISR                  1
    #define INCLUDE_vTaskDelayUntil                 1
    #define INCLUDE_vTaskDelay                      1
    #define INCLUDE_xTaskGetSchedulerState          1
    #define INCLUDE_xTaskGetCurrentTaskHandle       1
    #define INCLUDE_uxTaskGetStackHighWaterMark     0
    #define INCLUDE_xTaskGetIdleTaskHandle          0
    #define INCLUDE_eTaskGetState                   0
    #define INCLUDE_xEventGroupSetBitFromISR        1
    #define INCLUDE_xTimerPendFunctionCall          0
    #define INCLUDE_xTaskAbortDelay                 0
    #define INCLUDE_xTaskGetHandle                  0
    #define INCLUDE_xTaskResumeFromISR              1

    /* A header file that defines trace macro can be included here. */

    #endif /* FREERTOS_CONFIG_H */

Se recomienda no modificar este archivo a menos que sea absolutamente necesario para alguna aplicacion en particular y se tenga conocimiento de la macro que se esta cambiando.