# 01 · Introducción a Python

**Versión recomendada:** Python 3.12 (o superior, serie 3.x)
**Nivel:** Principiante absoluto en Python
**Duración estimada de estudio:** 4–6 horas (léelo despacio, ejecutando cada ejemplo)

---

## Índice de contenidos

1. [¿Qué es Python? Historia y filosofía](#1-qué-es-python-historia-y-filosofía)
2. [Instalación y configuración del entorno](#2-instalación-y-configuración-del-entorno)
3. [Intérpretes: CPython, PyPy, Jython, IronPython](#3-intérpretes-cpython-pypy-jython-ironpython)
4. [Entornos virtuales](#4-entornos-virtuales)
5. [Gestión de paquetes con pip](#5-gestión-de-paquetes-con-pip)
6. [Estructura de un proyecto Python](#6-estructura-de-un-proyecto-python)
7. [PEP 8 y estilo de código](#7-pep-8-y-estilo-de-código)
8. [Documentación con docstrings](#8-documentación-con-docstrings)
9. [Ejemplo integrador](#9-ejemplo-integrador)
10. [Buenas prácticas](#10-buenas-prácticas)
11. [Recursos recomendados](#11-recursos-recomendados)
12. [Mini resumen final](#12-mini-resumen-final)

---

## 1. ¿Qué es Python? Historia y filosofía

### 1.1. Empecemos por el principio: ¿qué es un lenguaje de programación?

Antes de hablar de Python, conviene entender **qué es un lenguaje de programación**, porque todo lo demás se apoya en esa idea.

Un ordenador no entiende el español ni el inglés. En su nivel más profundo solo entiende **instrucciones en código máquina**: secuencias de ceros y unos que activan circuitos. Escribir programas directamente en ceros y unos sería lentísimo y prácticamente imposible de mantener. Por eso existen los **lenguajes de programación**: idiomas intermedios, más cercanos al pensamiento humano, que describen *qué queremos que ocurra* y que luego se traducen a las instrucciones que la máquina sí entiende.

**Analogía:** piensa en un lenguaje de programación como el idioma en el que le das instrucciones a un ayudante que solo habla un dialecto muy raro. Tú escribes la orden en un idioma que ambos entendéis (el lenguaje de programación) y un traductor se encarga de convertirla al dialecto del ayudante (el código máquina). Cuanto más parecido sea ese idioma al lenguaje humano, más fácil te resultará dar órdenes correctas.

Python es uno de esos idiomas intermedios. Y es, de hecho, uno de los más cercanos al lenguaje humano que existen hoy.

### 1.2. ¿Qué significa que Python sea "interpretado", "de alto nivel" y "de propósito general"?

Estas tres expresiones aparecen en todas las definiciones de Python, así que vamos a desmenuzarlas una por una.

**"De alto nivel".** Un lenguaje es de alto nivel cuando está **lejos de la máquina y cerca de la persona**. En un lenguaje de bajo nivel (como el ensamblador), tú gestionas manualmente la memoria, los registros del procesador y las direcciones físicas. En uno de alto nivel, escribes cosas como `precio = 100` y el lenguaje se encarga de los detalles internos. Cuanto más alto el nivel, menos te preocupas por el hardware y más por el problema que quieres resolver.

**Analogía:** conducir un coche automático (alto nivel) frente a uno manual con caja de cambios, embrague y doble embrague (bajo nivel). Con el automático llegas igual al destino, pero dedicas la cabeza al viaje en lugar de a la mecánica.

**"Interpretado".** Existen dos grandes formas de traducir un lenguaje a código máquina:

- **Compilado:** un programa llamado *compilador* traduce **todo** el código de una vez a un archivo ejecutable antes de que lo ejecutes (como C o C++). Si hay errores de sintaxis, te enteras *antes* de ejecutar.
- **Interpretado:** un programa llamado *intérprete* lee y ejecuta tu código **línea a línea**, en el momento (como Python). Si hay un error, el programa avanza hasta esa línea y entonces se detiene.

**Analogía:** compilar es traducir un libro completo antes de publicarlo; interpretar es traducir en vivo, frase a frase, mientras alguien lo lee en voz alta. La traducción en vivo es más flexible y rápida de empezar, pero más lenta de ejecutar.

Esta diferencia tiene consecuencias muy prácticas que verás todo el tiempo: en Python **puedes probar una idea en segundos**, escribiendo código en una consola interactiva, sin compilar nada. Esa inmediatez es una de las razones de su éxito.

**"De propósito general".** Significa que Python **no está especializado** en un único tipo de tarea. Sirve para desarrollo web, ciencia de datos, inteligencia artificial, automatización, videojuegos, aplicaciones de escritorio, scripts del sistema… En cambio, hay lenguajes "de propósito específico" como SQL (para bases de datos) o R (muy orientado a estadística). Python vale para casi todo, aunque no siempre sea la mejor herramienta para cada cosa.

### 1.3. Un poco de historia: de dónde viene Python

Conocer la historia ayuda a entender **por qué** el lenguaje es como es.

Python fue creado por **Guido van Rossum**, un programador neerlandés, a finales de los años 80. Por entonces trabajaba en el sistema operativo distribuido **Amoeba** y necesitaba un lenguaje de scripting potente pero sencillo. Se inspiró en un lenguaje anterior llamado **ABC**, del que le gustaba su claridad, pero le añadió cosas que ABC no tenía: extensibilidad, manejo de excepciones y una comunidad abierta.

La **primera versión pública** llegó en **1991**. El nombre no proviene de la serpiente, sino del grupo cómico británico **Monty Python**, del que Guido era fan; quería un nombre corto, un poco irreverente y fácil de recordar.

Hitos que conviene situar en el tiempo:

| Año | Acontecimiento |
|---|---|
| 1991 | Python 0.9.0, primera versión pública |
| 2000 | Python 2.0 (añade recolección de basura moderna y `unicode`) |
| 2008 | Python 3.0 (rompe compatibilidad para limpiar el lenguaje) |
| 2020 | **Fin de soporte de Python 2** |
| 2023 | Python 3.12 |
| 2024+ | Serie 3.13 y posteriores |

**Consecuencia práctica importantísima:** Python 2 está **muerto** desde 2020. Todo el manual, sin excepción, usa **Python 3**. Si encuentras código antiguo con `print "hola"` (sin paréntesis) o `raw_input`, es Python 2 y debes traducirlo o evitarlo.

Guido dirigió el lenguaje durante décadas y en 2018 se retiró del cargo de "Dictador Benevolente de por Vida" (BDFL), pasando la evolución a un consejo de dirección. Hoy Python es un proyecto de código abierto mantenido por una comunidad enorme.

### 1.4. La filosofía: el Zen de Python

Python no solo es un lenguaje: tiene una **filosofía de diseño** explícita. Está recogida en el **PEP 20**, escrito por Tim Peters, y se conoce como el *Zen de Python*.

Puedes leerla tú mismo ejecutando este programa:

```python
import this
```

Salida esperada:

```text
The Zen of Python, by Tim Peters

Beautiful is better than ugly.
Explicit is better than implicit.
Simple is better than complex.
Complex is better than complicated.
Flat is better than nested.
Sparse is better than dense.
Readability counts.
Special cases aren't special enough to break the rules.
Although practicality beats purity.
Errors should never pass silently.
Unless explicitly silenced.
In the face of ambiguity, refuse the temptation to guess.
There should be one-- and preferably only one --obvious way to do it.
Although that way may not be obvious at first unless you're Dutch.
Now is better than never.
Although never is often better than *right* now.
If the implementation is hard to explain, it's a bad idea.
If the implementation is easy to explain, it may be a good idea.
Namespaces are one honking great idea -- let's do more of those!
```

**Explicación línea por línea de las ideas que guiarán este manual:**

- **"Beautiful is better than ugly" (Bello es mejor que feo).** El código se escribe una vez y se lee muchas. Prioriza la estética y la claridad.
- **"Explicit is better than implicit" (Explícito es mejor que implícito).** Evita la "magia": que el código diga claramente lo que hace. Si algo ocurre, que se vea en el código.
- **"Simple is better than complex" (Simple es mejor que complejo).** Si puedes resolver un problema de forma sencilla, no lo compliques.
- **"Readability counts" (La legibilidad cuenta).** Un fragmento que tarda diez minutos en entenderse es un mal fragmento, aunque funcione.
- **"Errors should never pass silently" (Los errores no deben pasar en silencio).** Cuando algo falla, es mejor que el programa te avise que ocultarlo (lo verás en el archivo 06).
- **"There should be one—and preferably only one—obvious way to do it" (Debe haber una forma obvia de hacerlo).** Python favorece la uniformidad: que todo el mundo resuelva lo mismo de la misma manera.
- **"Namespaces are one honking great idea" (Los espacios de nombres son una gran idea).** Organizar el código en módulos y nombres separados evita choques (lo verás en el archivo 07).

Estas frases no son adornos: son **criterios de decisión** que aplicarás a diario. Cuando dudes entre dos formas de escribir algo, pregúntate cuál es más explícita, más simple y más legible.

### 1.5. ¿Por qué Python y no otro lenguaje?

Python **no** es el lenguaje más rápido en ejecución. Para bucles numéricos intensivos, C o C++ lo superan con holgura. ¿Por qué se usa tanto entonces?

La clave es que el **tiempo del programador** suele ser más caro que el tiempo de la máquina. Python es extremadamente productivo: escribes menos código, lo entiendes antes y cometes menos errores. Y cuando el rendimiento es crítico, se delega en librerías escritas en C (como NumPy, en el archivo 18) que son rapidísimas.

| Característica | Python | C / C++ | Java |
|---|---|---|---|
| Curva de aprendizaje | Baja | Alta | Media |
| Velocidad de ejecución | Media/baja | Muy alta | Alta |
| Velocidad de desarrollo | Muy alta | Baja | Media |
| Tipado | Dinámico | Estático | Estático |
| Gestión de memoria | Automática | Manual | Automática |
| Uso típico | IA, datos, web, scripts | Sistemas, videojuegos | Empresa, Android |

**Conclusión:** elige Python cuando quieras **resolver problemas rápido** y leer código claro; elige C/C++ cuando el rendimiento sea lo único que importe. En la práctica, muchísimos sistemas combinan ambos: el "pegamento" en Python y los cuellos de botella en C.

### 1.6. Ejemplos progresivos

**Ejemplo 1 — Tu primera línea de código.** El programa más pequeño posible:

```python
print("Hola, mundo")
```

Salida esperada:

```text
Hola, mundo
```

**Explicación línea por línea:**
- `print` es una **función integrada** de Python: ya viene incluida y sirve para mostrar cosas en pantalla.
- `("Hola, mundo")` son los **paréntesis** que encierran lo que queremos mostrar. Todo lo que va dentro se llama **argumento**.
- `"Hola, mundo"` es una **cadena de texto** (*string*), un texto delimitado por comillas. Las comillas le dicen a Python "esto es texto, no código".

El programa "Hola, mundo" es una tradición en programación: sirve para comprobar que todo está bien instalado y que sabes ejecutar código.

**Ejemplo 2 — Python como calculadora.** No hace falta declarar nada; puedes evaluar expresiones directamente:

```python
print(2 + 3)
print(10 * 4)
print(2 ** 10)
print(7 / 2)
```

Salida esperada:

```text
5
40
1024
3.5
```

**Explicación línea por línea:**
- `2 + 3` → suma: `5`.
- `10 * 4` → multiplicación: `40`.
- `2 ** 10` → **potencia** (dos asteriscos): 2 elevado a 10 = `1024`.
- `7 / 2` → división: devuelve `3.5` (con decimales), no `3`. En Python 3 la división con `/` siempre da un número decimal.

Observa que no escribimos `int resultado = ...` ni nada parecido: Python deduce el tipo solo. Eso es el **tipado dinámico** en acción (lo estudiarás a fondo en el archivo 02).

**Ejemplo 3 — Usar una herramienta de la biblioteca estándar.** Python trae "baterías incluidas". Por ejemplo, matemáticas:

```python
import math

print(math.sqrt(144))
print(math.pi)
```

Salida esperada:

```text
12.0
3.141592653589793
```

**Explicación línea por línea:**
- `import math` **importa** el módulo `math`, un paquete de funciones matemáticas que viene con Python. Importar es como abrir una caja de herramientas.
- `math.sqrt(144)` llama a la función `sqrt` (*square root*, raíz cuadrada) que está dentro de esa caja. El punto `.` significa "de dentro de".
- `math.pi` es una **constante** ya definida con el valor de π.

**Ejemplo 4 — Un mini programa completo.** Combinemos texto, variables y cálculo:

```python
nombre = "Ana"
edad = 30

print(f"Hola, {nombre}. Tienes {edad} años.")
print(f"El año que viene tendrás {edad + 1}.")
```

Salida esperada:

```text
Hola, Ana. Tienes 30 años.
El año que viene tendrás 31.
```

**Explicación línea por línea:**
- `nombre = "Ana"` **crea una variable** llamada `nombre` y le asigna el texto `"Ana"`. El signo `=` no significa "igual que" en matemáticas, sino **"guarda esto en"**.
- `edad = 30` guarda el número `30` en la variable `edad`.
- `f"Hola, {nombre}..."` es una **f-string** (cadena con formato). La `f` delante de las comillas activa el reemplazo: todo lo que va entre `{ }` se evalúa y se inserta.
- `{edad + 1}` demuestra que dentro de las llaves puede haber **cálculos**, no solo variables.

Este ejemplo ya contiene tres conceptos fundamentales (variables, texto y f-strings) que profundizarás en el archivo 02.

### 1.7. Errores comunes

**Error 1 — Olvidar las comillas en un texto.**

```python
print(Hola)
```

```text
NameError: name 'Hola' is not defined
```

*¿Por qué ocurre?* Sin comillas, Python cree que `Hola` es el **nombre de una variable**, no un texto. Como esa variable no existe, lanza `NameError`. **Solución:** `print("Hola")`.

**Error 2 — Usar `print` de Python 2.**

```python
print "Hola"
```

```text
SyntaxError: Missing parentheses in call to 'print'
```

*¿Por qué ocurre?* Es sintaxis de Python 2. En Python 3, `print` es una función y **siempre** lleva paréntesis. **Solución:** `print("Hola")`.

**Error 3 — Confundir `=` con `==`.**

```python
if edad = 30:
    ...
```

```text
SyntaxError: invalid syntax
```

*¿Por qué ocurre?* `=` **asigna** un valor; `==` **compara**. En una condición necesitas comparar. **Solución:** `if edad == 30:` (lo verás en el archivo 03).

### 1.8. Checkpoint de comprensión

Antes de continuar, intenta responder **con tus propias palabras** (sin mirar arriba):

1. ¿Cuál es la diferencia entre un lenguaje **compilado** y uno **interpretado**? Pon un ejemplo de cada uno.
2. ¿Qué significa que Python sea de "alto nivel"?
3. ¿Por qué el Zen dice "explícito es mejor que implícito"? Relaciónalo con un ejemplo.
4. ¿Por qué Python 2 ya no se usa?

Si puedes responder estas preguntas, tienes la base conceptual. Si no, relee las secciones 1.1–1.4 antes de seguir: todo lo demás se apoya en ellas.

### 1.9. Ejercicio propuesto

**Ejercicio 1.1.** Escribe un programa que muestre tu nombre y, en la línea siguiente, el resultado de multiplicar 7 por 6.

**Pista:** necesitas dos llamadas a `print`. Para el cálculo, usa el operador `*`.

**Ejercicio 1.2.** Muestra en pantalla el valor de π y su raíz cuadrada.

**Pista:** importa `math` y usa `math.pi` y `math.sqrt(...)`.

---

## 2. Instalación y configuración del entorno

### 2.1. ¿Qué necesitas exactamente para programar en Python?

Programar en Python requiere, como mínimo, **dos piezas**:

1. **El intérprete de Python**: el programa que lee y ejecuta tu código. Sin él, tu computadora no entiende Python.
2. **Un editor de código**: donde escribes los programas. Técnicamente podrías usar el Bloc de notas, pero un editor pensado para programar te ayuda enormemente (colorea el código, señala errores, autocompleta).

Opcionalmente añadirás una tercera pieza: **gestores de entornos y paquetes**, que verás en las secciones 4 y 5.

### 2.2. ¿Qué versión instalar?

Instala siempre la **última versión estable de la serie 3.x** (Python 3.12 o superior al escribir este manual). Motivos:

- Python 2 está **sin soporte** desde 2020.
- Las versiones recientes traen mejoras de rendimiento y sintaxis (por ejemplo, `match-case`, visto en el archivo 03).
- Las librerías modernas ya solo soportan Python 3.

No instales una versión "muy nueva" en un proyecto de producción sin comprobar la compatibilidad de tus librerías.

### 2.3. Instalación paso a paso por sistema operativo

**Windows**

1. Entra en <https://www.python.org/downloads/> y descarga el instalador.
2. **Muy importante:** en la primera pantalla, marca la casilla **"Add python.exe to PATH"** antes de pulsar *Install Now*. Esa casilla hace que puedas escribir `python` en la terminal desde cualquier carpeta.
3. Elige *Install Now* y espera a que termine.

**macOS**

La forma más cómoda es con [Homebrew](https://brew.sh/):

```bash
brew install python@3.12
```

También puedes descargar el instalador oficial de python.org.

**Linux (Debian/Ubuntu)**

```bash
sudo apt update
sudo apt install python3 python3-pip python3-venv
```

En muchas distribuciones Linux, Python 3 ya viene preinstalado. Compruébalo antes con el comando de la sección siguiente.

### 2.4. Verificar que la instalación funcionó

Abre una terminal (Símbolo del sistema o PowerShell en Windows; Terminal en macOS/Linux) y escribe:

```bash
python --version
```

Si no funciona, prueba:

```bash
python3 --version
```

Salida esperada (el número puede variar):

```text
Python 3.12.3
```

**¿Por qué a veces es `python` y a veces `python3`?** En Linux y macOS, durante años existió `python` apuntando a Python 2, así que se estandarizó `python3`. En Windows y en instalaciones recientes, `python` suele bastar. Usa el que funcione en tu sistema.

**¿Qué significa que aparezca "command not found"?** Que el intérprete no está instalado o no está en la variable `PATH` (la lista de carpetas donde el sistema busca programas). En Windows, reinstala marcando "Add to PATH". En Linux, instala el paquete `python3`.

### 2.5. El intérprete interactivo (REPL)

Python incluye un modo interactivo llamado **REPL**, por *Read–Eval–Print Loop* (Leer–Evaluar–Imprimir–Repetir). Es una conversación con Python: escribes algo, él responde al instante.

Para entrar, escribe en la terminal:

```bash
python
```

Verás algo así:

```text
Python 3.12.3 (main, ...) [GCC ...] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> 
```

El símbolo `>>>` es el **prompt** (la invitación a escribir). Prueba:

```text
>>> 2 + 3
5
>>> "hola".upper()
'HOLA'
>>> nombre = "Ana"
>>> print(f"Hola, {nombre}")
Hola, Ana
>>> exit()
```

**Observaciones clave:**
- En el REPL, el resultado de una expresión se muestra **automáticamente**, sin necesidad de `print`. Por eso `2 + 3` muestra `5`.
- Puedes crear variables y usarlas en líneas siguientes: el REPL **recuerda** lo que escribiste.
- Para salir usa `exit()` o `quit()`, o pulsa `Ctrl+D` (Linux/macOS) / `Ctrl+Z` y Enter (Windows).

**¿Para qué sirve el REPL?** Para **experimentar**. Antes de escribir un programa completo, prueba una idea aquí. Es una de las mayores ventajas de un lenguaje interpretado.

### 2.6. Tu primer programa en un archivo

El REPL es genial para probar, pero los programas reales se guardan en archivos para poder ejecutarlos cuantas veces quieras.

**Paso 1.** Crea una carpeta para practicar y, dentro, un archivo llamado `hola.py`.

**Paso 2.** Escribe dentro:

```python
print("¡Hola, mundo!")
```

**Paso 3.** Desde la terminal, sitúate en esa carpeta y ejecuta:

```bash
python hola.py
```

Salida esperada:

```text
¡Hola, mundo!
```

**Explicación línea por línea:**
- La extensión `.py` indica que es un archivo de Python.
- Al ejecutar `python hola.py`, el intérprete **lee el archivo de arriba abajo** y ejecuta cada línea.
- `print(...)` muestra el texto. A diferencia del REPL, aquí **no** se imprime el resultado de las expresiones automáticamente: solo se muestra lo que pases a `print`.

**Diferencia clave REPL vs. archivo:** en el REPL, escribir `2 + 3` muestra `5`. En un archivo, `2 + 3` no muestra nada; necesitas `print(2 + 3)`. Muchos principiantes se confunden con esto.

### 2.7. Elegir un editor de código

No necesitas nada sofisticado para empezar, pero un buen editor multiplica tu productividad.

| Editor | Cuándo elegirlo |
|---|---|
| **VS Code** | Recomendado para empezar. Ligero, gratis, con extensión oficial de Python |
| **PyCharm** | IDE completo, ideal para proyectos grandes |
| **Thonny** | Pensado para principiantes absolutos |
| **Vim / Neovim** | Si ya te manejas en terminal y quieres velocidad |

Para este manual, **VS Code + la extensión de Python** es una elección excelente.

### 2.8. Errores comunes

**Error 1 — `python: command not found`.**

```text
bash: python: command not found
```

*Causa:* Python no está instalado o no está en el `PATH`. *Solución:* en Windows reinstala marcando "Add to PATH"; en Linux prueba `python3` o instala el paquete.

**Error 2 — Editar un archivo y ejecutar otro.**

```bash
python programa.py
```

...pero estabas editando `prueba.py`. *Síntoma:* no ves los cambios. *Solución:* comprueba la ruta y el nombre; puedes usar `python --version` o un `print` de prueba.

**Error 3 — Ejecutar un archivo vacío o mal guardado.**

Si `hola.py` no muestra nada, revisa que guardaste el archivo y que `print` está bien escrito (minúsculas, paréntesis).

**Error 4 — Mezclar tabuladores y espacios.**

Aunque aún no lo notes, Python es estricto con la indentación (los espacios al inicio de línea). Configura tu editor para insertar **4 espacios** en lugar de tabuladores. Lo verás formalmente en la sección 7.

### 2.9. Checkpoint de comprensión

1. ¿Cuál es la diferencia entre usar el **REPL** y ejecutar un **archivo**?
2. ¿Por qué en el REPL `2 + 3` muestra `5` pero en un archivo no muestra nada?
3. ¿Qué hace exactamente la casilla "Add to PATH" al instalar Python en Windows?

### 2.10. Ejercicio propuesto

**Ejercicio 2.1.** Crea un archivo `presentacion.py` que imprima en dos líneas distintas tu nombre y tu lenguaje de programación favorito.

**Pista:** puedes usar dos `print`, o un solo `print` con el carácter `\n` (salto de línea) dentro del texto.

**Ejercicio 2.2.** En el REPL, calcula cuántos segundos hay en un día (sin usar calculadora). Luego escribe el mismo cálculo en un archivo `segundos.py` y haz que lo muestre con `print`.

**Pista:** un día tiene 24 horas × 60 minutos × 60 segundos.

---

## 3. Intérpretes: CPython, PyPy, Jython, IronPython

### 3.1. Python es una especificación, no solo un programa

Aquí hay un matiz que sorprende a muchos: **"Python" es ante todo una especificación del lenguaje** (un conjunto de reglas sobre qué sintaxis es válida y qué debe hacer). Existen varias **implementaciones** que cumplen esa especificación. La más común es **CPython**, que es lo que instalas normalmente.

**Analogía:** el español es un idioma con unas reglas; distintos hablantes (de España, México, Argentina) lo hablan con acentos y matices. Del mismo modo, varias implementaciones "hablan Python" con diferencias de rendimiento y plataforma.

### 3.2. Las implementaciones principales

| Intérprete | Plataforma | Característica principal |
|---|---|---|
| **CPython** | Multiplataforma | La implementación de referencia, escrita en C. La que instalas por defecto |
| **PyPy** | Multiplataforma | Usa compilación JIT; muy rápido en bucles largos |
| **Jython** | Máquina virtual de Java | Se ejecuta sobre la JVM; integra con Java |
| **IronPython** | .NET | Se ejecuta sobre .NET; integra con C# y compañía |
| **MicroPython** | Microcontroladores | Versión mínima para hardware |

### 3.3. ¿Cuál deberías usar?

Para este manual y para el 95 % de los casos: **CPython**. Es con el que funcionan todas las librerías que verás (NumPy, Pandas, Flask, etc.) y el que instalan las guías oficiales.

**PyPy** solo tiene sentido si tu programa pasa mucho tiempo en **bucles de cálculo puro** y no depende de extensiones en C. Aunque es muy rápido en esos casos, algunas librerías científicas no funcionan igual de bien con él. No lo necesitas para aprender.

### 3.4. ¿Cómo sé qué intérprete estoy usando?

Python puede decirte con qué implementación se está ejecutando:

```python
import platform
import sys

print(sys.implementation.name)
print(platform.python_implementation())
print(sys.version)
```

Salida esperada (en CPython):

```text
cpython
CPython
3.12.3 (main, ...) [GCC ...]
```

**Explicación línea por línea:**
- `import platform` y `import sys` importan dos módulos de la biblioteca estándar con información del sistema.
- `sys.implementation.name` devuelve el nombre corto de la implementación: `"cpython"`, `"pypy"`, etc.
- `platform.python_implementation()` devuelve el nombre legible: `"CPython"`.
- `sys.version` es la cadena de versión completa, con la fecha de compilación.

### 3.5. Errores comunes

**Error — Instalar PyPy esperando que todo vaya más rápido.**

PyPy acelera bucles intensivos, pero puede **no ser compatible** con extensiones en C (como partes de NumPy) y su arranque es más lento. No es una mejora universal. Para aprender y para la mayoría de proyectos, **CPython es la elección correcta**.

### 3.6. Ejercicio propuesto

**Ejercicio 3.1.** Escribe un programa `entorno.py` que muestre por pantalla: la versión de Python, la implementación y el sistema operativo.

**Pista:** investiga la función `platform.system()` (devuelve `"Windows"`, `"Linux"`, `"Darwin"` en macOS).

---

## 4. Entornos virtuales

### 4.1. El problema que resuelven

Imagina que tienes dos proyectos:

- **Proyecto A** necesita la librería `Django` versión 3.
- **Proyecto B** necesita `Django` versión 5.

Si instalas las librerías "globalmente" (en el Python del sistema), solo puede haber **una** versión de Django a la vez. Instalar la 5 para B **rompe** A. Este problema se llama **conflicto de dependencias** y es una de las mayores fuentes de dolor en Python.

### 4.2. La solución: entornos virtuales

Un **entorno virtual** es una carpeta aislada que contiene **su propia copia del intérprete y sus propias librerías**. Cada proyecto tiene su entorno, de modo que los proyectos **no interfieren entre sí**.

**Analogía:** un entorno virtual es como una **cocina independiente** para cada receta. Si en una cocina necesitas "harina versión 2" y en otra "harina versión 5", no hay problema: cada cocina tiene sus propios ingredientes. Sin entornos virtuales, todos cocinarían en la misma cocina y se pisarían.

### 4.3. Herramientas disponibles

| Herramienta | Descripción |
|---|---|
| **venv** | Integrada en Python. La opción por defecto y la que usaremos |
| **virtualenv** | Más antigua, a veces más rápida |
| **conda** | Gestiona entornos y paquetes, popular en ciencia de datos |
| **pipenv** | Combina `pip` y `venv` con un archivo `Pipfile` |
| **poetry** | Gestión moderna de dependencias y empaquetado |

Para aprender, **venv** es suficiente y siempre está disponible.

### 4.4. Crear, activar y desactivar un entorno

**Paso 1 — Crear el entorno.** Desde la carpeta de tu proyecto:

```bash
python -m venv .venv
```

**Explicación línea por línea:**
- `python -m venv` ejecuta el módulo `venv` que viene con Python (la opción `-m` significa "ejecuta este módulo").
- `.venv` es el nombre de la carpeta que se creará. El punto del inicio la hace "oculta" y es una convención muy extendida. Puedes llamarla `venv` o `entorno`; da igual, pero sé consistente.

Tras ejecutarlo, aparecerá una carpeta `.venv/` con el intérprete y el gestor de paquetes aislados.

**Paso 2 — Activar el entorno.** Depende del sistema:

```bash
# Linux / macOS
source .venv/bin/activate

# Windows (PowerShell)
.venv\Scripts\Activate.ps1

# Windows (cmd)
.venv\Scripts\activate.bat
```

Cuando está activo, el prompt cambia para avisarte:

```text
(.venv) $ 
```

Ese `(.venv)` es la señal de que **todo lo que instales ahora irá a este entorno**, no al sistema.

**Paso 3 — Desactivar.** Cuando termines:

```bash
deactivate
```

El prompt vuelve a la normalidad.

### 4.5. Comprobar que el aislamiento funciona

Con el entorno activado, ejecuta:

```bash
# Linux / macOS
which python

# Windows
where python

pip list
```

Salida esperada:

```text
/home/roy/proyecto/.venv/bin/python
Package    Version
---------- -------
pip        24.0
```

Fíjate en dos cosas: la ruta del `python` apunta **dentro de `.venv`**, y la lista de paquetes es mínima (solo `pip` y poco más). Eso demuestra que el entorno está **limpio y aislado**.

### 4.6. Errores comunes

**Error 1 — Olvidar activar el entorno.**

Si no ves `(.venv)` en el prompt, instalarás paquetes en el Python global. *Síntoma:* tus librerías aparecen "en todos los proyectos". *Solución:* activa siempre el entorno antes de trabajar.

**Error 2 — Versionar la carpeta `.venv` en Git.**

La carpeta `.venv` es **específica de tu máquina** (contiene rutas absolutas y binarios). Nunca debe subirse a un repositorio. *Solución:* añádela a `.gitignore` (lo verás en la sección 6).

**Error 3 — Crear el entorno dentro de una carpeta sincronizada.**

Carpetas como Dropbox, OneDrive o Google Drive pueden corromper los binarios del entorno. *Solución:* crea los entornos fuera de esas carpetas.

**Error 4 — Usar `sudo pip install`.**

Instalar con permisos de administrador en el Python del sistema puede romper el sistema operativo. *Solución:* crea un entorno virtual; nunca uses `sudo` con `pip`.

### 4.7. Checkpoint de comprensión

1. ¿Qué problema concreto resuelven los entornos virtuales?
2. ¿Cómo sabes, mirando el prompt, que un entorno está activo?
3. ¿Por qué no debes subir `.venv/` a Git?

### 4.8. Ejercicio propuesto

**Ejercicio 4.1.** Crea un entorno virtual llamado `.venv`, actívalo, comprueba con `pip list` que está limpio y desactívalo.

**Pista:** recuerda que en Windows la ruta de activación es `Scripts` y en Linux/macOS es `bin`.

**Ejercicio 4.2.** Crea dos entornos en dos carpetas distintas e instala en uno una librería (por ejemplo `requests`). Comprueba que en el otro entorno **no** aparece.

**Pista:** usa `pip list` en cada entorno activado.

---

## 5. Gestión de paquetes con pip

### 5.1. ¿Qué es un paquete y qué es pip?

Un **paquete** (o librería) es código que otra persona ya escribió y publicó para que lo reutilices: por ejemplo, `requests` para hacer peticiones web, o `pandas` para analizar datos. En lugar de reinventar la rueda, la importas.

**pip** es el **gestor de paquetes** oficial de Python: la herramienta que descarga, instala, actualiza y desinstala paquetes. Los paquetes viven en **PyPI** (*Python Package Index*), un enorme repositorio público con cientos de miles de librerías.

**Analogía:** PyPI es una tienda gigante de herramientas; `pip` es el repartidor que va a la tienda, coge lo que pides y lo deja en tu taller (tu entorno virtual).

### 5.2. Instalar un paquete

Con el entorno virtual activado:

```bash
pip install requests
```

Salida esperada (resumida):

```text
Collecting requests
  Downloading requests-2.32.0-py3-none-any.whl (64 kB)
Installing collected packages: requests, charset-normalizer, idna, urllib3, certifi
Successfully installed certifi-2024.2.2 charset-normalizer-3.3.2 ...
```

Observa que `pip` instaló **varias** cosas: `requests` depende de otras librerías (*dependencias*) y pip las resolvió automáticamente. Esa gestión de dependencias es una de las grandes ventajas de los gestores de paquetes.

Comprobar la versión instalada:

```bash
pip show requests
```

```text
Name: requests
Version: 2.32.0
Summary: Python HTTP for Humans.
Home-page: https://requests.readthedocs.io
...
```

### 5.3. Congelar y restaurar dependencias

Aquí está el corazón del trabajo reproducible. Si solo tú tienes las librerías instaladas, ¿cómo las consigue otra persona (o tú en otro ordenador)? Con un archivo que **lista las dependencias**.

```bash
pip freeze > requirements.txt
```

**Explicación línea por línea:**
- `pip freeze` imprime **todos** los paquetes instalados en el entorno con su versión exacta.
- `>` redirige esa salida al archivo `requirements.txt` (lo crea o lo sobrescribe).

Contenido típico de `requirements.txt`:

```text
certifi==2024.2.2
charset-normalizer==3.3.2
idna==3.7
requests==2.32.0
urllib3==2.2.1
```

Restaurar esas dependencias en otro entorno es tan simple como:

```bash
pip install -r requirements.txt
```

**Explicación línea por línea:**
- `-r` viene de *requirement* ("requisito"): le dice a pip que lea la lista desde un archivo en lugar de la línea de comandos.

Este archivo es **oro puro**: permite que cualquiera reproduzca tu entorno exacto.

### 5.4. Versiones y restricciones

A veces no quieres una versión exacta, sino un rango. La sintaxis:

```bash
pip install "flask==3.0.3"     # versión exacta
pip install "flask>=3.0"       # al menos la 3.0
pip install "flask<4"          # cualquier versión anterior a la 4
pip install "flask~=3.0.0"     # compatible con la 3.0.x (parchea pero no salta de 3.1)
```

**¿Por qué fijar versiones?** Para garantizar que mañana el proyecto funcione igual que hoy. Si una librería publica una versión con cambios incompatibles, tu proyecto podría romperse sin avisar. Fijar versiones (o usar rangos conservadores) evita sorpresas.

### 5.5. Desinstalar y actualizar

```bash
pip uninstall requests
python -m pip install --upgrade pip
```

- `pip uninstall` elimina un paquete.
- `python -m pip install --upgrade pip` actualiza el propio pip. Usar `python -m pip` en lugar de `pip` es más seguro: garantiza que actualizas el pip **del intérprete activo**.

### 5.6. Errores comunes

**Error 1 — `pip: command not found`.**

*Causa:* pip no está en el `PATH` o el entorno no está activado. *Solución:* usa `python -m pip` en lugar de `pip`, que siempre invoca el pip correcto.

**Error 2 — Instalar sin entorno virtual.**

*Síntoma:* los paquetes aparecen "en todo el sistema" y provocan conflictos. *Solución:* activa un entorno virtual antes de instalar.

**Error 3 — Dependencias "fantasma".**

*Síntoma:* en tu máquina funciona, en la de un compañero no. *Causa:* olvidaste congelar las dependencias. *Solución:* mantén `requirements.txt` actualizado con `pip freeze`.

**Error 4 — Permisos denegados.**

```text
ERROR: Could not install packages due to an OSError: [Errno 13] Permission denied
```

*Causa:* intentas instalar en carpetas del sistema. *Solución:* crea y activa un entorno virtual; **nunca** uses `sudo pip`.

### 5.7. Checkpoint de comprensión

1. ¿Qué diferencia hay entre PyPI y pip?
2. ¿Para qué sirve exactamente `requirements.txt`?
3. ¿Por qué se recomienda `python -m pip` en lugar de `pip`?

### 5.8. Ejercicio propuesto

**Ejercicio 5.1.** Instala el paquete `rich` (embellece la salida de la terminal), genera un `requirements.txt`, desinstala `rich` y luego reinstálalo desde el archivo.

**Pista:** compara `pip freeze` antes y después de desinstalar; usa `pip install -r requirements.txt` para restaurar.

---

## 6. Estructura de un proyecto Python

### 6.1. ¿Por qué importa la estructura?

Cuando un proyecto crece, tener todo en un archivo se vuelve insostenible. Una estructura clara:

- Facilita encontrar cada cosa.
- Permite separar código, tests y documentación.
- Hace que otros (y tu yo del futuro) entiendan el proyecto en minutos.

No existe **una** estructura correcta, pero hay plantillas probadas.

### 6.2. Plantilla de proyecto (src layout)

```text
mi_proyecto/
├── .venv/                  # Entorno virtual (NO se versiona)
├── .gitignore              # Archivos que Git debe ignorar
├── README.md               # Descripción del proyecto
├── requirements.txt        # Dependencias
├── pyproject.toml          # Metadatos y configuración (moderno)
├── src/
│   └── mi_proyecto/
│       ├── __init__.py     # Marca la carpeta como paquete
│       ├── main.py         # Punto de entrada
│       └── utils.py        # Utilidades
├── tests/
│   ├── __init__.py
│   └── test_utils.py
└── docs/
    └── index.md
```

**¿Por qué `src/`?** Colocar el código dentro de `src/` evita un error sutil: que importes tu paquete "por accidente" desde la carpeta actual sin haberlo instalado. Es el estilo recomendado por la comunidad para proyectos serios.

### 6.3. El archivo `__init__.py`

Un archivo llamado `__init__.py` **marca una carpeta como paquete importable**. Puede estar vacío, o inicializar el paquete:

```python
"""Paquete mi_proyecto."""

__version__ = "0.1.0"
```

**Explicación línea por línea:**
- La primera cadena con triples comillas es el **docstring del paquete** (lo verás en la sección 8).
- `__version__` es una convención: guarda la versión del paquete para poder consultarla.

### 6.4. El punto de entrada `main.py`

```python
def saludar(nombre: str) -> str:
    """Devuelve un saludo personalizado."""
    return f"Hola, {nombre}!"


def main() -> None:
    """Función principal del programa."""
    print(saludar("mundo"))


if __name__ == "__main__":
    main()
```

Salida esperada al ejecutarlo:

```text
Hola, mundo!
```

**Explicación línea por línea:**
- `def saludar(nombre: str) -> str:` define una **función** llamada `saludar`. El `nombre: str` es una **anotación de tipo** (indica que se espera un texto) y `-> str` indica que devuelve un texto. Las funciones se estudian en el archivo 04.
- `return f"Hola, {nombre}!"` **devuelve** el resultado usando una f-string.
- `def main() -> None:` define la función principal. `-> None` indica que no devuelve nada.
- `if __name__ == "__main__":` es una línea que parece rara pero es importantísima: significa "ejecuta `main()` **solo si este archivo se ejecuta directamente**". Si otro archivo lo importa, `main()` **no** se ejecuta. Así el archivo sirve tanto de programa como de módulo reutilizable.

### 6.5. El archivo `.gitignore`

Git es el sistema de control de versiones. El archivo `.gitignore` lista lo que **no** debe guardarse en el repositorio:

```text
.venv/
__pycache__/
*.pyc
.env
*.egg-info/
```

**Explicación línea por línea:**
- `.venv/` excluye el entorno virtual (específico de tu máquina).
- `__pycache__/` y `*.pyc` excluyen archivos compilados que Python genera automáticamente.
- `.env` excluye variables de entorno con secretos (contraseñas, claves).
- `*.egg-info/` excluye metadatos de empaquetado generados.

### 6.6. Errores comunes

**Error 1 — Poner todo en un único archivo.**

*Síntoma:* un archivo de miles de líneas imposible de navegar. *Solución:* separa por responsabilidad: modelos, utilidades, punto de entrada.

**Error 2 — Versionar `.venv/` o `__pycache__/`.**

*Síntoma:* el repositorio pesa cientos de megabytes. *Solución:* añádelos a `.gitignore`.

**Error 3 — Nombres de carpeta con guiones.**

```text
mi-proyecto/     # ❌ no se puede importar
```

Python no permite importar paquetes con guiones. *Solución:* usa guion bajo: `mi_proyecto`.

**Error 4 — Olvidar `if __name__ == "__main__":`.**

*Síntoma:* al importar tu módulo desde otro archivo, se ejecuta el programa entero. *Solución:* envuelve la ejecución en esa condición.

### 6.7. Checkpoint de comprensión

1. ¿Para qué sirve `__init__.py`?
2. ¿Qué hace exactamente `if __name__ == "__main__":`?
3. ¿Por qué `.venv/` debe ir en `.gitignore`?

### 6.8. Ejercicio propuesto

**Ejercicio 6.1.** Crea la estructura anterior con un paquete `src/saludos/` que contenga `__init__.py` y `main.py`. Ejecútalo con `python -m saludos.main`.

**Pista:** investiga qué hace la opción `-m` de Python (ejecutar un módulo por su nombre en lugar de por su ruta).

---

## 7. PEP 8 y estilo de código

### 7.1. ¿Qué es un PEP?

**PEP** significa *Python Enhancement Proposal* (Propuesta de Mejora de Python). Son documentos que describen cómo evoluciona el lenguaje. El **PEP 8** es la **guía de estilo oficial**: un conjunto de reglas sobre cómo escribir Python de forma consistente y legible.

**¿Por qué importa el estilo?** Porque el código se lee mucho más de lo que se escribe. Un estilo uniforme reduce el esfuerzo mental y los errores. Dos programadores que siguen PEP 8 pueden leer el código del otro casi sin fricción.

### 7.2. Las reglas más importantes

| Regla | Correcto | Incorrecto |
|---|---|---|
| Indentación | 4 espacios | 2 espacios o tabulador |
| Longitud de línea | ≤ 79 caracteres | 200 caracteres |
| Nombres de variables/funciones | `snake_case` | `camelCase` |
| Nombres de clases | `PascalCase` | `snake_case` |
| Constantes | `MAYUSCULAS` | `minusculas` |
| Espacios alrededor de operadores | `a = b + c` | `a=b+c` |
| Líneas en blanco entre funciones | 2 | 0 |

**Explicación de los nombres:**
- `snake_case` (serpiente): palabras en minúsculas separadas por guion bajo. `calcular_total`, `nombre_usuario`.
- `PascalCase`: cada palabra empieza en mayúscula, sin separadores. `CuentaBancaria`, `Usuario`.
- `MAYUSCULAS_CON_GUION_BAJO`: para constantes, valores que no cambian. `IVA`, `MAX_INTENTOS`.

### 7.3. Ejemplo de código conforme a PEP 8

```python
# Constantes en mayúsculas
IVA = 0.21
MAX_INTENTOS = 3


class Calculadora:
    """Una calculadora sencilla."""

    def sumar(self, a: float, b: float) -> float:
        """Devuelve la suma de a y b."""
        return a + b


def calcular_precio(base: float) -> float:
    """Calcula el precio con IVA incluido."""
    return base * (1 + IVA)


if __name__ == "__main__":
    calc = Calculadora()
    print(calc.sumar(2, 3))
    print(calcular_precio(100))
```

Salida esperada:

```text
5
121.0
```

**Explicación línea por línea:**
- `IVA = 0.21` es una constante (mayúsculas) con el valor del IVA.
- `class Calculadora:` define una clase (PascalCase). La clase agrupa datos y comportamiento (archivo 05).
- `def sumar(self, a, b):` define un método. `self` es la referencia al objeto actual.
- Las funciones están separadas por **dos** líneas en blanco: PEP 8 lo pide para que se distingan.
- `calcular_precio(100)` devuelve `100 * (1 + 0.21) = 121.0`.

### 7.4. Herramientas automáticas

No memorices todas las reglas: deja que las herramientas lo hagan por ti. **ruff** es hoy la opción recomendada: es un linter y formateador ultrarrápido.

```bash
pip install ruff
ruff check .        # detecta problemas
ruff format .       # formatea automáticamente
```

Otras herramientas: `black` (formateador), `flake8` (linter), `isort` (ordena imports), `mypy` (verifica tipos).

### 7.5. Errores comunes

**Error 1 — Mezclar estilos de nombres.**

*Síntoma:* un archivo usa `calcularTotal` y otro `calcular_total`. *Solución:* elige `snake_case` para funciones y variables, siempre.

**Error 2 — Líneas kilométricas.**

*Síntoma:* hay que desplazar horizontalmente para leer. *Solución:* limita a 79–88 caracteres; parte expresiones largas en varias líneas.

**Error 3 — Ignorar el linter al principio.**

*Síntoma:* cambiar el estilo después de tener mucho código es doloroso. *Solución:* configura `ruff` desde el primer día.

### 7.6. Checkpoint de comprensión

1. ¿Qué significa PEP y por qué existe el PEP 8?
2. Convierte `miVariableGlobal` y `calcularAreaTotal` a las convenciones correctas.
3. ¿Por qué se recomienda usar herramientas automáticas en lugar de memorizar las reglas?

### 7.7. Ejercicio propuesto

**Ejercicio 7.1.** Toma el código del ejercicio 6.1, pásalo por `ruff check` y corrige todos los avisos.

**Pista:** instala `ruff` en tu entorno virtual y ejecútalo sobre el archivo: `ruff check archivo.py`.

---

## 8. Documentación con docstrings

### 8.1. ¿Qué es un docstring?

Un **docstring** (*documentation string*) es una cadena de texto especial que se coloca **como primera línea** dentro de un módulo, una clase o una función, y que **documenta su propósito**. No es un simple comentario: Python lo guarda en el atributo `__doc__` y herramientas como `help()` lo muestran.

**Analogía:** un docstring es como la etiqueta de un frasco de medicina: explica qué contiene y para qué sirve, sin que tengas que abrirlo.

**Diferencia con los comentarios `#`:** los comentarios son para el programador que lee el código fuente y desaparecen al ejecutar. Los docstrings son **accesibles en tiempo de ejecución** y se usan para generar documentación automática.

### 8.2. Docstring de función

```python
def area_rectangulo(base: float, altura: float) -> float:
    """Calcula el área de un rectángulo.

    Args:
        base: Longitud de la base.
        altura: Longitud de la altura.

    Returns:
        El área como número flotante.

    Raises:
        ValueError: Si base o altura son negativas.
    """
    if base < 0 or altura < 0:
        raise ValueError("Las dimensiones no pueden ser negativas")
    return base * altura


print(area_rectangulo(3, 4))
print(area_rectangulo.__doc__)
```

Salida esperada:

```text
12
Calcula el área de un rectángulo.

    Args:
        base: Longitud de la base.
        ...
```

**Explicación línea por línea:**
- La primera línea del docstring es el **resumen**: una sola frase que describe qué hace.
- `Args:` documenta cada parámetro (estilo Google, muy usado).
- `Returns:` documenta qué devuelve.
- `Raises:` documenta qué excepciones puede lanzar.
- `area_rectangulo.__doc__` accede al docstring **en tiempo de ejecución**, demostrando que no es un comentario cualquiera.

### 8.3. Docstring de módulo y de clase

```python
"""Módulo de geometría.

Proporciona funciones para calcular áreas de figuras planas.
"""


class Circulo:
    """Representa un círculo.

    Attributes:
        radio: El radio del círculo.
    """

    def __init__(self, radio: float) -> None:
        """Inicializa el círculo con un radio."""
        self.radio = radio
```

**Explicación:**
- El **docstring del módulo** va al principio del archivo, antes de cualquier código.
- El **docstring de la clase** describe qué representa y sus atributos.
- El **docstring de `__init__`** describe la inicialización.

### 8.4. Consultar la ayuda

```python
help(len)
```

Salida esperada:

```text
Help on built-in function len in module builtins:

len(obj, /)
    Return the number of items in a container.
```

Observa que incluso las funciones integradas de Python tienen docstrings. Escribir buenos docstrings es seguir la misma tradición.

### 8.5. Errores comunes

**Error 1 — Docstrings que repiten el nombre.**

```python
def suma(a, b):
    """Suma."""   # ❌ no aporta nada
```

*Solución:* explica **qué**, **para qué** y **cómo se usa**. Un buen docstring responde "¿qué necesito saber para usar esto?".

**Error 2 — Usar `#` para documentar en lugar de `"""`.**

```python
def suma(a, b):
    # Suma dos números   ❌ no aparece en help()
    return a + b
```

*Solución:* usa docstrings; aparecen en `help()` y en la documentación generada.

**Error 3 — Docstrings desactualizados.**

*Síntoma:* el docstring dice que devuelve un número y ahora devuelve una lista. *Solución:* actualiza el docstring **al mismo tiempo** que cambias el código.

### 8.6. Checkpoint de comprensión

1. ¿Cuál es la diferencia entre un comentario `#` y un docstring?
2. ¿Dónde debe ir el docstring de una función?
3. ¿Cómo puedes ver el docstring de una función en tiempo de ejecución?

### 8.7. Ejercicio propuesto

**Ejercicio 8.1.** Añade docstrings estilo Google a todas las funciones del ejercicio 6.1 y compruébalos con `help()`.

**Pista:** importa tu módulo y llama a `help(modulo.funcion)`.

---

## 9. Ejemplo integrador

Ahora reuniremos **todo lo aprendido en este archivo** en un pequeño proyecto: un analizador de texto que cuenta la frecuencia de palabras.

**Conceptos que integra:**
- Estructura de proyecto y paquete (sección 6).
- PEP 8: `snake_case`, 4 espacios, anotaciones de tipo (sección 7).
- Docstrings (sección 8).
- Uso de la biblioteca estándar (`collections.Counter`).
- El bloque `if __name__ == "__main__":`.

**Archivo `src/analizador/__init__.py`:**

```python
"""Paquete analizador: utilidades para analizar texto."""

__version__ = "0.1.0"
```

**Archivo `src/analizador/main.py`:**

```python
"""Programa de análisis de texto."""

from collections import Counter


def contar_palabras(texto: str) -> dict[str, int]:
    """Cuenta la frecuencia de cada palabra en un texto.

    Args:
        texto: El texto a analizar.

    Returns:
        Un diccionario palabra -> número de apariciones.
    """
    palabras = texto.lower().split()
    return dict(Counter(palabras))


def palabra_mas_comun(frecuencias: dict[str, int]) -> str:
    """Devuelve la palabra más frecuente.

    Args:
        frecuencias: Diccionario de frecuencias.

    Returns:
        La palabra más repetida.
    """
    return max(frecuencias, key=frecuencias.get)


def main() -> None:
    """Punto de entrada del programa."""
    texto = "python es genial python es claro python es simple"
    frecuencias = contar_palabras(texto)
    print(frecuencias)
    print(f"Palabra más común: {palabra_mas_comun(frecuencias)}")


if __name__ == "__main__":
    main()
```

Ejecución:

```bash
python -m analizador.main
```

Salida esperada:

```text
{'python': 3, 'es': 3, 'genial': 1, 'claro': 1, 'simple': 1}
Palabra más común: python
```

**Explicación línea por línea:**
- `from collections import Counter` importa `Counter`, una herramienta que cuenta elementos. Es parte de la biblioteca estándar.
- `texto.lower().split()` convierte el texto a minúsculas y lo divide en palabras. Los métodos de cadena se encadenan con puntos.
- `Counter(palabras)` crea un contador; `dict(...)` lo convierte a un diccionario normal.
- `max(frecuencias, key=frecuencias.get)` encuentra la clave con el valor más alto: recorre las palabras y compara por su frecuencia.
- La f-string final inserta el resultado de la función.

Este pequeño proyecto ya tiene estructura profesional: un paquete, docstrings, tipos, estilo PEP 8 y un punto de entrada limpio.

---

## 10. Buenas prácticas

1. **Un entorno virtual por proyecto.** Siempre, sin excepciones.
2. **Congela dependencias** con `requirements.txt` o `pyproject.toml`.
3. **Nombra con `snake_case`** funciones y variables; `PascalCase` para clases.
4. **Documenta con docstrings**, no solo con comentarios.
5. **Formatea automáticamente** con `ruff format` o `black`.
6. **No versiones `.venv/` ni `__pycache__/`.**
7. **Escribe código explícito:** legibilidad antes que astucia.
8. **Usa `python -m pip`** para evitar ambigüedades.
9. **Prueba ideas en el REPL** antes de escribir el programa completo.
10. **Usa la biblioteca estándar** antes de instalar una librería externa.

---

## 11. Recursos recomendados

- **Documentación oficial de Python:** <https://docs.python.org/3/>
- **PEP 8 (guía de estilo):** <https://peps.python.org/pep-0008/>
- **PEP 20 (Zen de Python):** <https://peps.python.org/pep-0020/>
- **PyPI (paquetes):** <https://pypi.org/>
- **Libro:** *Automate the Boring Stuff with Python*, Al Sweigart (gratis en línea).
- **Libro:** *Python Crash Course*, Eric Matthes.
- **Curso:** *Python for Everybody* (Charles Severance).
- **Tutorial oficial:** <https://docs.python.org/3/tutorial/>

---

## 12. Mini resumen final

- Python es un lenguaje **interpretado, de alto nivel y de propósito general**, creado por Guido van Rossum (1991).
- Su filosofía (el **Zen de Python**) prioriza la **legibilidad**, la **simplicidad** y la **explicitud**.
- **CPython** es la implementación de referencia; las demás (PyPy, Jython…) son alternativas.
- Los **entornos virtuales** (`venv`) aíslan las dependencias de cada proyecto.
- **pip** instala y gestiona paquetes desde PyPI; se congelan con `requirements.txt`.
- Un proyecto ordenado separa **código, tests y docs**; `__init__.py` marca paquetes.
- **PEP 8** define el estilo; herramientas como `ruff` lo aplican por ti.
- Los **docstrings** documentan módulos, clases y funciones y son accesibles vía `help()`.

---

### 🎯 Retos opcionales

1. **Reto 1:** Investiga la diferencia entre `pyproject.toml` y `requirements.txt` y escribe un `pyproject.toml` mínimo para el proyecto analizador.
2. **Reto 2:** Configura VS Code para que ejecute `ruff` al guardar cada archivo.
3. **Reto 3:** Publica un paquete de prueba en TestPyPI siguiendo la guía oficial (<https://packaging.python.org/>).

**Siguiente archivo:** [02_sintaxis_tipos_datos.md](02_sintaxis_tipos_datos.md) — sintaxis, tipos de datos y estructuras fundamentales.
