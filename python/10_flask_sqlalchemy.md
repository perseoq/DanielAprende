# 10 · Flask-SQLAlchemy

**Versión recomendada:** Flask-SQLAlchemy 3.1 · SQLAlchemy 2.0 · Python 3.12
**Requisito:** [09_flask.md](09_flask.md)
**Nivel:** Intermedio
**Duración estimada de estudio:** 8–12 horas

---

## Índice de contenidos

1. [Introducción a Flask-SQLAlchemy](#1-introducción-a-flask-sqlalchemy)
2. [Configuración de la base de datos](#2-configuración-de-la-base-de-datos)
3. [Definición de modelos](#3-definición-de-modelos)
4. [Tipos de campos y relaciones](#4-tipos-de-campos-y-relaciones)
5. [Relaciones uno a uno, uno a muchos, muchos a muchos](#5-relaciones-uno-a-uno-uno-a-muchos-muchos-a-muchos)
6. [Consultas básicas y avanzadas](#6-consultas-básicas-y-avanzadas)
7. [Migraciones con Flask-Migrate y Alembic](#7-migraciones-con-flask-migrate-y-alembic)
8. [Transacciones y sesiones](#8-transacciones-y-sesiones)
9. [Eventos y listeners](#9-eventos-y-listeners)
10. [Optimización de consultas](#10-optimización-de-consultas)
11. [Testing de modelos](#11-testing-de-modelos)
12. [Integración con PostgreSQL, MySQL y SQLite](#12-integración-con-postgresql-mysql-y-sqlite)
13. [Buenas prácticas](#13-buenas-prácticas)
14. [Recursos](#14-recursos)
15. [Mini resumen final](#15-mini-resumen-final)

---

## 1. Introducción a Flask-SQLAlchemy

### 1.1. ¿Qué es un ORM y por qué usarlo?

Sin ORM, para guardar datos en una base de datos escribes **SQL** a mano:

```sql
SELECT id, nombre FROM usuarios WHERE edad > 18 ORDER BY nombre;
```

Y luego traduces cada fila a objetos Python manualmente. Esto tiene problemas: SQL como texto no lo verifica el editor, es propenso a errores y a inyecciones, y no aprovecha el tipado de Python.

Un **ORM** (*Object-Relational Mapping*) mapea **clases** a **tablas** y **objetos** a **filas**. Tú trabajas con objetos Python y el ORM genera el SQL por ti.

**Analogía:** un ORM es como un **traductor automático**. Tú hablas Python (objetos); el ORM traduce a SQL (tablas) y viceversa, para que no tengas que cambiar de idioma.

**Flask-SQLAlchemy** integra **SQLAlchemy** (el ORM más potente de Python) con Flask. Aporta:

- Un objeto `db` que gestiona el motor y la sesión por petición.
- Una clase base declarativa (`db.Model`).
- Métodos de consulta cómodos.

### 1.2. Instalación

```bash
pip install flask-sqlalchemy
```

### 1.3. Configuración mínima

```python
from flask import Flask
from flask_sqlalchemy import SQLAlchemy

app = Flask(__name__)
app.config["SQLALCHEMY_DATABASE_URI"] = "sqlite:///app.db"
app.config["SQLALCHEMY_TRACK_MODIFICATIONS"] = False

db = SQLAlchemy(app)
```

**Explicación línea por línea:**
- `SQLALCHEMY_DATABASE_URI` indica a qué base conectarse. `sqlite:///app.db` es un archivo local (ideal para aprender).
- `SQLALCHEMY_TRACK_MODIFICATIONS = False` desactiva una función de seguimiento que consume recursos y ya no es necesaria.
- `db = SQLAlchemy(app)` crea el objeto que gestiona todo.

### 1.4. Errores comunes

**Error 1 — Crear varias instancias de `SQLAlchemy`.**

Debe haber **una sola** instancia `db` en toda la aplicación. *Solución:* créala una vez y usa `init_app` (sección 2).

**Error 2 — Usar `db` fuera del contexto de la aplicación.**

```text
RuntimeError: Working outside of application context.
```

*Solución:* usa `with app.app_context():` o accede dentro de una petición.

### 1.5. Checkpoint de comprensión

1. ¿Qué problema resuelve un ORM?
2. ¿Qué es el objeto `db`?
3. ¿Por qué `SQLALCHEMY_TRACK_MODIFICATIONS = False`?

### 1.6. Ejercicio propuesto

**Ejercicio 1.1.** Configura Flask-SQLAlchemy con SQLite en memoria (`sqlite:///:memory:`).

**Pista:** ideal para pruebas, porque no toca el disco.

---

## 2. Configuración de la base de datos

### 2.1. La URI de conexión

La URI sigue el formato `dialecto+driver://usuario:contraseña@host:puerto/base`:

| Base de datos | URI de ejemplo |
|---|---|
| SQLite | `sqlite:///app.db` |
| PostgreSQL | `postgresql+psycopg://user:pass@localhost/db` |
| MySQL | `mysql+pymysql://user:pass@localhost/db` |

### 2.2. El patrón de fábrica

```python
from flask import Flask
from flask_sqlalchemy import SQLAlchemy

db = SQLAlchemy()


def create_app() -> Flask:
    """Crea la aplicación y vincula la base de datos."""
    app = Flask(__name__)
    app.config["SQLALCHEMY_DATABASE_URI"] = "sqlite:///app.db"
    app.config["SQLALCHEMY_TRACK_MODIFICATIONS"] = False
    db.init_app(app)

    with app.app_context():
        db.create_all()

    return app
```

**Explicación línea por línea:**
- `db = SQLAlchemy()` se crea **sin** app (para poder importarlo desde los modelos).
- `db.init_app(app)` lo **vincula** a la aplicación.
- `with app.app_context():` abre el contexto de la aplicación, necesario para operar con `db` fuera de una petición.
- `db.create_all()` crea las tablas que falten.

**¿Por qué este patrón?** Porque los modelos necesitan importar `db`, y si `db` dependiera de `app`, tendrías imports circulares. Con `init_app` se rompe ese círculo.

### 2.3. Crear las tablas

```bash
flask --app app shell
>>> from app import db
>>> db.create_all()
```

### 2.4. Errores comunes

**Error 1 — Olvidar `app_context()`.**

*Síntoma:* `RuntimeError: Working outside of application context`. *Solución:* envuelve el código en `with app.app_context():`.

**Error 2 — Usar rutas relativas de SQLite** y confundir el directorio de trabajo.

### 2.5. Checkpoint de comprensión

1. ¿Por qué se crea `db` sin app y se vincula con `init_app`?
2. ¿Para qué sirve `app_context()`?
3. ¿Qué hace `db.create_all()`?

### 2.6. Ejercicio propuesto

**Ejercicio 2.1.** Configura la URI desde una variable de entorno `DATABASE_URL`.

**Pista:** `os.environ.get("DATABASE_URL", "sqlite:///app.db")`.

---

## 3. Definición de modelos

### 3.1. Qué es un modelo

Un **modelo** es una **clase** que representa una **tabla**. Cada **atributo** de la clase es una **columna**. Cada **instancia** es una **fila**.

### 3.2. Tu primer modelo

```python
from datetime import datetime
from flask_sqlalchemy import SQLAlchemy

db = SQLAlchemy()


class Usuario(db.Model):
    """Usuario registrado."""

    __tablename__ = "usuarios"

    id = db.Column(db.Integer, primary_key=True)
    nombre = db.Column(db.String(80), nullable=False)
    email = db.Column(db.String(120), unique=True, nullable=False, index=True)
    creado_en = db.Column(db.DateTime, default=datetime.utcnow)

    def __repr__(self) -> str:
        return f"<Usuario {self.nombre}>"
```

**Explicación línea por línea:**
- `class Usuario(db.Model):` hereda de `db.Model`, lo que la convierte en un modelo.
- `__tablename__ = "usuarios"` fija el nombre de la tabla (si no, se deduce del nombre de la clase).
- `db.Column(db.Integer, primary_key=True)` define la clave primaria.
- `nullable=False` significa que no puede ser nulo (obligatorio).
- `unique=True` garantiza que no se repita.
- `index=True` crea un índice para búsquedas rápidas.
- `default=datetime.utcnow` asigna la fecha actual por defecto.
- `__repr__` da una representación legible para depurar.

### 3.3. Operaciones CRUD

**Create (crear):**

```python
with app.app_context():
    usuario = Usuario(nombre="Ana", email="ana@example.com")
    db.session.add(usuario)
    db.session.commit()
```

**Read (leer):**

```python
with app.app_context():
    encontrado = db.session.get(Usuario, 1)
    print(encontrado.nombre)
```

**Update (actualizar):**

```python
with app.app_context():
    usuario = db.session.get(Usuario, 1)
    usuario.nombre = "Ana María"
    db.session.commit()
```

**Delete (eliminar):**

```python
with app.app_context():
    usuario = db.session.get(Usuario, 1)
    db.session.delete(usuario)
    db.session.commit()
```

**Explicación línea por línea:**
- `db.session.add(obj)` marca el objeto para insertarlo.
- `db.session.commit()` **confirma** los cambios (los escribe en la base). **Sin commit, nada persiste.**
- `db.session.get(Modelo, id)` busca por clave primaria.
- Al modificar un atributo de un objeto cargado, SQLAlchemy lo detecta y lo actualiza al hacer commit.

### 3.4. Errores comunes

**Error 1 — Olvidar `db.session.commit()`.**

*Síntoma:* los cambios desaparecen al cerrar. *Solución:* haz commit siempre.

**Error 2 — Nombre de tabla duplicado.**

*Solución:* usa `__tablename__` explícito.

**Error 3 — No definir `__repr__`.**

El admin y la consola muestran `<Usuario 0x...>`.

### 3.5. Checkpoint de comprensión

1. ¿Cómo se mapea una clase a una tabla?
2. ¿Qué hace `db.session.commit()`?
3. ¿Qué significa `nullable=False`?

### 3.6. Ejercicio propuesto

**Ejercicio 3.1.** Crea un modelo `Tarea` con `titulo`, `descripcion` y `hecha`.

**Pista:** `db.Boolean` con `default=False`.

---

## 4. Tipos de campos y relaciones

### 4.1. Los tipos de columna

| Tipo | Uso |
|---|---|
| `db.Integer` | Enteros |
| `db.String(n)` | Texto de longitud n |
| `db.Text` | Texto largo |
| `db.Float` / `db.Numeric` | Decimales |
| `db.Boolean` | Verdadero/Falso |
| `db.DateTime` / `db.Date` | Fechas |
| `db.JSON` | JSON nativo |

### 4.2. Restricciones comunes

```python
precio = db.Column(db.Numeric(10, 2), nullable=False, default=0)
estado = db.Column(db.String(20), default="pendiente")
creado = db.Column(db.DateTime, server_default=db.func.now())
```

**Explicación línea por línea:**
- `db.Numeric(10, 2)` es un decimal con 10 dígitos totales y 2 decimales: **ideal para dinero** (evita errores de redondeo de `Float`).
- `default="pendiente"` es un valor por defecto a nivel de Python.
- `server_default=db.func.now()` es un valor por defecto calculado por la base de datos.

### 4.3. Claves foráneas

```python
class Comentario(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    contenido = db.Column(db.Text)
    usuario_id = db.Column(db.Integer, db.ForeignKey("usuarios.id"))
```

**Explicación línea por línea:**
- `db.ForeignKey("usuarios.id")` crea una **clave foránea** que referencia la columna `id` de la tabla `usuarios`. Garantiza la integridad referencial.

### 4.4. Errores comunes

**Error 1 — Usar `db.String` sin longitud** en bases como MySQL.

**Error 2 — Guardar dinero en `Float`.**

*Solución:* usa `Numeric` para evitar errores de redondeo.

**Error 3 — Olvidar `nullable` y permitir nulos sin querer.**

### 4.5. Checkpoint de comprensión

1. ¿Por qué `Numeric` y no `Float` para dinero?
2. ¿Qué hace una `ForeignKey`?
3. ¿Qué diferencia hay entre `default` y `server_default`?

### 4.6. Ejercicio propuesto

**Ejercicio 4.1.** Añade un campo `prioridad` (`Integer`) y un `creado_en` a `Tarea`.

**Pista:** usa `server_default=db.func.now()`.

---

## 5. Relaciones uno a uno, uno a muchos, muchos a muchos

### 5.1. Los tres tipos de relación

- **Uno a muchos:** un autor tiene muchos libros.
- **Uno a uno:** un usuario tiene un perfil.
- **Muchos a muchos:** un libro tiene muchas etiquetas y viceversa.

`db.ForeignKey` define la restricción en la base; `db.relationship` permite **navegar** de un objeto a otro en Python.

### 5.2. Uno a muchos

```python
class Autor(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    nombre = db.Column(db.String(80))
    libros = db.relationship("Libro", back_populates="autor", cascade="all, delete")


class Libro(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    titulo = db.Column(db.String(120))
    autor_id = db.Column(db.Integer, db.ForeignKey("autor.id"))
    autor = db.relationship("Autor", back_populates="libros")
```

**Explicación línea por línea:**
- `libros = db.relationship(...)` permite acceder a `autor.libros` (una lista).
- `back_populates="autor"` enlaza ambas direcciones: `libro.autor` y `autor.libros`.
- `cascade="all, delete"` hace que al borrar un autor se borren sus libros.

**Uso:**

```python
autor = Autor(nombre="Borges", libros=[Libro(titulo="Ficciones")])
db.session.add(autor)
db.session.commit()
print(autor.libros[0].titulo)
```

Salida esperada:

```text
Ficciones
```

### 5.3. Uno a uno

```python
class Usuario(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    perfil = db.relationship("Perfil", back_populates="usuario", uselist=False)


class Perfil(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    usuario_id = db.Column(db.Integer, db.ForeignKey("usuario.id"), unique=True)
    usuario = db.relationship("Usuario", back_populates="perfil")
```

**Explicación línea por línea:**
- `uselist=False` convierte la relación en **uno a uno** (devuelve un solo objeto, no una lista).
- `unique=True` en la clave foránea garantiza que un usuario tenga un solo perfil.

### 5.4. Muchos a muchos

Se necesita una **tabla de asociación**:

```python
etiquetas_libros = db.Table(
    "etiquetas_libros",
    db.Column("libro_id", db.Integer, db.ForeignKey("libro.id"), primary_key=True),
    db.Column("etiqueta_id", db.Integer, db.ForeignKey("etiqueta.id"), primary_key=True),
)


class Etiqueta(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    nombre = db.Column(db.String(40))
    libros = db.relationship("Libro", secondary=etiquetas_libros, back_populates="etiquetas")


class Libro(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    titulo = db.Column(db.String(120))
    etiquetas = db.relationship("Etiqueta", secondary=etiquetas_libros, back_populates="libros")
```

**Explicación línea por línea:**
- `db.Table(...)` define la tabla intermedia con dos claves foráneas.
- `secondary=etiquetas_libros` indica a `relationship` que use esa tabla intermedia.

### 5.5. Errores comunes

**Error 1 — Olvidar `back_populates`** o escribirlo mal.

```text
InvalidRequestError: Mapper ... has no property ...
```

**Error 2 — No configurar `cascade`** y dejar registros huérfanos.

**Error 3 — Confundir el lado de la clave foránea.** La `ForeignKey` va en el lado "muchos".

### 5.6. Checkpoint de comprensión

1. ¿Dónde va la `ForeignKey` en una relación uno a muchos?
2. ¿Qué hace `uselist=False`?
3. ¿Por qué muchos a muchos necesita una tabla intermedia?

### 5.7. Ejercicio propuesto

**Ejercicio 5.1.** Modela `Curso` ↔ `Estudiante` como muchos a muchos.

**Pista:** usa una tabla de asociación con `db.Table`.

---

## 6. Consultas básicas y avanzadas

### 6.1. El estilo moderno: `select()`

SQLAlchemy 2.0 recomienda `db.session.execute(db.select(...))` en lugar del antiguo `Model.query`.

```python
from sqlalchemy import select

# Todos los usuarios
usuarios = db.session.execute(select(Usuario)).scalars().all()

# Filtrar
ana = db.session.execute(
    select(Usuario).where(Usuario.nombre == "Ana")
).scalar_one_or_none()

# Por clave primaria
u = db.session.get(Usuario, 1)
```

**Explicación línea por línea:**
- `select(Usuario)` construye una consulta.
- `.where(...)` filtra.
- `.scalars()` extrae los objetos de las filas.
- `.all()` devuelve una lista; `.scalar_one_or_none()` devuelve uno o `None`.

### 6.2. Filtros y orden

```python
stmt = (
    select(Usuario)
    .where(Usuario.nombre.like("A%"))
    .order_by(Usuario.nombre.desc())
    .limit(10)
)
resultados = db.session.execute(stmt).scalars().all()
```

**Explicación línea por línea:**
- `.like("A%")` filtra nombres que empiezan por "A".
- `.order_by(...desc())` ordena descendente.
- `.limit(10)` limita a 10 resultados.

### 6.3. Agregaciones

```python
from sqlalchemy import func, select

total = db.session.execute(select(func.count(Usuario.id))).scalar()
print(total)
```

**Explicación línea por línea:**
- `func.count(Usuario.id)` cuenta filas. `func` da acceso a las funciones SQL.

### 6.4. Cargar relaciones: `selectinload`

El **problema N+1**: si iteras autores y accedes a `autor.libros`, SQLAlchemy lanza **una consulta por cada autor**. Con 100 autores, 101 consultas. `selectinload` lo resuelve con una sola consulta adicional:

```python
from sqlalchemy.orm import selectinload

autores = db.session.execute(
    select(Autor).options(selectinload(Autor.libros))
).scalars().all()
```

### 6.5. Errores comunes

**Error 1 — Problema N+1.**

*Síntoma:* la app va lenta con muchos registros. *Solución:* usa `selectinload`/`joinedload`.

**Error 2 — Consultar dentro de un bucle.**

*Solución:* haz una sola consulta con un filtro `in_`.

**Error 3 — Usar `Model.query` (estilo antiguo).**

*Solución:* usa `select()` (estilo 2.0).

### 6.6. Checkpoint de comprensión

1. ¿Qué es el problema N+1 y cómo se resuelve?
2. ¿Qué diferencia hay entre `scalar_one_or_none` y `all`?
3. ¿Cómo cuentas filas?

### 6.7. Ejercicio propuesto

**Ejercicio 6.1.** Obtén todos los autores con más de 2 libros usando `group_by` y `having`.

**Pista:** `func.count(Libro.id) > 2`.

---

## 7. Migraciones con Flask-Migrate y Alembic

### 7.1. Por qué migraciones

Cuando cambias un modelo (añades una columna, por ejemplo), la base de datos **no** cambia sola. Las **migraciones** son scripts versionados que actualizan el esquema **sin perder datos**.

**Analogía:** las migraciones son como un historial de cambios de la base de datos. Puedes avanzar (`upgrade`) o retroceder (`downgrade`) como en un sistema de control de versiones.

### 7.2. Instalación y configuración

```bash
pip install flask-migrate
```

```python
from flask_migrate import Migrate

migrate = Migrate(app, db)
```

### 7.3. El flujo de trabajo

```bash
flask db init                      # crea la carpeta migrations/ (una vez)
flask db migrate -m "crear usuarios"  # genera el script de migración
flask db upgrade                   # aplica los cambios
flask db downgrade                 # revierte
```

**Explicación línea por línea:**
- `init` crea la infraestructura (solo la primera vez).
- `migrate` compara tus modelos con la base y genera un script.
- `upgrade` aplica el script a la base.
- `downgrade` revierte.

### 7.4. El flujo correcto

1. Modificas un modelo.
2. `flask db migrate -m "mensaje"`.
3. **Revisas** el script generado en `migrations/versions/`.
4. `flask db upgrade`.

**Revisar el script es crucial:** a veces Alembic no detecta ciertos cambios (renombrados) o genera algo peligroso.

### 7.5. Errores comunes

**Error 1 — Editar la base a mano.**

*Solución:* todo cambio pasa por migraciones.

**Error 2 — No versionar la carpeta `migrations/`.**

*Solución:* inclúyela en Git.

**Error 3 — Conflictos de migración** en equipos. Comunica los cambios.

### 7.6. Checkpoint de comprensión

1. ¿Para qué sirven las migraciones?
2. ¿Qué hace `flask db upgrade`?
3. ¿Por qué revisar el script generado?

### 7.7. Ejercicio propuesto

**Ejercicio 7.1.** Añade un campo a `Tarea`, genera una migración y aplícala.

**Pista:** revisa el archivo generado antes de `upgrade`.

---

## 8. Transacciones y sesiones

### 8.1. Qué es una transacción

Una **transacción** agrupa varias operaciones como una **unidad atómica**: o se aplican **todas**, o **ninguna**. Esto protege la integridad de los datos.

**Analogía:** una transferencia bancaria resta de una cuenta y suma a otra. Si el sistema fallara entre las dos operaciones, el dinero desaparecería. La transacción garantiza que ambas ocurran o ninguna.

### 8.2. Commit y rollback

`db.session` es una **unidad de trabajo**:

```python
try:
    usuario = Usuario(nombre="Ana", email="ana@example.com")
    db.session.add(usuario)
    db.session.flush()   # asigna el id sin confirmar
    perfil = Perfil(usuario_id=usuario.id)
    db.session.add(perfil)
    db.session.commit()  # confirma TODO
except Exception:
    db.session.rollback()  # deshace TODO
    raise
```

**Explicación línea por línea:**
- `flush()` envía el SQL pero **no confirma**; sirve para obtener el `id` generado.
- `commit()` confirma la transacción completa.
- `rollback()` revierte todos los cambios si algo falló.

### 8.3. El patrón de contexto

```python
with db.session.begin():
    db.session.add(Usuario(nombre="Luis", email="luis@example.com"))
```

**Explicación línea por línea:**
- `db.session.begin()` abre una transacción y la confirma al salir (o revierte si hay error).

### 8.4. Errores comunes

**Error 1 — Olvidar `rollback` tras una excepción.**

*Síntoma:* la sesión queda inconsistente y falla el siguiente commit. *Solución:* haz rollback en el `except`.

**Error 2 — Transacciones largas** que bloquean filas.

### 8.5. Checkpoint de comprensión

1. ¿Qué garantiza una transacción?
2. ¿Qué diferencia hay entre `flush` y `commit`?
3. ¿Cuándo se hace `rollback`?

### 8.6. Ejercicio propuesto

**Ejercicio 8.1.** Transfiere "saldo" entre dos cuentas en una sola transacción con rollback.

**Pista:** usa `try/except` con `commit`/`rollback`.

---

## 9. Eventos y listeners

### 9.1. Reaccionar a cambios

SQLAlchemy permite ejecutar código cuando ocurren eventos (antes/después de insertar, actualizar, etc.).

### 9.2. Normalizar al asignar

```python
from sqlalchemy import event


@event.listens_for(Usuario.email, "set")
def normalizar(target, value, oldvalue, initiator):
    """Convierte el email a minúsculas al asignarlo."""
    return value.lower()
```

**Explicación línea por línea:**
- `@event.listens_for(Usuario.email, "set")` se dispara cada vez que se asigna `usuario.email`.
- `return value.lower()` transforma el valor antes de guardarlo.

### 9.3. Reaccionar a la inserción

```python
@event.listens_for(Usuario, "after_insert")
def tras_insertar(mapper, connection, target):
    print(f"Usuario {target.nombre} insertado")
```

### 9.4. Errores comunes

**Error 1 — Lógica pesada en listeners.** Ralentiza cada operación.

**Error 2 — Efectos secundarios ocultos** difíciles de depurar.

### 9.5. Checkpoint de comprensión

1. ¿Para qué sirve un listener de eventos?
2. ¿Qué evento se dispara al asignar un atributo?

### 9.6. Ejercicio propuesto

**Ejercicio 9.1.** Normaliza el email a minúsculas al asignarlo con un listener.

**Pista:** usa `@event.listens_for(Usuario.email, "set")`.

---

## 10. Optimización de consultas

### 10.1. Por qué optimizar

A medida que crecen los datos, las consultas ineficientes degradan la aplicación. Optimizar reduce latencia y carga del servidor.

### 10.2. Técnicas

```python
from sqlalchemy import select
from sqlalchemy.orm import joinedload

# Eager loading para evitar N+1
autores = db.session.execute(
    select(Autor).options(joinedload(Autor.libros))
).scalars().unique().all()

# Cargar solo las columnas necesarias
stmt = select(Usuario.id, Usuario.nombre)
filas = db.session.execute(stmt).all()
```

**Explicación línea por línea:**
- `joinedload` trae la relación en la **misma** consulta (JOIN).
- `.unique()` elimina duplicados que produce el JOIN.
- Seleccionar solo columnas concretas ahorra memoria y ancho de banda.

### 10.3. Índices

```python
email = db.Column(db.String(120), index=True)
```

**Explicación línea por línea:**
- `index=True` crea un índice, acelerando las búsquedas por esa columna.

### 10.4. Errores comunes

**Error 1 — `SELECT *` innecesario.**

**Error 2 — Sin índices** en columnas de filtro frecuente.

### 10.5. Checkpoint de comprensión

1. ¿Qué hace un índice y cuándo lo añades?
2. ¿Cómo evitas el N+1?

### 10.6. Ejercicio propuesto

**Ejercicio 10.1.** Mide el tiempo de una consulta con y sin `joinedload`.

**Pista:** usa `time.perf_counter()`.

---

## 11. Testing de modelos

### 11.1. Aislar la base de datos

Los tests usan una base de datos **en memoria** y crean/eliminan el esquema en cada test, para no ensuciar datos reales.

### 11.2. Ejemplo con pytest

```python
import pytest
from app import create_app, db
from app.models import Usuario


@pytest.fixture
def app_ctx():
    app = create_app()
    app.config["SQLALCHEMY_DATABASE_URI"] = "sqlite:///:memory:"
    with app.app_context():
        db.create_all()
        yield app
        db.session.remove()
        db.drop_all()


def test_crear_usuario(app_ctx):
    usuario = Usuario(nombre="Ana", email="ana@example.com")
    db.session.add(usuario)
    db.session.commit()
    assert Usuario.query.count() == 1
```

**Explicación línea por línea:**
- El fixture crea la app con BD en memoria, crea las tablas y las destruye al terminar.
- Cada test parte de una base **limpia**.

### 11.3. Errores comunes

**Error 1 — Tests que comparten estado.**

*Solución:* crea y destruye el esquema por test.

**Error 2 — Probar contra la BD de desarrollo.**

### 11.4. Checkpoint de comprensión

1. ¿Por qué usar una BD en memoria para tests?
2. ¿Qué hace el fixture al terminar?

### 11.5. Ejercicio propuesto

**Ejercicio 11.1.** Escribe un test que verifique la restricción `unique` del email.

**Pista:** captura `IntegrityError`.

---

## 12. Integración con PostgreSQL, MySQL y SQLite

### 12.1. El mismo modelo, distintos motores

SQLAlchemy abstrae las diferencias: cambias la URI y el driver, y el resto funciona igual.

| Motor | Instalación | URI |
|---|---|---|
| SQLite | incluido | `sqlite:///app.db` |
| PostgreSQL | `pip install psycopg[binary]` | `postgresql+psycopg://...` |
| MySQL | `pip install pymysql` | `mysql+pymysql://...` |

### 12.2. Docker para desarrollo

```yaml
services:
  db:
    image: postgres:16
    environment:
      POSTGRES_PASSWORD: ejemplo
      POSTGRES_DB: app
    ports:
      - "5432:5432"
```

### 12.3. Errores comunes

**Error 1 — Diferencias de tipos** entre motores (por ejemplo, `String` sin longitud en MySQL).

**Error 2 — Suponer que SQLite soporta todo.** No maneja bien concurrencia ni tipos avanzados.

**Error 3 — SQLite para producción con alta concurrencia.** Usa PostgreSQL.

### 12.4. Checkpoint de comprensión

1. ¿Qué cambia al pasar de SQLite a PostgreSQL?
2. ¿Por qué SQLite no es ideal para producción con concurrencia?

### 12.5. Ejercicio propuesto

**Ejercicio 12.1.** Cambia la app de SQLite a PostgreSQL usando Docker.

**Pista:** ajusta la URI y las dependencias.

---

## 13. Buenas prácticas

1. **Una instancia `db`** por aplicación; `init_app` para vincular.
2. **Usa migraciones** desde el primer día.
3. **Evita el problema N+1** con eager loading.
4. **Usa `Numeric`** para dinero.
5. **Índices** en columnas de búsqueda frecuente.
6. **Transacciones cortas** y con `rollback` ante errores.
7. **Tests con BD en memoria.**
8. **Separa modelos en `models.py`.**
9. **Usa `select()`** (estilo 2.0).
10. **No dejes `SQLALCHEMY_TRACK_MODIFICATIONS` activo.**

---

## 14. Recursos

- **Flask-SQLAlchemy:** <https://flask-sqlalchemy.palletsprojects.com/>
- **SQLAlchemy:** <https://docs.sqlalchemy.org/>
- **Flask-Migrate:** <https://flask-migrate.readthedocs.io/>
- **Alembic:** <https://alembic.sqlalchemy.org/>
- **Libro:** *SQLAlchemy 2 In Practice*, Miguel Grinberg.

---

## 15. Mini resumen final

- **Flask-SQLAlchemy** integra el ORM con Flask mediante `db`.
- Los **modelos** heredan de `db.Model` y definen columnas.
- `db.relationship` y `db.ForeignKey` modelan relaciones 1-1, 1-N y N-M.
- Las **consultas** usan `db.session.execute(select(...))`.
- **Flask-Migrate/Alembic** versionan el esquema.
- Las **transacciones** se confirman con `commit` y revierten con `rollback`.
- Evita el **N+1** con eager loading.
- Los **tests** usan una BD en memoria.

---

### 🎯 Retos opcionales

1. **Reto 1:** Añade un modelo `Comentario` relacionado con `Usuario` y `Libro`.
2. **Reto 2:** Implementa paginación con `db.paginate`.
3. **Reto 3:** Añade una migración que cree un índice compuesto.

**Anterior:** [09_flask.md](09_flask.md) · **Siguiente:** [11_django_orm.md](11_django_orm.md)
