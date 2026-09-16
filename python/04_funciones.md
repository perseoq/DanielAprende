# 04 · Funciones y Programación Funcional

**Versión recomendada:** Python 3.12
**Requisito:** [03_control_flujo.md](03_control_flujo.md)
**Nivel:** Principiante → Intermedio
**Duración estimada de estudio:** 6–8 horas

---

## Índice de contenidos

1. [Definición de funciones](#1-definición-de-funciones)
2. [Argumentos posicionales, por nombre y por defecto](#2-argumentos-posicionales-por-nombre-y-por-defecto)
3. [*args y **kwargs](#3-args-y-kwargs)
4. [Funciones lambda](#4-funciones-lambda)
5. [map, filter, reduce, zip](#5-map-filter-reduce-zip)
6. [Decoradores](#6-decoradores)
7. [Closures y scope (LEGB)](#7-closures-y-scope-legb)
8. [functools e itertools](#8-functools-e-itertools)
9. [Ejemplo integrador](#9-ejemplo-integrador)
10. [Buenas prácticas](#10-buenas-prácticas)
11. [Recursos](#11-recursos)
12. [Mini resumen final](#12-mini-resumen-final)

---

## 1. Definición de funciones

### 1.1. ¿Por qué existen las funciones?

Imagina que en tu programa necesitas calcular el área de un rectángulo en veinte sitios distintos. Sin funciones, copiarías la fórmula veinte veces. Si mañana cambia, tendrías que corregirla en veinte lugares (y seguro olvidarías alguno). Las **funciones** resuelven esto: escribes la lógica **una vez**, le das un nombre y la **reutilizas** cuantas veces quieras.

**Analogía:** una función es como una **receta** con nombre. En lugar de explicar paso a paso cómo hacer un bizcocho cada vez, dices "haz el bizcocho" y todos saben qué hacer. La receta está escrita una vez, en un solo lugar.

Las funciones aportan:

- **Reutilización:** escribe una vez, usa muchas.
- **Legibilidad:** un nombre descriptivo explica qué hace.
- **Mantenimiento:** corriges un solo sitio.
- **Abstracción:** ocultas los detalles y te quedas con el concepto.
- **Testabilidad:** puedes probar cada función por separado.

### 1.2. La sintaxis básica

```python
def nombre_funcion(parametros):
    """Docstring: qué hace."""
    # cuerpo
    return resultado
```

Vamos con un ejemplo completo:

```python
def area_rectangulo(base: float, altura: float) -> float:
    """Devuelve el área de un rectángulo."""
    return base * altura


resultado = area_rectangulo(4, 5)
print(resultado)
```

Salida esperada:

```text
20
```

**Explicación línea por línea:**
- `def` es la palabra clave que **define** una función.
- `area_rectangulo` es el **nombre**. Usa verbos o descripciones claras (`calcular_total`, `enviar_email`).
- `(base: float, altura: float)` son los **parámetros** con anotaciones de tipo (opcionales pero recomendadas).
- `-> float` indica el **tipo de retorno**: la función devuelve un número decimal.
- La línea con triples comillas es el **docstring** (archivo 01, sección 8).
- `return base * altura` **devuelve** el resultado y **termina** la función.
- `area_rectangulo(4, 5)` **llama** a la función con los argumentos 4 y 5; el resultado se guarda en `resultado`.

**Vocabulario clave:**
- **Parámetro:** la variable en la definición (`base`, `altura`).
- **Argumento:** el valor concreto que pasas en la llamada (`4`, `5`).

### 1.3. Funciones que no devuelven nada

Si una función no tiene `return`, devuelve `None` automáticamente:

```python
def saludar(nombre: str) -> None:
    """Imprime un saludo. No devuelve valor."""
    print(f"Hola, {nombre}")


x = saludar("Ana")
print(x)
```

Salida esperada:

```text
Hola, Ana
None
```

**Explicación línea por línea:**
- `-> None` documenta que no devuelve nada.
- La función solo imprime.
- `x = saludar("Ana")` guarda el resultado de la llamada... que es `None`, porque no hubo `return`.

**Distinción importante:** **imprimir** no es **devolver**. `print` muestra algo en pantalla; `return` entrega un valor al código que llamó a la función. Una función que imprime es difícil de reutilizar; una que devuelve, fácil.

### 1.4. Múltiples valores de retorno

Puedes devolver varios valores separándolos por comas. En realidad, Python empaqueta una **tupla**:

```python
def min_max(valores: list[int]) -> tuple[int, int]:
    """Devuelve el mínimo y el máximo de una lista."""
    return min(valores), max(valores)


minimo, maximo = min_max([3, 1, 4, 1, 5])
print(minimo, maximo)
```

Salida esperada:

```text
1 5
```

**Explicación línea por línea:**
- `return min(valores), max(valores)` devuelve una tupla `(1, 5)`.
- `minimo, maximo = ...` **desempaqueta** esa tupla en dos variables.

### 1.5. La función debe definirse antes de usarse

Python lee el archivo de arriba abajo. Debes **definir** una función antes de **llamarla**:

```python
# print(area(3))    # ❌ NameError: name 'area' is not defined

def area(lado):
    return lado ** 2

print(area(3))       # ✅
```

Salida esperada:

```text
9
```

*Solución:* coloca las definiciones al principio del archivo o antes de usarlas.

### 1.6. Ejemplos progresivos

**Ejemplo 1 — Función de conversión.**

```python
def celsius_a_fahrenheit(celsius: float) -> float:
    """Convierte grados Celsius a Fahrenheit."""
    return celsius * 9 / 5 + 32


print(celsius_a_fahrenheit(0))
print(celsius_a_fahrenheit(100))
```

Salida esperada:

```text
32.0
212.0
```

**Ejemplo 2 — Función que valida y devuelve un booleano.**

```python
def es_par(numero: int) -> bool:
    """Indica si un número es par."""
    return numero % 2 == 0


print(es_par(4), es_par(7))
```

Salida esperada:

```text
True False
```

**Ejemplo 3 — Función que reutiliza otra.** Las funciones se componen:

```python
def es_par(numero: int) -> bool:
    return numero % 2 == 0


def contar_pares(numeros: list[int]) -> int:
    """Cuenta cuántos números pares hay en una lista."""
    contador = 0
    for n in numeros:
        if es_par(n):
            contador += 1
    return contador


print(contar_pares([1, 2, 3, 4, 5, 6]))
```

Salida esperada:

```text
3
```

**Explicación línea por línea:**
- `contar_pares` reutiliza `es_par` en lugar de repetir la lógica `% 2 == 0`. Esta composición es la esencia de la modularidad.

### 1.7. Errores comunes

**Error 1 — Llamar a una función antes de definirla.**

```text
NameError: name 'area' is not defined
```

*Solución:* define antes de llamar.

**Error 2 — Confundir `print` con `return`.**

```python
def suma(a, b):
    print(a + b)   # imprime, pero NO devuelve nada

resultado = suma(2, 3)
print(resultado)
```

Salida esperada:

```text
5
None
```

*¿Por qué?* La función imprime 5 pero devuelve `None`. *Solución:* usa `return a + b` si quieres el valor.

**Error 3 — Olvidar el `return`.**

Si una función que debería devolver un valor no lo hace, devuelve `None` silenciosamente, y el error aparece más tarde, lejos de su causa.

**Error 4 — Efectos secundarios inesperados.**

Una función que modifica variables globales o archivos "por sorpresa" es difícil de razonar. Prefiere funciones **puras**: que reciben datos y devuelven un resultado, sin efectos colaterales.

### 1.8. Checkpoint de comprensión

1. ¿Cuál es la diferencia entre `print` y `return`?
2. ¿Por qué es mejor escribir una función que copiar el código veinte veces?
3. ¿Qué devuelve una función sin `return`?

### 1.9. Ejercicio propuesto

**Ejercicio 1.1.** Escribe una función `es_primo(n)` que devuelva `True`/`False`.

**Pista:** prueba divisores hasta `int(n ** 0.5) + 1`; recuerda que 0 y 1 no son primos.

---

## 2. Argumentos posicionales, por nombre y por defecto

### 2.1. Tres formas de pasar argumentos

Python es muy flexible con los argumentos. Puedes pasarlos de tres maneras:

1. **Posicionales:** en orden. `f(1, 2)`.
2. **Por nombre (keyword):** indicando el nombre. `f(a=1, b=2)`.
3. **Por defecto:** si no los pasas, se usa un valor predefinido.

### 2.2. Argumentos posicionales

```python
def describir(nombre, edad, ciudad):
    return f"{nombre}, {edad} años, vive en {ciudad}"


print(describir("Ana", 30, "Madrid"))
```

Salida esperada:

```text
Ana, 30 años, vive en Madrid
```

Los argumentos se asignan **por posición**: `"Ana"` → `nombre`, `30` → `edad`, `"Madrid"` → `ciudad`.

### 2.3. Argumentos por nombre

Puedes indicar el nombre del parámetro para no depender del orden:

```python
print(describir(ciudad="Madrid", edad=30, nombre="Ana"))
```

Salida esperada:

```text
Ana, 30 años, vive en Madrid
```

**Ventaja:** el código es más legible y no importa el orden. Muy útil cuando hay muchos parámetros.

### 2.4. Argumentos con valor por defecto

Puedes dar un valor predefinido a un parámetro. Si no se pasa, se usa ese valor:

```python
def crear_usuario(nombre, edad, ciudad="Desconocida", activo=True):
    return {"nombre": nombre, "edad": edad, "ciudad": ciudad, "activo": activo}


print(crear_usuario("Ana", 30))
print(crear_usuario("Luis", 25, ciudad="Madrid"))
print(crear_usuario(edad=40, nombre="Eva"))
```

Salida esperada:

```text
{'nombre': 'Ana', 'edad': 30, 'ciudad': 'Desconocida', 'activo': True}
{'nombre': 'Luis', 'edad': 25, 'ciudad': 'Madrid', 'activo': True}
{'nombre': 'Eva', 'edad': 40, 'ciudad': 'Desconocida', 'activo': True}
```

**Explicación línea por línea:**
- Los parámetros con valor por defecto (`ciudad`, `activo`) pueden omitirse.
- En la tercera llamada, `nombre` y `edad` se pasan por nombre y en orden distinto.

**Regla de orden:** los parámetros **con** valor por defecto deben ir **después** de los que no lo tienen:

```python
# def mal(a=1, b):   # ❌ SyntaxError: non-default argument follows default argument
def bien(a, b=1):
    return a + b
```

### 2.5. El peligro de los valores por defecto mutables

Este es uno de los errores más sutiles y famosos de Python. **Nunca uses listas o diccionarios como valor por defecto.**

```python
def agregar(item, lista=[]):   # ¡PELIGROSO!
    lista.append(item)
    return lista


print(agregar(1))
print(agregar(2))
print(agregar(3))
```

Salida esperada:

```text
[1]
[1, 2]
[1, 2, 3]
```

**¿Por qué?** El valor por defecto `[]` se evalúa **una sola vez**, cuando se define la función, no en cada llamada. Así que todas las llamadas comparten **la misma lista**. Este comportamiento sorprende a todo el mundo la primera vez.

**Solución:** usa `None` como valor por defecto y crea la lista dentro:

```python
def agregar(item, lista=None):
    if lista is None:
        lista = []
    lista.append(item)
    return lista


print(agregar(1))
print(agregar(2))
```

Salida esperada:

```text
[1]
[2]
```

**Regla de oro:** si necesitas un valor por defecto mutable, usa `None` y créalo dentro de la función.

### 2.6. Argumentos solo por nombre

Puedes **obligar** a que ciertos argumentos se pasen por nombre usando un `*`:

```python
def dividir(a, b, *, redondear=False):
    resultado = a / b
    return round(resultado) if redondear else resultado


print(dividir(10, 3))
print(dividir(10, 3, redondear=True))
# dividir(10, 3, True)   # ❌ TypeError: takes 2 positional arguments but 3 were given
```

Salida esperada:

```text
3.3333333333333335
3
```

**Explicación línea por línea:**
- El `*` marca que todo lo que venga **después** solo puede pasarse por nombre.
- Esto evita errores de "argumento misterioso": quien lea `dividir(10, 3, True)` no sabría qué significa ese `True`.

### 2.7. Errores comunes

**Error 1 — Valor por defecto mutable.**

Como viste, es la trampa clásica. *Solución:* usa `None` y crea el objeto dentro.

**Error 2 — Poner un parámetro sin defecto después de uno con defecto.**

```text
SyntaxError: non-default argument follows default argument
```

*Solución:* reordena los parámetros.

**Error 3 — Pasar demasiados o demasiados pocos argumentos.**

```text
TypeError: describir() missing 1 required positional argument: 'ciudad'
```

*Solución:* revisa la firma de la función.

### 2.8. Checkpoint de comprensión

1. ¿Cuándo usarías argumentos por nombre en lugar de posicionales?
2. ¿Por qué `lista=[]` como valor por defecto es peligroso?
3. ¿Para qué sirve el `*` en la firma de una función?

### 2.9. Ejercicio propuesto

**Ejercicio 2.1.** Escribe `formatear_precio(valor, moneda="€", decimales=2)` que devuelva una cadena formateada.

**Pista:** usa f-strings con `:.{decimales}f`.

**Ejercicio 2.2.** Corrige esta función para que no comparta estado entre llamadas:

```python
def registrar(nombre, historial=[]):
    historial.append(nombre)
    return historial
```

**Pista:** usa `historial=None`.

---

## 3. *args y **kwargs

### 3.1. Funciones de aridad variable

A veces no sabes cuántos argumentos recibirá una función. Por ejemplo, una función que suma "todos los números que le pases". Para eso existen `*args` y `**kwargs`.

- `*args` recoge los argumentos **posicionales** variables en una **tupla**.
- `**kwargs` recoge los argumentos **por nombre** variables en un **diccionario**.

### 3.2. *args

```python
def sumar_todo(*numeros):
    """Suma cualquier cantidad de números."""
    return sum(numeros)


print(sumar_todo(1, 2, 3))
print(sumar_todo(10, 20))
print(sumar_todo())
```

Salida esperada:

```text
6
30
0
```

**Explicación línea por línea:**
- `*numeros` recoge todos los argumentos posicionales en una **tupla**.
- Con tres argumentos, `numeros` es `(1, 2, 3)`.
- Con ninguno, es `()` y `sum(())` es 0.

**Nota:** el nombre `args` es una convención; podrías llamarlo `*valores`. Lo importante es el asterisco.

### 3.3. **kwargs

```python
def configurar(**opciones):
    """Muestra las opciones de configuración."""
    for clave, valor in opciones.items():
        print(f"{clave} = {valor}")


configurar(color="azul", tamano=42)
```

Salida esperada:

```text
color = azul
tamano = 42
```

**Explicación línea por línea:**
- `**opciones` recoge los argumentos por nombre en un **diccionario**: `{"color": "azul", "tamano": 42}`.
- `.items()` permite recorrer los pares clave-valor.

### 3.4. Combinar todo

Puedes combinar parámetros normales, `*args` y `**kwargs`. El orden importa:

```python
def pedido(producto, *extras, descuento=0, **datos):
    print("Producto:", producto)
    print("Extras:", extras)
    print("Descuento:", descuento)
    print("Datos:", datos)


pedido("pizza", "queso", "aceitunas", descuento=10, direccion="Calle 1")
```

Salida esperada:

```text
Producto: pizza
Extras: ('queso', 'aceitunas')
Descuento: 10
Datos: {'direccion': 'Calle 1'}
```

**Explicación línea por línea:**
- `producto` recibe `"pizza"`.
- `*extras` recoge los posicionales restantes: `("queso", "aceitunas")`.
- `descuento=0` es un parámetro con valor por defecto.
- `**datos` recoge el resto por nombre: `{"direccion": "Calle 1"}`.

**Orden obligatorio:** `parametros_normales, *args, parametros_por_defecto, **kwargs`.

### 3.5. Desempaquetar al llamar

El mismo `*` y `**` sirven para **desempaquetar** una colección al llamar a una función:

```python
def suma(a, b, c):
    return a + b + c


valores = [1, 2, 3]
opciones = {"a": 1, "b": 2, "c": 3}

print(suma(*valores))
print(suma(**opciones))
```

Salida esperada:

```text
6
6
```

**Explicación línea por línea:**
- `suma(*valores)` "desempaqueta" la lista en tres argumentos: `suma(1, 2, 3)`.
- `suma(**opciones)` desempaqueta el diccionario en argumentos por nombre.

### 3.6. Errores comunes

**Error 1 — Confundir `args` con `*args`.**

Sin el asterisco, `args` es un parámetro normal. El asterisco es lo que captura los múltiples valores.

**Error 2 — Poner parámetros después de `**kwargs`.**

`**kwargs` debe ir **al final** de la firma.

**Error 3 — Abusar de `**kwargs`.**

Si conoces las claves de antemano, declara parámetros explícitos. `**kwargs` oculta la interfaz y dificulta el autocompletado.

### 3.7. Checkpoint de comprensión

1. ¿En qué estructura recoge `*args` los argumentos? ¿Y `**kwargs`?
2. ¿Cómo pasarías los elementos de una lista como argumentos separados?
3. ¿Por qué no conviene abusar de `**kwargs`?

### 3.8. Ejercicio propuesto

**Ejercicio 3.1.** Escribe `estadisticas(*numeros)` que devuelva un diccionario con `min`, `max`, `suma` y `media`.

**Pista:** valida que `numeros` no esté vacío para evitar divisiones entre cero.

---

## 4. Funciones lambda

### 4.1. ¿Qué es una función anónima?

Una **lambda** es una función **sin nombre**, definida en una sola línea, que consta de **una sola expresión**. Se usa cuando necesitas una función pequeña y desechable, normalmente como argumento de otra función.

**Analogía:** una lambda es como una nota adhesiva con una instrucción breve ("multiplica por 2"), frente a una función normal que sería un documento formal con nombre y firma.

### 4.2. Sintaxis y ejemplos

```python
lambda parametros: expresion
```

```python
cuadrado = lambda x: x ** 2
print(cuadrado(5))

suma = lambda a, b: a + b
print(suma(3, 4))
```

Salida esperada:

```text
25
7
```

**Explicación línea por línea:**
- `lambda x: x ** 2` define una función que recibe `x` y devuelve `x ** 2`.
- No lleva `return`: la expresión **es** el valor de retorno.
- `cuadrado(5)` la llama como cualquier función.

**Nota:** asignar una lambda a una variable (`cuadrado = lambda ...`) es una mala práctica. Si le pones nombre, usa `def`.

### 4.3. El uso idiomático: `key` en `sorted`

El verdadero uso de las lambdas es pasarlas como argumento, especialmente como criterio de ordenación:

```python
personas = [
    {"nombre": "Ana", "edad": 30},
    {"nombre": "Luis", "edad": 25},
    {"nombre": "Eva", "edad": 35},
]

ordenadas = sorted(personas, key=lambda p: p["edad"])
print([p["nombre"] for p in ordenadas])
```

Salida esperada:

```text
['Luis', 'Ana', 'Eva']
```

**Explicación línea por línea:**
- `key=lambda p: p["edad"]` indica que, para ordenar, se use la edad de cada persona.
- `sorted` aplica esa función a cada elemento y ordena por el resultado.
- `reverse=True` invertiría el orden.

### 4.4. Otras funciones que aceptan lambdas

```python
numeros = [1, 2, 3, 4]

# max con criterio
print(max(numeros, key=lambda n: -n))

# ordenar por longitud de texto
palabras = ["python", "es", "genial"]
print(sorted(palabras, key=lambda p: len(p)))
```

Salida esperada:

```text
1
['es', 'genial', 'python']
```

### 4.5. Cuándo NO usar lambda

```python
# Difícil de leer
procesar = lambda x: x.strip().lower().replace(" ", "_")

# Mucho mejor
def normalizar(x: str) -> str:
    """Normaliza un texto para usarlo como identificador."""
    return x.strip().lower().replace(" ", "_")
```

**Regla:** si la lambda no cabe cómodamente en una línea o tiene lógica compleja, usa `def`. Además, `def` permite docstrings y nombres útiles en los errores.

### 4.6. Errores comunes

**Error 1 — Intentar usar sentencias en una lambda.**

```python
lambda x: return x   # ❌ SyntaxError
```

Una lambda solo admite **expresiones**, no sentencias (`return`, bucles, asignaciones).

**Error 2 — Asignar una lambda a una variable.**

*Solución:* usa `def`. La lambda es para pasarla directamente como argumento.

**Error 3 — Lambdas con efectos secundarios.**

Las lambdas deben ser **puras**: calcular un valor a partir de la entrada. No imprimas ni modifiques cosas dentro de una lambda.

### 4.7. Checkpoint de comprensión

1. ¿Qué diferencia principal hay entre una lambda y una función `def`?
2. ¿Cuál es el uso idiomático de las lambdas?
3. ¿Por qué no conviene asignar una lambda a una variable?

### 4.8. Ejercicio propuesto

**Ejercicio 4.1.** Ordena una lista de tuplas `(nombre, nota)` por nota descendente usando `sorted` y una lambda.

**Pista:** usa `key=lambda t: -t[1]` o `reverse=True`.

---

## 5. map, filter, reduce, zip

### 5.1. La programación funcional

La **programación funcional** trata las funciones como valores y evita modificar el estado. Python no es un lenguaje puramente funcional, pero incorpora varias herramientas de este estilo. Las más importantes son `map`, `filter`, `reduce` y `zip`.

### 5.2. `map`: transformar cada elemento

`map` aplica una función a **cada** elemento de un iterable:

```python
numeros = [1, 2, 3, 4]

dobles = list(map(lambda n: n * 2, numeros))
print(dobles)

# Equivalente con comprensión (más pythónico)
dobles2 = [n * 2 for n in numeros]
print(dobles2)
```

Salida esperada:

```text
[2, 4, 6, 8]
[2, 4, 6, 8]
```

**Explicación línea por línea:**
- `map(funcion, iterable)` aplica `funcion` a cada elemento.
- Devuelve un **iterador**, así que lo envolvemos en `list()` para verlo.
- La comprensión de listas hace lo mismo y suele ser más legible.

### 5.3. `filter`: seleccionar elementos

`filter` conserva solo los elementos que cumplen una condición:

```python
numeros = [1, 2, 3, 4, 5, 6]

pares = list(filter(lambda n: n % 2 == 0, numeros))
print(pares)

# Equivalente con comprensión
pares2 = [n for n in numeros if n % 2 == 0]
print(pares2)
```

Salida esperada:

```text
[2, 4, 6]
[2, 4, 6]
```

### 5.4. `reduce`: acumular un resultado

`reduce` "reduce" una colección a un solo valor, aplicando una operación acumulativa. Está en `functools`:

```python
from functools import reduce

producto = reduce(lambda a, b: a * b, [1, 2, 3, 4])
print(producto)
```

Salida esperada:

```text
24
```

**Explicación línea por línea:**
- `reduce` aplica la función a los dos primeros elementos: `1 * 2 = 2`.
- Luego al resultado con el siguiente: `2 * 3 = 6`.
- Luego `6 * 4 = 24`.
- El resultado final es 24.

### 5.5. `zip`: combinar secuencias

```python
nombres = ["Ana", "Luis", "Eva"]
notas = [9, 7, 8]

for nombre, nota in zip(nombres, notas):
    print(f"{nombre}: {nota}")

print(list(zip(nombres, notas)))
```

Salida esperada:

```text
Ana: 9
Luis: 7
Eva: 8
[('Ana', 9), ('Luis', 7), ('Eva', 8)]
```

### 5.6. ¿map/filter o comprensiones?

En Python moderno, las **comprensiones suelen ser más legibles**:

| Con map/filter | Con comprensión |
|---|---|
| `list(map(f, xs))` | `[f(x) for x in xs]` |
| `list(filter(g, xs))` | `[x for x in xs if g(x)]` |

Usa `map`/`filter` si ya tienes una función con nombre (`map(str.strip, lineas)`) o si vienes de un estilo funcional. En general, prefiere comprensiones.

### 5.7. Errores comunes

**Error 1 — Olvidar que devuelven iteradores.**

```python
resultado = map(lambda n: n * 2, [1, 2, 3])
print(resultado)   # <map object at 0x...>
```

*Solución:* envuélvelo en `list()` si necesitas verlo o reutilizarlo.

**Error 2 — Usar `map` cuando una comprensión es más clara.**

**Error 3 — Olvidar importar `reduce`.**

`reduce` está en `functools`, no es una función integrada.

### 5.8. Checkpoint de comprensión

1. ¿Qué hace `map`? ¿Y `filter`?
2. ¿Por qué `reduce` está en `functools` y no es una función integrada?
3. Reescribe `list(filter(lambda x: x > 0, nums))` como comprensión.

### 5.9. Ejercicio propuesto

**Ejercicio 5.1.** Usando `map` y `filter`, obtén los cuadrados de los números impares de una lista.

**Pista:** encadena `filter` y luego `map`, o al revés.

---

## 6. Decoradores

### 6.1. ¿Qué es un decorador?

Un **decorador** es una función que **envuelve** a otra función para **añadirle comportamiento**, sin modificar su código. Se aplica con el símbolo `@` delante del nombre.

**Analogía:** un decorador es como **envolver un regalo**. El regalo (la función) sigue siendo el mismo, pero el envoltorio (el decorador) añade algo por encima: un lazo, una tarjeta, una capa de papel. Puedes cambiar el envoltorio sin tocar el regalo.

### 6.2. Un decorador básico

```python
def mayusculas(func):
    """Convierte a mayúsculas el resultado de func."""

    def envoltura(*args, **kwargs):
        resultado = func(*args, **kwargs)
        return resultado.upper()

    return envoltura


@mayusculas
def saludo(nombre: str) -> str:
    return f"hola, {nombre}"


print(saludo("ana"))
```

Salida esperada:

```text
HOLA, ANA
```

**Explicación línea por línea:**
- `mayusculas` recibe una función `func` como parámetro.
- `envoltura` es la función que realmente se ejecutará; dentro, llama a `func` y transforma el resultado.
- `return envoltura` devuelve la función envoltorio.
- `@mayusculas` es **azúcar sintáctico**: equivale a `saludo = mayusculas(saludo)`.
- Al llamar `saludo("ana")`, en realidad se ejecuta `envoltura("ana")`, que llama a la original y pone el resultado en mayúsculas.

### 6.3. Decoradores con parámetros

Para que un decorador reciba sus propios argumentos, se necesitan **tres** niveles de funciones:

```python
from functools import wraps


def repetir(veces):
    """Repite la ejecución de la función varias veces."""

    def decorador(func):
        @wraps(func)
        def envoltura(*args, **kwargs):
            for _ in range(veces):
                resultado = func(*args, **kwargs)
            return resultado

        return envoltura

    return decorador


@repetir(3)
def saludar(nombre: str) -> None:
    print(f"Hola, {nombre}")


saludar("Ana")
```

Salida esperada:

```text
Hola, Ana
Hola, Ana
Hola, Ana
```

**Explicación línea por línea:**
- `repetir(3)` devuelve el decorador `decorador`.
- `@repetir(3)` aplica ese decorador a `saludar`.
- `@wraps(func)` copia el nombre y el docstring originales a `envoltura` (¡muy importante!).

### 6.4. Por qué `@wraps` es esencial

Sin `@wraps`, la función decorada pierde su identidad:

```python
def sin_wraps(func):
    def envoltura(*args, **kwargs):
        return func(*args, **kwargs)
    return envoltura


@sin_wraps
def saludar():
    """Saluda."""
    return "hola"


print(saludar.__name__)   # 'envoltura', no 'saludar'
print(saludar.__doc__)    # None, se perdió el docstring
```

Con `@wraps`, `saludar.__name__` seguiría siendo `"saludar"`. Esto importa para la depuración y para herramientas que inspeccionan funciones.

### 6.5. Un decorador útil: medir tiempo

```python
import time
from functools import wraps


def medir_tiempo(func):
    """Mide cuánto tarda una función."""

    @wraps(func)
    def envoltura(*args, **kwargs):
        inicio = time.perf_counter()
        resultado = func(*args, **kwargs)
        fin = time.perf_counter()
        print(f"{func.__name__} tardó {fin - inicio:.4f}s")
        return resultado

    return envoltura


@medir_tiempo
def operacion_lenta():
    sum(range(1_000_000))


operacion_lenta()
```

Salida esperada (el tiempo varía):

```text
operacion_lenta tardó 0.0123s
```

**Explicación línea por línea:**
- `time.perf_counter()` devuelve un contador de alta precisión.
- Se mide antes y después de llamar a `func`, y se muestra la diferencia.
- El resultado se devuelve intacto: el decorador **añade** información, no la reemplaza.

### 6.6. Errores comunes

**Error 1 — Olvidar devolver la función envoltorio.**

```python
def mal_decorador(func):
    def envoltura():
        return func()
    # ❌ falta 'return envoltura'
```

*Síntoma:* `func` pasa a valer `None`. *Solución:* `return envoltura`.

**Error 2 — Olvidar `@wraps`.**

*Síntoma:* pierdes el nombre y el docstring originales.

**Error 3 — Abusar de decoradores.**

Demasiada "magia" dificulta seguir el flujo. Úsalos para responsabilidades transversales claras (logging, medición, caché, permisos).

### 6.7. Checkpoint de comprensión

1. ¿Qué hace exactamente `@decorador` por debajo?
2. ¿Por qué un decorador con parámetros necesita tres funciones?
3. ¿Para qué sirve `@wraps`?

### 6.8. Ejercicio propuesto

**Ejercicio 6.1.** Escribe un decorador `@contar_llamadas` que imprima cuántas veces se ha llamado a la función.

**Pista:** usa un atributo de la función (`envoltura.llamadas`) o una variable en el cierre.

---

## 7. Closures y scope (LEGB)

### 7.1. ¿Dónde vive una variable?

Cuando usas un nombre en Python, el intérprete busca su valor siguiendo una regla llamada **LEGB**:

1. **L**ocal: dentro de la función actual.
2. **E**nclosing (englobante): en funciones externas que la contienen.
3. **G**lobal: a nivel de módulo.
4. **B**uilt-in: nombres integrados (`len`, `print`, `sum`...).

Se busca en ese orden y se usa el primer nombre que aparece.

### 7.2. LEGB en acción

```python
x = "global"


def externa():
    x = "enclosing"

    def interna():
        print(x)   # busca en L, luego E, luego G

    interna()


externa()
```

Salida esperada:

```text
enclosing
```

**Explicación línea por línea:**
- `interna` no tiene una `x` local, así que busca en su función englobante (`externa`), donde la encuentra: `"enclosing"`.
- Si `externa` no tuviera `x`, se usaría la global `"global"`.

### 7.3. `global` y `nonlocal`

Por defecto, asignar a un nombre dentro de una función lo crea como **local**. Para modificar una variable global o englobante, debes declararlo:

```python
contador = 0


def incrementar():
    global contador
    contador += 1


incrementar()
incrementar()
print(contador)
```

Salida esperada:

```text
2
```

Sin `global`, `contador += 1` crearía una variable local y daría `UnboundLocalError`.

`nonlocal` hace lo mismo pero para la variable de la función englobante:

```python
def contador_factory():
    cuenta = 0

    def incrementar():
        nonlocal cuenta
        cuenta += 1
        return cuenta

    return incrementar


c = contador_factory()
print(c(), c(), c())
```

Salida esperada:

```text
1 2 3
```

**Explicación línea por línea:**
- `cuenta` vive en `contador_factory`, no en `incrementar`.
- `nonlocal cuenta` permite a `incrementar` modificar la variable englobante.
- Cada llamada a `contador_factory()` crea un contador **independiente**.

### 7.4. Closures

Una **closure** (cierre) es una función que **recuerda el entorno** donde fue creada, incluso después de que ese entorno haya terminado.

```python
def multiplicador(factor):
    """Devuelve una función que multiplica por factor."""

    def multiplicar(n):
        return n * factor

    return multiplicar


por_tres = multiplicador(3)
print(por_tres(10))
```

Salida esperada:

```text
30
```

**Explicación línea por línea:**
- `multiplicador(3)` crea `multiplicar`, que "recuerda" que `factor` es 3.
- Aunque `multiplicador` ya terminó, `por_tres` sigue teniendo acceso a `factor`.
- Eso es una closure: la función captura su entorno.

### 7.5. El error clásico del "late binding" en bucles

Un error muy conocido con closures dentro de bucles:

```python
funciones = [lambda: i for i in range(3)]
print([f() for f in funciones])
```

Salida esperada:

```text
[2, 2, 2]
```

**¿Por qué no `[0, 1, 2]`?** Porque las lambdas capturan la **variable** `i`, no su valor en cada iteración. Cuando se ejecutan (después del bucle), `i` ya vale 2. *Solución:* captura el valor con un argumento por defecto: `lambda i=i: i`.

### 7.6. Errores comunes

**Error 1 — `UnboundLocalError` por asignar sin `global`/`nonlocal`.**

```python
contador = 0
def f():
    contador += 1   # ❌ UnboundLocalError
```

*Solución:* declara `global contador` o usa una estructura mutable.

**Error 2 — Usar `global` en exceso.**

Rompe la encapsulación. *Solución:* pasa parámetros y devuelve resultados; usa clases o closures cuando necesites estado.

**Error 3 — Late binding en closures dentro de bucles.** Como viste arriba.

### 7.7. Checkpoint de comprensión

1. ¿Qué significa LEGB?
2. ¿Cuándo necesitas `nonlocal` en lugar de `global`?
3. ¿Qué es una closure y para qué sirve?

### 7.8. Ejercicio propuesto

**Ejercicio 7.1.** Crea una closure `crear_contador(inicio)` que devuelva funciones que incrementan desde `inicio`.

**Pista:** usa `nonlocal`.

---

## 8. functools e itertools

### 8.1. Dos módulos imprescindibles

La biblioteca estándar trae dos módulos que amplían enormemente el arsenal funcional:

- **`functools`**: herramientas para funciones (`lru_cache`, `partial`, `reduce`, `wraps`).
- **`itertools`**: iteradores eficientes (`chain`, `combinations`, `permutations`, `groupby`, `count`).

### 8.2. `functools.lru_cache`: memoización

La **memoización** guarda los resultados de una función para no recalcularlos. Perfecta para funciones recursivas costosas:

```python
from functools import lru_cache


@lru_cache(maxsize=None)
def fibonacci(n: int) -> int:
    """Calcula el n-ésimo número de Fibonacci con memoización."""
    if n < 2:
        return n
    return fibonacci(n - 1) + fibonacci(n - 2)


print(fibonacci(50))
print(fibonacci.cache_info())
```

Salida esperada:

```text
12586269025
CacheInfo(hits=48, misses=51, maxsize=None, currsize=51)
```

**Explicación línea por línea:**
- `@lru_cache` envuelve `fibonacci` y guarda cada resultado por su argumento.
- Sin caché, `fibonacci(50)` tardaría una eternidad (explosión exponencial). Con caché, es instantáneo.
- `cache_info()` muestra cuántos aciertos (`hits`) y fallos (`misses`) hubo.

**Requisito:** los argumentos deben ser **hashables** (números, textos, tuplas). No vale pasar listas.

### 8.3. `functools.partial`: fijar argumentos

`partial` crea una nueva función con algunos argumentos ya fijados:

```python
from functools import partial


def potencia(base, exponente):
    return base ** exponente


cuadrado = partial(potencia, exponente=2)
cubo = partial(potencia, exponente=3)

print(cuadrado(5))
print(cubo(3))
```

Salida esperada:

```text
25
27
```

**Explicación línea por línea:**
- `partial(potencia, exponente=2)` crea una función que siempre eleva al cuadrado.
- Útil para adaptar funciones a interfaces que esperan menos argumentos.

### 8.4. `itertools`: combinaciones y permutaciones

```python
import itertools

print(list(itertools.chain([1, 2], [3, 4])))
print(list(itertools.combinations([1, 2, 3], 2)))
print(list(itertools.permutations([1, 2, 3], 2)))
```

Salida esperada:

```text
[1, 2, 3, 4]
[(1, 2), (1, 3), (2, 3)]
[(1, 2), (1, 3), (2, 1), (2, 3), (3, 1), (3, 2)]
```

**Explicación línea por línea:**
- `chain` concatena iterables sin crear listas intermedias.
- `combinations(xs, 2)` genera todos los subconjuntos de 2 elementos **sin importar el orden**.
- `permutations(xs, 2)` genera todas las ordenaciones posibles de 2 elementos (**el orden importa**).

### 8.5. `itertools.count` e `islice`

```python
import itertools

for n in itertools.islice(itertools.count(start=10, step=2), 5):
    print(n, end=" ")
```

Salida esperada:

```text
10 12 14 16 18 
```

**Explicación línea por línea:**
- `count(start=10, step=2)` genera 10, 12, 14, 16... **infinitamente**.
- `islice(iterable, 5)` toma solo los primeros 5, evitando el bucle infinito.

### 8.6. `itertools.groupby`

Agrupa elementos **consecutivos** que comparten una clave. Requiere ordenar antes:

```python
import itertools

palabras = sorted(["banana", "avión", "casa", "auto", "barco"])
for letra, grupo in itertools.groupby(palabras, key=lambda p: p[0]):
    print(letra, list(grupo))
```

Salida esperada:

```text
a ['auto', 'avión']
b ['banana', 'barco']
c ['casa']
```

**Explicación línea por línea:**
- `sorted` agrupa las palabras que empiezan igual, dejándolas consecutivas.
- `groupby` forma grupos de elementos consecutivos con la misma clave.

### 8.7. Errores comunes

**Error 1 — Usar `lru_cache` con argumentos no hashables.**

```python
@lru_cache
def f(lista):   # ❌ lista no es hashable
    ...
```

*Solución:* usa tuplas o argumentos simples.

**Error 2 — Confundir `combinations` con `permutations`.**

`combinations` ignora el orden; `permutations` lo tiene en cuenta.

**Error 3 — No limitar iteradores infinitos** como `count()`.

### 8.8. Checkpoint de comprensión

1. ¿Qué es la memoización y para qué sirve `lru_cache`?
2. ¿Cuándo usarías `partial`?
3. ¿Por qué `groupby` requiere ordenar antes?

### 8.9. Ejercicio propuesto

**Ejercicio 8.1.** Usa `itertools.groupby` para agrupar palabras por su primera letra.

**Pista:** ordena la lista antes de agrupar.

---

## 9. Ejemplo integrador

Construiremos un **pipeline funcional de procesamiento de texto** que combina funciones, decoradores, `functools` e `itertools`.

```python
"""Pipeline de limpieza y análisis de texto."""

import itertools
from functools import lru_cache, reduce


def log(func):
    """Decorador que registra la salida de una función."""

    def envoltura(*args, **kwargs):
        resultado = func(*args, **kwargs)
        print(f"[{func.__name__}] -> {resultado}")
        return resultado

    return envoltura


@lru_cache(maxsize=None)
def limpiar(palabra: str) -> str:
    """Normaliza una palabra."""
    return palabra.strip(".,;:!?").lower()


@log
def palabras_unicas(texto: str) -> list[str]:
    """Devuelve las palabras únicas ordenadas."""
    palabras = map(limpiar, texto.split())
    return sorted(set(palabras))


def main() -> None:
    texto = "Python es genial. Python es claro, claro y simple!"
    unicas = palabras_unicas(texto)
    total_letras = reduce(lambda acc, p: acc + len(p), unicas, 0)
    print("Palabras:", unicas)
    print("Total de letras:", total_letras)
    print("Pares:", list(itertools.combinations(unicas, 2))[:3])


if __name__ == "__main__":
    main()
```

Salida esperada:

```text
[palabras_unicas] -> ['claro', 'es', 'genial', 'python', 'simple', 'y']
Palabras: ['claro', 'es', 'genial', 'python', 'simple', 'y']
Total de letras: 31
Pares: [('claro', 'es'), ('claro', 'genial'), ('claro', 'python')]
```

**Explicación línea por línea:**
- `log` es un decorador que imprime el resultado de la función decorada.
- `limpiar` está decorada con `lru_cache`: si la misma palabra aparece varias veces, se limpia una sola vez.
- `palabras_unicas` usa `map` para limpiar, `set` para eliminar duplicados y `sorted` para ordenar.
- `reduce` suma las longitudes de todas las palabras, empezando en 0 (el tercer argumento es el valor inicial).
- `itertools.combinations` genera todas las parejas; tomamos las 3 primeras.

**Conceptos integrados:** decoradores, `lru_cache`, `map`, `set`, `reduce`, `itertools.combinations`.

---

## 10. Buenas prácticas

1. **Funciones pequeñas y con una sola responsabilidad.**
2. **Nombres descriptivos** (verbos: `calcular_total`, no `ct`).
3. **Nunca uses mutables como valor por defecto.**
4. **Usa `@wraps`** en todos los decoradores.
5. **Prefiere comprensiones** a `map`/`filter` cuando sean más legibles.
6. **Documenta con docstrings** cada función pública.
7. **Evita `global`;** pasa parámetros o usa clases/closures.
8. **Usa `lru_cache`** para funciones puras costosas.
9. **Devuelve valores, no imprimas**, salvo que sea una función de presentación.
10. **Escribe funciones puras** siempre que puedas.

---

## 11. Recursos

- **Funciones oficial:** <https://docs.python.org/3/tutorial/controlflow.html#defining-functions>
- **`functools`:** <https://docs.python.org/3/library/functools.html>
- **`itertools`:** <https://docs.python.org/3/library/itertools.html>
- **Libro:** *Fluent Python*, capítulos 7, 9 y 10.
- **PEP 318 (decoradores):** <https://peps.python.org/pep-0318/>

---

## 12. Mini resumen final

- Las funciones encapsulan lógica reutilizable y devuelven valores (o `None`).
- Los argumentos pueden ser posicionales, por nombre o con valor por defecto.
- `*args` y `**kwargs` permiten aridad variable.
- Las **lambda** son funciones anónimas de una expresión.
- `map`, `filter`, `reduce` y `zip` son herramientas funcionales.
- Los **decoradores** añaden comportamiento sin tocar el código original.
- **LEGB** rige la resolución de nombres; `global`/`nonlocal` modifican el alcance.
- Las **closures** recuerdan su entorno de creación.
- `functools` e `itertools` amplían el arsenal funcional.

---

### 🎯 Retos opcionales

1. **Reto 1:** Implementa un decorador `@retry(n)` que reintente una función que lanza excepciones.
2. **Reto 2:** Usa `functools.partial` para crear variantes de una función de formateo.
3. **Reto 3:** Escribe un `memoize` propio (sin `lru_cache`) usando una closure y un diccionario.

**Anterior:** [03_control_flujo.md](03_control_flujo.md) · **Siguiente:** [05_poo.md](05_poo.md)
