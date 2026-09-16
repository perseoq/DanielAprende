# 02 · Sintaxis y Tipos de Datos

**Versión recomendada:** Python 3.12
**Requisito:** haber leído [01_introduccion_python.md](01_introduccion_python.md)
**Nivel:** Principiante
**Duración estimada de estudio:** 6–8 horas

---

## Índice de contenidos

1. [Variables y constantes](#1-variables-y-constantes)
2. [Tipos primitivos](#2-tipos-primitivos)
3. [Operadores](#3-operadores)
4. [Estructuras de datos](#4-estructuras-de-datos)
5. [Slicing e indexación](#5-slicing-e-indexación)
6. [Comprensiones](#6-comprensiones)
7. [F-strings y formateo](#7-f-strings-y-formateo)
8. [Fechas y horas](#8-fechas-y-horas)
9. [Ejemplo integrador](#9-ejemplo-integrador)
10. [Buenas prácticas](#10-buenas-prácticas)
11. [Recursos](#11-recursos)
12. [Mini resumen final](#12-mini-resumen-final)

---

## 1. Variables y constantes

### 1.1. ¿Qué es una variable, realmente?

Una **variable** es un **nombre** que apunta a un valor guardado en la memoria del ordenador. Cuando escribes `edad = 30`, no estás "metiendo el número 30 en una caja llamada edad" exactamente; estás diciendo: *"a partir de ahora, cuando yo escriba `edad`, quiero que el ordenador use el valor 30"*.

**Analogía:** piensa en una variable como una **etiqueta adhesiva** que pegas sobre un objeto. El objeto es el valor (`30`); la etiqueta es el nombre (`edad`). Puedes pegar la etiqueta sobre otro objeto distinto cuando quieras (reasignar), y varios nombres pueden apuntar al mismo objeto.

A diferencia de lenguajes como C o Java, en Python **no declaras el tipo**: no dices "esta variable es un entero". Simplemente asignas un valor y Python deduce el tipo. Esto se llama **tipado dinámico** y es una de las señales de identidad del lenguaje.

### 1.2. Crear y usar variables

```python
edad = 30
precio = 19.99
nombre = "Ana"
activo = True

print(edad)
print(nombre)
print(edad + 5)
```

Salida esperada:

```text
30
Ana
35
```

**Explicación línea por línea:**
- `edad = 30` asigna el entero `30` al nombre `edad`. El signo `=` se lee "asigna", no "igual".
- `precio = 19.99` asigna un número con decimales (*float*).
- `nombre = "Ana"` asigna un texto (*string*), delimitado por comillas.
- `activo = True` asigna un valor lógico o booleano (`True`/`False`, siempre con mayúscula inicial).
- `print(edad + 5)` muestra el resultado de sumar el valor de `edad` (30) más 5 → `35`.

Fíjate en que no escribimos el tipo en ningún momento. Python lo averiguó solo.

### 1.3. El tipo de una variable puede cambiar

Como el tipo se deduce del valor y no de una declaración, una misma variable puede cambiar de tipo:

```python
dato = 10
print(type(dato))

dato = "diez"
print(type(dato))
```

Salida esperada:

```text
<class 'int'>
<class 'str'>
```

**Explicación línea por línea:**
- `type(dato)` devuelve el tipo del valor actual. Primero es `int` (entero).
- Tras `dato = "diez"`, el tipo pasa a ser `str` (texto).

**¿Esto es bueno o malo?** Es flexible (puedes reutilizar nombres), pero también peligroso: si esperabas un número y recibes texto, tendrás un error más adelante. Por eso conviene usar nombres descriptivos y, en proyectos grandes, **anotaciones de tipo** (sección 1.5).

### 1.4. Constantes

Una **constante** es una variable cuyo valor **no debería cambiar** durante el programa. Python **no** las protege de verdad (puedes reasignarlas), pero existe una convención universal: escribir sus nombres en **MAYÚSCULAS**.

```python
PI = 3.14159
MAX_CONEXIONES = 100
TASA_IVA = 0.21

print(PI)
print(MAX_CONEXIONES)
```

Salida esperada:

```text
3.14159
100
```

**Explicación línea por línea:**
- `PI = 3.14159` es, técnicamente, igual que cualquier otra asignación. La diferencia es **semántica**: las mayúsculas avisan a quien lee "esto no debería cambiar".
- Si alguien ve `MAX_CONEXIONES` en el código, sabe que es un límite global, no un dato temporal.

**¿Por qué no hay constantes reales?** Porque Python prioriza la flexibilidad y confía en el programador. La convención de mayúsculas es suficiente en la práctica.

### 1.5. Anotaciones de tipo

Puedes **anotar** el tipo esperado de una variable. Es opcional y no obliga nada en tiempo de ejecución, pero mejora la legibilidad y permite que herramientas como `mypy` detecten errores.

```python
nombre: str = "Ana"
edad: int = 30
altura: float = 1.75
activo: bool = True

print(nombre, edad, altura, activo)
```

Salida esperada:

```text
Ana 30 1.75 True
```

**Explicación línea por línea:**
- `nombre: str = "Ana"` anota que `nombre` debería ser un texto. La anotación va entre dos puntos, antes del `=`.
- Las anotaciones **no impiden** asignar otro tipo; son una promesa y una ayuda para las herramientas.

**¿Por qué usarlas?** En scripts pequeños casi no aportan, pero en proyectos medianos y grandes reducen muchísimo los errores. Son una práctica profesional.

### 1.6. Asignación múltiple y desempaquetado

Python permite asignar varios valores a la vez, lo que hace el código más compacto:

```python
x, y, z = 1, 2, 3
print(x, y, z)

a = b = c = 0
print(a, b, c)

primero, *resto = [10, 20, 30, 40]
print(primero)
print(resto)
```

Salida esperada:

```text
1 2 3
0 0 0
10
[20, 30, 40]
```

**Explicación línea por línea:**
- `x, y, z = 1, 2, 3` **desempaqueta** la tupla de la derecha en tres variables. El número de nombres debe coincidir con el de valores.
- `a = b = c = 0` asigna el mismo valor a las tres variables de una vez.
- `primero, *resto = [10, 20, 30, 40]` usa el asterisco `*` para capturar "el primero y todo lo demás": `primero` vale 10 y `resto` es una lista con `[20, 30, 40]`.

Este desempaquetado se usa constantemente en Python (por ejemplo, al recorrer listas de pares).

### 1.7. El caso especial de la reasignación vs. la mutación

Hay una distinción que confunde mucho al principio. **Reasignar** cambia a qué valor apunta un nombre. **Mutar** cambia el contenido de un objeto.

```python
# Reasignación: el nombre apunta a otro sitio
a = [1, 2]
b = a          # b y a apuntan a la MISMA lista
b.append(3)    # mutamos la lista (añadimos 3)
print(a)       # a también ve el cambio

c = [1, 2]
d = c
d = [9, 9]     # reasignamos d a una lista NUEVA
print(c)       # c no cambia
```

Salida esperada:

```text
[1, 2, 3]
[1, 2]
```

**Explicación línea por línea:**
- `b = a` no copia la lista: hace que `b` apunte al **mismo** objeto que `a`.
- `b.append(3)` **muta** ese objeto compartido, así que `a` también lo "ve".
- `d = [9, 9]` no muta nada: hace que `d` apunte a un objeto **nuevo**. `c` sigue apuntando al original.

Este comportamiento se entiende a fondo con el concepto de "referencias" y lo volverás a encontrar con las estructuras de datos (sección 4).

### 1.8. Errores comunes

**Error 1 — Usar una variable antes de asignarla.**

```python
print(total)
```

```text
NameError: name 'total' is not defined
```

*¿Por qué?* `total` nunca recibió un valor. *Solución:* asígnala antes de usarla.

**Error 2 — Nombres que sobrescriben funciones integradas.**

```python
list = [1, 2, 3]
print(list("abc"))   # ¡error! 'list' ya no es la función, es tu lista
```

```text
TypeError: 'list' object is not callable
```

*¿Por qué?* Al usar `list` como nombre de variable, destruiste la función `list` integrada. *Solución:* usa nombres como `mi_lista`. Evita `list`, `dict`, `str`, `id`, `type`, `sum`, `max`.

**Error 3 — Confundir `=` con `==`.**

```python
if activo = True:
    ...
```

```text
SyntaxError: invalid syntax
```

*¿Por qué?* `=` asigna; `==` compara. En una condición necesitas comparar.

### 1.9. Checkpoint de comprensión

1. ¿Por qué se dice que Python tiene "tipado dinámico"?
2. ¿Qué diferencia hay entre reasignar y mutar? Ilústralo con una lista.
3. ¿Por qué no se deben usar nombres como `list` o `sum` para variables?

### 1.10. Ejercicio propuesto

**Ejercicio 1.1.** Intercambia los valores de dos variables `a` y `b` en **una sola línea**.

**Pista:** Python permite `a, b = b, a`.

**Ejercicio 1.2.** Crea una variable con tu edad, otra con tu año de nacimiento y muestra cuántos años han pasado desde entonces (usa el año actual como constante `ANIO_ACTUAL`).

**Pista:** define `ANIO_ACTUAL = 2026` y resta.

---

## 2. Tipos primitivos

### 2.1. El mapa de los tipos básicos

Python tiene un conjunto de tipos básicos que usarás constantemente:

| Tipo | Nombre en Python | Ejemplo | ¿Para qué? |
|---|---|---|---|
| Entero | `int` | `42`, `-7` | Contar, índices, cantidades exactas |
| Decimal | `float` | `3.14`, `1e-5` | Medidas, porcentajes, dinero aproximado |
| Complejo | `complex` | `2+3j` | Cálculos matemáticos avanzados |
| Booleano | `bool` | `True`, `False` | Condiciones, banderas |
| Texto | `str` | `"hola"` | Nombres, mensajes, datos textuales |
| Nulo | `NoneType` | `None` | "Sin valor", ausencia de dato |

Vamos a estudiarlos uno por uno, entendiendo **por qué** cada uno existe.

### 2.2. Enteros (`int`): precisión sin límite

Un **entero** es un número sin parte decimal. La particularidad de Python es que los enteros tienen **precisión ilimitada**: no existe un "desbordamiento" a partir de cierto tamaño, a diferencia de otros lenguajes.

```python
pequeno = 7
negativo = -42
enorme = 10 ** 50        # 1 seguido de 50 ceros
muy_enorme = 2 ** 1000

print(pequeno)
print(enorme)
print(len(str(muy_enorme)))   # cuántos dígitos tiene
```

Salida esperada:

```text
7
100000000000000000000000000000000000000000000000000
302
```

**Explicación línea por línea:**
- `10 ** 50` es 10 elevado a 50: un 1 seguido de 50 ceros.
- `2 ** 1000` es un número con **302 dígitos**. Python lo calcula sin problema.
- `len(str(muy_enorme))` convierte el número a texto y cuenta sus caracteres: 302 dígitos.

**¿Por qué es útil?** En C o Java, un `int` de 64 bits se desborda si el número es muy grande. En Python no tienes que preocuparte. El coste es que los enteros enormes ocupan más memoria y son más lentos.

### 2.3. Decimales (`float`): la representación imperfecta

Un **float** representa números reales con coma flotante. Aquí hay una trampa fundamental que debes entender bien:

```python
print(0.1 + 0.2)
print(0.1 + 0.2 == 0.3)
```

Salida esperada:

```text
0.30000000000000004
False
```

**¿Por qué ocurre esto?** Los ordenadores guardan los decimales en **binario** (base 2), y muchos números que en decimal son simples (como 0.1 o 0.3) **no tienen representación exacta** en binario. Es como intentar escribir 1/3 en decimal: 0.3333... nunca es exacto. El ordenador guarda la aproximación más cercana, y al sumar dos aproximaciones, el error se hace visible.

**Analogía:** es como medir con una regla que solo tiene marcas cada 3 milímetros. Puedes aproximar, pero nunca medir exactamente 10 mm.

**Solución:** para comparar flotantes, usa `math.isclose()`:

```python
import math

print(math.isclose(0.1 + 0.2, 0.3))
```

Salida esperada:

```text
True
```

**Explicación línea por línea:**
- `math.isclose(a, b)` comprueba si dos números son "suficientemente cercanos", tolerando el pequeño error de representación.

**Consecuencia práctica:** **nunca compares dinero con `==`**. Para dinero, usa `decimal.Decimal` o guarda céntimos como enteros.

### 2.4. Booleanos (`bool`): la verdad binaria

Un **booleano** representa un valor de verdad: `True` o `False`. Son la base de las condiciones y los bucles.

```python
es_mayor = True
tiene_descuento = False

print(es_mayor)
print(type(es_mayor))
print(True + True)
```

Salida esperada:

```text
True
<class 'bool'>
2
```

**Explicación línea por línea:**
- `True` y `False` se escriben con mayúscula inicial (obligatorio).
- `type(es_mayor)` confirma que es `bool`.
- `True + True` da `2`: en Python, `bool` es una **subclase de `int`**, donde `True` vale 1 y `False` vale 0. Curioso, pero útil.

### 2.5. Valores "falsy" y "truthy"

Aquí hay un concepto clave: **casi cualquier valor puede evaluarse como verdadero o falso**. Los valores que se comportan como `False` se llaman **falsy**; los demás, **truthy**.

```python
print(bool(0))
print(bool(0.0))
print(bool(""))
print(bool([]))
print(bool(None))

print(bool(1))
print(bool(-1))
print(bool("hola"))
print(bool([0]))
```

Salida esperada:

```text
False
False
False
False
False
True
True
True
True
```

**Los valores falsy son:** `0`, `0.0`, `""` (texto vacío), `[]` (lista vacía), `{}` (diccionario vacío), `()` (tupla vacía), `set()` (conjunto vacío), `None`. **Todo lo demás es truthy.**

**¿Por qué importa?** Porque permite escribir condiciones muy naturales:

```python
nombre = ""
if not nombre:
    print("No has escrito tu nombre")
```

Salida esperada:

```text
No has escrito tu nombre
```

En lugar de `if nombre == "":`, escribimos `if not nombre:`, que es más legible y funciona para cualquier valor vacío.

### 2.6. Texto (`str`): cadenas de caracteres

Un **string** es una secuencia de caracteres, delimitada por comillas simples o dobles. Python las trata igual, así que elige una y sé consistente.

```python
simple = 'hola'
doble = "mundo"
multilinea = """Esta cadena
ocupa varias
líneas."""
cruda = r"C:\nuevo\archivo"     # raw: no interpreta \n

print(simple, doble)
print(multilinea)
print(cruda)
print(len(simple))
```

Salida esperada:

```text
hola mundo
Esta cadena
ocupa varias
líneas.
C:\nuevo\archivo
4
```

**Explicación línea por línea:**
- `'hola'` y `"mundo"` son equivalentes.
- Las triples comillas `"""..."""` permiten cadenas multilínea.
- La `r` delante (`r"C:\nuevo..."`) crea una **cadena cruda** (*raw*): la barra invertida no se interpreta como carácter especial. Muy útil para rutas de Windows.
- `len(simple)` devuelve la longitud: `"hola"` tiene 4 caracteres.

**Secuencias de escape más comunes:** `\n` (salto de línea), `\t` (tabulador), `\\` (barra invertida literal), `\"` (comilla doble dentro de una cadena con comillas dobles).

### 2.7. Inmutabilidad de las cadenas

Las cadenas son **inmutables**: una vez creadas, no puedes modificar sus caracteres. Solo puedes crear cadenas nuevas.

```python
s = "hola"

# s[0] = "H"    # ❌ TypeError: 'str' object does not support item assignment

s = "H" + s[1:]  # ✅ creamos una cadena NUEVA
print(s)
```

Salida esperada:

```text
Hola
```

**Explicación línea por línea:**
- `s[0] = "H"` falla porque intenta modificar un carácter de una cadena existente.
- `s[1:]` toma desde el segundo carácter hasta el final (`"ola"`).
- `"H" + "ola"` concatena y crea una cadena nueva, que reasignamos a `s`.

**¿Por qué son inmutables?** Porque permite optimizaciones y hace que las cadenas sean seguras de compartir. Lo mismo ocurrirá con las tuplas (sección 4).

### 2.8. None: la ausencia de valor

`None` representa "ningún valor" o "todavía no hay valor". Es distinto de `0`, `""` o `False`: es la **ausencia**.

```python
resultado = None
print(resultado)
print(resultado is None)

if resultado is None:
    print("Aún no hay resultado")
```

Salida esperada:

```text
None
True
Aún no hay resultado
```

**Explicación línea por línea:**
- `None` es un valor único en todo Python (un *singleton*).
- Se compara con `is`, no con `==`: `resultado is None` pregunta "¿es exactamente la ausencia de valor?".
- Es común usarlo como valor inicial de una variable que se llenará después.

### 2.9. Ejemplos progresivos

**Ejemplo 1 — Calculadora de IVA.** Combina `float`, operadores y f-strings:

```python
precio_base = 100.0
tasa_iva = 0.21
precio_final = precio_base * (1 + tasa_iva)
print(f"Precio final: {precio_final:.2f} €")
```

Salida esperada:

```text
Precio final: 121.00 €
```

**Explicación línea por línea:**
- `precio_final = precio_base * (1 + tasa_iva)` calcula 100 × 1.21 = 121.0.
- `{precio_final:.2f}` formatea el número con **2 decimales** (el `.2f`). Esto se estudia a fondo en la sección 7.

**Ejemplo 2 — Conversión de tipos.** A veces necesitas cambiar de un tipo a otro:

```python
texto_numero = "42"
numero = int(texto_numero)
print(numero + 8)

print(float("3.14"))
print(str(100) + " unidades")
print(int(3.99))   # trunca, no redondea
```

Salida esperada:

```text
50
3.14
100 unidades
3
```

**Explicación línea por línea:**
- `int("42")` convierte el texto `"42"` en el entero `42`.
- `float("3.14")` convierte a decimal.
- `str(100)` convierte el número a texto, permitiendo concatenar con `" unidades"`.
- `int(3.99)` **trunca** (corta la parte decimal), no redondea. Para redondear, usa `round()`.

**Ejemplo 3 — Manejando la entrada del usuario.** `input()` siempre devuelve texto:

```python
# Ejemplo interactivo (requiere escribir en la terminal)
edad_texto = input("¿Cuántos años tienes? ")
edad = int(edad_texto)
print(f"El año que viene tendrás {edad + 1}")
```

Ejemplo de ejecución:

```text
¿Cuántos años tienes? 30
El año que viene tendrás 31
```

**Explicación línea por línea:**
- `input(...)` muestra un mensaje y **espera** a que el usuario escriba y pulse Enter.
- **Importante:** `input()` **siempre devuelve un texto**, aunque el usuario escriba un número. Por eso hacemos `int(edad_texto)`.
- Olvidar esta conversión es uno de los errores más comunes de los principiantes.

### 2.10. Errores comunes

**Error 1 — Concatenar texto con número.**

```python
print("Edad: " + 30)
```

```text
TypeError: can only concatenate str (not "int") to str
```

*¿Por qué?* No puedes sumar un texto y un número. *Solución:* usa una f-string (`f"Edad: {30}"`) o convierte (`"Edad: " + str(30)`).

**Error 2 — Comparar flotantes con `==`.**

```python
print(0.1 + 0.2 == 0.3)
```

```text
False
```

*Solución:* usa `math.isclose()`.

**Error 3 — Olvidar que `input()` devuelve texto.**

```python
edad = input("Edad: ")
print(edad + 1)
```

```text
TypeError: can only concatenate str (not "int") to str
```

*Solución:* `edad = int(input("Edad: "))`.

**Error 4 — Confundir `None` con falsy.**

```python
if resultado == None:   # funciona, pero no es idiomático
    ...
```

*Solución:* usa `if resultado is None:`. Es más correcto y más rápido.

### 2.11. Checkpoint de comprensión

1. ¿Por qué `0.1 + 0.2` no es exactamente `0.3`?
2. Enumera cinco valores "falsy".
3. ¿Por qué `input()` obliga a convertir el resultado?
4. ¿Qué diferencia hay entre `None` y `0`?

### 2.12. Ejercicio propuesto

**Ejercicio 2.1.** Pide dos números al usuario con `input()`, súmalos y muestra el resultado con dos decimales.

**Pista:** recuerda convertir a `float` con `float(...)`.

**Ejercicio 2.2.** Dado un precio en texto (por ejemplo `"19.99"`), conviértelo a número, aplícale un descuento del 15 % y muestra el resultado formateado.

**Pista:** `float(precio) * 0.85`.

---

## 3. Operadores

### 3.1. ¿Qué es un operador?

Un **operador** es un símbolo que realiza una operación sobre uno o más valores (llamados **operandos**). En `2 + 3`, el operador es `+` y los operandos son `2` y `3`. Los operadores son el vocabulario con el que expresas cálculos y decisiones.

### 3.2. Operadores aritméticos

```python
print(7 + 3)     # suma
print(7 - 3)     # resta
print(7 * 3)     # multiplicación
print(7 / 3)     # división (siempre float)
print(7 // 3)    # división entera
print(7 % 3)     # módulo (resto)
print(2 ** 10)   # potencia
```

Salida esperada:

```text
10
4
21
2.3333333333333335
2
1
1024
```

**Explicación línea por línea:**
- `7 / 3` da `2.333...`: en Python 3, la división con `/` **siempre** devuelve un float.
- `7 // 3` da `2`: la **división entera** descarta la parte decimal.
- `7 % 3` da `1`: el **módulo** es el resto de la división entera. Es muy útil para saber si un número es par (`n % 2 == 0`).
- `2 ** 10` da `1024`: **potencia**.

**El módulo en acción:**

```python
for numero in [4, 5, 6, 7]:
    if numero % 2 == 0:
        print(f"{numero} es par")
    else:
        print(f"{numero} es impar")
```

Salida esperada:

```text
4 es par
5 es impar
6 es par
7 es impar
```

### 3.3. Operadores de comparación

Comparan dos valores y devuelven un booleano:

```python
print(3 == 3)    # igual
print(3 != 4)    # distinto
print(3 < 4)     # menor que
print(4 >= 4)    # mayor o igual
print(1 < 2 < 3) # comparación encadenada
print("a" < "b") # orden alfabético
```

Salida esperada:

```text
True
True
True
True
True
True
```

**Explicación línea por línea:**
- `==` pregunta "¿son iguales en valor?"; `!=`, "¿son distintos?".
- `<`, `>`, `<=`, `>=` comparan.
- `1 < 2 < 3` es una **comparación encadenada**: equivale a `(1 < 2) and (2 < 3)`, pero se lee mejor.
- `"a" < "b"` compara texto en orden alfabético (por el código de cada carácter).

### 3.4. Operadores lógicos

Combinan condiciones booleanas:

```python
t = True
f = False

print(t and f)
print(t or f)
print(not t)
```

Salida esperada:

```text
False
True
False
```

- `and` → verdadero solo si **ambos** son verdaderos.
- `or` → verdadero si **al menos uno** es verdadero.
- `not` → invierte el valor.

**El cortocircuito (importante):** `and` y `or` **no siempre devuelven un booleano**; devuelven uno de sus operandos, y evalúan el mínimo necesario.

```python
print(0 or "valor por defecto")
print("config" and "usar")
print("" or 0 or "tercero")
```

Salida esperada:

```text
valor por defecto
usar
tercero
```

**Explicación línea por línea:**
- `0 or "valor por defecto"`: como `0` es falsy, `or` pasa al siguiente y devuelve `"valor por defecto"`. Patrón clásico para valores por defecto.
- `"config" and "usar"`: como `"config"` es truthy, `and` devuelve el segundo operando: `"usar"`.
- `"" or 0 or "tercero"`: devuelve el primer valor truthy, `"tercero"`.

Este comportamiento se usa muchísimo en código real.

### 3.5. Operadores de asignación aumentada

Son atajos para "aplicar una operación y reasignar":

```python
x = 10
x += 5
print(x)
x -= 3
print(x)
x *= 2
print(x)
x //= 4
print(x)
```

Salida esperada:

```text
15
12
24
6
```

**Explicación línea por línea:**
- `x += 5` equivale a `x = x + 5`.
- `x -= 3`, `x *= 2`, `x //= 4` son análogos con resta, multiplicación y división entera.

### 3.6. Operadores de identidad y pertenencia

```python
a = [1, 2]
b = [1, 2]

print(a == b)     # ¿mismo valor?
print(a is b)     # ¿mismo objeto?
print(2 in a)     # ¿está dentro?
```

Salida esperada:

```text
True
False
True
```

**Explicación línea por línea:**
- `a == b` es `True`: las dos listas **contienen lo mismo**.
- `a is b` es `False`: son **objetos distintos** en memoria, aunque iguales por dentro.
- `2 in a` es `True`: el operador `in` comprueba pertenencia.

**Regla práctica:** usa `is` solo con `None`, `True` y `False`. Para todo lo demás, usa `==`.

### 3.7. Precedencia de operadores

Cuando hay varios operadores, Python sigue un orden. Los más importantes, de mayor a menor prioridad:

```text
1. Paréntesis  ( )
2. Potencia    **
3. Unarios     +x, -x, not
4. Multiplicación/división  *, /, //, %
5. Suma/resta  +, -
6. Comparaciones  <, >, ==, !=
7. not
8. and
9. or
```

```python
print(2 + 3 * 4)      # 14, no 20
print((2 + 3) * 4)    # 20
print(not True or True)  # True (not se aplica antes que or)
```

Salida esperada:

```text
14
20
True
```

**Consejo:** ante la duda, **usa paréntesis**. Hacen el código más claro aunque no sean estrictamente necesarios.

### 3.8. Errores comunes

**Error 1 — Confundir `=` con `==`.**

*Solución:* recuerda que `=` asigna y `==` compara.

**Error 2 — Usar `and`/`or` con listas en lugar de `&`/`|`.**

```python
# Al filtrar arrays de NumPy o columnas de Pandas, usar 'and' da error.
```

*Solución:* en esos contextos se usan `&` y `|` con paréntesis (lo verás en los archivos 18 y 19).

**Error 3 — Creer que `is` compara valores.**

```python
a = 1000
b = 1000
print(a is b)   # puede ser False (objetos distintos)
```

*Solución:* usa `==` para valores; `is` solo para `None`/`True`/`False`.

**Error 4 — Olvidar la precedencia.**

```python
print(2 + 3 * 4)   # 14, no 20
```

*Solución:* usa paréntesis para dejar clara la intención.

### 3.9. Checkpoint de comprensión

1. ¿Qué diferencia hay entre `/` y `//`?
2. ¿Para qué sirve el operador `%`? Da un ejemplo real.
3. ¿Por qué `0 or "texto"` devuelve `"texto"`?
4. ¿Cuándo debes usar `is` y cuándo `==`?

### 3.10. Ejercicio propuesto

**Ejercicio 3.1.** Dado un número de segundos, calcula cuántas horas, minutos y segundos representa, usando `//` y `%`.

**Pista:** empieza por `horas = total // 3600`; luego usa el resto para los minutos.

**Ejercicio 3.2.** Pide un año al usuario y determina si es bisiesto (divisible por 4, excepto los divisibles por 100 salvo los divisibles por 400).

**Pista:** combina `%` con `and`/`or` y paréntesis.

---

## 4. Estructuras de datos

### 4.1. ¿Por qué necesitamos estructuras de datos?

Hasta ahora hemos guardado un valor por variable. Pero, ¿y si necesitas guardar las notas de 30 alumnos? Crear 30 variables sería absurdo. Las **estructuras de datos** permiten agrupar muchos valores en un solo objeto.

Python trae cuatro estructuras fundamentales, y elegir la correcta es una decisión de diseño importante:

| Estructura | Ordenada | Mutable | Duplicados | Acceso | Cuándo usarla |
|---|---|---|---|---|---|
| `list` | Sí | Sí | Sí | Por índice | Colección que cambia |
| `tuple` | Sí | No | Sí | Por índice | Datos fijos, claves |
| `dict` | Sí (3.7+) | Sí | Claves únicas | Por clave | Relación clave→valor |
| `set` | No | Sí | No | Por pertenencia | Eliminar duplicados, conjuntos |

Vamos a estudiarlas una a una.

### 4.2. Listas (`list`): la colección mutable

Una **lista** es una secuencia **ordenada** y **mutable** (puedes cambiarla) que admite duplicados. Es la estructura más usada.

**Analogía:** una lista es como una fila de casilleros numerados: puedes meter, sacar y cambiar cosas, y cada casillero tiene una posición.

```python
frutas = ["manzana", "pera", "uva"]
print(frutas)
print(len(frutas))
print(frutas[0])
print(frutas[-1])
```

Salida esperada:

```text
['manzana', 'pera', 'uva']
3
manzana
uva
```

**Explicación línea por línea:**
- `["manzana", "pera", "uva"]` crea una lista con tres textos.
- `len(frutas)` da el número de elementos: 3.
- `frutas[0]` accede al **primer** elemento. Los índices empiezan en **0**.
- `frutas[-1]` accede al **último** con índice negativo. `-2` sería el penúltimo.

**Métodos principales:**

```python
frutas = ["manzana", "pera"]
frutas.append("uva")        # añade al final
frutas.insert(1, "kiwi")    # inserta en la posición 1
print(frutas)

frutas.remove("pera")       # elimina la primera aparición
ultima = frutas.pop()       # extrae y devuelve el último
print(frutas)
print(ultima)

print(sorted(frutas))       # devuelve una lista ordenada NUEVA
print(frutas)               # la original no cambia
```

Salida esperada:

```text
['manzana', 'kiwi', 'pera', 'uva']
['manzana', 'kiwi']
uva
['kiwi', 'manzana']
['manzana', 'kiwi']
```

**Explicación línea por línea:**
- `.append(x)` añade `x` al final, **mutando** la lista.
- `.insert(i, x)` inserta `x` en la posición `i`.
- `.remove(x)` elimina la primera aparición de `x`.
- `.pop()` extrae y devuelve el **último** elemento, reduciendo la lista.
- `sorted(frutas)` devuelve una **nueva** lista ordenada; no modifica la original. En cambio, `frutas.sort()` sí la ordena en su sitio.

### 4.3. Tuplas (`tuple`): la colección inmutable

Una **tupla** es como una lista pero **inmutable**: una vez creada, no cambia. Se define con paréntesis.

**¿Para qué querer algo inmutable?** Porque garantiza que el dato no se altere por accidente, y porque permite usar la tupla como **clave de diccionario** o en conjuntos (las listas no pueden).

```python
punto = (3, 4)
print(punto[0])
print(len(punto))

x, y = punto          # desempaquetado
print(x, y)

# punto[0] = 5        # ❌ TypeError: 'tuple' object does not support item assignment
```

Salida esperada:

```text
3
2
3 4
```

**Explicación línea por línea:**
- `punto = (3, 4)` crea una tupla de dos elementos.
- `punto[0]` accede igual que en una lista.
- `x, y = punto` **desempaqueta** la tupla en dos variables.
- El intento de modificar `punto[0]` lanza `TypeError` porque las tuplas son inmutables.

**El caso de la tupla de un solo elemento:**

```python
no_es_tupla = (5)      # esto es un int
si_es_tupla = (5,)     # esto es una tupla
print(type(no_es_tupla))
print(type(si_es_tupla))
```

Salida esperada:

```text
<class 'int'>
<class 'tuple'>
```

**Explicación:** los paréntesis por sí solos no crean una tupla; lo que la crea es la **coma**. `(5)` es solo el número 5 entre paréntesis. La coma final `(5,)` es obligatoria para una tupla de un elemento. Es una trampa clásica.

### 4.4. Diccionarios (`dict`): la relación clave-valor

Un **diccionario** asocia **claves** con **valores**. Es perfecto cuando quieres buscar un dato por un nombre en lugar de por una posición.

**Analogía:** un diccionario es como una guía telefónica: buscas por el nombre (clave) y obtienes el teléfono (valor), sin importar el orden.

```python
persona = {"nombre": "Ana", "edad": 30, "ciudad": "Madrid"}
print(persona["nombre"])
print(persona.get("telefono", "no disponible"))
print(len(persona))
```

Salida esperada:

```text
Ana
no disponible
3
```

**Explicación línea por línea:**
- `{"nombre": "Ana", ...}` crea un diccionario con pares clave-valor.
- `persona["nombre"]` accede por clave.
- `.get(clave, defecto)` devuelve el valor o un valor por defecto si la clave no existe. Es más seguro que `[]`, que lanza `KeyError`.
- `len(persona)` cuenta el número de pares.

**Añadir, modificar y recorrer:**

```python
persona = {"nombre": "Ana", "edad": 30}
persona["email"] = "ana@example.com"   # añade
persona["edad"] = 31                    # modifica

for clave, valor in persona.items():
    print(f"{clave}: {valor}")

print(list(persona.keys()))
print(list(persona.values()))
```

Salida esperada:

```text
nombre: Ana
edad: 31
email: ana@example.com
['nombre', 'edad', 'email']
['Ana', 31, 'ana@example.com']
```

**Explicación línea por línea:**
- `persona["email"] = ...` crea la clave si no existe; la actualiza si ya existe.
- `.items()` devuelve pares `(clave, valor)` que se desempaquetan en el `for`.
- `.keys()` y `.values()` devuelven las claves y los valores.

**¿Por qué las claves deben ser inmutables?** Porque Python las usa para localizar el valor. Si una clave pudiera cambiar, se "perdería". Por eso las listas **no** pueden ser claves, pero las tuplas sí.

### 4.5. Conjuntos (`set`): la colección sin duplicados

Un **conjunto** es una colección **sin orden** y **sin duplicados**. Es ideal para operaciones matemáticas de conjuntos y para eliminar repeticiones.

```python
a = {1, 2, 3}
b = {3, 4, 5}

print(a | b)   # unión
print(a & b)   # intersección
print(a - b)   # diferencia
print(a ^ b)   # diferencia simétrica
```

Salida esperada:

```text
{1, 2, 3, 4, 5}
{3}
{1, 2}
{1, 2, 4, 5}
```

**Explicación línea por línea:**
- `a | b` (**unión**): todos los elementos de ambos.
- `a & b` (**intersección**): los que están en los dos.
- `a - b` (**diferencia**): los que están en `a` pero no en `b`.
- `a ^ b` (**diferencia simétrica**): los que están en uno u otro, pero no en ambos.

**Eliminar duplicados de una lista:**

```python
numeros = [1, 2, 2, 3, 3, 3]
unicos = list(set(numeros))
print(unicos)
```

Salida esperada (el orden puede variar):

```text
[1, 2, 3]
```

**Advertencia:** `set` **no conserva el orden**. Si necesitas el orden original, hay otras técnicas.

### 4.6. `frozenset`: el conjunto inmutable

`frozenset` es la versión inmutable de `set`. Se usa cuando necesitas un conjunto que sea **hashable** (por ejemplo, como clave de diccionario).

```python
fs = frozenset([1, 2, 3])
print(fs)
# fs.add(4)   # ❌ AttributeError
```

### 4.7. Estructuras anidadas

Las estructuras pueden contener otras estructuras, formando datos complejos:

```python
escuela = {
    "alumnos": [
        {"nombre": "Ana", "notas": [8, 9, 7]},
        {"nombre": "Luis", "notas": [6, 7, 8]},
    ]
}

print(escuela["alumnos"][0]["nombre"])
print(escuela["alumnos"][0]["notas"][1])
```

Salida esperada:

```text
Ana
9
```

**Explicación línea por línea:**
- `escuela["alumnos"]` accede a la lista de alumnos.
- `[0]` toma el primer alumno (un diccionario).
- `["nombre"]` obtiene el valor `"Ana"`.
- `["notas"][1]` accede a la lista de notas y toma la segunda (índice 1) → `9`.

Este tipo de estructura (listas de diccionarios) es extremadamente común, por ejemplo al leer JSON.

### 4.8. Ejemplos progresivos

**Ejemplo 1 — Contar palabras con un diccionario.** Antes de conocer `Counter`:

```python
texto = "python es genial python es claro"
frecuencias = {}

for palabra in texto.split():
    frecuencias[palabra] = frecuencias.get(palabra, 0) + 1

print(frecuencias)
```

Salida esperada:

```text
{'python': 2, 'es': 2, 'genial': 1, 'claro': 1}
```

**Explicación línea por línea:**
- `frecuencias = {}` crea un diccionario vacío.
- `texto.split()` divide el texto en una lista de palabras.
- `frecuencias.get(palabra, 0) + 1` obtiene la cuenta actual (0 si es la primera vez) y le suma 1.
- Así se construye un contador a mano.

**Ejemplo 2 — Lista de tuplas.** Representar datos tabulares sencillos:

```python
temperaturas = [("Madrid", 30), ("Lima", 25), ("Bogotá", 20)]

for ciudad, temp in temperaturas:
    print(f"{ciudad}: {temp}°C")

ciudades = [ciudad for ciudad, _ in temperaturas]
print(ciudades)
```

Salida esperada:

```text
Madrid: 30°C
Lima: 25°C
Bogotá: 20°C
['Madrid', 'Lima', 'Bogotá']
```

**Explicación línea por línea:**
- Cada elemento es una tupla `(ciudad, temperatura)`.
- `for ciudad, temp in temperaturas` desempaqueta cada tupla.
- `[ciudad for ciudad, _ in temperaturas]` usa una comprensión (sección 6) y el guion bajo `_` para ignorar la temperatura.

### 4.9. Errores comunes

**Error 1 — Índice fuera de rango.**

```python
frutas = ["manzana", "pera"]
print(frutas[5])
```

```text
IndexError: list index out of range
```

*Solución:* comprueba `len(frutas)` o usa índices válidos. Recuerda que el último índice es `len - 1`.

**Error 2 — `KeyError` por acceder a una clave inexistente.**

```python
persona = {"nombre": "Ana"}
print(persona["edad"])
```

```text
KeyError: 'edad'
```

*Solución:* usa `.get("edad", 0)` o comprueba `"edad" in persona`.

**Error 3 — Usar una lista como clave de diccionario.**

```python
d = {[1, 2]: "valor"}
```

```text
TypeError: unhashable type: 'list'
```

*Solución:* usa una tupla: `d = {(1, 2): "valor"}`.

**Error 4 — Modificar una lista mientras la recorres.**

```python
nums = [1, 2, 3, 4]
for n in nums:
    if n % 2 == 0:
        nums.remove(n)
print(nums)
```

```text
[1, 3]
```

*¿Por qué es peligroso?* Modificar la lista durante la iteración altera los índices y puede saltarse elementos. En este caso simple funcionó, pero con más datos falla. *Solución:* itera sobre una **copia**: `for n in nums[:]:`.

**Error 5 — Olvidar la coma en una tupla de un elemento.**

```python
t = (5)
print(type(t))
```

```text
<class 'int'>
```

*Solución:* `t = (5,)`.

### 4.10. Checkpoint de comprensión

1. ¿Cuándo usarías una lista y cuándo una tupla?
2. ¿Por qué las claves de un diccionario deben ser inmutables?
3. ¿Cómo eliminarías duplicados de una lista conservando el orden? (Investígalo.)
4. ¿Qué diferencia hay entre `frutas.sort()` y `sorted(frutas)`?

### 4.11. Ejercicio propuesto

**Ejercicio 4.1.** Dada una lista de palabras, crea un diccionario que cuente cuántas veces aparece cada una.

**Pista:** usa `frecuencias.get(palabra, 0) + 1`.

**Ejercicio 4.2.** Dadas dos listas `nombres` y `edades`, crea un diccionario que las relacione.

**Pista:** usa `zip(nombres, edades)` y `dict(...)`.

**Ejercicio 4.3.** Dada una lista con duplicados, obtén una nueva lista sin repetidos **conservando el orden**.

**Pista:** investiga cómo usar `dict.fromkeys(...)`.

---

## 5. Slicing e indexación

### 5.1. La idea central: `[inicio:fin:paso]`

El **slicing** (rebanado) extrae una subsecuencia de una secuencia. La sintaxis es `secuencia[inicio:fin:paso]`, con una regla fundamental: **`inicio` se incluye, `fin` se excluye**.

**Analogía:** cortar una barra de pan. Le dices "desde la rebanada 2 hasta la 5", y entiendes que la 5 no entra. Python hace lo mismo: `[2:5]` toma las rebanadas 2, 3 y 4.

```python
letras = list("abcdefgh")   # ['a','b','c','d','e','f','g','h']

print(letras[2:5])
print(letras[:3])
print(letras[5:])
print(letras[::2])
print(letras[::-1])
```

Salida esperada:

```text
['c', 'd', 'e']
['a', 'b', 'c']
['f', 'g', 'h']
['a', 'c', 'e', 'g']
['h', 'g', 'f', 'e', 'd', 'c', 'b', 'a']
```

**Explicación línea por línea:**
- `letras[2:5]` toma desde el índice 2 hasta el 4 (el 5 no entra): `c, d, e`.
- `letras[:3]` desde el principio hasta el índice 2: `a, b, c`.
- `letras[5:]` desde el índice 5 hasta el final.
- `letras[::2]` toma un elemento de cada dos (paso 2).
- `letras[::-1]` con paso -1 **invierte** la secuencia.

### 5.2. Índices negativos

Los índices negativos cuentan desde el final: `-1` es el último, `-2` el penúltimo, etc.

```python
letras = list("abcdefgh")

print(letras[-1])
print(letras[-3:])
print(letras[:-2])
```

Salida esperada:

```text
h
['f', 'g', 'h']
['a', 'b', 'c', 'd', 'e', 'f']
```

**Explicación línea por línea:**
- `letras[-1]` es el último.
- `letras[-3:]` son los últimos tres.
- `letras[:-2]` es todo menos los dos últimos.

### 5.3. Slicing en cadenas y tuplas

El slicing funciona igual en cadenas y tuplas (ambas son secuencias):

```python
nombre = "programacion"
print(nombre[:8])
print(nombre[8:])
print(nombre[::-1])
```

Salida esperada:

```text
programa
cion
noicamargorp
```

### 5.4. Copia con slicing

Un detalle muy importante: `lista[:]` crea una **copia superficial** de la lista.

```python
original = [1, 2, 3]
copia = original[:]
copia.append(4)
print(original)
print(copia)
```

Salida esperada:

```text
[1, 2, 3]
[1, 2, 3, 4]
```

**Explicación línea por línea:**
- `original[:]` crea una lista **nueva** con los mismos elementos.
- Al añadir a `copia`, `original` no cambia.

**Pero cuidado con la copia "superficial":** si la lista contiene objetos mutables, estos se comparten.

```python
anidada = [[1, 2], [3, 4]]
copia = anidada[:]
copia[0].append(99)   # muta la lista interna, compartida
print(anidada)
```

Salida esperada:

```text
[[1, 2, 99], [3, 4]]
```

*Solución:* para copias profundas, usa `copy.deepcopy()`.

### 5.5. Errores comunes

**Error 1 — Creer que `fin` es inclusivo.**

```python
print(list(range(10))[0:3])   # 3 elementos, no 4
```

```text
[0, 1, 2]
```

*Solución:* recuerda: `inicio` incluido, `fin` excluido.

**Error 2 — Confundir copia superficial con profunda.**

Como viste, `[:]` no copia los objetos internos.

**Error 3 — Índices invertidos.**

```python
print(list(range(5))[3:1])   # []
```

*Solución:* si `inicio >= fin` con paso positivo, el resultado es vacío. Usa un paso negativo si quieres recorrer al revés.

### 5.6. Checkpoint de comprensión

1. ¿Qué devuelve `[1, 2, 3, 4, 5][1:4]`?
2. ¿Cómo invertirías una cadena sin usar `reversed()`?
3. ¿Por qué `lista[:]` no es una copia profunda?

### 5.7. Ejercicio propuesto

**Ejercicio 5.1.** Dada la cadena `"programacion"`, obtén las letras en posiciones pares y luego la cadena invertida.

**Pista:** `cadena[::2]` y `cadena[::-1]`.

**Ejercicio 5.2.** Dada una lista de 10 números, obtén los elementos del centro (índices 3 al 6, ambos incluidos en tu resultado).

**Pista:** recuerda que el fin es exclusivo: para incluir el 6, el fin es 7.

---

## 6. Comprensiones

### 6.1. ¿Qué es una comprensión?

Una **comprensión** es una forma compacta y elegante de construir una colección a partir de otra. Es una de las características más "pythónicas" y expresa la transformación de forma casi matemática.

**Analogía:** si un bucle `for` que llena una lista es "recorre esto y ve apuntando resultados", una comprensión es "dame todos los resultados de aplicar esto a aquello". Más directo y legible.

### 6.2. Comprensión de listas

```python
# Con bucle
cuadrados = []
for n in range(6):
    cuadrados.append(n ** 2)

# Con comprensión
cuadrados = [n ** 2 for n in range(6)]

print(cuadrados)
```

Salida esperada:

```text
[0, 1, 4, 9, 16, 25]
```

**Explicación línea por línea:**
- La comprensión `[n ** 2 for n in range(6)]` se lee: "para cada `n` en `range(6)`, calcula `n ** 2` y reúne los resultados en una lista".
- Es más corta, más rápida y más legible que el bucle equivalente.

**Con condición (filtro):**

```python
pares = [n for n in range(10) if n % 2 == 0]
print(pares)
```

Salida esperada:

```text
[0, 2, 4, 6, 8]
```

La parte `if n % 2 == 0` **filtra**: solo se incluyen los `n` que cumplen la condición.

### 6.3. Comprensión de diccionarios

```python
nombres = ["ana", "luis", "eva"]
longitudes = {n: len(n) for n in nombres}
print(longitudes)
```

Salida esperada:

```text
{'ana': 3, 'luis': 4, 'eva': 3}
```

**Explicación línea por línea:**
- `{n: len(n) for n in nombres}` crea un diccionario donde cada clave es el nombre y cada valor su longitud.

### 6.4. Comprensión de conjuntos

```python
restos = {n % 3 for n in range(10)}
print(restos)
```

Salida esperada:

```text
{0, 1, 2}
```

Observa que los duplicados desaparecen automáticamente (es un conjunto).

### 6.5. Comprensiones anidadas

```python
matriz = [[1, 2], [3, 4], [5, 6]]
plana = [x for fila in matriz for x in fila]
print(plana)
```

Salida esperada:

```text
[1, 2, 3, 4, 5, 6]
```

**Explicación línea por línea:**
- El primer `for` recorre las filas; el segundo, los elementos de cada fila.
- El resultado es una lista plana. Se lee de izquierda a derecha como bucles anidados.

### 6.6. Expresión condicional dentro de la comprensión

Puedes poner un `if/else` para transformar, no solo filtrar:

```python
etiquetas = ["par" if n % 2 == 0 else "impar" for n in range(5)]
print(etiquetas)
```

Salida esperada:

```text
['par', 'impar', 'par', 'impar', 'par']
```

**Diferencia clave:** `[x for n in ... if cond]` **filtra**; `[a if cond else b for n in ...]` **transforma**. La posición del `if` cambia el significado.

### 6.7. Cuándo NO usar comprensiones

Si una comprensión no cabe legiblemente en una línea, usa un bucle. El Zen de Python dice: "la legibilidad cuenta".

```python
# Difícil de leer
resultado = [f(x) if x > 0 else g(x) for x in datos if validar(x) and x != 0]

# Mejor con bucle
resultado = []
for x in datos:
    if validar(x) and x != 0:
        resultado.append(f(x) if x > 0 else g(x))
```

### 6.8. Errores comunes

**Error 1 — Confundir filtro con transformación.**

```python
# Esto FILTRA (solo incluye los pares)
[x for x in range(5) if x % 2 == 0]      # [0, 2, 4]

# Esto TRANSFORMA (todos, con etiqueta)
["par" if x % 2 == 0 else "impar" for x in range(5)]  # ['par', 'impar', ...]
```

**Error 2 — Abusar de comprensiones complejas.** Usa bucles para lógica enrevesada.

**Error 3 — Usar comprensiones con efectos secundarios.**

```python
[print(x) for x in range(3)]   # funciona, pero es mala práctica
```

Las comprensiones son para **construir** colecciones, no para ejecutar efectos.

### 6.9. Checkpoint de comprensión

1. Escribe una comprensión que devuelva los cuadrados de los números impares del 1 al 20.
2. ¿Qué diferencia hay entre `[x for x in nums if x > 0]` y `[x if x > 0 else 0 for x in nums]`?
3. ¿Cuándo NO deberías usar una comprensión?

### 6.10. Ejercicio propuesto

**Ejercicio 6.1.** Crea una lista con los cubos de los números del 1 al 20 que sean múltiplos de 3.

**Pista:** combina el cálculo con un `if` dentro de la comprensión.

**Ejercicio 6.2.** Dada una frase, crea un diccionario que asocie cada palabra con su longitud.

**Pista:** `{p: len(p) for p in frase.split()}`.

---

## 7. F-strings y formateo

### 7.1. ¿Por qué formatear texto?

Casi todos los programas muestran información al usuario: mensajes, informes, tablas. Necesitas **insertar valores** dentro de textos y, a menudo, **darles formato** (decimales, alineación, separadores de miles). Las **f-strings** son la forma moderna, clara y recomendada de hacerlo.

### 7.2. Uso básico

```python
nombre = "Ana"
edad = 30

print(f"{nombre} tiene {edad} años.")
print(f"El año que viene tendrá {edad + 1}.")
print(f"{nombre.upper()} en mayúsculas.")
```

Salida esperada:

```text
Ana tiene 30 años.
El año que viene tendrá 31.
ANA en mayúsculas.
```

**Explicación línea por línea:**
- La `f` antes de las comillas activa el modo f-string.
- `{nombre}` inserta el valor de la variable.
- `{edad + 1}` demuestra que dentro de las llaves puede haber **expresiones**, no solo variables.
- `{nombre.upper()}` llama a un método dentro de las llaves.

### 7.3. Formato de números

```python
precio = 1234.5678

print(f"{precio:.2f}")
print(f"{precio:,.2f}")
print(f"{0.256:.1%}")
print(f"{42:05d}")
print(f"{255:x}")
```

Salida esperada:

```text
1234.57
1,234.57
25.6%
00042
ff
```

**Explicación línea por línea:**
- `{precio:.2f}` → 2 decimales (`.2f` significa *float* con 2 decimales).
- `{precio:,.2f}` → 2 decimales **y** separador de miles.
- `{0.256:.1%}` → formato de porcentaje (multiplica por 100 y añade `%`).
- `{42:05d}` → entero (`d`) rellenado con ceros a la izquierda hasta 5 dígitos.
- `{255:x}` → representación **hexadecimal** (255 en base 16 es `ff`).

### 7.4. Alineación

Muy útil para tablas:

```python
datos = [("Ana", 9.5), ("Luis", 7.0), ("Alejandra", 10.0)]

for nombre, nota in datos:
    print(f"{nombre:<12}{nota:>6.1f}")
```

Salida esperada:

```text
Ana            9.5
Luis           7.0
Alejandra     10.0
```

**Explicación línea por línea:**
- `<12` alinea el texto a la **izquierda** en un ancho de 12 caracteres.
- `>6.1f` alinea el número a la **derecha** en 6 caracteres con 1 decimal.
- El resultado es una tabla con columnas alineadas.

### 7.5. Alternativas históricas (y por qué evitarlas)

```python
nombre = "Ana"

# str.format (aún se ve en código antiguo)
print("Hola, {}".format(nombre))

# Operador % (estilo muy antiguo)
print("Hola, %s" % nombre)
```

Ambas funcionan, pero son más verbosas y propensas a errores. **Usa f-strings** salvo que necesites compatibilidad con versiones muy antiguas.

### 7.6. Errores comunes

**Error 1 — Olvidar la `f`.**

```python
nombre = "Ana"
print("{nombre}")
```

```text
{nombre}
```

*¿Por qué?* Sin la `f`, Python lo trata como texto literal. *Solución:* `print(f"{nombre}")`.

**Error 2 — Usar comillas conflictivas.**

```python
# En Python < 3.12, esto da error:
print(f"{'hola'}")
```

*Solución:* en versiones antiguas, usa comillas distintas dentro y fuera, o extrae el valor a una variable.

**Error 3 — Cálculos complejos dentro de las llaves.**

```python
print(f"{calcular(x, y) if condicion else otro(z)}")
```

*Solución:* calcula antes en una variable con nombre claro y muéstrala.

### 7.7. Checkpoint de comprensión

1. ¿Cómo mostrarías el número 3.14159 con solo 2 decimales?
2. ¿Qué significa `<12` en una f-string?
3. ¿Por qué se prefieren las f-strings a `%` o `.format()`?

### 7.8. Ejercicio propuesto

**Ejercicio 7.1.** Muestra una tabla de conversión de euros a dólares con 2 decimales y columnas alineadas, para los valores 1, 10, 50 y 100 euros (usa una tasa fija).

**Pista:** usa `{valor:>10.2f}` y una constante `TASA = 1.08`.

---

## 8. Fechas y horas

### 8.1. ¿Por qué es difícil trabajar con fechas?

Las fechas parecen sencillas, pero esconden complejidad: meses de distinta duración, años bisiestos, zonas horarias, horario de verano... Por eso Python trae dos módulos estándar muy potentes: **`datetime`** y **`zoneinfo`**.

| Clase | Representa |
|---|---|
| `date` | Una fecha (año, mes, día) |
| `time` | Una hora (hora, minuto, segundo) |
| `datetime` | Fecha y hora juntas |
| `timedelta` | Una duración (diferencia entre fechas) |

### 8.2. Obtener la fecha y hora actual

```python
from datetime import datetime

ahora = datetime.now()
print(ahora)
print(ahora.year, ahora.month, ahora.day)
print(ahora.strftime("%d/%m/%Y %H:%M"))
```

Salida esperada (varía según el momento):

```text
2026-09-16 10:30:45.123456
2026 9 16
16/09/2026 10:30
```

**Explicación línea por línea:**
- `from datetime import datetime` importa la clase `datetime` del módulo del mismo nombre.
- `datetime.now()` devuelve la fecha y hora actuales.
- `.year`, `.month`, `.day` acceden a los componentes.
- `.strftime("%d/%m/%Y %H:%M")` **formatea** la fecha como texto. `%d` = día, `%m` = mes, `%Y` = año con siglo, `%H:%M` = hora y minuto.

### 8.3. Crear fechas y parsear texto

```python
from datetime import date, datetime

cumple = date(2026, 9, 16)
print(cumple)

fecha = datetime.strptime("16/09/2026", "%d/%m/%Y")
print(fecha)
print(type(fecha))
```

Salida esperada:

```text
2026-09-16
2026-09-16 00:00:00
<class 'datetime.datetime'>
```

**Explicación línea por línea:**
- `date(2026, 9, 16)` crea una fecha directamente.
- `datetime.strptime(texto, formato)` **parsea** (interpreta) un texto según un formato y devuelve un `datetime`.

**Regla mnemotécnica:** `strftime` = *string format time* (formatear a texto); `strptime` = *string parse time* (parsear desde texto). La `f` es de *format*, la `p` de *parse*.

### 8.4. Aritmética con `timedelta`

```python
from datetime import date, timedelta

hoy = date(2026, 9, 16)

print(hoy + timedelta(days=10))
print(hoy - timedelta(weeks=1))
print((date(2026, 12, 31) - hoy).days, "días para fin de año")
```

Salida esperada:

```text
2026-09-26
2026-09-09
106 días para fin de año
```

**Explicación línea por línea:**
- `timedelta(days=10)` representa una duración de 10 días.
- Sumar o restar un `timedelta` a una fecha devuelve una fecha nueva.
- Restar dos fechas devuelve un `timedelta`; `.days` extrae el número de días.

### 8.5. Zonas horarias con `zoneinfo`

Aquí está la parte que mucha gente ignora y que causa bugs sutiles. Un `datetime` sin zona horaria es **ambiguo**: "las 10:00" ¿en qué parte del mundo?

```python
from datetime import datetime
from zoneinfo import ZoneInfo

madrid = datetime.now(ZoneInfo("Europe/Madrid"))
tokio = madrid.astimezone(ZoneInfo("Asia/Tokyo"))

print("Madrid:", madrid.strftime("%H:%M"))
print("Tokio: ", tokio.strftime("%H:%M"))
```

Salida esperada:

```text
Madrid: 10:30
Tokio:  17:30
```

**Explicación línea por línea:**
- `ZoneInfo("Europe/Madrid")` representa la zona horaria de Madrid (base de datos IANA).
- `datetime.now(zona)` obtiene la hora actual en esa zona.
- `.astimezone(otra_zona)` convierte el mismo instante a otra zona. El momento es el mismo; solo cambia la representación.

**Regla profesional:** en aplicaciones reales, guarda y trabaja en **UTC** y convierte a la zona del usuario solo al mostrar.

### 8.6. Errores comunes

**Error 1 — Trabajar con fechas "naive" (sin zona).**

Un `datetime` sin zona es ambiguo. En sistemas con usuarios en varios países, provoca errores de horas. *Solución:* usa `ZoneInfo` y trabaja en UTC.

**Error 2 — Confundir `strftime` con `strptime`.**

*Solución:* recuerda la regla mnemotécnica de la sección 8.3.

**Error 3 — Usar `datetime.utcnow()` (desaconsejado).**

Devuelve un `datetime` naive en UTC. *Solución:* `datetime.now(ZoneInfo("UTC"))`.

**Error 4 — Restar fechas naive y conscientes de zona.**

```python
# datetime.now() (naive) - datetime.now(ZoneInfo("UTC"))  -> TypeError
```

*Solución:* no mezcles fechas naive y conscientes de zona.

### 8.7. Checkpoint de comprensión

1. ¿Qué diferencia hay entre `strftime` y `strptime`?
2. ¿Por qué se recomienda trabajar en UTC y convertir solo al mostrar?
3. ¿Cómo calculas cuántos días faltan entre dos fechas?

### 8.8. Ejercicio propuesto

**Ejercicio 8.1.** Calcula cuántos días faltan desde hoy hasta tu próximo cumpleaños.

**Pista:** construye un `date` con el próximo cumpleaños y resta `date.today()`.

**Ejercicio 8.2.** Dada una lista de fechas en texto `"dd/mm/aaaa"`, conviértelas a objetos `date` y ordénalas.

**Pista:** usa `datetime.strptime` y `sorted`.

---

## 9. Ejemplo integrador

Construiremos un **analizador de gastos mensuales** que reúne: listas de diccionarios, diccionarios, comprensiones, f-strings con alineación y fechas.

```python
"""Resumen de gastos mensuales."""

from datetime import date

GASTOS = [
    {"fecha": date(2026, 9, 2), "categoria": "comida", "monto": 45.50},
    {"fecha": date(2026, 9, 5), "categoria": "transporte", "monto": 20.00},
    {"fecha": date(2026, 9, 9), "categoria": "comida", "monto": 30.25},
    {"fecha": date(2026, 9, 12), "categoria": "ocio", "monto": 60.00},
    {"fecha": date(2026, 9, 15), "categoria": "transporte", "monto": 15.75},
]


def total_por_categoria(gastos: list[dict]) -> dict[str, float]:
    """Agrupa y suma los gastos por categoría."""
    resumen: dict[str, float] = {}
    for gasto in gastos:
        cat = gasto["categoria"]
        resumen[cat] = resumen.get(cat, 0.0) + gasto["monto"]
    return resumen


def main() -> None:
    """Muestra el resumen y el total general."""
    resumen = total_por_categoria(GASTOS)
    for categoria, total in sorted(resumen.items()):
        print(f"{categoria:<12}{total:>10.2f} €")
    gran_total = sum(resumen.values())
    print("-" * 22)
    print(f"{'TOTAL':<12}{gran_total:>10.2f} €")


if __name__ == "__main__":
    main()
```

Salida esperada:

```text
comida           75.75 €
ocio             60.00 €
transporte       35.75 €
----------------------
TOTAL           171.50 €
```

**Explicación línea por línea:**
- `GASTOS` es una lista de diccionarios; cada uno tiene una fecha (`date`), una categoría y un monto (`float`).
- `total_por_categoria` acumula en un diccionario usando `.get(cat, 0.0) + monto`, el patrón de conteo.
- `sorted(resumen.items())` ordena alfabéticamente por categoría.
- `f"{categoria:<12}{total:>10.2f} €"` alinea la categoría a la izquierda y el total a la derecha con 2 decimales.
- `sum(resumen.values())` suma todos los totales.
- `"-" * 22` genera una línea de 22 guiones.

**Conceptos integrados:** listas de diccionarios, `dict.get`, `sorted`, `sum`, f-strings con alineación, `datetime.date` y el bloque `__main__`.

---

## 10. Buenas prácticas

1. **Usa f-strings** en lugar de `%` o `.format()`.
2. **Prefiere comprensiones** para transformaciones simples; bucles para lógica compleja.
3. **No uses listas como claves** de diccionario (usa tuplas).
4. **Trabaja con fechas conscientes de zona** en aplicaciones reales; usa UTC internamente.
5. **Evita nombres que sombreen built-ins** (`list`, `dict`, `id`, `type`).
6. **Compara flotantes con `math.isclose()`**, nunca con `==`.
7. **Recuerda que `fin` en slicing es exclusivo.**
8. **Usa `is` solo con `None`/`True`/`False`.**
9. **Convierte siempre el resultado de `input()`.**
10. **Para dinero, evita `float`:** usa `Decimal` o céntimos como enteros.

---

## 11. Recursos

- **Documentación de tipos:** <https://docs.python.org/3/library/stdtypes.html>
- **`datetime`:** <https://docs.python.org/3/library/datetime.html>
- **`zoneinfo`:** <https://docs.python.org/3/library/zoneinfo.html>
- **Tutorial oficial:** <https://docs.python.org/3/tutorial/introduction.html>
- **Libro:** *Fluent Python*, Luciano Ramalho (capítulos 1–2).

---

## 12. Mini resumen final

- Python infiere tipos: **int, float, complex, bool, str, None**.
- Los **enteros** no se desbordan; los **floats** tienen imprecisión (usa `isclose`).
- Los valores **falsy** (`0`, `""`, `[]`, `None`…) permiten condiciones naturales.
- Los **operadores** incluyen aritméticos, comparación, lógicos, identidad y pertenencia.
- **list** (mutable), **tuple** (inmutable), **dict** (clave-valor), **set** (sin duplicados).
- El **slicing** `[inicio:fin:paso]` extrae subsecuencias; `fin` es exclusivo.
- Las **comprensiones** construyen listas/diccionarios/conjuntos de forma concisa.
- Las **f-strings** son la forma moderna de formatear texto.
- `datetime` + `zoneinfo` gestionan fechas, horas y zonas horarias.

---

### 🎯 Retos opcionales

1. **Reto 1:** Procesa una cadena y devuelve la palabra más larga sin usar comprensiones.
2. **Reto 2:** Convierte una lista de fechas en formato `"YYYY-MM-DD"` a objetos `date` y ordénalos.
3. **Reto 3:** Crea una función que reciba un diccionario anidado y lo "aplane" a claves con notación de punto (`a.b.c`).

**Anterior:** [01_introduccion_python.md](01_introduccion_python.md) · **Siguiente:** [03_control_flujo.md](03_control_flujo.md)
