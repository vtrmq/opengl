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

Estas líneas de código se utilizan al inicio de un programa en C o C++ para configurar y preparar el entorno gráfico utilizando la librería GLFW junto con OpenGL.

## Desglose línea por línea
- glfwInit();
  - **Qué hace:** Inicializa la librería GLFW.
  - **Por qué es importante:** Es el paso obligatorio antes de llamar a casi cualquier otra función de GLFW. Se encarga de configurar los sistemas internos de la librería (como el manejo de hilos, entradas del teclado/ratón, etc.). Si falla, devolverá un error.
- glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 3); y glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 3);
  - **Qué hacen:** Indican a GLFW qué versión de OpenGL queremos usar. En este caso, la versión 3.3.
  - **Por qué son importantes:** Por defecto, las computadoras pueden abrir contextos de OpenGL muy antiguos (como la versión 1.1). Estas líneas le avisan al sistema operativo y a la tarjeta gráfica que requerimos específicamente las características de OpenGL 3.3.
- glfwWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_CORE_PROFILE);
  - **Qué hace:** Selecciona el perfil de OpenGL que deseamos, en este caso, el Core Profile (Perfil Principal).
  - **Por qué es importante:** OpenGL tiene dos perfiles principales: el Compatibility Profile (que incluye funciones antiguas y obsoletas del pipeline fijo) y el Core Profile (que elimina todo lo antiguo y se enfoca exclusivamente en el desarrollo moderno basado en shaders, VBOs y VAOs). Usar el Core Profile es el estándar actual para aprender y desarrollar gráficos modernos.

> Nota: Estas funciones de glfwWindowHint deben colocarse siempre antes de crear la ventana real con glfwCreateWindow, ya que actúan como una configuración previa que GLFW lee al momento de generar el contexto gráfico.

Para manejar un fallo en la inicialización, debes evaluar el valor que devuelve la función glfwInit(). Esta función retorna GLFW_TRUE (o un valor distinto de cero) si tiene éxito, y GLFW_FALSE (0) si ocurre un error.

Aquí tienes el código típico en C++ para comprobarlo:

```cpp
#include <iostream>
#include <GLFW/glfw3.h>

int main() {
    // Intentamos inicializar GLFW y verificamos si falla
    if (!glfwInit()) {
        std::cerr << "Fallo al inicializar GLFW" << std::endl;
        return -1; // Salimos del programa indicando un error
    }

    // El resto de tu código de configuración y bucle principal...

    glfwTerminate(); // No olvides limpiar recursos al terminar
    return 0;
}
```

## Detalles útiles:
- glfwTerminate(): Aunque el programa esté fallando y salgas con return -1, es una buena práctica asegurarte de liberar cualquier recurso residual si ya se había inicializado parcialmente algo complejo (aunque si glfwInit falla por completo, por lo general no es estrictamente necesario, pero sí lo es al cerrar el programa con éxito).
- glfwGetError(const char** description): Si necesitas saber exactamente por qué falló glfwInit(), puedes usar esta función antes de que el programa termine para capturar el mensaje de error de GLFW.
