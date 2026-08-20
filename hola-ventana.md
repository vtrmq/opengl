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

Para usar glfwGetError, le pasas la dirección de un puntero a const char*. La función se encarga de rellenar ese puntero con un mensaje de texto legible que describe el último error ocurrido en el hilo actual.

Aquí tienes un ejemplo práctico de cómo implementarlo cuando falla glfwInit():

```cpp
#include <iostream>
#include <GLFW/glfw3.h>

int main() {
    // Declaramos un puntero para almacenar la descripción del error
    const char* description = nullptr;

    // Intentamos inicializar GLFW
    if (!glfwInit()) {
        // Obtenemos el código de error y el mensaje descriptivo
        int errorCode = glfwGetError(&description);
        
        std::cerr << "Fallo al inicializar GLFW." << std::endl;
        std::cerr << "Codigo de error: " << errorCode << std::endl;
        
        if (description) {
            std::cerr << "Descripcion: " << description << std::endl;
        }
        
        return -1;
    }

    // Código restante...

    glfwTerminate();
    return 0;
}
```

## Puntos clave a tener en cuenta:
- **No liberes la memoria:** La cadena devuelta en description es administrada internamente por GLFW. No debes usar delete ni free en ella.
- **Tiempo de vida breve:** El texto solo es válido hasta que ocurra el siguiente error de GLFW o hasta que se llame a glfwTerminate(). Por eso es mejor imprimirlo o guardarlo inmediatamente.


En la función principal, primero inicializamos GLFW con glfwInit, tras lo cual podemos configurarlo usando glfwWindowHint. El primer argumento de glfwWindowHint nos indica qué opción queremos configurar, pudiendo seleccionarla de una larga enumeración de opciones posibles con el prefijo GLFW_. El segundo argumento es un número entero que establece el valor de nuestra opción. Puede encontrar una lista de todas las opciones posibles y sus valores correspondientes en la documentación de ventanas de GLFW¹. Si intenta ejecutar la aplicación ahora y aparecen muchos errores de referencia indefinida, significa que no ha enlazado correctamente la biblioteca GLFW.

Dado que este libro se centra en OpenGL versión 3.3, indicaremos a GLFW que esta es la versión que queremos usar. De esta forma, GLFW podrá realizar los ajustes necesarios al crear el contexto de OpenGL. Esto garantiza que, si un usuario no tiene la versión de OpenGL adecuada, GLFW no se ejecutará. Establecemos la versión principal y secundaria en 3. También indicamos a GLFW que queremos usar explícitamente el perfil core. Al indicarle a GLFW que queremos usar el perfil core, tendremos acceso a un subconjunto más reducido de funciones de OpenGL, sin las funciones retrocompatibles que ya no necesitamos. Tenga en cuenta que en Mac OS X debe agregar glfwWindowHint(GLFW_OPENGL_FORWARD_COMPAT, GL_TRUE) a su código de inicialización para que funcione.

> Asegúrate de tener instalada la versión 3.3 o superior de OpenGL en tu sistema/hardware; de ​​lo contrario, la aplicación fallará o mostrará un comportamiento errático. Para averiguar la versión de OpenGL en tu equipo, ejecuta `glxinfo` en Linux o utiliza una utilidad como OpenGL Extension Viewer para Windows. Si la versión compatible es inferior, comprueba si tu tarjeta gráfica es compatible con OpenGL 3.3 o superior (de lo contrario, es muy antigua) y/o actualiza tus controladores.

Para empezar con LearnOpenGL, necesitas verificar y preparar tres cosas principales en tu sistema:

1. Un compilador de C++ (como g++ o clang).
2. Make / CMake (para compilar los proyectos).
3. Las librerías de desarrollo que pide OpenGL (GLFW, GLAD, y librerías de soporte gráfico para Linux como X11/Wayland y OpenGL headers).

Vamos a revisar paso a paso cómo comprobar qué tienes instalado ejecutando comandos en tu terminal.

## Paso 1: Verificar el Compilador de C++ (g++) y CMake
Abre tu terminal (Ctrl + Alt + T) y escribe los siguientes comandos para ver si ya los tienes instalados y qué versión tienes:

```bash
g++ --version
cmake --version
```
- **Qué debes ver:** Si te devuelve un número de versión (por ejemplo, g++ (Ubuntu ...) 11.x o similar), ¡vas por buen camino!
- **Si no lo tienes:** No te preocupes, si te sale un error de que no se encuentra el comando, puedes instalar ambos ejecutando:

```bash
sudo apt update
sudo apt install build-essential cmake
```

## Paso 2: Verificar las herramientas gráficas y librerías del sistema en Linux
En Ubuntu, OpenGL viene soportado por los drivers de tu tarjeta gráfica (NVIDIA, AMD o Intel), pero para compilar proyectos gráficos necesitamos asegurarnos de tener los paquetes de desarrollo de OpenGL y X11/Wayland.

Ejecuta este comando para ver si tienes las librerías de desarrollo de OpenGL instaladas:

```bash
dpkg -l | grep -E "libgl1-mesa-dev|libglu1-mesa-dev"
```

- **Qué debes ver:** Si la terminal te devuelve líneas que dicen ii  libgl1-mesa-dev, significa que ya están instaladas.
- **Si no tienes nada o sale vacío**, instálalas con este comando (son esenciales para que tu código pueda comunicarse con la tarjeta gráfica en Linux):

```bash
sudo apt install libgl1-mesa-dev libglu1-mesa-dev
```

## Paso 3: ¿Qué pasa con GLFW y GLAD? (Las librerías del libro)
LearnOpenGL utiliza dos librerías específicas que raras veces vienen preinstaladas en Ubuntu, ya que se descargan o configuran para cada proyecto:

1. GLFW: Una librería para crear la ventana, el contexto de OpenGL y capturar el teclado/mouse.
2. GLAD: Un cargador de funciones de OpenGL (necesario porque las funciones de OpenGL cambian según la tarjeta gráfica y GLAD se encarga de cargarlas en tiempo de ejecución).

Puedes instalar GLFW directamente desde el gestor de paquetes de Ubuntu para que esté disponible globalmente:

```bash
sudo apt install libglfw3-dev libglfw3
```

> (Nota: GLAD no se instala con apt porque se genera a medida desde una web oficial según la versión de OpenGL que quieras usar, pero el libro te enseña paso a paso cómo integrarlo en tu primer proyecto).

## Verificar GLFW
Antes de pasar al código, nos falta verificar si tienes instalada la librería GLFW (la que se encarga de crear la ventana y escuchar el teclado y el ratón).

Ejecuta este comando en tu terminal para comprobarlo:

```bash
dpkg -l | grep libglfw3-dev
```

- **Si te aparece con ii al inicio:** ¡Ya la tienes instalada!
- **Si no sale nada o está vacío:** Instálala rápidamente ejecutando:

```bash
sudo apt install libglfw3-dev
```

## GLAD
Como habrás visto en el libro, OpenGL necesita una librería llamada GLAD para cargar las funciones de la tarjeta gráfica.

## ¿Qué es GLAD y por qué se necesita?
OpenGL no es una biblioteca de código que descargas y vinculas (.lib o .so), sino una especificación creada por el Khronos Group. Los fabricantes de tarjetas gráficas (NVIDIA, AMD, Intel) implementan esa especificación en los drivers de tu sistema operativo.

Dado que cada sistema operativo maneja las funciones gráficas de forma distinta (especialmente en Windows o Linux), las funciones de OpenGL de la versión 3.3 en adelante no se pueden invocar directamente. Tienes que buscar dinámicamente en memoria la dirección de cada función de la GPU en tiempo de ejecución.

GLAD es un cargador (loader) que hace ese trabajo sucio por ti: inicializa los punteros de todas las funciones de OpenGL para que puedas llamarlas con normalidad.

## ¿Cuáles son las alternativas de uso de GLAD?
Existen tres formas principales de integrar un cargador de OpenGL en un proyecto:

Alternativa A: Usar GLEW (El competidor antiguo de GLAD)
Qué es: Otra librería de carga similar a GLAD, pero más antigua.

Por qué se usaba antes: Era el estándar de la industria hace 10 años.

Por qué ya no se recomienda tanto: Es más pesada, a veces genera conflictos con extensiones modernas de OpenGL y su arquitectura interna es más compleja de mantener.

```
learnopengl/
│
├── CMakeLists.txt              <-- (El archivo maestro que gobierna todo)
├── third_party/
│   └── glad/
│       ├── include/
│       │   ├── glad/glad.h
│       │   └── KHR/khrplatform.h
│       └── src/
│           └── glad.c          <-- (GLAD vive AQUÍ una sola vez y para siempre)
│
└── chapter3/
    └── hello-window/
        ├── CMakeLists.txt      <-- (Un archivo chiquito que solo compila tu main.cpp)
        └── main.cpp            <-- (Tu código del capítulo)
```
