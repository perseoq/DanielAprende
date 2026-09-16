# 07 · Módulos y Paquetes

**Versión recomendada:** Python 3.12
**Requisito:** [06_errores_excepciones.md](06_errores_excepciones.md)
**Nivel:** Intermedio
**Duración estimada de estudio:** 4–6 horas

---

## Índice de contenidos

1. [Importación de módulos](#1-importación-de-módulos)
2. [Creación de paquetes](#2-creación-de-paquetes)
3. [`__init__.py` y namespaces](#3-__init__py-y-namespaces)
4. [Importlib](#4-importlib)
5. [Publicación de paquetes en PyPI](#5-publicación-de-paquetes-en-pypi)
6. [Ejemplo integrador](#6-ejemplo-integrador)
7. [Buenas prácticas](#7-buenas-prácticas)
8. [Recursos](#8-recursos)
9. [Mini resumen final](#9-mini-resumen-final)

---

## 1. Importación de módulos

### 1.1. ¿Qué es un módulo y por qué dividir el código?

A medida que un programa crece, meter todo en un archivo se vuelve inmanejable. Un **módulo** es simplemente un archivo `.py` con código reutilizable (funciones, clases, variables) que puedes **importar** desde otros archivos.

**Analogía:** un módulo es como una **caja de herramientas etiquetada**. En lugar de tener todas las herramientas apiladas en el suelo (un archivo gigante), las organizas en cajas: la caja "matemáticas", la caja "texto", la caja "base de datos". Cuando necesitas un destornillador, abres la caja correspondiente.

Dividir el código en módulos aporta:

- **Organización:** cada módulo tiene un propósito claro.
- **Reutilización:** usas el mismo módulo desde muchos sitios.
- **Mantenimiento:** cambias un módulo sin tocar los demás.
- **Espacios de nombres:** evitas colisiones de nombres (el Zen lo celebra).

### 1.2. Importar un módulo de la biblioteca estándar

```python
import math

print(math.sqrt(16))
print(math.pi)
```

Salida esperada:

```text
4.0
3.141592653589793
```

**Explicación línea por línea:**
- `import math` carga el módulo `math` y lo hace accesible bajo el nombre `math`.
- `math.sqrt(16)` llama a la función `sqrt` **dentro** del módulo. El punto significa "de dentro de".
- `math.pi` accede a una constante del módulo.

Este es el estilo más seguro: dejas claro de dónde viene cada cosa.

### 1.3. Las formas de importar

Python ofrece varias formas. Cada una tiene sus ventajas:

```python
import math                          # módulo completo
import math as m                     # módulo con alias
from math import sqrt                # un nombre concreto
from math import sqrt, pi            # varios nombres
from math import *                   # todos los nombres (EVÍTALO)
```

Veámoslas:

```python
import math as m
from math import sqrt

print(m.pi)
print(sqrt(25))
```

Salida esperada:

```text
3.141592653589793
5.0
```

**Explicación línea por línea:**
- `import math as m` da un **alias** al módulo. Útil para nombres largos (`import pandas as pd`).
- `from math import sqrt` trae **solo** `sqrt` al espacio de nombres actual, así que puedes llamarlo sin `math.`.

### 1.4. ¿Cuándo usar cada forma?

| Forma | Cuándo usarla |
|---|---|
| `import modulo` | Casi siempre. Deja claro el origen de cada nombre |
| `import modulo as alias` | Nombres largos o convenciones (`np`, `pd`) |
| `from modulo import nombre` | Cuando usas mucho un nombre concreto |
| `from modulo import *` | **Nunca** en código serio |

**¿Por qué evitar `from math import *`?** Porque "contamina" tu espacio de nombres con decenas de nombres que no controlas. Si dos módulos definen `sqrt`, el último gana silenciosamente, y puedes sobrescribir tus propias variables sin darte cuenta.

### 1.5. Módulos de la biblioteca estándar

Python trae "baterías incluidas": cientos de módulos listos para usar. Algunos de los más útiles:

```python
import os
import sys
import json
import random
from collections import Counter

print(os.getcwd())
print(sys.version_info.major)
print(json.dumps({"a": 1}))
print(random.randint(1, 6))
print(Counter("banana"))
```

Salida esperada (el directorio y el dado varían):

```text
/home/roy
3
{"a": 1}
4
Counter({'a': 3, 'n': 2, 'b': 1})
```

**Explicación línea por línea:**
- `os.getcwd()` devuelve el directorio de trabajo actual.
- `sys.version_info.major` da la versión mayor de Python (3).
- `json.dumps(...)` convierte un diccionario a texto JSON.
- `random.randint(1, 6)` simula un dado.
- `Counter("banana")` cuenta cuántas veces aparece cada letra.

**Regla de oro:** antes de instalar una librería externa, comprueba si la biblioteca estándar ya lo resuelve.

### 1.6. El orden de los imports (PEP 8)

PEP 8 recomienda agrupar los imports en tres bloques, separados por una línea en blanco:

```python
# 1. Biblioteca estándar
import os
from pathlib import Path

# 2. Terceros (instalados con pip)
import requests

# 3. Locales de tu proyecto
from mi_proyecto.utils import helper
```

**Explicación:** este orden hace evidente qué dependencias tiene tu código y de qué tipo son. Herramientas como `isort` y `ruff` lo ordenan automáticamente.

### 1.7. Import circular

Un **import circular** ocurre cuando `a.py` importa `b.py` y `b.py` importa `a.py`. Python no puede completar ninguno de los dos y falla:

```text
ImportError: cannot import name 'algo' from partially initialized module
```

**Soluciones:**

1. **Reestructura:** mueve lo compartido a un tercer módulo.
2. **Importa dentro de la función** (no al principio del archivo):

```python
def mi_funcion():
    from modulo_b import helper   # import diferido
    return helper()
```

### 1.8. Errores comunes

**Error 1 — Nombrar un archivo como un módulo estándar.**

Si llamas `json.py` a tu archivo, `import json` importará **el tuyo**, no el estándar. *Solución:* renombra tu archivo.

**Error 2 — `from modulo import *`.**

*Solución:* importa nombres concretos.

**Error 3 — Import circular.**

*Solución:* reestructura o usa import diferido.

**Error 4 — `ModuleNotFoundError`.**

```text
ModuleNotFoundError: No module named 'requests'
```

*Causa:* el módulo no está instalado o el entorno no está activado. *Solución:* `pip install requests` en el entorno correcto.

### 1.9. Checkpoint de comprensión

1. ¿Cuál es la diferencia entre `import math` y `from math import sqrt`?
2. ¿Por qué se evita `from modulo import *`?
3. ¿Qué es un import circular y cómo se resuelve?

### 1.10. Ejercicio propuesto

**Ejercicio 1.1.** Crea un módulo `matematica.py` con funciones `sumar` y `restar`, e impórtalo desde `main.py`.

**Pista:** ambos archivos en la misma carpeta; usa `from matematica import sumar`.

---

## 2. Creación de paquetes

### 2.1. ¿Qué es un paquete?

Un **paquete** es una **carpeta** que agrupa varios módulos. Mientras un módulo es un archivo, un paquete es una carpeta con módulos dentro.

**Analogía:** si un módulo es una caja de herramientas, un paquete es el **estante** que agrupa varias cajas relacionadas.

```text
mi_paquete/
├── __init__.py
├── aritmetica.py
└── texto.py
```

### 2.2. Un paquete de ejemplo

**`mi_paquete/aritmetica.py`:**

```python
"""Operaciones aritméticas."""


def sumar(a: float, b: float) -> float:
    return a + b


def restar(a: float, b: float) -> float:
    return a - b
```

**`mi_paquete/texto.py`:**

```python
"""Operaciones con texto."""


def invertir(texto: str) -> str:
    return texto[::-1]
```

**`main.py`:**

```python
from mi_paquete import aritmetica
from mi_paquete.texto import invertir

print(aritmetica.sumar(2, 3))
print(invertir("python"))
```

Salida esperada:

```text
5
nohtyp
```

**Explicación línea por línea:**
- `from mi_paquete import aritmetica` importa el módulo `aritmetica` del paquete.
- `from mi_paquete.texto import invertir` importa una función concreta del módulo `texto`.
- Se accede con la ruta completa: paquete → módulo → nombre.

### 2.3. Subpaquetes

Los paquetes pueden contener subpaquetes, formando jerarquías:

```text
mi_paquete/
├── __init__.py
├── matematicas/
│   ├── __init__.py
│   └── avanzadas.py
└── texto/
    ├── __init__.py
    └── utils.py
```

```python
from mi_paquete.matematicas import avanzadas
```

### 2.4. Ejecutar un módulo con `-m`

Puedes ejecutar un módulo por su **nombre** en lugar de por su ruta, con la opción `-m`:

```bash
python -m mi_paquete.aritmetica
```

**Explicación:** `-m` busca el módulo en el `PYTHONPATH` y lo ejecuta como programa. Es la forma recomendada para ejecutar paquetes, porque resuelve los imports correctamente.

### 2.5. Errores comunes

**Error 1 — Carpeta con guiones.**

```text
mi-paquete/   # ❌ no se puede importar
```

*Solución:* usa guion bajo: `mi_paquete`.

**Error 2 — Falta `__init__.py`.**

Aunque Python 3 admite *namespace packages* (carpetas sin `__init__.py`), incluirlo es más predecible y explícito.

**Error 3 — Ejecutar el archivo equivocado.**

*Solución:* verifica la ruta y desde dónde ejecutas (`sys.path`).

### 2.6. Checkpoint de comprensión

1. ¿Qué diferencia hay entre un módulo y un paquete?
2. ¿Cómo accedes a una función dentro de un paquete?
3. ¿Para qué sirve `python -m`?

### 2.7. Ejercicio propuesto

**Ejercicio 2.1.** Crea un paquete `geometria/` con módulos `areas.py` y `perimetros.py`.

**Pista:** añade `__init__.py` a la carpeta.

---

## 3. `__init__.py` y namespaces

### 3.1. ¿Para qué sirve `__init__.py`?

`__init__.py` se ejecuta automáticamente **al importar el paquete**. Se usa para:

- **Exponer una API pública:** que `from paquete import funcion` funcione sin rutas largas.
- **Definir `__all__`:** controlar qué se importa con `*`.
- **Inicializar recursos:** registrar cosas, cargar configuración.

### 3.2. Exponer nombres en `__init__.py`

**`mi_paquete/__init__.py`:**

```python
"""Paquete de ejemplo."""

from .aritmetica import sumar, restar
from .texto import invertir

__all__ = ["sumar", "restar", "invertir"]
__version__ = "0.1.0"
```

Ahora se puede usar:

```python
from mi_paquete import sumar, invertir

print(sumar(1, 2))
print(invertir("abc"))
```

Salida esperada:

```text
3
cba
```

**Explicación línea por línea:**
- `from .aritmetica import sumar, restar` importa nombres del módulo hermano. El punto `.` significa "módulo del mismo paquete".
- `__all__` define qué nombres exporta el paquete con `from paquete import *`.
- `__version__` guarda la versión del paquete.
- Gracias a esto, el usuario no necesita saber en qué módulo interno vive cada función.

### 3.3. Imports relativos

Los imports relativos usan puntos para referirse a la estructura del paquete:

```python
from . import aritmetica          # mismo paquete
from .aritmetica import sumar     # un nombre de un módulo hermano
from ..utils import helper        # paquete padre
```

**Explicación línea por línea:**
- Un punto `.` = paquete actual.
- Dos puntos `..` = paquete padre.
- Tres puntos `...` = abuelo, etc.

**Limitación:** los imports relativos **solo funcionan dentro de paquetes**, no en un script ejecutado directamente. Si ejecutas el archivo como script, obtienes:

```text
ImportError: attempted relative import with no known parent package
```

*Solución:* ejecuta con `python -m paquete.modulo`.

### 3.4. Namespace packages

Python 3 permite paquetes **sin** `__init__.py`, llamados **namespace packages**. Sirven para dividir un paquete en varias carpetas físicas (por ejemplo, plugins distribuidos por separado). Para el 99 % de los proyectos, usa `__init__.py`.

### 3.5. Errores comunes

**Error 1 — Hacer trabajo pesado en `__init__.py`.**

Si `__init__.py` abre conexiones o lee archivos, **cada import** paga ese coste. *Solución:* mantén `__init__.py` ligero.

**Error 2 — Imports relativos en un script directo.**

*Solución:* usa `python -m`.

**Error 3 — `__all__` desactualizado.**

*Solución:* mantenlo en sincronía con la API pública.

### 3.6. Checkpoint de comprensión

1. ¿Cuándo se ejecuta `__init__.py`?
2. ¿Qué hace `__all__`?
3. ¿Qué significan `.` y `..` en un import?

### 3.7. Ejercicio propuesto

**Ejercicio 3.1.** Define `__all__` en el paquete `geometria` y expón solo las funciones principales.

**Pista:** `__all__` controla qué importa `from paquete import *`.

---

## 4. Importlib

### 4.1. ¿Por qué importar dinámicamente?

Normalmente escribes `import modulo` y ya está. Pero a veces el nombre del módulo **no se conoce hasta la ejecución**: por ejemplo, un sistema de **plugins** que carga todos los archivos de una carpeta. Para eso está `importlib`.

### 4.2. Importar por nombre

```python
import importlib

nombre = "math"
modulo = importlib.import_module(nombre)
print(modulo.sqrt(9))
```

Salida esperada:

```text
3.0
```

**Explicación línea por línea:**
- `importlib.import_module(nombre)` importa el módulo cuyo nombre está en la variable.
- Es equivalente a `import math`, pero el nombre se decide en tiempo de ejecución.

### 4.3. Recargar un módulo

Durante el desarrollo, si modificas un módulo ya importado, Python no lo recarga solo. Puedes forzarlo:

```python
import importlib
import mi_modulo

importlib.reload(mi_modulo)
```

**Explicación línea por línea:**
- `importlib.reload(modulo)` vuelve a ejecutar el módulo y actualiza su contenido.

**Advertencia:** recargar módulos con estado global puede duplicar datos. Úsalo con cuidado, sobre todo en el REPL.

### 4.4. El patrón de plugins

```python
import importlib
from pathlib import Path


def cargar_plugins(directorio: str) -> dict:
    """Carga dinámicamente todos los módulos .py de un directorio."""
    plugins = {}
    for ruta in Path(directorio).glob("*.py"):
        if ruta.stem.startswith("_"):
            continue
        mod = importlib.import_module(ruta.stem)
        plugins[ruta.stem] = mod
    return plugins
```

**Explicación línea por línea:**
- `Path(directorio).glob("*.py")` recorre todos los archivos `.py`.
- `ruta.stem` es el nombre sin extensión.
- Se saltan los que empiezan por `_` (internos).
- `importlib.import_module` carga cada uno y se guarda en un diccionario.

**Precaución de seguridad:** no importes módulos a partir de nombres que vengan de entrada no confiable: importar ejecuta código arbitrario.

### 4.5. Errores comunes

**Error 1 — Recargar módulos con estado global.**

Puede duplicar datos o dejar referencias obsoletas.

**Error 2 — Importar nombres no confiables.**

*Solución:* valida los nombres contra una lista permitida.

### 4.6. Checkpoint de comprensión

1. ¿Cuándo necesitas `importlib` en lugar de `import`?
2. ¿Qué hace `importlib.reload`?
3. ¿Qué riesgo de seguridad tiene importar por nombre?

### 4.7. Ejercicio propuesto

**Ejercicio 4.1.** Pide al usuario el nombre de un módulo de la biblioteca estándar y muestra su docstring.

**Pista:** `importlib.import_module(nombre).__doc__`.

---

## 5. Publicación de paquetes en PyPI

### 5.1. ¿Qué es publicar un paquete?

Publicar un paquete significa subirlo a **PyPI** para que cualquiera pueda instalarlo con `pip install nombre`. Es la forma de compartir tu código con el mundo.

### 5.2. Requisitos

1. Una estructura de proyecto con `pyproject.toml`.
2. Construir las distribuciones (`sdist` y `wheel`).
3. Subirlas a PyPI (o TestPyPI para pruebas).

### 5.3. `pyproject.toml`

Es el archivo moderno de configuración de un paquete:

```toml
[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"

[project]
name = "mi-paquete"
version = "0.1.0"
description = "Un paquete de ejemplo"
readme = "README.md"
requires-python = ">=3.10"
authors = [{ name = "Ana", email = "ana@example.com" }]
license = { text = "MIT" }
dependencies = ["requests>=2.31"]
```

**Explicación línea por línea:**
- `[build-system]` indica con qué herramienta construir el paquete.
- `[project]` describe el paquete: nombre, versión, descripción, autor, licencia.
- `dependencies` lista las librerías que necesita.

### 5.4. Construir y publicar

```bash
pip install build twine

python -m build
# genera dist/mi_paquete-0.1.0.tar.gz y dist/mi_paquete-0.1.0-py3-none-any.whl

twine check dist/*
twine upload --repository testpypi dist/*   # prueba en TestPyPI
twine upload dist/*                          # publicación real
```

**Explicación línea por línea:**
- `python -m build` crea las distribuciones en `dist/`.
- `twine check` valida que los metadatos sean correctos.
- `twine upload --repository testpypi` sube a TestPyPI (entorno de pruebas).
- `twine upload` sube a PyPI (real).

### 5.5. Instalar tu propio paquete

Una vez publicado:

```bash
pip install mi-paquete
```

### 5.6. Errores comunes

**Error 1 — Nombre ya tomado en PyPI.**

*Solución:* comprueba antes de publicar y elige un nombre único.

**Error 2 — Re-publicar la misma versión.**

```text
HTTPError: 400 Bad Request ... File already exists
```

*Solución:* incrementa la versión en `pyproject.toml` antes de volver a construir.

**Error 3 — Incluir secretos o `.venv`.**

*Solución:* revisa el contenido del wheel y usa `.gitignore`.

### 5.7. Checkpoint de comprensión

1. ¿Qué dos tipos de distribución genera `python -m build`?
2. ¿Por qué se prueba primero en TestPyPI?
3. ¿Qué debes hacer antes de re-publicar?

### 5.8. Ejercicio propuesto

**Ejercicio 5.1.** Crea un `pyproject.toml` para tu paquete `geometria` y constrúyelo con `python -m build`.

**Pista:** no lo subas a PyPI todavía; solo verifica que se construye.

---

## 6. Ejemplo integrador

Construiremos una **calculadora modular** repartida en un paquete con API pública.

**Estructura:**

```text
calculadora/
├── __init__.py
├── operaciones.py
└── main.py
```

**`calculadora/operaciones.py`:**

```python
"""Operaciones aritméticas básicas."""


def sumar(a: float, b: float) -> float:
    """Suma dos números."""
    return a + b


def dividir(a: float, b: float) -> float:
    """Divide a entre b.

    Raises:
        ZeroDivisionError: Si b es cero.
    """
    if b == 0:
        raise ZeroDivisionError("No se puede dividir entre cero")
    return a / b
```

**`calculadora/__init__.py`:**

```python
"""Paquete calculadora."""

from .operaciones import dividir, sumar

__all__ = ["sumar", "dividir"]
__version__ = "0.1.0"
```

**`calculadora/main.py`:**

```python
"""Punto de entrada de la calculadora."""

from calculadora import dividir, sumar


def main() -> None:
    print(sumar(5, 3))
    print(dividir(10, 4))


if __name__ == "__main__":
    main()
```

Ejecución:

```bash
python -m calculadora.main
```

Salida esperada:

```text
8
2.5
```

**Explicación línea por línea:**
- `operaciones.py` define las funciones puras.
- `__init__.py` las expone en la API pública del paquete con `__all__`.
- `main.py` las importa **desde el paquete** (`from calculadora import ...`), no desde el módulo interno. Así, si mañana reorganizas los módulos, `main.py` no cambia.
- `python -m calculadora.main` ejecuta el módulo resolviendo los imports correctamente.

**Conceptos integrados:** paquete, `__init__.py`, `__all__`, imports relativos, `python -m`.

---

## 7. Buenas prácticas

1. **Un módulo, una responsabilidad.**
2. **Evita `from modulo import *`.**
3. **Ordena los imports** (stdlib, terceros, locales).
4. **Expón una API pública** clara desde `__init__.py`.
5. **Usa `__all__`** para delimitar la API.
6. **Nombra paquetes en `snake_case`** y sin guiones.
7. **No hagas trabajo pesado en `__init__.py`.**
8. **Documenta cada módulo** con un docstring inicial.
9. **Ejecuta paquetes con `python -m`.**
10. **Evita imports circulares** por diseño.

---

## 8. Recursos

- **Módulos oficial:** <https://docs.python.org/3/tutorial/modules.html>
- **`importlib`:** <https://docs.python.org/3/library/importlib.html>
- **Empaquetado:** <https://packaging.python.org/>
- **PEP 8 (imports):** <https://peps.python.org/pep-0008/#imports>
- **`pyproject.toml`:** <https://peps.python.org/pep-0621/>

---

## 9. Mini resumen final

- Un **módulo** es un `.py`; un **paquete**, una carpeta de módulos.
- `import`, `from ... import ...` y alias cubren las formas de importar.
- `__init__.py` inicializa el paquete y define su API con `__all__`.
- Los **imports relativos** (`.`, `..`) funcionan dentro de paquetes.
- `importlib` permite importar y recargar módulos dinámicamente.
- Publicar en **PyPI** requiere `pyproject.toml`, `build` y `twine`.

---

### 🎯 Retos opcionales

1. **Reto 1:** Convierte el ejemplo integrador en un paquete instalable con `pyproject.toml`.
2. **Reto 2:** Implementa un cargador de plugins con `importlib` y `pkgutil`.
3. **Reto 3:** Añade tests al paquete y ejecútalos con `pytest`.

**Anterior:** [06_errores_excepciones.md](06_errores_excepciones.md) · **Siguiente:** [08_python_avanzado.md](08_python_avanzado.md)
