**************************
Raspberry Pi Pico en C/C++
**************************

Si bien el lenguaje C/C++ tiene una curva de aprendizaje algo lenta, nos ofrece mucho control sobre el hardware de los sistemas embebidos, razon por la cual es un lenguaje muy usado en estas plataformas. 

Particularmente en el caso de la Raspberry Pi Pico, esta placa de desarrollo demanda una serie de herramientas para poder codear, compilar y flashear el microcontrolador. En esta documentacion describiremos algunas posibles soluciones para poder programar este microcontrolador sin morir en el intento. Entre ellas tenemos:

- `Visual Studio Code`_
- `PlatformIO`_
- `Wokwi`_

.. _Visual Studio Code: ./vscode.html
.. _PlatformIO: .platformio.html
.. _Wokwi: ./wokwi.html

Una vez instaladas las herramientas, podemos trabajar con este microcontrolador. Abajo dejamos algunas otras notas de aplicacion sobre el uso de esta placa de desarrollo.

- `CMakeLists.txt y como agregar librerias de terceros`_
- `FreeRTOS en la Raspberry Pi Pico`_
- `FreeRTOS con dual core simetrico`_
- `Raspberry Pi Pico baremetal con CMSIS`_

.. _CMakeLists.txt y como agregar librerias de terceros: ./cmakelists.html
.. _FreeRTOS en la Raspberry Pi Pico: ./freertos.html
.. _FreeRTOS con dual core simetrico: ./freertos-smp.html
.. _Raspberry Pi Pico baremetal con CMSIS: ./cmsis.html