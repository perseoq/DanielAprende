# Manual Completo de Python y su Ecosistema

**Versión de referencia:** Python 3.12 · Actualizado: 2026

Este manual enseña Python y su ecosistema de librerías y frameworks de forma estructurada. Cada tema se entrega en **un archivo Markdown independiente y autocontenido**, con teoría, ejemplos funcionales, explicaciones paso a paso y ejercicios prácticos.

> Todos los ejemplos de código viven dentro de este manual como bloques resaltados. No se requieren archivos `.py` externos para seguirlo.

---

## Cómo usar este manual

1. **Sigue el orden** si eres nuevo en Python: Parte I → VI.
2. Si ya programas en Python, salta directo a la Parte o tema que necesites (cada archivo es autocontenido).
3. En cada concepto encontrarás: teoría → ejemplo comentado → salida esperada → errores comunes → ejercicio propuesto.
4. Al final de cada archivo hay ejemplos integradores, buenas prácticas y recursos.

**Convenciones:**
- Bloques `python` para código ejecutable.
- Bloques `text` para la salida esperada.
- 🧪 marca un ejercicio propuesto (con pista, sin solución completa).
- 🔥 marca un error común o trampa.

---

## PARTE I — Fundamentos de Python

| # | Archivo | Contenido |
|---|---------|-----------|
| 01 | [01_introduccion_python.md](01_introduccion_python.md) | Historia, instalación, intérpretes, entornos virtuales, pip, estructura de proyecto, PEP 8, docstrings |
| 02 | [02_sintaxis_tipos_datos.md](02_sintaxis_tipos_datos.md) | Variables, tipos primitivos, operadores, list/tuple/dict/set, slicing, comprensiones, f-strings, fechas |
| 03 | [03_control_flujo.md](03_control_flujo.md) | if/elif/else, for, while, break/continue/pass, match-case, iteradores y generadores |
| 04 | [04_funciones.md](04_funciones.md) | Definición, argumentos, *args/**kwargs, lambda, map/filter/reduce/zip, decoradores, closures, functools/itertools |
| 05 | [05_poo.md](05_poo.md) | Clases, herencia, polimorfismo, encapsulamiento, métodos mágicos, ABCs, dataclasses, metaclases |
| 06 | [06_errores_excepciones.md](06_errores_excepciones.md) | try/except/else/finally, excepciones personalizadas, context managers, logging |
| 07 | [07_modulos_paquetes.md](07_modulos_paquetes.md) | Imports, paquetes, `__init__.py`, namespaces, importlib, publicación en PyPI |
| 08 | [08_python_avanzado.md](08_python_avanzado.md) | Type hints, async/await, threading, multiprocessing, GIL, regex, serialización, pathlib, testing, metaprogramación |

## PARTE II — Desarrollo Web

| # | Archivo | Contenido |
|---|---------|-----------|
| 09 | [09_flask.md](09_flask.md) | Rutas, métodos HTTP, Jinja2, formularios, sesiones, blueprints, hooks, API REST, despliegue |
| 10 | [10_flask_sqlalchemy.md](10_flask_sqlalchemy.md) | Modelos, relaciones, consultas, migraciones, transacciones, testing, PostgreSQL/MySQL/SQLite |
| 11 | [11_django_orm.md](11_django_orm.md) | MVT, settings, URLs, templates, ORM, QuerySet, admin, auth, DRF, Channels, despliegue |
| 12 | [12_fastapi_orm.md](12_fastapi_orm.md) | Endpoints, Pydantic, dependencias, OAuth2/JWT, async, SQLAlchemy/SQLModel/Tortoise, Alembic |
| 13 | [13_pyramid.md](13_pyramid.md) | "Start small, finish big", rutas/vistas, view configs, templates, ACL, traversal, SQLAlchemy |

## PARTE III — Acceso a Datos y ORM

| # | Archivo | Contenido |
|---|---------|-----------|
| 14 | [14_sqlalchemy.md](14_sqlalchemy.md) | Core y ORM, engine/dialectos, Table/Column, sesiones, relaciones, Alembic, pooling, SQLAlchemy 2.0 |

## PARTE IV — Automatización y Aplicaciones de Escritorio

| # | Archivo | Contenido |
|---|---------|-----------|
| 15 | [15_selenium.md](15_selenium.md) | Localizadores, esperas, iframes, ActionChains, Page Object Model, Selenium Grid, Playwright |
| 16 | [16_tkinter.md](16_tkinter.md) | Widgets, geometría, eventos, Canvas, ttk, Treeview, threading, PyInstaller, MVC |
| 17 | [17_pyside6.md](17_pyside6.md) | Qt, widgets, layouts, señales/slots, modelos/vistas, QML, QThread, QtSql, Qt Designer |

## PARTE V — Ciencia de Datos y Machine Learning

| # | Archivo | Contenido |
|---|---------|-----------|
| 18 | [18_numpy.md](18_numpy.md) | ndarray, dtype, broadcasting, ufuncs, agregaciones, álgebra lineal, FFT, vectorización |
| 19 | [19_pandas.md](19_pandas.md) | Series/DataFrame, loc/iloc, IO, limpieza, groupby, merge, series temporales, MultiIndex |
| 20 | [20_scikit_learn.md](20_scikit_learn.md) | API fit/transform/predict, preprocesamiento, PCA, modelos supervisados/no supervisados, pipelines |
| 21 | [21_opencv.md](21_opencv.md) | Imágenes, espacios de color, filtros, bordes, morfología, contornos, features, DNN, video |

## PARTE VI — Temas Transversales

| # | Archivo | Contenido |
|---|---------|-----------|
| 22 | [22_integracion.md](22_integracion.md) | Flask+SQLAlchemy+Pandas, FastAPI+SQLModel, Django+sklearn, OpenCV+PySide6, microservicios |
| 23 | [23_buenas_practicas.md](23_buenas_practicas.md) | Estructura de proyectos, Clean Architecture, patrones GoF, SOLID, TDD, CI/CD, Docker, Sphinx |
| 24 | [24_apendices.md](24_apendices.md) | Comparativas, cheat sheets, recursos, glosario, ejercicios y soluciones |

---

## Estado del manual

- [x] `00_indice.md`
- [x] `01_introduccion_python.md`
- [x] `02_sintaxis_tipos_datos.md`
- [x] `03_control_flujo.md`
- [x] `04_funciones.md`
- [x] `05_poo.md`
- [x] `06_errores_excepciones.md`
- [x] `07_modulos_paquetes.md`
- [x] `08_python_avanzado.md`
- [x] `09_flask.md`
- [x] `10_flask_sqlalchemy.md`
- [x] `11_django_orm.md`
- [x] `12_fastapi_orm.md`
- [x] `13_pyramid.md`
- [x] `14_sqlalchemy.md`
- [x] `15_selenium.md`
- [x] `16_tkinter.md`
- [x] `17_pyside6.md`
- [x] `18_numpy.md`
- [x] `19_pandas.md`
- [x] `20_scikit_learn.md`
- [x] `21_opencv.md`
- [x] `22_integracion.md`
- [x] `23_buenas_practicas.md`
- [x] `24_apendices.md`
