# 08 · Temas Avanzados de Python

**Versión recomendada:** Python 3.12
**Requisito:** [07_modulos_paquetes.md](07_modulos_paquetes.md)
**Nivel:** Intermedio → Avanzado
**Duración estimada de estudio:** 10–14 horas

---

## Índice de contenidos

1. [Type hints y typing](#1-type-hints-y-typing)
2. [Async/await y asyncio](#2-asyncawait-y-asyncio)
3. [Concurrencia: threading y multiprocessing](#3-concurrencia-threading-y-multiprocessing)
4. [El GIL y sus implicaciones](#4-el-gil-y-sus-implicaciones)
5. [Expresiones regulares (re)](#5-expresiones-regulares-re)
6. [Serialización: JSON, pickle, YAML](#6-serialización-json-pickle-yaml)
7. [Archivos y pathlib](#7-archivos-y-pathlib)
8. [Testing con unittest, pytest y mock](#8-testing-con-unittest-pytest-y-mock)
9. [Cython y extensiones en C](#9-cython-y-extensiones-en-c)
10. [Metaprogramación](#10-metaprogramación)
11. [Buenas prácticas](#11-buenas-prácticas)
12. [Recursos](#12-recursos)
13. [Mini resumen final](#13-mini-resumen-final)

---

## 1. Type hints y typing

### 1.1. ¿Para qué anotar tipos si Python no los exige?

Python tiene **tipado dinámico**: no declaras tipos y no los comprueba al ejecutar. Entonces, ¿por qué anotarlos? Porque las anotaciones son **documentación verificable**:

- **Comunican intención:** quien lee `def f(n: int) -> str` sabe qué esperar.
- **Permiten verificación estática:** herramientas como `mypy` o `pyright` detectan errores **antes** de ejecutar.
- **Mejoran el autocompletado:** los editores ofrecen mejores sugerencias.
- **Facilitan el refactor:** cambiar una firma avisa de todos los usos incompatibles.

**Analogía:** anotar tipos es como poner etiquetas a los cables de una instalación eléctrica. El sistema funciona sin etiquetas, pero con ellas cualquiera (incluido tú en el futuro) sabe qué conecta con qué.

### 1.2. Anotaciones básicas

```python
def sumar(a: int, b: int) -> int:
    return a + b


nombre: str = "Ana"
precios: list[float] = [1.5, 2.0]
mapa: dict[str, int] = {"a": 1}
coordenada: tuple[int, int] = (3, 4)
opcional: int | None = None
```

**Explicación línea por línea:**
- `a: int, b: int` anota los parámetros; `-> int` el retorno.
- `list[float]` indica una lista de decimales (sintaxis moderna, 3.9+).
- `dict[str, int]` un diccionario de texto a entero.
- `tuple[int, int]` una tupla de dos enteros.
- `int | None` un entero **o** `None` (sintaxis moderna, 3.10+).

**Importante:** estas anotaciones **no se aplican** en tiempo de ejecución. Puedes pasar un texto a `sumar` y Python no se queja; solo `mypy` lo detectaría.

### 1.3. Tipos compuestos y `Optional`

Antes de Python 3.10 se usaba `typing`:

```python
from typing import Optional, Union

def buscar(id: int) -> Optional[str]:
    """Devuelve un nombre o None."""
    return {1: "Ana"}.get(id)


def procesar(valor: Union[int, str]) -> str:
    return str(valor)
```

**Explicación línea por línea:**
- `Optional[str]` equivale a `str | None`: el valor puede ser texto o nada.
- `Union[int, str]` equivale a `int | str`: uno u otro.

En Python moderno, **prefiere** `str | None` y `int | str` a las versiones de `typing`.

### 1.4. Callable, Iterable y genéricos

```python
from collections.abc import Callable, Iterable
from typing import TypeVar

T = TypeVar("T")


def primero(items: Iterable[T]) -> T:
    """Devuelve el primer elemento de un iterable."""
    return next(iter(items))


def aplicar(func: Callable[[int], int], valor: int) -> int:
    return func(valor)


print(primero([10, 20, 30]))
print(aplicar(lambda x: x * 2, 5))
```

Salida esperada:

```text
10
10
```

**Explicación línea por línea:**
- `Iterable[T]` indica "algo sobre lo que se puede iterar y produce elementos de tipo `T`".
- `TypeVar("T")` crea una **variable de tipo**: `primero` funciona con listas de enteros, textos, etc., y el tipo de retorno se ajusta.
- `Callable[[int], int]` describe una función que recibe un `int` y devuelve un `int`.

### 1.5. `TypedDict`: diccionarios con forma

```python
from typing import TypedDict


class Punto(TypedDict):
    x: int
    y: int


p: Punto = {"x": 1, "y": 2}
```

**Explicación línea por línea:**
- `TypedDict` describe la **forma** esperada de un diccionario: qué claves y de qué tipo.
- `mypy` avisará si falta una clave o si el tipo es incorrecto.

### 1.6. Verificación con mypy

```bash
pip install mypy
mypy mi_archivo.py
```

Si todo está bien:

```text
Success: no issues found in 1 source file
```

Si hay un error:

```python
def sumar(a: int, b: int) -> int:
    return a + b

sumar("1", "2")
```

```text
error: Argument 1 to "sumar" has incompatible type "str"; expected "int"
```

### 1.7. Errores comunes

**Error 1 — Anotar y luego no verificar.**

Sin `mypy`/`pyright`, las anotaciones son solo documentación. *Solución:* añade el chequeo a tu flujo de trabajo.

**Error 2 — Usar `Any` en exceso.**

```python
from typing import Any
def f(x: Any) -> Any: ...   # anula el beneficio del tipado
```

**Error 3 — Confundir `list[int]` (moderno) con `List[int]` (antiguo).**

Ambos funcionan, pero el moderno es más limpio en 3.9+.

### 1.8. Checkpoint de comprensión

1. ¿Para qué sirven las anotaciones si Python no las aplica?
2. ¿Qué significa `int | None`?
3. ¿Qué herramienta verifica los tipos?

### 1.9. Ejercicio propuesto

**Ejercicio 1.1.** Anota completamente una función que reciba una lista de diccionarios y devuelva la media de una clave.

**Pista:** usa `Sequence[dict[str, float]]` como tipo del parámetro.

---

## 2. Async/await y asyncio

### 2.1. El problema: esperar sin bloquear

Muchas tareas pasan la mayor parte del tiempo **esperando**: descargar una web, leer de disco, consultar una base de datos. En un programa normal, mientras esperas una descarga, el programa está **parado**. Si necesitas descargar 100 páginas, esperarías una tras otra.

La **programación asíncrona** permite que, mientras una tarea espera, **otra avance**. Todo ocurre en un solo hilo, gestionado por un **bucle de eventos**.

**Analogía:** un camarero que toma el pedido de la mesa 1, lo lleva a la cocina, y mientras se cocina, atiende la mesa 2. No se queda plantado esperando a que la cocina termine. Un solo camarero (un hilo) atiende muchas mesas (tareas).

### 2.2. Corrutinas

Una **corrutina** es una función `async` que puede **pausarse** en los puntos `await`:

```python
import asyncio


async def saludar(nombre: str) -> str:
    await asyncio.sleep(1)   # simula una espera de E/S
    return f"Hola, {nombre}"


async def main() -> None:
    resultado = await saludar("Ana")
    print(resultado)


asyncio.run(main())
```

Salida esperada (tras 1 segundo):

```text
Hola, Ana
```

**Explicación línea por línea:**
- `async def` define una **corrutina**.
- `await asyncio.sleep(1)` **cede el control** al bucle de eventos durante 1 segundo, permitiendo que otras tareas avancen.
- `await saludar("Ana")` espera el resultado de la corrutina.
- `asyncio.run(main())` arranca el **bucle de eventos** y ejecuta `main`.

### 2.3. Ejecución concurrente con `gather`

Aquí está la magia. Lanzamos varias corrutinas **a la vez**:

```python
import asyncio
import time


async def tarea(nombre: str, segundos: float) -> str:
    await asyncio.sleep(segundos)
    return f"{nombre} terminada"


async def main() -> None:
    inicio = time.perf_counter()
    resultados = await asyncio.gather(
        tarea("A", 2),
        tarea("B", 1),
        tarea("C", 3),
    )
    print(resultados)
    print(f"Tiempo: {time.perf_counter() - inicio:.1f}s")


asyncio.run(main())
```

Salida esperada:

```text
['A terminada', 'B terminada', 'C terminada']
Tiempo: 3.0s
```

**Explicación línea por línea:**
- `asyncio.gather` lanza las tres corrutinas **concurrentemente**.
- El tiempo total es ~3 s (la más larga), **no** 6 s (la suma). Ese es el poder de la concurrencia.
- Mientras A espera, B y C avanzan.

### 2.4. El error fatal: bloquear el bucle

En una corrutina, **nunca** uses código bloqueante como `time.sleep` o cálculos pesados: congelaría **todo** el bucle de eventos.

```python
async def malo():
    import time
    time.sleep(1)   # ❌ bloquea TODO el bucle

async def bueno():
    import asyncio
    await asyncio.sleep(1)   # ✅ cede el control
```

Si necesitas ejecutar código bloqueante, delega en un hilo:

```python
resultado = await asyncio.to_thread(funcion_bloqueante)
```

### 2.5. Errores comunes

**Error 1 — Olvidar `await`.**

```python
resultado = saludar("Ana")   # devuelve una corrutina sin ejecutar
```

*Síntoma:* `RuntimeWarning: coroutine 'saludar' was never awaited`. *Solución:* añade `await`.

**Error 2 — Bloquear el bucle** con `time.sleep`, `requests` o cálculos largos.

**Error 3 — Llamar a `asyncio.run` dentro de una corrutina.**

*Solución:* usa `await` directamente.

### 2.6. Checkpoint de comprensión

1. ¿Qué ventaja aporta la programación asíncrona frente a la secuencial?
2. ¿Por qué `time.sleep` es un error dentro de una corrutina?
3. ¿Qué hace `asyncio.gather`?

### 2.7. Ejercicio propuesto

**Ejercicio 2.1.** Descarga (simulada con `sleep`) 5 URLs en paralelo y mide el tiempo total.

**Pista:** usa `asyncio.gather` con una lista de corrutinas.

---

## 3. Concurrencia: threading y multiprocessing

### 3.1. Tres formas de hacer varias cosas a la vez

Python ofrece tres modelos de concurrencia, cada uno para un escenario:

| Modelo | Ideal para | Comparte memoria | Paralelismo real de CPU |
|---|---|---|---|
| `threading` | Tareas con mucha **E/S** | Sí | No (GIL) |
| `multiprocessing` | Tareas intensivas de **CPU** | No (procesos separados) | Sí |
| `asyncio` | Muchas conexiones de **E/S** | Sí (un hilo) | No |

**Analogía:**
- **Threading:** varios cocineros en la **misma** cocina, compartiendo utensilios (y chocando a veces).
- **Multiprocessing:** varias cocinas **independientes**, cada una con su cocinero y sus utensilios.
- **Asyncio:** un cocinero muy organizado que alterna tareas mientras espera.

### 3.2. Threading

```python
import threading
import time


def trabajador(nombre: str) -> None:
    print(f"{nombre} inicia")
    time.sleep(1)
    print(f"{nombre} termina")


hilos = [threading.Thread(target=trabajador, args=(f"Hilo-{i}",)) for i in range(3)]
for h in hilos:
    h.start()
for h in hilos:
    h.join()
print("Todos terminaron")
```

Salida esperada (el orden puede variar):

```text
Hilo-0 inicia
Hilo-1 inicia
Hilo-2 inicia
Hilo-0 termina
Hilo-1 termina
Hilo-2 termina
Todos terminaron
```

**Explicación línea por línea:**
- `threading.Thread(target=..., args=...)` crea un hilo que ejecutará la función.
- `.start()` lo pone en marcha.
- `.join()` espera a que termine.
- Los tres hilos esperan **a la vez**: el programa tarda ~1 s, no 3 s.

**El problema de los hilos: condiciones de carrera.** Si varios hilos modifican el mismo dato a la vez, el resultado es impredecible. Se resuelve con `Lock`, colas o evitando el estado compartido.

### 3.3. Multiprocessing

Para tareas intensivas de CPU, los hilos **no ayudan** (por el GIL, sección 4). Se usan **procesos**:

```python
from multiprocessing import Pool


def cuadrado(n: int) -> int:
    return n * n


if __name__ == "__main__":
    with Pool(processes=4) as pool:
        resultados = pool.map(cuadrado, range(10))
    print(resultados)
```

Salida esperada:

```text
[0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
```

**Explicación línea por línea:**
- `Pool(processes=4)` crea 4 procesos trabajadores.
- `pool.map(func, iterable)` reparte el trabajo entre ellos y recoge los resultados.
- **Nota:** `if __name__ == "__main__":` es **obligatorio** con multiprocessing en Windows.

### 3.4. `concurrent.futures`: la API unificada

Python ofrece una API común para hilos y procesos:

```python
from concurrent.futures import ThreadPoolExecutor


def tarea(n: int) -> int:
    return n * 2


with ThreadPoolExecutor(max_workers=4) as executor:
    resultados = list(executor.map(tarea, range(5)))
print(resultados)
```

Salida esperada:

```text
[0, 2, 4, 6, 8]
```

Sustituye `ThreadPoolExecutor` por `ProcessPoolExecutor` y tienes la versión con procesos.

### 3.5. Errores comunes

**Error 1 — Condiciones de carrera.**

*Solución:* usa `Lock`, colas (`queue.Queue`) o evita el estado compartido.

**Error 2 — Usar threading para CPU.**

*Síntoma:* el programa no se acelera. *Solución:* usa multiprocessing.

**Error 3 — Olvidar `if __name__ == "__main__":`** con multiprocessing en Windows.

### 3.6. Checkpoint de comprensión

1. ¿Qué modelo usarías para descargar 100 páginas? ¿Y para procesar imágenes?
2. ¿Qué es una condición de carrera?
3. ¿Qué ventaja aporta `concurrent.futures`?

### 3.7. Ejercicio propuesto

**Ejercicio 3.1.** Compara el tiempo de sumar 10 millones de números con un bucle y con `multiprocessing`.

**Pista:** divide el rango entre procesos.

---

## 4. El GIL y sus implicaciones

### 4.1. ¿Qué es el GIL?

El **GIL** (*Global Interpreter Lock*) es un candado que garantiza que **solo un hilo ejecute bytecode de Python a la vez** en CPython. Aunque tu máquina tenga 8 núcleos, un programa Python con hilos usará efectivamente **uno** para ejecutar código Python.

**¿Por qué existe?** Simplifica la gestión de memoria y hace que CPython sea más rápido en programas de un solo hilo. Es un compromiso histórico.

### 4.2. La demostración

```python
import threading
import time


def contar() -> int:
    total = 0
    for i in range(50_000_000):
        total += i
    return total


inicio = time.perf_counter()
contar()
contar()
print(f"Secuencial: {time.perf_counter() - inicio:.2f}s")

inicio = time.perf_counter()
h1 = threading.Thread(target=contar)
h2 = threading.Thread(target=contar)
h1.start(); h2.start()
h1.join(); h2.join()
print(f"Con hilos: {time.perf_counter() - inicio:.2f}s")
```

Salida esperada (los tiempos varían):

```text
Secuencial: 4.10s
Con hilos: 4.30s
```

**Explicación:** no hay mejora; incluso puede empeorar por el coste de cambiar de hilo. El GIL impide que ambos hilos calculen a la vez.

### 4.3. Cómo evitar el GIL

- **`multiprocessing`:** procesos separados, cada uno con su propio intérprete y su propio GIL.
- **Librerías en C que liberan el GIL:** NumPy, por ejemplo, lo libera durante sus operaciones pesadas.
- **Python 3.13+:** existe un modo *free-threaded* experimental.

### 4.4. ¿Cuándo SÍ ayudan los hilos?

Los hilos **sí** aceleran tareas de **E/S**. Mientras un hilo espera una respuesta de red, el GIL se libera y otro hilo puede ejecutar.

```python
# Descargar 10 páginas con hilos SÍ acelera (la espera no usa el GIL)
```

### 4.5. Errores comunes

**Error 1 — Creer que añadir hilos acelera todo.**

Solo acelera la E/S.

**Error 2 — Ignorar el GIL al diseñar.**

*Solución:* elige el modelo correcto (hilos para E/S, procesos para CPU).

### 4.6. Checkpoint de comprensión

1. ¿Qué impide el GIL?
2. ¿Por qué los hilos sí ayudan en tareas de E/S?
3. ¿Cómo consigues paralelismo real de CPU?

### 4.7. Ejercicio propuesto

**Ejercicio 4.1.** Demuestra que descargar "datos" (sleep) sí se acelera con hilos, pero calcular no.

**Pista:** compara `time.sleep` con un bucle de CPU.

---

## 5. Expresiones regulares (re)

### 5.1. ¿Qué es una expresión regular?

Una **expresión regular** (o *regex*) es un **patrón** que describe un conjunto de textos. Sirven para **buscar**, **extraer** y **reemplazar** texto de forma muy flexible.

**Analogía:** una expresión regular es como una plantilla de calco. No describe un texto concreto, sino **la forma** que deben tener los textos que buscas: "una palabra de 5 letras", "algo que parezca un email", "un número de teléfono".

### 5.2. Los símbolos esenciales

| Símbolo | Significado |
|---|---|
| `.` | Cualquier carácter (salvo salto de línea) |
| `\d` | Un dígito (0-9) |
| `\w` | Letra, dígito o guion bajo |
| `\s` | Espacio en blanco |
| `+` | Una o más repeticiones |
| `*` | Cero o más |
| `?` | Cero o una |
| `{n,m}` | Entre n y m repeticiones |
| `^` `$` | Inicio / fin del texto |
| `[]` | Un conjunto de caracteres |
| `()` | Un grupo de captura |

### 5.3. Buscar coincidencias

```python
import re

texto = "Contacto: ana@example.com y luis@test.org"

print(re.findall(r"[\w.]+@[\w.]+", texto))
print(re.search(r"\w+@\w+\.\w+", texto).group())
print(re.match(r"Contacto", texto) is not None)
```

Salida esperada:

```text
['ana@example.com', 'luis@test.org']
ana@example.com
True
```

**Explicación línea por línea:**
- `re.findall(patron, texto)` devuelve **todas** las coincidencias como lista.
- `re.search(patron, texto)` devuelve la **primera** coincidencia (un objeto `Match`) o `None`; `.group()` extrae el texto.
- `re.match` ancla al **inicio** de la cadena.
- La `r` delante (`r"..."`) crea una **cadena cruda**: las barras invertidas no se interpretan. En regex es casi siempre lo que quieres.

### 5.4. Grupos de captura

Los paréntesis `()` **capturan** partes del patrón:

```python
import re

patron = r"(\d{4})-(\d{2})-(\d{2})"
fecha = "2026-09-16"
m = re.match(patron, fecha)
print(m.groups())
print(m.group(1))
```

Salida esperada:

```text
('2026', '09', '16')
2026
```

**Explicación línea por línea:**
- `(\d{4})` captura 4 dígitos (el año); `(\d{2})` captura 2 (mes y día).
- `m.groups()` devuelve una tupla con todos los grupos.
- `m.group(1)` devuelve el primer grupo.

### 5.5. Sustituir texto

```python
import re

texto = "Hola   mundo    Python"
limpio = re.sub(r"\s+", " ", texto)
print(limpio)
```

Salida esperada:

```text
Hola mundo Python
```

**Explicación línea por línea:**
- `\s+` coincide con uno o más espacios en blanco.
- `re.sub(patron, reemplazo, texto)` los sustituye por un solo espacio.

**Sustitución con función:** puedes usar una función para transformar cada coincidencia:

```python
import re

def invertir(match):
    return match.group()[::-1]

print(re.sub(r"\w+", invertir, "hola mundo"))
```

Salida esperada:

```text
aloh odnum
```

### 5.6. Compilar patrones reutilizables

Si usas el mismo patrón muchas veces, compílalo para ganar rendimiento y claridad:

```python
import re

PATRON = re.compile(r"\b\w{5}\b")
print(PATRON.findall("Hola mundo Python código"))
```

Salida esperada:

```text
['mundo', 'Hola']
```

### 5.7. Errores comunes

**Error 1 — Olvidar `r"..."`.**

Sin la `r`, `\d` podría interpretarse mal. *Solución:* usa siempre cadenas crudas en patrones.

**Error 2 — Usar regex para HTML/XML.**

Las regex no entienden estructuras anidadas. *Solución:* usa un parser como BeautifulSoup o lxml.

**Error 3 — Patrones catastróficamente lentos.**

Ciertos patrones con backtracking pueden tardar una eternidad con textos largos. *Solución:* simplifica o usa `re` con cuidado.

### 5.8. Checkpoint de comprensión

1. ¿Qué diferencia hay entre `search`, `match` y `findall`?
2. ¿Para qué sirven los grupos de captura?
3. ¿Por qué se recomienda compilar los patrones reutilizables?

### 5.9. Ejercicio propuesto

**Ejercicio 5.1.** Extrae todas las fechas en formato `dd/mm/aaaa` de un texto y conviértelas a `aaaa-mm-dd`.

**Pista:** usa `re.sub` con una función de reemplazo.

---

## 6. Serialización: JSON, pickle, YAML

### 6.1. ¿Qué es serializar?

**Serializar** es convertir un objeto (diccionario, lista, objeto) en un formato que se pueda **guardar** o **transmitir**, y **deserializar** es el proceso inverso.

**Analogía:** serializar es como empaquetar un mueble para mudarlo: lo desmontas en piezas transportables. Deserializar es montarlo de nuevo.

### 6.2. Los tres formatos

| Formato | Legible | Seguro | Uso típico |
|---|---|---|---|
| JSON | Sí | Sí | APIs, configuración |
| pickle | No | **No** | Objetos Python |
| YAML | Sí | Sí* | Configuración |

### 6.3. JSON

```python
import json

datos = {"nombre": "Ana", "edad": 30, "hobbies": ["leer", "correr"]}
texto = json.dumps(datos, indent=2, ensure_ascii=False)
print(texto)

recuperado = json.loads(texto)
print(recuperado["nombre"])
```

Salida esperada:

```text
{
  "nombre": "Ana",
  "edad": 30,
  "hobbies": ["leer", "correr"]
}
Ana
```

**Explicación línea por línea:**
- `json.dumps(obj)` convierte el objeto a **texto** JSON.
- `indent=2` lo formatea con sangría; `ensure_ascii=False` permite acentos.
- `json.loads(texto)` hace lo inverso: texto → objeto.

**Con archivos:**

```python
with open("datos.json", "w", encoding="utf-8") as f:
    json.dump(datos, f, ensure_ascii=False)

with open("datos.json", "r", encoding="utf-8") as f:
    datos = json.load(f)
```

### 6.4. Pickle

```python
import pickle

objeto = {"a": 1, "b": [1, 2, 3]}
datos = pickle.dumps(objeto)
recuperado = pickle.loads(datos)
print(recuperado)
```

Salida esperada:

```text
{'a': 1, 'b': [1, 2, 3]}
```

**⚠️ Advertencia de seguridad:** `pickle` puede ejecutar código arbitrario al deserializar. **Nunca** cargues pickle de fuentes no confiables. Para eso, usa JSON.

### 6.5. YAML

```python
import yaml   # pip install pyyaml

config = """
servidor:
  host: localhost
  puerto: 8000
"""
datos = yaml.safe_load(config)
print(datos["servidor"]["host"])
```

Salida esperada:

```text
localhost
```

**Explicación línea por línea:**
- YAML es un formato muy legible, ideal para archivos de configuración.
- `yaml.safe_load` carga el texto de forma segura. **Nunca** uses `yaml.load` sin `Loader` (ejecuta código arbitrario).

### 6.6. Errores comunes

**Error 1 — `pickle` para datos de usuario.**

Riesgo grave de seguridad. *Solución:* usa JSON.

**Error 2 — `yaml.load` sin `Loader`.**

*Solución:* usa `yaml.safe_load`.

**Error 3 — Serializar objetos no serializables.**

`datetime` no es serializable a JSON por defecto. *Solución:* usa un *encoder* personalizado o convierte a texto ISO.

### 6.7. Checkpoint de comprensión

1. ¿Qué diferencia de seguridad hay entre JSON y pickle?
2. ¿Por qué `yaml.safe_load` y no `yaml.load`?
3. ¿Cuándo usarías JSON y cuándo YAML?

### 6.8. Ejercicio propuesto

**Ejercicio 6.1.** Guarda una lista de diccionarios en JSON y recupérala; luego hazlo en YAML.

**Pista:** usa `json.dump` y `yaml.safe_dump`.

---

## 7. Archivos y pathlib

### 7.1. El problema de manejar rutas

Trabajar con rutas como texto (`"carpeta/archivo.txt"`) es propenso a errores: separadores distintos en Windows y Linux, concatenaciones mal hechas, etc. El módulo **`pathlib`** ofrece una forma **orientada a objetos** y multiplataforma de manejar rutas.

### 7.2. Crear y manipular rutas

```python
from pathlib import Path

base = Path("/home/roy/proyecto")
archivo = base / "datos" / "config.json"   # el operador / une rutas

print(archivo.name)
print(archivo.stem)
print(archivo.suffix)
print(archivo.parent)
print(archivo.exists())
```

Salida esperada:

```text
config.json
config
.json
/home/roy/proyecto/datos
False
```

**Explicación línea por línea:**
- `Path("/home/roy/proyecto")` crea un objeto ruta.
- `base / "datos" / "config.json"` **une** rutas con el operador `/`. ¡Mucho más limpio que concatenar textos!
- `.name` es el nombre del archivo; `.stem`, sin extensión; `.suffix`, la extensión.
- `.parent` es la carpeta contenedora.
- `.exists()` comprueba si la ruta existe.

### 7.3. Leer y escribir

```python
from pathlib import Path

ruta = Path("salida.txt")
ruta.write_text("Hola\nMundo\n", encoding="utf-8")
print(ruta.read_text(encoding="utf-8"))
```

Salida esperada:

```text
Hola
Mundo
```

**Explicación línea por línea:**
- `.write_text(...)` escribe texto en el archivo (creándolo si no existe).
- `.read_text(...)` lo lee entero.
- Siempre especifica `encoding="utf-8"` para evitar problemas con acentos.

### 7.4. Recorrer directorios

```python
from pathlib import Path

for py in Path(".").glob("**/*.py"):
    print(py)
```

**Explicación línea por línea:**
- `.glob("**/*.py")` busca recursivamente todos los `.py` en la carpeta actual y subcarpetas.

### 7.5. Operaciones comunes

```python
from pathlib import Path

ruta = Path("temporal")
ruta.mkdir(exist_ok=True)
(ruta / "a.txt").touch()
print([p.name for p in ruta.iterdir()])
```

Salida esperada:

```text
['a.txt']
```

**Explicación línea por línea:**
- `.mkdir(exist_ok=True)` crea la carpeta si no existe (sin error si ya está).
- `.touch()` crea un archivo vacío.
- `.iterdir()` itera sobre el contenido de la carpeta.

### 7.6. Errores comunes

**Error 1 — Concatenar rutas con `+`.**

```python
ruta = carpeta + "/" + archivo   # ❌ frágil
ruta = Path(carpeta) / archivo   # ✅
```

**Error 2 — Olvidar `encoding="utf-8"`.**

Provoca errores en otros sistemas.

**Error 3 — No comprobar existencia antes de leer.**

*Solución:* usa `.exists()` o captura `FileNotFoundError`.

### 7.7. Checkpoint de comprensión

1. ¿Qué ventaja tiene `pathlib` sobre `os.path`?
2. ¿Qué hace el operador `/` entre rutas?
3. ¿Cómo recorres recursivamente todos los `.py`?

### 7.8. Ejercicio propuesto

**Ejercicio 7.1.** Lista todos los archivos `.txt` de una carpeta y su tamaño.

**Pista:** usa `.glob("*.txt")` y `.stat().st_size`.

---

## 8. Testing con unittest, pytest y mock

### 8.1. Por qué probar el código

Los **tests** son código que verifica que otro código hace lo que esperas. Aportan:

- **Confianza:** puedes cambiar código sin miedo a romperlo.
- **Documentación viva:** muestran cómo se usa.
- **Detección temprana:** encuentras bugs antes que el usuario.

**Analogía:** un test es como un control de calidad en una fábrica. No produce nada, pero garantiza que lo producido funciona.

### 8.2. pytest: el estándar de facto

```python
# calculadora.py
def sumar(a: int, b: int) -> int:
    return a + b


def dividir(a: float, b: float) -> float:
    if b == 0:
        raise ZeroDivisionError
    return a / b
```

```python
# test_calculadora.py
import pytest
from calculadora import dividir, sumar


def test_sumar():
    assert sumar(2, 3) == 5


def test_dividir():
    assert dividir(10, 2) == 5


def test_dividir_entre_cero():
    with pytest.raises(ZeroDivisionError):
        dividir(1, 0)
```

Ejecución:

```bash
pip install pytest
pytest -v
```

Salida esperada:

```text
test_calculadora.py::test_sumar PASSED
test_calculadora.py::test_dividir PASSED
test_calculadora.py::test_dividir_entre_cero PASSED
```

**Explicación línea por línea:**
- Los tests son funciones cuyo nombre empieza por `test_`.
- `assert` comprueba una condición; si es falsa, el test falla.
- `pytest.raises(Error)` comprueba que se lanza una excepción.

### 8.3. unittest: el integrado

```python
import unittest
from calculadora import sumar


class TestCalculadora(unittest.TestCase):
    def test_sumar(self):
        self.assertEqual(sumar(2, 3), 5)


if __name__ == "__main__":
    unittest.main()
```

`unittest` viene con Python, pero `pytest` es más conciso y potente.

### 8.4. mock: simular dependencias

Los tests **no** deben depender de la red, la base de datos o servicios externos. `mock` los simula:

```python
from unittest.mock import patch
import requests


def obtener_precio() -> int:
    respuesta = requests.get("https://api.example.com/precio")
    return respuesta.json()["precio"]


@patch("requests.get")
def test_obtener_precio(mock_get):
    mock_get.return_value.json.return_value = {"precio": 100}
    assert obtener_precio() == 100
```

**Explicación línea por línea:**
- `@patch("requests.get")` reemplaza la función real por un *mock*.
- `mock_get.return_value.json.return_value = ...` define qué devolverá.
- Así el test es rápido y no toca la red.

### 8.5. Fixtures de pytest

Los *fixtures* preparan el entorno de un test:

```python
import pytest


@pytest.fixture
def usuario():
    return {"nombre": "Ana", "edad": 30}


def test_nombre(usuario):
    assert usuario["nombre"] == "Ana"
```

### 8.6. La pirámide de tests

```text
        /\
       /E2E\        pocos, lentos (todo el sistema)
      /------\
     /  Integ \     medios (varias piezas)
    /----------\
   /   Unitarios\   muchos, rápidos (una función)
  /--------------\
```

### 8.7. Errores comunes

**Error 1 — Tests que dependen del orden.**

Cada test debe ser **independiente**. *Solución:* prepara el estado en cada test (fixtures).

**Error 2 — No probar casos límite.**

Prueba cero, vacío, `None`, negativos... Los bugs viven en los extremos.

**Error 3 — Tests lentos o con red real.**

*Solución:* usa `mock`.

### 8.8. Checkpoint de comprensión

1. ¿Por qué los tests aumentan la confianza al refactorizar?
2. ¿Para qué sirve `mock`?
3. ¿Qué distingue un test unitario de uno de integración?

### 8.9. Ejercicio propuesto

**Ejercicio 8.1.** Escribe tests para una función `es_palindromo(texto)` que cubran mayúsculas y espacios.

**Pista:** parametriza con `@pytest.mark.parametrize`.

---

## 9. Cython y extensiones en C

### 9.1. Cuándo Python no es suficiente

Python es rápido de escribir pero lento de ejecutar. Cuando un fragmento concreto se convierte en un **cuello de botella**, puedes escribirlo en **C** o **Cython** y llamarlo desde Python.

### 9.2. Cython

**Cython** es un superconjunto de Python que se **compila a C**, mucho más rápido en bucles numéricos:

**`suma.pyx`:**

```cython
def sumar_hasta(int n):
    cdef int i
    cdef long total = 0
    for i in range(n):
        total += i
    return total
```

**`setup.py`:**

```python
from setuptools import setup
from Cython.Build import cythonize

setup(ext_modules=cythonize("suma.pyx"))
```

```bash
pip install cython
python setup.py build_ext --inplace
```

**Explicación línea por línea:**
- `cdef int i` declara una variable con **tipo C**, mucho más rápida.
- El bucle se compila a C y evita la sobrecarga de Python.

### 9.3. Alternativas modernas

- **`ctypes`:** llamar a librerías C existentes.
- **`cffi`:** interfaz C más segura.
- **`pybind11`** / **`pyo3`** (Rust): extensiones modernas.
- **`numba`:** compilación JIT para funciones NumPy.

### 9.4. Errores comunes

**Error 1 — Optimizar antes de medir.**

*Solución:* perfila primero con `cProfile`; optimiza solo el cuello de botella real.

**Error 2 — Reescribir todo en C.**

*Solución:* solo los *hot spots* (el 1-5 % del código que consume el 90 % del tiempo).

### 9.5. Checkpoint de comprensión

1. ¿Cuándo merece la pena escribir una extensión en C?
2. ¿Qué hace Cython?
3. ¿Qué herramienta usas para encontrar el cuello de botella?

### 9.6. Ejercicio propuesto

**Ejercicio 9.1.** Perfila una función lenta con `cProfile` e identifica el cuello de botella.

**Pista:** `python -m cProfile -s cumtime script.py`.

---

## 10. Metaprogramación

### 10.1. ¿Qué es la metaprogramación?

La **metaprogramación** es código que **manipula código**: decoradores, metaclases, acceso dinámico a atributos, funciones que generan funciones. Permite gran flexibilidad, pero también complejidad.

**Analogía:** normalmente usas herramientas; la metaprogramación es **fabricar herramientas** que a su vez construyen cosas.

### 10.2. Acceso dinámico a atributos

```python
class Config:
    def __init__(self, **datos):
        self.__dict__.update(datos)


c = Config(host="localhost", puerto=8000)
print(c.host)
print(getattr(c, "puerto"))
setattr(c, "debug", True)
print(c.debug)
```

Salida esperada:

```text
localhost
8000
True
```

**Explicación línea por línea:**
- `self.__dict__.update(datos)` crea atributos a partir de un diccionario.
- `getattr(obj, nombre)` lee un atributo por su nombre (texto).
- `setattr(obj, nombre, valor)` lo asigna dinámicamente.

### 10.3. `__getattr__` para atributos calculados

```python
class Env:
    """Accede a variables de entorno por atributo."""

    def __getattr__(self, nombre: str) -> str:
        import os
        try:
            return os.environ[nombre.upper()]
        except KeyError:
            raise AttributeError(nombre) from None


import os
os.environ["HOME_PATH"] = "/home/roy"
env = Env()
print(env.home_path)
```

Salida esperada:

```text
/home/roy
```

**Explicación línea por línea:**
- `__getattr__` se llama **solo** cuando el atributo no existe de forma normal.
- Aquí busca la variable de entorno con el nombre en mayúsculas.

### 10.4. Generar funciones dinámicamente

```python
def crear_multiplicador(n: int):
    def multiplicar(x: int) -> int:
        return x * n
    multiplicar.__name__ = f"multiplicar_por_{n}"
    return multiplicar


por_5 = crear_multiplicador(5)
print(por_5(3), por_5.__name__)
```

Salida esperada:

```text
15 multiplicar_por_5
```

### 10.5. `eval` y `exec` (con mucha precaución)

```python
expresion = "2 + 3 * 4"
print(eval(expresion))
```

Salida esperada:

```text
14
```

**⚠️ Nunca uses `eval`/`exec` con entrada no confiable:** ejecutan código arbitrario. Es una de las vulnerabilidades más graves.

### 10.6. Errores comunes

**Error 1 — Abusar de la metaprogramación.**

Hace el código difícil de seguir y depurar. *Solución:* úsala solo cuando aporte un beneficio claro.

**Error 2 — `eval` sobre datos de usuario.**

*Solución:* nunca. Busca alternativas (`ast.literal_eval` para literales).

### 10.7. Checkpoint de comprensión

1. ¿Qué es la metaprogramación?
2. ¿Qué hace `__getattr__`?
3. ¿Por qué `eval` es peligroso?

### 10.8. Ejercicio propuesto

**Ejercicio 10.1.** Crea una clase que exponga claves de un diccionario como atributos de solo lectura con `__getattr__`.

**Pista:** lanza `AttributeError` si la clave no existe.

---

## 11. Buenas prácticas

1. **Anota los tipos** y verifica con `mypy`.
2. **Usa asyncio para E/S concurrente**, multiprocessing para CPU.
3. **Recuerda el GIL** al elegir hilos.
4. **Compila patrones regex** reutilizables.
5. **Prefiere JSON** para interoperabilidad; evita `pickle` con datos externos.
6. **Usa `pathlib`** en lugar de `os.path`.
7. **Escribe tests** con `pytest` y aísla dependencias con `mock`.
8. **Mide antes de optimizar.**
9. **Evita `eval`/`exec`** con entrada externa.
10. **Usa metaprogramación con moderación.**

---

## 12. Recursos

- **`typing`:** <https://docs.python.org/3/library/typing.html>
- **`asyncio`:** <https://docs.python.org/3/library/asyncio.html>
- **`concurrent.futures`:** <https://docs.python.org/3/library/concurrent.futures.html>
- **`re`:** <https://docs.python.org/3/library/re.html>
- **`pathlib`:** <https://docs.python.org/3/library/pathlib.html>
- **pytest:** <https://docs.pytest.org/>
- **Cython:** <https://cython.org/>
- **Libro:** *Fluent Python*, capítulos 19–24.

---

## 13. Mini resumen final

- Los **type hints** documentan y permiten verificación estática.
- **asyncio** gestiona E/S concurrente en un solo hilo.
- **threading** sirve para E/S; **multiprocessing**, para CPU.
- El **GIL** impide paralelismo real de CPU con hilos.
- **`re`** busca, extrae y sustituye patrones.
- **JSON/YAML** son formatos legibles; **pickle**, potente pero inseguro.
- **`pathlib`** moderniza el manejo de rutas.
- **pytest** y **mock** son el estándar para testing.
- **Cython** y extensiones en C aceleran código crítico.
- La **metaprogramación** da flexibilidad, con moderación.

---

### 🎯 Retos opcionales

1. **Reto 1:** Convierte un scraper síncrono en asíncrono con `aiohttp` y compara tiempos.
2. **Reto 2:** Escribe una suite de tests con cobertura usando `pytest-cov`.
3. **Reto 3:** Perfila y optimiza una función con `cProfile` y una extensión Cython.

**Anterior:** [07_modulos_paquetes.md](07_modulos_paquetes.md) · **Siguiente (Parte II):** [09_flask.md](09_flask.md)
