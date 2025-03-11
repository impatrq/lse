*****************
Raspberry Pi Pico
*****************

La Raspberry Pi Pico es una placa de desarrollo basada en el microcontrolador RP2040_ de Raspberry Pi Foundation.

.. _RP2040: https://www.raspberrypi.com/documentation/microcontrollers/rp2040.html

Algunas caracteristicas de este microcontrolador son:

- Doble nucleo ARM Cortex-M0+ @ 125 MHz
- 264 KB de SRAM
- 30 GPIOs de los cuales 4 son analogicos
- 2 UARTs
- 2 SPIs
- 2 I2Cs
- 16 canales de PWM
- USB 1.1
- 8 maquinas de estado para los PIO

La placa de desarrollo con la que trabajaremos tiene ademas 2 MB de flash para nuestros programas y solo 26 de los 30 GPIOs disponibles con 3 de los 4 analogicos.

Este microcontrolador es posible de programar en varios lenguajes, entre ellos ``C/C++``, ``Micropython`` y, por su puesto, ``Assembler``.

.. toctree:: 
    :maxdepth: 2
    :caption: Setup

    setup/01_vscode_extension
    setup/02_new_project

.. toctree:: 
  :maxdepth: 2
  :hidden:

  c-c++/cmakelists
  c-c++/cmsis
  c-c++/platformio
  c-c++/freertos-v1
  c-c++/freertos
  c-c++/vscode
  c-c++/index