# 22 · Integración entre Tecnologías

**Versión recomendada:** Python 3.12 · ecosistema 2026
**Requisito:** todas las Partes anteriores
**Nivel:** Avanzado
**Duración estimada de estudio:** 8–12 horas

---

## Índice de contenidos

1. [Introducción](#1-introducción)
2. [Flask + SQLAlchemy + Pandas](#2-flask--sqlalchemy--pandas)
3. [FastAPI + SQLModel + NumPy](#3-fastapi--sqlmodel--numpy)
4. [Django + Scikit-learn](#4-django--scikit-learn)
5. [OpenCV + PySide6](#5-opencv--pyside6-interfaces-gráficas)
6. [Selenium + Pandas](#6-selenium--pandas-scraping-y-análisis)
7. [Tkinter/PySide6 + SQLAlchemy](#7-tkinterpyside6--sqlalchemy)
8. [APIs REST con Flask/FastAPI/Django](#8-apis-rest-con-flaskfastapidjango)
9. [Microservicios en Python](#9-microservicios-en-python)
10. [Buenas prácticas](#10-buenas-prácticas)
11. [Recursos](#11-recursos)
12. [Mini resumen final](#12-mini-resumen-final)

---

## 1. Introducción

### 1.1. Por qué integrar

En los archivos anteriores aprendiste cada tecnología por separado. Pero los proyectos reales **combinan varias**: una API web que consulta una base de datos y devuelve análisis; una app de escritorio que procesa imágenes y las guarda; un scraper que extrae datos y los analiza.

**Analogía:** cada tecnología es un instrumento; un proyecto real es la **orquesta**. Saber tocar cada instrumento es necesario, pero el valor está en hacerlos sonar juntos.

### 1.2. El principio de las capas

La clave para integrar bien es **separar responsabilidades en capas**:

```text
[ Presentación ]  ← web (Flask/FastAPI/Django) o GUI (Tkinter/PySide6)
      │
[ Lógica de negocio ]  ← servicios (cálculos, ML, reglas)
      │
[ Acceso a datos ]  ← SQLAlchemy, Pandas
      │
[ Base de datos ]
```

Cada capa solo conoce a la inmediatamente inferior. Esto facilita cambiar una sin tocar las demás.

### 1.3. Errores comunes

**Error 1 — Meter lógica de ML en las vistas web.** Sepárala en un servicio.

**Error 2 — Consultar la BD desde la UI en cada evento.** Cachea o usa modelos.

### 1.4. Checkpoint de comprensión

1. ¿Por qué conviene separar en capas?
2. ¿Qué capa conoce a cuál?

### 1.5. Ejercicio propuesto

**Ejercicio 1.1.** Dibuja las capas de una app que muestre predicciones ML vía web.

**Pista:** UI → API → servicio ML → modelo persistido.

---

## 2. Flask + SQLAlchemy + Pandas

### 2.1. El patrón clásico de análisis web

Una API Flask consulta la base con SQLAlchemy y devuelve análisis generados con Pandas.

```python
import pandas as pd
from flask import Flask, jsonify
from flask_sqlalchemy import SQLAlchemy

app = Flask(__name__)
app.config["SQLALCHEMY_DATABASE_URI"] = "sqlite:///ventas.db"
db = SQLAlchemy(app)


class Venta(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    ciudad = db.Column(db.String(60))
    total = db.Column(db.Float)


@app.get("/resumen")
def resumen():
    """Devuelve ventas agregadas por ciudad."""
    filas = db.session.execute(db.select(Venta)).scalars().all()
    df = pd.DataFrame([{"ciudad": v.ciudad, "total": v.total} for v in filas])
    if df.empty:
        return jsonify([])
    agrupado = df.groupby("ciudad")["total"].sum().reset_index()
    return jsonify(agrupado.to_dict(orient="records"))
```

**Explicación línea por línea:**
- El modelo `Venta` define la tabla.
- La vista consulta con SQLAlchemy (archivo 14).
- Se construye un DataFrame con Pandas (archivo 19).
- `groupby` agrega y `to_dict(orient="records")` convierte a JSON.

**Petición:**

```bash
curl http://127.0.0.1:5000/resumen
```

```json
[{"ciudad": "Lima", "total": 250.0}, {"ciudad": "Madrid", "total": 250.0}]
```

### 2.2. Mejoras de rendimiento

- Ejecuta la **agregación en SQL** (`func.sum`) para tablas grandes, no en Pandas.
- **Cachea** el resultado si cambia poco.
- Usa `pd.read_sql` con la sesión para consultas complejas.

```python
df = pd.read_sql("SELECT ciudad, SUM(total) AS total FROM venta GROUP BY ciudad", db.engine)
```

**Explicación línea por línea:**
- `pd.read_sql` ejecuta la consulta y devuelve un DataFrame directamente.

### 2.3. Errores comunes

**Error 1 — Cargar toda la tabla** en memoria para agregar. Usa SQL.

**Error 2 — Bloquear la petición** con cálculos largos. Usa background tasks.

### 2.4. Checkpoint de comprensión

1. ¿Por qué agregar en SQL es mejor para tablas grandes?
2. ¿Qué devuelve `to_dict(orient="records")`?

### 2.5. Ejercicio propuesto

**Ejercicio 2.1.** Añade un endpoint `/top?n=5` que devuelva las N ciudades con más ventas.

**Pista:** ordena el DataFrame.

---

## 3. FastAPI + SQLModel + NumPy

### 3.1. API de cálculo

FastAPI valida con Pydantic/SQLModel y calcula con NumPy. Ideal para APIs de cálculo numérico:

```python
import numpy as np
from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI()


class Datos(BaseModel):
    valores: list[float]


class Resultado(BaseModel):
    media: float
    desviacion: float
    maximo: float


@app.post("/estadisticas", response_model=Resultado)
def estadisticas(datos: Datos) -> Resultado:
    """Calcula estadísticas con NumPy."""
    arr = np.array(datos.valores, dtype=float)
    return Resultado(
        media=float(arr.mean()),
        desviacion=float(arr.std()),
        maximo=float(arr.max()),
    )
```

**Explicación línea por línea:**
- `Datos` valida la entrada (una lista de floats).
- `np.array(...)` convierte a array.
- Se calculan las estadísticas vectorizadas.
- `float(...)` convierte los tipos de NumPy a tipos serializables.

**Petición:**

```bash
curl -X POST http://127.0.0.1:8000/estadisticas \
  -H "Content-Type: application/json" \
  -d '{"valores": [1, 2, 3, 4]}'
```

```json
{"media": 2.5, "desviacion": 1.118, "maximo": 4.0}
```

### 3.2. Con persistencia

```python
from sqlmodel import Field, SQLModel


class Medicion(SQLModel, table=True):
    id: int | None = Field(default=None, primary_key=True)
    media: float
    desviacion: float
```

**Explicación línea por línea:**
- Se puede persistir cada cálculo en la base con SQLModel (archivo 12).

### 3.3. Errores comunes

**Error 1 — Convertir arrays NumPy a JSON sin castear** → error de serialización.

**Error 2 — Cálculos pesados en el endpoint** sin `async`/threadpool.

### 3.4. Checkpoint de comprensión

1. ¿Por qué castear con `float(...)`?
2. ¿Cómo persistirías las mediciones?

### 3.5. Ejercicio propuesto

**Ejercicio 3.1.** Añade un endpoint que calcule la correlación entre dos listas.

**Pista:** `np.corrcoef`.

---

## 4. Django + Scikit-learn

### 4.1. Servir un modelo entrenado

El patrón: un modelo ML se entrena **offline**, se persiste con `joblib` y se sirve desde una vista Django.

**`ml/servicio.py`:**

```python
from pathlib import Path

import joblib
import numpy as np

MODELO = joblib.load(Path(__file__).parent / "modelo.joblib")


def predecir(caracteristicas: list[float]) -> int:
    """Devuelve la predicción del modelo."""
    X = np.array(caracteristicas).reshape(1, -1)
    return int(MODELO.predict(X)[0])
```

**`views.py`:**

```python
import json

from django.http import JsonResponse
from django.views.decorators.http import require_POST

from ml.servicio import predecir


@require_POST
def predecir_api(request):
    """Endpoint de predicción."""
    datos = json.loads(request.body)
    clase = predecir(datos["caracteristicas"])
    return JsonResponse({"clase": clase})
```

**Explicación línea por línea:**
- El modelo se carga **una sola vez** al importar el módulo (no en cada petición).
- `reshape(1, -1)` adapta la entrada al formato que espera el modelo.
- La vista valida que sea POST y devuelve JSON.

### 4.2. Por qué entrenar fuera

Entrenar un modelo puede tardar minutos u horas. **Nunca** lo hagas en una vista: entrena offline, guarda el modelo y cárgalo al arrancar.

### 4.3. Errores comunes

**Error 1 — Entrenar en la vista.**

**Error 2 — Recargar el modelo por petición** (lento).

### 4.4. Checkpoint de comprensión

1. ¿Por qué cargar el modelo al inicio?
2. ¿Dónde se entrena el modelo?

### 4.5. Ejercicio propuesto

**Ejercicio 4.1.** Persiste un modelo de iris y expón una API de predicción en Django.

**Pista:** `joblib.dump` y una vista POST.

---

## 5. OpenCV + PySide6 (interfaces gráficas)

### 5.1. Cámara en una GUI

Integrar la webcam en una GUI Qt: captura con OpenCV y muestra en un `QLabel` mediante `QImage`/`QPixmap`.

```python
import sys

import cv2
from PySide6.QtCore import Qt, QTimer
from PySide6.QtGui import QImage, QPixmap
from PySide6.QtWidgets import QApplication, QLabel, QMainWindow


class VentanaCamara(QMainWindow):
    def __init__(self) -> None:
        super().__init__()
        self.label = QLabel()
        self.label.setAlignment(Qt.AlignCenter)
        self.setCentralWidget(self.label)

        self.cap = cv2.VideoCapture(0)
        self.timer = QTimer()
        self.timer.timeout.connect(self.actualizar)
        self.timer.start(30)

    def actualizar(self) -> None:
        ok, frame = self.cap.read()
        if not ok:
            return
        frame = cv2.cvtColor(frame, cv2.COLOR_BGR2RGB)
        alto, ancho, _ = frame.shape
        imagen = QImage(frame.data, ancho, alto, ancho * 3, QImage.Format_RGB888)
        self.label.setPixmap(QPixmap.fromImage(imagen))

    def closeEvent(self, event) -> None:
        self.cap.release()
        super().closeEvent(event)


app = QApplication(sys.argv)
VentanaCamara().show()
sys.exit(app.exec())
```

**Explicación línea por línea:**
- `QTimer` llama a `actualizar` cada 30 ms **sin bloquear la UI** (archivo 17).
- Se convierte **BGR → RGB** porque Qt espera RGB.
- `QImage(...)` envuelve los datos del frame; `QPixmap.fromImage` lo muestra.
- `closeEvent` **libera la cámara** al cerrar.

### 5.2. Puntos clave de la integración

1. Convierte BGR → RGB.
2. Usa `QTimer` para no congelar la interfaz.
3. Libera la cámara al cerrar.

### 5.3. Errores comunes

**Error 1 — Mostrar el frame BGR** → colores invertidos.

**Error 2 — Capturar en un bucle `while`** en el hilo de UI → interfaz congelada.

### 5.4. Checkpoint de comprensión

1. ¿Por qué usar `QTimer` en lugar de un bucle?
2. ¿Por qué convertir BGR a RGB?

### 5.5. Ejercicio propuesto

**Ejercicio 5.1.** Añade un botón que capture y guarde el frame actual.

**Pista:** `cv2.imwrite` desde un slot.

---

## 6. Selenium + Pandas (scraping y análisis)

### 6.1. Extraer y analizar

Selenium extrae datos dinámicos; Pandas los limpia y analiza:

```python
import pandas as pd
from selenium import webdriver
from selenium.webdriver.common.by import By

driver = webdriver.Chrome()
driver.get("https://quotes.toscrape.com")

filas = []
for cita in driver.find_elements(By.CSS_SELECTOR, ".quote"):
    texto = cita.find_element(By.CSS_SELECTOR, ".text").text
    autor = cita.find_element(By.CSS_SELECTOR, ".author").text
    filas.append({"cita": texto, "autor": autor})

driver.quit()

df = pd.DataFrame(filas)
print(df["autor"].value_counts().head())
df.to_csv("citas.csv", index=False)
```

**Explicación línea por línea:**
- Selenium recorre los elementos de la página.
- Se construye una lista de diccionarios.
- Pandas crea el DataFrame y analiza las frecuencias.
- Se guarda a CSV.

### 6.2. Buenas prácticas

- Respeta `robots.txt` y los términos (archivo 15).
- Añade pausas y limita la tasa.
- Limpia y valida los datos extraídos.

### 6.3. Errores comunes

**Error 1 — Scraping agresivo** o de datos personales.

**Error 2 — No limpiar** el texto (espacios, caracteres).

### 6.4. Checkpoint de comprensión

1. ¿Por qué Selenium y no `requests`?
2. ¿Qué responsabilidad tiene cada herramienta?

### 6.5. Ejercicio propuesto

**Ejercicio 6.1.** Extrae una tabla de una web dinámica y guárdala en CSV.

**Pista:** localiza las filas y columnas con CSS selectors.

---

## 7. Tkinter/PySide6 + SQLAlchemy

### 7.1. App de escritorio con persistencia

La GUI captura datos, un servicio usa SQLAlchemy y la vista refleja cambios:

```python
import tkinter as tk

from sqlalchemy import String, create_engine, select
from sqlalchemy.orm import DeclarativeBase, Mapped, Session, mapped_column


class Base(DeclarativeBase):
    pass


class Contacto(Base):
    __tablename__ = "contactos"
    id: Mapped[int] = mapped_column(primary_key=True)
    nombre: Mapped[str] = mapped_column(String(80))


engine = create_engine("sqlite:///contactos.db")
Base.metadata.create_all(engine)


def agregar(nombre: str) -> None:
    with Session(engine) as session:
        session.add(Contacto(nombre=nombre))
        session.commit()


def listar() -> list[str]:
    with Session(engine) as session:
        return [c.nombre for c in session.execute(select(Contacto)).scalars()]


raiz = tk.Tk()
entrada = tk.Entry(raiz)
entrada.pack()
lista = tk.Listbox(raiz)
lista.pack()


def guardar() -> None:
    agregar(entrada.get())
    lista.delete(0, tk.END)
    for nombre in listar():
        lista.insert(tk.END, nombre)


tk.Button(raiz, text="Agregar", command=guardar).pack()
raiz.mainloop()
```

**Explicación línea por línea:**
- El modelo `Contacto` define la tabla (archivo 14).
- `agregar` y `listar` encapsulan el acceso a datos.
- La GUI llama a esas funciones y refresca la lista.

### 7.2. Separación de capas

Extrae el acceso a datos a un módulo `repositorio.py`; la GUI **no** debería saber de SQLAlchemy. Así puedes cambiar la base sin tocar la interfaz.

### 7.3. Errores comunes

**Error 1 — Consultar en el hilo de UI** con operaciones lentas.

**Error 2 — Compartir una `Session` entre hilos.**

### 7.4. Checkpoint de comprensión

1. ¿Por qué separar el acceso a datos de la GUI?
2. ¿Qué problema hay con compartir una sesión entre hilos?

### 7.5. Ejercicio propuesto

**Ejercicio 7.1.** Añade edición y borrado de contactos con SQLAlchemy.

**Pista:** usa `session.get` y `session.delete`.

---

## 8. APIs REST con Flask/FastAPI/Django

### 8.1. El mismo recurso en tres frameworks

**Flask:**

```python
from flask import Flask, jsonify

app = Flask(__name__)


@app.get("/items")
def listar():
    return jsonify([{"id": 1, "nombre": "A"}])
```

**FastAPI:**

```python
from fastapi import FastAPI

app = FastAPI()


@app.get("/items")
def listar():
    return [{"id": 1, "nombre": "A"}]
```

**Django REST Framework:**

```python
from rest_framework.decorators import api_view
from rest_framework.response import Response


@api_view(["GET"])
def listar(request):
    return Response([{"id": 1, "nombre": "A"}])
```

**Explicación línea por línea:**
- Los tres devuelven la misma estructura JSON.
- FastAPI no necesita `jsonify`: serializa solo.

### 8.2. Criterios de elección

| Necesidad | Framework |
|---|---|
| Simplicidad | Flask |
| Rendimiento y async | FastAPI |
| Admin, ORM y auth integrados | Django |

### 8.3. Errores comunes

**Error 1 — No versionar la API** (`/v1/...`).

**Error 2 — No documentar** (FastAPI lo hace solo; en Flask/Django añádelo).

### 8.4. Checkpoint de comprensión

1. ¿Qué ventaja tiene FastAPI en las APIs?
2. ¿Cuándo elegirías Django?

### 8.5. Ejercicio propuesto

**Ejercicio 8.1.** Implementa el mismo CRUD en los tres frameworks.

**Pista:** compara líneas de código y validación.

---

## 9. Microservicios en Python

### 9.1. Qué son

Los **microservicios** dividen la aplicación en **servicios pequeños**, desplegables y escalables de forma independiente. Se comunican por HTTP/gRPC/mensajería.

```text
[Frontend] → [API Gateway] → [Servicio usuarios] → [DB usuarios]
                           → [Servicio ML]       → [Modelo]
                           → [Servicio pagos]    → [DB pagos]
```

### 9.2. Dos servicios FastAPI que se comunican

**Servicio A (usuarios):**

```python
from fastapi import FastAPI

app = FastAPI()


@app.get("/usuarios/{user_id}")
def obtener(user_id: int):
    return {"id": user_id, "nombre": "Ana"}
```

**Servicio B (consumidor):**

```python
import httpx
from fastapi import FastAPI

app = FastAPI()


@app.get("/perfil/{user_id}")
async def perfil(user_id: int):
    async with httpx.AsyncClient() as cliente:
        r = await cliente.get(f"http://usuarios:8000/usuarios/{user_id}")
    return {"perfil": r.json()}
```

**Explicación línea por línea:**
- El servicio B llama al A por HTTP.
- `httpx.AsyncClient` es asíncrono (no bloquea).

### 9.3. Comunicación asíncrona con colas

Para tareas y eventos, usa **RabbitMQ**, **Redis** o **Kafka** con Celery/RQ/Arq.

### 9.4. Errores comunes

**Error 1 — Microservicios prematuros** (complejidad innecesaria). Empieza monolítico.

**Error 2 — Acoplamiento fuerte** entre servicios.

**Error 3 — Sin observabilidad** (logs, métricas, trazas).

### 9.5. Checkpoint de comprensión

1. ¿Qué ventaja aportan los microservicios? ¿Y qué coste?
2. ¿Cuándo NO usarlos?

### 9.6. Ejercicio propuesto

**Ejercicio 9.1.** Divide una app en dos servicios que se comuniquen por HTTP.

**Pista:** usa Docker Compose para orquestarlos.

---

## 10. Buenas prácticas

1. **Separa UI, lógica y datos.**
2. **Reutiliza servicios** entre web, GUI y CLI.
3. **Carga modelos/recursos pesados una sola vez.**
4. **No bloquees** la UI ni el servidor.
5. **Valida en los límites** del sistema.
6. **Cachea** lo que cambia poco.
7. **Documenta los contratos** entre componentes.
8. **Empieza monolítico**; divide cuando haga falta.
9. **Agrega en la base** para datos grandes.
10. **Convierte formatos** (BGR↔RGB, NumPy↔JSON) en los límites.

---

## 11. Recursos

- **Flask:** <https://flask.palletsprojects.com/>
- **FastAPI:** <https://fastapi.tiangolo.com/>
- **Django:** <https://docs.djangoproject.com/>
- **SQLAlchemy:** <https://docs.sqlalchemy.org/>
- **Pandas:** <https://pandas.pydata.org/>
- **OpenCV:** <https://docs.opencv.org/>
- **Docker Compose:** <https://docs.docker.com/compose/>

---

## 12. Mini resumen final

- Las tecnologías se combinan en **capas**: UI, negocio, datos.
- **Flask + SQLAlchemy + Pandas** exponen análisis.
- **FastAPI + SQLModel + NumPy** sirven cálculos validados.
- **Django + scikit-learn** sirven modelos persistidos.
- **OpenCV + PySide6** integran visión en GUIs.
- **Selenium + Pandas** automatizan y analizan datos web.
- **APIs REST** se implementan en los tres frameworks.
- Los **microservicios** escalan, pero añaden complejidad.

---

### 🎯 Retos opcionales

1. **Reto 1:** Construye una API que reciba imágenes, las procese con OpenCV y devuelva un análisis.
2. **Reto 2:** Crea una GUI que consulte una API y muestre un gráfico de los datos.
3. **Reto 3:** Orquesta dos microservicios con Docker Compose.

**Anterior:** [21_opencv.md](21_opencv.md) · **Siguiente:** [23_buenas_practicas.md](23_buenas_practicas.md)
