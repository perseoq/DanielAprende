# 03 · Control de Flujo

**Versión recomendada:** Python 3.12 (la sección `match` requiere 3.10+)
**Requisito:** [02_sintaxis_tipos_datos.md](02_sintaxis_tipos_datos.md)
**Nivel:** Principiante
**Duración estimada de estudio:** 5–7 horas

---

## Índice de contenidos

1. [Condicionales: if, elif, else](#1-condicionales-if-elif-else)
2. [Bucles: for y while](#2-bucles-for-y-while)
3. [break, continue, pass y else en bucles](#3-break-continue-pass-y-else-en-bucles)
4. [Match-case](#4-match-case-python-310)
5. [Iteradores](#5-iteradores)
6. [Generadores y yield](#6-generadores-y-yield)
7. [Ejemplo integrador](#7-ejemplo-integrador)
8. [Buenas prácticas](#8-buenas-prácticas)
9. [Recursos](#9-recursos)
10. [Mini resumen final](#10-mini-resumen-final)

---

## 1. Condicionales: if, elif, else

### 1.1. ¿Por qué necesita un programa tomar decisiones?

Un programa que siempre hace lo mismo en el mismo orden es poco útil. La mayoría de las tareas reales requieren **elegir**: si el usuario tiene saldo, permitir la compra; si la contraseña es correcta, dejar entrar; si la temperatura baja de cero, encender la calefacción.

Los **condicionales** permiten ejecutar bloques de código **solo cuando se cumple una condición**. Son la base del razonamiento en programación.

**Analogía:** un condicional es un semáforo o una bifurcación en la carretera. Según lo que "veas" (la condición), tomas un camino u otro.

### 1.2. La estructura básica

```python
if condicion:
    # este bloque se ejecuta si la condición es verdadera
```

Vamos con un ejemplo concreto:

```python
edad = 20

if edad >= 18:
    print("Puedes votar")
```

Salida esperada:

```text
Puedes votar
```

**Explicación línea por línea:**
- `edad = 20` asigna el valor.
- `if edad >= 18:` evalúa la condición `edad >= 18`, que es `True`.
- Los **dos puntos `:`** marcan el inicio del bloque. Son obligatorios.
- La línea siguiente va **indentada con 4 espacios**: en Python, la indentación **define qué líneas pertenecen al bloque**. No es decoración: es sintaxis.

**¿Qué pasa si la condición es falsa?** El bloque se **salta** y el programa continúa:

```python
edad = 15

if edad >= 18:
    print("Puedes votar")

print("Fin del programa")
```

Salida esperada:

```text
Fin del programa
```

Observa que el segundo `print` está **sin indentar**, por lo que está **fuera** del `if` y se ejecuta siempre.

### 1.3. Añadiendo alternativas: else

`else` define qué hacer cuando la condición es falsa:

```python
edad = 15

if edad >= 18:
    print("Puedes votar")
else:
    print("Aún no puedes votar")
```

Salida esperada:

```text
Aún no puedes votar
```

**Explicación línea por línea:**
- `else:` no lleva condición: se ejecuta en "cualquier otro caso".
- Va al mismo nivel de indentación que el `if`.

### 1.4. Múltiples casos: elif

Cuando hay más de dos posibilidades, se usa `elif` (*else if*):

```python
nota = 7.5

if nota >= 9:
    categoria = "sobresaliente"
elif nota >= 7:
    categoria = "notable"
elif nota >= 5:
    categoria = "aprobado"
else:
    categoria = "suspenso"

print(categoria)
```

Salida esperada:

```text
notable
```

**Explicación línea por línea — este es el punto clave:**
- Se evalúa `nota >= 9` → `7.5 >= 9` es `False`; se pasa al siguiente.
- Se evalúa `nota >= 7` → `7.5 >= 7` es `True`: **se ejecuta ese bloque y se ignoran todos los siguientes**.
- Si ninguna condición hubiera sido verdadera, se ejecutaría `else`.

**Regla de oro:** Python evalúa las ramas **de arriba abajo** y ejecuta **solo la primera** que sea verdadera. El orden importa. Si hubiéramos puesto `nota >= 5` antes que `nota >= 7`, una nota de 7.5 daría "aprobado", lo cual es incorrecto.

### 1.5. Condiciones compuestas

Puedes combinar condiciones con `and`, `or`, `not`:

```python
edad = 25
tiene_entrada = True

if edad >= 18 and tiene_entrada:
    print("Puedes entrar")

temperatura = 5
if temperatura < 0 or temperatura > 40:
    print("Temperatura extrema")

if not tiene_entrada:
    print("Necesitas una entrada")
```

Salida esperada:

```text
Puedes entrar
```

**Explicación línea por línea:**
- `edad >= 18 and tiene_entrada` es `True` solo si **ambas** son verdaderas.
- `temperatura < 0 or temperatura > 40` es `False` (5 no cumple ninguna), así que no imprime nada.
- `not tiene_entrada` invierte el valor: como `tiene_entrada` es `True`, `not True` es `False`.

### 1.6. La expresión condicional (operador ternario)

Para decisiones simples, Python permite escribir un `if/else` en una sola línea:

```python
edad = 20
estado = "mayor" if edad >= 18 else "menor"
print(estado)
```

Salida esperada:

```text
mayor
```

**Explicación línea por línea:**
- `"mayor" if edad >= 18 else "menor"` se lee: "el valor `'mayor'` si la condición es verdadera; si no, `'menor'`".
- Es útil para asignaciones simples, pero **no abuses**: si la lógica es compleja, usa un `if` normal.

### 1.7. Truthiness en condiciones

Recuerda del archivo 02 que muchos valores se evalúan como falsos. Aprovéchalo para escribir condiciones naturales:

```python
usuario = ""

if not usuario:
    print("Debes iniciar sesión")

carrito = []
if carrito:
    print("Tienes productos")
else:
    print("El carrito está vacío")
```

Salida esperada:

```text
Debes iniciar sesión
El carrito está vacío
```

**Explicación línea por línea:**
- `if not usuario:` es verdadero porque `""` es falsy.
- `if carrito:` es falso porque `[]` es falsy.

Es más limpio que comparar con `== ""` o `len(carrito) == 0`.

### 1.8. Condicionales anidados

Un `if` puede contener otro `if`. Pero cuidado con la profundidad excesiva:

```python
usuario = "ana"
password = "1234"

if usuario == "ana":
    if password == "1234":
        print("Acceso concedido")
    else:
        print("Contraseña incorrecta")
else:
    print("Usuario desconocido")
```

Salida esperada:

```text
Acceso concedido
```

**Explicación línea por línea:**
- El segundo `if` está indentado dentro del primero: solo se evalúa si el usuario es correcto.
- Este patrón funciona, pero si anidas tres o cuatro niveles, el código se vuelve difícil de leer. Una alternativa es combinar condiciones: `if usuario == "ana" and password == "1234":`.

### 1.9. Ejemplos progresivos

**Ejemplo 1 — Clasificar un número.**

```python
numero = -5

if numero > 0:
    print("positivo")
elif numero < 0:
    print("negativo")
else:
    print("cero")
```

Salida esperada:

```text
negativo
```

**Ejemplo 2 — Validar un formulario.**

```python
nombre = "Ana"
email = "ana@example.com"

if not nombre:
    print("Falta el nombre")
elif "@" not in email:
    print("El email no es válido")
else:
    print("Formulario correcto")
```

Salida esperada:

```text
Formulario correcto
```

**Explicación línea por línea:**
- Primero se comprueba el caso más básico (nombre vacío).
- Si el nombre está bien, se comprueba el email.
- Si todo pasa, se confirma. Este patrón de "guardas" es muy común.

**Ejemplo 3 — Descuento por tramos.**

```python
compra = 150

if compra >= 200:
    descuento = 0.20
elif compra >= 100:
    descuento = 0.10
else:
    descuento = 0.0

total = compra * (1 - descuento)
print(f"Total con descuento: {total:.2f} €")
```

Salida esperada:

```text
Total con descuento: 135.00 €
```

**Explicación línea por línea:**
- 150 no llega a 200, pero sí a 100, así que el descuento es del 10 %.
- `compra * (1 - 0.10)` = 150 × 0.9 = 135.

### 1.10. Errores comunes

**Error 1 — Usar varios `if` en lugar de `elif`.**

```python
nota = 7.5
if nota >= 5:
    print("aprobado")
if nota >= 7:
    print("notable")     # se ejecuta también
```

Salida esperada:

```text
aprobado
notable
```

*¿Por qué?* Con `if` separados, **todos** se evalúan. Si querías categorías exclusivas, debías usar `elif`. *Solución:* usa `elif` para casos mutuamente excluyentes.

**Error 2 — `=` en lugar de `==`.**

```python
if edad = 18:
    ...
```

```text
SyntaxError: invalid syntax
```

*Solución:* `=` asigna, `==` compara.

**Error 3 — Comparar con `True`/`False` innecesariamente.**

```python
if activo == True:   # funciona, pero redundante
    ...
```

*Solución:* `if activo:`.

**Error 4 — Olvidar los dos puntos o la indentación.**

```python
if edad >= 18
    print("Puedes votar")
```

```text
SyntaxError: expected ':'
```

*Solución:* no olvides los `:` ni la indentación de 4 espacios.

### 1.11. Checkpoint de comprensión

1. ¿Cuál es la diferencia entre varios `if` y una cadena `if/elif/else`?
2. ¿Por qué el orden de las condiciones en una cadena `elif` importa?
3. Reescribe `if activo == True:` de forma más limpia.
4. ¿Cuándo usarías el operador ternario y cuándo no?

### 1.12. Ejercicio propuesto

**Ejercicio 1.1.** Dado un año, determina si es bisiesto (divisible por 4, excepto los divisibles por 100 salvo los divisibles por 400).

**Pista:** usa `%` y combina condiciones con `and`/`or` y paréntesis.

**Ejercicio 1.2.** Pide una nota (0–10) al usuario y muestra la categoría correspondiente. Valida que esté en rango.

**Pista:** usa `input()`, `float()` y una cadena `if/elif/else`.

---

## 2. Bucles: for y while

### 2.1. ¿Por qué necesitamos repetir?

Imagina que quieres imprimir los números del 1 al 100. Escribir 100 `print` es absurdo. Los **bucles** permiten **repetir** un bloque de código.

Python tiene dos tipos de bucles, y elegir el correcto es importante:

- **`for`**: cuando sabes **sobre qué** iterar (una colección, un rango). "Haz esto **para cada** elemento".
- **`while`**: cuando repites **mientras** se cumpla una condición. "Haz esto **mientras** siga siendo verdad".

**Analogía:** el `for` es como pasar lista: recorres una lista de nombres. El `while` es como esperar el autobús: repites "mirar si llega" mientras no haya llegado.

### 2.2. El bucle for

```python
for i in range(5):
    print(i)
```

Salida esperada:

```text
0
1
2
3
4
```

**Explicación línea por línea:**
- `range(5)` genera la secuencia 0, 1, 2, 3, 4 (cinco números, empezando en 0 y **sin incluir** el 5).
- En cada vuelta, la variable `i` toma un valor de esa secuencia.
- El bloque indentado se ejecuta una vez por cada valor.

**Recorrer una lista:**

```python
frutas = ["manzana", "pera", "uva"]
for fruta in frutas:
    print(fruta.upper())
```

Salida esperada:

```text
MANZANA
PERA
UVA
```

**Recorrer una cadena:**

```python
for letra in "abc":
    print(letra)
```

Salida esperada:

```text
a
b
c
```

### 2.3. La función `range` en detalle

`range` tiene tres formas:

```python
print(list(range(5)))
print(list(range(2, 8)))
print(list(range(2, 8, 2)))
print(list(range(5, 0, -1)))
```

Salida esperada:

```text
[0, 1, 2, 3, 4]
[2, 3, 4, 5, 6, 7]
[2, 4, 6]
[5, 4, 3, 2, 1]
```

**Explicación línea por línea:**
- `range(5)` → de 0 a 4.
- `range(2, 8)` → de 2 a 7 (el 8 no entra).
- `range(2, 8, 2)` → de 2 a 7, con paso 2.
- `range(5, 0, -1)` → de 5 a 1, descendiendo (paso -1).

**Nota:** `range` no crea una lista en memoria; genera los números a medida que se necesitan. Por eso envolvemos con `list(...)` solo para mostrarlo.

### 2.4. Iterar con índice: `enumerate`

A veces necesitas tanto el elemento como su posición. `enumerate` te da ambos:

```python
animales = ["perro", "gato", "pez"]

for i, animal in enumerate(animales):
    print(i, animal)
```

Salida esperada:

```text
0 perro
1 gato
2 pez
```

**Explicación línea por línea:**
- `enumerate(animales)` devuelve pares `(índice, elemento)`.
- El `for i, animal` **desempaqueta** cada par.

Puedes empezar en 1 con `enumerate(animales, start=1)`:

```python
for i, animal in enumerate(animales, start=1):
    print(f"{i}. {animal}")
```

Salida esperada:

```text
1. perro
2. gato
3. pez
```

**¿Por qué no usar `range(len(lista))`?** Porque `enumerate` es más limpio, más rápido y menos propenso a errores.

### 2.5. Iterar en paralelo: `zip`

`zip` combina dos o más secuencias, tomando un elemento de cada una en cada vuelta:

```python
nombres = ["Ana", "Luis", "Eva"]
edades = [30, 25, 28]

for nombre, edad in zip(nombres, edades):
    print(f"{nombre}: {edad}")
```

Salida esperada:

```text
Ana: 30
Luis: 25
Eva: 28
```

**Explicación línea por línea:**
- `zip(nombres, edades)` produce `("Ana", 30)`, `("Luis", 25)`, `("Eva", 28)`.
- El `for` desempaqueta cada par.

**Importante:** `zip` se detiene en la secuencia **más corta**:

```python
print(list(zip([1, 2, 3], ["a", "b"])))
```

Salida esperada:

```text
[(1, 'a'), (2, 'b')]
```

### 2.6. El bucle while

`while` repite mientras la condición sea verdadera:

```python
intentos = 0
while intentos < 3:
    print(f"Intento {intentos + 1}")
    intentos += 1
print("Fin")
```

Salida esperada:

```text
Intento 1
Intento 2
Intento 3
Fin
```

**Explicación línea por línea:**
- Antes de cada vuelta, se evalúa `intentos < 3`.
- Dentro del bloque, `intentos += 1` **actualiza** la variable. Si no lo hiciera, la condición nunca cambiaría y tendríamos un **bucle infinito**.
- Cuando `intentos` llega a 3, la condición es falsa y el bucle termina.

### 2.7. El patrón del bucle infinito controlado

Un uso muy común de `while` es repetir hasta que el usuario decida salir:

```python
while True:
    respuesta = input("Escribe 'salir' para terminar: ")
    if respuesta == "salir":
        break
    print(f"Escribiste: {respuesta}")
print("Adiós")
```

Ejemplo de ejecución:

```text
Escribe 'salir' para terminar: hola
Escribiste: hola
Escribe 'salir' para terminar: salir
Adiós
```

**Explicación línea por línea:**
- `while True:` crea un bucle que en principio nunca termina por sí solo.
- La única salida es `break`, que lo corta.
- Este patrón es ideal para menús e interacciones.

### 2.8. ¿for o while?

| Usa `for` cuando... | Usa `while` cuando... |
|---|---|
| Iteras sobre una colección conocida | Repites según una condición |
| Sabes cuántas veces repetir | No sabes cuántas veces |
| Recorres un rango | Esperas un evento o entrada |

**Regla:** si puedes usar `for`, úsalo. Es más difícil equivocarse con él.

### 2.9. Errores comunes

**Error 1 — Bucle infinito por no actualizar la condición.**

```python
i = 0
while i < 5:
    print(i)      # ¡nunca incrementa i!
```

*Síntoma:* el programa no para. *Solución:* actualiza la variable dentro del bucle (`i += 1`). Si te pasa, pulsa `Ctrl+C` para interrumpir.

**Error 2 — Modificar una lista mientras la recorres.**

```python
nums = [1, 2, 3, 4]
for n in nums:
    if n % 2 == 0:
        nums.remove(n)
print(nums)   # resultado inesperado
```

*Solución:* itera sobre una copia: `for n in nums[:]:`.

**Error 3 — Usar `range(len(...))` para indexar.**

```python
for i in range(len(animales)):
    print(animales[i])
```

*Solución:* `for animal in animales:` o `for i, animal in enumerate(animales):`.

**Error 4 — Olvidar que `range` no incluye el final.**

```python
for i in range(1, 5):
    print(i)   # imprime 1,2,3,4 — no el 5
```

*Solución:* si quieres incluir el 5, usa `range(1, 6)`.

### 2.10. Checkpoint de comprensión

1. ¿Cuándo usarías `for` y cuándo `while`?
2. ¿Qué devuelve `range(3, 10, 3)`?
3. ¿Por qué es mejor `enumerate` que `range(len(lista))`?
4. ¿Qué pasa si `zip` recibe listas de distinto tamaño?

### 2.11. Ejercicio propuesto

**Ejercicio 2.1.** Imprime la tabla de multiplicar del 1 al 5 usando bucles anidados.

**Pista:** un `for` externo para la tabla (1 a 5) y otro interno para los factores.

**Ejercicio 2.2.** Pide números al usuario en un bucle hasta que escriba "fin", y muestra la suma total.

**Pista:** usa `while True` y `break`; acumula en una variable.

---

## 3. break, continue, pass y else en bucles

### 3.1. Control fino de los bucles

A veces necesitas interrumpir un bucle antes de tiempo, saltar una iteración o marcar un bloque vacío. Python ofrece cuatro herramientas para ello.

| Sentencia | Efecto |
|---|---|
| `break` | Sale del bucle **inmediatamente** |
| `continue` | Salta a la **siguiente iteración** |
| `pass` | No hace nada (marcador de posición) |
| `else` | Se ejecuta si el bucle terminó **sin `break`** |

### 3.2. break: salir del bucle

```python
numeros = [1, 2, 3, 4, 5, 6]

for n in numeros:
    if n == 4:
        print("Encontré el 4, salgo")
        break
    print(n)
```

Salida esperada:

```text
1
2
3
Encontré el 4, salgo
```

**Explicación línea por línea:**
- El bucle imprime 1, 2, 3.
- Al llegar a 4, la condición `n == 4` es verdadera: se imprime el mensaje y `break` **corta** el bucle.
- Los números 5 y 6 **nunca** se procesan.

### 3.3. continue: saltar una iteración

```python
for n in range(6):
    if n % 2 == 0:
        continue     # salta los pares
    print(n)
```

Salida esperada:

```text
1
3
5
```

**Explicación línea por línea:**
- Si `n` es par, `continue` salta directamente a la siguiente vuelta, sin ejecutar el `print`.
- Solo se imprimen los impares.

**Diferencia clave con `break`:** `continue` **sigue iterando**; `break` **corta el bucle**.

### 3.4. pass: el marcador vacío

`pass` no hace absolutamente nada. Se usa cuando la sintaxis exige un bloque pero todavía no lo has escrito:

```python
def funcion_por_implementar():
    pass   # TODO: implementar más adelante

for i in range(3):
    pass   # bucle intencionadamente vacío
```

**¿Por qué existe?** Porque Python no permite bloques vacíos. `pass` los rellena sin hacer nada.

### 3.5. El else de un bucle: el patrón de búsqueda

Aquí hay una característica poco conocida pero muy elegante: un bucle puede tener `else`, que se ejecuta **solo si el bucle terminó sin `break`**.

```python
objetivo = 4

for n in [1, 2, 3]:
    if n == objetivo:
        print("Encontrado")
        break
else:
    print("No encontrado")
```

Salida esperada:

```text
No encontrado
```

**Explicación línea por línea:**
- El bucle recorre 1, 2, 3; ninguno es 4, así que nunca hace `break`.
- Al terminar sin `break`, se ejecuta el `else`.
- Si hubiera encontrado el objetivo, el `else` **no** se ejecutaría.

**¿Por qué es útil?** Reemplaza el uso de una "bandera" booleana, haciendo el código más limpio:

```python
# Sin else de bucle (más verboso)
encontrado = False
for n in [1, 2, 3]:
    if n == objetivo:
        encontrado = True
        break
if not encontrado:
    print("No encontrado")

# Con else de bucle (más limpio)
for n in [1, 2, 3]:
    if n == objetivo:
        break
else:
    print("No encontrado")
```

### 3.6. Ejemplo integrador de las cuatro sentencias

```python
numeros = [1, 3, 5, 7, 8, 9]

for n in numeros:
    if n == 5:
        print("Saltando el 5")
        continue
    if n == 8:
        print("Encontrado el 8, salgo")
        break
    print(n)
else:
    print("No hubo break")
```

Salida esperada:

```text
1
3
Saltando el 5
7
Encontrado el 8, salgo
```

**Explicación línea por línea:**
- 1 y 3 se imprimen.
- El 5 se salta con `continue`.
- El 7 se imprime.
- El 8 provoca `break`.
- Como hubo `break`, el `else` **no** se ejecuta.

### 3.7. Errores comunes

**Error 1 — Confundir `continue` con `break`.**

*Síntoma:* el bucle termina antes de lo esperado (o no termina). *Solución:* recuerda: `continue` salta una vuelta; `break` sale del bucle.

**Error 2 — Olvidar que el `else` del bucle también se ejecuta si la secuencia está vacía.**

```python
for n in []:
    ...
else:
    print("No hubo break")   # se imprime, aunque no se iteró nada
```

*Solución:* tenlo en cuenta al usar `for/else`.

**Error 3 — Usar `pass` creyendo que "salta".**

`pass` no salta nada: simplemente no hace nada. Para saltar una iteración, usa `continue`.

### 3.8. Checkpoint de comprensión

1. ¿Qué diferencia hay entre `break` y `continue`?
2. ¿Cuándo se ejecuta el `else` de un bucle?
3. Reescribe un patrón de "bandera encontrada" usando `for/else`.

### 3.9. Ejercicio propuesto

**Ejercicio 3.1.** Dada una lista de números, encuentra el primer número primo usando `for/else`. Si no hay ninguno, imprime un mensaje.

**Pista:** `else` se ejecuta cuando el bucle no hizo `break`.

**Ejercicio 3.2.** Recorre los números del 1 al 20 e imprime solo los que **no** sean múltiplos de 3.

**Pista:** usa `continue`.

---

## 4. Match-case (Python 3.10+)

### 4.1. ¿Qué es el pattern matching?

Durante décadas, otros lenguajes tuvieron una estructura `switch` para elegir entre muchos casos. Python llegó tarde a esa fiesta, pero lo hizo con una herramienta **más potente**: `match`, introducido en Python 3.10. No solo compara valores: compara **estructuras** y puede **extraer** partes.

**Analogía:** un `if/elif` es como preguntar "¿eres esto? ¿eres aquello?". Un `match` es como un clasificador de correo que reconoce **patrones** ("si es un sobre con remitente y sello, ponlo aquí").

### 4.2. Match básico

```python
comando = "salir"

match comando:
    case "ayuda":
        print("Mostrando ayuda")
    case "salir" | "exit":
        print("Saliendo del programa")
    case _:
        print("Comando desconocido")
```

Salida esperada:

```text
Saliendo del programa
```

**Explicación línea por línea:**
- `match comando:` indica qué valor se va a comparar.
- `case "ayuda":` coincide si el valor es exactamente `"ayuda"`.
- `case "salir" | "exit":` el operador `|` combina varios patrones (OR).
- `case _:` es el **comodín** (*wildcard*): coincide con cualquier cosa, como el `default` de un `switch`.

**Importante:** a diferencia de otros lenguajes, **no hay "caída"** entre casos: solo se ejecuta el primer patrón que coincide.

### 4.3. Capturar valores con patrones

Aquí está la potencia real. Los patrones pueden **descomponer** estructuras y **capturar** partes en variables:

```python
punto = (3, 0)

match punto:
    case (0, 0):
        print("Origen")
    case (x, 0):
        print(f"Sobre el eje X en {x}")
    case (0, y):
        print(f"Sobre el eje Y en {y}")
    case (x, y):
        print(f"Punto en ({x}, {y})")
```

Salida esperada:

```text
Sobre el eje X en 3
```

**Explicación línea por línea:**
- `case (0, 0):` coincide solo con el par exacto `(0, 0)`.
- `case (x, 0):` coincide con cualquier par cuya **segunda** coordenada sea 0, y **captura** la primera en `x`.
- El caso `(3, 0)` encaja aquí, así que `x` vale 3.

Este patrón de "reconocer la forma y extraer los datos" es lo que distingue `match` de un simple `switch`.

### 4.4. Guardas (condiciones adicionales)

Puedes añadir una condición con `if` dentro de un `case`:

```python
def clasificar(numero: int) -> str:
    match numero:
        case n if n < 0:
            return "negativo"
        case 0:
            return "cero"
        case n if n % 2 == 0:
            return "positivo par"
        case _:
            return "positivo impar"


print(clasificar(-5), clasificar(0), clasificar(4), clasificar(7))
```

Salida esperada:

```text
negativo cero positivo par positivo impar
```

**Explicación línea por línea:**
- `case n if n < 0:` captura cualquier número en `n` **y** comprueba la condición.
- El orden importa: `case 0` va después de la guarda negativa para que el 0 no se capture antes.
- Los patrones se evalúan de arriba abajo.

### 4.5. Patrones con diccionarios y clases

```python
evento = {"tipo": "clic", "x": 100, "y": 50}

match evento:
    case {"tipo": "clic", "x": x, "y": y}:
        print(f"Clic en ({x}, {y})")
    case {"tipo": "tecla", "valor": tecla}:
        print(f"Tecla {tecla}")
    case _:
        print("Evento desconocido")
```

Salida esperada:

```text
Clic en (100, 50)
```

**Explicación línea por línea:**
- `case {"tipo": "clic", "x": x, "y": y}:` coincide si el diccionario tiene esas claves con esos valores, y captura `x` e `y`.

### 4.6. ¿Cuándo usar match y cuándo if/elif?

| Usa `match` cuando... | Usa `if/elif` cuando... |
|---|---|
| Descompones estructuras (tuplas, dicts) | Comparas valores simples |
| Tienes muchos casos claros | Las condiciones son rangos o lógica compleja |
| Quieres código declarativo y legible | Pocas ramas |

No hay que usar `match` por moda. Para dos o tres casos simples, `if/elif` es perfectamente válido.

### 4.7. Errores comunes

**Error 1 — Usar `match` en versiones anteriores a 3.10.**

```text
SyntaxError: invalid syntax
```

*Solución:* actualiza Python o usa `if/elif`.

**Error 2 — Confundir captura con comparación.**

```python
match valor:
    case x:      # captura CUALQUIER valor en x
        print(x)
```

`case x:` no compara con una variable `x`; **captura** el valor en un nombre nuevo. Para comparar con una constante, usa su valor literal o una guarda.

**Error 3 — Abusar de `match`.** Si el código queda más enrevesado que con `if/elif`, no lo uses.

### 4.8. Checkpoint de comprensión

1. ¿Qué ventaja tiene `match` sobre un `switch` tradicional?
2. ¿Para qué sirve `case _:`?
3. ¿Cómo capturarías partes de una tupla con `match`?

### 4.9. Ejercicio propuesto

**Ejercicio 4.1.** Escribe una función que reciba un diccionario `{"tipo": "circulo", "radio": 5}` y calcule el área según el tipo (`circulo`, `cuadrado`, `rectangulo`) usando `match`.

**Pista:** los patrones pueden coincidir con claves de diccionario: `case {"tipo": "circulo", "radio": r}:`.

---

## 5. Iteradores

### 5.1. ¿Qué es iterar?

**Iterar** significa recorrer los elementos de una colección uno a uno. Ya lo haces constantemente con los bucles `for`. Pero, ¿qué ocurre por debajo? Entenderlo te permitirá crear tus propias colecciones.

Un **iterable** es cualquier objeto del que se pueden obtener elementos (listas, cadenas, diccionarios...). Un **iterador** es el objeto que **produce** esos elementos, uno a uno, y recuerda por dónde iba.

**Analogía:** un iterable es un libro; un iterador es un marcador que va señalando por qué página vas. Cada vez que lees la página y avanzas el marcador, "consumes" un elemento.

### 5.2. Obtener un iterador manualmente

```python
lista = [10, 20, 30]
it = iter(lista)

print(next(it))
print(next(it))
print(next(it))
```

Salida esperada:

```text
10
20
30
```

**Explicación línea por línea:**
- `iter(lista)` obtiene un iterador de la lista.
- `next(it)` devuelve el siguiente elemento y **avanza** el iterador.
- Si llamaras a `next(it)` una cuarta vez, obtendrías `StopIteration` (el iterador se agotó).

Un `for` no es más que azúcar sintáctico sobre este mecanismo: llama a `iter()` una vez y a `next()` repetidamente hasta que salta `StopIteration`.

### 5.3. El protocolo de iteración

Para que un objeto sea iterable, debe implementar `__iter__`; para ser iterador, también `__next__`. Esto te permite crear colecciones personalizadas:

```python
class Contador:
    """Iterador que cuenta de 1 a un límite."""

    def __init__(self, limite: int) -> None:
        self.limite = limite
        self.actual = 1

    def __iter__(self):
        return self

    def __next__(self) -> int:
        if self.actual > self.limite:
            raise StopIteration
        valor = self.actual
        self.actual += 1
        return valor


for n in Contador(3):
    print(n)
```

Salida esperada:

```text
1
2
3
```

**Explicación línea por línea:**
- `__iter__` devuelve el propio objeto (es su propio iterador).
- `__next__` devuelve el siguiente valor o lanza `StopIteration` cuando termina.
- El `for` usa automáticamente estos métodos.

### 5.4. Iterables perezosos (lazy)

Algunos iterables no guardan todos sus elementos en memoria; los generan al vuelo. `range`, `enumerate`, `zip`, `map` y `filter` funcionan así.

```python
r = range(1_000_000_000)   # mil millones de números...
print(next(iter(r)))        # ...pero no ocupan memoria proporcional
```

Salida esperada:

```text
0
```

**¿Por qué importa?** Porque puedes trabajar con secuencias enormes (o infinitas) sin agotar la memoria.

### 5.5. Errores comunes

**Error 1 — Consumir un iterador dos veces.**

```python
it = iter([1, 2, 3])
print(list(it))   # [1, 2, 3]
print(list(it))   # []  ¡ya se agotó!
```

*Solución:* si necesitas reutilizarlo, conviértelo a lista una vez.

**Error 2 — Esperar que `map`/`filter` devuelvan listas.**

En Python 3 devuelven **iteradores**. *Solución:* envuélvelos con `list()` si necesitas una lista.

**Error 3 — Iterar sobre algo que no es iterable.**

```python
for x in 5:
    ...
```

```text
TypeError: 'int' object is not iterable
```

*Solución:* comprueba que el objeto sea iterable (lista, rango, cadena...).

### 5.6. Checkpoint de comprensión

1. ¿Cuál es la diferencia entre un iterable y un iterador?
2. ¿Qué excepción se lanza cuando un iterador se agota?
3. ¿Por qué los iteradores ahorran memoria?

### 5.7. Ejercicio propuesto

**Ejercicio 5.1.** Implementa una clase `Pares` que itere sobre los números pares hasta un máximo.

**Pista:** implementa `__iter__` y `__next__`, y lanza `StopIteration` al terminar.

---

## 6. Generadores y yield

### 6.1. ¿Qué es un generador?

Un **generador** es una función que produce valores **de uno en uno** usando `yield`, en lugar de devolverlos todos de golpe con `return`. Es la forma más sencilla y "pythónica" de crear un iterador.

**Analogía:** una máquina expendedora entrega un producto cada vez que pulsas el botón, en lugar de vaciar toda la carga encima del mostrador. La función generadora "recuerda" dónde se quedó y continúa desde ahí la próxima vez.

### 6.2. Una función generadora

```python
def cuenta_hasta(n: int):
    """Genera números de 1 a n."""
    for i in range(1, n + 1):
        yield i


for numero in cuenta_hasta(3):
    print(numero)
```

Salida esperada:

```text
1
2
3
```

**Explicación línea por línea:**
- `yield i` **pausa** la función y entrega el valor `i` al llamante.
- En la siguiente petición, la función **se reanuda** justo después del `yield`.
- A diferencia de `return`, `yield` **no termina** la función: la suspende.

### 6.3. `return` vs `yield`: la diferencia esencial

```python
def con_return():
    return [1, 2, 3]      # construye TODA la lista y la devuelve

def con_yield():
    yield 1                # entrega uno, se pausa
    yield 2
    yield 3
```

Ambas producen 1, 2, 3, pero `con_return` **crea toda la lista en memoria** antes de devolverla, mientras que `con_yield` la produce **a demanda**. Para colecciones pequeñas da igual; para colecciones enormes, la diferencia es enorme.

### 6.4. Comparación de memoria

```python
import sys

def cuadrados_lista(n: int) -> list[int]:
    return [i ** 2 for i in range(n)]

def cuadrados_generador(n: int):
    for i in range(n):
        yield i ** 2

lista = cuadrados_lista(1000)
gen = cuadrados_generador(1000)

print(sys.getsizeof(lista))
print(sys.getsizeof(gen))
```

Salida esperada (los valores pueden variar):

```text
8856
104
```

**Explicación línea por línea:**
- `sys.getsizeof` devuelve el tamaño en bytes.
- La lista ocupa miles de bytes porque guarda **todos** los cuadrados.
- El generador ocupa apenas unos cientos de bytes, independientemente de `n`: solo guarda su estado.

### 6.5. Expresiones generadoras

Igual que existe la comprensión de listas con `[...]`, existe la **expresión generadora** con `(...)`:

```python
gen = (n ** 2 for n in range(5))
print(sum(gen))
```

Salida esperada:

```text
30
```

**Explicación línea por línea:**
- `(n ** 2 for n in range(5))` es una expresión generadora: no crea la lista, produce los valores uno a uno.
- `sum(gen)` los consume y los suma: 0+1+4+9+16 = 30.

**Diferencia clave:** `[n ** 2 for n in ...]` crea una **lista**; `(n ** 2 for n in ...)` crea un **generador**. La primera usa corchetes, la segunda paréntesis.

### 6.6. Generadores infinitos

Una de las grandes ventajas: puedes representar secuencias infinitas sin problemas, porque solo se calculan a demanda.

```python
def naturales():
    """Genera números naturales indefinidamente."""
    n = 0
    while True:
        yield n
        n += 1


gen = naturales()
for _ in range(5):
    print(next(gen), end=" ")
```

Salida esperada:

```text
0 1 2 3 4 
```

**Explicación línea por línea:**
- `while True` haría un bucle infinito con `return`, pero con `yield` es perfectamente válido: solo se produce un valor cuando se pide.
- Tomamos solo los primeros 5 con `next()`.

### 6.7. Delegar con `yield from`

`yield from` delega la producción a otro iterable:

```python
def encadenar(*iterables):
    for it in iterables:
        yield from it


print(list(encadenar([1, 2], [3, 4], [5])))
```

Salida esperada:

```text
[1, 2, 3, 4, 5]
```

**Explicación línea por línea:**
- `*iterables` recibe cualquier número de iterables.
- `yield from it` produce todos los elementos de `it`, uno a uno.

### 6.8. Errores comunes

**Error 1 — Confundir `return` con `yield`.**

Si usas `return` dentro de una función con `yield`, terminas el generador prematuramente.

**Error 2 — Reutilizar un generador agotado.**

```python
gen = (x for x in range(3))
print(list(gen))   # [0, 1, 2]
print(list(gen))   # []  ¡agotado!
```

*Solución:* crea un generador nuevo cada vez.

**Error 3 — Esperar ejecución inmediata.**

```python
def gen():
    print("Esto no se imprime aún")
    yield 1

g = gen()   # NO se ejecuta nada todavía
```

El cuerpo de una función generadora **no se ejecuta** hasta que se itera sobre ella.

### 6.9. Checkpoint de comprensión

1. ¿Qué diferencia hay entre `return` y `yield`?
2. ¿Por qué un generador usa menos memoria que una lista?
3. ¿Cómo representarías una secuencia infinita?

### 6.10. Ejercicio propuesto

**Ejercicio 6.1.** Escribe un generador `fibonacci()` infinito y toma los primeros 10 valores.

**Pista:** usa `a, b = b, a + b` dentro de un `while True`.

**Ejercicio 6.2.** Convierte la comprensión `[n ** 2 for n in range(1000000)]` en una expresión generadora y comprueba la diferencia de memoria.

**Pista:** usa paréntesis y `sys.getsizeof`.

---

## 7. Ejemplo integrador

Construiremos un **filtro de registros de ventas** usando generadores encadenados, que combina `for`, condicionales, generadores y `yield`.

```python
"""Procesamiento perezoso de registros de ventas."""

from typing import Iterator

VENTAS = [
    {"producto": "A", "unidades": 10, "precio": 5.0},
    {"producto": "B", "unidades": 0, "precio": 12.0},
    {"producto": "C", "unidades": 3, "precio": 7.5},
    {"producto": "D", "unidades": 8, "precio": 2.0},
]


def ventas_validas(registros: list[dict]) -> Iterator[dict]:
    """Genera solo los registros con unidades mayores que cero."""
    for registro in registros:
        if registro["unidades"] > 0:
            yield registro


def con_importe(registros: Iterator[dict]) -> Iterator[dict]:
    """Añade el importe calculado a cada registro."""
    for registro in registros:
        yield {**registro, "importe": registro["unidades"] * registro["precio"]}


def main() -> None:
    """Muestra los registros procesados y el total."""
    total = 0.0
    for registro in con_importe(ventas_validas(VENTAS)):
        print(f"{registro['producto']}: {registro['importe']:.2f} €")
        total += registro["importe"]
    print(f"TOTAL: {total:.2f} €")


if __name__ == "__main__":
    main()
```

Salida esperada:

```text
A: 50.00 €
C: 22.50 €
D: 16.00 €
TOTAL: 88.50 €
```

**Explicación línea por línea:**
- `ventas_validas` es un generador: descarta los registros con 0 unidades (el producto B) y produce los demás uno a uno.
- `con_importe` recibe un iterador y produce nuevos diccionarios con el campo `importe` añadido. `{**registro, "importe": ...}` copia el diccionario original y añade una clave.
- En `main`, el `for` encadena ambos generadores: los datos fluyen **de uno en uno**, sin construir listas intermedias.
- El total se acumula y se muestra al final.

**Conceptos integrados:** generadores, `yield`, encadenamiento de iteradores, desempaquetado de diccionarios (`{**registro, ...}`), bucles y f-strings.

---

## 8. Buenas prácticas

1. **Prefiere `for` sobre `while`** cuando iteres una secuencia.
2. **Usa `enumerate`** en lugar de `range(len(...))`.
3. **Usa `zip`** para recorrer secuencias en paralelo.
4. **Evita banderas booleanas** con `for/else` cuando buscas un elemento.
5. **Reserva `match`** para estructuras complejas; `if/elif` para casos simples.
6. **Usa generadores** para datos grandes o flujos infinitos.
7. **No modifiques** la colección que estás recorriendo.
8. **No uses `time.sleep` ni bucles ocupados** para esperar (usa herramientas específicas).

---

## 9. Recursos

- **Control de flujo oficial:** <https://docs.python.org/3/tutorial/controlflow.html>
- **PEP 634 (match):** <https://peps.python.org/pep-0634/>
- **Iteradores y generadores:** <https://docs.python.org/3/tutorial/classes.html#generators>
- **Libro:** *Fluent Python*, capítulos 14 y 17.

---

## 10. Mini resumen final

- `if/elif/else` ejecuta la primera rama verdadera; existe la expresión ternaria.
- `for` itera secuencias; `while` repite según una condición.
- `enumerate` y `zip` simplifican índices y paralelismo.
- `break`, `continue`, `pass` y `else` controlan bucles.
- `match-case` (3.10+) hace *pattern matching* estructural.
- Los **iteradores** implementan `__iter__`/`__next__`; los **generadores** usan `yield`.
- Los generadores ahorran memoria y permiten flujos infinitos.

---

### 🎯 Retos opcionales

1. **Reto 1:** Implementa la secuencia de Collatz con un generador.
2. **Reto 2:** Reescribe el ejemplo integrador usando `itertools.islice` para limitar a los 2 mayores importes.
3. **Reto 3:** Usa `match` para parsear comandos de una mini-CLI (`add`, `remove`, `list`).

**Anterior:** [02_sintaxis_tipos_datos.md](02_sintaxis_tipos_datos.md) · **Siguiente:** [04_funciones.md](04_funciones.md)
