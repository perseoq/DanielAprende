# 14 · SQLAlchemy (Núcleo, sin Framework Web)

**Versión recomendada:** SQLAlchemy 2.0 · Python 3.12
**Requisito:** [13_pyramid.md](13_pyramid.md) (o [08_python_avanzado.md](08_python_avanzado.md))
**Nivel:** Intermedio → Avanzado
**Duración estimada de estudio:** 12–18 horas

---

## Índice de contenidos

1. [Introducción a SQLAlchemy](#1-introducción-a-sqlalchemy)
2. [Arquitectura: Core y ORM](#2-arquitectura-core-y-orm)
3. [Instalación y configuración](#3-instalación-y-configuración)
4. [Engine y conexiones](#4-engine-y-conexiones)
5. [Dialectos](#5-dialectos-sqlite-postgresql-mysql-oracle-sql-server)
6. [SQLAlchemy Core](#6-sqlalchemy-core)
7. [SQLAlchemy ORM](#7-sqlalchemy-orm)
8. [Alembic para migraciones](#8-alembic-para-migraciones)
9. [Transacciones y aislamiento](#9-transacciones-y-aislamiento)
10. [Connection pooling](#10-connection-pooling)
11. [Optimización y profiling](#11-optimización-y-profiling)
12. [SQLAlchemy 2.0: novedades](#12-sqlalchemy-20-novedades)
13. [Integración con Pandas](#13-integración-con-pandas)
14. [Testing de modelos SQLAlchemy](#14-testing-de-modelos-sqlalchemy)
15. [Buenas prácticas](#15-buenas-prácticas)
16. [Recursos](#16-recursos)
17. [Mini resumen final](#17-mini-resumen-final)

---

## 1. Introducción a SQLAlchemy

### 1.1. La librería de acceso a datos más potente

**SQLAlchemy** es la biblioteca de acceso a bases de datos más potente y madura de Python. A diferencia de un ORM simple, ofrece **dos capas** que puedes usar por separado o juntas:

- **Core:** SQL construido programáticamente, sin ORM. Tú describes consultas con objetos Python y SQLAlchemy genera el SQL.
- **ORM:** mapeo de clases a tablas, como en los archivos anteriores.

**¿Por qué importa esta distinción?** Porque no todos los problemas se resuelven igual de bien. Para reportes masivos, Core es más eficiente; para lógica de negocio, el ORM es más cómodo. SQLAlchemy te da ambos.

### 1.2. ¿Por qué SQLAlchemy y no otro?

- **Portabilidad:** el mismo código funciona en SQLite, PostgreSQL, MySQL, Oracle, SQL Server.
- **Control total:** desde SQL puro (Core) hasta objetos (ORM).
- **Potencia:** relaciones, herencia, eventos, migraciones.
- **Madurez:** es la base de Flask-SQLAlchemy y SQLModel.

### 1.3. Errores comunes

**Error 1 — Confundir Core con ORM.**

Son dos estilos distintos. Core genera SQL; el ORM trabaja con objetos.

**Error 2 — Usar el ORM para consultas masivas** donde Core sería más eficiente.

### 1.4. Checkpoint de comprensión

1. ¿Qué dos capas ofrece SQLAlchemy?
2. ¿Cuándo usarías Core en lugar del ORM?

### 1.5. Ejercicio propuesto

**Ejercicio 1.1.** Enumera dos casos donde usarías Core y dos donde usarías ORM.

**Pista:** piensa en reportes masivos vs. lógica de negocio.

---

## 2. Arquitectura: Core y ORM

### 2.1. El diagrama mental

```text
Tu código
   │
   ├── ORM (Session, declarative)  ← objetos
   │        │
   └── Core (Engine, Connection, Table, select)  ← SQL
            │
        Dialecto (sqlite, postgresql, mysql…)
            │
        DBAPI (sqlite3, psycopg, pymysql…)
            │
        Base de datos
```

**Explicación de cada pieza:**
- **Engine:** punto de entrada; gestiona el pool de conexiones.
- **Connection:** una conexión concreta con la base.
- **Dialect:** traduce las operaciones a SQL del motor concreto.
- **DBAPI:** la librería de bajo nivel (por ejemplo, `sqlite3`).
- **Session (ORM):** unidad de trabajo que agrupa operaciones sobre objetos.

### 2.2. El flujo de una consulta

1. Tu código pide algo (por ejemplo, un usuario).
2. El ORM lo traduce a una consulta Core.
3. El Core genera SQL con ayuda del dialecto.
4. El dialecto usa el DBAPI para hablar con la base.
5. La base devuelve filas.
6. El ORM las convierte en objetos.

### 2.3. Errores comunes

**Error 1 — Crear un `Engine` por operación.**

Es costoso: el Engine gestiona un pool. *Solución:* créalo una vez y reutilízalo.

### 2.4. Checkpoint de comprensión

1. ¿Qué hace el Engine?
2. ¿Qué papel juega el dialecto?

### 2.5. Ejercicio propuesto

**Ejercicio 2.1.** Dibuja el flujo de una consulta ORM hasta la base de datos.

**Pista:** Session → Connection → Dialect → DBAPI.

---

## 3. Instalación y configuración

### 3.1. Instalación

```bash
pip install sqlalchemy
# drivers:
pip install psycopg[binary]   # PostgreSQL
pip install pymysql            # MySQL
```

### 3.2. La conexión más simple

```python
from sqlalchemy import create_engine, text

engine = create_engine("sqlite:///ejemplo.db", echo=True)

with engine.connect() as conn:
    resultado = conn.execute(text("SELECT 1"))
    print(resultado.scalar())
```

Salida esperada (con `echo=True` verás el SQL):

```text
2026-09-16 10:30:45,123 INFO sqlalchemy.engine.Engine SELECT 1
1
```

**Explicación línea por línea:**
- `create_engine(...)` crea el motor.
- `echo=True` imprime el SQL generado: **muy útil para aprender y depurar**.
- `with engine.connect() as conn:` abre una conexión y la cierra al salir.
- `text("SELECT 1")` envuelve SQL crudo.
- `.scalar()` devuelve el primer valor.

### 3.3. Errores comunes

**Error 1 — Dejar `echo=True` en producción** (ruido y coste).

**Error 2 — URLs mal formadas** en PostgreSQL/MySQL.

### 3.4. Checkpoint de comprensión

1. ¿Para qué sirve `echo=True`?
2. ¿Por qué usar `with engine.connect()`?

### 3.5. Ejercicio propuesto

**Ejercicio 3.1.** Conéctate a una base SQLite en memoria y ejecuta `SELECT 1`.

**Pista:** `create_engine("sqlite:///:memory:")`.

---

## 4. Engine y conexiones

### 4.1. Engine vs. Connection

El **Engine** es el gestor del pool; una **Connection** es una conexión concreta. `engine.begin()` abre una **transacción**.

### 4.2. Crear tablas e insertar

```python
from sqlalchemy import create_engine, text

engine = create_engine("sqlite:///ejemplo.db")

with engine.begin() as conn:
    conn.execute(text("CREATE TABLE IF NOT EXISTS saludo (id INTEGER PRIMARY KEY, texto TEXT)"))
    conn.execute(text("INSERT INTO saludo (texto) VALUES (:t)"), {"t": "hola"})

with engine.connect() as conn:
    filas = conn.execute(text("SELECT texto FROM saludo")).all()
    print(filas)
```

Salida esperada:

```text
[('hola',)]
```

**Explicación línea por línea:**
- `engine.begin()` abre una transacción y la **confirma** automáticamente al salir.
- `text("... :t")` define un **parámetro** seguro.
- `{"t": "hola"}` rellena el parámetro.
- `engine.connect()` es para operaciones de lectura (no confirma nada).

**La regla de oro de la seguridad:** **nunca** concatenes valores en SQL. Usa parámetros (`:nombre`). Así evitas la **inyección SQL**, uno de los ataques más graves.

```python
# ❌ PELIGROSO
conn.execute(text(f"SELECT * FROM users WHERE name = '{nombre}'"))

# ✅ SEGURO
conn.execute(text("SELECT * FROM users WHERE name = :nombre"), {"nombre": nombre})
```

### 4.3. Errores comunes

**Error 1 — Concatenar SQL** (inyección).

**Error 2 — Olvidar `commit`** al usar `connect()`.

### 4.4. Checkpoint de comprensión

1. ¿Qué diferencia hay entre `connect()` y `begin()`?
2. ¿Por qué usar parámetros en lugar de concatenar?

### 4.5. Ejercicio propuesto

**Ejercicio 4.1.** Inserta tres filas y consulta las que cumplan una condición.

**Pista:** usa `WHERE` con parámetro.

---

## 5. Dialectos (SQLite, PostgreSQL, MySQL, Oracle, SQL Server)

### 5.1. Qué es un dialecto

El **dialecto** adapta SQLAlchemy al motor concreto. Cada motor tiene su URL:

| Motor | URL |
|---|---|
| SQLite | `sqlite:///archivo.db` |
| PostgreSQL | `postgresql+psycopg://user:pass@host/db` |
| MySQL | `mysql+pymysql://user:pass@host/db` |
| Oracle | `oracle+oracledb://user:pass@host/db` |
| SQL Server | `mssql+pyodbc://user:pass@dsn` |

### 5.2. Configurar el engine por motor

```python
from sqlalchemy import create_engine

# PostgreSQL con pool y timeout
engine = create_engine(
    "postgresql+psycopg://user:pass@localhost/app",
    pool_size=10,
    max_overflow=20,
    pool_pre_ping=True,
)

# MySQL con charset
engine = create_engine("mysql+pymysql://user:pass@localhost/app?charset=utf8mb4")
```

**Explicación línea por línea:**
- `+psycopg` indica el **driver** a usar.
- `pool_size=10` mantiene 10 conexiones.
- `pool_pre_ping=True` verifica la conexión antes de usarla (evita errores por conexiones cerradas).
- `charset=utf8mb4` asegura el soporte completo de Unicode en MySQL.

### 5.3. Errores comunes

**Error 1 — SQLite para producción con concurrencia alta.** Usa PostgreSQL.

**Error 2 — Olvidar instalar el driver** correspondiente.

### 5.4. Checkpoint de comprensión

1. ¿Qué indica la parte `+driver` de la URL?
2. ¿Qué hace `pool_pre_ping`?

### 5.5. Ejercicio propuesto

**Ejercicio 5.1.** Escribe la URL de conexión para PostgreSQL, MySQL y SQLite.

**Pista:** incluye el driver (`+psycopg`, `+pymysql`).

---

## 6. SQLAlchemy Core

### 6.1. Metadata y Table

En Core, describes las tablas con `Table` y las agrupas en `MetaData`:

```python
from sqlalchemy import Column, Integer, MetaData, String, Table

metadata = MetaData()

usuarios = Table(
    "usuarios",
    metadata,
    Column("id", Integer, primary_key=True),
    Column("nombre", String(80), nullable=False),
    Column("email", String(120), unique=True),
)

metadata.create_all(engine)
```

**Explicación línea por línea:**
- `MetaData()` agrupa el esquema.
- `Table(...)` describe una tabla con sus columnas.
- `metadata.create_all(engine)` crea todas las tablas descritas.

### 6.2. Columnas y tipos

```python
from sqlalchemy import Boolean, Date, DateTime, Float, Numeric, Text

tabla = Table(
    "productos",
    metadata,
    Column("id", Integer, primary_key=True),
    Column("nombre", String(100), nullable=False),
    Column("precio", Numeric(10, 2), default=0),
    Column("activo", Boolean, default=True),
    Column("descripcion", Text),
)
```

**Explicación línea por línea:**
- `Numeric(10, 2)` para dinero.
- `default=...` asigna un valor por defecto.

### 6.3. Expresiones y consultas

```python
from sqlalchemy import delete, insert, select, update

# INSERT
with engine.begin() as conn:
    conn.execute(insert(usuarios).values(nombre="Ana", email="ana@example.com"))

# SELECT
with engine.connect() as conn:
    stmt = select(usuarios).where(usuarios.c.nombre == "Ana")
    for fila in conn.execute(stmt):
        print(fila)

# UPDATE
with engine.begin() as conn:
    conn.execute(update(usuarios).where(usuarios.c.id == 1).values(nombre="Ana María"))

# DELETE
with engine.begin() as conn:
    conn.execute(delete(usuarios).where(usuarios.c.id == 1))
```

**Explicación línea por línea:**
- `usuarios.c.nombre` accede a una columna (la `c` es de *columns*).
- `select(...)`, `insert(...)`, `update(...)`, `delete(...)` construyen consultas.
- `.where(...)` filtra; `.values(...)` asigna.

### 6.4. Joins y subconsultas

```python
stmt = (
    select(usuarios.c.nombre, pedidos.c.total)
    .select_from(usuarios.join(pedidos, usuarios.c.id == pedidos.c.usuario_id))
    .where(pedidos.c.total > 100)
)
```

**Explicación línea por línea:**
- `usuarios.join(pedidos, condicion)` une dos tablas.
- `select_from(...)` especifica el origen.

### 6.5. Funciones SQL y agregaciones

```python
from sqlalchemy import func, select

stmt = select(func.count(usuarios.c.id), func.avg(pedidos.c.total))
```

**Explicación línea por línea:**
- `func` da acceso a las funciones SQL (`count`, `avg`, `sum`...).

### 6.6. Errores comunes

**Error 1 — Concatenar valores en SQL** en lugar de parámetros.

**Error 2 — Usar `select([...])` (estilo 1.x)** en lugar de `select(...)` (2.0).

### 6.7. Checkpoint de comprensión

1. ¿Qué es `MetaData`?
2. ¿Cómo accedes a una columna de una `Table`?
3. ¿Qué hace `func`?

### 6.8. Ejercicio propuesto

**Ejercicio 6.1.** Crea una tabla `libros` con `titulo` y `paginas`, inserta datos y calcula la media de páginas.

**Pista:** `func.avg`.

---

## 7. SQLAlchemy ORM

### 7.1. Declarative Base

```python
from sqlalchemy.orm import DeclarativeBase


class Base(DeclarativeBase):
    """Base declarativa de los modelos."""
```

### 7.2. Definición de modelos

```python
from sqlalchemy import ForeignKey, String
from sqlalchemy.orm import Mapped, mapped_column, relationship


class Usuario(Base):
    __tablename__ = "usuarios"

    id: Mapped[int] = mapped_column(primary_key=True)
    nombre: Mapped[str] = mapped_column(String(80))
    email: Mapped[str] = mapped_column(String(120), unique=True)

    pedidos: Mapped[list["Pedido"]] = relationship(back_populates="usuario")

    def __repr__(self) -> str:
        return f"Usuario(id={self.id!r}, nombre={self.nombre!r})"


class Pedido(Base):
    __tablename__ = "pedidos"

    id: Mapped[int] = mapped_column(primary_key=True)
    total: Mapped[float]
    usuario_id: Mapped[int] = mapped_column(ForeignKey("usuarios.id"))

    usuario: Mapped["Usuario"] = relationship(back_populates="pedidos")
```

**Explicación línea por línea:**
- `Mapped[int]` es la anotación moderna (2.0) que combina el tipo Python con la columna.
- `mapped_column(...)` define las opciones de la columna.
- `relationship(back_populates="...")` enlaza ambas direcciones.

### 7.3. Session y Unit of Work

```python
from sqlalchemy.orm import sessionmaker

Session = sessionmaker(bind=engine)

with Session() as session:
    usuario = Usuario(nombre="Ana", email="ana@example.com")
    session.add(usuario)
    session.commit()
    print(usuario.id)
```

**Explicación línea por línea:**
- `sessionmaker(bind=engine)` crea una fábrica de sesiones.
- `session.add(obj)` marca el objeto para insertar.
- `session.commit()` confirma.
- `usuario.id` ya tiene el id generado tras el commit.

### 7.4. Consultas con `select()`

```python
from sqlalchemy import select

with Session() as session:
    stmt = select(Usuario).where(Usuario.nombre == "Ana")
    usuario = session.execute(stmt).scalar_one_or_none()
    print(usuario)

    todos = session.execute(select(Usuario)).scalars().all()
    print(todos)
```

**Explicación línea por línea:**
- `select(Usuario)` consulta todos los usuarios.
- `.where(...)` filtra.
- `.scalar_one_or_none()` devuelve uno o `None`.
- `.scalars().all()` devuelve todos.

### 7.5. Relaciones y eager loading

```python
from sqlalchemy.orm import selectinload

stmt = select(Usuario).options(selectinload(Usuario.pedidos))
usuarios = session.execute(stmt).scalars().all()
```

**Explicación línea por línea:**
- `selectinload` carga los pedidos en una consulta adicional, evitando el N+1.

### 7.6. Hybrid properties

```python
from sqlalchemy.ext.hybrid import hybrid_property


class Usuario(Base):
    ...
    @hybrid_property
    def nombre_mayus(self) -> str:
        return self.nombre.upper()
```

**Explicación línea por línea:**
- `@hybrid_property` permite usar la propiedad tanto en Python (`usuario.nombre_mayus`) como en consultas SQL.

### 7.7. Eventos y listeners

```python
from sqlalchemy import event


@event.listens_for(Usuario.nombre, "set")
def normalizar(target, value, oldvalue, initiator):
    return value.strip()
```

**Explicación línea por línea:**
- Se dispara cada vez que se asigna `usuario.nombre`, normalizando el valor.

### 7.8. Herencia y polimorfismo

```python
class Persona(Base):
    __tablename__ = "personas"
    id: Mapped[int] = mapped_column(primary_key=True)
    tipo: Mapped[str] = mapped_column(String(20))

    __mapper_args__ = {"polymorphic_identity": "persona", "polymorphic_on": tipo}


class Empleado(Persona):
    __mapper_args__ = {"polymorphic_identity": "empleado"}
    salario: Mapped[float] = mapped_column(default=0)
```

**Explicación línea por línea:**
- `polymorphic_on=tipo` indica la columna que distingue los tipos.
- `polymorphic_identity` identifica cada clase.

### 7.9. Errores comunes

**Error 1 — Sesiones de larga vida** que acumulan estado.

**Error 2 — Problema N+1** por lazy loading. Usa `selectinload`.

**Error 3 — Olvidar `back_populates`** en ambos lados.

### 7.10. Checkpoint de comprensión

1. ¿Qué es la "unidad de trabajo" de la Session?
2. ¿Cómo evitas el N+1?
3. ¿Qué hace `hybrid_property`?

### 7.11. Ejercicio propuesto

**Ejercicio 7.1.** Modela `Autor` ↔ `Libro` (1-N) y consulta los libros de un autor.

**Pista:** usa `relationship` y `selectinload`.

---

## 8. Alembic para migraciones

### 8.1. Versionar el esquema

**Alembic** versiona el esquema del ORM. Genera scripts de migración a partir de los cambios en los modelos.

### 8.2. Configuración

```bash
pip install alembic
alembic init migrations
```

**`migrations/env.py`:**

```python
from myapp.models import Base
target_metadata = Base.metadata
```

**`alembic.ini`:**

```ini
sqlalchemy.url = sqlite:///ejemplo.db
```

**Explicación línea por línea:**
- `env.py` conecta Alembic con tus modelos.
- `target_metadata` le dice qué esquema comparar.
- `alembic.ini` define la URL de conexión.

### 8.3. Uso

```bash
alembic revision --autogenerate -m "crear usuarios"
alembic upgrade head
alembic downgrade -1
alembic history
```

**Explicación línea por línea:**
- `revision --autogenerate` compara modelos y base y crea el script.
- `upgrade head` aplica todas las migraciones pendientes.
- `downgrade -1` revierte una.
- `history` muestra el historial.

### 8.4. Errores comunes

**Error 1 — No importar todos los modelos** en `env.py` → migraciones incompletas.

**Error 2 — Editar la BD a mano.** Todo por migraciones.

### 8.5. Checkpoint de comprensión

1. ¿Qué hace `autogenerate`?
2. ¿Por qué importar los modelos en `env.py`?

### 8.6. Ejercicio propuesto

**Ejercicio 8.1.** Añade una columna `telefono` a `Usuario`, genera y aplica la migración.

**Pista:** revisa el script antes de `upgrade`.

---

## 9. Transacciones y aislamiento

### 9.1. Qué garantiza una transacción

Una **transacción** agrupa operaciones atómicas. El **nivel de aislamiento** controla qué ven las transacciones concurrentes:

| Nivel | Fenómeno evitado |
|---|---|
| READ COMMITTED | Lecturas sucias |
| REPEATABLE READ | Lecturas no repetibles |
| SERIALIZABLE | Lecturas fantasma |

### 9.2. Uso

```python
from sqlalchemy import select

with Session() as session:
    with session.begin():
        usuario = session.get(Usuario, 1)
        usuario.nombre = "Nuevo"
    # commit automático al salir del bloque
```

**Explicación línea por línea:**
- `session.begin()` abre una transacción; se confirma al salir o revierte si hay error.

### 9.3. SAVEPOINT (transacción anidada)

```python
with Session() as session:
    session.add(Usuario(nombre="A", email="a@x.com"))
    with session.begin_nested():   # SAVEPOINT
        session.add(Usuario(nombre="B", email="a@x.com"))   # falla unique
    session.commit()
```

**Explicación línea por línea:**
- `begin_nested()` crea un **punto de guardado**: si falla, solo revierte ese bloque.

### 9.4. Errores comunes

**Error 1 — Transacciones largas** que bloquean filas.

**Error 2 — No manejar `IntegrityError`** y dejar la sesión inconsistente.

### 9.5. Checkpoint de comprensión

1. ¿Qué es un SAVEPOINT?
2. ¿Qué nivel de aislamiento evita las lecturas fantasma?

### 9.6. Ejercicio propuesto

**Ejercicio 9.1.** Transfiere saldo entre dos cuentas en una transacción con rollback.

**Pista:** usa `try/except` y `session.rollback()`.

---

## 10. Connection pooling

### 10.1. Por qué un pool

Abrir una conexión a la base es **caro** (handshake de red, autenticación). El **pool** reutiliza conexiones en lugar de crear una por operación.

### 10.2. Configuración

```python
engine = create_engine(
    "postgresql+psycopg://user:pass@localhost/app",
    pool_size=10,          # conexiones permanentes
    max_overflow=20,       # conexiones extra temporales
    pool_timeout=30,       # segundos de espera
    pool_recycle=1800,     # recicla cada 30 min
    pool_pre_ping=True,    # verifica antes de usar
)
```

**Explicación línea por línea:**
- `pool_size` es el número de conexiones mantenidas.
- `max_overflow` permite conexiones adicionales temporales.
- `pool_recycle` evita usar conexiones cerradas por el servidor.
- `pool_pre_ping` comprueba que la conexión siga viva.

### 10.3. Errores comunes

**Error 1 — Pool demasiado pequeño** para la carga.

**Error 2 — No usar `pool_pre_ping`** con bases que cierran conexiones inactivas.

### 10.4. Checkpoint de comprensión

1. ¿Qué problema resuelve el pool?
2. ¿Qué hace `pool_recycle`?

### 10.5. Ejercicio propuesto

**Ejercicio 10.1.** Ajusta el pool para una API con 100 usuarios concurrentes.

**Pista:** considera `pool_size` y `max_overflow`.

---

## 11. Optimización y profiling

### 11.1. Encontrar consultas lentas

Puedes escuchar los eventos de SQLAlchemy para medir consultas:

```python
import time
from sqlalchemy import event


@event.listens_for(engine, "before_cursor_execute")
def antes(conn, cursor, statement, parameters, context, executemany):
    conn.info.setdefault("inicio", []).append(time.perf_counter())


@event.listens_for(engine, "after_cursor_execute")
def despues(conn, cursor, statement, parameters, context, executemany):
    total = time.perf_counter() - conn.info["inicio"].pop()
    if total > 0.5:
        print(f"CONSULTA LENTA ({total:.2f}s): {statement[:100]}")
```

**Explicación línea por línea:**
- `before_cursor_execute` guarda el tiempo de inicio.
- `after_cursor_execute` calcula la duración y avisa si supera 0.5 s.

### 11.2. Eager loading

```python
from sqlalchemy.orm import joinedload, selectinload

select(Usuario).options(joinedload(Usuario.pedidos))
```

### 11.3. Errores comunes

**Error 1 — Cargar más datos de los necesarios.**

**Error 2 — Ignorar índices** en columnas de filtro.

### 11.4. Checkpoint de comprensión

1. ¿Cómo detectas consultas lentas?
2. ¿Qué diferencia hay entre `joinedload` y `selectinload`?

### 11.5. Ejercicio propuesto

**Ejercicio 11.1.** Registra consultas que tarden más de 100 ms.

**Pista:** usa los eventos `before/after_cursor_execute`.

---

## 12. SQLAlchemy 2.0: novedades

### 12.1. Qué cambió

SQLAlchemy 2.0 unifica estilos:

- **`select()`** como forma principal (no `Query`).
- **`Mapped[...]` / `mapped_column`** tipados.
- **`DeclarativeBase`** en lugar de `declarative_base()`.
- **Async** soportado de forma nativa.

### 12.2. Estilo 1.x vs 2.0

```python
# Estilo antiguo (1.x)
session.query(Usuario).filter(Usuario.nombre == "Ana").first()

# Estilo moderno (2.0)
session.execute(select(Usuario).where(Usuario.nombre == "Ana")).scalar_one_or_none()
```

**Explicación línea por línea:**
- El estilo 2.0 es más explícito y consistente con Core.

### 12.3. Async

```python
from sqlalchemy.ext.asyncio import async_sessionmaker, create_async_engine

engine = create_async_engine("postgresql+asyncpg://user:pass@localhost/app")
AsyncSession = async_sessionmaker(engine, expire_on_commit=False)


async def obtener_usuarios():
    async with AsyncSession() as session:
        resultado = await session.execute(select(Usuario))
        return resultado.scalars().all()
```

**Explicación línea por línea:**
- `create_async_engine` requiere un driver async (`asyncpg`, `aiosqlite`).
- `await session.execute(...)` ejecuta sin bloquear.

### 12.4. Errores comunes

**Error 1 — Mezclar `Query` y `select()`** en el mismo código.

**Error 2 — Usar async sin driver async.**

### 12.5. Checkpoint de comprensión

1. ¿Qué reemplaza a `Query` en 2.0?
2. ¿Qué driver necesitas para async con PostgreSQL?

### 12.6. Ejercicio propuesto

**Ejercicio 12.1.** Convierte una consulta 1.x a estilo 2.0.

**Pista:** reemplaza `query().filter()` por `select().where()`.

---

## 13. Integración con Pandas

### 13.1. Consultas a DataFrames

`pandas.read_sql` ejecuta una consulta y devuelve un `DataFrame`, ideal para análisis:

```python
import pandas as pd
from sqlalchemy import create_engine

engine = create_engine("sqlite:///ejemplo.db")

df = pd.read_sql("SELECT * FROM usuarios", engine)
print(df.head())

# Escribir un DataFrame a la BD
df.to_sql("usuarios_copia", engine, if_exists="replace", index=False)
```

**Explicación línea por línea:**
- `read_sql` devuelve un DataFrame.
- `to_sql` guarda un DataFrame como tabla.

### 13.2. Con parámetros

```python
from sqlalchemy import text

df = pd.read_sql(text("SELECT * FROM usuarios WHERE id > :id"), engine, params={"id": 5})
```

### 13.3. Errores comunes

**Error 1 — Cargar tablas enormes en memoria.** Filtra en SQL.

**Error 2 — `to_sql` sin índice** cuando la tabla lo requiere.

### 13.4. Checkpoint de comprensión

1. ¿Qué devuelve `read_sql`?
2. ¿Por qué filtrar en SQL en lugar de en Pandas?

### 13.5. Ejercicio propuesto

**Ejercicio 13.1.** Exporta una consulta agregada a un DataFrame y guarda el resultado en CSV.

**Pista:** `df.to_csv("resumen.csv")`.

---

## 14. Testing de modelos SQLAlchemy

### 14.1. Base en memoria

Los tests usan una base **en memoria** y crean el esquema con `Base.metadata.create_all`.

### 14.2. Ejemplo con pytest

```python
import pytest
from sqlalchemy import create_engine, select
from sqlalchemy.orm import sessionmaker
from myapp.models import Base, Usuario


@pytest.fixture
def session():
    engine = create_engine("sqlite:///:memory:")
    Base.metadata.create_all(engine)
    Session = sessionmaker(bind=engine)
    with Session() as s:
        yield s


def test_crear_usuario(session):
    usuario = Usuario(nombre="Ana", email="ana@example.com")
    session.add(usuario)
    session.commit()
    resultado = session.execute(select(Usuario)).scalars().all()
    assert len(resultado) == 1
```

**Explicación línea por línea:**
- El fixture crea una base nueva por test.
- `yield s` entrega la sesión y la cierra al terminar.

### 14.3. Errores comunes

**Error 1 — Compartir el engine entre tests** sin limpiar.

**Error 2 — No usar `create_all`** y fallar por tablas inexistentes.

### 14.4. Checkpoint de comprensión

1. ¿Por qué usar una base en memoria?
2. ¿Qué hace el fixture?

### 14.5. Ejercicio propuesto

**Ejercicio 14.1.** Testea que el email es único capturando `IntegrityError`.

**Pista:** importa `from sqlalchemy.exc import IntegrityError`.

---

## 15. Buenas prácticas

1. **Un `Engine` por aplicación.**
2. **Sesiones cortas** y cerradas con `with`.
3. **Parámetros siempre** (nunca concatenar SQL).
4. **`select()` moderno** (2.0).
5. **Eager loading** para evitar N+1.
6. **Migraciones con Alembic.**
7. **Pool configurado** según carga.
8. **`echo=False` en producción.**
9. **Tests con BD en memoria.**
10. **Mide antes de optimizar.**

---

## 16. Recursos

- **Documentación oficial:** <https://docs.sqlalchemy.org/>
- **Tutorial ORM:** <https://docs.sqlalchemy.org/en/20/tutorial/>
- **Alembic:** <https://alembic.sqlalchemy.org/>
- **Libro:** *SQLAlchemy 2 In Practice*, Miguel Grinberg.
- **Migración a 2.0:** <https://docs.sqlalchemy.org/en/20/changelog/migration_20.html>

---

## 17. Mini resumen final

- SQLAlchemy tiene dos capas: **Core** (SQL) y **ORM** (objetos).
- El **Engine** gestiona el pool; la **Session** es la unidad de trabajo.
- Los **dialectos** permiten múltiples motores.
- Core construye SQL con `Table`, `select`, `insert`, `update`, `delete`.
- El ORM usa `DeclarativeBase`, `Mapped`, `relationship` y `select()`.
- **Alembic** versiona el esquema.
- Las **transacciones** garantizan atomicidad; el **pool** optimiza conexiones.
- SQLAlchemy 2.0 aporta tipado, `select()` y async.

---

### 🎯 Retos opcionales

1. **Reto 1:** Construye un CRUD completo en Core y luego en ORM.
2. **Reto 2:** Añade herencia polimórfica (Empleado/Cliente).
3. **Reto 3:** Migra un proyecto de SQLAlchemy 1.x a 2.0.

**Anterior:** [13_pyramid.md](13_pyramid.md) · **Siguiente (Parte IV):** [15_selenium.md](15_selenium.md)
