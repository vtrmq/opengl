Veamos si podemos poner en marcha GLFW. Primero, crea un archivo .cpp y añade las siguientes inclusiones al principio del archivo recién creado.
```cpp
#include <glad/glad.h>
#include <GLFW/glfw3.h>
```
> Asegúrese de incluir GLAD antes que GLFW. El archivo de inclusión de GLAD incluye los encabezados de OpenGL necesarios internamente (como GL/gl.h), así que asegúrese de incluir GLAD antes que otros archivos de encabezado que requieran OpenGL (como GLFW).
