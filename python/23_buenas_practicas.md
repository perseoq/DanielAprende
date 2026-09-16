# 23 · Buenas Prácticas y Patrones

**Versión recomendada:** Python 3.12
**Requisito:** todas las Partes anteriores
**Nivel:** Intermedio → Avanzado
**Duración estimada de estudio:** 10–14 horas

---

## Índice de contenidos

1. [Estructura de proyectos](#1-estructura-de-proyectos)
2. [Clean Architecture en Python](#2-clean-architecture-en-python)
3. [Patrones de diseño (GoF)](#3-patrones-de-diseño-gof)
4. [SOLID en Python](#4-solid-en-python)
5. [Testing y TDD](#5-testing-y-tdd)
6. [CI/CD](#6-cicd-con-github-actions-gitlab-ci)
7. [Docker y contenedores](#7-docker-y-contenedores)
8. [Documentación con Sphinx y MkDocs](#8-documentación-con-sphinx-y-mkdocs)
9. [Linting y formateo](#9-linting-y-formateo)
10. [Type checking con mypy](#10-type-checking-con-mypy)
11. [Recursos](#11-recursos)
12. [Mini resumen final](#12-mini-resumen-final)

---

## 1. Estructura de proyectos

### 1.1. Por qué importa la estructura

Un proyecto bien estructurado es más fácil de entender, mantener y extender. La estructura **comunica** cómo está organizado el código antes de leer una sola línea.

### 1.2. Src layout (recomendado para librerías)

```text
mi_proyecto/
├── .venv/
├── .gitignore
├── pyproject.toml
├── README.md
├── src/
│   └── mi_proyecto/
│       ├── __init__.py
│       ├── config.py
│       ├── domain/          # entidades y reglas de negocio
│       ├── services/        # casos de uso
│       ├── repositories/    # acceso a datos
│       └── api/             # interfaz (web/CLI)
├── tests/
│   ├── unit/
│   └── integration/
└── docs/
```

**Explicación de las capas:**
- `domain/`: el corazón del negocio, sin dependencias externas.
- `services/`: orquestan los casos de uso.
- `repositories/`: encapsulan el acceso a datos.
- `api/`: la interfaz (web, CLI...).

**¿Por qué `src/`?** Evita que importes tu paquete "por accidente" desde la carpeta actual sin instalarlo. Es el estilo recomendado por la comunidad.

### 1.3. Aplicación web

```text
mi_app/
├── app/
│   ├── __init__.py       # create_app()
│   ├── models/
│   ├── schemas/
│   ├── routers/
│   ├── services/
│   └── templates/
├── tests/
├── migrations/
├── requirements.txt
└── Dockerfile
```

### 1.4. Errores comunes

**Error 1 — Todo en un archivo.**

**Error 2 — Mezclar tests con código de producción.**

**Error 3 — Nombres de carpetas con guiones** (no importables).

### 1.5. Checkpoint de comprensión

1. ¿Qué ventaja aporta el `src layout`?
2. ¿Qué va en cada capa?

### 1.6. Ejercicio propuesto

**Ejercicio 1.1.** Estructura un proyecto de tareas con capas `domain`, `services` y `repositories`.

**Pista:** usa `src/`.

---

## 2. Clean Architecture en Python

### 2.1. La regla de dependencia

La **Clean Architecture** (Robert C. Martin) organiza el código en **capas concéntricas**:

```text
        [ Frameworks y drivers ]  ← web, BD, UI
        [ Interfaces/adaptadores ] ← repositorios, presentadores
        [ Casos de uso ]           ← lógica de aplicación
        [ Entidades ]              ← reglas de negocio
```

**La regla de dependencia:** las capas **internas no conocen las externas**. El dominio no sabe si lo usan Flask, Django o una GUI. Se comunican por **interfaces** (abstracciones).

**Analogía:** el núcleo (las reglas de negocio) es el "corazón"; las capas externas son "enchufes" que se conectan a él. Puedes cambiar el enchufe sin tocar el corazón.

### 2.2. Un ejemplo

**Entidad (dominio):**

```python
from dataclasses import dataclass


@dataclass
class Tarea:
    """Entidad de dominio."""

    id: int | None
    titulo: str
    completada: bool = False

    def completar(self) -> None:
        """Regla de negocio."""
        self.completada = True
```

**Interfaz del repositorio (puerto):**

```python
from abc import ABC, abstractmethod


class RepositorioTareas(ABC):
    """Puerto de persistencia."""

    @abstractmethod
    def guardar(self, tarea: Tarea) -> Tarea: ...

    @abstractmethod
    def listar(self) -> list[Tarea]: ...
```

**Caso de uso:**

```python
class CrearTarea:
    """Caso de uso: crear una tarea."""

    def __init__(self, repositorio: RepositorioTareas) -> None:
        self.repositorio = repositorio

    def ejecutar(self, titulo: str) -> Tarea:
        tarea = Tarea(id=None, titulo=titulo)
        return self.repositorio.guardar(tarea)
```

**Adaptador (implementación concreta):**

```python
class RepositorioSQLAlchemy(RepositorioTareas):
    def guardar(self, tarea: Tarea) -> Tarea:
        ...   # implementación con SQLAlchemy
```

**Explicación línea por línea:**
- `Tarea` es una entidad pura, sin dependencias.
- `RepositorioTareas` es una **interfaz** (puerto): define qué se necesita, no cómo.
- `CrearTarea` depende de la **abstracción**, no de SQLAlchemy.
- `RepositorioSQLAlchemy` es el **adaptador** concreto.

### 2.3. Beneficios

- **Testeable:** puedes usar repositorios en memoria para los tests.
- **Independiente del framework.**
- **Reglas de negocio aisladas.**

### 2.4. Errores comunes

**Error 1 — Aplicar Clean Architecture a un script pequeño.** Es sobre-ingeniería.

**Error 2 — Filtrar detalles del framework** en el dominio.

### 2.5. Checkpoint de comprensión

1. ¿Qué dice la regla de dependencia?
2. ¿Qué es un puerto y qué un adaptador?

### 2.6. Ejercicio propuesto

**Ejercicio 2.1.** Implementa un repositorio en memoria y otro en SQLite para el mismo puerto.

**Pista:** ambos implementan `RepositorioTareas`.

---

## 3. Patrones de diseño (GoF)

### 3.1. Qué son los patrones

Los **patrones de diseño** (*Gang of Four*) son soluciones **probadas y reutilizables** a problemas comunes. No son código copiable, sino **recetas** que adaptas.

**Analogía:** un patrón es como un plano de cocina: no es la comida, sino una forma probada de organizar el espacio para cocinar bien.

| Categoría | Patrones |
|---|---|
| Creacionales | Singleton, Factory, Builder |
| Estructurales | Adapter, Decorator, Facade |
| Comportamiento | Strategy, Observer, Command |

### 3.2. Singleton

Garantiza que una clase tenga **una sola instancia**:

```python
class Config:
    _instancia = None

    def __new__(cls):
        if cls._instancia is None:
            cls._instancia = super().__new__(cls)
            cls._instancia.valor = "config"
        return cls._instancia


print(Config() is Config())
```

Salida esperada:

```text
True
```

**Explicación línea por línea:**
- `__new__` controla la creación del objeto.
- Si ya existe una instancia, la reutiliza.
- **En Python, a menudo un módulo ya actúa como singleton** (se importa una sola vez).

### 3.3. Factory

Crea objetos sin que el cliente conozca la clase concreta:

```python
class Notificador:
    def enviar(self, mensaje: str) -> None: ...


class Email(Notificador):
    def enviar(self, mensaje: str) -> None:
        print("Email:", mensaje)


class SMS(Notificador):
    def enviar(self, mensaje: str) -> None:
        print("SMS:", mensaje)


def crear_notificador(tipo: str) -> Notificador:
    return {"email": Email, "sms": SMS}[tipo]()
```

**Explicación línea por línea:**
- `crear_notificador` decide qué clase instanciar.
- El cliente no necesita conocer `Email` ni `SMS`.

### 3.4. Strategy

Permite cambiar el algoritmo en tiempo de ejecución:

```python
from abc import ABC, abstractmethod


class Descuento(ABC):
    @abstractmethod
    def aplicar(self, total: float) -> float: ...


class SinDescuento(Descuento):
    def aplicar(self, total: float) -> float:
        return total


class DiezPorCiento(Descuento):
    def aplicar(self, total: float) -> float:
        return total * 0.9
```

**Explicación línea por línea:**
- Cada estrategia implementa el mismo método.
- El contexto puede cambiar de estrategia dinámicamente.

### 3.5. Observer

Notifica cambios a múltiples suscriptores:

```python
class Sujeto:
    def __init__(self) -> None:
        self._observadores = []

    def suscribir(self, observador) -> None:
        self._observadores.append(observador)

    def notificar(self, evento: str) -> None:
        for observador in self._observadores:
            observador(evento)
```

**Explicación línea por línea:**
- Los observadores se suscriben.
- `notificar` avisa a todos.
- **Es exactamente el sistema de señales/slots de Qt (archivo 17).**

### 3.6. Errores comunes

**Error 1 — Forzar patrones** donde no aportan.

**Error 2 — Preferir patrones a las características idiomáticas de Python.** Muchos patrones clásicos son innecesarios en Python (funciones de primera clase, decoradores...).

### 3.7. Checkpoint de comprensión

1. ¿Qué es un patrón de diseño?
2. ¿Cuándo usarías Strategy?
3. ¿Por qué el Observer es similar a las señales de Qt?

### 3.8. Ejercicio propuesto

**Ejercicio 3.1.** Implementa el patrón Strategy para ordenar con distintos criterios.

**Pista:** pasa la función de ordenación como estrategia.

---

## 4. SOLID en Python

### 4.1. Los cinco principios

| Principio | Idea |
|---|---|
| **S**RP | Una clase, una responsabilidad |
| **O**CP | Abierta a extensión, cerrada a modificación |
| **L**SP | Las subclases sustituyen a la base |
| **I**SP | Interfaces específicas, no gordas |
| **D**IP | Depende de abstracciones, no de concreciones |

### 4.2. Ejemplo

```python
from abc import ABC, abstractmethod


class FuenteDatos(ABC):                       # DIP: abstracción
    @abstractmethod
    def obtener(self) -> list[str]: ...


class ArchivoCSV(FuenteDatos):
    def obtener(self) -> list[str]:
        return ["a", "b"]


class Procesador:
    def __init__(self, fuente: FuenteDatos) -> None:   # depende de la abstracción
        self.fuente = fuente

    def procesar(self) -> int:
        return len(self.fuente.obtener())
```

**Explicación línea por línea:**
- `Procesador` depende de `FuenteDatos` (abstracción), no de `ArchivoCSV` (concreción). Eso es **DIP**.
- Puedes añadir una `FuenteAPI` sin tocar `Procesador`: **OCP**.

### 4.3. Errores comunes

**Error 1 — Clases que hacen de todo** (violan SRP).

**Error 2 — Abstracciones prematuras** que añaden complejidad.

### 4.4. Checkpoint de comprensión

1. ¿Qué dice el principio de inversión de dependencias?
2. Da un ejemplo de violación de SRP.

### 4.5. Ejercicio propuesto

**Ejercicio 4.1.** Refactoriza una clase que lee, procesa y guarda datos en tres clases.

**Pista:** aplica SRP.

---

## 5. Testing y TDD

### 5.1. Qué es TDD

El **TDD** (*Test-Driven Development*) sigue el ciclo **rojo → verde → refactor**:

1. **Rojo:** escribe un test que **falla** (aún no hay código).
2. **Verde:** escribe el código **mínimo** para que pase.
3. **Refactor:** mejora el código sin romper el test.

**¿Por qué funciona?** Porque escribir el test primero te obliga a pensar en el **comportamiento deseado** antes de la implementación.

### 5.2. La pirámide de tests

```text
        /\
       /E2E\        pocos, lentos (todo el sistema)
      /------\
     /  Integ \     medios (varias piezas)
    /----------\
   /   Unitarios\   muchos, rápidos (una función)
  /--------------\
```

### 5.3. Un ejemplo de TDD

**Paso 1 — Test (rojo):**

```python
def test_es_palindromo():
    assert es_palindromo("anilina") is True
    assert es_palindromo("python") is False
```

**Paso 2 — Implementación (verde):**

```python
def es_palindromo(texto: str) -> bool:
    """Comprueba si un texto es palíndromo."""
    limpio = texto.lower().replace(" ", "")
    return limpio == limpio[::-1]
```

**Paso 3 — Refactor:** mejorar sin cambiar el comportamiento.

### 5.4. Cobertura

```bash
pip install pytest pytest-cov
pytest --cov=mi_paquete --cov-report=term-missing
```

**Explicación línea por línea:**
- `--cov` mide qué porcentaje del código ejecutan los tests.

### 5.5. Errores comunes

**Error 1 — Buscar el 100 % de cobertura** sin valor real.

**Error 2 — Tests que prueban implementación**, no comportamiento.

**Error 3 — Tests frágiles** que fallan por cambios menores.

### 5.6. Checkpoint de comprensión

1. ¿Cuáles son las tres fases del TDD?
2. ¿Por qué la cobertura del 100 % no es el objetivo?

### 5.7. Ejercicio propuesto

**Ejercicio 5.1.** Aplica TDD para implementar una función de validación de emails.

**Pista:** escribe primero los casos inválidos.

---

## 6. CI/CD con GitHub Actions, GitLab CI

### 6.1. Qué son CI y CD

- **CI** (*Continuous Integration*): ejecutar tests y linters automáticamente en cada push.
- **CD** (*Continuous Delivery/Deployment*): publicar automáticamente tras pasar los tests.

**Analogía:** CI es un control de calidad automático en la fábrica; CD es el envío automático del producto aprobado.

### 6.2. GitHub Actions

**`.github/workflows/ci.yml`:**

```yaml
name: CI

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        python-version: ["3.11", "3.12"]
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: ${{ matrix.python-version }}
      - run: pip install -r requirements.txt
      - run: ruff check .
      - run: mypy .
      - run: pytest --cov
```

**Explicación línea por línea:**
- `on: [push, pull_request]` dispara el workflow en cada push y PR.
- `matrix` prueba en varias versiones de Python.
- Cada `run` ejecuta un comando: lint, tipos y tests.

### 6.3. GitLab CI

**`.gitlab-ci.yml`:**

```yaml
stages: [test]

test:
  image: python:3.12
  stage: test
  script:
    - pip install -r requirements.txt
    - pytest
```

### 6.4. Errores comunes

**Error 1 — CI sin caché** (lento).

**Error 2 — No fijar versiones** de dependencias.

**Error 3 — Desplegar sin pasar tests.**

### 6.5. Checkpoint de comprensión

1. ¿Qué diferencia hay entre CI y CD?
2. ¿Para qué sirve una matriz de versiones?

### 6.6. Ejercicio propuesto

**Ejercicio 6.1.** Configura un workflow que ejecute ruff, mypy y pytest.

**Pista:** usa una matriz de versiones.

---

## 7. Docker y contenedores

### 7.1. Qué es Docker

**Docker** empaqueta la aplicación **con sus dependencias** en una **imagen** reproducible. Resuelve el clásico "en mi máquina funciona".

**Analogía:** un contenedor es como una **caja de mudanza sellada** que incluye el programa y todo lo que necesita. Da igual en qué casa (servidor) la abras: funcionará igual.

### 7.2. Dockerfile

```dockerfile
FROM python:3.12-slim

ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

CMD ["python", "-m", "mi_app"]
```

**Explicación línea por línea:**
- `FROM python:3.12-slim` parte de una imagen base ligera.
- `ENV` configura variables de entorno (sin `.pyc`, salida sin buffer).
- `WORKDIR /app` establece el directorio de trabajo.
- `COPY requirements.txt .` copia solo las dependencias primero (para aprovechar la caché).
- `RUN pip install ...` instala.
- `COPY . .` copia el código.
- `CMD` define el comando de arranque.

### 7.3. Docker Compose

```yaml
services:
  web:
    build: .
    ports:
      - "8000:8000"
    environment:
      DATABASE_URL: postgresql+psycopg://user:pass@db/app
    depends_on:
      - db
  db:
    image: postgres:16
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: pass
      POSTGRES_DB: app
    volumes:
      - datos:/var/lib/postgresql/data

volumes:
  datos:
```

**Explicación línea por línea:**
- `services` define varios contenedores.
- `depends_on` indica el orden de arranque.
- `volumes` persiste los datos de Postgres.

### 7.4. Errores comunes

**Error 1 — Copiar `.venv` o `.git`** a la imagen. Usa `.dockerignore`.

**Error 2 — Imágenes enormes.** Usa `slim` y multi-stage.

**Error 3 — Secretos en el Dockerfile.** Usa variables de entorno.

### 7.5. Checkpoint de comprensión

1. ¿Qué problema resuelve Docker?
2. ¿Por qué copiar `requirements.txt` antes que el código?

### 7.6. Ejercicio propuesto

**Ejercicio 7.1.** Dockeriza una API FastAPI con Postgres con Compose.

**Pista:** separa los servicios y usa un volumen.

---

## 8. Documentación con Sphinx y MkDocs

### 8.1. Dos herramientas

- **Sphinx:** documentación técnica, con `autodoc` desde docstrings.
- **MkDocs** (+ Material): documentación en Markdown, sencilla y bonita.

### 8.2. MkDocs

```bash
pip install mkdocs mkdocs-material
mkdocs new .
mkdocs serve
```

**`mkdocs.yml`:**

```yaml
site_name: Mi Proyecto
theme:
  name: material
nav:
  - Inicio: index.md
  - Guía: guia.md
```

**Explicación línea por línea:**
- `mkdocs new` crea la estructura.
- `mkdocs serve` levanta un servidor local con recarga.
- `theme: material` aplica un tema moderno.

### 8.3. Sphinx

```bash
pip install sphinx
sphinx-quickstart docs
```

```python
# conf.py
extensions = ["sphinx.ext.autodoc", "sphinx.ext.napoleon"]
```

```bash
sphinx-apidoc -o docs/source src/mi_paquete
make html
```

**Explicación línea por línea:**
- `autodoc` genera documentación desde los docstrings.
- `napoleon` entiende el estilo Google de docstrings.
- `make html` genera el HTML.

### 8.4. Errores comunes

**Error 1 — Docstrings desactualizados.**

**Error 2 — No documentar la API pública.**

### 8.5. Checkpoint de comprensión

1. ¿Qué hace `autodoc`?
2. ¿Cuándo elegirías MkDocs sobre Sphinx?

### 8.6. Ejercicio propuesto

**Ejercicio 8.1.** Publica la documentación de un paquete con MkDocs Material.

**Pista:** usa `mkdocs build`.

---

## 9. Linting y formateo

### 9.1. Herramientas

- **ruff:** linter y formateador ultrarrápido (reemplaza flake8, isort, black en muchos casos).
- **black:** formateador opinado.
- **flake8:** linter clásico.
- **isort:** ordena imports.

### 9.2. Configuración de ruff

**`pyproject.toml`:**

```toml
[tool.ruff]
line-length = 88
target-version = "py312"

[tool.ruff.lint]
select = ["E", "F", "I", "N", "UP", "B"]
```

```bash
ruff check .
ruff format .
```

**Explicación línea por línea:**
- `select` elige las reglas a comprobar (E: estilo, F: errores, I: imports, N: nombres...).
- `ruff check` detecta; `ruff format` formatea.

### 9.3. Pre-commit

**`.pre-commit-config.yaml`:**

```yaml
repos:
  - repo: https://github.com/astral-sh/ruff-pre-commit
    rev: v0.5.0
    hooks:
      - id: ruff
      - id: ruff-format
```

```bash
pip install pre-commit
pre-commit install
```

**Explicación línea por línea:**
- `pre-commit install` configura un *hook* que ejecuta ruff **antes de cada commit**.

### 9.4. Errores comunes

**Error 1 — Ignorar reglas sin motivo** (`# noqa` masivo).

**Error 2 — Formatear y lintear con configuraciones en conflicto.**

### 9.5. Checkpoint de comprensión

1. ¿Qué ventaja tiene ruff frente a varias herramientas separadas?
2. ¿Qué hace pre-commit?

### 9.6. Ejercicio propuesto

**Ejercicio 9.1.** Configura ruff y pre-commit en un proyecto.

**Pista:** ejecuta `pre-commit run --all-files`.

---

## 10. Type checking con mypy

### 10.1. Verificar tipos estáticamente

**mypy** comprueba las anotaciones de tipo **antes de ejecutar**, detectando errores que Python no ve:

```bash
pip install mypy
mypy src/
```

```python
def sumar(a: int, b: int) -> int:
    return a + b


sumar("1", "2")   # mypy: error
```

```text
error: Argument 1 to "sumar" has incompatible type "str"; expected "int"
```

**Explicación línea por línea:**
- mypy analiza el código y detecta el error de tipos sin ejecutarlo.

### 10.2. Configuración

**`pyproject.toml`:**

```toml
[tool.mypy]
python_version = "3.12"
strict = true
warn_unused_ignores = true
```

**Explicación línea por línea:**
- `strict = true` activa todas las comprobaciones.

### 10.3. Errores comunes

**Error 1 — `Any` por doquier** que anula la verificación.

**Error 2 — Ignorar errores con `# type: ignore`** sin justificar.

### 10.4. Checkpoint de comprensión

1. ¿Qué detecta mypy que Python no?
2. ¿Qué hace `strict = true`?

### 10.5. Ejercicio propuesto

**Ejercicio 10.1.** Añade tipos a un módulo y corrige los errores de mypy.

**Pista:** usa `--strict`.

---

## 11. Recursos

- **Clean Architecture (libro):** Robert C. Martin.
- **Patrones de diseño:** <https://refactoring.guru/design-patterns/python>
- **pytest:** <https://docs.pytest.org/>
- **GitHub Actions:** <https://docs.github.com/actions>
- **Docker:** <https://docs.docker.com/>
- **MkDocs:** <https://www.mkdocs.org/>
- **ruff:** <https://docs.astral.sh/ruff/>
- **mypy:** <https://mypy.readthedocs.io/>

---

## 12. Mini resumen final

- Una **estructura clara** y el **src layout** mejoran el mantenimiento.
- La **Clean Architecture** aísla el dominio y facilita el testing.
- Los **patrones GoF** aportan soluciones probadas, con moderación.
- **SOLID** guía un diseño mantenible.
- El **TDD** y la pirámide de tests equilibran velocidad y cobertura.
- **CI/CD** automatiza calidad y despliegue.
- **Docker** garantiza entornos reproducibles.
- **Sphinx/MkDocs** documentan; **ruff/mypy** aseguran calidad y tipos.

---

### 🎯 Retos opcionales

1. **Reto 1:** Refactoriza un proyecto monolítico a capas con Clean Architecture.
2. **Reto 2:** Configura CI con ruff, mypy y pytest, y un pre-commit.
3. **Reto 3:** Dockeriza el proyecto y publica la documentación con MkDocs.

**Anterior:** [22_integracion.md](22_integracion.md) · **Siguiente:** [24_apendices.md](24_apendices.md)
