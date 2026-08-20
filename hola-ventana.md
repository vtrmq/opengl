Veamos si podemos poner en marcha GLFW. Primero, crea un archivo .cpp y añade las siguientes inclusiones al principio del archivo recién creado.
```cpp
#include <glad/glad.h>
#include <GLFW/glfw3.h>
```
> Asegúrese de incluir GLAD antes que GLFW. El archivo de inclusión de GLAD incluye los encabezados de OpenGL necesarios internamente (como GL/gl.h), así que asegúrese de incluir GLAD antes que otros archivos de encabezado que requieran OpenGL (como GLFW).

A continuación, creamos la función principal donde instanciaremos la ventana GLFW:
```cpp
int main() {
  glfwInit();
  glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 3);
  glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 3);
  glfwWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_CORE_PROFILE);
  //glfwWindowHint(GLFW_OPENGL_FORWARD_COMPAT, GL_TRUE);
  return 0;
}
```
