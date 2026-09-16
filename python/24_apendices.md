# 24 · Apéndices

**Versión recomendada:** Python 3.12
**Requisito:** todas las Partes anteriores
**Nivel:** Referencia

---

## Índice de contenidos

1. [Comparativa de frameworks web](#1-comparativa-de-frameworks-web)
2. [Comparativa de ORM](#2-comparativa-de-orm)
3. [Comparativa de librerías GUI](#3-comparativa-de-librerías-gui)
4. [Comparativa de librerías de ML](#4-comparativa-de-librerías-de-ml)
5. [Cheat sheets por tecnología](#5-cheat-sheets-por-tecnología)
6. [Recursos, libros y cursos recomendados](#6-recursos-libros-y-cursos-recomendados)
7. [Comunidades y foros](#7-comunidades-y-foros)
8. [Glosario de términos](#8-glosario-de-términos)
9. [Ejercicios y proyectos propuestos](#9-ejercicios-y-proyectos-propuestos)
10. [Soluciones a ejercicios](#10-soluciones-a-ejercicios)

---

## 1. Comparativa de frameworks web

### 1.1. La tabla comparativa

| Criterio | Flask | Django | FastAPI | Pyramid |
|---|---|---|---|---|
| Tipo | Microframework | Full-stack | API async | Flexible |
| ORM | A elección | Incluido | A elección | A elección |
| Plantillas | Jinja2 | DTL | A elección | A elección |
| Admin | No | Sí | No | No |
| Validación | Manual/Flask-WTF | Formularios | Pydantic | Manual |
| Docs API | Extensión | DRF | Automática | Manual |
| Rendimiento | Medio | Medio | Muy alto | Medio |
| Async | Limitado | Limitado | Nativo | Limitado |
| Curva | Baja | Alta | Media | Media-alta |
| Ideal para | Prototipos, APIs simples | Apps completas con admin | APIs, microservicios | Apps grandes flexibles |

### 1.2. Cómo decidir

**Hazte estas preguntas:**

1. **¿Necesitas un panel de administración automático?** → Django.
2. **¿Es una API con muchos usuarios concurrentes?** → FastAPI.
3. **¿Es un prototipo o una API pequeña?** → Flask.
4. **¿Necesitas elegir cada componente y permisos complejos?** → Pyramid.

**Analogía:** Flask es un **kit de herramientas**, Django una **casa prefabricada**, FastAPI un **coche deportivo** y Pyramid un **mecano ajustable**.

---

## 2. Comparativa de ORM

### 2.1. La tabla comparativa

| Criterio | Django ORM | SQLAlchemy | SQLModel | Tortoise ORM | Peewee |
|---|---|---|---|---|---|
| Estilo | Active Record | Data Mapper | Data Mapper + Pydantic | Active Record async | Active Record |
| Async | Parcial (4.1+) | Sí (2.0) | Sí | Sí | No |
| Migraciones | Integradas | Alembic | Alembic | Aerich | Playhouse |
| Curva | Baja | Alta | Media | Baja | Muy baja |
| Potencia | Alta | Muy alta | Alta | Media | Media |
| Integración | Django | Cualquiera | FastAPI | FastAPI | Cualquiera |
| Ideal para | Django | Proyectos grandes | FastAPI | Async puro | Proyectos simples |

### 2.2. Active Record vs. Data Mapper

- **Active Record** (Django, Peewee): el objeto **sabe** cómo guardarse. `usuario.save()`.
- **Data Mapper** (SQLAlchemy): los objetos son **puros**; una `Session` separada gestiona la persistencia.

**¿Cuál es mejor?** Data Mapper separa mejor las responsabilidades (recuerda Clean Architecture), pero es más complejo. Active Record es más directo para proyectos simples.

### 2.3. Notas

- **SQLAlchemy** es el más potente y portable; su ORM es un *Data Mapper*.
- **Django ORM** es el más cómodo dentro de Django.
- **SQLModel** une Pydantic y SQLAlchemy para FastAPI.

---

## 3. Comparativa de librerías GUI

### 3.1. La tabla comparativa

| Criterio | Tkinter | PySide6 (Qt) | Kivy | wxPython |
|---|---|---|---|---|
| Incluida | Sí | No | No | No |
| Look nativo | Básico (ttk) | Excelente | Propio | Nativo |
| Complejidad | Baja | Media-alta | Media | Media |
| Móvil | No | Limitado | Sí | No |
| Gráficos | Canvas básico | QtCharts | OpenGL | Varios |
| Modelo/vista | No | Sí | Parcial | Parcial |
| Licencia | PSF | LGPL | MIT | wxWindows |
| Ideal para | Herramientas simples | Apps profesionales | Apps táctiles | Apps nativas |

### 3.2. Cómo decidir

- **Utilidad interna simple** → Tkinter.
- **App de escritorio profesional** → PySide6.
- **App táctil/móvil** → Kivy.
- **Integración nativa con el SO** → wxPython.

---

## 4. Comparativa de librerías de ML

### 4.1. La tabla comparativa

| Criterio | scikit-learn | XGBoost | LightGBM | CatBoost | PyTorch |
|---|---|---|---|---|---|
| Uso | ML clásico | Boosting | Boosting | Boosting | Deep Learning |
| Datos tabulares | Excelente | Excelente | Excelente | Excelente | Bueno |
| GPU | No | Sí | Sí | Sí | Sí |
| Categóricas | Manual | Bueno | Bueno | Excelente | Manual |
| Curva | Baja | Media | Media | Media | Alta |
| Ecosistema | Amplio | Amplio | Amplio | Amplio | Muy amplio |

### 4.2. El flujo típico

1. **Explora y limpia** con Pandas/NumPy.
2. **Entrena un baseline** con scikit-learn.
3. **Mejora** con XGBoost/LightGBM/CatBoost.
4. **Deep learning** con PyTorch si hace falta.

**Regla:** empieza siempre por lo simple (scikit-learn). No saltes a deep learning sin agotar los modelos clásicos.

---

## 5. Cheat sheets por tecnología

### 5.1. Python básico

```python
# Variables y tipos
x = 1; y = 2.5; s = "texto"; b = True; n = None

# Colecciones
lista = [1, 2, 3]
tupla = (1, 2)
dic = {"a": 1}
conjunto = {1, 2}

# Comprensiones
cuadrados = [n ** 2 for n in range(5)]

# f-strings
print(f"{x} y {y:.2f}")
```

### 5.2. Control de flujo

```python
if x > 0:
    ...
elif x == 0:
    ...
else:
    ...

for i, v in enumerate(lista):
    ...

while condicion:
    ...
```

### 5.3. Funciones

```python
def f(a, b=1, *args, **kwargs):
    return a + b

lambda x: x * 2
```

### 5.4. POO

```python
class Cosa:
    def __init__(self, x):
        self.x = x

    def __repr__(self):
        return f"Cosa({self.x})"


class Hija(Cosa):
    def __init__(self, x, y):
        super().__init__(x)
        self.y = y
```

### 5.5. Flask

```python
from flask import Flask, jsonify, render_template, request

app = Flask(__name__)


@app.route("/", methods=["GET", "POST"])
def index():
    return render_template("index.html")


@app.get("/api")
def api():
    return jsonify({"ok": True})
```

### 5.6. Django

```bash
django-admin startproject config .
python manage.py startapp app
python manage.py makemigrations
python manage.py migrate
python manage.py runserver
python manage.py createsuperuser
```

### 5.7. FastAPI

```python
from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI()


class Item(BaseModel):
    nombre: str


@app.post("/items")
def crear(item: Item):
    return item
```

### 5.8. SQLAlchemy

```python
from sqlalchemy import select
from sqlalchemy.orm import DeclarativeBase, Mapped, Session, mapped_column


class Base(DeclarativeBase):
    pass


class User(Base):
    __tablename__ = "users"
    id: Mapped[int] = mapped_column(primary_key=True)


with Session(engine) as s:
    s.execute(select(User)).scalars().all()
```

### 5.9. NumPy

```python
import numpy as np

a = np.arange(10)
a.reshape(2, 5)
a[a > 5]
a.mean(), a.sum(axis=0)
a @ a.T
```

### 5.10. Pandas

```python
import pandas as pd

df = pd.read_csv("datos.csv")
df = df.dropna().drop_duplicates()
df.groupby("ciudad")["ventas"].sum()
df.pivot_table(index="mes", columns="producto", values="ventas", aggfunc="sum")
df.merge(otro, on="id", how="left")
```

### 5.11. scikit-learn

```python
from sklearn.linear_model import LogisticRegression
from sklearn.model_selection import cross_val_score, train_test_split
from sklearn.pipeline import Pipeline
from sklearn.preprocessing import StandardScaler

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)
pipe = Pipeline([("scaler", StandardScaler()), ("clf", LogisticRegression())])
pipe.fit(X_train, y_train)
pipe.score(X_test, y_test)
```

### 5.12. OpenCV

```python
import cv2

img = cv2.imread("foto.jpg")
gris = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
bordes = cv2.Canny(gris, 100, 200)
contornos, _ = cv2.findContours(bordes, cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE)
```

### 5.13. Git

```bash
git status
git add .
git commit -m "mensaje"
git push origin main
git pull --rebase
git switch -c feature/x
git merge feature/x
```

---

## 6. Recursos, libros y cursos recomendados

### 6.1. Libros

| Título | Autor | Tema |
|---|---|---|
| *Automate the Boring Stuff with Python* | Al Sweigart | Fundamentos y automatización |
| *Python Crash Course* | Eric Matthes | Fundamentos |
| *Fluent Python* | Luciano Ramalho | Python avanzado |
| *Effective Python* | Brett Slatkin | Buenas prácticas |
| *Python Data Science Handbook* | Jake VanderPlas | NumPy/Pandas |
| *Hands-On Machine Learning* | Aurélien Géron | ML |
| *Two Scoops of Django* | Feldroy | Django |
| *Flask Web Development* | Miguel Grinberg | Flask |
| *SQLAlchemy 2 In Practice* | Miguel Grinberg | ORM |
| *Clean Code* / *Clean Architecture* | Robert C. Martin | Diseño |
| *Refactoring* | Martin Fowler | Refactorización |

### 6.2. Cursos y sitios

- **Documentación oficial** de cada librería (siempre primero).
- **Real Python:** <https://realpython.com/>
- **Python for Everybody:** <https://www.py4e.com/>
- **FastAPI Tutorial:** <https://fastapi.tiangolo.com/tutorial/>
- **Django Tutorial:** <https://docs.djangoproject.com/en/stable/intro/tutorial01/>
- **Kaggle Learn:** <https://www.kaggle.com/learn>
- **PyImageSearch:** <https://pyimagesearch.com/>

### 6.3. Canales y podcasts

- **Talk Python To Me** (podcast).
- **Real Python Podcast.**
- **Corey Schafer** (YouTube).
- **ArjanCodes** (YouTube, diseño).

---

## 7. Comunidades y foros

| Comunidad | Enlace |
|---|---|
| Python.org | <https://www.python.org/community/> |
| Stack Overflow | <https://stackoverflow.com/questions/tagged/python> |
| Reddit r/Python | <https://www.reddit.com/r/Python/> |
| Reddit r/learnpython | <https://www.reddit.com/r/learnpython/> |
| Discord Python | <https://discord.gg/python> |
| PyPI | <https://pypi.org/> |
| GitHub | <https://github.com/> |
| PySlackers | <https://pyslackers.com/> |

---

## 8. Glosario de términos

| Término | Definición |
|---|---|
| **Argumento** | Valor que se pasa a una función. |
| **Async/await** | Sintaxis para programación asíncrona. |
| **Broadcasting** | Regla de NumPy para operar arrays de formas distintas. |
| **Closure** | Función que recuerda su entorno de creación. |
| **Corrutina** | Función `async` que puede pausarse. |
| **DataFrame** | Tabla etiquetada de Pandas. |
| **Decorador** | Función que envuelve a otra. |
| **Duck typing** | "Si camina como pato...": tipado por comportamiento. |
| **Engine** | Punto de entrada a la BD en SQLAlchemy. |
| **GIL** | *Global Interpreter Lock* de CPython. |
| **Iterador** | Objeto que produce elementos uno a uno. |
| **Middleware** | Capa que procesa peticiones/respuestas. |
| **MRO** | Orden de resolución de métodos en herencia múltiple. |
| **ndarray** | Array multidimensional de NumPy. |
| **ORM** | Mapeo objeto-relacional. |
| **Pipeline** | Secuencia de transformaciones/modelos. |
| **Puerto (arquitectura)** | Interfaz abstracta de un componente. |
| **ROI** | Región de interés en una imagen. |
| **Serializar** | Convertir un objeto a un formato almacenable. |
| **Signal/Slot** | Mecanismo de eventos de Qt. |
| **Socket** | Extremo de una conexión de red. |
| **WSGI/ASGI** | Interfaces entre servidor web y app Python. |
| **Yield** | Pausa un generador devolviendo un valor. |

---

## 9. Ejercicios y proyectos propuestos

### 9.1. Nivel principiante

1. **Calculadora de propinas** con Tkinter.
2. **Analizador de texto**: frecuencia de palabras con Pandas.
3. **Agenda de contactos** con SQLite y SQLAlchemy.

### 9.2. Nivel intermedio

4. **API de tareas** con FastAPI + SQLModel + Alembic.
5. **Blog** con Flask + SQLAlchemy + Jinja2.
6. **Scraper** con Selenium + Pandas que genere un informe.
7. **Dashboard** de ventas con Pandas + Matplotlib.

### 9.3. Nivel avanzado

8. **Sistema de recomendación** con scikit-learn sobre un dataset real.
9. **App de escritorio** PySide6 que consuma una API y persista localmente.
10. **Pipeline de visión** con OpenCV: detección + OCR + GUI.
11. **Microservicios** con FastAPI, Docker Compose y cola de tareas.
12. **Plataforma web** Django con admin, API DRF y tareas asíncronas.

---

## 10. Soluciones a ejercicios

> Las siguientes son **soluciones de referencia** a ejercicios seleccionados. Los ejercicios marcados 🧪 en cada archivo están pensados para que los resuelvas antes de mirar aquí.

### 10.1. Python básico

**Intercambiar dos variables (01/02):**

```python
a, b = 1, 2
a, b = b, a
print(a, b)   # 2 1
```

**Año bisiesto (03):**

```python
def es_bisiesto(anio: int) -> bool:
    return anio % 4 == 0 and (anio % 100 != 0 or anio % 400 == 0)
```

**Contar palabras (02):**

```python
from collections import Counter


def contar(texto: str) -> dict[str, int]:
    return dict(Counter(texto.lower().split()))
```

### 10.2. Funciones

**Decorador de conteo (04):**

```python
from functools import wraps


def contar_llamadas(func):
    @wraps(func)
    def envoltura(*args, **kwargs):
        envoltura.llamadas += 1
        print(f"{func.__name__} llamada {envoltura.llamadas} veces")
        return func(*args, **kwargs)

    envoltura.llamadas = 0
    return envoltura
```

### 10.3. POO

**Fracción (05):**

```python
from math import gcd


class Fraccion:
    def __init__(self, num: int, den: int) -> None:
        comun = gcd(num, den)
        self.num = num // comun
        self.den = den // comun

    def __add__(self, otra: "Fraccion") -> "Fraccion":
        return Fraccion(self.num * otra.den + otra.num * self.den, self.den * otra.den)

    def __eq__(self, otra: object) -> bool:
        return isinstance(otra, Fraccion) and (self.num, self.den) == (otra.num, otra.den)

    def __repr__(self) -> str:
        return f"{self.num}/{self.den}"
```

### 10.4. Web

**CRUD en memoria (09):**

```python
from flask import Flask, jsonify, request

app = Flask(__name__)
tareas: dict[int, dict] = {}
contador = {"id": 1}


@app.get("/tareas")
def listar():
    return jsonify(list(tareas.values()))


@app.post("/tareas")
def crear():
    datos = request.get_json()
    tareas[contador["id"]] = {"id": contador["id"], **datos}
    contador["id"] += 1
    return jsonify(tareas[contador["id"] - 1]), 201


@app.delete("/tareas/<int:tid>")
def borrar(tid: int):
    tareas.pop(tid, None)
    return "", 204
```

### 10.5. Datos y ML

**Normalizar por columnas (18):**

```python
import numpy as np

m = np.random.default_rng(0).random((3, 4))
normalizada = (m - m.mean(axis=0)) / m.std(axis=0)
```

**Pipeline de clasificación (20):**

```python
from sklearn.datasets import load_iris
from sklearn.linear_model import LogisticRegression
from sklearn.model_selection import cross_val_score
from sklearn.pipeline import Pipeline
from sklearn.preprocessing import StandardScaler

X, y = load_iris(return_X_y=True)
pipe = Pipeline([("scaler", StandardScaler()), ("clf", LogisticRegression(max_iter=1000))])
print(cross_val_score(pipe, X, y, cv=5).mean())
```

### 10.6. OpenCV

**Contar objetos (21):**

```python
import cv2

gris = cv2.cvtColor(cv2.imread("objetos.jpg"), cv2.COLOR_BGR2GRAY)
_, binaria = cv2.threshold(gris, 0, 255, cv2.THRESH_BINARY + cv2.THRESH_OTSU)
contornos, _ = cv2.findContours(binaria, cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE)
objetos = [c for c in contornos if cv2.contourArea(c) > 100]
print("Objetos:", len(objetos))
```

---

## Cierre del manual

Has recorrido las seis partes:

- **Parte I — Fundamentos:** el lenguaje Python.
- **Parte II — Desarrollo web:** Flask, Django, FastAPI, Pyramid.
- **Parte III — Datos y ORM:** SQLAlchemy.
- **Parte IV — Automatización y escritorio:** Selenium, Tkinter, PySide6.
- **Parte V — Ciencia de datos y ML:** NumPy, Pandas, scikit-learn, OpenCV.
- **Parte VI — Transversales:** integración, buenas prácticas y apéndices.

**Siguientes pasos recomendados:**

1. Elige un proyecto de la sección 9 y constrúyelo de principio a fin.
2. Aplica tests, linting y CI desde el inicio.
3. Publica tu código en GitHub y comparte lo aprendido.
4. Vuelve a los archivos de referencia cuando lo necesites: cada uno es autocontenido.

> *"Simple es mejor que complejo."* — Zen de Python

**Anterior:** [23_buenas_practicas.md](23_buenas_practicas.md) · **Volver al índice:** [00_indice.md](00_indice.md)
