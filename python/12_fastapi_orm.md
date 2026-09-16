# 12 · FastAPI y su ORM

**Versión recomendada:** FastAPI 0.110+ · Pydantic 2.x · SQLAlchemy 2.0 · Python 3.12
**Requisito:** [11_django_orm.md](11_django_orm.md)
**Nivel:** Intermedio → Avanzado
**Duración estimada de estudio:** 12–18 horas

---

## Índice de contenidos

1. [Introducción a FastAPI](#1-introducción-a-fastapi)
2. [Instalación y estructura de proyecto](#2-instalación-y-estructura-de-proyecto)
3. [Rutas y endpoints](#3-rutas-y-endpoints)
4. [Path parameters y query parameters](#4-path-parameters-y-query-parameters)
5. [Request body y Pydantic](#5-request-body-y-pydantic)
6. [Validación de datos con Pydantic](#6-validación-de-datos-con-pydantic)
7. [Modelos de respuesta](#7-modelos-de-respuesta)
8. [Dependencias e inyección](#8-dependencias-e-inyección)
9. [Seguridad: OAuth2 y JWT](#9-seguridad-oauth2-y-jwt)
10. [Documentación automática](#10-documentación-automática-swagger-redoc)
11. [Async/await en FastAPI](#11-asyncawait-en-fastapi)
12. [Integración con SQLAlchemy](#12-integración-con-sqlalchemy)
13. [Integración con SQLModel](#13-integración-con-sqlmodel)
14. [Integración con Tortoise ORM](#14-integración-con-tortoise-orm)
15. [Integración con Databases (encode)](#15-integración-con-databases-encode)
16. [Migraciones con Alembic](#16-migraciones-con-alembic)
17. [WebSockets](#17-websockets)
18. [Background tasks](#18-background-tasks)
19. [Testing con TestClient](#19-testing-con-testclient)
20. [Despliegue](#20-despliegue-con-uvicorn-y-gunicorn)
21. [Comparativa FastAPI vs Flask vs Django](#21-comparativa-fastapi-vs-flask-vs-django)
22. [Buenas prácticas](#22-buenas-prácticas)
23. [Recursos](#23-recursos)
24. [Mini resumen final](#24-mini-resumen-final)

---

## 1. Introducción a FastAPI

### 1.1. ¿Qué es FastAPI?

**FastAPI** es un framework moderno para construir APIs con Python. Se apoya en dos pilares:

- **Starlette** (ASGI): un framework asíncrono de alto rendimiento.
- **Pydantic** (validación): valida y serializa datos usando **tipos de Python**.

Sus ventajas principales:

- **Rendimiento alto:** comparable a Node.js o Go en benchmarks.
- **Documentación automática:** genera Swagger UI y ReDoc sin escribir nada.
- **Validación automática:** usando anotaciones de tipo (recuerda el archivo 08).
- **Async nativo:** aprovecha `async/await`.

### 1.2. La idea central: los tipos hacen el trabajo

La gran innovación de FastAPI es que **los tipos de Python son la especificación de la API**. Si escribes `item_id: int`, FastAPI sabe que:

1. Debe ser un entero.
2. Si no lo es, devolver un error 422.
3. Documentarlo así en OpenAPI.
4. Convertirlo automáticamente.

Esto conecta directamente con los **type hints** del archivo 08.

### 1.3. Aplicación mínima

```python
from fastapi import FastAPI

app = FastAPI()


@app.get("/")
def inicio():
    return {"mensaje": "¡Hola, FastAPI!"}
```

Ejecución:

```bash
pip install "fastapi[standard]"
fastapi dev main.py
```

Salida esperada:

```text
INFO:     Uvicorn running on http://127.0.0.1:8000
```

**Explicación línea por línea:**
- `FastAPI()` crea la aplicación.
- `@app.get("/")` registra un endpoint para GET.
- La función devuelve un `dict`, que FastAPI convierte a JSON automáticamente.

**Visita `http://127.0.0.1:8000/docs` y verás la documentación interactiva.**

### 1.4. Errores comunes

**Error 1 — Confundir FastAPI con un framework "todo incluido".**

No trae ORM ni admin. *Solución:* añádelos tú (SQLAlchemy, etc.).

**Error 2 — Usar funciones síncronas bloqueantes en endpoints `async`.**

Bloquea el servidor (sección 11).

### 1.5. Checkpoint de comprensión

1. ¿Sobre qué dos librerías se construye FastAPI?
2. ¿Qué papel juegan los tipos de Python?
3. ¿Dónde ves la documentación automática?

### 1.6. Ejercicio propuesto

**Ejercicio 1.1.** Crea una app con `/saludo/{nombre}` y visita `/docs`.

**Pista:** FastAPI genera la documentación sola.

---

## 2. Instalación y estructura de proyecto

### 2.1. Instalación

```bash
python -m venv .venv
source .venv/bin/activate
pip install "fastapi[standard]" sqlalchemy
```

### 2.2. Estructura recomendada

```text
app/
├── main.py
├── config.py
├── database.py
├── models/
│   └── user.py
├── schemas/
│   └── user.py
├── routers/
│   └── users.py
├── dependencies.py
└── tests/
```

**Explicación de las carpetas:**
- `models/`: modelos del ORM (tablas).
- `schemas/`: modelos Pydantic (entrada/salida de la API).
- `routers/`: endpoints agrupados por recurso.
- `dependencies.py`: dependencias compartidas.

**¿Por qué separar `models` y `schemas`?** Porque el modelo de base de datos y el de la API no tienen por qué coincidir: no quieres exponer la contraseña de la base, por ejemplo.

### 2.3. `main.py`

```python
from fastapi import FastAPI
from app.routers import users

app = FastAPI(title="Mi API", version="1.0.0")
app.include_router(users.router)


@app.get("/health")
def health():
    return {"status": "ok"}
```

**Explicación línea por línea:**
- `FastAPI(title=..., version=...)` da metadatos a la documentación.
- `app.include_router(users.router)` monta los endpoints del router.
- `/health` es un endpoint típico para comprobar que el servicio vive.

### 2.4. Errores comunes

**Error 1 — Poner todo en `main.py`.**

*Solución:* divide en routers y módulos.

### 2.5. Checkpoint de comprensión

1. ¿Qué diferencia hay entre `models/` y `schemas/`?
2. ¿Qué hace `include_router`?

### 2.6. Ejercicio propuesto

**Ejercicio 2.1.** Crea un router `items` y móntalo en `main.py`.

**Pista:** usa `APIRouter`.

---

## 3. Rutas y endpoints

### 3.1. Los decoradores de método

```python
from fastapi import FastAPI

app = FastAPI()


@app.get("/")
def raiz():
    return {"mensaje": "inicio"}


@app.post("/items")
def crear_item():
    return {"accion": "creado"}


@app.put("/items/{item_id}")
def reemplazar(item_id: int):
    return {"accion": "reemplazado", "id": item_id}


@app.patch("/items/{item_id}")
def actualizar(item_id: int):
    return {"accion": "actualizado", "id": item_id}


@app.delete("/items/{item_id}", status_code=204)
def borrar(item_id: int):
    return None
```

**Explicación línea por línea:**
- Cada decorador corresponde a un método HTTP.
- `status_code=204` fija el código de respuesta (sin contenido).
- Los nombres de función deben ser **únicos**.

### 3.2. Routers

Los routers agrupan endpoints:

```python
from fastapi import APIRouter

router = APIRouter(prefix="/users", tags=["users"])


@router.get("/")
def listar():
    return []


@router.get("/{user_id}")
def detalle(user_id: int):
    return {"id": user_id}
```

**Explicación línea por línea:**
- `prefix="/users"` añade el prefijo a todas las rutas.
- `tags=["users"]` agrupa los endpoints en la documentación.

### 3.3. Errores comunes

**Error 1 — Rutas que chocan.**

`/users/me` y `/users/{id}`: si defines `{id}` primero, `me` se interpreta como un id. *Solución:* define `/users/me` **antes**.

**Error 2 — No usar `tags`** y tener la documentación desordenada.

### 3.4. Checkpoint de comprensión

1. ¿Qué ventaja aporta un `APIRouter`?
2. ¿Por qué importa el orden de las rutas?

### 3.5. Ejercicio propuesto

**Ejercicio 3.1.** Crea un router con prefijo `/productos` y un endpoint de detalle.

**Pista:** `APIRouter(prefix="/productos")`.

---

## 4. Path parameters y query parameters

### 4.1. Path parameters

Un **path parameter** es parte de la URL (`/items/5`). Se declara en la ruta y con su tipo en la función:

```python
from fastapi import FastAPI

app = FastAPI()


@app.get("/items/{item_id}")
def leer(item_id: int):
    return {"item_id": item_id}
```

**Explicación línea por línea:**
- `{item_id}` es el parámetro en la ruta.
- `item_id: int` lo convierte a entero. Si visitas `/items/abc`, FastAPI devuelve **422** automáticamente.

### 4.2. Query parameters

Un **query parameter** va tras el `?` (`/items?skip=0&limit=10`). Se declara como un parámetro normal con valor por defecto:

```python
@app.get("/buscar")
def buscar(q: str | None = None, skip: int = 0, limit: int = 10):
    return {"q": q, "skip": skip, "limit": limit}
```

**Explicación línea por línea:**
- `q: str | None = None` es opcional.
- `skip: int = 0` y `limit: int = 10` tienen valores por defecto.

Visitar `/buscar?q=python&limit=5` devuelve `{"q": "python", "skip": 0, "limit": 5}`.

### 4.3. Validación con `Path` y `Query`

```python
from fastapi import Path, Query


@app.get("/items/{item_id}")
def leer(
    item_id: int = Path(gt=0),
    q: str = Query(min_length=3, max_length=50),
):
    return {"item_id": item_id, "q": q}
```

**Explicación línea por línea:**
- `Path(gt=0)` exige que el id sea mayor que 0.
- `Query(min_length=3, max_length=50)` limita la longitud de `q`.

### 4.4. Errores comunes

**Error 1 — Olvidar el tipo** → se trata como texto y no se valida.

**Error 2 — No validar rangos** y aceptar valores absurdos.

### 4.5. Checkpoint de comprensión

1. ¿Qué diferencia hay entre un path y un query parameter?
2. ¿Cómo haces opcional un query parameter?

### 4.6. Ejercicio propuesto

**Ejercicio 4.1.** Crea `/productos` con `skip`, `limit` y un filtro opcional `categoria`.

**Pista:** usa `Query` con `le`.

---

## 5. Request body y Pydantic

### 5.1. El cuerpo de la petición

Cuando un cliente envía datos (por ejemplo, para crear un recurso), van en el **cuerpo** (*body*) de la petición. FastAPI usa **modelos Pydantic** para validarlos.

### 5.2. Un modelo y un endpoint

```python
from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI()


class Item(BaseModel):
    """Item a crear."""

    nombre: str
    precio: float
    descripcion: str | None = None
    en_stock: bool = True


@app.post("/items", status_code=201)
def crear(item: Item):
    return {"item": item, "total": item.precio * 1.21}
```

**Explicación línea por línea:**
- `class Item(BaseModel)` define el esquema del cuerpo.
- `nombre: str` es obligatorio; `descripcion: str | None = None` es opcional.
- FastAPI valida el JSON entrante contra `Item` y **convierte** a un objeto `Item`.
- `item.precio` accede al valor validado.

**Petición:**

```bash
curl -X POST http://127.0.0.1:8000/items \
  -H "Content-Type: application/json" \
  -d '{"nombre": "Teclado", "precio": 49.99}'
```

```json
{"item": {"nombre": "Teclado", "precio": 49.99, "descripcion": null, "en_stock": true}, "total": 60.4879}
```

### 5.3. Combinar body, path y query

```python
@app.put("/items/{item_id}")
def actualizar(item_id: int, item: Item, q: str | None = None):
    return {"item_id": item_id, "item": item, "q": q}
```

**Explicación línea por línea:**
- FastAPI distingue automáticamente: `item_id` viene de la ruta, `item` del cuerpo, `q` de la query.

### 5.4. Errores comunes

**Error 1 — Leer `request.json()` a mano.**

*Solución:* usa un modelo Pydantic; ganas validación y documentación.

**Error 2 — Modelos mutables compartidos.**

*Solución:* Pydantic crea instancias nuevas, pero ten cuidado con valores por defecto mutables (usa `Field(default_factory=...)`).

### 5.5. Checkpoint de comprensión

1. ¿Qué valida FastAPI con un modelo Pydantic?
2. ¿Cómo distingue FastAPI entre path, query y body?

### 5.6. Ejercicio propuesto

**Ejercicio 5.1.** Crea un modelo `Producto` con validación de precio positivo.

**Pista:** investiga `Field(gt=0)`.

---

## 6. Validación de datos con Pydantic

### 6.1. Validación automática y conversión

Pydantic valida y **convierte** tipos. Si envías `"30"` donde se espera un `int`, lo convierte. Si no puede, devuelve un error detallado.

### 6.2. `Field` y restricciones

```python
from pydantic import BaseModel, Field


class Usuario(BaseModel):
    nombre: str = Field(min_length=2, max_length=80)
    edad: int = Field(ge=0, le=120)
    email: str
```

**Explicación línea por línea:**
- `min_length`/`max_length` limitan la longitud del texto.
- `ge=0` (greater or equal) y `le=120` (less or equal) limitan la edad.

### 6.3. Validadores personalizados

```python
from pydantic import BaseModel, EmailStr, field_validator


class Usuario(BaseModel):
    nombre: str
    email: EmailStr

    @field_validator("nombre")
    @classmethod
    def nombre_no_vacio(cls, valor: str) -> str:
        if not valor.strip():
            raise ValueError("El nombre no puede estar vacío")
        return valor.strip()
```

**Explicación línea por línea:**
- `@field_validator("nombre")` registra una validación personalizada para ese campo.
- `raise ValueError(...)` hace que Pydantic devuelva un error 422.
- `return valor.strip()` transforma el valor antes de aceptarlo.
- `EmailStr` valida el formato del email (requiere `email-validator`).

### 6.4. Modelos anidados y listas

```python
class Direccion(BaseModel):
    calle: str
    ciudad: str


class Cliente(BaseModel):
    nombre: str
    direcciones: list[Direccion] = []
```

**Explicación línea por línea:**
- Los modelos se anidan: `direcciones` es una lista de `Direccion`.
- Pydantic valida recursivamente toda la estructura.

### 6.5. Errores comunes

**Error 1 — Ignorar los errores de validación.** FastAPI devuelve 422 con detalle automáticamente.

**Error 2 — Usar `dict` en lugar de modelos.** Pierdes tipado y validación.

### 6.6. Checkpoint de comprensión

1. ¿Qué hace Pydantic si el tipo no coincide pero es convertible?
2. ¿Para qué sirve un `field_validator`?

### 6.7. Ejercicio propuesto

**Ejercicio 6.1.** Valida que un `precio` sea mayor que cero y redondea a 2 decimales.

**Pista:** usa `field_validator` con `round`.

---

## 7. Modelos de respuesta

### 7.1. Filtrar lo que se devuelve

`response_model` filtra y valida la respuesta, evitando exponer campos sensibles (como contraseñas):

```python
from pydantic import BaseModel


class UsuarioEntrada(BaseModel):
    nombre: str
    password: str


class UsuarioSalida(BaseModel):
    id: int
    nombre: str


@app.post("/usuarios", response_model=UsuarioSalida)
def crear_usuario(datos: UsuarioEntrada):
    return {"id": 1, "nombre": datos.nombre, "password": datos.password}
```

**Explicación línea por línea:**
- `datos: UsuarioEntrada` es lo que **entra** (con password).
- `response_model=UsuarioSalida` define lo que **sale**: FastAPI filtra el `password` aunque esté en el diccionario devuelto.
- La respuesta solo tendrá `id` y `nombre`.

### 7.2. Excluir valores nulos

```python
@app.get("/usuarios/{user_id}", response_model=UsuarioSalida, response_model_exclude_none=True)
def detalle(user_id: int):
    ...
```

### 7.3. Errores comunes

**Error 1 — Devolver el modelo de base de datos completo.**

*Solución:* define un modelo de salida sin campos internos.

**Error 2 — Confundir el modelo de entrada con el de salida.**

### 7.4. Checkpoint de comprensión

1. ¿Para qué sirve `response_model`?
2. ¿Cómo ocultas un campo en la respuesta?

### 7.5. Ejercicio propuesto

**Ejercicio 7.1.** Crea un `response_model` que oculte el email de un usuario.

**Pista:** define un modelo de salida sin ese campo.

---

## 8. Dependencias e inyección

### 8.1. Qué son las dependencias

El sistema de **dependencias** (`Depends`) inyecta recursos reutilizables en los endpoints: sesiones de base de datos, usuario actual, configuración... Se resuelven por endpoint y se **cachean** por petición.

**Analogía:** es como un servicio de catering: en lugar de preparar la mesa tú mismo, la pides y te la traen. Tú solo indicas qué necesitas.

### 8.2. Dependencia simple

```python
from fastapi import Depends, FastAPI

app = FastAPI()


def obtener_config() -> dict:
    """Devuelve la configuración de la app."""
    return {"entorno": "dev"}


@app.get("/info")
def info(config: dict = Depends(obtener_config)):
    return config
```

**Explicación línea por línea:**
- `Depends(obtener_config)` ejecuta la función y pasa su resultado como `config`.
- Es una forma limpia de reutilizar lógica común.

### 8.3. Dependencia con parámetros

```python
from fastapi import Depends, Query


def paginacion(skip: int = 0, limit: int = Query(10, le=100)) -> dict:
    return {"skip": skip, "limit": limit}


@app.get("/items")
def listar(pag: dict = Depends(paginacion)):
    return pag
```

**Explicación línea por línea:**
- La dependencia puede recibir parámetros (query, path...).
- FastAPI los resuelve automáticamente.

### 8.4. Dependencia de base de datos (la más importante)

```python
from collections.abc import Generator
from sqlalchemy.orm import Session
from app.database import SessionLocal


def get_db() -> Generator[Session, None, None]:
    """Proporciona una sesión de BD y la cierra al terminar."""
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()
```

**Explicación línea por línea:**
- `yield db` entrega la sesión al endpoint.
- El `finally` garantiza que la sesión se **cierre** siempre, aunque el endpoint falle.
- Es el patrón estándar para gestionar sesiones en FastAPI.

### 8.5. Errores comunes

**Error 1 — Abrir sesiones y no cerrarlas.**

*Solución:* usa `yield` en el `finally`.

**Error 2 — Dependencias costosas sin caché.**

FastAPI cachea las dependencias por defecto; si no quieres, usa `Depends(func, use_cache=False)`.

### 8.6. Checkpoint de comprensión

1. ¿Qué hace `Depends`?
2. ¿Por qué la dependencia de BD usa `yield`?
3. ¿Cuándo se cachea una dependencia?

### 8.7. Ejercicio propuesto

**Ejercicio 8.1.** Crea una dependencia `usuario_actual` que lea un token de la cabecera.

**Pista:** usa `Header`.

---

## 9. Seguridad: OAuth2 y JWT

### 9.1. El flujo de autenticación

1. El usuario envía sus credenciales a `/token`.
2. El servidor las valida y devuelve un **JWT** (*JSON Web Token*).
3. El cliente envía el JWT en `Authorization: Bearer <token>`.
4. El servidor verifica el token en cada petición protegida.

### 9.2. Instalación

```bash
pip install "python-jose[cryptography]" "passlib[bcrypt]"
```

### 9.3. Ejemplo completo

```python
from datetime import datetime, timedelta, timezone
from fastapi import Depends, FastAPI, HTTPException
from fastapi.security import OAuth2PasswordBearer, OAuth2PasswordRequestForm
from jose import JWTError, jwt
from passlib.context import CryptContext

SECRET_KEY = "cambia-esto"
ALGORITHM = "HS256"

pwd_context = CryptContext(schemes=["bcrypt"], deprecated="auto")
oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")

app = FastAPI()

USUARIOS = {"ana": pwd_context.hash("secreto")}


def verificar_password(plano: str, hashed: str) -> bool:
    return pwd_context.verify(plano, hashed)


def crear_token(data: dict) -> str:
    datos = data.copy()
    datos["exp"] = datetime.now(timezone.utc) + timedelta(minutes=30)
    return jwt.encode(datos, SECRET_KEY, algorithm=ALGORITHM)


@app.post("/token")
def login(form: OAuth2PasswordRequestForm = Depends()):
    if form.username not in USUARIOS or not verificar_password(
        form.password, USUARIOS[form.username]
    ):
        raise HTTPException(status_code=401, detail="Credenciales inválidas")
    return {"access_token": crear_token({"sub": form.username}), "token_type": "bearer"}


def usuario_actual(token: str = Depends(oauth2_scheme)) -> str:
    try:
        payload = jwt.decode(token, SECRET_KEY, algorithms=[ALGORITHM])
        return payload["sub"]
    except JWTError:
        raise HTTPException(status_code=401, detail="Token inválido")


@app.get("/perfil")
def perfil(usuario: str = Depends(usuario_actual)):
    return {"usuario": usuario}
```

**Explicación línea por línea:**
- `pwd_context.hash(...)` guarda la contraseña **hasheada** (nunca en texto plano).
- `crear_token` genera un JWT con fecha de expiración.
- `usuario_actual` decodifica y verifica el token; si falla, devuelve 401.
- `Depends(usuario_actual)` protege el endpoint.

### 9.4. Errores comunes

**Error 1 — Guardar contraseñas en texto plano.**

**Error 2 — `SECRET_KEY` en el código.** Usa variables de entorno.

**Error 3 — Tokens sin expiración.**

### 9.5. Checkpoint de comprensión

1. ¿Qué es un JWT?
2. ¿Por qué se hashean las contraseñas?
3. ¿Cómo se envía el token en cada petición?

### 9.6. Ejercicio propuesto

**Ejercicio 9.1.** Añade un endpoint protegido que devuelva datos solo con token válido.

**Pista:** usa `Depends(usuario_actual)`.

---

## 10. Documentación automática (Swagger, ReDoc)

### 10.1. Dos interfaces gratis

FastAPI genera **OpenAPI** y sirve:

- `/docs` → **Swagger UI** (interactiva, puedes probar los endpoints).
- `/redoc` → **ReDoc** (lectura).

### 10.2. Personalizar

```python
app = FastAPI(
    title="API de Ejemplo",
    description="Demostración de FastAPI",
    version="1.0.0",
    contact={"name": "Equipo", "email": "dev@example.com"},
    docs_url="/docs",
    redoc_url="/redoc",
)
```

### 10.3. Metadatos en endpoints

```python
@app.get(
    "/items",
    summary="Lista items",
    description="Devuelve todos los items paginados.",
    response_description="Lista de items",
    tags=["items"],
)
def listar():
    return []
```

**Explicación línea por línea:**
- `summary`, `description`, `tags` enriquecen la documentación.

### 10.4. Errores comunes

**Error 1 — No documentar respuestas de error.**

*Solución:* usa `responses={404: {...}}`.

**Error 2 — Endpoints sin `summary`** en APIs grandes.

### 10.5. Checkpoint de comprensión

1. ¿Qué dos interfaces de documentación genera FastAPI?
2. ¿Cómo añades metadatos a un endpoint?

### 10.6. Ejercicio propuesto

**Ejercicio 10.1.** Añade metadatos a un endpoint y revisa `/docs`.

**Pista:** usa `summary` y `tags`.

---

## 11. Async/await en FastAPI

### 11.1. La regla de oro

FastAPI corre sobre ASGI. Los endpoints `async def` se ejecutan en el **bucle de eventos**. La regla es simple pero crucial:

- **`async def`**: si usas librerías **asíncronas** (`httpx`, `asyncpg`, SQLAlchemy async).
- **`def`**: si usas librerías **síncronas**; FastAPI las ejecuta en un *threadpool*.

### 11.2. Ejemplo async

```python
import asyncio
from fastapi import FastAPI

app = FastAPI()


@app.get("/lento")
async def lento():
    await asyncio.sleep(1)
    return {"ok": True}
```

**Explicación línea por línea:**
- `await asyncio.sleep(1)` cede el control, permitiendo que otras peticiones avancen.

### 11.3. El error fatal

```python
import time
import requests

@app.get("/malo")
async def malo():
    time.sleep(1)                          # ❌ bloquea TODO el servidor
    requests.get("https://example.com")    # ❌ bloquea TODO el servidor
    return {"ok": True}
```

**¿Por qué es grave?** Porque `async def` corre en el bucle de eventos: si bloqueas, **ninguna** otra petición avanza. *Solución:* usa `asyncio.sleep`, `httpx.AsyncClient`, o declara el endpoint como `def` (síncrono) para que FastAPI lo ejecute en un threadpool.

### 11.4. Errores comunes

**Error 1 — Mezclar `requests` con `async def`.** Usa `httpx.AsyncClient`.

**Error 2 — Creer que `async def` acelera la CPU.** No lo hace; para CPU usa procesos.

### 11.5. Checkpoint de comprensión

1. ¿Cuándo usar `async def` y cuándo `def`?
2. ¿Por qué `time.sleep` en un endpoint async es un error grave?

### 11.6. Ejercicio propuesto

**Ejercicio 11.1.** Convierte un endpoint que usa `time.sleep` a `asyncio.sleep`.

**Pista:** compara la concurrencia con varias peticiones simultáneas.

---

## 12. Integración con SQLAlchemy

### 12.1. La combinación más común

SQLAlchemy 2.0 con una `Session` por petición inyectada con `Depends` es el patrón estándar.

### 12.2. `database.py`

```python
from sqlalchemy import create_engine
from sqlalchemy.orm import DeclarativeBase, sessionmaker

DATABASE_URL = "sqlite:///./app.db"

engine = create_engine(DATABASE_URL, connect_args={"check_same_thread": False})
SessionLocal = sessionmaker(bind=engine, autoflush=False, autocommit=False)


class Base(DeclarativeBase):
    """Base declarativa de los modelos."""
```

**Explicación línea por línea:**
- `create_engine` crea el motor; `check_same_thread=False` es necesario para SQLite con hilos.
- `sessionmaker` crea fábricas de sesiones.
- `Base` es la base de los modelos.

### 12.3. Modelo y schema

```python
from sqlalchemy import Integer, String
from sqlalchemy.orm import Mapped, mapped_column
from app.database import Base


class User(Base):
    __tablename__ = "users"

    id: Mapped[int] = mapped_column(Integer, primary_key=True)
    nombre: Mapped[str] = mapped_column(String(80))
    email: Mapped[str] = mapped_column(String(120), unique=True)
```

```python
from pydantic import BaseModel, EmailStr


class UserCreate(BaseModel):
    nombre: str
    email: EmailStr


class UserRead(BaseModel):
    id: int
    nombre: str
    email: EmailStr

    model_config = {"from_attributes": True}
```

**Explicación línea por línea:**
- El **modelo** SQLAlchemy define la tabla.
- El **schema** Pydantic define la API.
- `model_config = {"from_attributes": True}` permite construir el schema a partir de un objeto ORM.

### 12.4. Endpoints CRUD

```python
from fastapi import APIRouter, Depends, HTTPException
from sqlalchemy import select
from sqlalchemy.orm import Session
from app.database import get_db
from app.models.user import User
from app.schemas.user import UserCreate, UserRead

router = APIRouter(prefix="/users", tags=["users"])


@router.post("/", response_model=UserRead, status_code=201)
def crear(datos: UserCreate, db: Session = Depends(get_db)):
    usuario = User(**datos.model_dump())
    db.add(usuario)
    db.commit()
    db.refresh(usuario)
    return usuario


@router.get("/", response_model=list[UserRead])
def listar(db: Session = Depends(get_db)):
    return db.execute(select(User)).scalars().all()


@router.get("/{user_id}", response_model=UserRead)
def detalle(user_id: int, db: Session = Depends(get_db)):
    usuario = db.get(User, user_id)
    if not usuario:
        raise HTTPException(status_code=404, detail="No encontrado")
    return usuario
```

**Explicación línea por línea:**
- `datos.model_dump()` convierte el modelo Pydantic a diccionario.
- `db.add`, `db.commit`, `db.refresh` insertan y recargan el objeto (para obtener el id).
- `HTTPException` genera respuestas de error correctas.

### 12.5. Errores comunes

**Error 1 — Usar `check_same_thread=False` en producción** (específico de SQLite).

**Error 2 — No cerrar la sesión.** Usa el `finally` del generador.

### 12.6. Checkpoint de comprensión

1. ¿Por qué separar modelo y schema?
2. ¿Qué hace `db.refresh`?
3. ¿Cómo se cierra la sesión?

### 12.7. Ejercicio propuesto

**Ejercicio 12.1.** Añade un endpoint `PUT /users/{id}` para actualizar.

**Pista:** usa `db.commit()` y `db.refresh()`.

---

## 13. Integración con SQLModel

### 13.1. Qué es SQLModel

**SQLModel** (del autor de FastAPI) unifica modelos de Pydantic y SQLAlchemy en **una sola clase**.

### 13.2. Ejemplo

```python
from sqlmodel import Field, Session, SQLModel, create_engine, select


class Hero(SQLModel, table=True):
    id: int | None = Field(default=None, primary_key=True)
    nombre: str
    poder: str


engine = create_engine("sqlite:///heroes.db")
SQLModel.metadata.create_all(engine)


@app.post("/heroes", response_model=Hero)
def crear(hero: Hero):
    with Session(engine) as session:
        session.add(hero)
        session.commit()
        session.refresh(hero)
        return hero


@app.get("/heroes")
def listar():
    with Session(engine) as session:
        return session.exec(select(Hero)).all()
```

**Explicación línea por línea:**
- `table=True` convierte la clase en una tabla.
- `Field(default=None, primary_key=True)` define la clave.
- Es a la vez modelo Pydantic y modelo de tabla: menos código.

### 13.3. Errores comunes

**Error 1 — Usar el mismo modelo para tabla y respuesta** cuando hay campos sensibles. Sepáralos.

### 13.4. Checkpoint de comprensión

1. ¿Qué ventaja aporta SQLModel?
2. ¿Qué hace `table=True`?

### 13.5. Ejercicio propuesto

**Ejercicio 13.1.** Crea un `HeroCreate` sin `id` y un `HeroRead` con `id`.

**Pista:** `table=True` solo en el modelo de tabla.

---

## 14. Integración con Tortoise ORM

### 14.1. Qué es Tortoise ORM

**Tortoise ORM** es un ORM **asíncrono** inspirado en el de Django, ideal para FastAPI async puro.

### 14.2. Ejemplo

```python
from tortoise import fields, models


class Usuario(models.Model):
    id = fields.IntField(pk=True)
    nombre = fields.CharField(max_length=80)
    email = fields.CharField(max_length=120, unique=True)

    class Meta:
        table = "usuarios"
```

```python
from fastapi import FastAPI
from tortoise.contrib.fastapi import register_tortoise

app = FastAPI()

register_tortoise(
    app,
    db_url="sqlite://db.sqlite3",
    modules={"models": ["app.models"]},
    generate_schemas=True,
    add_exception_handlers=True,
)
```

**Explicación línea por línea:**
- `register_tortoise` conecta el ORM a FastAPI y gestiona el ciclo de vida.
- `generate_schemas=True` crea las tablas (útil en desarrollo).

### 14.3. Errores comunes

**Error 1 — Mezclar Tortoise con SQLAlchemy** en el mismo proyecto.

**Error 2 — Olvidar `generate_schemas`** en desarrollo.

### 14.4. Checkpoint de comprensión

1. ¿Qué hace especial a Tortoise ORM?
2. ¿Qué hace `register_tortoise`?

### 14.5. Ejercicio propuesto

**Ejercicio 14.1.** Crea un CRUD asíncrono de `Usuario` con Tortoise.

**Pista:** usa `await Usuario.create(...)`.

---

## 15. Integración con Databases (encode)

### 15.1. Qué es `databases`

**`databases`** es una capa de acceso **asíncrona** sobre SQLAlchemy Core, útil para consultas ligeras.

### 15.2. Ejemplo

```python
import databases
import sqlalchemy

DATABASE_URL = "sqlite:///test.db"
database = databases.Database(DATABASE_URL)
metadata = sqlalchemy.MetaData()

users = sqlalchemy.Table(
    "users",
    metadata,
    sqlalchemy.Column("id", sqlalchemy.Integer, primary_key=True),
    sqlalchemy.Column("nombre", sqlalchemy.String(80)),
)


@app.on_event("startup")
async def startup():
    await database.connect()


@app.on_event("shutdown")
async def shutdown():
    await database.disconnect()


@app.get("/users")
async def listar():
    query = users.select()
    return await database.fetch_all(query)
```

**Explicación línea por línea:**
- `database.connect()` abre el pool al arrancar.
- `fetch_all(query)` ejecuta una consulta asíncrona.

### 15.3. Errores comunes

**Error 1 — Olvidar `connect`/`disconnect`** en el ciclo de vida.

### 15.4. Checkpoint de comprensión

1. ¿Qué ventaja aporta `databases`?
2. ¿Cuándo se conecta y desconecta?

### 15.5. Ejercicio propuesto

**Ejercicio 15.1.** Inserta un usuario con `database.execute(users.insert().values(...))`.

**Pista:** usa `await`.

---

## 16. Migraciones con Alembic

### 16.1. Versionar el esquema

**Alembic** versiona el esquema de SQLAlchemy, igual que en Flask/Django.

### 16.2. Configuración

```bash
pip install alembic
alembic init migrations
```

**`migrations/env.py`:**

```python
from app.database import Base
target_metadata = Base.metadata
```

**`alembic.ini`:**

```ini
sqlalchemy.url = sqlite:///./app.db
```

### 16.3. Uso

```bash
alembic revision --autogenerate -m "crear users"
alembic upgrade head
alembic downgrade -1
```

**Explicación línea por línea:**
- `revision --autogenerate` compara modelos y base y genera el script.
- `upgrade head` aplica todas las migraciones pendientes.

### 16.4. Errores comunes

**Error 1 — No importar los modelos** en `env.py` → migraciones vacías.

**Error 2 — Aplicar sin revisar** el script generado.

### 16.5. Checkpoint de comprensión

1. ¿Qué hace `autogenerate`?
2. ¿Por qué importar los modelos en `env.py`?

### 16.6. Ejercicio propuesto

**Ejercicio 16.1.** Genera y aplica una migración para la tabla `users`.

**Pista:** revisa el archivo generado antes de `upgrade`.

---

## 17. WebSockets

### 17.1. Comunicación bidireccional

FastAPI soporta **WebSockets** nativamente: conexiones persistentes donde cliente y servidor se envían mensajes en ambos sentidos.

### 17.2. Ejemplo: chat

```python
from fastapi import FastAPI, WebSocket, WebSocketDisconnect

app = FastAPI()


class GestorConexiones:
    def __init__(self) -> None:
        self.activas: list[WebSocket] = []

    async def conectar(self, ws: WebSocket) -> None:
        await ws.accept()
        self.activas.append(ws)

    def desconectar(self, ws: WebSocket) -> None:
        self.activas.remove(ws)

    async def difundir(self, mensaje: str) -> None:
        for conexion in self.activas:
            await conexion.send_text(mensaje)


gestor = GestorConexiones()


@app.websocket("/ws")
async def websocket_endpoint(websocket: WebSocket):
    await gestor.conectar(websocket)
    try:
        while True:
            mensaje = await websocket.receive_text()
            await gestor.difundir(f"Mensaje: {mensaje}")
    except WebSocketDisconnect:
        gestor.desconectar(websocket)
```

**Explicación línea por línea:**
- `ws.accept()` acepta la conexión.
- El bucle `while True` recibe mensajes.
- `difundir` los reenvía a todos los conectados.
- `WebSocketDisconnect` se captura al cerrar.

### 17.3. Errores comunes

**Error 1 — No manejar `WebSocketDisconnect`.**

**Error 2 — Bloquear el event loop** dentro del bucle.

### 17.4. Checkpoint de comprensión

1. ¿Qué diferencia hay entre HTTP y WebSocket?
2. ¿Qué hace `accept`?

### 17.5. Ejercicio propuesto

**Ejercicio 17.1.** Añade un mensaje de bienvenida al conectarse.

**Pista:** envía antes del bucle `while`.

---

## 18. Background tasks

### 18.1. Trabajo tras la respuesta

`BackgroundTasks` ejecuta trabajo **después** de enviar la respuesta, sin hacer esperar al cliente:

```python
from fastapi import BackgroundTasks, FastAPI

app = FastAPI()


def enviar_email(email: str) -> None:
    """Simula el envío de un correo."""
    print(f"Enviando email a {email}")


@app.post("/registro")
def registrar(email: str, tareas: BackgroundTasks):
    tareas.add_task(enviar_email, email)
    return {"mensaje": "Usuario registrado"}
```

**Explicación línea por línea:**
- `tareas.add_task(funcion, *args)` programa la tarea.
- La respuesta se envía **inmediatamente**; la tarea corre después.

### 18.2. Cuándo NO usarlo

Para tareas largas o que deben sobrevivir a reinicios, usa un sistema de **colas** (Celery, RQ, Arq).

### 18.3. Errores comunes

**Error 1 — Usar BackgroundTasks para procesos largos.** Bloquea un worker.

**Error 2 — Esperar un resultado** de una background task.

### 18.4. Checkpoint de comprensión

1. ¿Cuándo corre una background task?
2. ¿Cuándo necesitas una cola en lugar de BackgroundTasks?

### 18.5. Ejercicio propuesto

**Ejercicio 18.1.** Añade una tarea que registre en log el email registrado.

**Pista:** `tareas.add_task(...)`.

---

## 19. Testing con TestClient

### 19.1. El cliente de pruebas

FastAPI se testea con `TestClient` (basado en `httpx`), que simula peticiones sin levantar servidor:

```python
from fastapi.testclient import TestClient
from app.main import app

cliente = TestClient(app)


def test_health():
    respuesta = cliente.get("/health")
    assert respuesta.status_code == 200
    assert respuesta.json() == {"status": "ok"}


def test_crear_usuario():
    respuesta = cliente.post("/users/", json={"nombre": "Ana", "email": "ana@example.com"})
    assert respuesta.status_code == 201
    assert respuesta.json()["nombre"] == "Ana"
```

Ejecución:

```bash
pytest -v
```

### 19.2. Sobrescribir dependencias

Para tests, se reemplaza la BD real por una de prueba:

```python
from app.database import get_db
from app.main import app


def get_db_test():
    ...   # sesión de test


app.dependency_overrides[get_db] = get_db_test
```

**Explicación línea por línea:**
- `dependency_overrides` permite sustituir dependencias en tests.

### 19.3. Errores comunes

**Error 1 — Tests que comparten BD.**

**Error 2 — No limpiar `dependency_overrides`** entre tests.

### 19.4. Checkpoint de comprensión

1. ¿Qué hace `TestClient`?
2. ¿Para qué sirve `dependency_overrides`?

### 19.5. Ejercicio propuesto

**Ejercicio 19.1.** Testea que crear un usuario con email inválido devuelve 422.

**Pista:** envía un email mal formado.

---

## 20. Despliegue con Uvicorn y Gunicorn

### 20.1. Servidores ASGI

Uvicorn es el servidor ASGI; en producción se usa con Gunicorn como gestor de procesos (`UvicornWorker`).

### 20.2. Comandos

```bash
pip install "uvicorn[standard]" gunicorn

# desarrollo
uvicorn app.main:app --reload

# producción
gunicorn app.main:app -w 4 -k uvicorn.workers.UvicornWorker -b 0.0.0.0:8000
```

**Explicación línea por línea:**
- `-w 4` lanza 4 workers.
- `-k uvicorn.workers.UvicornWorker` usa el worker ASGI.
- `--reload` solo en desarrollo.

### 20.3. Docker

```dockerfile
FROM python:3.12-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
CMD ["gunicorn", "app.main:app", "-w", "4", "-k", "uvicorn.workers.UvicornWorker", "-b", "0.0.0.0:8000"]
```

### 20.4. Errores comunes

**Error 1 — Usar `--reload` en producción.**

**Error 2 — Un solo worker** con endpoints bloqueantes.

### 20.5. Checkpoint de comprensión

1. ¿Qué papel juega Gunicorn con Uvicorn?
2. ¿Por qué varios workers?

### 20.6. Ejercicio propuesto

**Ejercicio 20.1.** Despliega con Docker y 4 workers, y prueba la carga con `ab` o `hey`.

**Pista:** mide peticiones por segundo.

---

## 21. Comparativa FastAPI vs Flask vs Django

| Criterio | FastAPI | Flask | Django |
|---|---|---|---|
| Tipo | API async | Microframework | Full-stack |
| Rendimiento | Muy alto | Medio | Medio |
| Validación | Pydantic | Manual | Formularios/DRF |
| Docs automáticas | Sí | No (extensión) | No (DRF) |
| ORM incluido | No | No | Sí |
| Admin | No | No | Sí |
| Curva de aprendizaje | Media | Baja | Alta |
| Ideal para | APIs, microservicios | Prototipos, APIs simples | Apps completas con admin |

### 21.1. Ejercicio propuesto

**Ejercicio 21.1.** Escribe el mismo endpoint `/suma` en los tres frameworks.

**Pista:** compara la cantidad de código y la validación.

---

## 22. Buenas prácticas

1. **Separa routers, schemas y modelos.**
2. **Usa `Depends`** para sesiones y configuración.
3. **Modelos de salida separados** para no exponer datos.
4. **`async def` solo con librerías asíncronas.**
5. **Valida todo con Pydantic.**
6. **Usa Alembic** para migraciones.
7. **Autentica con OAuth2 + JWT.**
8. **Aprovecha la documentación automática.**
9. **Tests con `TestClient`.**
10. **Hashea siempre las contraseñas.**

---

## 23. Recursos

- **Documentación oficial:** <https://fastapi.tiangolo.com/>
- **Pydantic:** <https://docs.pydantic.dev/>
- **SQLModel:** <https://sqlmodel.tiangolo.com/>
- **Uvicorn:** <https://www.uvicorn.org/>
- **Tutorial completo:** <https://fastapi.tiangolo.com/tutorial/>

---

## 24. Mini resumen final

- FastAPI es un framework **async** para APIs con validación por tipos.
- **Pydantic** valida entrada y salida; `response_model` filtra campos.
- Los **path/query params** y los **body models** se validan automáticamente.
- Las **dependencias** inyectan sesiones, auth y configuración.
- **OAuth2 + JWT** implementa autenticación.
- Se integra con **SQLAlchemy**, **SQLModel**, **Tortoise** y **databases**.
- **Alembic** gestiona migraciones.
- Soporta **WebSockets** y **background tasks**.
- Se despliega con **Uvicorn + Gunicorn**.

---

### 🎯 Retos opcionales

1. **Reto 1:** Construye una API CRUD completa con SQLAlchemy async y Alembic.
2. **Reto 2:** Añade autenticación JWT y roles.
3. **Reto 3:** Implementa un chat con WebSockets y persistencia.

**Anterior:** [11_django_orm.md](11_django_orm.md) · **Siguiente:** [13_pyramid.md](13_pyramid.md)
