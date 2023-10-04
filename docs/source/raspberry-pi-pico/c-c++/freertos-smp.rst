*************************************
FreeRTOS en un doble nucleo simetrico
*************************************

El proposito de esta nota de aplicacion es introducir a la idea de como usar un sistema operativo en tiempo real como FreeRTOS en un microcontrolador que cuenta con dos procesadores.

Se pretende que se tenga cierta nocion de como usar la Raspberry Pi Pico, como crear un proyecto y nociones de FreeRTOS para proceder.

Symmetric Multi-Processing
~~~~~~~~~~~~~~~~~~~~~~~~~~

El termino *Symmetric Multi-Processing* o ``SMP`` para abreviar se refiere a microcontroladores que tienen dos nucleos simetricos. En el caso de la Raspberry Pi Pico, esta cuenta con dos procesadores *Cortex-M0+* que comparten todos los recursos del microcontrolador, incluido el *Systick* que FreeRTOS usa como base de tiempo para el Scheduler.

Afortunadamente, ya hay un port de FreeRTOS que tiene contemplado esto y nuestra tarea es entender como usarlo y que modificar en nuestro *FreeRTOSConfig.h* para que funcione.

Como configurar el uso de ambos procesadores
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Habiendo creado un proyecto nuevo, vamos a ir directamente a nuestro *FreeRTOSConfig.h*. Si venimos usando el mismo de esta `nota de aplicacion`_, deberiamos encontrar una seccion llamada ``SMP port only``.

.. _nota de aplicacion: ./freertos.rst

En esta seccion, las opciones claves son:

.. code:: c

    #define configNUM_CORES     2

Esta le dice a FreeRTOS que puede usar ambos procesadores.

.. code:: c

    #define configRUN_MULTIPLE_PRIORITIES   1

Esta linea nos va a permitir correr en simulataneo pero en distintos procesadores, tareas que tengan prioridades distintas. Si solo queremos que se corran tareas de la misma prioridad de forma simultanea entonces lo cambiaremos a 0.

.. code:: c

    #define configUSE_CORE_AFFINITY     1

Esta macro nos va a permitir hacer uso de ``vTaskCoreAffinitySet()`` que va a habilitarnos a restringir tareas a que solo corran en un procesador.

Codigo de ejemplo
~~~~~~~~~~~~~~~~~

En nuestro *main.c* vamos a copiar lo siguiente:

.. code:: c

    #include <stdio.h>
    #include "pico/stdlib.h"

    #include <FreeRTOS.h>
    #include <task.h>
    #include <semphr.h>


    // Bitshift para procesadores
    #define CORE_0  (1 << 0)
    #define CORE_1  (1 << 1)

    // Mutex para la consola
    SemaphoreHandle_t mutex;

    /**
    * @brief Imprime por consola de forma segura
    * @param str es la cadena de caracteres a imprimir
    */
    void safe_print(char *str) {
        // Tomo el semaforo
        xSemaphoreTake(mutex, portMAX_DELAY);
        // Imprimo por consola
        puts(str);
        // Devuelvo el semaforo
        xSemaphoreGive(mutex);
    }

    /**
    * @brief Tarea que imprime un mensaje 
    * por consola periodicamente
    */
    void task_hello(void *params) {
        // Obtengo el handle de esta tarea
        TaskHandle_t handle = xTaskGetCurrentTaskHandle();
        // Variable para string
        char str[50];
        sprintf(
            str, 
            "%s desde core %d (mask: %x)\n",
            pcTaskGetName(handle), 
            get_core_num(),
            vTaskCoreAffinityGet(handle)
        );

        while(1) {
            // Imprimo mensaje
            safe_print("Hello world!");
            // Imprimo numero de core
            safe_print(str);
            // Bloqueo tarea por un segundo
            vTaskDelay(1000 / portTICK_PERIOD_MS);
        }
    }

    /**
    * @brief Tarea que hace un blink del
    * LED de la Pico periodicamente
    */
    void task_blink(void *params) {
        // Obtengo el handle de esta tarea
        TaskHandle_t handle = xTaskGetCurrentTaskHandle();
        // Variable para string
        char str[50];
        sprintf(
            str, 
            "%s desde core %d (mask: %x)\n",
            pcTaskGetName(handle), 
            get_core_num(),
            vTaskCoreAffinityGet(handle)
        );

        while(1) {
            // Conmuto LED
            gpio_put(25, !gpio_get(25));
            // Imprimo numero de core
            safe_print(str);
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

        // Inicializo mutex
        mutex = xSemaphoreCreateMutex();

        // Creo handles para tareas
        TaskHandle_t task_hello_handle;
        TaskHandle_t task_blink_handle;

        // Creo tareas 

        xTaskCreate(
            task_hello,
            "Hello",
            configMINIMAL_STACK_SIZE,
            NULL,
            tskIDLE_PRIORITY + 1UL,
            &task_hello_handle
        );

        xTaskCreate(
            task_blink,
            "Blink",
            configMINIMAL_STACK_SIZE,
            NULL,
            tskIDLE_PRIORITY + 1UL,
            &task_blink_handle
        );

        // Asigno afinidad de procesadores
        vTaskCoreAffinitySet(task_hello_handle, CORE_0);
        vTaskCoreAffinitySet(task_blink_handle, CORE_1);

        // Inicializo scheduler
        vTaskStartScheduler();

        while(1);
    }

Partiendo desde la funcion principal, no notaremos mucho distinto al ejemplo original de FreeRTOS. Lo nuevo, es el uso de handles para las tareas. Estos handles se usan mas abajo en las funciones:

.. code:: c

    // Asigno afinidad de procesadores
    vTaskCoreAffinitySet(task_hello_handle, CORE_0);
    vTaskCoreAffinitySet(task_blink_handle, CORE_1);

De esta forma, se asigna una tarea a cada procesador de forma independiente. Las macros ``CORE_0`` y ``CORE_1`` no son mas que un bitshift de cero para el procesador cero y un bitshift de uno para el procesador uno. Si no asignaramos afinidad, las tareas podrian correrse en cualquier procesador apenas este se desocupe y la tarea este desbloqueada.

Por otro lado, tendremos dos tareas que corren cada una en un procesador imprimiendo un mensaje que contendra: el nombre de la tarea, el procesador en el que esta corriendo y la mascara de afinidad. Estos mensajes se imprimiran periodicamente en la consola para verificar que cada tarea se corre en un procesador diferente.

Debido a que habra dos tareas compitiendo por el mismo recurso, es decir, el USB para enviar el mensaje por consola, se usa una tarea guardiana llamada *safe_print* que se asegura, mediente un mutex, que solo una tarea a la vez haga uso del USB para evitar colisiones.

Una vez compilado el programa, lo flasheamos a nuestra Raspberry Pi Pico y abrimos la consola con minicom o Teraterm para verificar el resultado, deberiamos ver algo como:

.. code::
                                                                
    Hello world!                                                                    
    Hello desde core 0 (mask: 1)                                                    
                                                                                    
    Blink desde core 1 (mask: 2)                                                    
                                                                                    
    Blink desde core 1 (mask: 2)     

    Hello world!                                                                    
    Hello desde core 0 (mask: 1)                                                    
                                                                                    
    Blink desde core 1 (mask: 2)                                                    
                                                                                    
    Blink desde core 1 (mask: 2)    