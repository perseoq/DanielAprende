# 13 · Pyramid

**Versión recomendada:** Pyramid 2.0 · Python 3.12
**Requisito:** [12_fastapi_orm.md](12_fastapi_orm.md)
**Nivel:** Intermedio → Avanzado
**Duración estimada de estudio:** 10–14 horas

---

## Índice de contenidos

1. [Introducción a Pyramid](#1-introducción-a-pyramid)
2. [Filosofía: "Start small, finish big"](#2-filosofía-start-small-finish-big)
3. [Instalación y estructura de proyecto](#3-instalación-y-estructura-de-proyecto)
4. [Configuración y desarrollo](#4-configuración-y-desarrollo)
5. [Rutas y vistas](#5-rutas-y-vistas)
6. [View configs y predicates](#6-view-configs-y-predicates)
7. [Templates](#7-templates-jinja2-chameleon-mako)
8. [Autenticación y autorización (ACL)](#8-autenticación-y-autorización-acl)
9. [Sesiones](#9-sesiones)
10. [Traversal vs URL dispatch](#10-traversal-vs-url-dispatch)
11. [Integración con SQLAlchemy](#11-integración-con-sqlalchemy)
12. [Integración con otros ORM](#12-integración-con-otros-orm)
13. [Testing](#13-testing)
14. [Despliegue](#14-despliegue)
15. [Comparativa con Flask y Django](#15-comparativa-con-flask-y-django)
16. [Buenas prácticas](#16-buenas-prácticas)
17. [Recursos](#17-recursos)
18. [Mini resumen final](#18-mini-resumen-final)

---

## 1. Introducción a Pyramid

### 1.1. ¿Qué es Pyramid?

**Pyramid** es un framework web **maduro y flexible**. A diferencia de Flask (minimalista) y Django (todo incluido), Pyramid se sitúa en un punto intermedio: no impone un ORM ni un motor de plantillas, pero ofrece herramientas potentes para aplicaciones que crecen.

Se distingue por:

- **No imponer decisiones:** eliges ORM, plantillas, etc.
- **Escalar sin reescribir:** empiezas pequeño y creces.
- **Dos formas de enrutar:** *URL dispatch* y *traversal*.
- **Seguridad declarativa** con ACL (*Access Control Lists*).

### 1.2. ¿Cuándo usarlo?

| Escenario | ¿Pyramid? |
|---|---|
| Proyecto que quiere elegir cada componente | Sí |
| Modelos de permisos complejos | Sí |
| Sistema de larga vida que debe evolucionar | Sí |
| Prototipo rápido y pequeño | Mejor Flask |
| App con admin automático | Mejor Django |

### 1.3. Aplicación mínima

```python
from wsgiref.simple_server import make_server
from pyramid.config import Configurator
from pyramid.response import Response


def hola(request):
    return Response("¡Hola, Pyramid!")


if __name__ == "__main__":
    with Configurator() as config:
        config.add_route("hola", "/")
        config.add_view(hola, route_name="hola")
        app = config.make_wsgi_app()
    server = make_server("0.0.0.0", 6543, app)
    server.serve_forever()
```

**Explicación línea por línea:**
- `Configurator()` es el objeto que **registra** todo (rutas, vistas...).
- `config.add_route("hola", "/")` registra una ruta con nombre.
- `config.add_view(hola, route_name="hola")` asocia la vista a la ruta.
- `config.make_wsgi_app()` construye la aplicación WSGI.
- `make_server` levanta un servidor de la biblioteca estándar (solo para desarrollo).

**Observa la diferencia:** en Flask, `@app.route` hace todo a la vez. En Pyramid, **registras la ruta y la vista por separado**. Esto es más explícito (recuerda el Zen) y más flexible.

### 1.4. Errores comunes

**Error 1 — Esperar convenciones como en Django.**

Pyramid es explícito: tú registras cada pieza.

**Error 2 — Usar el servidor de la stdlib en producción.**

*Solución:* usa Waitress o Gunicorn.

### 1.5. Checkpoint de comprensión

1. ¿En qué se diferencia Pyramid de Flask y Django?
2. ¿Qué hace `Configurator`?
3. ¿Por qué hay que registrar la ruta y la vista por separado?

### 1.6. Ejercicio propuesto

**Ejercicio 1.1.** Modifica la app mínima para devolver JSON.

**Pista:** usa `pyramid.response.Response(json=...)` o un renderer `json`.

---

## 2. Filosofía: "Start small, finish big"

### 2.1. Qué significa

El lema de Pyramid resume su enfoque: **empieza pequeño y termina grande**. Evita decisiones prematuras: no te obliga a tener un ORM, ni plantillas, ni una estructura concreta desde el día uno. Añades capas cuando las necesitas.

**Analogía:** construir una casa con un sistema modular. Puedes empezar con una habitación y añadir más sin demoler lo construido. En otros frameworks, quizá tengas que decidir todos los cimientos desde el principio.

### 2.2. Implicaciones

- **Flexibilidad:** eliges Jinja2, Chameleon o Mako; SQLAlchemy, Peewee o MongoDB.
- **Escalabilidad:** la misma aplicación crece.
- **Explícitud:** registras cada pieza, así que sabes exactamente qué pasa.

### 2.3. Errores comunes

**Error 1 — Abusar de la flexibilidad** y crear proyectos sin estructura.

*Solución:* define convenciones propias y sé consistente.

**Error 2 — Copiar configuraciones sin entenderlas.**

### 2.4. Checkpoint de comprensión

1. ¿Qué significa "start small, finish big"?
2. ¿Qué ventaja tiene no imponer un ORM?

### 2.5. Ejercicio propuesto

**Ejercicio 2.1.** Enumera qué componentes elegirías para una API JSON y por qué.

**Pista:** plantillas quizá no sean necesarias.

---

## 3. Instalación y estructura de proyecto

### 3.1. Instalación

```bash
python -m venv .venv
source .venv/bin/activate
pip install pyramid
```

Con plantillas oficiales (Cookiecutter):

```bash
pip install cookiecutter
cookiecutter gh:Pylons/pyramid-cookiecutter-starter
```

### 3.2. Estructura típica

```text
mi_proyecto/
├── development.ini
├── production.ini
├── setup.py
├── mi_proyecto/
│   ├── __init__.py      # main() / fábrica
│   ├── routes.py
│   ├── views.py
│   ├── models.py
│   ├── templates/
│   └── static/
└── tests/
```

### 3.3. El punto de entrada

**`mi_proyecto/__init__.py`:**

```python
from pyramid.config import Configurator


def main(global_config, **settings):
    """Fábrica WSGI de la aplicación."""
    with Configurator(settings=settings) as config:
        config.include("mi_proyecto.routes")
        config.scan("mi_proyecto.views")
        return config.make_wsgi_app()
```

**Explicación línea por línea:**
- `main` es la **fábrica WSGI**: recibe la configuración y devuelve la app.
- `config.include("mi_proyecto.routes")` incluye un módulo de configuración.
- `config.scan("mi_proyecto.views")` busca automáticamente los `@view_config` en ese paquete.
- `**settings` recibe la configuración del archivo `.ini`.

### 3.4. Errores comunes

**Error 1 — No usar la fábrica `main`.**

*Solución:* úsala; facilita el testing y el despliegue.

**Error 2 — Mezclar configuración en el código.** Usa `.ini`.

### 3.5. Checkpoint de comprensión

1. ¿Qué es la fábrica `main`?
2. ¿Qué hace `config.scan`?
3. ¿De dónde vienen los `settings`?

### 3.6. Ejercicio propuesto

**Ejercicio 3.1.** Crea un `routes.py` con `includeme(config)` y regístralo.

**Pista:** `config.include` espera una función `includeme`.

---

## 4. Configuración y desarrollo

### 4.1. Archivos `.ini`

Pyramid usa archivos `.ini` (formato PasteDeploy) para la configuración, con secciones `[app:main]` y `[server:main]`.

### 4.2. `development.ini`

```ini
[app:main]
use = egg:mi_proyecto

pyramid.reload_templates = true
sqlalchemy.url = sqlite:///mi_proyecto.db

[server:main]
use = egg:waitress#main
listen = localhost:6543

[loggers]
keys = root

[logger_root]
level = INFO
handlers = console
```

**Explicación línea por línea:**
- `[app:main]` configura la aplicación.
- `use = egg:mi_proyecto` indica qué fábrica usar.
- `pyramid.reload_templates = true` recarga plantillas al editarlas (desarrollo).
- `[server:main]` configura el servidor (Waitress).
- `listen` es la dirección y puerto.

### 4.3. Ejecutar

```bash
pip install waitress
pserve development.ini --reload
```

**Explicación línea por línea:**
- `pserve` arranca la app usando la configuración del `.ini`.
- `--reload` reinicia al detectar cambios (solo desarrollo).

### 4.4. Leer ajustes desde el código

```python
def mi_vista(request):
    url = request.registry.settings["sqlalchemy.url"]
    return Response(url)
```

**Explicación línea por línea:**
- `request.registry.settings` da acceso a la configuración del `.ini`.

### 4.5. Errores comunes

**Error 1 — Editar `production.ini` con datos de desarrollo.**

**Error 2 — Usar `--reload` en producción.**

### 4.6. Checkpoint de comprensión

1. ¿Para qué sirven los archivos `.ini`?
2. ¿Qué hace `pserve`?
3. ¿Cómo accedes a la configuración desde una vista?

### 4.7. Ejercicio propuesto

**Ejercicio 4.1.** Añade una setting personalizada y léela en una vista.

**Pista:** `request.registry.settings`.

---

## 5. Rutas y vistas

### 5.1. `add_route` y `add_view`

`add_route` define un patrón con placeholders; `add_view` conecta la ruta a una vista.

### 5.2. Ejemplo con `@view_config`

```python
from pyramid.view import view_config


@view_config(route_name="home", renderer="json")
def home(request):
    return {"mensaje": "inicio"}


@view_config(route_name="usuario", renderer="json")
def usuario(request):
    user_id = int(request.matchdict["id"])
    return {"id": user_id}
```

**`routes.py`:**

```python
def includeme(config):
    config.add_route("home", "/")
    config.add_route("usuario", "/usuarios/{id}")
```

**Explicación línea por línea:**
- `@view_config(route_name="home", renderer="json")` asocia la vista a la ruta y declara que devuelve JSON.
- `request.matchdict["id"]` accede al parámetro de la ruta.
- `config.add_route("usuario", "/usuarios/{id}")` define la ruta con un placeholder.

### 5.3. `request.params`

```python
@view_config(route_name="buscar", renderer="json")
def buscar(request):
    q = request.params.get("q", "")
    return {"q": q}
```

**Explicación línea por línea:**
- `request.params` combina query string y datos de formulario.

### 5.4. Generar URLs con `route_url`

```python
url = request.route_url("usuario", id=42)   # http://localhost/usuarios/42
```

**Explicación línea por línea:**
- `route_url` genera la URL de una ruta por su nombre (equivalente a `url_for`).

### 5.5. Errores comunes

**Error 1 — Usar `renderer="json"` sin devolver un tipo serializable.**

**Error 2 — Olvidar registrar la ruta** antes de la vista.

### 5.6. Checkpoint de comprensión

1. ¿Qué hace `add_route`? ¿Y `add_view`?
2. ¿Cómo accedes a los parámetros de la ruta?
3. ¿Qué hace `route_url`?

### 5.7. Ejercicio propuesto

**Ejercicio 5.1.** Crea una ruta `/productos/{id}` que devuelva JSON con el id.

**Pista:** usa `request.matchdict`.

---

## 6. View configs y predicates

### 6.1. Qué son los predicates

Los **predicates** determinan **cuándo** se aplica una vista: por método HTTP, cabecera, `Accept`, etc. Permiten mapear varias vistas a la misma ruta.

### 6.2. Predicado por método HTTP

```python
@view_config(route_name="item", request_method="GET", renderer="json")
def ver_item(request):
    return {"accion": "ver"}


@view_config(route_name="item", request_method="POST", renderer="json")
def crear_item(request):
    return {"accion": "crear"}


@view_config(route_name="item", request_method="DELETE", renderer="json")
def borrar_item(request):
    return {"accion": "borrar"}
```

**Explicación línea por línea:**
- Tres vistas comparten la misma ruta, pero cada una responde a un método distinto.

### 6.3. Predicado personalizado

```python
def es_api(request):
    return "application/json" in request.accept


@view_config(route_name="recurso", custom_predicates=[es_api], renderer="json")
def api(request):
    return {"tipo": "api"}
```

### 6.4. Predicado por cabecera

```python
@view_config(route_name="home", header="X-Version:2", renderer="json")
def home_v2(request):
    return {"version": 2}
```

**Explicación línea por línea:**
- Solo se aplica si la petición trae la cabecera `X-Version: 2`.

### 6.5. Errores comunes

**Error 1 — Predicados que se solapan** sin orden claro.

**Error 2 — Abusar de predicados** en lugar de separar rutas.

### 6.6. Checkpoint de comprensión

1. ¿Qué determina un predicate?
2. ¿Cómo mapeas varias vistas a la misma ruta?

### 6.7. Ejercicio propuesto

**Ejercicio 6.1.** Crea dos vistas para `/recurso`: una para `Accept: text/html` y otra para JSON.

**Pista:** usa `accept=` como predicado.

---

## 7. Templates (Jinja2, Chameleon, Mako)

### 7.1. Elegir el motor

Pyramid no impone motor de plantillas. Se registra con `config.include('pyramid_jinja2')` (u otro).

### 7.2. Jinja2

```bash
pip install pyramid_jinja2
```

```python
config.include("pyramid_jinja2")
config.add_jinja2_renderer(".html")
config.add_jinja2_search_path("mi_proyecto:templates")
```

```python
@view_config(route_name="home", renderer="home.html")
def home(request):
    return {"titulo": "Inicio", "items": ["a", "b"]}
```

**`templates/home.html`:**

```html
<!DOCTYPE html>
<html lang="es">
<head><title>{{ titulo }}</title></head>
<body>
    <h1>{{ titulo }}</h1>
    <ul>
        {% for item in items %}
            <li>{{ item }}</li>
        {% endfor %}
    </ul>
</body>
</html>
```

**Explicación línea por línea:**
- `config.include("pyramid_jinja2")` activa Jinja2.
- `add_jinja2_search_path` indica dónde buscar plantillas.
- `renderer="home.html"` renderiza esa plantilla con el diccionario devuelto.

### 7.3. Chameleon y Mako

```python
config.include("pyramid_chameleon")
config.include("pyramid_mako")
```

- **Chameleon:** plantillas con TAL, validación XML.
- **Mako:** plantillas rápidas con sintaxis tipo Python.

### 7.4. Errores comunes

**Error 1 — No añadir la ruta de búsqueda** de plantillas.

**Error 2 — Mezclar motores** sin necesidad.

### 7.5. Checkpoint de comprensión

1. ¿Qué ventaja tiene poder elegir el motor de plantillas?
2. ¿Qué hace `renderer="home.html"`?

### 7.6. Ejercicio propuesto

**Ejercicio 7.1.** Renderiza una plantilla Jinja2 que muestre una lista de productos.

**Pista:** usa `renderer="productos.html"`.

---

## 8. Autenticación y autorización (ACL)

### 8.1. La seguridad declarativa

Pyramid implementa seguridad con:

- **Authentication policy:** identifica **quién** es el usuario.
- **Authorization policy:** decide **qué** puede hacer, mediante **ACL** (listas de control de acceso).

**Analogía:** la autenticación es el portero que te identifica; la autorización es la lista de qué puertas puedes abrir.

### 8.2. Definir una ACL

```python
from pyramid.security import Allow, Everyone


class Recurso:
    __acl__ = [
        (Allow, Everyone, "view"),
        (Allow, "group:editores", "edit"),
    ]

    def __init__(self, request):
        self.request = request
```

**Explicación línea por línea:**
- `__acl__` es una lista de reglas `(Allow/Deny, principal, permiso)`.
- `(Allow, Everyone, "view")` permite ver a **todos**.
- `(Allow, "group:editores", "edit")` permite editar solo al grupo `editores`.

### 8.3. Vista protegida

```python
@view_config(route_name="editar", renderer="json", permission="edit")
def editar(request):
    return {"mensaje": "solo editores"}
```

**Explicación línea por línea:**
- `permission="edit"` exige el permiso `edit`; si el usuario no lo tiene, recibe un 403.

### 8.4. Configurar las policies

```python
from pyramid.authentication import AuthTktAuthenticationPolicy
from pyramid.authorization import ACLAuthorizationPolicy


def grupo_usuario(userid, request):
    return ["group:editores"] if userid == "ana" else []


config.set_authentication_policy(
    AuthTktAuthenticationPolicy("secreto", callback=grupo_usuario, hashalg="sha512")
)
config.set_authorization_policy(ACLAuthorizationPolicy())
```

**Explicación línea por línea:**
- `AuthTktAuthenticationPolicy` identifica usuarios mediante una cookie firmada.
- `callback=grupo_usuario` determina a qué grupos pertenece un usuario.
- `ACLAuthorizationPolicy` aplica las ACL.

### 8.5. Errores comunes

**Error 1 — Confundir autenticación (quién eres) con autorización (qué puedes).**

**Error 2 — ACL demasiado permisiva** (`Allow, Everyone` para todo).

### 8.6. Checkpoint de comprensión

1. ¿Qué diferencia hay entre autenticación y autorización?
2. ¿Qué es una ACL?
3. ¿Qué hace `permission="edit"`?

### 8.7. Ejercicio propuesto

**Ejercicio 8.1.** Crea una ACL que permita ver a todos y editar solo al grupo `admin`.

**Pista:** usa `Allow`, `Everyone` y un principal de grupo.

---

## 9. Sesiones

### 9.1. Sesiones firmadas

Pyramid gestiona sesiones accesibles con `request.session`:

```python
from pyramid.view import view_config


@view_config(route_name="login", renderer="json")
def login(request):
    request.session["usuario"] = "ana"
    return {"ok": True}


@view_config(route_name="perfil", renderer="json")
def perfil(request):
    return {"usuario": request.session.get("usuario")}


@view_config(route_name="logout", renderer="json")
def logout(request):
    request.session.invalidate()
    return {"ok": True}
```

**Explicación línea por línea:**
- `request.session["usuario"] = ...` guarda un dato.
- `.get(...)` lo recupera.
- `.invalidate()` destruye la sesión.

### 9.2. Configurar el tipo de sesión

```python
from pyramid.session import SignedCookieSessionFactory

config.set_session_factory(SignedCookieSessionFactory("secreto"))
```

**Explicación línea por línea:**
- `SignedCookieSessionFactory` guarda la sesión en una cookie **firmada** (no se puede alterar).

### 9.3. Errores comunes

**Error 1 — Guardar objetos grandes en la sesión** (viajan al cliente).

**Error 2 — No invalidar la sesión** al cerrar sesión.

### 9.4. Checkpoint de comprensión

1. ¿Cómo se accede a la sesión?
2. ¿Qué hace `invalidate`?

### 9.5. Ejercicio propuesto

**Ejercicio 9.1.** Implementa un contador de visitas por sesión.

**Pista:** incrementa `request.session["visitas"]`.

---

## 10. Traversal vs URL dispatch

### 10.1. Dos paradigmas de enrutado

- **URL dispatch (rutas):** como Flask/Django; explícitas y directas.
- **Traversal:** navega un **árbol de recursos**; ideal para jerarquías y ACL por recurso.

### 10.2. Traversal

```python
class Raiz:
    __acl__ = [(Allow, Everyone, "view")]

    def __init__(self, request):
        self.request = request

    def __getitem__(self, key):
        """Navega a un hijo por nombre."""
        return Recurso(key)


def recurso_view(context, request):
    return Response(f"Recurso: {context.nombre}")


config.set_root_factory(Raiz)
config.add_view(recurso_view, context=Recurso, renderer="string")
```

**Explicación línea por línea:**
- `set_root_factory(Raiz)` define el nodo raíz del árbol.
- `__getitem__` navega a los hijos según los segmentos de la URL.
- `context=Recurso` asocia la vista a un tipo de recurso.

**¿Cómo funciona?** Para la URL `/libros/ficciones`, Pyramid navega: raíz → hijo "libros" → hijo "ficciones", y aplica la ACL de cada nodo.

### 10.3. Cuándo usar cada uno

- **URL dispatch:** la mayoría de APIs y apps.
- **Traversal:** permisos por objeto, wikis, CMS jerárquicos.

### 10.4. Errores comunes

**Error 1 — Mezclar ambos sin criterio.** Dificulta el mantenimiento.

**Error 2 — Profundidad de árbol excesiva.**

### 10.5. Checkpoint de comprensión

1. ¿Qué diferencia hay entre URL dispatch y traversal?
2. ¿Cuándo usarías traversal?

### 10.6. Ejercicio propuesto

**Ejercicio 10.1.** Define un árbol con una raíz y dos recursos hijos.

**Pista:** implementa `__getitem__`.

---

## 11. Integración con SQLAlchemy

### 11.1. El ORM más usado con Pyramid

Pyramid se integra con SQLAlchemy mediante `pyramid_sqlalchemy` o manualmente con `request.dbsession`.

### 11.2. Configuración manual

```python
from sqlalchemy import create_engine
from sqlalchemy.orm import declarative_base, sessionmaker

Base = declarative_base()


def includeme(config):
    settings = config.registry.settings
    engine = create_engine(settings["sqlalchemy.url"])
    session_factory = sessionmaker(bind=engine)

    def dbsession(request):
        session = session_factory()

        def cleanup(request):
            session.close()

        request.add_finished_callback(cleanup)
        return session

    config.add_request_method(dbsession, "dbsession", reify=True)
```

**Explicación línea por línea:**
- `create_engine(settings["sqlalchemy.url"])` crea el motor con la URL del `.ini`.
- `add_request_method` añade `request.dbsession`.
- `add_finished_callback(cleanup)` cierra la sesión al terminar la petición.
- `reify=True` hace que la sesión se cree una sola vez por petición.

### 11.3. Modelo y vista

```python
from sqlalchemy import Column, Integer, String


class Usuario(Base):
    __tablename__ = "usuarios"
    id = Column(Integer, primary_key=True)
    nombre = Column(String(80))


@view_config(route_name="usuarios", renderer="json")
def listar(request):
    usuarios = request.dbsession.query(Usuario).all()
    return {"usuarios": [u.nombre for u in usuarios]}
```

**Explicación línea por línea:**
- El modelo es SQLAlchemy puro.
- La vista usa `request.dbsession` para consultar.

### 11.4. Errores comunes

**Error 1 — Sesiones sin cerrar.** Usa callbacks de finalización.

**Error 2 — Un engine por petición.** Créalo una vez.

### 11.5. Checkpoint de comprensión

1. ¿Cómo se añade una sesión de BD a la petición?
2. ¿Por qué cerrar la sesión al terminar?

### 11.6. Ejercicio propuesto

**Ejercicio 11.1.** Crea una vista que inserte un usuario con `request.dbsession`.

**Pista:** `add` + `commit`.

---

## 12. Integración con otros ORM

### 12.1. Libertad de elección

Pyramid no impone ORM. Puedes usar **Peewee**, **SQLObject**, **MongoDB** (motor), etc.

### 12.2. Peewee

```python
from peewee import CharField, Model, SqliteDatabase

db = SqliteDatabase("app.db")


class Usuario(Model):
    nombre = CharField()

    class Meta:
        database = db
```

Se integra con `add_request_method` igual que SQLAlchemy.

### 12.3. MongoDB

Para Pyramid (WSGI) suele usarse `pymongo`:

```python
from pymongo import MongoClient

cliente = MongoClient("mongodb://localhost:27017")
db = cliente["app"]
```

### 12.4. Errores comunes

**Error 1 — Mezclar ORM en el mismo proyecto** sin necesidad.

**Error 2 — No gestionar el ciclo de vida** de la conexión.

### 12.5. Checkpoint de comprensión

1. ¿Qué ventaja tiene poder elegir el ORM?
2. ¿Cómo integras una librería con Pyramid?

### 12.6. Ejercicio propuesto

**Ejercicio 12.1.** Integra Peewee y expón un endpoint de listado.

**Pista:** usa `Usuario.select()`.

---

## 13. Testing

### 13.1. Herramientas de testing

Pyramid ofrece `testing.setUp`/`tearDown` y `DummyRequest` para pruebas unitarias, además de `WebTest` para integración.

### 13.2. Prueba unitaria

```python
import unittest
from pyramid import testing


class TestVistas(unittest.TestCase):
    def setUp(self):
        self.config = testing.setUp()

    def tearDown(self):
        testing.tearDown()

    def test_home(self):
        from mi_proyecto.views import home
        request = testing.DummyRequest()
        resultado = home(request)
        self.assertEqual(resultado["mensaje"], "inicio")
```

**Explicación línea por línea:**
- `testing.setUp()` prepara el entorno.
- `DummyRequest()` simula una petición.
- `testing.tearDown()` limpia.

### 13.3. Prueba de integración con WebTest

```python
import unittest
from webtest import TestApp
from mi_proyecto import main


class TestIntegracion(unittest.TestCase):
    def setUp(self):
        app = main({}, sqlalchemy_url="sqlite://")
        self.testapp = TestApp(app)

    def test_home(self):
        respuesta = self.testapp.get("/", status=200)
        self.assertIn("inicio", respuesta.text)
```

**Explicación línea por línea:**
- `TestApp(app)` envuelve la app WSGI para simular peticiones.
- `self.testapp.get("/", status=200)` hace una petición y exige el estado 200.

### 13.4. Errores comunes

**Error 1 — No llamar a `testing.tearDown()`** y contaminar tests.

**Error 2 — Probar contra la BD real.**

### 13.5. Checkpoint de comprensión

1. ¿Para qué sirve `DummyRequest`?
2. ¿Qué hace `TestApp`?

### 13.6. Ejercicio propuesto

**Ejercicio 13.1.** Escribe un test con `WebTest` para una ruta JSON.

**Pista:** `self.testapp.get("/api", status=200).json`.

---

## 14. Despliegue

### 14.1. Servidores WSGI

Se usa un servidor WSGI como **Waitress** (multiplataforma) o **Gunicorn** (Unix), detrás de Nginx.

### 14.2. Waitress

```bash
pip install waitress
pserve production.ini
```

**`production.ini`:**

```ini
[server:main]
use = egg:waitress#main
listen = 0.0.0.0:6543
```

### 14.3. Gunicorn

```bash
gunicorn --paste production.ini
```

### 14.4. Nginx

```nginx
server {
    listen 80;
    server_name ejemplo.com;

    location /static/ {
        alias /var/www/app/static/;
    }

    location / {
        proxy_pass http://127.0.0.1:6543;
        proxy_set_header Host $host;
    }
}
```

### 14.5. Errores comunes

**Error 1 — Usar `pserve --reload` en producción.**

**Error 2 — No servir estáticos** eficientemente.

### 14.6. Checkpoint de comprensión

1. ¿Qué servidor WSGI usarías en Windows? ¿Y en Linux?
2. ¿Qué papel juega Nginx?

### 14.7. Ejercicio propuesto

**Ejercicio 14.1.** Despliega con Waitress detrás de Nginx.

**Pista:** ajusta `production.ini`.

---

## 15. Comparativa con Flask y Django

| Criterio | Pyramid | Flask | Django |
|---|---|---|---|
| Filosofía | Flexible, explícito | Minimalista | Todo incluido |
| ORM | A elección | A elección | Incluido |
| Plantillas | A elección | Jinja2 | DTL |
| Seguridad | ACL declarativa | Manual/extensiones | Incluida |
| Enrutado | URL dispatch + traversal | Rutas | URLs |
| Admin | No | No | Sí |
| Curva | Media-alta | Baja | Alta |
| Ideal para | Apps grandes y flexibles | Prototipos y APIs | Apps completas |

### 15.1. Ejercicio propuesto

**Ejercicio 15.1.** Reescribe un CRUD simple en Pyramid y compáralo con Flask.

**Pista:** nota cuánta configuración explícita requiere cada uno.

---

## 16. Buenas prácticas

1. **Usa la fábrica `main`** y `config.scan`.
2. **Separa rutas, vistas, modelos y plantillas.**
3. **Configura por `.ini`** según entorno.
4. **Gestiona sesiones de BD** con callbacks.
5. **Aplica ACL** con mínimo privilegio.
6. **Usa `route_url`** para generar URLs.
7. **Tests unitarios con `DummyRequest`** y de integración con `WebTest`.
8. **Despliega con Waitress/Gunicorn + Nginx.**
9. **Elige el ORM y las plantillas con criterio.**
10. **No abuses de traversal** si no necesitas jerarquías.

---

## 17. Recursos

- **Documentación oficial:** <https://docs.pylonsproject.org/projects/pyramid/>
- **Cookiecutters:** <https://github.com/Pylons?q=cookiecutter>
- **`pyramid_jinja2`:** <https://docs.pylonsproject.org/projects/pyramid-jinja2/>
- **Waitress:** <https://docs.pylonsproject.org/projects/waitress/>
- **Libro:** *The Pyramid Web Application Development Framework*, Chris McDonough.

---

## 18. Mini resumen final

- Pyramid es un framework **flexible y explícito** ("start small, finish big").
- Se configura por **`.ini`** y una **fábrica WSGI**.
- Las **rutas** y **vistas** se registran por separado; los **predicates** refinan el mapeo.
- El motor de **plantillas** es a elección (Jinja2, Chameleon, Mako).
- La **seguridad** usa authentication policy + **ACL**.
- Soporta **URL dispatch** y **traversal**.
- Se integra con **SQLAlchemy** u otros ORM.
- Se despliega con **Waitress/Gunicorn + Nginx**.

---

### 🎯 Retos opcionales

1. **Reto 1:** Construye una API REST con Pyramid + SQLAlchemy.
2. **Reto 2:** Implementa permisos por recurso con traversal y ACL.
3. **Reto 3:** Añade autenticación por sesión y una página protegida.

**Anterior:** [12_fastapi_orm.md](12_fastapi_orm.md) · **Siguiente (Parte III):** [14_sqlalchemy.md](14_sqlalchemy.md)
