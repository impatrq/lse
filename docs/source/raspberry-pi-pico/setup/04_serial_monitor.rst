**************
Monitor Serial
**************

Una de las herramientas más útiles a la hora de ver qué puede estar pasando en la aplicación que está corriendo en el microcontrolador es el monitor serial.

Podemos aprovechar en nuestra aplicación la función ``printf()`` para mandar mensajes de información, error o incluso variables por el USB hacia la computadora. Estos mensajes, bien aprovechados, pueden ayudar mucho a determinar qué puede estar pasando en nuestra aplicación cuando no está funcionando como debería.

Ya sea que tengamos un adaptador UART a USB y los mensajes del microcontrolador salgan por UART o que tengamos configurado para que los mensajes salgan directamente por el micro USB que tenemos en la placa de desarrollo, tenemos que tener alguna aplicación en nuestra computadora para poder leer esos mensajes.

Afortunadamente, no necesitamos ninguna aplicación adicional, sino que en el propio VS Code tenemos una extensión llamada `Serial Monitor <https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-serial-monitor>`_ que va a permitirnos acceder a un monitor serial dentro del mismo IDE.

Para abrir un nuevo monitor serial para leer los mensajes de la Raspberry Pi Pico, vamos al menú superior a *Terminal > New Terminal*. Esto va a abrir una vista de terminal en la parte inferior de la aplicación con numerosas pestañas.

Dentro de esas pestañas, vamos a buscar la que tiene el nombre *SERIAL MONITOR*. Esta vista de monitor serial tiene numerosas configuraciones pero vamos a concentrarnos en dos de ellas.

Selección de puerto
*******************

La opción de *Port* es la que se encarga de seleccionar a qué dispositivo queremos conectarnos. Los nombres cambian de sistema operativo en sistema operativo pero, si tenemos la Raspberry Pi Pico conectada, deberíamos ver en la lista desplegable alguna opción que la mencione. Si esto no sucede, los posibles problemas son:

- No tenemos alimentada la Raspberry Pi Pico.
- Estamos usando un cable micro USB que solo tiene alimentación pero no líneas de datos.
- No configuramos correctamente en la aplicación el USB (``stdio_init_all()``).
- La aplicación que está corriendo en el microcontrolador no es la correcta o hay algo que produjo un error en el procesador.

Velocidad de transmisión
************************

La opción de *Baud rate* es la que se encarga de configurar a qué velocidad se va a transmitir la información. Para nuestro caso, 115200 es lo normal.

Una incorrecta velocidad de transmisión puede implicar que a la hora de ver mensajes desde la Raspberry Pi Pico, los caracteres no coincidan con lo que deberíamos ver o incluso aparezcan símbolos raros. Esto se debe a una mala coordinación entre microcontrolador y computadora para mandar y recibir los mensajes.

Establecer conexión
*******************

Para iniciar la conexión, le damos al botón de *Start Monitoring*. Si la configuración es la correcta, deberíamos ver mensajes en la consola, si es que la aplicación del microcontrolador así lo dispone.

Durante el desarrollo, es muy normal que la aplicación se grabe múltiples veces en el microcontrolador con correcciones. Esto implica que hay que repetir el proceso de reconexión cada vez que hay una nueva aplicación corriendo. Para simplificar un poco este proceso, recomendamos habilitar la opción *Toggle Automatic Reconnection* luego del botón que inicia la conexión.