# 06 · Manejo de Errores y Excepciones

**Versión recomendada:** Python 3.12
**Requisito:** [05_poo.md](05_poo.md)
**Nivel:** Intermedio
**Duración estimada de estudio:** 5–7 horas

---

## Índice de contenidos

1. [try, except, else, finally](#1-try-except-else-finally)
2. [Excepciones personalizadas](#2-excepciones-personalizadas)
3. [raise y jerarquía de excepciones](#3-raise-y-jerarquía-de-excepciones)
4. [Context managers (with)](#4-context-managers-with)
5. [Logging y depuración](#5-logging-y-depuración)
6. [Ejemplo integrador](#6-ejemplo-integrador)
7. [Buenas prácticas](#7-buenas-prácticas)
8. [Recursos](#8-recursos)
9. [Mini resumen final](#9-mini-resumen-final)

---

## 1. try, except, else, finally

### 1.1. ¿Qué es una excepción?

Cuando un programa hace algo imposible —dividir entre cero, abrir un archivo que no existe, convertir `"abc"` a número— Python **lanza una excepción**: un objeto que representa el error. Si nadie lo captura, el programa se detiene con un *traceback* (el informe del error).

**Analogía:** una excepción es como una alarma de incendios. Cuando algo va mal, suena. Si nadie la atiende, todo se detiene. Pero puedes **preparar un protocolo**: "si suena la alarma en la cocina, usa el extintor". Capturar excepciones es tener ese protocolo.

Manejar errores no es opcional en software real: los datos de entrada son impredecibles, los archivos faltan, las redes fallan. Un programa robusto **prevé** los errores.

### 1.2. La estructura completa

```python
try:
    # código que puede fallar
except TipoDeError:
    # qué hacer si falla
else:
    # se ejecuta si NO hubo error
finally:
    # se ejecuta SIEMPRE (haya error o no)
```

### 1.3. Un ejemplo básico

```python
try:
    numero = int("abc")
except ValueError:
    print("No es un número válido")
```

Salida esperada:

```text
No es un número válido
```

**Explicación línea por línea:**
- `try:` inicia el bloque "vigilado".
- `int("abc")` lanza `ValueError` porque `"abc"` no es un número.
- `except ValueError:` captura **ese** error concreto y ejecuta su bloque.
- El programa **continúa** en lugar de detenerse.

**Sin el `try`**, el programa mostraría el traceback y terminaría:

```text
Traceback (most recent call last):
  File "ejemplo.py", line 1, in <module>
    numero = int("abc")
ValueError: invalid literal for int() with base 10: 'abc'
```

### 1.4. La estructura completa en acción

```python
def dividir(a: float, b: float) -> float:
    """Divide a entre b capturando el error de división."""
    try:
        resultado = a / b
    except ZeroDivisionError:
        print("Error: división entre cero")
        return 0.0
    else:
        print("División correcta")
        return resultado
    finally:
        print("Operación finalizada")


print(dividir(10, 2))
print(dividir(10, 0))
```

Salida esperada:

```text
División correcta
Operación finalizada
5.0
Error: división entre cero
Operación finalizada
0.0
```

**Explicación línea por línea — observa el orden:**
- En `dividir(10, 2)`: el `try` tiene éxito, luego corre el `else` ("División correcta"), y por último el `finally` ("Operación finalizada").
- En `dividir(10, 0)`: el `try` falla, se ejecuta el `except` ("Error..."), y luego el `finally`.
- **El `finally` se ejecuta SIEMPRE**, haya error o no. Es perfecto para liberar recursos.

**Regla clave:** `else` corre **solo** si no hubo excepción; `finally` corre **siempre**.

### 1.5. Capturar varias excepciones

Puedes capturar distintos tipos de error en el mismo bloque:

```python
def convertir(valor: str) -> float:
    try:
        return 10 / int(valor)
    except (ValueError, ZeroDivisionError) as e:
        print(f"Error: {e}")
        return 0.0


print(convertir("2"))
print(convertir("abc"))
print(convertir("0"))
```

Salida esperada:

```text
5.0
Error: invalid literal for int() with base 10: 'abc'
0.0
Error: division by zero
0.0
```

**Explicación línea por línea:**
- `except (ValueError, ZeroDivisionError) as e:` captura **ambos** tipos y guarda la excepción en `e`.
- `e` contiene el mensaje del error, que se imprime.

También puedes tener varios `except` separados, cada uno con su lógica:

```python
try:
    valor = int(input("Número: "))
    print(10 / valor)
except ValueError:
    print("No es un número")
except ZeroDivisionError:
    print("No puedo dividir entre cero")
```

### 1.6. La captura genérica (con cuidado)

```python
try:
    x = 1 / 0
except Exception as e:
    print(f"Ocurrió: {type(e).__name__}: {e}")
```

Salida esperada:

```text
Ocurrió: ZeroDivisionError: division by zero
```

**Explicación línea por línea:**
- `except Exception` captura **cualquier** excepción normal (no las del sistema).
- `type(e).__name__` da el nombre de la clase de la excepción.
- Úsalo como último recurso: capturar todo indiscriminadamente **oculta bugs**.

**Nunca uses `except:` a secas.** Captura incluso `KeyboardInterrupt` (Ctrl+C), lo que impide interrumpir el programa.

### 1.7. Errores comunes

**Error 1 — Capturar todo sin distinguir.**

```python
try:
    hacer_algo()
except:              # ❌ demasiado amplio
    pass
```

*Problema:* oculta también errores de programación (typos, variables mal escritas). *Solución:* captura tipos específicos.

**Error 2 — `try` demasiado grande.**

```python
try:
    abrir_conexion()
    procesar_datos()
    guardar_resultado()
    enviar_email()
except Exception:
    ...
```

*Problema:* no sabes qué falló. *Solución:* envuelve solo la operación que puede fallar.

**Error 3 — Silenciar la excepción con `pass`.**

```python
try:
    operacion_critica()
except Exception:
    pass   # ❌ el error desaparece sin dejar rastro
```

*Solución:* registra el error (con `logging`) o vuelve a lanzarlo.

### 1.8. Checkpoint de comprensión

1. ¿Cuándo se ejecuta el `else`? ¿Y el `finally`?
2. ¿Por qué es mala idea usar `except:` a secas?
3. ¿Qué significa "el `try` demasiado grande"?

### 1.9. Ejercicio propuesto

**Ejercicio 1.1.** Pide un número al usuario y repite hasta que sea válido.

**Pista:** envuelve `int(input(...))` en un `try/except ValueError` dentro de un `while True`.

---

## 2. Excepciones personalizadas

### 2.1. ¿Por qué crear tus propias excepciones?

Las excepciones de Python (`ValueError`, `KeyError`...) describen errores **genéricos**. Pero tu aplicación tiene errores **propios**: "saldo insuficiente", "usuario duplicado", "código de descuento caducado". Crear excepciones personalizadas hace que el código sea más expresivo y permite manejarlas de forma específica.

**Analogía:** los errores genéricos son como "algo salió mal". Una excepción propia es como "la tarjeta fue rechazada por fondos insuficientes": te dice exactamente qué pasó.

### 2.2. Definir una excepción propia

```python
class SaldoInsuficienteError(Exception):
    """Se lanza cuando el saldo no alcanza para la operación."""

    def __init__(self, saldo: float, solicitado: float) -> None:
        self.saldo = saldo
        self.solicitado = solicitado
        super().__init__(
            f"Saldo insuficiente: tienes {saldo}, necesitas {solicitado}"
        )


class Cuenta:
    def __init__(self, saldo: float) -> None:
        self.saldo = saldo

    def retirar(self, monto: float) -> None:
        if monto > self.saldo:
            raise SaldoInsuficienteError(self.saldo, monto)
        self.saldo -= monto


cuenta = Cuenta(100)
try:
    cuenta.retirar(150)
except SaldoInsuficienteError as e:
    print(e)
    print(f"Faltan {e.solicitado - e.saldo}")
```

Salida esperada:

```text
Saldo insuficiente: tienes 100, necesitas 150
Faltan 50
```

**Explicación línea por línea:**
- `class SaldoInsuficienteError(Exception):` hereda de `Exception`, lo que la convierte en una excepción válida.
- `__init__` guarda datos **extra** (saldo y solicitado) además del mensaje.
- `super().__init__(mensaje)` inicializa la excepción con un mensaje legible.
- `raise SaldoInsuficienteError(...)` **lanza** la excepción.
- El `except` la captura y accede a sus atributos (`e.saldo`, `e.solicitado`).

**Ventaja:** el `except` no solo sabe que falló, sino **con qué datos**, lo que permite reaccionar con precisión.

### 2.3. Jerarquía de excepciones propias

Lo ideal es crear una excepción **base** de tu aplicación y derivar las específicas:

```python
class AppError(Exception):
    """Base de todos los errores de la aplicación."""


class ValidacionError(AppError):
    """Error de validación de datos."""


class PersistenciaError(AppError):
    """Error al guardar o cargar datos."""


try:
    raise ValidacionError("Campo obligatorio")
except AppError as e:   # captura cualquiera de las subclases
    print(type(e).__name__, "-", e)
```

Salida esperada:

```text
ValidacionError - Campo obligatorio
```

**Explicación línea por línea:**
- `ValidacionError` y `PersistenciaError` heredan de `AppError`.
- `except AppError` captura **cualquiera** de las subclases. Así puedes capturar "todos los errores de mi app" o "solo los de validación", según necesites.

### 2.4. Errores comunes

**Error 1 — Heredar de `BaseException`.**

`BaseException` es la raíz de todo, incluidos `SystemExit` y `KeyboardInterrupt`. *Solución:* hereda de `Exception`.

**Error 2 — Crear excepciones sin mensaje.**

Una excepción sin mensaje es inútil al depurar. *Solución:* pasa siempre un mensaje descriptivo.

**Error 3 — Crear una excepción para cada cosa.**

No conviertas el flujo normal en excepciones. Úsalas para situaciones **excepcionales**.

### 2.5. Checkpoint de comprensión

1. ¿Por qué crear excepciones propias en lugar de usar las genéricas?
2. ¿De qué clase deben heredar?
3. ¿Qué ventaja tiene una excepción base de la aplicación?

### 2.6. Ejercicio propuesto

**Ejercicio 2.1.** Define `EdadInvalidaError` y úsala en una función `validar_edad(edad)`.

**Pista:** hereda de `ValueError` si encaja semánticamente (es un valor inválido).

---

## 3. raise y jerarquía de excepciones

### 3.1. `raise`: lanzar excepciones

`raise` lanza una excepción. Puedes lanzar las integradas o las tuyas:

```python
def validar_edad(edad: int) -> None:
    if edad < 0:
        raise ValueError("La edad no puede ser negativa")
    if edad > 120:
        raise ValueError("La edad parece irreal")


validar_edad(-5)
```

Salida esperada:

```text
Traceback (most recent call last):
  ...
ValueError: La edad no puede ser negativa
```

**Explicación línea por línea:**
- `raise ValueError(...)` interrumpe la ejecución y propaga el error hacia arriba, hasta que alguien lo capture o el programa termine.

### 3.2. La jerarquía de excepciones

Las excepciones forman un **árbol**. Entenderlo te permite capturar en el nivel adecuado:

```text
BaseException
 ├── SystemExit
 ├── KeyboardInterrupt
 ├── GeneratorExit
 └── Exception
      ├── ArithmeticError
      │    └── ZeroDivisionError
      ├── LookupError
      │    ├── IndexError
      │    └── KeyError
      ├── ValueError
      ├── TypeError
      ├── OSError
      │    └── FileNotFoundError
      └── RuntimeError
```

**Explicación:**
- `IndexError` es un tipo de `LookupError`, que a su vez es un `Exception`. Por eso `except Exception` captura todos.
- `ZeroDivisionError` es un `ArithmeticError`.
- `FileNotFoundError` es un `OSError`.

**Consecuencia práctica:** si capturas `LookupError`, capturas tanto `IndexError` como `KeyError`.

### 3.3. Encadenar excepciones con `from`

Cuando conviertes un error en otro, es buena práctica conservar la **causa original**:

```python
def procesar(valor: str) -> int:
    try:
        return int(valor)
    except ValueError as e:
        raise ValueError(f"Valor inválido: {valor!r}") from e


try:
    procesar("abc")
except ValueError as e:
    print(e)
    print("Causa:", e.__cause__)
```

Salida esperada:

```text
Valor inválido: 'abc'
Causa: invalid literal for int() with base 10: 'abc'
```

**Explicación línea por línea:**
- `raise ... from e` indica que la nueva excepción fue **causada** por `e`.
- `e.__cause__` accede a la causa original.
- Sin `from`, perderías el rastro de dónde vino realmente el problema.

### 3.4. Re-lanzar con `raise` sin argumentos

Dentro de un `except`, `raise` a secas vuelve a lanzar la excepción actual:

```python
try:
    raise KeyError("clave")
except KeyError:
    print("Registrando el error...")
    raise   # vuelve a lanzar la misma excepción
```

Salida esperada:

```text
Registrando el error...
Traceback (most recent call last):
  ...
KeyError: 'clave'
```

**Explicación línea por línea:**
- Se registra el error (útil para logging) y luego se **re-lanza** para que quien llamó se entere.
- Este patrón es común: "haz algo y deja que el error siga subiendo".

### 3.5. Errores comunes

**Error 1 — Usar excepciones para control de flujo normal.**

Las excepciones son para casos **excepcionales**, no para decisiones cotidianas. Si esperas un error con frecuencia, usa condicionales.

**Error 2 — Capturar y lanzar otra sin `from`.**

*Solución:* usa `raise Nueva(...) from e` para no perder la causa.

**Error 3 — Lanzar `Exception` genérica.**

*Solución:* sé específico (`ValueError`, tu excepción propia...).

### 3.6. Checkpoint de comprensión

1. ¿Qué hace `raise`?
2. ¿Por qué `IndexError` se puede capturar con `except LookupError`?
3. ¿Para qué sirve `raise ... from e`?

### 3.7. Ejercicio propuesto

**Ejercicio 3.1.** Envuelve una lectura de archivo y re-lanza un error propio con `from`.

**Pista:** captura `OSError` y lanza `PersistenciaError(...) from e`.

---

## 4. Context managers (with)

### 4.1. El problema de los recursos

Muchos recursos deben **abrirse** y luego **cerrarse**: archivos, conexiones de red, transacciones. Si olvidas cerrarlos, tienes fugas de memoria, archivos bloqueados o conexiones colgadas. Peor aún: si ocurre un error a mitad, puede que nunca llegues a cerrarlos.

Los **context managers** resuelven esto: garantizan la **adquisición** y la **liberación** del recurso, pase lo que pase.

**Analogía:** un context manager es como una puerta con cierre automático. La abres, entras, y se cierra sola al salir, **aunque salgas corriendo por una emergencia**.

### 4.2. El ejemplo clásico: archivos

```python
with open("datos.txt", "w", encoding="utf-8") as archivo:
    archivo.write("Hola, mundo\n")
# el archivo se cierra automáticamente aquí

with open("datos.txt", "r", encoding="utf-8") as archivo:
    contenido = archivo.read()
print(contenido.strip())
```

Salida esperada:

```text
Hola, mundo
```

**Explicación línea por línea:**
- `with open(...) as archivo:` abre el archivo y lo asigna a `archivo`.
- Al salir del bloque `with` (incluso si hubo una excepción), el archivo **se cierra automáticamente**.
- `encoding="utf-8"` asegura que los acentos se manejen bien.
- `"w"` = escritura (sobrescribe); `"r"` = lectura; `"a"` = añadir.

**Sin `with`**, tendrías que cerrar manualmente y arriesgarte a olvidarlo:

```python
archivo = open("datos.txt")
try:
    contenido = archivo.read()
finally:
    archivo.close()   # hay que recordarlo SIEMPRE
```

El `with` es más limpio y seguro.

### 4.3. Crear tu propio context manager

Implementando `__enter__` y `__exit__`:

```python
class Temporizador:
    """Mide el tiempo de un bloque de código."""

    import time

    def __enter__(self):
        self.inicio = self.time.perf_counter()
        return self

    def __exit__(self, exc_type, exc_val, exc_tb):
        self.fin = self.time.perf_counter()
        print(f"Tardó {self.fin - self.inicio:.4f}s")
        return False   # no suprime excepciones


with Temporizador():
    sum(range(1_000_000))
```

Salida esperada (el tiempo varía):

```text
Tardó 0.0123s
```

**Explicación línea por línea:**
- `__enter__` se ejecuta al entrar en el `with`. Aquí inicia el cronómetro y devuelve `self`.
- `__exit__` se ejecuta al salir. Aquí calcula y muestra el tiempo.
- `return False` indica que **no** se suprimen las excepciones (si hubo una, se propaga).
- Si `__exit__` devolviera `True`, la excepción se "tragaría".

### 4.4. La forma sencilla: `contextlib.contextmanager`

Para no escribir una clase entera, usa el decorador `@contextmanager`:

```python
from contextlib import contextmanager


@contextmanager
def etiqueta(nombre: str):
    """Imprime etiquetas de apertura y cierre."""
    print(f"<{nombre}>")
    try:
        yield
    finally:
        print(f"</{nombre}>")


with etiqueta("seccion"):
    print("Contenido")
```

Salida esperada:

```text
<seccion>
Contenido
</seccion>
```

**Explicación línea por línea:**
- Todo lo anterior al `yield` es el "entrar".
- El `yield` es donde se ejecuta el cuerpo del `with`.
- El `finally` garantiza que el "cerrar" se ejecute siempre.

### 4.5. Errores comunes

**Error 1 — Olvidar cerrar recursos.**

*Solución:* usa `with` en lugar de `open()`/`close()` manual.

**Error 2 — `__exit__` que devuelve `True` sin querer.**

*Síntoma:* las excepciones desaparecen silenciosamente. *Solución:* devuelve `False` (o nada) salvo que quieras suprimir.

**Error 3 — Hacer trabajo pesado en `__enter__` que puede fallar.**

*Solución:* maneja los errores dentro del context manager o documenta que puede fallar.

### 4.6. Checkpoint de comprensión

1. ¿Qué garantiza un context manager?
2. ¿Qué métodos implementa un context manager de clase?
3. ¿Cómo crearías uno rápidamente con `contextlib`?

### 4.7. Ejercicio propuesto

**Ejercicio 4.1.** Escribe un context manager `cambio_directorio(ruta)` con `contextlib`.

**Pista:** guarda el directorio actual, haz `chdir`, y restáuralo en el `finally`.

---

## 5. Logging y depuración

### 5.1. Por qué no usar `print`

Al principio, todos usamos `print` para entender qué pasa. Pero en proyectos reales, `print` se queda corto:

- No distingue niveles (información vs. error).
- No añade marcas de tiempo.
- No se puede filtrar ni dirigir a un archivo.
- Hay que borrarlo antes de producción.

El módulo **`logging`** resuelve todo esto: niveles, formato, destinos configurables.

### 5.2. Los niveles de log

| Nivel | Uso |
|---|---|
| `DEBUG` | Detalles de diagnóstico |
| `INFO` | Confirmación de funcionamiento normal |
| `WARNING` | Algo inesperado pero no crítico |
| `ERROR` | Fallo en una operación |
| `CRITICAL` | Fallo grave del sistema |

Cada nivel incluye a los superiores: si configuras `INFO`, se muestran INFO, WARNING, ERROR y CRITICAL (pero no DEBUG).

### 5.3. Configuración básica

```python
import logging

logging.basicConfig(
    level=logging.INFO,
    format="%(asctime)s [%(levelname)s] %(message)s",
)

logging.debug("Esto no se muestra (nivel INFO)")
logging.info("Programa iniciado")
logging.warning("Queda poco espacio")
logging.error("No se pudo conectar")
```

Salida esperada:

```text
2026-09-16 10:30:45,123 [INFO] Programa iniciado
2026-09-16 10:30:45,123 [WARNING] Queda poco espacio
2026-09-16 10:30:45,123 [ERROR] No se pudo conectar
```

**Explicación línea por línea:**
- `basicConfig` configura el logger global: nivel y formato.
- `%(asctime)s` inserta la fecha y hora; `%(levelname)s`, el nivel; `%(message)s`, el mensaje.
- `logging.debug(...)` **no** se muestra porque el nivel mínimo es INFO.

### 5.4. Un logger por módulo

La práctica recomendada es crear un logger con el nombre del módulo:

```python
import logging

logger = logging.getLogger(__name__)


def procesar(dato: str) -> None:
    logger.info("Procesando %s", dato)
    try:
        int(dato)
    except ValueError:
        logger.exception("Fallo al convertir %s", dato)


logging.basicConfig(level=logging.INFO)
procesar("abc")
```

Salida esperada:

```text
... [INFO] Procesando abc
... [ERROR] Fallo al convertir abc
Traceback (most recent call last):
  ...
ValueError: invalid literal for int() with base 10: 'abc'
```

**Explicación línea por línea:**
- `getLogger(__name__)` crea (o recupera) un logger con el nombre del módulo. Así sabes de dónde viene cada mensaje.
- `logger.exception(...)` registra el mensaje **y** el traceback completo. Úsalo dentro de los `except`.
- `logger.info("...%s", dato)` usa formato diferido: el `%s` se sustituye solo si el mensaje se va a registrar (más eficiente).

### 5.5. Registrar en un archivo

```python
logging.basicConfig(
    filename="app.log",
    level=logging.DEBUG,
    format="%(asctime)s [%(levelname)s] %(name)s: %(message)s",
)
```

Ahora los mensajes van al archivo `app.log` en lugar de la consola.

### 5.6. Depuración con `breakpoint()`

Python incluye un depurador interactivo (`pdb`). Puedes pausar el programa en cualquier punto:

```python
def suma(a, b):
    breakpoint()   # el programa se pausa aquí
    return a + b
```

Al ejecutar, entras en el depurador. Comandos útiles:

| Comando | Acción |
|---|---|
| `n` | Siguiente línea (*next*) |
| `s` | Entrar en la función (*step*) |
| `c` | Continuar (*continue*) |
| `p variable` | Imprimir el valor de una variable |
| `q` | Salir (*quit*) |

**¿Por qué `breakpoint()` y no `pdb.set_trace()`?** Porque `breakpoint()` es la forma moderna y respeta la variable de entorno `PYTHONBREAKPOINT`.

### 5.7. Errores comunes

**Error 1 — Dejar `print` como sistema de logs.**

*Solución:* usa `logging` desde el principio.

**Error 2 — Registrar datos sensibles.**

Nunca registres contraseñas, tokens ni datos personales. Los logs pueden filtrarse.

**Error 3 — Olvidar `logger.exception` en los `except`.**

*Síntoma:* ves el mensaje pero no el traceback, y no sabes dónde falló. *Solución:* usa `logger.exception`.

### 5.8. Checkpoint de comprensión

1. ¿Qué ventaja tiene `logging` sobre `print`?
2. ¿Qué hace `logger.exception` que no hace `logger.error`?
3. ¿Para qué sirve `breakpoint()`?

### 5.9. Ejercicio propuesto

**Ejercicio 5.1.** Configura logging a un archivo `app.log` con nivel `DEBUG`.

**Pista:** usa `logging.basicConfig(filename="app.log", ...)`.

---

## 6. Ejemplo integrador

Construiremos un **lector de configuración robusto** que combina excepciones personalizadas, context managers, `pathlib` y logging.

```python
"""Carga de configuración desde un archivo JSON."""

import json
import logging
from pathlib import Path

logger = logging.getLogger(__name__)


class ConfigError(Exception):
    """Error al cargar la configuración."""


def cargar_config(ruta: str) -> dict:
    """Carga un archivo JSON de configuración.

    Args:
        ruta: Ruta al archivo JSON.

    Returns:
        Diccionario con la configuración.

    Raises:
        ConfigError: Si el archivo no existe o no es JSON válido.
    """
    path = Path(ruta)
    try:
        with path.open(encoding="utf-8") as f:
            return json.load(f)
    except FileNotFoundError as e:
        logger.error("No existe %s", ruta)
        raise ConfigError(f"Configuración no encontrada: {ruta}") from e
    except json.JSONDecodeError as e:
        logger.error("JSON inválido en %s", ruta)
        raise ConfigError(f"JSON inválido: {e}") from e


def main() -> None:
    logging.basicConfig(level=logging.INFO)
    Path("config.json").write_text('{"debug": true}', encoding="utf-8")
    try:
        config = cargar_config("config.json")
        print("Config:", config)
        cargar_config("no_existe.json")
    except ConfigError as e:
        print("Error controlado:", e)


if __name__ == "__main__":
    main()
```

Salida esperada:

```text
Config: {'debug': True}
Error controlado: Configuración no encontrada: no_existe.json
```

**Explicación línea por línea:**
- `ConfigError` es una excepción propia para los fallos de configuración.
- `path.open(encoding="utf-8")` es un context manager: cierra el archivo automáticamente.
- Se capturan dos errores distintos (`FileNotFoundError` y `JSONDecodeError`) y se convierten en `ConfigError` con `from e`, conservando la causa.
- `logger.error` registra el fallo; el `print` muestra al usuario un mensaje controlado.
- El programa **no se cae**: maneja el error con elegancia.

**Conceptos integrados:** excepción personalizada, `raise ... from`, context manager (`with`), `pathlib`, logging, `try/except`.

---

## 7. Buenas prácticas

1. **Captura solo lo que puedas manejar.**
2. **Sé específico** con los tipos de excepción.
3. **Usa `from`** al re-lanzar para preservar la causa.
4. **Prefiere `with`** para recursos.
5. **Registra con `logging`**, no con `print`.
6. **No silencies** excepciones con `pass`.
7. **Usa `finally`** para limpieza crítica.
8. **Crea excepciones propias** para errores de dominio.
9. **No uses excepciones** para el flujo normal.

---

## 8. Recursos

- **Errores oficial:** <https://docs.python.org/3/tutorial/errors.html>
- **`logging`:** <https://docs.python.org/3/library/logging.html>
- **`contextlib`:** <https://docs.python.org/3/library/contextlib.html>
- **PEP 3134 (encadenamiento):** <https://peps.python.org/pep-3134/>

---

## 9. Mini resumen final

- `try/except/else/finally` estructura el manejo de errores.
- Las **excepciones personalizadas** modelan errores de dominio.
- `raise ... from` encadena causas; la jerarquía permite capturas amplias.
- Los **context managers** (`with`) liberan recursos de forma segura.
- **`logging`** reemplaza a `print` con niveles y formato.
- `breakpoint()` y `pdb` permiten depurar paso a paso.

---

### 🎯 Retos opcionales

1. **Reto 1:** Crea un decorador `@manejar_errores` que capture excepciones y las registre.
2. **Reto 2:** Implementa un context manager que mida y registre el tiempo de ejecución.
3. **Reto 3:** Diseña una jerarquía de excepciones para un sistema de pedidos.

**Anterior:** [05_poo.md](05_poo.md) · **Siguiente:** [07_modulos_paquetes.md](07_modulos_paquetes.md)
