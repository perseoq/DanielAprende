### Tipos de Datos Fundamentales en Lenguaje C

#### Objetivo de aprendizaje

Comprender qué es un tipo de datos en C, identificar los tipos fundamentales (enteros, decimales y caracteres), y aprender a elegir el tipo correcto según el espacio de memoria que requiere la información que vamos a procesar. Usaremos el estándar **C99**.

#### Explicación teórica y analogía

Imagina que organizas una bodega y tienes diferentes tipos de **recipientes**: una caja pequeña para guardar monedas, un cajón mediano para libros y un contenedor industrial para guardar vehículos. Si intentas meter un vehículo en una caja de zapatos, se rompe; si guardas una moneda en un contenedor industrial, desperdicias espacio.

En C, los **tipos de datos** son esos recipientes. La computadora necesita saber exactamente cuánta memoria (cuántos bytes) apartar para cada variable antes de que empiece a correr el programa.

Los tipos básicos en C se dividen principalmente en tres familias:

1. **Enteros (`int`, `short`, `long`, `long long`)**: Para números sin parte decimal (positivos y negativos).    
2. **Punto Flotante o Decimales (`float`, `double`)**: Para números con fracciones o decimales.
3. **Caracteres (`char`)**: Para almacenar letras o símbolos individuales (internamente la computadora los guarda como códigos numéricos).
    
_Nota:_ Cada tipo entero y de carácter puede llevar el modificador `unsigned` (sin signo), lo que significa que solo guardará números positivos, duplicando su capacidad hacia arriba.
#### Ejemplo de código comentado

Aquí tienes un programa completo, compilable y funcional que declara diferentes variables y muestra en pantalla tanto su valor como el espacio exacto que ocupan en memoria (usando el operador `sizeof`):

```c
#include <stdio.h> // Biblioteca estándar para entrada y salida

int main(void) {
    // 1. Tipos enteros
    int edad = 25;
    unsigned int poblacion = 4000000;
    
    // 2. Tipos decimales (reales)
    float temperatura = 36.5f;
    double pi_preciso = 3.1415926535; // Mayor precisión
    
    // 3. Tipo carácter
    char inicial = 'A';

    // Imprimiendo los valores y sus tamaños en bytes
    printf("  VALORES ALMACENADOS  \n");
    printf("Edad (int): %d (Ocupa %zu bytes)\n", edad, sizeof(edad));
    printf("Poblacion (unsigned int): %u (Ocupa %zu bytes)\n", poblacion, sizeof(poblacion));
    printf("Temperatura (float): %.1f (Ocupa %zu bytes)\n", temperatura, sizeof(temperatura));
    printf("Pi (double): %.10f (Ocupa %zu bytes)\n", pi_preciso, sizeof(pi_preciso));
    printf("Inicial (char): %c (Ocupa %zu bytes)\n", inicial, sizeof(inicial));

    return 0;
}
```

#### Explicación línea por línea

- `#include <stdio.h>`: Importa las herramientas para poder usar funciones de consola como `printf`.
- `int edad = 25;`: Crea una caja llamada `edad` de tipo entero (`int`) y guarda el valor `25` en ella.
- `float temperatura = 36.5f;`: Crea una variable decimal de simple precisión. La letra `f` al final le indica explícitamente al compilador que es un literal `float`.
- `sizeof(edad)`: Es un operador especial de C que calcula cuántos bytes de memoria utiliza esa variable en tu computadora. El especificador `%zu` está diseñado específicamente para imprimir el resultado de `sizeof`.
- `%.10f`: En el `printf`, el modificador `10` después del punto indica que queremos ver 10 decimales de precisión para el número `double`.
    
#### Errores comunes y cómo evitarlos

1. **Mezclar especificadores de formato en `printf`**: Si usas `%d` (para enteros) e intentas imprimir un `float` o un `double`, el programa imprimirá basura o se comportará de forma extraña porque la computadora leerá los bits de forma incorrecta. ¡Asegúrate de usar `%f` para decimales y `%c` para caracteres!

2. **Desbordamiento (_Overflow_)**: Asignar un número demasiado grande a un tipo pequeño (por ejemplo, meter un número de 5 cifras en un `char` que solo soporta de -128 a 127). El valor "dará la vuelta" y corromperá el resultado.

#### Ejercicio práctico propuesto

Escribe un programa en C que declare tres variables con los siguientes datos sobre ti (o un personaje ficticio):

1. Tu **edad** (número entero).

2. La **inicial de tu primer nombre** (un carácter, recuerda usar comillas simples `' '`).

3. Tu **estatura en metros** en formato decimal (por ejemplo, `1.75`).

Luego, haz que el programa imprima un mensaje presentándote utilizando esas variables y sus respectivos formatos en el `printf`.

> **Pista para el ejercicio:**
> - Para la edad usa `%d`.
> - Para la inicial usa `%c`.
> - Para la estatura usa `%f`.
#### Mini resumen

- C requiere que declares explícitamente el tipo de cada variable antes de usarla.
- Los tipos básicos son **enteros** (`int`), **decimales** (`float`/`double`) y **caracteres** (`char`).
- El tamaño en memoria de estos tipos puede variar ligeramente según la arquitectura de tu computadora, por lo que usar `sizeof` es una excelente práctica para consultarlo.

### Tipos de Datos y sus Límites en Lenguaje C

#### Objetivo de aprendizaje

Conocer en detalle los tipos de datos fundamentales en C (bajo el estándar **C99**), el tamaño que ocupan en memoria y los límites exactos de almacenamiento permitidos por las bibliotecas estándar `<limits.h>` y `<float.h>`.

#### Explicación teórica y analogía

Imagina que fabricas cerraduras de combinación o odómetros en un auto: si el medidor tiene únicamente tres rodillos numéricos, su capacidad máxima es de 999. Si intentas registrar el 1000, el marcador dará la vuelta o sufrirá un desbordamiento.

En C, los tipos de datos determinan la **amplitud del rango** que podemos almacenar. Como la memoria RAM está compuesta de bytes, cada tipo consume una cantidad específica de espacio físico, lo cual define exactamente qué tan grande o pequeño puede ser el número antes de que falle el cálculo.

#### Tipos de Datos Enteros y sus Límites (Estándar C99)

Los enteros se dividen según su capacidad y si aceptan o no números negativos (firmados vs no firmados). Los valores mostrados a continuación corresponden a arquitecturas estándar de 32 y 64 bits típicas:  


|**Tipo de Dato**|**Tamaño en Memoria**|**Rango Mínimo / Máximo (Límites estándar)**|
| | | |
|`char` / `signed char`|1 byte (8 bits)|`-127` a `+127` (o `-128` a `127`)|
|`unsigned char`|1 byte (8 bits)|`0` a `255`|
|`short` (o `short int`)|2 bytes (16 bits)|`-32,768` a `32,767`|
|`unsigned short`|2 bytes (16 bits)|`0` a `65,535`|
|`int`|4 bytes (32 bits)|`-2,147,483,648` a `2,147,483,647`|
|`unsigned int`|4 bytes (32 bits)|`0` a `4,294,967,295`|
|`long` (o `long int`)|4 u 8 bytes|Varía según plataforma (mínimo de `-2,147,483,647` a `2,147,483,647`)|
|`long long`|8 bytes (64 bits)|`-9,223,372,036,854,775,808` a `9,223,372,036,854,775,807`|

#### Ejemplo de código comentado

Podemos consultar los límites exactos directamente desde nuestro código utilizando las macros provistas por `<limits.h>`:

```
#include <stdio.h>
#include <limits.h> // Contiene las constantes de límites enteros

int main(void) {
    printf("  LIMITES DE ENTEROS EN ESTE SISTEMA  \n");
    
    // Consultando límites para el tipo int usando INT_MIN e INT_MAX
    printf("Tipo 'int':\n");
    printf("  Minimo: %d\n", INT_MIN);
    printf("  Maximo: %d\n", INT_MAX);
    
    // Consultando límites para el tipo unsigned int
    printf("\nTipo 'unsigned int':\n");
    printf("  Minimo: 0\n");
    printf("  Maximo: %u\n", UINT_MAX);

    // Consultando límites para long long
    printf("\nTipo 'long long':\n");
    printf("  Minimo: %lld\n", LLONG_MIN);
    printf("  Maximo: %lld\n", LLONG_MAX);

    return 0;
}
```

#### Explicación línea por línea

- `#include <limits.h>`: Importa la biblioteca del sistema que define las constantes con los valores límite de cada tipo entero.
- `INT_MIN` e `INT_MAX`: Macros predefinidas que devuelven el valor límite inferior y superior que soporta una variable tipo `int` en la máquina actual.
- `%u` y `%lld`: Especificadores de formato adecuados para imprimir con seguridad enteros sin signo grandes (`unsigned int`) y enteros muy largos (`long long`), respectivamente.

#### Errores comunes y cómo evitarlos

1. **Asumir tamaños fijos en todas las computadoras**: Aunque `int` suele ser de 4 bytes en la gran mayoría de equipos actuales, el estándar de C permite que varíe en sistemas embebidos pequeños. Si necesitas un tamaño estricto de bits (por ejemplo, exactamente 32 bits), es una excelente práctica moderna incluir `<stdint.h>` y utilizar tipos como `int32_t` o `uint32_t`.
2. **Ignorar el desbordamiento (_Overflow_)**: Si sumas una unidad al valor máximo de un tipo `unsigned`, este no se detiene ni arroja error: simplemente da la vuelta y regresa a `0`, corrompenado la lógica matemática de tu programa.

#### Ejercicio práctico propuesto

Escribe un programa en C que utilice la biblioteca `<limits.h>` para imprimir en pantalla el valor máximo y mínimo que soporta una variable de tipo `short` (`SHRT_MIN` y `SHRT_MAX`).


> **Pista para el ejercicio:**

> - Recuerda incluir `#include <limits.h>` al inicio de tu archivo.
> - Utiliza el especificador de formato `%d` dentro del `printf` para mostrar los límites del tipo `short`.


### Palabras Reservadas en Lenguaje C

Las **palabras reservadas** (o keywords) son identificadores que tienen un significado sintáctico especial para el compilador. Están predefinidas en el lenguaje y **no puedes utilizarlas** para nombrar variables, funciones o cualquier otro elemento que tú crees.

A continuación tienes el listado completo de las **32 palabras reservadas oficiales del estándar ANSI/ISO C** (comunes también en C99 y versiones modernas), agrupadas por su función principal:

 

#### 1. Control de Flujo y Decisiones

Estas palabras dirigen el camino que toma la ejecución del programa según las condiciones:

* `if`: Evalúa una condición lógica; si es verdadera, ejecuta un bloque de código.
* `else`: Alternativa al `if` cuando la condición no se cumple.
* `switch`: Evalúa una variable para elegir entre múltiples caminos posibles.
* `case`: Etiqueta individual dentro de un `switch`.
* `default`: Caso por defecto si ninguna etiqueta `case` coincide en un `switch`.
* `while`: Bucle que se repite mientras una condición sea verdadera.
* `do`: Inicia un bucle que se ejecuta al menos una vez antes de evaluar la condición.
* `for`: Bucle controlado por contador estructurado.
* `break`: Sale de inmediato de un bucle o de un bloque `switch`.
* `continue`: Salta a la siguiente iteración de un bucle ignorando el resto del código actual.
* `goto`: Realiza un salto incondicional hacia una etiqueta específica en el código.
* `return`: Finaliza la ejecución de una función y opcionalmente devuelve un valor.



 

#### 2. Tipos de Datos Fundamentales

Sirven para declarar de qué tipo será una variable o qué tipo de valor manejará una función:

* `int`: Tipo entero (números sin decimales).


* `char`: Tipo carácter (una letra o símbolo individual).


* `float`: Tipo de punto flotante de precisión simple (decimales).


* `double`: Tipo de punto flotante de doble precisión (decimales más exactos).


* `void`: Vacío o ausencia de tipo (usado en funciones que no devuelven nada o punteros genéricos).



 

#### 3. Clases de Almacenamiento y Ámbito

Modifican cómo se comporta la memoria, el tiempo de vida o la visibilidad de las variables:

* `auto`: Indica almacenamiento automático para variables locales (es el valor por defecto en funciones).


* `register`: Sugiere al compilador almacenar la variable en un registro rápido del procesador.


* `static`: Mantiene el valor de una variable local entre llamadas o limita la visibilidad de una variable/función global al archivo actual.


* `extern`: Indica que una variable o función está definida en otro archivo del programa.



 

#### 4. Modificadores de Tipo

Alteran las propiedades o capacidades de los tipos de datos básicos:

* `signed`: Indica que un tipo numérico admite tanto valores positivos como negativos (es el comportamiento predeterminado).
* `unsigned`: Limita un tipo entero exclusivamente a valores positivos, duplicando su rango superior.
* `short`: Define enteros de menor tamaño (generalmente 2 bytes).


* `long`: Define enteros o decimales de mayor tamaño y capacidad (4 u 8 bytes).



 

#### 5. Estructuras de Datos Avanzadas

Permiten crear tipos de datos personalizados o compuestos:

* `struct`: Define una estructura para agrupar variables de diferentes tipos bajo un mismo nombre.


* `union`: Define una estructura donde todos los miembros comparten exactamente la misma posición de memoria.


* `enum`: Define una enumeración (conjunto de constantes enteras con nombre).


* `typedef`: Crea un alias o nuevo nombre para un tipo de dato existente.



 

#### 6. Operadores y Calificadores Especiales

* `sizeof`: Operador especial que calcula el tamaño en bytes que ocupa un tipo de dato o variable en memoria.


* `const`: Especifica que el valor de una variable no puede ser modificado una vez inicializado.


* `volatile`: Advierte al compilador que el valor de una variable puede cambiar de forma imprevista (por ejemplo, por hardware externo) y no debe optimizarse agresivamente.



 

> **Nota importante:** Recuerda que C **distingue entre mayúsculas y minúsculas** (*case-sensitive*). Las palabras reservadas deben escribirse siempre en **minúsculas** (por ejemplo, `int` es válida, pero `INT` o `Int` serían interpretadas como nombres normales creados por ti y causarían errores de compilación).
> 
> 

### Conversión de Tipos (Casting) y Límites de Contexto en C

#### Objetivo de aprendizaje

Comprender cómo y cuándo el lenguaje C realiza conversiones de tipos automáticas (implícitas) o forzadas (explícitas mediante *cast*), y analizar cómo los límites de almacenamiento de cada contexto afectan los resultados numéricos. Usaremos el estándar **C99**.

 

#### Explicación teórica y analogía

Imagina que tienes agua en un vaso pequeño y quieres verterla en una cubeta grande, o al revés: si intentas meter un volumen enorme de líquido en un recipiente minúsculo, el contenido se desborda (lo que en programación llamamos desbordamiento o *overflow*).

En C, cuando combinas distintos tipos de datos en una operación (como sumar un entero y un decimal), o cuando asignas un valor de un tipo a una variable de otro, la computadora necesita transformar los datos para que "hablen el mismo idioma". Esto ocurre de dos formas:

1. **Conversión Implícita (Automática):** El compilador promueve o transforma los tipos de datos de forma automática para evitar perder precisión (por ejemplo, al sumar un `int` y un `float`, el entero se convierte temporalmente a decimal).


2. **Conversión Explícita (Casting):** Obligas al compilador a transformar un tipo de dato escribiendo el operador de molde `(tipo)` antes de la expresión.



Los **límites de contexto** se refieren a las restricciones que imponen los rangos de cada tipo de dato al realizar estas conversiones; si superas el límite del tipo receptor, ocurren recortes de bits o pérdidas de precisión imprevisibles.

 

#### Ejemplo de código comentado

```c
#include <stdio.h>

int main(void) {
    int a = 7, b = 2;
    float division_incorrecta, division_correcta;

    // 1. División entera implícita
    // Al operar dos enteros, C trunca el resultado decimal.
    division_incorrecta = a / b; // 7 / 2 da 3, no 3.5

    // 2. Casting explícito
    // Forzamos a que 'a' se comporte como float antes de dividir
    division_correcta = (float) a / b; // 7.0 / 2 da 3.5

    printf("Division sin casting: %.2f\n", division_incorrecta);
    printf("Division con casting explicito: %.2f\n", division_correcta);

    return 0;
}

```

 

#### Explicación línea por línea

* `int a = 7, b = 2;`: Se declaran dos variables enteras de tipo `int`.
* `division_incorrecta = a / b;`: Como ambos operandos son enteros, la división es entera. El resultado de `7 / 2` es `3`, y luego se convierte a `float` (`3.00`) al asignarlo. Se pierde por completo la parte fraccionaria.
* `division_correcta = (float) a / b;`: El operador de *cast* `(float)` transforma temporalmente el valor de `a` a punto flotante. Por las reglas de conversión de C, el operador binario promueve a `b` al mismo tipo, logrando una división decimal real (`3.5`).



 

#### Errores comunes y cómo evitarlos

1. **Olvidar el casting en divisiones enteras**: Es el error clásico al calcular promedios o porcentajes. Si divides dos enteros, obtendrás un valor entero truncado aunque guardes el resultado en una variable `float`.
2. **Desbordamiento por casting imprudente**: Forzar la conversión de un número entero muy grande (como un `long long`) a un tipo pequeño (`char` o `short`) trunca los bits de mayor orden, alterando por completo el valor original.



 

#### Ejercicio práctico propuesto

Escribe un programa en C que calcule el promedio con decimales de tres calificaciones enteras (por ejemplo: `85`, `90` y `78`).

> **Pista para el ejercicio:**
> * Suma las tres calificaciones en una variable entera.
> * Utiliza el operador de *cast* `(float)` al momento de dividir la suma total entre el número de elementos (`3` o `3.0f`) para evitar el truncamiento entero.
> * Imprime el resultado utilizando el especificador de formato `%.2f`.
> 
> 

 

#### Mini resumen

* Las conversiones implícitas ocurren de forma automática cuando C busca un tipo común en operaciones mixtas.


* El *cast* explícito `(tipo) expresion` nos permite forzar conversiones de manera consciente.


* Conocer los límites de contexto evita pérdidas de precisión y errores lógicos de truncamiento en cálculos matemáticos.

 

### Título del tema: Delimitadores `{ }`, Bloques y Ámbito de Variables (Locales vs. Globales)

#### Objetivo de aprendizaje

Comprender la función estructural de las llaves `{ }` en C para crear bloques de código y dominar las reglas de visibilidad, almacenamiento y tiempo de vida de las variables locales y globales.

 

#### Explicación teórica y analogía

Imagina que tu programa en C es una gran empresa:

* **Variables Globales (Gerencia General):** Son oficinas ubicadas en la entrada principal, fuera de cualquier departamento. Cualquier empleado de cualquier departamento puede verlas y usarlas en cualquier momento. Viven desde que enciende la empresa hasta que cierra.
* **Variables Locales y Bloques `{ }` (Oficinas Privadas por Departamento):** Las llaves `{ }` definen un **bloque** o recinto cerrado (un departamento). Las variables que creas dentro de esas llaves son privadas; solo existen y se pueden usar dentro de ese departamento específico. Cuando el flujo del programa sale de esas llaves, la variable es destruida y su memoria se libera automáticamente.



En C, un par de llaves `{ }` agrupa declaraciones y sentencias en una unidad lógica llamada **bloque** (o sentencia compuesta). Todo lo que declaras dentro de un bloque tiene **ámbito local** (*block scope*), mientras que lo declarado fuera de cualquier función tiene **ámbito de archivo** (*file scope* o global).

 

#### Ejemplo de código comentado

Analicemos este programa compilable que demuestra cómo funcionan los límites de las llaves, el ocultamiento de variables (*shadowing*) y el tiempo de vida:

```c
#include <stdio.h>

// VARIABLE GLOBAL (Ámbito de archivo / File Scope)
// Accesible para cualquier función de este archivo de aquí en adelante.
int nivel_global = 100; 

int main(void) {
    // VARIABLE LOCAL A 'main'
    int nivel_local = 10;

    printf("  FUERA DEL BLOQUE INTERNO  \n");
    printf("Global: %d\n", nivel_global);
    printf("Local (main): %d\n", nivel_local);

    // INICIO DE UN BLOQUE INTERNO (Delimitado por llaves)
    {
        // VARIABLE LOCAL AL BLOQUE INTERNO
        int nivel_local = 50; // Oculta temporalmente a la variable 'nivel_local' de afuera
        int solo_del_bloque = 999;

        printf("\n  DENTRO DEL BLOQUE INTERNO  \n");
        printf("Global (accesible): %d\n", nivel_global);
        printf("Local interna (shadowing): %d\n", nivel_local); // Imprime 50
        printf("Variable exclusiva del bloque: %d\n", solo_del_bloque);
    } 
    // FIN DEL BLOQUE INTERNO: Las variables 'nivel_local' (50) y 'solo_del_bloque' mueren aquí.

    printf("\n  DE NUEVO FUERA DEL BLOQUE INTERNO  \n");
    printf("Local (main original): %d\n", nivel_local); // Vuelve a valer 10

    // ERROR POTENCIAL SI DESCOMENTARAS LA SIGUIENTE LÍNEA:
    // printf("%d", solo_del_bloque); // ¡Fallo! 'solo_del_bloque' ya no existe fuera de sus llaves.

    return 0;
}

```

 

#### Explicación línea por línea

* `int nivel_global = 100;`: Se declara fuera de `main()`. Pertenece al ámbito global; vive durante toda la ejecución del programa y se almacena en memoria estática.


* `{ ... }`: El bloque interno crea un nuevo sub-contexto de visibilidad.


* `int nivel_local = 50;` dentro del bloque: Crea una nueva variable que **oscurece** (*shadows*) a la variable `nivel_local` del `main`. Durante estas llaves, si nombras a `nivel_local`, C usará la versión de valor `50`. A esto se le conoce como **ocultamiento de nombres en bloques anidados**.


* Fin de las llaves `}`: El compilador desasigna de la pila (*stack*) la memoria de todas las variables que nacieron dentro de ese bloque. Dejan de existir por completo.



 

#### Errores comunes y cómo evitarlos

1. **Intentar usar variables locales fuera de su bloque**: Si declaras una variable dentro de un `if`, un `for` o un bloque delimitado por `{ }`, intentarla invocar fuera de esas llaves generará un error de compilación (*variable undeclared*). Mantén tus declaraciones en el bloque correcto.
2. **Abusar de las variables globales**: Aunque una variable global es cómoda porque "se ve en todas partes", rompe la modularidad y puede ser modificada por error en cualquier parte del código, volviendo los programas difíciles de depurar. Prefiere siempre el uso de variables locales y pasa datos mediante parámetros o retornos.



 

#### Ejercicio práctico propuesto

Escribe un programa en C que declare una variable global llamada `contador_global` con valor `0`. Dentro de `main()`, declara una variable local llamada `contador_local` con valor `5`. Luego, abre un bloque interno `{ }` mediante llaves, declara otra variable local dentro que se llame igual (`contador_local`) pero con valor `50`, e imprime ambas dentro y fuera del bloque para observar cómo operan los límites de visibilidad.

> **Pista para el ejercicio:**
> * Observa cómo se comportan los valores al imprimir `contador_local` antes, durante y después del bloque anidado.
> * Recuerda que las llaves `{` y `}` delimitan el nacimiento y la muerte del ámbito local.
> 
> 
> 
> 

 

#### Mini resumen

* Las llaves `{ }` delimitan bloques de código y establecen los límites estrictos de visibilidad y tiempo de vida para las variables locales.


* Las **variables locales** nacen al entrar a su bloque y mueren al salir de él.


* Las **variables globales** se definen fuera de las funciones y están disponibles para todo el programa, aunque su uso excesivo no es una buena práctica.


* Si un bloque interno declara una variable con el mismo nombre que una externa, la interna "oculta" temporalmente a la otra (*shadowing*).


### Título del tema: Estructuras en C (`struct`)

#### Objetivo de aprendizaje

Comprender qué es una estructura en C, aprender a definir su plantilla, declarar variables de tipo estructura, e interactuar con sus campos individuales utilizando el operador punto (`.`).

 

#### Explicación teórica y analogía

Imagina que tienes una ficha de inscripción o una tarjeta de contacto en papel. En esa tarjeta no guardas solo números o solo letras; agarras un solo cartoncillo y en él escribes:

* Un texto con el **nombre** (cadena de caracteres).
* Un número entero con la **edad**.
* Un número decimal con la **estatura**.

En C, una estructura (`struct`) es exactamente eso: un contenedor personalizado que agrupa variables de diferentes tipos (enteros, decimales, caracteres, etc.) bajo una sola entidad lógica. Mientras que un arreglo te obliga a que todos los elementos sean del mismo tipo, la estructura te permite construir un tipo de dato a la medida de tu problema.

 

#### Ejemplo de código comentado

Aquí tienes un programa completo, compilable y funcional que define una estructura para representar a un estudiante, crea una instancia de ella y manipula sus datos:

```c
#include <stdio.h>
#include <string.h> // Necesario para usar strcpy si queremos manipular cadenas

// 1. Definición de la plantilla de la estructura (Estructura 'Estudiante')
struct Estudiante {
    char nombre[50];
    int edad;
    float promedio;
};

int main(void) {
    // 2. Declaración de una variable de tipo 'struct Estudiante'
    struct Estudiante alumno1;

    // 3. Asignación de valores a los campos usando el operador punto (.)
    // Usamos strcpy para asignar el texto al arreglo de caracteres
    strcpy(alumno1.nombre, "Carlos Perez");
    alumno1.edad = 20;
    alumno1.promedio = 9.5f;

    // 4. Lectura e impresión de los campos de la estructura
    printf("  DATOS DEL ESTUDIANTE  \n");
    printf("Nombre: %s\n", alumno1.nombre);
    printf("Edad: %danos\n", alumno1.edad);
    printf("Promedio: %.2f\n", alumno1.promedio);

    return 0;
}

```

 

#### Explicación línea per línea

* `struct Estudiante { ... };`: Define la plantilla o molde. En este punto la computadora **no** reserva memoria todavía; solo aprende cómo es la forma de un "Estudiante".


* `struct Estudiante alumno1;`: Crea oficialmente la variable `alumno1` reservando el espacio en memoria necesario para almacenar sus tres campos juntos.


* `alumno1.edad = 20;`: El operador punto (`.`) sirve para conectar la variable estructurada con el campo interno específico al que deseas acceder o modificar.



 

#### Errores comunes y cómo evitarlos

1. **Olvidar el punto y coma final en la definición**: La llave que cierra la plantilla del `struct` **debe** terminar obligatoriamente con un punto y coma (`};`). Olvidarlo genera un error en cascada en el compilador.
2. **Intentar asignar cadenas con `=` directamente**: En C, las cadenas de caracteres (arreglos) no se puedenigualar con el operador `=` después de haber sido declaradas (por ejemplo: `alumno1.nombre = "Carlos";` fallará). Debes usar funciones como `strcpy` de la biblioteca `<string.h>` o inicializar la estructura al momento de declararla.



 

#### Ejercicio práctico propuesto

Escribe un programa en C que defina una estructura llamada `Pelicula` con tres campos:

1. `titulo` (un arreglo de caracteres de 40).
2. `anio` (un número entero).
3. `duracion_minutos` (un número entero).

Luego, dentro de `main()`, declara una variable de tipo `struct Pelicula`, asígnale valores utilizando `strcpy` para el título y el operador `.` para los números, e imprímelos en pantalla de forma ordenada.

> **Pista para el ejercicio:**
> * Recuerda incluir `#include <string.h>` si vas a usar `strcpy` para el título de tu película.
> * Para imprimir los datos, utiliza los especificadores `%s` (para el título), `%d` (para el año) y `%d` (para la duración).
> 
> 

 

#### Mini resumen

* Una estructura (`struct`) agrupa variables de diferentes tipos de datos bajo un nombre común para modelar objetos del mundo real.


* La definición del `struct` crea una plantilla, y la declaración de una variable de ese tipo es la que realmente reserva memoria.


* Accedemos a los campos individuales de una estructura utilizando el operador punto (`.`).

### Uso de la Condicional `if` en Lenguaje C

#### Objetivo de aprendizaje

Comprender la estructura de la toma de decisiones mediante la instrucción condicional `if`, evaluar expresiones lógicas y relacionales, y aprender a manejar bloques de código alternativos con `else` bajo el estándar **C99**.

 

#### Explicación teórica y analogía

Imagina que vas conduciendo por una carretera y llegas a una bifurcación: si el semáforo está en verde, continúas avanzando; si está en rojo, te detienes. La computadora no es diferente; necesita tomar decisiones basadas en situaciones que cambian mientras el programa corre.

En C, la instrucción `if` actúa como ese punto de decisión. Evalúa una condición matemática o lógica: si el resultado es verdadero (cualquier valor distinto de cero), la computadora ejecuta un bloque de instrucciones específico; si es falso (cero), simplemente ignora ese bloque o se dirige a una alternativa (`else`).

 

#### Ejemplo de código comentado

Aquí tienes un programa completo, compilable y funcional que evalúa si una persona es mayor de edad utilizando un condicional `if-else`:

```c
#include <stdio.h> // Biblioteca estándar para printf y scanf

int main(void) {
    int edad;

    printf("Ingresa tu edad: ");
    scanf("%d", &edad);

    // Condicional if-else
    if (edad >= 18) {
        printf("Eres mayor de edad. Puedes ingresar.\n");
    } else {
        printf("Eres menor de edad. Acceso denegado.\n");
    }

    return 0;
}

```

 

#### Explicación línea por línea

* `scanf("%d", &edad);`: Captura el número entero ingresado por el usuario desde el teclado y lo guarda en la variable `edad`.
* `if (edad >= 18)`: Evalúa la condición relacional. Si el valor de `edad` es mayor o igual a `18`, la condición se considera verdadera (resultado distinto de cero).


* `{ ... }` (primer bloque): Las llaves agrupan las sentencias que se ejecutarán **únicamente** si la condición del `if` se cumple.


* `else`: Si la condición inicial resulta falsa (menor de 18), se activa de manera automática este camino alternativo.



 

#### Errores comunes y cómo evitarlos

1. **Confundir el operador de igualdad (`==`) con el de asignación (`=`)**: Escribir `if (x = 5)` asigna el valor 5 a `x` y evalúa como verdadero porque el resultado es distinto de cero, lo cual destruye la lógica de tu condición. Utiliza siempre doble igual (`==`) para comparar.


2. **Olvidar las llaves `{ }` cuando hay múltiples instrucciones**: Si tu `if` o `else` va a controlar más de una línea de código, **debes** encerrarlas entre llaves. Si las omites, C solo considerará la primera línea como parte del condicional, generando errores de lógica difíciles de detectar.



 

#### Ejercicio práctico propuesto

Escribe un programa en C que solicite al usuario ingresar un número entero cualquiera. Utiliza una estructura `if-else` para determinar e imprimir en pantalla si el número ingresado es **positivo** o **negativo** (puedes asumir que el cero pertenece a los positivos o evaluarlo de forma independiente).

> **Pista para el ejercicio:**
> * Compara el número ingresado con cero utilizando el operador mayor que (`>`) o menor que (`<`).
> 
> 
> * No olvides usar llaves `{ }` en los bloques del `if` y del `else` para mantener el código ordenado y seguro.
> 
> 
> 
> 

 

#### Mini resumen

* La instrucción `if` permite que un programa tome decisiones lógicas según el valor de una condición (verdadero si es distinta de cero, falso si es cero).


* El uso opcional de `else` nos da una ruta alternativa en caso de que la condición principal no se cumpla.


* Es fundamental utilizar operadores de comparación correctos (`==`) y agrupar las sentencias múltiples mediante llaves `{ }`.


### El Bucle `for` en Lenguaje C

#### Objetivo de aprendizaje

Comprender la estructura y el funcionamiento del bucle `for` para automatizar tareas repetitivas de forma eficiente, controlando la inicialización, la condición de parada y la actualización en una sola línea compacta bajo el estándar **C99**.

 

#### Explicación teórica y clara

Imagina que estás entrenando para una competencia y tu entrenador te pide dar exactamente 5 vueltas a una pista de atletismo. Antes de empezar, te colocas en la línea de salida (inicialización), en cada vuelta verificas si ya completaste las 5 (condición) y, al terminar cada tramo, sumas 1 a tu contador de vueltas (actualización o avance).

En C, el bucle `for` empaqueta estos tres pasos esenciales en una sola estructura dentro de los paréntesis:

1. **Inicialización**: Se ejecuta una sola vez antes de empezar el ciclo.
2. **Condición**: Se evalúa antes de cada iteración; si es verdadera, el bloque de código se ejecuta; si es falsa, el bucle termina de inmediato.
3. **Actualización (avance)**: Se ejecuta al final de cada iteración para modificar la variable de control.

 

#### Ejemplo de código comentado

Aquí tienes un programa completo, compilable y funcional que imprime una serie numérica utilizando un bucle `for`:

```c
#include <stdio.h>

int main(void) {
    // Imprimir los números del 1 al 5 usando un bucle for
    for (int i = 1; i <= 5; i++) {
        printf("Vuelta numero: %d\n", i);
    }

    return 0;
}

```

 

#### Explicación línea por línea

* `#include <stdio.h>`: Importa la biblioteca estándar de entrada/salida para poder utilizar la función `printf`.
* `for (int i = 1; i <= 5; i++)`: La cabecera del bucle concentra los tres elementos clave separados por puntos y comas:
* `int i = 1`: Declara e inicializa la variable de control `i` en `1` (permitido formalmente desde el estándar C99).
* `i <= 5`: Es la condición que se evalúa antes de cada vuelta; mientras sea verdadera, el ciclo continúa.
* `i++`: Incrementa el valor de `i` en una unidad al finalizar cada iteración.


* `printf(...)`: El bloque de código interno que se repetirá en cada ciclo, mostrando el valor actual de la variable `i`.

 

#### Errores comunes y cómo evitarlos

1. **Colocar un punto y coma al final de la cabecera del `for**`: Escribir `for (int i = 0; i < 5; i++);` por error hace que el bucle se ejecute vacío y desconecte el bloque de código posterior, ejecutándolo una sola vez fuera del ciclo.
2. **Bucle infinito**: Si omites la actualización (por ejemplo, olvidar incrementar `i`) o escribes mal la condición de salida, el bucle nunca se detendrá, consumiendo recursos del sistema.

 

#### Ejercicio práctico propuesto

Escribe un programa en C que utilice un bucle `for` para calcular e imprimir la **tabla de multiplicar del 5** (desde `5 x 1 = 5` hasta `5 x 10 = 50`).

> **Pista para el ejercicio:**
> * Haz que tu variable de control `i` recorra los valores desde `1` hasta `10`.
> * Dentro del `printf`, imprime la operación multiplicando `5 * i` y utiliza el especificador `%d` para mostrar los valores correspondientes.
> 
> 

 

#### Mini resumen

* El bucle `for` es la estructura ideal cuando sabemos de antemano cuántas veces queremos repetir un bloque de instrucciones.
* Agrupa de forma ordenada la inicialización, la condición y el incremento en su propia cabecera.


* Gracias al estándar C99, puedes declarar la variable de control directamente dentro del paréntesis de inicialización.

### El Bucle `while` en Lenguaje C

#### Objetivo de aprendizaje

Comprender el funcionamiento del bucle `while` para repetir un bloque de código de forma controlada mientras se cumpla una condición específica, bajo el estándar **C99**.

 

#### Explicación teórica y analogía

Imagina que estás inflando un globo con un inflador manual: antes de cada bombeo, verificas visualmente si el globo ya alcanzó el tamaño deseado (la condición). Si aún no está lo suficientemente grande (verdadero), sigues bombeando (ejecutas el bloque de código); cuando finalmente alcanza el tamaño correcto (falso), te detienes y el ciclo de inflado termina.

En C, el bucle `while` opera bajo exactamente el mismo principio. Primero evalúa una condición lógica: si el resultado es verdadero (distinto de cero), se ejecuta el bloque de instrucciones interno; al finalizar, el flujo regresa automáticamente a evaluar la condición. Si en algún momento la condición resulta falsa (cero), el bucle se interrumpe de inmediato y el programa continúa su curso con la instrucción posterior.

 

#### Ejemplo de código comentado

Aquí tienes un programa completo, compilable y funcional que utiliza un bucle `while` para mostrar una cuenta ascendente:

```c
#include <stdio.h>

int main(void) {
    int contador = 1; // Inicializamos la variable de control

    // El bucle se repite mientras 'contador' sea menor o igual a 5
    while (contador <= 5) {
        printf("Iteracion numero: %d\n", contador);
        
        contador++; // ¡Importante! Incrementamos la variable para evitar un bucle infinito
    }

    printf("¡Bucle finalizado con exito!\n");

    return 0;
}

```

 

#### Explicación línea por línea

* `#include <stdio.h>`: Importa la biblioteca estándar de entrada y salida para permitir el uso de funciones como `printf`.
* `int contador = 1;`: Declara y asigna el valor inicial a la variable que servirá para controlar cuántas veces se repetirá el ciclo.
* `while (contador <= 5)`: Evalúa la condición antes de cada iteración. Si `contador` es menor o igual a `5`, permite la ejecución del bloque; de lo contrario, el bucle termina.


* `contador++;`: Modifica la variable de control incrementándola en una unidad. Esto es vital para que la condición eventualmente se vuelva falsa.

 

#### Errores comunes y cómo evitarlos

1. **El bucle infinito:** Si olvidas actualizar la variable de control dentro del bloque (por ejemplo, omitir `contador++`), la condición nunca cambiará, haciendo que el programa quede atrapado repitiéndose indefinidamente y consumiendo los recursos del sistema.
2. **Punto y coma al final de la cabecera:** Escribir por error `while (contador <= 5);` coloca una instrucción nula que vacía el cuerpo del bucle, provocando un comportamiento inesperado o un ciclo infinito si la condición es verdadera.

 

#### Ejercicio práctico propuesto

Escribe un programa en C que utilice un bucle `while` para solicitar repetidamente al usuario que ingrese un número entero, y haga que el programa se repita **únicamente** mientras el usuario no ingrese el número `0`. Cuando escriba `0`, el bucle debe terminar y mostrar un mensaje de despedida.

> **Pista para el ejercicio:**
> * Declara una variable para almacenar la entrada del usuario. Puedes asignarle un valor inicial distinto de cero para asegurar que el bucle entre al menos la primera vez.
> * Configura la condición del `while` para que evalúe si el número ingresado es diferente de `0` (por ejemplo, `numero != 0`).
> 
> 

 

#### Mini resumen

* El bucle `while` evalúa su condición de parada **antes** de cada iteración, lo que significa que si la condición es falsa desde el inicio, el bloque interno jamás se ejecuta.


* Es la estructura ideal cuando no sabemos de antemano cuántas veces se repetirá una acción, sino que dependemos de un estado o evento dinámico.
* Siempre debemos asegurarnos de modificar los elementos de la condición dentro del bloque para permitir una salida limpia del ciclo.

### Menú Interactivo con el Bucle `while` en C

#### Objetivo de aprendizaje

Aprender a diseñar un menú de opciones persistente utilizando un bucle `while` combinado con una estructura de selección `switch`, permitiendo que el usuario interactúe con el programa de forma repetida hasta decidir salir de manera explícita. Usaremos el estándar **C99**.

 

#### Explicación teórica y analogía

Imagina que llegas a un cajero automático o a una máquina de boletos: la pantalla no se apaga después de hacer una sola operación. En su lugar, te muestra un menú con varias alternativas, ejecutas una acción, y al terminar, la pantalla te vuelve a mostrar el menú principal hasta que presionas el botón de "Salir".

En programación, combinamos la flexibilidad de un bucle `while` con una estructura de control `switch` para lograr exactamente este comportamiento. El bucle `while` actúa como el vigilante que mantiene el programa "vivo" repitiéndose, mientras que el `switch` decide qué camino tomar basándose en la opción que el usuario ingresa por teclado.

 

#### Ejemplo de código comentado

Aquí tienes un programa completo, compilable y funcional que implementa un menú interactivo con tres opciones (dos operaciones simuladas y una opción para salir):

```c
#include <stdio.h>

int main(void) {
    int opcion = 0; // Variable para almacenar la opción del usuario

    // El bucle se repite mientras la opción sea diferente de 3 (Salir)
    while (opcion != 3) {
        // Mostramos el menú en pantalla
        printf("\n  MENU PRINCIPAL  \n");
        printf("1. Saludar\n");
        printf("2. Mostrar un mensaje motivacional\n");
        printf("3. Salir\n");
        printf("Elige una opcion (1-3): ");
        
        // Leemos la opción ingresada por el usuario
        scanf("%d", &opcion);

        // Evaluamos la opción elegida usando un switch
        switch (opcion) {
            case 1:
                printf("\n¡Hola! Qué gusto saludarte, programador.\n");
                break;
            case 2:
                printf("\n¡Sigue practicando! La constancia es la clave del éxito en C.\n");
                break;
            case 3:
                printf("\nSaliendo del programa... ¡Hasta pronto!\n");
                break;
            default:
                printf("\n[Error] Opción no válida. Por favor, elige entre 1 y 3.\n");
                break;
        }
    }

    return 0;
}

```

 

#### Explicación línea por línea

* `int opcion = 0;`: Inicializamos la variable `opcion` en `0` para asegurar que entre al bucle `while` al menos la primera vez (ya que `0 != 3` es verdadero).
* `while (opcion != 3)`: Controla la persistencia del menú. El bloque se repetirá indefinidamente hasta que el usuario escriba y seleccione el número `3`.
* `scanf("%d", &opcion);`: Captura el número que digita el usuario y actualiza la variable de control. Si no actualizamos esta variable, el programa se quedaría atrapado en un bucle infinito.
* `switch (opcion)`: Toma el valor ingresado y lo compara con los casos definidos (`case 1`, `case 2`, `case 3`).


* `break;`: Es fundamental dentro de cada `case` para evitar que la ejecución "caiga" (*fall through*) hacia las siguientes opciones por inercia.


* `default:`: Captura cualquier número que esté fuera del rango esperado (por ejemplo, si el usuario escribe `5` o `-1`), informándole del error sin romper el flujo del programa.



 

#### Errores comunes y cómo evitarlos

1. **Olvidar actualizar la variable de control**: Si dentro del `while` olvidas colocar el `scanf` para pedir nuevamente la opción, el programa entrará en un bucle infinito atrapado en la primera elección.
2. **Confundir el orden de las llaves y los `break**`: Omitir el `break` al final de un `case` en el `switch` hará que C ejecute las instrucciones del caso actual y continúe ejecutando los casos de abajo por error, alterando la lógica del menú.



 

#### Ejercicio práctico propuesto

Modifica el ejemplo anterior para agregar una **cuarta opción** al menú: *Calcular el doble de un número*.

* Cuando el usuario elija la opción `4`, el programa debe pedirle que ingrese un número entero, calcular su doble y mostrarlo en pantalla antes de volver a desplegar el menú principal.
* Asegúrate de actualizar la condición del bucle `while` para que el programa se mantenga activo mientras la opción sea diferente de `5` (si decides que el `5` sea salir).

> **Pista para el ejercicio:**
> * Añade `printf("4. Calcular el doble de un número\n");` en la sección visual del menú.
> * Amplía el rango de opciones válidas en tu condición de salida (`while`) y agrega un nuevo `case 4:` dentro del `switch` donde declares una variable temporal, pidas el número con `scanf` y multipliques por `2`.
> 
> 

 

#### Mini resumen

* Un menú interactivo combina la repetitividad de un bucle `while` con la selección múltiple de un `switch`.


* La variable de control evaluada en el `while` debe modificarse dentro del ciclo (mediante la interacción del usuario con `scanf`) para permitir una salida limpia.
* El uso de `break` en cada `case` es obligatorio para evitar ejecuciones indeseadas en cascada.


### Uso de la Instrucción `switch` en Lenguaje C

#### Objetivo de aprendizaje

Comprender cómo funciona la estructura de selección múltiple `switch` para evaluar una expresión entera y dirigir el flujo del programa hacia diferentes bloques de código de forma limpia y ordenada, utilizando el estándar **C99**.

 

#### Explicación teórica y analogía

Imagina que estás en una máquina expendedora de boletos de metro: en lugar de preguntar uno por uno *"¿quieres la línea 1? ¿quieres la línea 2? ¿quieres la línea 3?"* con muchos `if` anidados, la máquina tiene botones específicos y tú presionas uno solo. El sistema mira exactamente qué botón presionaste y salta directo al mecanismo correspondiente a esa opción.

En C, la instrucción `switch` actúa de esa manera cuando necesitamos comparar una misma variable contra múltiples valores constantes enteros o caracteres posibles. Es una alternativa mucho más limpia y legible que escribir una cadena interminable de `if-else if`.

 

#### Ejemplo de código comentado

Aquí tienes un programa completo, compilable y funcional que utiliza un `switch` para identificar el tipo de día de la semana según un número del 1 al 5:

```c
#include <stdio.h>

int main(void) {
    int dia = 3;

    switch (dia) {
        case 1:
            printf("Lunes: Inicio de semana laboral.\n");
            break;
        case 2:
            printf("Martes: ¡A seguir avanzando!\n");
            break;
        case 3:
            printf("Miércoles: ¡Mitad de semana!\n");
            break;
        case 4:
            printf("Jueves: Casi es viernes.\n");
            break;
        case 5:
            printf("Viernes: ¡Último día de la semana!\n");
            break;
        default:
            printf("Número no válido. Debe ser entre 1 y 5.\n");
            break;
    }

    return 0;
}

```

 

#### Explicación línea por línea

* `switch (dia)`: Evalúa el valor contenido en la variable `dia`. El resultado debe ser de tipo entero (o un carácter convertible a entero).


* `case 1:`: Es una etiqueta constante. Si el valor de `dia` coincide con `1`, la ejecución entra y comienza a ejecutar el código a partir de ese punto.


* `break;`: Es fundamental. Obliga al programa a salir inmediatamente del `switch`, evitando que la ejecución "caiga" (*fall through*) por inercia hacia los siguientes casos.


* `default:`: Es la opción por defecto. Se ejecuta de manera automática si el valor evaluado no coincide con ninguno de los `case` anteriores.



 

#### Errores comunes y cómo evitarlos

1. **Olvidar la instrucción `break**`: Si omites el `break` al final de un caso, C continuará ejecutando los códigos de los casos siguientes en cascada (*fall through*), lo cual suele generar errores de lógica graves.


2. **Usar valores no constantes o decimales en los `case**`: Las etiquetas de los `case` deben ser valores enteros constantes evaluables en tiempo de compilación; no puedes poner variables dinámicas ni números con decimales (`float`/`double`).



 

#### Ejercicio práctico propuesto

Escribe un programa en C que solicite al usuario ingresar una letra mayúscula que represente una calificación (`A`, `B`, `C`, `D` o `F`). Utiliza una estructura `switch` para imprimir un mensaje descriptivo para cada letra (por ejemplo: `A` -> "Excelente", `B` -> "Notable", etc.), incluyendo un caso `default` para calificaciones inválidas.

> **Pista para el ejercicio:**
> * Recuerda que los caracteres en C se pueden usar en los `case` encerrándolos entre comillas simples, por ejemplo: `case 'A':`.
> * No olvides colocar un `break;` al finalizar cada caso para mantener el control correcto de la selección.
> 
> 
> 
> 

 

#### Mini resumen

* La instrucción `switch` permite implementar selecciones múltiples de forma limpia cuando evaluamos una variable entera o carácter frente a valores constantes.


* Cada `case` funciona como una etiqueta de salto y el bloque se cierra habitualmente con un `break` para salir de la estructura.


* El bloque opcional `default` maneja cualquier valor que no haya encajado en los casos anteriores.


### Menú Interactivo en C sin Usar `switch`, `case` ni `break`

#### Objetivo de aprendizaje

Aprender a implementar un menú interactivo repetitivo utilizando un bucle `while` combinado con una cadena de decisiones `if - else if - else`, controlando el flujo y la salida del programa de manera natural sin depender de las instrucciones `switch`, `case` o `break`. Usaremos el estándar **C99**.

 

#### Explicación teórica y clara

Cuando diseñamos un menú interactivo, necesitamos cumplir dos requisitos:

1. **Persistencia:** Que el programa se mantenga en ejecución mostrando las opciones tantas veces como el usuario lo requiera.
2. **Selección múltiple:** Que el programa tome un camino diferente según el número o la opción que elija el usuario.

Aunque el bloque `switch` y la instrucción `break` son herramientas comunes para esto, podemos lograr exactamente el mismo resultado utilizando una estructura de control secuencial con `if - else if - else`. En lugar de usar un `break` para salir drásticamente del ciclo, hacemos que la propia condición del bucle `while` evalúe cuándo debe terminar el programa de forma limpia y controlada.

 

#### Ejemplo de código comentado

Aquí tienes un programa completo, compilable y funcional que implementa un menú de opciones utilizando exclusivamente un bucle `while` y condiciones `if-else`:

```c
#include <stdio.h>

int main(void) {
    int opcion = 0; // Variable de control para el menú

    // El bucle se mantiene activo mientras la opción sea diferente de 3 (Salir)
    while (opcion != 3) {
        // Desplegamos el menú en la consola
        printf("\n  MENU PRINCIPAL (SIN SWITCH)  \n");
        printf("1. Saludar al usuario\n");
        printf("2. Mostrar un mensaje de motivacion\n");
        printf("3. Salir del programa\n");
        printf("Elige una opcion (1-3): ");
        
        // Leemos la opción ingresada por el usuario
        scanf("%d", &opcion);

        // Evaluamos las opciones usando una cadena if - else if - else
        if (opcion == 1) {
            printf("\n> ¡Hola! Qué gusto tenerte por aquí practicando lenguaje C.\n");
        } 
        else if (opcion == 2) {
            printf("\n> ¡Tú puedes lograrlo! La práctica constante hace al maestro.\n");
        } 
        else if (opcion == 3) {
            printf("\n> Cerrando el programa. ¡Hasta pronto!\n");
        } 
        else {
            // Manejo de errores para cualquier número fuera del rango válido
            printf("\n> [Error] Opción no válida. Por favor, elige un número entre 1 y 3.\n");
        }
    } // Fin del while. Si opcion == 3, la condición se vuelve falsa y el programa termina de forma natural.

    return 0;
}

```

 

#### Explicación línea por línea

* `int opcion = 0;`: Inicializamos la variable en `0` para garantizar que la condición del `while` (`opcion != 3`) sea verdadera desde el inicio y el menú se muestre por primera vez.
* `while (opcion != 3)`: Funciona como el guardián del ciclo. Evaluará la condición antes de cada iteración; si el usuario elige la opción `3`, la condición se vuelve falsa y el bucle finaliza sin necesidad de usar un `break`.
* `if (opcion == 1)`: Evalúa si el usuario seleccionó la primera alternativa, ejecutando su bloque exclusivo si se cumple.
* `else if (opcion == 2)` y `else if (opcion == 3)`: Forman la cadena de decisiones sucesivas para evaluar el resto de los caminos válidos.
* `else`: Captura cualquier valor numérico incorrecto (como un `5` o un `-1`), informándole al usuario del error sin interrumpir ni cerrar el menú por la fuerza.

 

#### Errores comunes y cómo evitarlos

1. **Olvidar actualizar la variable de control:** Si omites la instrucción `scanf("%d", &opcion);` dentro del bucle, la variable nunca cambiará de valor, atrapando al programa en un ciclo infinito estático.
2. **Mal uso de la lógica de exclusión:** Al usar una cadena `if - else if`, asegúrate de que las condiciones sean excluyentes entre sí para evitar que se ejecuten múltiples bloques por error.

 

#### Ejercicio práctico propuesto

Escribe un programa en C que implemente un menú interactivo de tres opciones utilizando exclusivamente `while` y `if-else if-else` (sin usar `switch`, `case` ni `break`):

1. **Calcular el doble de un número** (pide un entero al usuario y muestra su valor multiplicado por 2).
2. **Verificar si un número es par o impar** (pide un entero y usa el operador módulo `%` para indicarlo).
3. **Salir**.

> **Pista para el ejercicio:**
> * Diseña tu bucle `while` para que se repita mientras la opción sea diferente de `3`.
> * Dentro del `if` o `else if` correspondiente a la opción 1 o 2, puedes declarar variables locales temporales y solicitar un nuevo número mediante `scanf` para realizar la operación matemática antes de volver a mostrar el menú.
> 
> 

 

#### Mini resumen

* Es totalmente posible construir menús robustos y limpios combinando un bucle `while` con una estructura de selección en cascada `if - else if - else`.
* La salida del ciclo se controla de manera natural evaluando la variable de entrada directamente en la cabecera del `while`, evitando saltos artificiales.
* El bloque `else` final actúa como una red de seguridad infalible para procesar entradas erróneas del usuario.

### Métodos de Ordenamiento en Lenguaje C (Algoritmo de Burbuja)

#### Objetivo de aprendizaje

Comprender la lógica fundamental detrás de los algoritmos para ordenar elementos dentro de un arreglo en C, implementando el método clásico de la burbuja (*Bubble Sort*) bajo el estándar **C99**.

 

#### Explicación teórica y analogía

Imagina que tienes una fila de vasos de diferentes tamaños sobre una mesa y quieres ordenarlos del más pequeño al más grande. Vas revisando la fila de dos en dos: comparas el primer vaso con el segundo; si el primero es más grande que el segundo, los intercambias de posición. Luego comparas el segundo con el tercero, y así sucesivamente hasta llegar al final. Al terminar la primera pasada, el vaso más grande habrá "flotado" hasta el final de la fila, exactamente como una burbuja de aire sube a la superficie del agua. Si repites el proceso varias veces, toda la fila quedará perfectamente ordenada.

En el lenguaje C, los arreglos no se pueden ordenar con una simple instrucción automática global; necesitamos diseñar la lógica de comparación y el intercambio (*swap*) utilizando bucles anidados que recorran las posiciones de la memoria del arreglo.

 

#### Ejemplo de código comentado

Aquí tienes un programa completo, compilable y funcional que implementa el algoritmo de ordenamiento burbuja para ordenar un arreglo de números enteros de menor a mayor:

```c
#include <stdio.h>

int main(void) {
    // Declaramos e inicializamos un arreglo desordenado de 5 enteros
    int numeros[] = {34, 12, 5, 67, 23};
    int n = 5; // Cantidad de elementos del arreglo
    int temp;  // Variable temporal para realizar los intercambios

    printf("  ARREGLO ORIGINAL  \n");
    for (int i = 0; i < n; i++) {
        printf("%d ", numeros[i]);
    }
    printf("\n");

    // Algoritmo de Ordenamiento Burbuja (Bubble Sort)
    // Bucle externo: controla el número de pasadas
    for (int i = 0; i < n - 1; i++) {
        // Bucle interno: realiza las comparaciones adyacentes
        for (int j = 0; j < n - 1 - i; j++) {
            // Si el elemento actual es mayor que el siguiente, los intercambiamos
            if (numeros[j] > numeros[j + 1]) {
                temp = numeros[j];          // Guardamos temporalmente el valor actual
                numeros[j] = numeros[j + 1];  // Movemos el menor hacia la izquierda
                numeros[j + 1] = temp;        // Colocamos el mayor a la derecha
            }
        }
    }

    // Imprimimos el arreglo ya ordenado
    printf("\n  ARREGLO ORDENADO (DE MENOR A MAYOR)  \n");
    for (int i = 0; i < n; i++) {
        printf("%d ", numeros[i]);
    }
    printf("\n");

    return 0;
}

```

 

#### Explicación línea por línea

* `for (int i = 0; i < n - 1; i++)`: El bucle externo controla cuántas pasadas completas realizaremos sobre el arreglo. Se repite `n - 1` veces porque en cada pasada aseguramos que el elemento mayor restante quede ubicado en su posición definitiva al final.
* `for (int j = 0; j < n - 1 - i; j++)`: El bucle interno recorre los elementos adyacentes. Restamos `- i` porque en cada pasada el extremo derecho del arreglo ya queda ordenado, por lo que no es necesario volver a evaluarlo.
* `if (numeros[j] > numeros[j + 1])`: Compara dos elementos contiguos. Si el de la izquierda es mayor que el de la derecha, el orden es incorrecto y se debe proceder al intercambio.
* `temp = numeros[j]; numeros[j] = numeros[j + 1]; numeros[j + 1] = temp;`: Las tres líneas clásicas de **intercambio (*swap*)**. Sin la variable auxiliar `temp`, al sobrescribir `numeros[j]` perderíamos su valor original.

 

#### Errores comunes y cómo evitarlos

1. **Desbordamiento de índices (*Buffer Overflow / Out of bounds*)**: Al comparar `numeros[j]` con `numeros[j + 1]`, asegúrate de que el límite del bucle interno evite que `j + 1` rebase el tamaño máximo del arreglo, de lo contrario leerás memoria basura o provocarás fallos.
2. **Olvidar la variable temporal en el intercambio**: Intentar hacer un intercambio directo escribiendo `a = b; b = a;` destruye uno de los valores. Siempre se requiere una variable auxiliar (`temp`) para respaldar los datos durante el proceso.

 

#### Ejercicio práctico propuesto

Escribe un programa en C que declare un arreglo de 6 números enteros con valores desordenados ingresados por el usuario (o predefinidos). Modifica la lógica del algoritmo de burbuja para que el arreglo quede ordenado **de mayor a menor** en lugar de menor a mayor.

> **Pista para el ejercicio:**
> * Analiza el operador de comparación dentro del `if`: si actualmente buscas que el número mayor avance hacia la derecha usando `>`), ¿qué operador deberías usar para que los números más pequeños queden a la derecha y los mayores asciendan al principio?
> 
> 

 

#### Mini resumen

* Los métodos de ordenamiento permiten organizar los datos de un arreglo de manera lógica (ascendente o descendente).
* El **ordenamiento burbuja** compara elementos adyacentes repetidamente y los intercambia si están desordenados, requiriendo bucles anidados.
* El intercambio seguro de dos variables en C siempre exige el uso de una variable temporal auxiliar.

### Otros Métodos de Ordenamiento Populares en Lenguaje C

#### Objetivo de aprendizaje

Conocer la existencia y el propósito de otros algoritmos fundamentales para ordenar arreglos en C (como *Selection Sort*, *Insertion Sort* y el algoritmo avanzado *Quicksort*), entendiendo cuándo y por qué elegir alternativas al ordenamiento burbuja.

 

#### Explicación teórica y analogía

Imagina que estás organizando una baraja de cartas sobre la mesa:

* **Ordenamiento por Selección (*Selection Sort*):** Miras todo el montón de cartas, buscas la carta más baja de todas, la extraes y la pones en la primera posición. Luego buscas la menor entre las que quedan y la colocas en la segunda posición, repitiendo el proceso hasta terminar.
* **Ordenamiento por Inserción (*Insertion Sort*):** Vas tomando las cartas una por una desde el montón desordenado y las vas insertando en su lugar correcto dentro de una mano de cartas que ya tienes ordenada (exactamente como acomodas cartas en tu mano cuando juegas póker).
* **Quicksort (Ordenamiento Rápido):** Tomas una carta como "pivote" de referencia, divides el montón en dos grupos (las cartas menores a la izquierda y las mayores a la derecha) y repites el proceso recursivamente en cada grupo pequeño. Es el método por excelencia que utiliza por dentro la función estándar `qsort()` de C.



 

#### 1. Ordenamiento por Selección (*Selection Sort*)

Este método busca el elemento más pequeño de todo el arreglo y lo coloca al principio mediante un intercambio, repitiendo el proceso para el resto del arreglo.

```c
#include <stdio.h>

int main(void) {
    int numeros[] = {29, 10, 14, 37, 13};
    int n = 5;

    // Algoritmo de Selección
    for (int i = 0; i < n - 1; i++) {
        int min_idx = i; // Suponemos que el actual es el menor
        
        for (int j = i + 1; j < n; j++) {
            if (numeros[j] < numeros[min_idx]) {
                min_idx = j; // Actualizamos el índice del verdadero menor
            }
        }
        
        // Intercambiamos el menor encontrado con el primer elemento de la pasada
        if (min_idx != i) {
            int temp = numeros[i];
            numeros[i] = numeros[min_idx];
            numeros[min_idx] = temp;
        }
    }

    printf("  ORDENADO POR SELECCIÓN  \n");
    for (int i = 0; i < n; i++) {
        printf("%d ", numeros[i]);
    }
    printf("\n");

    return 0;
}

```

 

#### 2. La solución avanzada integrada en C: `qsort`

Como programador en C, rara vez necesitas escribir algoritmos complejos de ordenamiento desde cero para proyectos grandes. La biblioteca estándar `<stdlib.h>` incluye una implementación altamente optimizada basada en *Quicksort* a través de la función `qsort`:

```c
#include <stdio.h>
#include <stdlib.h> // Necesario para qsort

// Función de comparación requerida por qsort
int comparar_enteros(const void *a, const void *b) {
    int int_a = *((int *)a);
    int int_b = *((int *)b);
    return (int_a - int_b);
}

int main(void) {
    int numeros[] = {42, 7, 19, 3, 85};
    int n = 5;

    // Llamada a la función qsort de la biblioteca estándar
    qsort(numeros, n, sizeof(int), comparar_enteros);

    printf("  ORDENADO CON QSORT DE LA BIBLIOTECA ESTÁNDAR  \n");
    for (int i = 0; i < n; i++) {
        printf("%d ", numeros[i]);
    }
    printf("\n");

    return 0;
}

```

 

#### Errores comunes y cómo evitarlos

1. **Olvidar la función de comparación en `qsort**`: La función nativa de C necesita saber cómo comparar tus datos específicos, por lo que debes escribir una pequeña función auxiliar de comparación que devuelva un valor negativo, cero o positivo.


2. **Confundir la complejidad**: Algoritmos como Burbuja y Selección son excelentes para aprender lógica y arreglos pequeños, pero ineficientes ($O(n^2)$) con miles de datos en comparación con algoritmos avanzados como Quicksort ($O(n \log n)$).

 

#### Ejercicio práctico propuesto

Escribe un programa en C que implemente el algoritmo de **Inserción (*Insertion Sort*)** para ordenar un arreglo de 5 números enteros de menor a mayor.

> **Pista para el ejercicio:**
> * Guarda el valor del elemento actual en una variable temporal (por ejemplo, `key = numeros[i]`).
> * Utiliza un bucle hacia atrás para desplazar hacia la derecha los elementos que sean mayores que `key` antes de insertarlo en su posición correcta.
> 
> 

 

#### Mini resumen

* Además de la burbuja, existen métodos clásicos como **Selección** e **Inserción** ideales para comprender la manipulación de índices y arreglos.


* Para aplicaciones reales y eficientes en C, la biblioteca estándar provee la función universal `qsort` dentro de `<stdlib.h>`.


### Implementación de Quicksort en C sin usar `qsort`

#### Objetivo de aprendizaje

Comprender el funcionamiento del algoritmo de ordenamiento rápido (*Quicksort*) mediante la técnica de divide y vencerás y la recursividad, implementándolo desde cero en lenguaje C utilizando el estándar **C99**.

 

#### Explicación teórica y analogía

Imagina que tienes una baraja de cartas revueltas sobre la mesa y quieres ordenarlas rápidamente:

1. **El pivote:** Eliges una carta del centro para que sirva de referencia (por ejemplo, un 7).
2. **Partición:** Agrupas todas las cartas menores que 7 a tu izquierda y todas las mayores o iguales a tu derecha.
3. **Recursividad:** Ahora tienes dos montones más pequeños (los menores y los mayores). Repites exactamente el mismo proceso en cada montón de manera independiente hasta que cada sub-pila tenga una o cero cartas. ¡Listo, la baraja completa queda ordenada!

En programación, **Quicksort** es uno de los algoritmos más eficientes y elegantes para ordenar arreglos grandes mediante **recursividad** (una función que se llama a sí misma para resolver sub-problemas más pequeños).

 

#### Ejemplo de código comentado

Aquí tienes un programa completo, compilable y funcional basado en la estructura clásica de Quicksort para ordenar un arreglo de enteros de menor a mayor:

```c
#include <stdio.h>

// Declaración de funciones
void quicksort(int v[], int left, int right);
void swap(int v[], int i, int j);

int main(void) {
    int numeros[] = {33, 10, 55, 7, 21, 89, 42};
    int n = 7;

    printf("  ARREGLO ORIGINAL  \n");
    for (int i = 0; i < n; i++) {
        printf("%d ", numeros[i]);
    }
    printf("\n");

    // Llamamos a nuestro quicksort implementado desde cero
    quicksort(numeros, 0, n - 1);

    printf("\n  ARREGLO ORDENADO CON QUICKSORT  \n");
    for (int i = 0; i < n; i++) {
        printf("%d ", numeros[i]);
    }
    printf("\n");

    return 0;
}

// Función principal de Quicksort (Divide y Vencerás)
void quicksort(int v[], int left, int right) {
    int i, last;

    // Caso base: si el sub-arreglo tiene 0 o 1 elemento, ya está ordenado[cite: 1]
    if (left >= right)
        return;

    // Movemos el elemento central al inicio (left) como nuestro pivote temporal[cite: 1]
    swap(v, left, (left + right) / 2);
    last = left;

    // Partición: reorganizamos los elementos en torno al pivote[cite: 1]
    for (i = left + 1; i <= right; i++) {
        if (v[i] < v[left]) {
            swap(v, ++last, i);
        }
    }

    // Colocamos el pivote en su posición definitiva (en 'last')[cite: 1]
    swap(v, left, last);

    // Llamadas recursivas para ordenar la mitad izquierda y la mitad derecha[cite: 1]
    quicksort(v, left, last - 1);
    quicksort(v, last + 1, right);
}

// Función auxiliar para intercambiar dos elementos del arreglo[cite: 1]
void swap(int v[], int i, int j) {
    int temp = v[i];
    v[i] = v[j];
    v[j] = temp;
}

```

 

#### Explicación línea por línea

* `if (left >= right) return;`: Es el **caso base** de la recursividad. Si los índices se cruzan o son iguales, significa que el sub-segmento tiene 0 o 1 elemento, por lo que la función se detiene de inmediato.


* `swap(v, left, (left + right) / 2);`: Toma el elemento ubicado exactamente en la mitad del sub-arreglo y lo desplaza al extremo izquierdo (`left`) para usarlo como pivote de referencia.


* `for (i = left + 1; i <= right; i++)`: Recorre el resto del sub-arreglo comparando cada elemento con el valor del pivote. Si encuentra un número menor, incrementa `last` y realiza un intercambio para agruparlo a la izquierda.


* `quicksort(v, left, last - 1);` y `quicksort(v, last + 1, right);`: Las llamadas recursivas que dividen el problema en dos mitades (los elementos menores al pivote y los mayores), reduciendo drásticamente el costo computacional con una complejidad promedio de $O(n \log n)$.



 

#### Errores comunes y cómo evitarlos

1. **Olvidar el caso base:** Si omites la validación `if (left >= right) return;`, la función entrará en una recursividad infinita hasta agotar la memoria de la pila (*stack overflow*).
2. **Desbordamiento de índices en arreglos grandes:** Asegúrate de pasar los límites correctos (`0` para el inicio y `n - 1` para el final) al invocar `quicksort` por primera vez.

 

#### Ejercicio práctico propuesto

Modifica el programa anterior para que ordene el arreglo de enteros **de mayor a menor** en lugar de menor a mayor.

> **Pista para el ejercicio:**
> * Analiza la condición de partición dentro del bucle `for`: `if (v[i] < v[left])`. Si quieres que los números más grandes queden en la primera mitad y los menores a la derecha, ¿cómo debería cambiar ese operador de comparación (`<` vs `>`)?
> 
> 
> 
> 

 

#### Mini resumen

* **Quicksort** implementa la estrategia de "divide y vencerás" utilizando funciones recursivas para ordenar arreglos de manera altamente eficiente.


* No requiere de funciones de bibliotecas externas como `qsort`, permitiéndote controlar la lógica interna de partición e intercambio mediante un pivote.
* Todo algoritmo recursivo necesita obligatoriamente un **caso base** bien definido para evitar bucles infinitos de llamadas.


### Listas Enlazadas Dinámicas en C: Agregar, Recorrer y Liberar Elementos

#### Objetivo de aprendizaje

Aprender a construir y manipular una **lista enlazada simple** utilizando estructuras (`struct`), punteros y asignación dinámica de memoria (`malloc` y `free`), comprendiendo cómo agregar elementos, recorrerlos y liberar los recursos correctamente bajo el estándar **C99**.

 

#### Explicación teórica y analogía

Imagina que estás participando en una búsqueda del tesoro: encuentras la primera pista, la anotas, y esa misma pista te dice exactamente en qué lugar exacto se encuentra la siguiente. Cada pista es independiente, no necesita estar contigua en una misma libreta, y puedes seguir la cadena hasta llegar al final (marcado con una señal de fin o `NULL`).

En C, a diferencia de los arreglos estáticos que requieren un bloque de memoria fijo y contiguo, una **lista enlazada** es una estructura de datos dinámica compuesta por nodos. Cada nodo contiene dos partes esenciales:

1. **El dato** (por ejemplo, un número entero).
2. **Un puntero** que apunta al siguiente nodo de la lista.



 

#### Ejemplo de código comentado

Aquí tienes un programa completo, compilable y funcional que crea una estructura de nodo, agrega elementos al frente de la lista utilizando memoria dinámica y recorre la estructura para mostrar los valores acumulados:

```c
#include <stdio.h>
#include <stdlib.h> // Necesario para malloc y free

// 1. Definición de la estructura del Nodo
struct Nodo {
    int dato;
    struct Nodo *sig; // Puntero que apunta al siguiente nodo de la lista
};

// Función para agregar un nuevo elemento al inicio de la lista
struct Nodo* agregarAlFrente(struct Nodo *cabeza, int nuevoDato) {
    // Reservamos memoria dinámica para un nuevo nodo
    struct Nodo *nuevoNodo = (struct Nodo *) malloc(sizeof(struct Nodo));
    
    if (nuevoNodo == NULL) {
        printf("Error: No hay memoria disponible.\n");
        return cabeza;
    }

    nuevoNodo->dato = nuevoDato;  // Asignamos el valor
    nuevoNodo->sig = cabeza;      // El nuevo nodo apunta al que antes era el primero
    
    return nuevoNodo;             // El nuevo nodo pasa a ser la nueva cabeza de la lista
}

// Función para recorrer e imprimir los elementos de la lista
void imprimirLista(struct Nodo *cabeza) {
    struct Nodo *actual = cabeza;
    printf("  CONTENIDO DE LA LISTA  \n");
    while (actual != NULL) {
        printf("[%d] -> ", actual->dato);
        actual = actual->sig; // Avanzamos al siguiente nodo
    }
    printf("NULL\n\n");
}

// Función para liberar toda la memoria de la lista dinámicamente
void liberarLista(struct Nodo *cabeza) {
    struct Nodo *temporal;
    while (cabeza != NULL) {
        temporal = cabeza;       // Guardamos el nodo actual
        cabeza = cabeza->sig;    // Avanzamos al siguiente
        free(temporal);          // Liberamos la memoria del nodo guardado
    }
}

int main(void) {
    struct Nodo *miLista = NULL; // Inicializamos la lista vacía

    // Agregamos elementos a nuestra lista
    miLista = agregarAlFrente(miLista, 10);
    miLista = agregarAlFrente(miLista, 20);
    miLista = agregarAlFrente(miLista, 30);

    // Mostramos la lista en pantalla
    imprimirLista(miLista);

    // Liberamos la memoria antes de finalizar el programa para evitar fugas (*memory leaks*)
    liberarLista(miLista);

    return 0;
}

```

 

#### Explicación línea por línea

* `struct Nodo *sig;`: Es un puntero autorreferenciado; un campo dentro de la estructura que es capaz de almacenar la dirección de memoria de otro nodo del mismo tipo.
* `malloc(sizeof(struct Nodo));`: Solicita al sistema operativo un bloque de memoria en el *heap* (montón) con el tamaño exacto requerido para albergar un nuevo nodo.


* `nuevoNodo->sig = cabeza;`: Conecta el nuevo nodo con el resto de la lista existente, situándose justo antes del primer elemento anterior.
* `while (actual != NULL)`: Es el modismo clásico de recorrido para caminar por los nodos de una lista enlazada hasta encontrar el final marcado por `NULL`.


* `free(temporal);`: Devuelve al sistema la memoria reservada dinámicamente una vez que dejamos de usarla, previniendo fugas de memoria.



 

#### Errores comunes y cómo evitarlos

1. **Perder la referencia a la cabeza de la lista:** Si modificas el puntero principal que apunta al inicio de la lista sin guardar una referencia temporal, perderás el acceso a todos los nodos subsiguientes, provocando una fuga masiva de memoria inalcanzable.
2. **Intentar acceder a un nodo ya liberado:** Una vez que ejecutas `free()` sobre un nodo, sus datos dejan de ser seguros. Acceder a ellos causará un fallo de segmentación (*Segmentation Fault*). Asegúrate de liberar la memoria siempre al final y en el orden correcto (guardando el enlace al siguiente antes de borrar el actual).

 

#### Ejercicio práctico propuesto

Escribe una función adicional llamada `agregarAlFinal(struct Nodo *cabeza, int nuevoDato)` que recorra la lista enlazada hasta encontrar el último nodo (aquel cuyo campo `sig` sea `NULL`) y enganche el nuevo nodo creado al final de la secuencia, en lugar de al principio.

> **Pista para el ejercicio:**
> * Si la lista está vacía (`cabeza == NULL`), el nuevo nodo se convierte directamente en la cabeza.
> * Si no está vacía, utiliza un puntero auxiliar para recorrer nodo por nodo hasta que `auxiliar->sig == NULL`, momento en el cual actualizas ese enlace para que apunte al nuevo nodo.
> 
> 

 

#### Mini resumen

* Las listas enlazadas superan las limitaciones de tamaño fijo de los arreglos mediante nodos conectados dinámicamente por punteros en memoria.


* Cada nodo agrupa datos y la dirección de memoria del siguiente elemento, cerrando la cadena con un valor `NULL`.
* El uso de `malloc` para agregar elementos y `free` para limpiar la estructura es obligatorio para mantener la eficiencia y salud de la memoria del programa.


### Pilas en C (*Stacks*: Agregar, Borrar y Consultar Elementos)

#### Objetivo de aprendizaje

Comprender el concepto y el funcionamiento de una estructura de datos tipo **Pila (Stack)** bajo el principio LIFO (*Last-In, First-Out*), implementando las operaciones fundamentales de inserción (`push`), eliminación (`pop`) y consulta (`peek`) utilizando arreglos y punteros bajo el estándar **C99**.

 

#### Explicación teórica y analogía

Imagina una pila de platos en una cafetería: cuando lavas un plato y lo guardas, lo colocas **encima** de los demás (operación **push** o apilar). Cuando necesitas un plato para servir comida, siempre tomas el que está hasta arriba, es decir, el **último** que se colocó (operación **pop** o desapilar). Si quieres ver qué plato está arriba sin retirarlo, simplemente echas un vistazo a la cima (operación **peek**).

A este comportamiento se le conoce técnicamente como **LIFO** (*Last-In, First-Out* / Último en entrar, primero en salir). En C, una pila se puede implementar fácilmente utilizando un arreglo unidimensional junto con una variable de control llamada comúnmente tope (*sp* o *stack pointer*).

 

#### Ejemplo de código comentado

Aquí tienes un programa completo, compilable y funcional que implementa una pila de enteros utilizando una estructura y un arreglo estático:

```c
#include <stdio.h>
#define MAX 5 // Tamaño máximo de la pila

// Estructura para representar la Pila
struct Pila {
    int elementos[MAX];
    int tope; // Índice del último elemento agregado
};

// Inicializar la pila
void inicializar(struct Pila *p) {
    p->tope = -1; // -1 indica que la pila está vacía
}

// Verificar si la pila está llena
int estaLlena(struct Pila *p) {
    return p->tope == MAX - 1;
}

// Verificar si la pila está vacía
int estaVacia(struct Pila *p) {
    return p->tope == -1;
}

// 1. AGREGAR un elemento (PUSH)
void push(struct Pila *p, int valor) {
    if (estaLlena(p)) {
        printf("Error: Desbordamiento de pila (Stack Overflow).\n");
    } else {
        p->tope++;
        p->elementos[p->tope] = valor;
        printf("Elemento %d apilado con exito.\n", valor);
    }
}

// 2. BORRAR / EXTRAER un elemento (POP)
int pop(struct Pila *p) {
    if (estaVacia(p)) {
        printf("Error: Subdesbordamiento de pila (Stack Underflow).\n");
        return -1; // Valor de error
    } else {
        int valorEliminado = p->elementos[p->tope];
        p->tope--; // Reducimos el tope para "eliminar" lógicamente el elemento
        return valorEliminado;
    }
}

// 3. CONSULTAR el elemento en la cima sin borrarlo (PEEK)
int peek(struct Pila *p) {
    if (estaVacia(p)) {
        printf("La pila esta vacia.\n");
        return -1;
    }
    return p->elementos[p->tope];
}

int main(void) {
    struct Pila miPila;
    inicializar(&miPila);

    // Agregando elementos (Push)
    push(&miPila, 10);
    push(&miPila, 20);
    push(&miPila, 30);

    // Consultando la cima (Peek)
    printf("Elemento en la cima (Peek): %d\n", peek(&miPila));

    // Borrando elementos (Pop)
    printf("Elemento extraido (Pop): %d\n", pop(&miPila));
    printf("Elemento extraido (Pop): %d\n", pop(&miPila));

    // Consultando nuevamente la cima
    printf("Nueva cima despues de los pops: %d\n", peek(&miPila));

    return 0;
}

```

 

#### Explicación línea por línea

* `#define MAX 5`: Establece la capacidad máxima fija que soportará nuestra estructura de datos.
* `p->tope = -1;`: Inicializa la variable de control `tope` en `-1` para señalar que la pila no contiene ningún elemento todavía.
* `p->tope++; p->elementos[p->tope] = valor;`: Incrementa la posición del índice y almacena el nuevo valor en esa ranura exacta del arreglo.
* `p->tope--;`: Descuenta una unidad al índice del tope, haciendo que el elemento anterior quede fuera del alcance activo de la pila (eliminación lógica).
* `&miPila`: Envía la dirección de memoria de la estructura a las funciones mediante un puntero (`struct Pila *p`), permitiendo modificar directamente sus valores originales.

 

#### Errores comunes y cómo evitarlos

1. **Desbordamiento de pila (*Stack Overflow*)**: Intentar hacer un `push` en una pila que ya alcanzó su capacidad máxima (`MAX`), lo que sobrescribiría memoria no asignada si no se valida con una función como `estaLlena()`.
2. **Subdesbordamiento (*Stack Underflow*)**: Intentar extraer o consultar un elemento (`pop` o `peek`) de una pila totalmente vacía, lo que generaría lecturas de datos erróneos.

 

#### Ejercicio práctico propuesto

Escribe una función adicional llamada `mostrarPila(struct Pila *p)` que recorra e imprima todos los elementos contenidos en la pila desde la cima hasta el fondo, sin alterar el estado actual de la estructura.

> **Pista para el ejercicio:**
> * Utiliza un bucle `for` que comience desde el índice `p->tope` y decremente de forma segura hasta llegar a `0`.
> 
> 

 

#### Mini resumen

* Una pila (*Stack*) sigue estrictamente el principio **LIFO** (*Last-In, First-Out*).
* Sus operaciones esenciales son **push** (agregar a la cima), **pop** (extraer de la cima) y **peek** (consultar la cima sin extraer).
* Es fundamental validar los límites de la pila antes de operar para prevenir errores de desbordamiento o subdesbordamiento.

### Colas en C (*Queues*: FIFO)

#### Objetivo de aprendizaje

Comprender el concepto y el funcionamiento de una estructura de datos tipo **Cola (Queue)** bajo el principio FIFO (*First-In, First-Out*), implementando las operaciones fundamentales de inserción (`enqueue`) y extracción (`dequeue`) utilizando arreglos y punteros bajo el estándar **C99**.

 

#### Explicación teórica y analogía

Imagina que estás formado en la taquilla del cine o en la fila del banco: la primera persona que llega a formarse es la primera en ser atendida y salir del servicio; las personas que van llegando se forman estrictamente al final. A este comportamiento se le conoce como **FIFO** (*First-In, First-Out* / Primero en entrar, primero en salir).

A diferencia de una pila (*Stack*) donde todo ocurre en la misma cima, en una cola interactuamos por **dos extremos diferentes**:

1. **El final (*Rear* o *Enqueue*):** Por donde ingresan los nuevos elementos.
2. **El frente (*Front* o *Dequeue*):** Por donde se retiran los elementos para ser procesados.

 

#### Ejemplo de código comentado

Aquí tienes un programa completo, compilable y funcional que implementa una cola lineal estática para almacenar números enteros:

```c
#include <stdio.h>
#define MAX 5 // Capacidad máxima de la cola

// Estructura para representar la Cola
struct Cola {
    int elementos[MAX];
    int frente;
    int final;
};

// Inicializar la cola
void inicializar(struct Cola *q) {
    q->frente = 0;
    q->final = -1;
}

// Verificar si la cola está llena
int estaLlena(struct Cola *q) {
    return q->final == MAX - 1;
}

// Verificar si la cola está vacía
int estaVacia(struct Cola *q) {
    return q->frente > q->final;
}

// 1. AGREGAR un elemento al final (Enqueue / Encolar)
void encolar(struct Cola *q, int valor) {
    if (estaLlena(q)) {
        printf("Error: La cola esta llena (Queue Overflow).\n");
    } else {
        q->final++;
        q->elementos[q->final] = valor;
        printf("Elemento %d agregado a la cola.\n", valor);
    }
}

// 2. BORRAR / EXTRAER un elemento del frente (Dequeue / Desencolar)
int desencolar(struct Cola *q) {
    if (estaVacia(q)) {
        printf("Error: La cola esta vacia (Queue Underflow).\n");
        return -1; // Valor de error
    } else {
        int valorEliminado = q->elementos[q->frente];
        q->frente++; // Avanzamos el frente para retirar lógicamente el elemento
        return valorEliminado;
    }
}

int main(void) {
    struct Cola miCola;
    inicializar(&miCola);

    // Agregando elementos (Encolar)
    encolar(&miCola, 10);
    encolar(&miCola, 20);
    encolar(&miCola, 30);

    // Extrayendo elementos (Desencolar)
    printf("Elemento atendido: %d\n", desencolar(&miCola));
    printf("Elemento atendido: %d\n", desencolar(&miCola));

    // Agregando otro elemento para verificar el flujo
    encolar(&miCola, 40);

    return 0;
}

```

 

#### Explicación línea por línea

* `int frente; int final;`: Son las dos variables de control (índices). `frente` apunta al próximo elemento que va a salir, y `final` apunta al último elemento que acaba de entrar.
* `q->frente = 0; q->final = -1;`: Configuran el estado inicial de una cola vacía.
* `q->final++; q->elementos[q->final] = valor;`: Desplaza el índice del final hacia adelante y guarda el nuevo dato en esa ranura del arreglo.
* `q->frente++;`: Mueve el índice del frente una posición hacia adelante, descartando lógicamente al elemento que acaba de ser atendido.

 

#### Errores comunes y cómo evitarlos

1. **Desperdicio de espacio en colas lineales simples**: A medida que hacemos `desencolar`, el índice `frente` avanza y las posiciones anteriores quedan inutilizadas, dando la falsa impresión de que la cola está llena aunque existan espacios libres al principio. Para solucionar esto en implementaciones avanzadas, se suelen usar **colas circulares** o listas enlazadas.
2. **Intentar operar con colas vacías o llenas**: Omitir las validaciones con `estaVacia()` o `estaLlena()` provocará fallos lógicos o lecturas de datos basura.

 

#### Ejercicio práctico propuesto

Escribe una función adicional llamada `mostrarCola(struct Cola *q)` que recorra e imprima todos los elementos que se encuentran actualmente formados en la cola, desde el `frente` hasta el `final`.

> **Pista para el ejercicio:**
> * Utiliza un bucle `for` que comience en el índice `q->frente` y termine exactamente en `q->final`.
> * Recuerda validar primero si la cola no está vacía utilizando `estaVacia(q)` antes de intentar recorrerla.
> 
> 

 

#### Mini resumen

* Una cola (*Queue*) opera bajo el principio **FIFO** (*First-In, First-Out* / Primero en entrar, primero en salir).
* Sus operaciones principales son **encolar** (*enqueue*, insertar al final) y **desencolar** (*dequeue*, extraer del frente).
* Se gestiona mediante dos índices (`frente` y `final`) para controlar los límites de la estructura.

## Ejemplo práctico de Pilas, Listas y Colas

En un restaurante concurrido, estas tres estructuras de datos trabajan juntas pero cumplen roles completamente diferentes:

1. **La Cola (*Queue* - FIFO):** Representa la línea de pedidos que van llegando de los clientes. El primer pedido que llega es el primero que la cocina empieza a preparar.
2. **La Pila (*Stack* - LIFO):** Representa la torre de platos limpios en el estante. El lavaplatos coloca los platos nuevos **encima** (push), y el chef siempre toma el plato que está **hasta arriba** para servir (pop).
3. **La Lista Enlazada (*Linked List*):** Representa el menú general del restaurante o la base de datos dinámica de platillos disponibles, la cual puede crecer o cambiar de tamaño conforme se añaden o retiran recetas.

 

### Ejemplo Integrado: Sistema de Cocina en C (Estándar C99)

Aquí tienes un programa completo, compilable y funcional que simula este escenario integrando las tres estructuras:

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define MAX_COLA 5
#define MAX_PILA 5

// ==========================================
// 1. LISTA ENLAZADA: Menú del Restaurante
// ==========================================
struct MenuItem {
    char nombre[30];
    float precio;
    struct MenuItem *sig; // Puntero al siguiente elemento del menú
};

// Función para agregar un plato al menú (Lista)
struct MenuItem* agregarPlatoMenu(struct MenuItem *cabeza, const char *nombre, float precio) {
    struct MenuItem *nuevo = (struct MenuItem *) malloc(sizeof(struct MenuItem));
    strcpy(nuevo->nombre, nombre);
    nuevo->precio = precio;
    nuevo->sig = cabeza;
    return nuevo;
}

void mostrarMenu(struct MenuItem *cabeza) {
    printf("\n  MENU DEL RESTAURANTE (Lista Enlazada)  \n");
    struct MenuItem *actual = cabeza;
    while (actual != NULL) {
        printf("- %s ($%.2f) -> ", actual->nombre, actual->precio);
        actual = actual->sig;
    }
    printf("NULL\n");
}

// ==========================================
// 2. PILA (STACK): Torre de Platos Limpios
// ==========================================
struct PilaPlatos {
    int platos[MAX_PILA];
    int tope;
};

void inicializarPila(struct Pila *p) { /* Omitido por brevedad en structs anidados */ }

void apilarPlato(struct PilaPlatos *p, int idPlato) {
    if (p->tope == MAX_PILA - 1) {
        printf("[Pila] ¡Alerta! Torre de platos llena.\n");
    } else {
        p->tope++;
        p->elementos[p->tope] = idPlato; // Usaremos un arreglo simple simulado
    }
}

// ==========================================
// 3. COLA (QUEUE): Pedidos de Clientes
// ==========================================
struct ColaPedidos {
    int idsPedidos[MAX_COLA];
    int frente;
    int final;
};

void encolarPedido(struct ColaPedidos *q, int idPedido) {
    if (q->final == MAX_COLA - 1) {
        printf("[Cola] ¡La fila de pedidos esta llena!\n");
    } else {
        q->final++;
        q->idsPedidos[q->final] = idPedido;
        printf("-> Pedido #%d anadido a la cola de espera.\n", idPedido);
    }
}

int desencolarPedido(struct ColaPedidos *q) {
    if (q->frente > q->final) {
        printf("[Cola] No hay pedidos pendientes.\n");
        return -1;
    } else {
        int atendido = q->idsPedidos[q->frente];
        q->frente++;
        return atendido;
    }
}

int main(void) {
    //   1. Inicializamos la Lista del Menú  
    struct MenuItem *menu = NULL;
    menu = agregarPlatoMenu(menu, "Tacos al Pastor", 85.0f);
    menu = agregarPlatoMenu(menu, "Sopa de Tortilla", 60.0f);
    menu = agregarPlatoMenu(menu, "Agua de Horchata", 25.0f);
    mostrarMenu(menu);

    //   2. Inicializamos la Pila de Platos  
    struct PilaPlatos pilaPlatos;
    pilaPlatos.tope = -1;
    // Apilamos 3 platos limpios
    pilaPlatos.tope++; pilaPlatos.platos[pilaPlatos.tope] = 101;
    pilaPlatos.tope++; pilaPlatos.platos[pilaPlatos.tope] = 102;
    pilaPlatos.tope++; pilaPlatos.platos[pilaPlatos.tope] = 103;
    printf("\n[Pila] Platos limpios listos en la torre. Cima actual: Plato #%d\n", pilaPlatos.platos[pilaPlatos.tope]);

    //   3. Inicializamos la Cola de Pedidos  
    struct ColaPedidos colaPedidos;
    colaPedidos.frente = 0;
    colaPedidos.final = -1;

    printf("\n  LLEGADA DE CLIENTES (COLA)  \n");
    encolarPedido(&colaPedidos, 501);
    encolarPedido(&colaPedidos, 502);
    encolarPedido(&colaPedidos, 503);

    //   4. Simulando la operacion de la cocina  
    printf("\n  COCINA EN ACCION  \n");
    int pedidoAtendido = desencolarPedido(&colaPedidos); // Sale el 501 (FIFO)
    if (pedidoAtendido != -1) {
        int platoUsado = pilaPlatos.platos[pilaPlatos.tope]; // Tomamos el plato de arriba (LIFO)
        pilaPlatos.tope--; // Retiramos el plato de la pila
        printf("Cocinando pedido #%d... Se uso el plato limpio #%d\n", pedidoAtendido, platoUsado);
    }

    // Liberando memoria de la lista enlazada del menú
    struct MenuItem *temp;
    while (menu != NULL) {
        temp = menu;
        menu = menu->sig;
        free(temp);
    }

    return 0;
}

```

 

### ¿Cómo se complementan en este ejemplo?

* **La Lista** define estática o dinámicamente qué opciones existen (los datos de referencia del menú que no cambian de orden por urgencia).
* **La Cola** organiza dinámicamente el orden de atención cronológico (el cliente que llega primero es atendido primero).
* **La Pila** gestiona un recurso físico inmediato bajo demanda (el último plato que se lava o se apila es el primero que se utiliza para despachar).



### Título del tema: Árboles Generales y Recorrido Jerárquico de Directorios en C

#### Objetivo de aprendizaje

Comprender cómo modelar estructuras de datos jerárquicas con ramificación múltiple (árboles generales) utilizando estructuras auto-referenciadas, y aplicarlo al análisis y recorrido recursivo de sistemas de archivos (directorios y subdirectorios).

 

#### Explicación teórica y analogía

Imagina el explorador de archivos de tu computadora: tienes una carpeta raíz (`/`), dentro hay varias carpetas principales, y cada una de ellas puede contener a su vez un número ilimitado de subcarpetas y archivos.

* A diferencia de un **árbol binario** (donde cada nodo tiene estrictamente como máximo dos hijos: izquierdo y derecho), un **árbol general** permite que un nodo padre tenga **múltiples hijos** (0, 1, 3, 10 o los que necesite).


* En C, modelamos esto mediante **estructuras auto-referenciadas** (`struct`), donde un nodo contiene la información del elemento actual y punteros o listas dinámicas para conectar con sus descendientes o hermanos.
* El sistema de archivos del sistema operativo es, por naturaleza, un árbol general. Para recorrerlo de forma limpia, utilizamos **recursividad**, una técnica en la que una función se llama a sí misma para adentrarse en cada subcarpeta encontrada (exactamente como opera el comando clásico de búsqueda o listado recursivo de directorios).



 

#### Ejemplo de código comentado

A continuación, veremos un programa inspirando en los principios de recorrido jerárquico de directorios (estilo la utilidad `fsize` de los sistemas UNIX). Utiliza las llamadas estándar de directorios (`opendir`, `readdir`) para explorar recursivamente una estructura de carpetas:

```c
#include <stdio.h>
#include <string.h>
#include <dirent.h>   // Biblioteca estándar para manejo de directorios
#include <sys/stat.h> // Para obtener información de los archivos/carpetas

// Función recursiva para recorrer un directorio en forma de árbol general
void explorarDirectorio(const char *rutaBase, int nivel) {
    DIR *dir = opendir(rutaBase);
    if (dir == NULL) {
        perror("No se pudo abrir el directorio");
        return;
    }

    struct dirent *entrada;
    char rutaCompleta[1024];

    // Leemos cada entrada (archivo o carpeta) dentro del directorio
    while ((entrada = readdir(dir)) != NULL) {
        // Omitimos los directorios especiales "." (actual) y ".." (padre)
        if (strcmp(entrada->d_name, ".") == 0 || strcmp(entrada->d_name, "..") == 0) {
            continue;
        }

        // Imprimimos la indentación según la profundidad del nivel en el árbol
        for (int i = 0; i < nivel; i++) {
            printf("  "); 
        }

        // Construimos la ruta completa combinando la base y el nombre del elemento
        snprintf(rutaCompleta, sizeof(rutaCompleta), "%s/%s", rutaBase, entrada->d_name);

        struct stat st;
        if (stat(rutaCompleta, &st) == 0) {
            // Verificamos si el elemento es un directorio (nodo con hijos)
            if (S_ISDIR(st.st_mode)) {
                printf("[Carpeta] %s\n", entrada->d_name);
                // Llamada recursiva descendiendo un nivel en el árbol general
                explorarDirectorio(rutaCompleta, nivel + 1);
            } else {
                // Es un archivo (nodo hoja)
                printf("[Archivo] %s (%ld bytes)\n", entrada->d_name, st.st_size);
            }
        }
    }

    closedir(dir); // Cerramos el directorio para liberar recursos del sistema
}

int main(void) {
    printf("  EXPLORANDO EL DIRECTORIO ACTUAL EN FORMA DE ARBOL  \n");
    // Iniciamos la exploración desde el directorio actual "."
    explorarDirectorio(".", 0);

    return 0;
}

```

 

#### Explicación línea por línea

* `#include <dirent.h>` y `#include <sys/stat.h>`: Importan las herramientas del sistema operativo para abrir flujos de directorios y consultar los metadatos de los archivos (como tamaño y tipo).


* `DIR *dir = opendir(rutaBase);`: Abre el directorio especificado y devuelve un puntero de flujo de tipo `DIR *` para comenzar la lectura secuencial.


* `while ((entrada = readdir(dir)) != NULL)`: Lee uno a uno los elementos contenidos en la carpeta. Cada iteración representa un nodo hijo dentro del árbol general del sistema de archivos.


* `if (strcmp(entrada->d_name, ".") == 0 || ...)`: Filtra los enlaces al directorio actual y anterior para **evitar un bucle infinito** de recursividad.


* `explorarDirectorio(rutaCompleta, nivel + 1);`: La llamada recursiva clave. Al encontrar una subcarpeta, la función se invoca a sí misma incrementando el nivel de profundidad, ramificando el árbol general de manera limpia.

 

#### Errores comunes y cómo evitarlos

1. **Olvidar cerrar los directorios (`closedir`)**: Cada vez que abres un directorio con `opendir`, el sistema operativo asigna un descriptor de archivo. Si omites cerrarlo al terminar, puedes agotar los recursos disponibles del sistema (*file descriptor leak*).


2. **Caer en bucles infinitos por enlaces circulares**: No omitir los nombres `.` (referencia a sí mismo) y `..` (referencia al padre) hará que tu función recursiva intente explorar la misma carpeta infinitamente hasta provocar un fallo de desbordamiento de pila (*stack overflow*).



 

#### Ejercicio práctico propuesto

Escribe un programa en C que defina una estructura para un nodo de un **árbol general en memoria** (no en disco), donde cada nodo contenga un nombre (`char nombre[50]`) y un arreglo estático de punteros hacia sus hijos (por ejemplo, hasta 5 sub-nodos). Implementa una función recursiva sencilla para imprimir el árbol completo.

> **Pista para el ejercicio:**
> * Define tu estructura de nodo combinando cadenas de caracteres y un arreglo de punteros del mismo tipo de estructura: `struct Nodo *hijos[5];`.
> * Utiliza una variable auxiliar `int num_hijos;` para llevar el control de cuántos hijos tiene activos cada nodo ramificado.
> 
> 

 

#### Mini resumen

* Un **árbol general** permite modelar jerarquías complejas donde un nodo puede tener múltiples ramificaciones o hijos simultáneos.
* El manejo de directorios en C utiliza estructuras como `DIR`, `struct dirent` y funciones clave como `opendir`, `readdir` y `stat` para interactuar con el sistema de archivos operativo.


* La **recursividad** es la herramienta matemática y lógica natural para recorrer tanto árboles en memoria como estructuras de directorios en disco.



### Título del tema: Árboles Binarios Interactivos en C (Estructura y Recorrido)

#### Objetivo de aprendizaje

Aprender a diseñar un programa interactivo en C que construya un **Árbol Binario de Búsqueda (ABB)** en tiempo de ejecución a partir de la entrada por teclado del usuario, implementando asignación dinámica de memoria (`malloc`), punteros autorreferenciados y recorridos recursivos bajo el estándar **C99**.

 

#### Explicación teórica y analogía

Imagina un sistema de fichas bibliográficas en una gran biblioteca: cuando llega un nuevo libro, el bibliotecario lo compara con el libro que está en el mostrador central (la raíz). Si el título alfabéticamente es menor, lo manda a la mesa de la izquierda; si es mayor, lo manda a la derecha. Ese proceso se repite en cada mesa secundaria hasta encontrar un espacio vacío donde colocar la nueva ficha.

En informática, un **Árbol Binario de Búsqueda (ABB)** es una estructura jerárquica auto-referenciada donde cada nodo tiene un valor y como máximo dos hijos (izquierdo y derecho):

* Todo lo ubicado en el sub-árbol izquierdo es menor que el nodo padre.


* Todo lo ubicado en el sub-árbol derecho es mayor que el nodo padre.



Para hacerlo **interactivo**, combinaremos un menú repetitivo (usando bucles y lectura de datos) con funciones recursivas de inserción y de impresión en orden (*in-order traversal*), que organizan automáticamente los números de menor a mayor conforme se van ingresando.

 

#### Ejemplo de código comentado

Aquí tienes un programa completo, compilable y funcional que implementa un árbol binario interactivo donde puedes añadir números y mostrarlos ordenados en pantalla:

```c
#include <stdio.h>
#include <stdlib.h>

// 1. Definición de la estructura del Nodo del Árbol
struct NodoArbol {
    int dato;
    struct NodoArbol *izq;
    struct NodoArbol *der;
};

// Función para crear un nuevo nodo de forma dinámica
struct NodoArbol* crearNodo(int valor) {
    struct NodoArbol *nuevo = (struct NodoArbol *) malloc(sizeof(struct NodoArbol));
    if (nuevo == NULL) {
        printf("Error: No hay memoria disponible.\n");
        exit(1);
    }
    nuevo->dato = valor;
    nuevo->izq = NULL;
    nuevo->der = NULL;
    return nuevo;
}

// Función recursiva para insertar un valor en el Árbol Binario de Búsqueda
struct NodoArbol* insertar(struct NodoArbol *raiz, int valor) {
    if (raiz == NULL) {
        return crearNodo(valor); // Si llegamos a un hueco libre, creamos el nodo aquí
    }
    
    if (valor < raiz->dato) {
        raiz->izq = insertar(raiz->izq, valor); // Nos movemos hacia la rama izquierda
    } else if (valor > raiz->dato) {
        raiz->der = insertar(raiz->der, valor); // Nos movemos hacia la rama derecha
    }
    // Si el valor ya existe, no hacemos nada (evitamos duplicados)
    
    return raiz;
}

// Recorrido In-Order (imprime los elementos ordenados de menor a mayor)[cite: 1]
void mostrarInOrder(struct NodoArbol *raiz) {
    if (raiz != NULL) {
        mostrarInOrder(raiz->izq);
        printf("%d ", raiz->dato);
        mostrarInOrder(raiz->der);
    }
}

// Función recursiva para liberar la memoria del árbol
void liberarArbol(struct NodoArbol *raiz) {
    if (raiz != NULL) {
        liberarArbol(raiz->izq);
        liberarArbol(raiz->der);
        free(raiz);
    }
}

int main(void) {
    struct NodoArbol *raiz = NULL;
    int opcion = 0, valor;

    // Menú interactivo
    while (opcion != 3) {
        printf("\n  MENU ARBOL BINARIO INTERACTIVO  \n");
        printf("1. Insertar un numero\n");
        printf("2. Mostrar elementos ordenados (In-Order)\n");
        printf("3. Salir\n");
        printf("Elige una opcion: ");
        scanf("%d", &opcion);

        if (opcion == 1) {
            printf("Ingresa un numero entero: ");
            scanf("%d", &valor);
            raiz = insertar(raiz, valor);
            printf("¡Numero %d insertado con exito en el arbol!\n", valor);
        } 
        else if (opcion == 2) {
            printf("\n> Elementos del arbol en orden ascendente: ");
            mostrarInOrder(raiz);
            printf("\n");
        } 
        else if (opcion == 3) {
            printf("\nSaliendo del programa y liberando memoria...\n");
        } 
        else {
            printf("\n[Error] Opcion no valida.\n");
        }
    }

    // Liberamos toda la memoria dinámica antes de cerrar
    liberarArbol(raiz);

    return 0;
}

```

 

#### Explicación línea por línea

* `struct NodoArbol *izq; struct NodoArbol *der;`: Son los dos punteros autorreferenciados que ramifican el nodo hacia el sub-árbol izquierdo y derecho.


* `raiz = insertar(raiz, valor);`: Como la inserción modifica la raíz principal cuando el árbol está vacío, devolvemos siempre la referencia actualizada del nodo.
* `if (valor < raiz->dato)`: Evalúa la regla de oro del Árbol Binario de Búsqueda: los menores van a la izquierda y los mayores a la derecha.


* `mostrarInOrder(raiz->izq);`: Aplica recursividad para visitar primero toda la rama izquierda, luego procesa la raíz y finalmente la rama derecha, logrando que la salida en pantalla aparezca mágicamente ordenada de menor a mayor.



 

#### Errores comunes y cómo evitarlos

1. **Olvidar actualizar la raíz al insertar el primer elemento:** Cuando el árbol está inicialmente en `NULL`, el primer `insertar` crea la raíz principal. Si no reasignas el valor retornado (`raiz = insertar(raiz, valor);`), el puntero principal se quedará apuntando a la basura o a `NULL`.
2. **Fugas de memoria al cerrar el programa:** Los nodos creados con `malloc` durante la interacción se quedan albergados en el *heap*. Olvidar ejecutar una función de limpieza como `liberarArbol()` al terminar deja recursos colgados en el sistema operativo.

 

#### Ejercicio práctico propuesto

Amplía el programa interactivo anterior agregando una **opción 4** en el menú llamada *"Buscar un número"*. Debes implementar una función recursiva llamada `buscar(struct NodoArbol *raiz, int buscado)` que devuelva un mensaje indicando si el número ingresado por el usuario existe o no dentro del árbol.

> **Pista para el ejercicio:**
> * Si `raiz == NULL`, el número no está.
> * Si `raiz->dato == buscado`, ¡lo has encontrado!
> * Si el número buscado es menor que `raiz->dato`, haz una llamada recursiva evaluando la rama izquierda (`buscar(raiz->izq, buscado)`). De lo contrario, búscalo por la rama derecha.
> 
> 

 

#### Mini resumen

* Un **Árbol Binario de Búsqueda (ABB)** organiza los datos de forma jerárquica manteniendo una regla estricta: los menores a la izquierda y los mayores a la derecha.


* La combinación de menús interactivos con funciones recursivas permite construir aplicaciones dinámicas muy potentes en C.
* El recorrido *In-Order* es la clave para leer los datos de un ABB de forma ordenada sin necesidad de pasarlos por un algoritmo de ordenamiento tradicional.

### Manejo de Archivos en Lenguaje C: Lectura, Escritura y Edición

#### Objetivo de aprendizaje

Aprender a manipular archivos de texto en C utilizando la biblioteca estándar (`<stdio.h>`), dominando los diferentes modos de apertura (`"r"`, `"w"`, `"a"`, `"r+"`), la búsqueda de información y las técnicas seguras para insertar, borrar o reemplazar contenido mediante archivos temporales bajo el estándar **C99**.

 

#### Explicación teórica y analogía

Imagina que un archivo de texto en tu disco duro es como una libreta de notas física:

* Si quieres **leerla**, la abres en modo de **solo lectura** (`"r"`), miras las páginas pero no puedes escribir en ella.
* Si quieres **escribir desde cero**, abres una libreta nueva en modo **escritura** (`"w"`), lo que borra todo lo anterior si ya existía.
* Si quieres **agregar notas al final**, usas el modo **añadir o append** (`"a"`).
* Si quieres **editar el contenido en medio**, en la vida real tachas o usas corrector. En C, los archivos de texto almacenados en disco no permiten "insertar" o "borrar" una línea en medio de manera directa sin desplazar todo el texto restante. Por ello, la técnica estándar y más segura en C para **insertar, borrar líneas o reemplazar palabras** consiste en leer el archivo original, procesar los cambios y escribirlos en un **archivo temporal**, para luego reemplazar el archivo viejo por el nuevo.

 

#### Ejemplo de código comentado

Aquí tienes un programa completo, compilable y funcional que demuestra cómo crear un archivo, escribir en él, leer su contenido, buscar una palabra y simular la edición (reemplazo) mediante un archivo temporal:

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

int main(void) {
    // 1. ESCRIBIR y AGREGAR (Modos "w" y "a")
    FILE *fp = fopen("notas.txt", "w"); // Crea el archivo o sobrescribe si existe
    if (fp == NULL) {
        printf("Error al abrir el archivo para escritura.\n");
        return 1;
    }
    fprintf(fp, "Linea 1: Introduccion a C\n");
    fprintf(fp, "Linea 2: Estructuras de datos\n");
    fprintf(fp, "Linea 3: Manejo de archivos\n");
    fclose(fp); // Siempre debemos cerrar el archivo

    // Agregando una línea al final (Modo "a" - Append)
    fp = fopen("notas.txt", "a");
    if (fp != NULL) {
        fprintf(fp, "Linea 4: Buenas practicas\n");
        fclose(fp);
    }

    // 2. LECTURA y BÚSQUEDA de palabras (Modo "r")
    fp = fopen("notas.txt", "r");
    if (fp == NULL) {
        printf("Error al abrir el archivo para lectura.\n");
        return 1;
    }

    char buffer[256];
    char palabraBuscada[] = "archivos";
    int encontrada = 0;

    printf("  CONTENIDO DEL ARCHIVO Y BUSQUEDA  \n");
    while (fgets(buffer, sizeof(buffer), fp) != NULL) {
        printf("%s", buffer);
        // Buscamos si la palabra está presente en la línea actual
        if (strstr(buffer, palabraBuscada) != NULL) {
            encontrada = 1;
        }
    }
    fclose(fp);

    if (encontrada) {
        printf("\n[Resultado] ¡La palabra '%s' fue encontrada en el archivo!\n", palabraBuscada);
    } else {
        printf("\n[Resultado] La palabra no aparece.\n");
    }

    // 3. EDICIÓN / REEMPLAZO usando un archivo temporal
    // Supongamos que queremos cambiar "Estructuras" por "Algoritmos"
    FILE *original = fopen("notas.txt", "r");
    FILE *temporal = fopen("temp.txt", "w");

    if (original != NULL && temporal != NULL) {
        char linea[256];
        while (fgets(linea, sizeof(linea), original) != NULL) {
            // Si la línea contiene la palabra a reemplazar, escribimos la versión modificada
            if (strstr(linea, "Estructuras") != NULL) {
                fprintf(temporal, "Linea 2: Algoritmos de datos\n");
            } else {
                fprintf(temporal, "%s", linea); // Copiamos la línea tal cual
            }
        }
        fclose(original);
        fclose(temporal);

        // Reemplazamos el archivo original por el temporal
        remove("notas.txt");
        rename("temp.txt", "notas.txt");
        printf("\n[Edicion] El archivo ha sido actualizado con éxito.\n");
    }

    return 0;
}

```

 

#### Explicación línea por línea

* `FILE *fp;`: Declara un puntero a la estructura `FILE`, que gestiona toda la información de control del archivo abierto.
* `fopen("notas.txt", "w");`: Abre o crea el archivo en modo escritura (`w`). Devuelve `NULL` si el sistema operativo deniega el permiso o falla.
* `fgets(buffer, sizeof(buffer), fp);`: Lee una línea completa del archivo (incluyendo espacios y saltos de línea) hasta alcanzar el tamaño del buffer o el final del archivo (`EOF`).
* `strstr(buffer, palabraBuscada);`: Función de la biblioteca `<string.h>` que busca una subcadena dentro de otra, facilitando la **búsqueda de palabras**.
* `remove("notas.txt");` y `rename("temp.txt", "notas.txt");`: Funciones estándar para borrar archivos obsoletos y renombrar el archivo temporal, logrando un mecanismo limpio de **edición y borrado de líneas**.

 

#### Errores comunes y cómo evitarlos

1. **No verificar si el puntero es `NULL**`: Intentar leer o escribir en un archivo que no se Pudo abrir provocará un fallo de segmentación (*Segmentation Fault*). Siempre valida `if (fp == NULL)`.
2. **Olvidar cerrar el archivo (`fclose`)**: Deja fugas de descriptores de archivos en el sistema operativo y puede corromper los búferes de escritura pendientes.
3. **Confundir `"w"` con `"r+"**`: El modo `"w"` destruye el contenido anterior al abrir el archivo, mientras que `"r+"` permite lectura y edición manteniendo el contenido previo.

 

#### Ejercicio práctico propuesto

Escribe un programa independiente en C que abra un archivo de texto y cuente **cuántas líneas totales** contiene, imprimiendo el resultado en la consola.

> **Pista para el ejercicio:**
> * Abre el archivo en modo lectura (`"r"`).
> * Utiliza un bucle con `fgets()` o lee carácter por carácter con `fgetc()` incrementando un contador entero cada vez que detectes un salto de línea (`'\n'`).
> 
> 

 

#### Mini resumen

* Los archivos en C se conectan mediante punteros de tipo `FILE *` usando la función `fopen()` y se liberan con `fclose()`.
* Los modos principales son lectura (`"r"`), escritura destructiva (`"w"`), y adición al final (`"a"`).
* Para realizar operaciones complejas como insertar, borrar líneas o reemplazar palabras, la estrategia estándar en C utiliza archivos auxiliares temporales combinados con `remove()` y `rename()`.


### Definición de Constantes en Lenguaje C

#### Objetivo de aprendizaje

Aprender a declarar y utilizar valores fijos e inalterables mediante directivas del preprocesador (`#define`) y calificadores de tipo (`const`), entendiendo sus diferencias y aplicaciones prácticas.

 

#### Explicación teórica y analogía

Imagina que estás diseñando un videojuego y el valor de la gravedad de la Tierra (`9.8`) o el puntaje máximo por nivel (`1000`) nunca deben cambiar mientras el programa corre. Si usaras variables comunes, podrías modificar su valor por error en cualquier cálculo.

En C, disponemos de dos herramientas principales para fijar estos valores:

1. **Macros simbólicas con `#define**`: Es una orden para el preprocesador que reemplaza un texto por un valor antes de compilar el código. No ocupan espacio de memoria como variables tradicionales, ya que actúan como un simple intercambio de texto.


2. **El calificador `const**`: Convierte una variable común en un objeto de **solo lectura**. El compilador vigilará que nadie intente reasignarle un valor nuevo.



 

#### Ejemplo de código comentado

Aquí tienes un programa funcional y compilable que utiliza ambas formas de definir constantes:

```c
#include <stdio.h>

// 1. Constante simbólica usando el preprocesador (#define)
#define MAX_INTENTOS 3 

int main(void) {
    // 2. Variable constante usando el calificador const
    const float PI = 3.1416f;
    int intentos_actuales = 1;

    printf("  SISTEMA DE ACCESO  \n");
    printf("Numero maximo de intentos permitidos: %d\n", MAX_INTENTOS);
    printf("Valor constante de PI: %.4f\n", PI);

    // Intentamos reasignar una constante const (esto generaría un error de compilación)
    // PI = 3.14; // <- Descomentar esto rompería la compilación

    return 0;
}

```

 

#### Explicación línea por línea

* `#define MAX_INTENTOS 3`: Le indica al preprocesador que cada vez que encuentre la palabra `MAX_INTENTOS` en el código antes de compilar, la sustituirá por el número `3`. Nota que **no lleva punto y coma al final**.


* `const float PI = 3.1416f;`: Declara una variable de tipo flotante llamada `PI`. La palabra clave `const` al inicio le prohíbe estrictamente al programa modificar su contenido más adelante.



 

#### Errores comunes y cómo evitarlos

1. **Poner un punto y coma al final de `#define**`: Escribir `#define TAMAÑO 100;` por error provocará que el punto y coma se inserte literalmente donde uses `TAMAÑO`, rompiendo las operaciones matemáticas o sintácticas posteriores.
2. **Intentar modificar un objeto `const**`: Una vez inicializada una variable `const`, cualquier intento de cambiar su valor mediante una asignación (ej. `PI = 3.14;`) detendrá la compilación con un error de tipo *read-only variable*.



 

#### Ejercicio práctico propuesto

Escribe un programa en C que calcule el área de un círculo ($Area = \pi \times radio^2$).

* Utiliza `#define` para declarar el valor de `PI` como `3.1416`.
* Declara una variable `radio` (por ejemplo, con valor `5.0`).
* Calcula el área e imprímela en pantalla usando `printf`.

> **Pista para el ejercicio:**
> * Recuerda que puedes elevar al cuadrado multiplicando el radio por sí mismo (`radio * radio`).
> * No olvides colocar `#define PI 3.1416` antes de la función `main()`.
> 
> 

 

#### Mini resumen

* Las constantes aseguran que los valores fijos de un programa permanezcan inalterados durante su ejecución.


* `#define` realiza una sustitución textual a través del preprocesador antes de compilar.


* El calificador `const` otorga seguridad de solo lectura a nivel de compilación para variables específicas.



### Directivas del Preprocesador: Uso de `#define` y Compilación Condicional (`#ifdef` / `#ifndef`)

#### Objetivo de aprendizaje

Comprender el rol del preprocesador de C antes de la compilación, aprender a definir constantes simbólicas y macros con `#define`, y controlar qué bloques de código se compilan selectivamente usando directivas condicionales como `#ifdef` e `#ifndef` bajo el estándar **C99**.

 

#### Explicación teórica y clara

Antes de que el compilador traduzca tu código C a lenguaje de máquina, este pasa por una fase previa llamada **preprocesamiento**. El preprocesador actúa como un editor de texto inteligente que revisa tu archivo buscando líneas que comienzan con el signo numeral (`#`).

1. **`#define` (Definición de macros):** Sirve para crear constantes simbólicas o abreviaturas textuales. Cuando el preprocesador encuentra el nombre de la macro en tu código, lo reemplaza literalmente por su valor o secuencia de tokens antes de compilar.


2. **Compilación Condicional (`#ifdef` / `#ifndef`):** Nos permite incluir o excluir bloques enteros de código de forma selectiva, dependiendo de si un identificador o macro ha sido definido previamente.


* `#ifdef MACRO` significa *"si MACRO está definido"*.


* `#ifndef MACRO` significa *"si MACRO **no** está definido"*.





Esto es sumamente útil para depuración, para adaptar código a diferentes sistemas operativos o para evitar que un archivo de cabecera se incluya múltiples veces.

 

#### Ejemplo de código comentado

Aquí tienes un programa completo, compilable y funcional que demuestra el uso de `#define` y las directivas condicionales:

```c
#include <stdio.h>

// Definimos una macro simbólica para el límite de intentos
#define MAX_INTENTOS 3

// Definimos una macro para activar el modo de depuración
#define MODO_DEBUG 1

int main(void) {
    printf("  SISTEMA INICIALIZADO  \n");
    printf("Intentos maximos permitidos: %d\n", MAX_INTENTOS);

    // Compilación condicional: este bloque solo se compila si MODO_DEBUG está definido
    #ifdef MODO_DEBUG
        printf("[DEBUG] El programa esta corriendo en modo de desarrollo.\n");
    #endif

    // Compilación condicional basada en la ausencia de una macro
    #ifndef MODO_PRODUCCION
        printf("[INFO] Modo produccion no especificado explicitamente.\n");
    #endif

    return 0;
}

```

 

#### Explicación línea por línea

* `#define MAX_INTENTOS 3`: Indica al preprocesador que cada aparición posterior de `MAX_INTENTOS` sea sustituida por el número `3`.


* `#ifdef MODO_DEBUG`: Comprueba si la macro `MODO_DEBUG` existe. Como la definimos previamente en la línea 5, el bloque interior se incluye en la compilación.


* `#endif`: Cierra formalmente el bloque condicional iniciado por `#ifdef` o `#ifndef`.


* `#ifndef MODO_PRODUCCION`: Verifica si la macro `MODO_PRODUCCION` **no** ha sido definida. Como no existe en nuestro código, el mensaje informativo se compila e imprime.



 

#### Errores comunes y cómo evitarlos

1. **Poner un punto y coma al final de `#define`:** Escribir `#define TAMAÑO 100;` por error provocará que el punto y coma se inserte literalmente donde uses la macro, lo que suele romper las operaciones matemáticas o la sintaxis posterior.
2. **Olvidar cerrar los bloques condicionales:** Toda directiva de apertura como `#ifdef` o `#ifndef` debe cerrarse obligatoriamente con su respectivo `#endif`, de lo contrario obtendremos un error de preprocesamiento.



 

#### Ejercicio práctico propuesto

Escribe un programa en C que utilice `#define` para declarar una macro llamada `VERSION` con el valor `2`. Luego, utiliza la compilación condicional para imprimir un mensaje personalizado en la consola evaluando esa versión.

> **Pista para el ejercicio:**
> * Puedes usar la directiva `#if VERSION >= 2` para realizar una evaluación numérica directa de la constante definida por el preprocesador.
> 
> 
> * No olvides terminar tu estructura condicional utilizando siempre `#endif`.
> 
> 
> 
> 

 

#### Mini resumen

* El preprocesador opera antes de la compilación propiamente dicha, ejecutando sustituciones de texto y controlando la inclusión selectiva de código.


* `#define` reemplaza identificadores por valores o fragmentos de código de manera limpia y eficiente.


* Las directivas `#ifdef` e `#ifndef` evalúan la existencia de macros para decidir qué líneas de código formarán parte del programa ejecutable final.


### Creación y Uso de Archivos de Cabecera (*Header Files*) Personalizados en C

#### Objetivo de aprendizaje

Aprender a modularizar un programa en C separando la interfaz (declaraciones y macros en archivos `.h`) de la implementación (código fuente en archivos `.c`), aplicando protecciones de inclusión múltiple (*guard macros*) bajo el estándar **C99**.

 

#### Explicación teórica y analogía

Imagina que estás construyendo un edificio de departamentos complejo: en lugar de fabricar todos los componentes (tuberías, puertas, ventanas) dentro de un único y gigantesco plano ilegible, divides el diseño en planos especializados y catálogos de piezas que los diferentes equipos de obra pueden consultar cuando los necesitan.

En C, cuando los proyectos crecen, dejar todo el código en un solo archivo `.c` se vuelve inmanejable. Para solucionar esto:

1. **El archivo de cabecera (`.h`)** funciona como el "catálogo" o la interfaz: contiene las declaraciones de funciones (`extern`), las definiciones de tipos (`struct`) y las constantes simbólicas (`#define`), permitiendo que múltiples archivos fuente sepan qué herramientas existen.


2. **El archivo fuente (`.c`)** contiene la lógica real y el cuerpo de las funciones implementadas.
3. **Las guardas de inclusión (`#ifndef` / `#define` / `#endif`)** actúan como un sello de seguridad para evitar que un mismo archivo de cabecera se procese dos veces por error durante la compilación, previniendo conflictos de redefinición.



 

#### Ejemplo de código comentado

Vamos a estructurar un programa dividido en tres partes: un archivo de cabecera personalizado (`operaciones.h`), su respectiva implementación (`operaciones.c`), y el programa principal (`main.c`).

##### 1. El archivo de cabecera: `operaciones.h`

Este archivo contiene las declaraciones públicas protegidas con una guarda contra inclusiones múltiples:

```c
#ifndef OPERACIONES_H
#define OPERACIONES_H

// Declaración de una constante simbólica
#define FACTOR_IVA 1.16f

// Declaración (prototipo) de la función
float calcular_total_con_iva(float subtotal);

#endif /* OPERACIONES_H */

```

##### 2. El archivo de implementación: `operaciones.c`

Este archivo incluye su cabecera y desarrolla la lógica real de la función:

```c
#include "operaciones.h"

// Definición de la función declarada en el header
float calcular_total_con_iva(float subtotal) {
    return subtotal * FACTOR_IVA;
}

```

##### 3. El programa principal: `main.c`

Aquí utilizamos nuestra cabecera personalizada envolviéndola entre comillas dobles (`"..."`) para que el preprocesador la busque primero en el directorio actual:

```c
#include <stdio.h>
#include "operaciones.h" // Incluimos nuestro header personalizado

int main(void) {
    float precio_original = 200.0f;
    float precio_final = calcular_total_con_iva(precio_original);

    printf("  SISTEMA DE FACTURACION  \n");
    printf("Subtotal: $%.2f\n", precio_original);
    printf("Total con IVA aplicado: $%.2f\n", precio_final);

    return 0;
}

```

 

#### Explicación línea por línea

* `#ifndef OPERACIONES_H` y `#define OPERACIONES_H`: Forman la **guarda de inclusión** (*wrapper #ifndef*). Si `OPERACIONES_H` no ha sido definido, lo define y permite leer el contenido del archivo; de lo contrario, lo ignora por completo para evitar errores de duplicidad.


* `#include "operaciones.h"`: Le ordena al preprocesador que busque y copie textualmente las declaraciones del archivo de cabecera local en el punto exacto donde se invoca.


* `gcc main.c operaciones.c -o programa`: El comando de compilación necesario para enlazar los dos módulos fuente independientes en un solo archivo ejecutable coherente.



 

#### Errores comunes y cómo evitarlos

1. **Olvidar las guardas de inclusión (`#ifndef` / `#define` / `#endif`)**: Si un archivo `.c` incluye varias cabeceras que a su vez se requieren entre sí, podrías declarar una misma estructura o función dos veces, lo que detendría la compilación con un error de redefinición.


2. **Confundir comillas con corchetes en el `#include**`: Utiliza comillas dobles (`"archivo.h"`) para tus propios archivos de cabecera ubicados en el proyecto, y corchetes angulares (`<stdio.h>`) exclusivamente para las bibliotecas estándar del sistema.



 

#### Ejercicio práctico propuesto

Crea tu propia biblioteca matemática personalizada llamada `geometria.h` y `geometria.c`.

* En el header (`geometria.h`), declara una macro para el valor de `PI` (`3.1416`) y el prototipo de una función que calcule el área de un círculo recibiendo el radio (`float calcular_area_circulo(float radio);`).
* Implementa la función en `geometria.c` e inclúyela en un archivo `main.c` para imprimir el resultado.

> **Pista para el ejercicio:**
> * Recuerda proteger tu archivo `geometria.h` utilizando una guarda contra inclusiones múltiples (`#ifndef GEOMETRIA_H ...`).
> 
> 
> * Al compilar desde la terminal, recuerda incluir ambos archivos fuente en la misma instrucción de GCC (`gcc main.c geometria.c -o geometria`).
> 
> 
> 
> 

 

#### Mini resumen

* Los archivos de cabecera (`.h`) centralizan las declaraciones, constantes y macros para compartirlas limpiamente entre múltiples archivos fuente (`.c`).


* Las **guardas de inclusión** (`#ifndef` / `#define` / `#endif`) evitan errores de compilación por procesamiento duplicado de un mismo header.


* Para compilar proyectos modulares en C, es necesario pasar todos los archivos fuente involucrados al compilador de forma conjunta.



### Módulos Independientes: Listas, Pilas y Colas en C

Para este ejemplo, crearemos tres archivos de cabecera independientes (`listas.h`, `pilas.h` y `colas.h`) utilizando funciones en línea (`static inline`) para simplificar su inclusión directa y compilación en un archivo principal `main.c`.

 

#### 1. Archivo de Cabecera: `listas.h`

Este módulo define la estructura de nodos auto-referenciados para una **lista enlazada simple**, permitiendo agregar elementos dinámicamente y recorrerlos.

```c
#ifndef LISTAS_H
#define LISTAS_H

#include <stdio.h>
#include <stdlib.h>
#include <string.h>

// Definición del nodo de la lista enlazada
struct NodoLista {
    int id;
    char descripcion[40];
    struct NodoLista *sig; // Puntero al siguiente nodo
};

// Función para agregar un elemento al frente de la lista
static inline struct NodoLista* agregar_lista(struct NodoLista *cabeza, int id, const char *desc) {
    struct NodoLista *nuevo = (struct NodoLista *) malloc(sizeof(struct NodoLista));
    if (nuevo == NULL) {
        printf("Error: Memoria insuficiente.\n");
        return cabeza;
    }
    nuevo->id = id;
    strcpy(nuevo->descripcion, desc);
    nuevo->sig = cabeza;
    return nuevo;
}

// Función para mostrar los elementos de la lista
static inline void mostrar_lista(struct NodoLista *cabeza) {
    struct NodoLista *actual = cabeza;
    printf("  LISTA ENLAZADA  \n");
    while (actual != NULL) {
        printf("[ID: %d | Desc: %s] -> ", actual->id, actual->descripcion);
        actual = actual->sig;
    }
    printf("NULL\n\n");
}

// Función para liberar la memoria de la lista
static inline void liberar_lista(struct NodoLista *cabeza) {
    struct NodoLista *temp;
    while (cabeza != NULL) {
        temp = cabeza;
        cabeza = cabeza->sig;
        free(temp);
    }
}

#endif /* LISTAS_H */

```

 

#### 2. Archivo de Cabecera: `pilas.h`

Este módulo implementa una **Pila (Stack)** basada en el principio LIFO (*Last-In, First-Out*), utilizando un arreglo estático y un índice de control para la cima (*tope*).

```c
#ifndef PILAS_H
#define PILAS_H

#include <stdio.h>

#define MAX_PILA 50

struct Pila {
    int elementos[MAX_PILA];
    int tope;
};

// Inicializar la pila
static inline void inicializar_pila(struct Pila *p) {
    p->tope = -1;
}

// Verificar si está vacía
static inline int pila_vacia(struct Pila *p) {
    return p->tope == -1;
}

// Agregar elemento a la cima (Push)
static inline void push(struct Pila *p, int valor) {
    if (p->tope >= MAX_PILA - 1) {
        printf("[Pila] Desbordamiento (Stack Overflow)\n");
    } else {
        p->tope++;
        p->elementos[p->tope] = valor;
        printf("[Pila] Elemento %d apilado.\n", valor);
    }
}

// Extraer elemento de la cima (Pop)
static inline int pop(struct Pila *p) {
    if (pila_vacia(p)) {
        printf("[Pila] Subdesbordamiento (Stack Underflow)\n");
        return -1;
    } else {
        int val = p->elementos[p->tope];
        p->tope--;
        return val;
    }
}

#endif /* PILAS_H */

```

 

#### 3. Archivo de Cabecera: `colas.h`

Este módulo implementa una **Cola (Queue)** basada en el principio FIFO (*First-In, First-Out*), gestionando dos extremos independientes (`frente` y `final`).

```c
#ifndef COLAS_H
#define COLAS_H

#include <stdio.h>

#define MAX_COLA 50

struct Cola {
    int elementos[MAX_COLA];
    int frente;
    int final;
};

// Inicializar la cola
static inline void inicializar_cola(struct Cola *c) {
    c->frente = 0;
    c->final = -1;
}

// Verificar si está vacía
static inline int cola_vacia(struct Cola *c) {
    return c->frente > c->final;
}

// Agregar elemento al final (Enqueue)
static inline void encolar(struct Cola *c, int valor) {
    if (c->final >= MAX_COLA - 1) {
        printf("[Cola] La cola esta llena (Queue Overflow).\n");
    } else {
        c->final++;
        c->elementos[c->final] = valor;
        printf("[Cola] Elemento %d encolado.\n", valor);
    }
}

// Extraer elemento del frente (Dequeue)
static inline int desencolar(struct Cola *c) {
    if (cola_vacia(c)) {
        printf("[Cola] La cola esta vacia (Queue Underflow).\n");
        return -1;
    } else {
        int val = c->elementos[c->frente];
        c->frente++;
        return val;
    }
}

#endif /* COLAS_H */

```

 

#### 4. Programa Principal: `main.c`

Finalmente, unificamos los tres módulos personalizados utilizando directivas de inclusión local con comillas dobles (`"..."`).

```c
#include <stdio.h>
#include "listas.h"
#include "pilas.h"
#include "colas.h"

int main(void) {
    printf("=== SISTEMA INTEGRADO DE ESTRUCTURAS DE DATOS ===\n\n");

    // 1. Probando el módulo de Listas
    struct NodoLista *inventario = NULL;
    inventario = agregar_lista(inventario, 101, "Laptop Dell");
    inventario = agregar_lista(inventario, 102, "Monitor LG 24\"");
    mostrar_lista(inventario);

    // 2. Probando el módulo de Pilas (LIFO)
    struct Pila historialAcciones;
    inicializar_pila(&historialAcciones);
    printf("  OPERACIONES DE PILA  \n");
    push(&historialAcciones, 10);
    push(&historialAcciones, 20);
    printf("Elemento extraido de la pila: %d\n\n", pop(&historialAcciones));

    // 3. Probando el módulo de Colas (FIFO)
    struct Cola lineaEspera;
    inicializar_cola(&lineaEspera);
    printf("  OPERACIONES DE COLA  \n");
    encolar(&lineaEspera, 500);
    encolar(&lineaEspera, 500логик); // (501)
    printf("Elemento atendido de la cola: %d\n", desencolar(&lineaEspera));

    // Liberar memoria dinámica de la lista
    liberar_lista(inventario);

    return 0;
}

```

 

#### ¿Cómo compilar este proyecto modular?

Para compilar este programa desde tu terminal, solo necesitas invocar a GCC especificando el archivo principal, ya que los archivos de cabecera son absorbidos automáticamente por el preprocesador:

```bash
gcc main.c -o sistema_estructuras

```

 

### Mini resumen

* La modularización en C permite separar interfaces y estructuras en archivos de cabecera `.h` independientes.


* Las **guardas de inclusión** (`#ifndef`, `#define`, `#endif`) previenen errores críticos de redefinición si un header se incluye múltiples veces.


* Las listas manejan nodos dinámicos mediante punteros, las pilas operan bajo la lógica LIFO con un tope, y las colas administran flujos ordenados FIFO con un frente y un final.


### Sistema Modular de Gestión de un Restaurante en C (`listas.h`, `pilas.h`, `colas.h`)

#### Objetivo de aprendizaje

Aprender a diseñar una aplicación del mundo real totalmente modularizada bajo el estándar **C99**, separando las responsabilidades de las estructuras de datos (listas, pilas y colas) en archivos de cabecera independientes protegidos con guardas de inclusión.

 

#### Explicación teórica y analogía

Imagina la operación diaria de un restaurante concurrido. Cada área de la cocina utiliza una lógica de almacenamiento y control completamente diferente:

1. **El Menú del Restaurante (Lista Enlazada):** Es una base de datos dinámica de platillos disponibles. Los elementos están conectados por punteros y pueden crecer o actualizarse sin seguir un orden estricto de llegada.
2. **La Fila de Pedidos de los Clientes (Cola - FIFO):** Funciona bajo el principio *First-In, First-Out* (Primero en entrar, primero en salir). El primer cliente en llegar y hacer su pedido es el primero que la cocina comienza a preparar.
3. **La Torre de Platos Limpios (Pila - Stack - LIFO):** Funciona bajo el principio *Last-In, First-Out* (Último en entrar, primero en salir). El lavaplatos coloca los platos limpios **encima** de la torre (push), y el chef siempre toma el plato de la **cima** para servir (pop).

En un entorno profesional en C, nunca acumulamos todo este código en un solo archivo fuente. Dividimos cada estructura en su propio archivo `.h` para mantener el código limpio, reutilizable y fácil de mantener.

 

#### Ejemplo de código comentado

A continuación, estructuraremos nuestro proyecto en cuatro archivos independientes: tres cabeceras (`listas.h`, `pilas.h`, `colas.h`) y el programa principal (`main.c`).

##### 1. Archivo de Cabecera: `listas.h`

Maneja los platillos del menú mediante una lista enlazada simple protegida con guardas de inclusión.

```c
#ifndef LISTAS_H
#define LISTAS_H

#include <stdio.h>
#include <stdlib.h>
#include <string.h>

// Definición de la estructura del nodo para el menú
struct Platillo {
    char nombre[30];
    float precio;
    struct Platillo *sig; // Puntero autorreferenciado al siguiente platillo
};

// Función para agregar un platillo al inicio de la lista
static inline struct Platillo* agregar_platillo(struct Platillo *cabeza, const char *nombre, float precio) {
    struct Platillo *nuevo = (struct Platillo *) malloc(sizeof(struct Platillo));
    if (nuevo == NULL) {
        printf("Error: Memoria insuficiente.\n");
        return cabeza;
    }
    strcpy(nuevo->nombre, nombre);
    nuevo->precio = precio;
    nuevo->sig = cabeza;
    return nuevo;
}

// Función para mostrar el menú completo
static inline void mostrar_menu(struct Platillo *cabeza) {
    printf("\n  MENU DEL RESTAURANTE (Lista Enlazada)  \n");
    struct Platillo *actual = cabeza;
    while (actual != NULL) {
        printf("- %s ($%.2f) -> ", actual->nombre, actual->precio);
        actual = actual->sig;
    }
    printf("NULL\n");
}

// Función para liberar la memoria dinámica de la lista
static inline void liberar_menu(struct Platillo *cabeza) {
    struct Platillo *temp;
    while (cabeza != NULL) {
        temp = cabeza;
        cabeza = cabeza->sig;
        free(temp);
    }
}

#endif /* LISTAS_H */

```

##### 2. Archivo de Cabecera: `pilas.h`

Maneja la torre de platos limpios bajo la lógica LIFO.

```c
#ifndef PILAS_H
#define PILAS_H

#include <stdio.h>

#define MAX_PLATOS 10

struct PilaPlatos {
    int ids_platos[MAX_PLATOS];
    int tope; // Índice de la cima de la pila
};

// Inicializar la pila
static inline void inicializar_pila(struct PilaPlatos *p) {
    p->tope = -1; // -1 indica pila vacía
}

// Apilar un plato limpio (Push)
static inline void apilar_plato(struct PilaPlatos *p, int id_plato) {
    if (p->tope >= MAX_PLATOS - 1) {
        printf("[Pila] ¡Torre de platos llena! (Stack Overflow)\n");
    } else {
        p->tope++;
        p->ids_platos[p->tope] = id_plato;
        printf("[Pila] Plato limpio #%d colocado en la cima.\n", id_plato);
    }
}

// Desapilar un plato para servir (Pop)
static inline int desapilar_plato(struct PilaPlatos *p) {
    if (p->tope == -1) {
        printf("[Pila] ¡No hay platos limpios disponibles! (Stack Underflow)\n");
        return -1;
    } else {
        int plato = p->ids_platos[p->tope];
        p->tope--;
        return plato;
    }
}

#endif /* PILAS_H */

```

##### 3. Archivo de Cabecera: `colas.h`

Maneja la fila de pedidos pendientes bajo la lógica FIFO.

```c
#ifndef COLAS_H
#define COLAS_H

#include <stdio.h>

#define MAX_PEDIDOS 10

struct ColaPedidos {
    int ids_pedidos[MAX_PEDIDOS];
    int frente;
    int final;
};

// Inicializar la cola
static inline void inicializar_cola(struct ColaPedidos *c) {
    c->frente = 0;
    c->final = -1;
}

// Encolar un nuevo pedido (Enqueue)
static inline void encolar_pedido(struct ColaPedidos *c, int id_pedido) {
    if (c->final >= MAX_PEDIDOS - 1) {
        printf("[Cola] La fila de pedidos esta llena (Queue Overflow).\n");
    } else {
        c->final++;
        c->ids_pedidos[c->final] = id_pedido;
        printf("[Cola] Pedido #%d anadido a la fila de espera.\n", id_pedido);
    }
}

// Desencolar para atender al siguiente cliente (Dequeue)
static inline int desencolar_pedido(struct ColaPedidos *c) {
    if (c->frente > c->final) {
        printf("[Cola] No hay pedidos pendientes en la fila.\n");
        return -1;
    } else {
        int pedido = c->ids_pedidos[c->frente];
        c->frente++;
        return pedido;
    }
}

#endif /* COLAS_H */

```

##### 4. Programa Principal: `main.c`

Integra los tres módulos independientes para simular el turno del restaurante.

```c
#include <stdio.h>
#include "listas.h"
#include "pilas.h"
#include "colas.h"

int main(void) {
    printf("=== SISTEMA DE GESTION DE RESTAURANTE MODULAR ===\n\n");

    // 1. Inicializamos la Lista del Menú
    struct Platillo *menu = NULL;
    menu = agregar_platillo(menu, "Tacos al Pastor", 85.0f);
    menu = agregar_platillo(menu, "Sopa de Tortilla", 60.0f);
    menu = agregar_platillo(menu, "Agua de Horchata", 25.0f);
    mostrar_menu(menu);

    // 2. Inicializamos la Pila de Platos Limpios
    struct PilaPlatos pilaPlatos;
    inicializar_pila(&pilaPlatos);
    printf("\n  PREPARANDO ESTACION DE PLATOS (PILA)  \n");
    apilar_plato(&pilaPlatos, 101);
    apilar_plato(&pilaPlatos, 102);
    apilar_plato(&pilaPlatos, 103);

    // 3. Inicializamos la Cola de Pedidos
    struct ColaPedidos colaPedidos;
    inicializar_cola(&colaPedidos);
    printf("\n  LLEGADA DE PEDIDOS (COLA)  \n");
    encolar_pedido(&colaPedidos, 501);
    encolar_pedido(&colaPedidos, 502);

    // 4. Operación de la cocina integrando las estructuras
    printf("\n  COCINA EN ACCION  \n");
    int pedidoActual = desencolar_pedido(&colaPedidos); // Sale el 501 (FIFO)
    if (pedidoActual != -1) {
        int platoUsado = desapilar_plato(&pilaPlatos);   // Sale el 103 de la cima (LIFO)
        if (platoUsado != -1) {
            printf("¡Atendiendo pedido #%d! Usando el plato limpio #%d.\n", pedidoActual, platoUsado);
        }
    }

    // Liberar memoria dinámica de la lista del menú
    liberar_menu(menu);
    printf("\nMemoria liberada correctamente. ¡Turno finalizado!\n");

    return 0;
}

```

 

#### Explicación línea por línea

* `#ifndef LISTAS_H ... #define LISTAS_H ... #endif`: Son las **guardas de inclusión**. Evitan que el preprocesador procese un mismo archivo de cabecera múltiples veces durante la compilación, previniendo errores de redefinición.
* `static inline`: Modificador que permite que las funciones definidas dentro de los archivos de cabecera `.h` se integren de manera directa en el programa principal sin generar conflictos de símbolos múltiples al compilar.
* `menu = agregar_platillo(...)`: Actualiza dinámicamente la cabeza de la lista enlazada cada vez que se introduce un nuevo platillo al menú.
* `desencolar_pedido(&colaPedidos)` y `desapilar_plato(&pilaPlatos)`: Demuestran cómo la cocina extrae datos bajo criterios diferentes (cronológico para los pedidos, y físico-estructural de cima para los platos).

 

#### Errores comunes y cómo evitarlos

1. **Olvidar las guardas de inclusión en los headers**: Si omites `#ifndef` / `#define` / `#endif`, incluir varias cabeceras interconectadas provocará errores masivos de compilación por duplicidad de estructuras.
2. **Confundir la compilación de archivos múltiples**: Recuerda que al trabajar con archivos `.h` locales, debes invocarlos usando comillas dobles (`"listas.h"`) y asegurarte de compilar el archivo principal (`gcc main.c -o restaurante`).

 

#### Ejercicio práctico propuesto

Amplía el módulo de colas (`colas.h`) y el programa principal para agregar una función llamada `int contar_pedidos_pendientes(struct ColaPedidos *c)` que devuelva cuántos clientes siguen formados en la fila de espera.

> **Pista para el ejercicio:**
> * Recuerda que en una cola lineal simple, el número de elementos activos se puede calcular matemáticamente utilizando los índices actuales de `final` y `frente` (`final - frente + 1`).
> 
> 

 

#### Mini resumen

* La modularización en C permite separar interfaces y estructuras en archivos de cabecera (`.h`) independientes y reutilizables.
* Las guardas de inclusión protegen al preprocesador contra inclusiones múltiples accidentales.
* Un sistema real combina múltiples estructuras de datos (listas para catálogos, colas para flujos de espera y pilas para gestión de recursos bajo demanda).



### Título del tema: Instalación y Configuración de SQLite para Lenguaje C

#### Objetivo de aprendizaje

Aprender a instalar las bibliotecas de desarrollo de SQLite en diferentes sistemas operativos y configurar el compilador para enlazar correctamente el motor de base de datos en proyectos escritos en C.

 

#### Explicación teórica y analogía

Imagina que quieres instalar una cerradura eléctrica sofisticada en una puerta de madera: la puerta es tu programa en C, pero para que funcione la cerradura necesitas comprar e instalar el mecanismo y los cables que la hacen funcionar (las bibliotecas de desarrollo).

SQLite es una biblioteca escrita en C puro. Para que tu código pueda usar funciones como `sqlite3_open()` o `sqlite3_exec()`, tu computadora necesita dos cosas:

1. **El archivo de cabecera (`sqlite3.h`)**: El "plano" que le dice a tu código qué funciones existen y cómo se llaman.
2. **La biblioteca compilada (`libsqlite3` o `.lib/.dll`)**: El motor binario que ejecuta las operaciones reales cuando compilas tu programa.

 

#### Pasos de instalación según tu sistema operativo

##### 1. En Linux (Ubuntu / Debian / Linux Mint)

Abre tu terminal e instala el paquete de desarrollo ejecutando:

```bash
sudo apt update
sudo apt install libsqlite3-dev
```

##### 2. En Linux (Fedora / RHEL / CentOS)

Dado que utilizas entornos orientados a Fedora, el comando estándar con el gestor de paquetes DNF es:

```bash
sudo dnf install sqlite-devel
```

##### 3. En macOS (Usando Homebrew)

Si utilizas Homebrew para gestionar paquetes en tu Mac:

```bash
brew install sqlite
```

*(Nota: En macOS, Homebrew suele instalar las cabeceras en rutas que el compilador busca automáticamente o mediante flags de ruta).*

##### 4. En Windows

* **Opción A (MSYS2 / MinGW):** Si compilas usando GCC en MSYS2, puedes instalarlo ejecutando:

```bash
pacman -S mingw-w64-ucrt-x86_64-sqlite3
```


**Opción B (Código fuente / Amalgamation):** Puedes descargar directamente el archivo de código fuente unificado (*SQLite Amalgamation* compuesto por `sqlite3.c` y `sqlite3.h`) desde la [página oficial de SQLite](https://www.sqlite.org/download.html?utm_source=gemini) y colocar esos dos archivos directamente en la carpeta de tu proyecto para compilarlos junto con tu código C.

 

#### ¿Cómo compilar un programa que usa SQLite?

Cuando escribes un programa en C que incluye `#include <sqlite3.h>`, el compilador (GCC) necesita que le indiques explícitamente que debe **enlazar** (*link*) la biblioteca de SQLite al generar el archivo ejecutable.

El comando de compilación estándar en Linux/macOS es el siguiente:

```bash
gcc tu_programa.c -o mi_programa -lsqlite3
```

* **Explicación del comando:**
* `tu_programa.c`: Tu archivo de código fuente.
* `-o mi_programa`: El nombre que tendrá el archivo ejecutable resultante.
* `-lsqlite3`: La bandera fundamental que le indica al enlazador (*linker*) que busque e integre la biblioteca `libsqlite3` en tu programa.



 

#### Errores comunes y cómo evitarlos

1. **Error: `sqlite3.h: No such file or directory**`
* *Causa:* No instalaste el paquete de desarrollo (`libsqlite3-dev` o `sqlite-devel`) o el compilador no sabe dónde buscar el archivo de cabecera. Asegúrate de haber instalado las herramientas de desarrollo de tu distribución.


2. **Error: `undefined reference to sqlite3_open` (o similares)**
* *Causa:* Olvidaste agregar la bandera `-lsqlite3` al final del comando `gcc`. Recuerda que compilar archivos con bases de datos requiere enlazar la biblioteca externa de forma explícita.



 

#### Ejercicio práctico propuesto

Abre la terminal de tu sistema operativo, verifica si ya tienes instalado SQLite escribiendo `sqlite3 --version`. Después, intenta compilar un pequeño archivo de prueba que solo incluya `#include <sqlite3.h>` dentro de `main()` usando la bandera `-lsqlite3` para comprobar que tu entorno responde correctamente.

> **Pista para el ejercicio:**
> * Si al ejecutar `gcc prueba.c -lsqlite3` no aparece ningún error de compilación ni de referencias indefinidas, ¡tu entorno está perfectamente configurado y listo para crear aplicaciones persistentes!
> 
> 

 

#### Mini resumen

* SQLite requiere la instalación previa de sus paquetes de desarrollo en el sistema operativo (`libsqlite3-dev` o `sqlite-devel`).
* Para compilar código en C que utilice SQLite, es obligatorio añadir la bandera `-lsqlite3` al comando de GCC.
* Los archivos de cabecera (`sqlite3.h`) estructuran las llamadas en tu código, mientras que la biblioteca enlazada ejecuta las operaciones en disco.



### Título del tema: Persistencia de Datos en C con SQLite (Sistema de Restaurante)

#### Objetivo de aprendizaje

Aprender a conectar un programa en C con una base de datos SQLite utilizando su API oficial (`<sqlite3.h>`), para crear tablas, insertar registros de forma persistente y consultar información en nuestro sistema de restaurante.

 

#### Explicación teórica y analogía

Imagina que las estructuras que vimos antes (listas, pilas y colas) son como la **pizarra del chef**: ahí se anota todo rápido mientras la cocina está abierta, pero al terminar el turno se borra por completo (memoria volátil o RAM).

En cambio, **SQLite** actúa como el **libro contable oficial y la caja fuerte** del restaurante. Es un motor de base de datos ultraligero que guarda la información directamente en un archivo en el disco duro (`.db`), asegurando que los datos del menú, los clientes y los pedidos permanezcan intactos aunque la computadora se apague o reinicies el programa.

Para usar SQLite desde C, utilizamos funciones clave de su biblioteca:

1. `sqlite3_open()`: Abre o crea el archivo de la base de datos.
2. `sqlite3_exec()`: Ejecuta sentencias SQL (como `CREATE TABLE`, `INSERT`, `SELECT`).
3. `sqlite3_close()`: Cierra la conexión de manera segura liberando los recursos.

 

#### Ejemplo de código comentado

Aquí tienes un programa completo, compilable y funcional que crea una base de datos para el restaurante, crea una tabla de platillos, inserta un par de opciones y consulta el contenido:

> *Nota de compilación:* Para compilar este código en tu entorno de desarrollo, recuerda vincular la biblioteca de SQLite agregando la bandera `-lsqlite3` en la terminal (por ejemplo: `gcc restaurante_db.c -o restaurante -lsqlite3`).

```c
#include <stdio.h>
#include <stdlib.h>
#include <sqlite3.h> // Biblioteca oficial de SQLite

// Función callback para procesar los resultados de una consulta SELECT
int callback(void *data, int argc, char **argv, char **azColName) {
    for (int i = 0; i < argc; i++) {
        printf("%s = %s\n", azColName[i], argv[i] ? argv[i] : "NULL");
    }
    printf("         \n");
    return 0;
}

int main(void) {
    sqlite3 *db;
    char *err_msg = 0;
    int rc;

    // 1. Abrir o crear la base de datos en un archivo llamado "restaurante.db"
    rc = sqlite3_open("restaurante.db", &db);
    if (rc != SQLITE_OK) {
        fprintf(stderr, "No se pudo abrir la base de datos: %s\n", sqlite3_errmsg(db));
        sqlite3_close(db);
        return 1;
    }
    printf("¡Base de datos conectada con exito!\n\n");

    // 2. Sentencia SQL para crear una tabla de platillos si no existe
    const char *sql_create = "CREATE TABLE IF NOT EXISTS menu ("
                             "id INTEGER PRIMARY KEY AUTOINCREMENT, "
                             "nombre TEXT NOT NULL, "
                             "precio REAL NOT NULL);";

    rc = sqlite3_exec(db, sql_create, 0, 0, &err_msg);
    if (rc != SQLITE_OK) {
        fprintf(stderr, "Error al crear la tabla: %s\n", err_msg);
        sqlite3_free(err_msg);
        sqlite3_close(db);
        return 1;
    }

    // 3. Insertar registros iniciales en el menú
    const char *sql_insert = "INSERT INTO menu (nombre, precio) VALUES ('Tacos al Pastor', 85.50);"
                             "INSERT INTO menu (nombre, precio) VALUES ('Sopa de Tortilla', 60.00);";

    rc = sqlite3_exec(db, sql_insert, 0, 0, &err_msg);
    if (rc != SQLITE_OK) {
        // Si ya existen por ejecuciones previas, no pasa nada grave, solo lo informamos
        printf("Aviso de insercion (posibles duplicados): %s\n", err_msg);
        sqlite3_free(err_msg);
    } else {
        printf("Platillos insertados en la base de datos.\n\n");
    }

    // 4. Consultar los datos usando SELECT y la función callback
    printf("  CONSULTANDO EL MENU PERSISTENTE  \n");
    const char *sql_select = "SELECT * FROM menu;";
    
    rc = sqlite3_exec(db, sql_select, callback, 0, &err_msg);
    if (rc != SQLITE_OK) {
        fprintf(stderr, "Error en la consulta: %s\n", err_msg);
        sqlite3_free(err_msg);
    }

    // 5. Cerrar la base de datos para liberar recursos
    sqlite3_close(db);
    printf("Conexión cerrada correctamente.\n");

    return 0;
}

```

 

#### Explicación línea por línea

* `sqlite3 *db;`: Declara un puntero al manejador de la base de datos SQLite, el cual utilizaremos en todas las operaciones posteriores.
* `sqlite3_open("restaurante.db", &db);`: Intenta abrir el archivo físico de la base de datos. Si no existe en el directorio actual, SQLite lo crea automáticamente de manera transparente.
* `sqlite3_exec(...)`: Es la navaja suiza de SQLite en C. Envía instrucciones SQL escritas en texto plano (como `CREATE TABLE` o `INSERT`) para que el motor las ejecute directamente.
* `int callback(...)`: Una función de retorno (*callback*) que SQLite llama automáticamente por cada fila de datos que devuelve una consulta `SELECT`. Permite capturar y mostrar los campos y valores fila por fila.
* `sqlite3_close(db);`: Libera la memoria y cierra los descriptores de archivos asociados a la base de datos. Es un paso obligatorio antes de terminar el programa.

 

#### Errores comunes y cómo evitarlos

1. **Olvidar enlazar la biblioteca `-lsqlite3` en el compilador**: Si compilas escribiendo solo `gcc programa.c`, el compilador arrojará errores masivos de referencias indefinidas (*undefined reference to sqlite3_open*). Recuerda incluir siempre `-lsqlite3` al final de tu comando de compilación.
2. **No liberar los mensajes de error (`sqlite3_free`)**: Cuando ocurre un fallo en `sqlite3_exec`, SQLite asigna memoria dinámica para el texto del error en `err_msg`. Si omites liberarlo con `sqlite3_free()`, provocarás una fuga de memoria (*memory leak*).

 

#### Ejercicio práctico propuesto

Modifica el programa anterior para que, antes de realizar la consulta `SELECT`, ejecute una sentencia SQL de actualización (`UPDATE`) que modifique el precio de los "Tacos al Pastor" a `90.00` pesos dentro de la base de datos.

> **Pista para el ejercicio:**
> * Puedes usar una instrucción SQL estándar de actualización, por ejemplo: `UPDATE menu SET precio = 90.00 WHERE nombre = 'Tacos al Pastor';`.
> * Pásala a la función `sqlite3_exec` tal como lo hicimos con las sentencias de inserción o creación de tablas.
> 
> 

 

#### Mini resumen

* SQLite permite llevar los datos de nuestro programa C desde la memoria volátil (RAM) hacia archivos persistentes en el disco duro.
* La API de C utiliza funciones sencillas como `sqlite3_open`, `sqlite3_exec` y `sqlite3_close` para gestionar la conexión y las consultas SQL.
* Las funciones *callback* son esenciales para iterar y procesar de forma dinámica los resultados de las consultas de tipo `SELECT`.





























