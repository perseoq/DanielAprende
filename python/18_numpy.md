# 18 · NumPy

**Versión recomendada:** NumPy 2.0 · Python 3.12
**Requisito:** [17_pyside6.md](17_pyside6.md) (o fundamentos)
**Nivel:** Intermedio
**Duración estimada de estudio:** 10–14 horas

---

## Índice de contenidos

1. [Introducción a NumPy](#1-introducción-a-numpy)
2. [Instalación y configuración](#2-instalación-y-configuración)
3. [El objeto ndarray](#3-el-objeto-ndarray)
4. [Creación de arrays](#4-creación-de-arrays)
5. [Tipos de datos (dtype)](#5-tipos-de-datos-dtype)
6. [Indexación y slicing](#6-indexación-y-slicing)
7. [Indexación booleana y fancy indexing](#7-indexación-booleana-y-fancy-indexing)
8. [Operaciones aritméticas y broadcasting](#8-operaciones-aritméticas-y-broadcasting)
9. [Funciones universales (ufunc)](#9-funciones-universales-ufunc)
10. [Agregaciones y estadísticas](#10-agregaciones-y-estadísticas)
11. [Manipulación de formas](#11-manipulación-de-formas-reshape-transpose-ravel)
12. [Concatenación y división](#12-concatenación-y-división-de-arrays)
13. [Álgebra lineal](#13-álgebra-lineal-linalg)
14. [Números aleatorios](#14-números-aleatorios-random)
15. [FFT y procesamiento de señales](#15-fft-y-procesamiento-de-señales)
16. [Entrada/salida de archivos](#16-entradasalida-de-archivos)
17. [Memoria: vistas vs copias](#17-memoria-vistas-vs-copias)
18. [Vectorización y rendimiento](#18-vectorización-y-rendimiento)
19. [Integración con Pandas y otras librerías](#19-integración-con-pandas-y-otras-librerías)
20. [Buenas prácticas](#20-buenas-prácticas)
21. [Recursos](#21-recursos)
22. [Mini resumen final](#22-mini-resumen-final)

---

## 1. Introducción a NumPy

### 1.1. El problema: Python puro es lento para números

Python es un lenguaje excelente, pero **lento** para cálculos numéricos masivos. Si sumas un millón de números con un bucle `for`, Python interpreta cada operación una a una, con toda su sobrecarga. Para ciencia de datos e IA, esto es inaceptable.

**NumPy** (*Numerical Python*) resuelve esto: sus operaciones están escritas en **C** y trabajan sobre bloques de memoria contiguos, aplicando la operación a **todos** los elementos a la vez. Es decenas o cientos de veces más rápido que un bucle de Python.

**Analogía:** un bucle de Python es como contar monedas de una en una; NumPy es como pesar el montón entero de golpe con una balanza calibrada.

### 1.2. El ndarray

La estructura central de NumPy es el **ndarray** (*n-dimensional array*): un array multidimensional **homogéneo** (todos los elementos del mismo tipo) con operaciones vectorizadas.

NumPy es la **base** de casi toda la ciencia de datos en Python: Pandas, SciPy, scikit-learn, OpenCV y TensorFlow se apoyan en él.

### 1.3. Errores comunes

**Error 1 — Usar bucles de Python** donde hay operaciones vectorizadas.

**Error 2 — Confundir `np.array` con una lista.** Son tipos distintos con comportamientos distintos.

### 1.4. Checkpoint de comprensión

1. ¿Por qué NumPy es más rápido que un bucle de Python?
2. ¿Qué es un ndarray?

### 1.5. Ejercicio propuesto

**Ejercicio 1.1.** Suma 1 a cada elemento de una lista de un millón de números con un bucle y con NumPy, y compara tiempos.

**Pista:** usa `time.perf_counter`.

---

## 2. Instalación y configuración

### 2.1. Instalación

```bash
pip install numpy
```

### 2.2. Verificación

```python
import numpy as np

print(np.__version__)
```

Salida esperada:

```text
2.0.0
```

### 2.3. La convención `np`

Todo el mundo importa NumPy como `np`. Es una convención universal; úsala.

```python
import numpy as np   # alias estándar en la comunidad
```

### 2.4. Errores comunes

**Error 1 — Nombrar tu archivo `numpy.py`.**

*Síntoma:* `import numpy` importa tu archivo, no la librería. *Solución:* renómbralo.

### 2.5. Checkpoint de comprensión

1. ¿Por qué se usa el alias `np`?
2. ¿Qué problema crea un archivo llamado `numpy.py`?

### 2.6. Ejercicio propuesto

**Ejercicio 2.1.** Instala NumPy y comprueba la versión.

**Pista:** `np.__version__`.

---

## 3. El objeto ndarray

### 3.1. Los atributos clave

Un ndarray tiene varios atributos que conviene conocer:

- **shape:** las dimensiones (una tupla).
- **ndim:** el número de dimensiones.
- **dtype:** el tipo de los elementos.
- **size:** el número total de elementos.

### 3.2. Ejemplo

```python
import numpy as np

a = np.array([[1, 2, 3], [4, 5, 6]])
print(a.shape)
print(a.ndim)
print(a.dtype)
print(a.size)
```

Salida esperada:

```text
(2, 3)
2
int64
6
```

**Explicación línea por línea:**
- `a.shape` es `(2, 3)`: 2 filas, 3 columnas.
- `a.ndim` es 2: es bidimensional.
- `a.dtype` es `int64`: todos los elementos son enteros de 64 bits.
- `a.size` es 6: 2 × 3 elementos.

### 3.3. La diferencia con las listas

```python
lista = [1, 2, 3]
array = np.array([1, 2, 3])
print(lista * 2)
print(array * 2)
```

Salida esperada:

```text
[1, 2, 3, 1, 2, 3]
[2 4 6]
```

**Explicación línea por línea:**
- `lista * 2` **repite** la lista.
- `array * 2` **multiplica cada elemento** por 2. Esta es la **vectorización**: la operación se aplica a todos los elementos sin escribir un bucle.

### 3.4. Errores comunes

**Error 1 — Esperar que un array heterogéneo mantenga tipos.**

```python
np.array([1, "dos", 3.0])   # dtype=object, pierde eficiencia
```

**Error 2 — Confundir `shape` con `size`.**

`shape` son las dimensiones; `size`, el total de elementos.

### 3.5. Checkpoint de comprensión

1. ¿Qué significa `shape`? ¿Y `ndim`?
2. ¿Qué hace `array * 2` que no hace `lista * 2`?

### 3.6. Ejercicio propuesto

**Ejercicio 3.1.** Crea una matriz 3x4 y muestra shape, ndim y size.

**Pista:** `np.zeros((3, 4))`.

---

## 4. Creación de arrays

### 4.1. Funciones de creación

NumPy ofrece muchas formas de crear arrays:

```python
import numpy as np

print(np.zeros((2, 3)))
print(np.ones((2, 2)))
print(np.full((2, 2), 7))
print(np.eye(3))
print(np.arange(0, 10, 2))
print(np.linspace(0, 1, 5))
```

Salida esperada:

```text
[[0. 0. 0.]
 [0. 0. 0.]]
[[1. 1.]
 [1. 1.]]
[[7 7]
 [7 7]]
[[1. 0. 0.]
 [0. 1. 0.]
 [0. 0. 1.]]
[0 2 4 6 8]
[0.   0.25 0.5  0.75 1.  ]
```

**Explicación línea por línea:**
- `np.zeros((2, 3))` crea un array de ceros con esa forma.
- `np.ones((2, 2))` de unos.
- `np.full((2, 2), 7)` lleno de un valor.
- `np.eye(3)` es la matriz identidad.
- `np.arange(0, 10, 2)` de 0 a 9 con paso 2.
- `np.linspace(0, 1, 5)` 5 valores **equiespaciados** entre 0 y 1.

### 4.2. Arrays aleatorios

```python
rng = np.random.default_rng(seed=42)
print(rng.random((2, 2)))
print(rng.integers(0, 10, size=5))
```

**Explicación línea por línea:**
- `default_rng(seed=42)` crea un generador con semilla (reproducible).
- `.random(...)` genera decimales en [0, 1).
- `.integers(0, 10, size=5)` genera 5 enteros entre 0 y 9.

### 4.3. Desde listas y rangos

```python
a = np.array([1, 2, 3])
b = np.array([[1, 2], [3, 4]], dtype=float)
c = np.arange(12).reshape(3, 4)
```

### 4.4. Errores comunes

**Error 1 — `np.arange` con flotantes** por errores de precisión. Usa `np.linspace`.

**Error 2 — `np.random.seed` global** en código nuevo. Usa `default_rng`.

### 4.5. Checkpoint de comprensión

1. ¿Qué diferencia hay entre `arange` y `linspace`?
2. ¿Por qué usar `default_rng` en lugar de `np.random.seed`?

### 4.6. Ejercicio propuesto

**Ejercicio 4.1.** Crea una matriz identidad 5x5 y un array de 10 ceros.

**Pista:** `np.eye(5)` y `np.zeros(10)`.

---

## 5. Tipos de datos (dtype)

### 5.1. Elegir el tipo correcto

El **dtype** define el tipo de los elementos. Elegirlo bien ahorra memoria y acelera los cálculos:

| dtype | Descripción |
|---|---|
| `int8/16/32/64` | Enteros con signo |
| `uint8/16/32/64` | Enteros sin signo |
| `float16/32/64` | Coma flotante |
| `complex64/128` | Complejos |
| `bool` | Booleanos |
| `object` | Objetos Python |

### 5.2. Conversión de tipos

```python
import numpy as np

a = np.array([1, 2, 3], dtype=np.int32)
b = a.astype(np.float64)
print(a.dtype, b.dtype)
print(a.nbytes, b.nbytes)
```

Salida esperada:

```text
int32 float64
12 24
```

**Explicación línea por línea:**
- `dtype=np.int32` crea enteros de 32 bits.
- `.astype(np.float64)` convierte a decimales de 64 bits.
- `.nbytes` da el tamaño en bytes: 3 elementos × 4 bytes = 12; × 8 = 24.

### 5.3. El peligro del desbordamiento

```python
a = np.array([250], dtype=np.uint8)
print(a + 10)
```

Salida esperada:

```text
[4]
```

**Explicación línea por línea:**
- `uint8` solo puede representar de 0 a 255.
- `250 + 10 = 260`, que se desborda y "da la vuelta" a 4. Es un error silencioso y peligroso.

### 5.4. Errores comunes

**Error 1 — `uint8` en restas** que se vuelven negativas.

**Error 2 — `float64` cuando `float32` basta** (más memoria).

### 5.5. Checkpoint de comprensión

1. ¿Por qué el dtype afecta al rendimiento?
2. ¿Qué es el desbordamiento?

### 5.6. Ejercicio propuesto

**Ejercicio 5.1.** Convierte un array de `int64` a `float32` y compara el uso de memoria.

**Pista:** `arr.astype(np.float32).nbytes`.

---

## 6. Indexación y slicing

### 6.1. Acceder a elementos

La indexación funciona como en listas, pero por **dimensión**, y el slicing devuelve **vistas** (no copias):

```python
import numpy as np

a = np.arange(10)
print(a[2])
print(a[2:5])
print(a[::-1])

m = np.array([[1, 2, 3], [4, 5, 6], [7, 8, 9]])
print(m[0, 1])
print(m[:, 1])
print(m[1, :])
print(m[:2, :2])
```

Salida esperada:

```text
2
[2 3 4]
[9 8 7 6 5 4 3 2 1 0]
2
[2 5 8]
[4 5 6]
[[1 2]
 [4 5]]
```

**Explicación línea por línea:**
- `a[2]` accede al tercer elemento (índices desde 0).
- `a[2:5]` un rango (fin exclusivo).
- `a[::-1]` invierte.
- `m[0, 1]` accede a la fila 0, columna 1 (¡un solo par de corchetes!).
- `m[:, 1]` toda la columna 1.
- `m[:2, :2]` el bloque superior izquierdo.

### 6.2. Slicing devuelve vistas

```python
a = np.arange(10)
b = a[2:5]
b[0] = 99
print(a)
```

Salida esperada:

```text
[ 0  1 99  3  4  5  6  7  8  9]
```

**Explicación línea por línea:**
- `b = a[2:5]` **no copia**: `b` es una **vista** del mismo bloque de memoria.
- Modificar `b` modifica `a`. Esto es eficiente (no duplica datos) pero peligroso si no lo sabes.

### 6.3. Errores comunes

**Error 1 — Confundir slicing con copia.**

*Solución:* usa `.copy()` si necesitas una copia.

**Error 2 — Confundir `m[0, 1]` con `m[0][1]`.**

El primero es más eficiente y claro; el segundo funciona pero crea un array intermedio.

### 6.4. Checkpoint de comprensión

1. ¿Qué diferencia hay entre `a[2:5]` y una copia?
2. ¿Cómo accedes al elemento de la fila 1, columna 2 de una matriz?

### 6.5. Ejercicio propuesto

**Ejercicio 6.1.** Extrae la segunda columna de una matriz 4x4.

**Pista:** `m[:, 1]`.

---

## 7. Indexación booleana y fancy indexing

### 7.1. Filtrar con máscaras booleanas

La **indexación booleana** selecciona elementos según una condición:

```python
import numpy as np

a = np.array([10, 20, 30, 40, 50])
print(a[a > 25])
```

Salida esperada:

```text
[30 40 50]
```

**Explicación línea por línea:**
- `a > 25` crea una **máscara** booleana: `[False, False, True, True, True]`.
- `a[mascara]` devuelve solo los elementos donde la máscara es `True`.

### 7.2. Fancy indexing

El **fancy indexing** selecciona con una lista de índices:

```python
print(a[[0, 2, 4]])
```

Salida esperada:

```text
[10 30 50]
```

**Explicación línea por línea:**
- `a[[0, 2, 4]]` devuelve los elementos en esas posiciones.
- A diferencia del slicing, **devuelve una copia**, no una vista.

### 7.3. Combinar condiciones

```python
print(a[(a > 15) & (a < 45)])
```

Salida esperada:

```text
[20 30 40]
```

**Explicación línea por línea:**
- Se usa `&` (AND) y `|` (OR), **no** `and`/`or`.
- Los paréntesis son obligatorios por precedencia.

### 7.4. Asignación con máscara

```python
a[a > 30] = 0
print(a)
```

Salida esperada:

```text
[10 20 30  0  0]
```

**Explicación línea por línea:**
- Se pueden asignar valores a todos los elementos que cumplan la condición.

### 7.5. Errores comunes

**Error 1 — Usar `and`/`or`** en lugar de `&`/`|`.

**Error 2 — Máscara de distinta forma** que el array.

### 7.6. Checkpoint de comprensión

1. ¿Qué devuelve `a > 25`?
2. ¿Por qué usar `&` en lugar de `and`?

### 7.7. Ejercicio propuesto

**Ejercicio 7.1.** Pon a cero todos los valores negativos de un array.

**Pista:** `arr[arr < 0] = 0`.

---

## 8. Operaciones aritméticas y broadcasting

### 8.1. Operaciones elemento a elemento

Las operaciones aritméticas se aplican **elemento a elemento**:

```python
import numpy as np

a = np.array([1, 2, 3])
b = np.array([10, 20, 30])
print(a + b)
print(a * 2)
print(a ** 2)
```

Salida esperada:

```text
[11 22 33]
[2 4 6]
[1 4 9]
```

### 8.2. Broadcasting: formas distintas

El **broadcasting** permite operar arrays de formas diferentes siguiendo reglas:

```python
m = np.array([[1, 2, 3], [4, 5, 6]])
v = np.array([10, 20, 30])
print(m + v)
```

Salida esperada:

```text
[[11 22 33]
 [14 25 36]]
```

**Explicación línea por línea:**
- `m` tiene forma `(2, 3)`; `v` tiene forma `(3,)`.
- El vector `v` se "estira" (broadcast) a `(2, 3)`: se suma a cada fila.
- No se copia `v`: NumPy lo hace de forma eficiente.

### 8.3. Las reglas del broadcasting

1. Se alinean las formas por la **derecha**.
2. Las dimensiones de tamaño 1 se **estiran**.
3. Si no coinciden y ninguna es 1, **error**.

```python
# (3, 4) + (4,)   ✅ -> (3, 4)
# (3, 4) + (3,)   ❌ -> error
# (3, 1) + (1, 4) ✅ -> (3, 4)
```

### 8.4. Errores comunes

**Error 1 — Formas incompatibles.**

```text
ValueError: operands could not be broadcast together with shapes (3,4) (3,)
```

**Error 2 — Esperar copia** al operar (se crean arrays nuevos).

### 8.5. Checkpoint de comprensión

1. ¿Qué es el broadcasting?
2. ¿Cuándo fallan dos formas?

### 8.6. Ejercicio propuesto

**Ejercicio 8.1.** Normaliza una matriz restando la media de cada columna.

**Pista:** usa `m - m.mean(axis=0)`.

---

## 9. Funciones universales (ufunc)

### 9.1. Matemáticas vectorizadas

Las **ufunc** aplican funciones matemáticas elemento a elemento, de forma vectorizada:

```python
import numpy as np

a = np.array([0, np.pi / 2, np.pi])
print(np.sin(a))
print(np.exp(np.array([0, 1, 2])))
print(np.sqrt(np.array([4, 9, 16])))
print(np.log(np.array([1, np.e])))
```

Salida esperada:

```text
[0.0000000e+00 1.0000000e+00 1.2246468e-16]
[1.         2.71828183 7.3890561 ]
[4. 3. 2.]
[0. 1.]
```

**Explicación línea por línea:**
- `np.sin(a)` aplica el seno a cada elemento.
- `np.exp(...)` la exponencial; `np.sqrt(...)` la raíz; `np.log(...)` el logaritmo.
- El valor `1.22e-16` es prácticamente cero (error de precisión del seno de π).

### 9.2. Operaciones entre arrays

```python
print(np.add(a, a))
print(np.maximum(np.array([1, 5]), np.array([3, 2])))
print(np.clip(np.array([-5, 0, 5]), 0, 3))
```

Salida esperada:

```text
[0. 3.14159265 6.28318531]
[3 5]
[0 0 3]
```

**Explicación línea por línea:**
- `np.maximum` toma el mayor de cada par.
- `np.clip(arr, 0, 3)` limita los valores al rango [0, 3].

### 9.3. Errores comunes

**Error 1 — `np.sqrt` de negativos** → `nan` con warning.

**Error 2 — Ignorar warnings de dominio** (log de 0, división por cero...).

### 9.4. Checkpoint de comprensión

1. ¿Qué es una ufunc?
2. ¿Qué hace `np.clip`?

### 9.5. Ejercicio propuesto

**Ejercicio 9.1.** Calcula el logaritmo natural de un array y maneja los negativos con `np.where`.

**Pista:** `np.where(a > 0, np.log(a), 0)`.

---

## 10. Agregaciones y estadísticas

### 10.1. Reducciones

NumPy ofrece `sum`, `mean`, `std`, `min`, `max`, `argmin`, `argmax`, `median`, `percentile`:

```python
import numpy as np

a = np.array([[1, 2, 3], [4, 5, 6]])
print(a.sum())
print(a.mean())
print(a.sum(axis=0))
print(a.sum(axis=1))
print(a.max(axis=1))
print(a.argmax())
```

Salida esperada:

```text
21
3.5
[5 7 9]
[ 6 15]
[3 6]
5
```

**Explicación línea por línea:**
- `a.sum()` suma **todo**.
- `a.mean()` la media global.
- `a.sum(axis=0)` suma **colapsando las filas** (resultado por columna).
- `a.sum(axis=1)` suma **colapsando las columnas** (resultado por fila).
- `a.argmax()` devuelve el **índice** del máximo (en el array aplanado).

### 10.2. El significado de `axis`

Esta es una de las confusiones más comunes. La regla:

- `axis=0`: **elimina** la dimensión 0 (las filas). Resultado: un valor por columna.
- `axis=1`: **elimina** la dimensión 1 (las columnas). Resultado: un valor por fila.

**Analogía:** `axis` es la dimensión que **aplastas**.

### 10.3. Percentiles y estadísticas

```python
print(np.median(a))
print(np.percentile(a, 50))
print(np.std(a), np.var(a))
```

**Explicación línea por línea:**
- `np.median` es el percentil 50.
- `np.std` es la desviación estándar; `np.var`, la varianza.

### 10.4. Errores comunes

**Error 1 — Confundir el eje** en `axis`.

**Error 2 — `argmax` sobre matriz plana** si no especificas `axis`.

### 10.5. Checkpoint de comprensión

1. ¿Qué hace `axis=0`? ¿Y `axis=1`?
2. ¿Qué devuelve `argmax`?

### 10.6. Ejercicio propuesto

**Ejercicio 10.1.** Calcula la media por columna y el máximo por fila.

**Pista:** `axis=0` y `axis=1`.

---

## 11. Manipulación de formas (reshape, transpose, ravel)

### 11.1. Cambiar la forma

```python
import numpy as np

a = np.arange(12)
print(a.reshape(3, 4))
print(a.reshape(3, 4).T.shape)
print(a.reshape(3, 4).ravel())
print(a.reshape(2, -1))
```

Salida esperada:

```text
[[ 0  1  2  3]
 [ 4  5  6  7]
 [ 8  9 10 11]]
(4, 3)
[ 0  1  2  3  4  5  6  7  8  9 10 11]
[[ 0  1  2  3  4  5]
 [ 6  7  8  9 10 11]]
```

**Explicación línea por línea:**
- `.reshape(3, 4)` reorganiza en 3 filas y 4 columnas.
- `.T` **transpone** (intercambia filas y columnas).
- `.ravel()` aplana a una dimensión.
- `.reshape(2, -1)` usa `-1` para que NumPy calcule la dimensión que falta.

### 11.2. Añadir y quitar dimensiones

```python
a = np.array([1, 2, 3])
print(a[:, np.newaxis].shape)
print(a[np.newaxis, :].shape)
print(a.reshape(1, -1).squeeze().shape)
```

Salida esperada:

```text
(3, 1)
(1, 3)
(3,)
```

**Explicación línea por línea:**
- `np.newaxis` añade una dimensión.
- `.squeeze()` elimina dimensiones de tamaño 1.

### 11.3. Errores comunes

**Error 1 — `reshape` con tamaño incompatible.**

```text
ValueError: cannot reshape array of size 12 into shape (5,)
```

**Error 2 — `ravel` sobre datos no contiguos** puede copiar.

### 11.4. Checkpoint de comprensión

1. ¿Qué hace `reshape`?
2. ¿Para qué sirve `-1` en `reshape`?

### 11.5. Ejercicio propuesto

**Ejercicio 11.1.** Convierte un array de 24 elementos en una matriz 2x3x4.

**Pista:** `reshape(2, 3, 4)`.

---

## 12. Concatenación y división de arrays

### 12.1. Unir arrays

```python
import numpy as np

a = np.array([[1, 2], [3, 4]])
b = np.array([[5, 6], [7, 8]])

print(np.concatenate([a, b], axis=0))
print(np.concatenate([a, b], axis=1))
print(np.vstack([a, b]))
print(np.hstack([a, b]))
```

Salida esperada:

```text
[[1 2]
 [3 4]
 [5 6]
 [7 8]]
[[1 2 5 6]
 [3 4 7 8]]
[[1 2]
 [3 4]
 [5 6]
 [7 8]]
[[1 2 5 6]
 [3 4 7 8]]
```

**Explicación línea por línea:**
- `concatenate(..., axis=0)` apila **verticalmente**.
- `concatenate(..., axis=1)` apila **horizontalmente**.
- `vstack` y `hstack` son atajos.

### 12.2. Dividir arrays

```python
a = np.arange(6)
print(np.split(a, 3))
print(np.split(a, [2, 4]))
```

Salida esperada:

```text
[array([0, 1]), array([2, 3]), array([4, 5])]
[array([0, 1]), array([2, 3]), array([4, 5])]
```

**Explicación línea por línea:**
- `np.split(a, 3)` divide en 3 partes iguales.
- `np.split(a, [2, 4])` divide en los índices 2 y 4.

### 12.3. Errores comunes

**Error 1 — Ejes incompatibles** al concatenar.

**Error 2 — `split` con divisiones no exactas** → error.

### 12.4. Checkpoint de comprensión

1. ¿Qué diferencia hay entre `vstack` y `hstack`?
2. ¿Cómo divides un array en puntos concretos?

### 12.5. Ejercicio propuesto

**Ejercicio 12.1.** Une dos matrices por columnas y luego divídelas de nuevo.

**Pista:** `hstack` y `hsplit`.

---

## 13. Álgebra lineal (linalg)

### 13.1. Operaciones matriciales

`np.linalg` ofrece producto matricial, inversa, determinante, autovalores y más:

```python
import numpy as np

a = np.array([[1, 2], [3, 4]])
b = np.array([[5, 6], [7, 8]])

print(a @ b)                 # producto matricial
print(np.linalg.det(a))      # determinante
print(np.linalg.inv(a))      # inversa
```

Salida esperada:

```text
[[19 22]
 [43 50]]
-2.0000000000000004
[[-2.   1. ]
 [ 1.5 -0.5]]
```

**Explicación línea por línea:**
- `a @ b` es el **producto matricial** (¡no confundir con `a * b`, que es elemento a elemento!).
- `np.linalg.det(a)` el determinante.
- `np.linalg.inv(a)` la inversa.

### 13.2. Resolver sistemas de ecuaciones

```python
a = np.array([[3, 1], [1, 2]])
b = np.array([9, 8])
x = np.linalg.solve(a, b)
print(x)
```

Salida esperada:

```text
[2. 3.]
```

**Explicación línea por línea:**
- `np.linalg.solve(A, b)` resuelve `Ax = b`. Mucho más eficiente y estable que invertir la matriz.

### 13.3. Autovalores

```python
valores, vectores = np.linalg.eig(np.array([[2, 0], [0, 3]]))
print(valores)
```

Salida esperada:

```text
[2. 3.]
```

### 13.4. Errores comunes

**Error 1 — Usar `*` en lugar de `@`** para producto matricial.

**Error 2 — Invertir matrices singulares** → `LinAlgError`.

### 13.5. Checkpoint de comprensión

1. ¿Qué diferencia hay entre `a * b` y `a @ b`?
2. ¿Cómo resuelves `Ax = b`?

### 13.6. Ejercicio propuesto

**Ejercicio 13.1.** Resuelve el sistema `2x + y = 5`, `x + 3y = 10`.

**Pista:** `np.linalg.solve`.

---

## 14. Números aleatorios (random)

### 14.1. El generador moderno

El **Generator** (`default_rng`) reemplaza al antiguo `np.random.*` global. Es reproducible con `seed`:

```python
import numpy as np

rng = np.random.default_rng(seed=42)

print(rng.random(3))                      # [0, 1)
print(rng.integers(1, 7, size=5))         # dados
print(rng.normal(loc=0, scale=1, size=3)) # normal
print(rng.choice(["a", "b", "c"], size=4))
```

**Explicación línea por línea:**
- `rng.random(3)` genera 3 decimales en [0, 1).
- `rng.integers(1, 7, size=5)` simula 5 tiradas de dado (1 a 6).
- `rng.normal(...)` genera una distribución normal.
- `rng.choice(...)` elige elementos al azar.

### 14.2. Mezclar y muestrear

```python
a = np.arange(10)
rng.shuffle(a)
print(a)
print(rng.choice(a, size=3, replace=False))
```

**Explicación línea por línea:**
- `rng.shuffle(a)` mezcla **in-place**.
- `replace=False` muestrea **sin reemplazo** (sin repetir).

### 14.3. Errores comunes

**Error 1 — No fijar `seed`** y obtener resultados no reproducibles.

**Error 2 — `np.random.seed`** en código nuevo.

### 14.4. Checkpoint de comprensión

1. ¿Por qué fijar la semilla?
2. ¿Qué hace `replace=False`?

### 14.5. Ejercicio propuesto

**Ejercicio 14.1.** Simula 1000 lanzamientos de un dado y calcula la frecuencia de cada cara.

**Pista:** `np.bincount`.

---

## 15. FFT y procesamiento de señales

### 15.1. La transformada de Fourier

`np.fft` calcula la **Transformada Rápida de Fourier**, esencial en señales y audio: descompone una señal en sus frecuencias.

```python
import numpy as np

t = np.linspace(0, 1, 500, endpoint=False)
senal = np.sin(2 * np.pi * 5 * t) + 0.5 * np.sin(2 * np.pi * 20 * t)

espectro = np.fft.fft(senal)
frecuencias = np.fft.fftfreq(len(t), d=t[1] - t[0])

magnitud = np.abs(espectro)[: len(t) // 2]
print(frecuencias[np.argmax(magnitud)])
```

Salida esperada:

```text
5.0
```

**Explicación línea por línea:**
- Se construye una señal con dos frecuencias: 5 Hz y 20 Hz.
- `np.fft.fft(senal)` calcula el espectro.
- `np.fft.fftfreq(...)` genera las frecuencias correspondientes.
- `np.argmax(magnitud)` encuentra la frecuencia dominante: 5 Hz.

### 15.2. Filtrado simple

```python
# Eliminar altas frecuencias
espectro[100:] = 0
filtrada = np.fft.ifft(espectro).real
```

### 15.3. Errores comunes

**Error 1 — No normalizar** la frecuencia con `fftfreq`.

**Error 2 — Usar `ifft` sin tomar la parte real.**

### 15.4. Checkpoint de comprensión

1. ¿Para qué sirve la FFT?
2. ¿Qué devuelve `fftfreq`?

### 15.5. Ejercicio propuesto

**Ejercicio 15.1.** Genera una señal de 3 Hz y detecta su frecuencia con FFT.

**Pista:** usa `argmax` sobre la magnitud.

---

## 16. Entrada/salida de archivos

### 16.1. Formatos binarios y de texto

`np.save`/`np.load` guardan arrays en formato binario `.npy`; `np.savez` para varios; `np.savetxt`/`np.loadtxt` para texto:

```python
import numpy as np

a = np.arange(10)
np.save("datos.npy", a)
recuperado = np.load("datos.npy")
print(recuperado)

np.savetxt("datos.txt", a)
leido = np.loadtxt("datos.txt")
print(leido)
```

**Explicación línea por línea:**
- `np.save(...)` guarda un array en `.npy` (binario, rápido, preserva dtype).
- `np.load(...)` lo recupera.
- `np.savetxt`/`np.loadtxt` usan texto (legible pero más lento).

### 16.2. Varios arrays

```python
np.savez("varios.npz", a=a, b=np.ones(3))
data = np.load("varios.npz")
print(data["a"], data["b"])
```

**Explicación línea por línea:**
- `np.savez(...)` guarda varios arrays con nombre.
- Se recuperan como un diccionario.

### 16.3. Errores comunes

**Error 1 — `savetxt` con arrays multidimensionales** requiere forma 2D.

**Error 2 — Confundir `.npy` (un array) con `.npz` (varios).**

### 16.4. Checkpoint de comprensión

1. ¿Qué diferencia hay entre `.npy` y `.npz`?
2. ¿Cuándo usarías `savetxt`?

### 16.5. Ejercicio propuesto

**Ejercicio 16.1.** Guarda una matriz en `.npy` y recupérala.

**Pista:** `np.save` / `np.load`.

---

## 17. Memoria: vistas vs copias

### 17.1. La distinción crucial

- El **slicing básico** devuelve **vistas** (comparten memoria).
- El **fancy indexing** y las **máscaras** devuelven **copias**.

```python
import numpy as np

a = np.arange(10)
vista = a[2:5]
vista[0] = 99
print(a)

copia = a[[2, 3, 4]]
copia[0] = -1
print(a)
```

Salida esperada:

```text
[ 0  1 99  3  4  5  6  7  8  9]
[ 0  1 99  3  4  5  6  7  8  9]
```

**Explicación línea por línea:**
- `vista = a[2:5]` es una vista: modificar `vista` modifica `a`.
- `copia = a[[2, 3, 4]]` es una copia: modificar `copia` **no** afecta a `a`.

### 17.2. Comprobar si comparten memoria

```python
print(np.shares_memory(a, a[2:5]))
print(np.shares_memory(a, a[[2, 3]]))
```

Salida esperada:

```text
True
False
```

### 17.3. Errores comunes

**Error 1 — Modificar sin querer el original** a través de una vista.

**Error 2 — Creer que toda operación copia.**

### 17.4. Checkpoint de comprensión

1. ¿Qué devuelve el slicing, una vista o una copia?
2. ¿Cómo compruebas si dos arrays comparten memoria?

### 17.5. Ejercicio propuesto

**Ejercicio 17.1.** Demuestra que `reshape` suele devolver una vista y `.copy()` no.

**Pista:** `np.shares_memory`.

---

## 18. Vectorización y rendimiento

### 18.1. La regla de oro

**Evita los bucles de Python.** Expresa la operación como un array. Es la clave del rendimiento.

### 18.2. Comparación

```python
import numpy as np
import time

n = 1_000_000
a = np.arange(n)

inicio = time.perf_counter()
resultado_bucle = [x * 2 for x in a]
print("Bucle:", time.perf_counter() - inicio)

inicio = time.perf_counter()
resultado_vec = a * 2
print("Vectorizado:", time.perf_counter() - inicio)
```

Salida esperada (los tiempos varían):

```text
Bucle: 0.08
Vectorizado: 0.002
```

**Explicación línea por línea:**
- El bucle de Python tarda ~40 veces más.
- La versión vectorizada ejecuta la operación en C sobre todo el array.

### 18.3. Cuándo el bucle es inevitable

- Cuando hay dependencias entre iteraciones.
- Con lógica condicional muy compleja.

Alternativas: `np.where`, `np.select`, `numba`.

### 18.4. Errores comunes

**Error 1 — Iterar con `for`** para operaciones aritméticas.

**Error 2 — Usar `np.vectorize`** creyendo que acelera. Es un bucle disfrazado.

### 18.5. Checkpoint de comprensión

1. ¿Por qué la vectorización es más rápida?
2. ¿Por qué `np.vectorize` no acelera?

### 18.6. Ejercicio propuesto

**Ejercicio 18.1.** Vectoriza el cálculo de la distancia euclídea entre dos arrays.

**Pista:** `np.sqrt(((a - b) ** 2).sum())`.

---

## 19. Integración con Pandas y otras librerías

### 19.1. La base de todo

Pandas se construye sobre NumPy; scikit-learn, OpenCV y SciPy consumen arrays.

```python
import numpy as np
import pandas as pd

df = pd.DataFrame({"a": np.arange(5), "b": np.random.default_rng(0).random(5)})
print(df.to_numpy())
print(df["a"].values)
```

**Explicación línea por línea:**
- `df.to_numpy()` convierte el DataFrame a array.
- `df["a"].values` da la columna como array.

### 19.2. Con scikit-learn

```python
from sklearn.linear_model import LinearRegression

X = np.array([[1], [2], [3], [4]])
y = np.array([2, 4, 6, 8])
modelo = LinearRegression().fit(X, y)
print(modelo.predict([[5]]))
```

Salida esperada:

```text
[10.]
```

### 19.3. Errores comunes

**Error 1 — Conversiones innecesarias** entre listas y arrays.

**Error 2 — Copiar datos entre Pandas y NumPy** sin necesidad.

### 19.4. Checkpoint de comprensión

1. ¿Cómo conviertes un DataFrame a array?
2. ¿Por qué las librerías científicas usan arrays?

### 19.5. Ejercicio propuesto

**Ejercicio 19.1.** Convierte un DataFrame a array, opera y vuelve a DataFrame.

**Pista:** `df.to_numpy()` y `pd.DataFrame(...)`.

---

## 20. Buenas prácticas

1. **Vectoriza** siempre que puedas.
2. **Elige el `dtype` adecuado** para ahorrar memoria.
3. **Usa `default_rng`** con semilla para reproducibilidad.
4. **Recuerda `axis`** en las agregaciones.
5. **Distingue vistas de copias.**
6. **No nombres archivos como `numpy.py`.**
7. **Evita `np.vectorize`** para rendimiento.
8. **Usa `@` para producto matricial**, no `*`.
9. **Cuidado con el desbordamiento** de tipos pequeños.
10. **Usa `linspace` para rangos decimales.**

---

## 21. Recursos

- **Documentación oficial:** <https://numpy.org/doc/stable/>
- **NumPy for Absolute Beginners:** <https://numpy.org/doc/stable/user/absolute_beginners.html>
- **Guía de broadcasting:** <https://numpy.org/doc/stable/user/basics.broadcasting.html>
- **Libro:** *Python Data Science Handbook*, Jake VanderPlas.

---

## 22. Mini resumen final

- **NumPy** aporta el **ndarray** y operaciones vectorizadas en C.
- Los arrays tienen **shape**, **ndim**, **dtype** y **size**.
- Se crean con `zeros`, `ones`, `arange`, `linspace`, `random`.
- **Indexación/slicing**, **máscaras** y **fancy indexing** seleccionan datos.
- El **broadcasting** combina formas compatibles.
- Las **ufunc** y **agregaciones** (con `axis`) resumen datos.
- `linalg`, `fft` y `random` cubren álgebra, señales y aleatoriedad.
- El **slicing devuelve vistas**; las máscaras, copias.
- La **vectorización** es clave para el rendimiento.

---

### 🎯 Retos opcionales

1. **Reto 1:** Normaliza (media 0, desviación 1) una matriz por columnas.
2. **Reto 2:** Implementa un filtro de media móvil con slicing y `cumsum`.
3. **Reto 3:** Detecta la frecuencia dominante de una señal de audio sintética.

**Anterior:** [17_pyside6.md](17_pyside6.md) · **Siguiente:** [19_pandas.md](19_pandas.md)
