# 09 · Flask

**Versión recomendada:** Flask 3.0 · Python 3.12
**Requisito:** [08_python_avanzado.md](08_python_avanzado.md)
**Nivel:** Intermedio
**Duración estimada de estudio:** 10–14 horas

---

## Índice de contenidos

1. [Introducción a Flask y su filosofía](#1-introducción-a-flask-y-su-filosofía)
2. [Instalación y estructura de proyecto](#2-instalación-y-estructura-de-proyecto)
3. [Rutas y vistas](#3-rutas-y-vistas)
4. [Métodos HTTP](#4-métodos-http)
5. [Templates con Jinja2](#5-templates-con-jinja2)
6. [Formularios y validación (Flask-WTF)](#6-formularios-y-validación-flask-wtf)
7. [Sesiones y cookies](#7-sesiones-y-cookies)
8. [Blueprints y modularización](#8-blueprints-y-modularización)
9. [Middleware y hooks](#9-middleware-y-hooks)
10. [Manejo de errores](#10-manejo-de-errores)
11. [Archivos estáticos](#11-archivos-estáticos)
12. [Configuración y variables de entorno](#12-configuración-y-variables-de-entorno)
13. [Extensiones populares](#13-extensiones-populares)
14. [Testing en Flask](#14-testing-en-flask)
15. [Despliegue](#15-despliegue)
16. [API REST con Flask](#16-api-rest-con-flask)
17. [Buenas prácticas](#17-buenas-prácticas)
18. [Recursos](#18-recursos)
19. [Mini resumen final](#19-mini-resumen-final)

---

## 1. Introducción a Flask y su filosofía

### 1.1. ¿Qué es un framework web y por qué necesitas uno?

Cuando escribes una aplicación web, necesitas resolver siempre los mismos problemas:

- Recibir peticiones HTTP y decidir qué hacer con cada una.
- Generar HTML o JSON como respuesta.
- Gestionar rutas, formularios, sesiones, cookies.
- Servir archivos estáticos (CSS, imágenes).

Podrías programar todo eso desde cero con sockets, pero sería enorme y repetitivo. Un **framework web** te da esas piezas resueltas para que te centres en la lógica de tu aplicación.

**Analogía:** un framework web es como una **cocina equipada**. No tienes que fabricar los fogones ni las sartenes; vienen puestos. Tú solo cocinas (escribes tu aplicación).

### 1.2. ¿Qué hace especial a Flask?

**Flask** es un **microframework**: trae lo mínimo y deja que tú elijas el resto. "Micro" no significa que haga poco, sino que **no impone decisiones**.

Sus principios:

- **Minimalista:** una aplicación completa puede caber en un solo archivo.
- **Explícito:** pocas convenciones ocultas (recuerda el Zen: "explícito es mejor que implícito").
- **Extensible:** crece contigo mediante **extensiones** (base de datos, autenticación...).
- **Basado en Werkzeug** (utilidades WSGI) y **Jinja2** (plantillas).

### 1.3. ¿Cuándo usar Flask?

| Escenario | ¿Flask? |
|---|---|
| API REST pequeña o mediana | Sí |
| Prototipo rápido | Sí |
| Microservicio | Sí |
| App con panel de administración automático | Mejor Django (archivo 11) |
| App con ORM, auth y admin integrados | Mejor Django |
| API de altísimo rendimiento async | Mejor FastAPI (archivo 12) |

### 1.4. Tu primera aplicación

```python
from flask import Flask

app = Flask(__name__)


@app.route("/")
def inicio():
    return "¡Hola, Flask!"


if __name__ == "__main__":
    app.run(debug=True)
```

Ejecución:

```bash
flask --app app run --debug
```

Salida esperada:

```text
 * Serving Flask app 'app'
 * Debug mode: on
 * Running on http://127.0.0.1:5000
```

**Explicación línea por línea:**
- `from flask import Flask` importa la clase `Flask`.
- `app = Flask(__name__)` crea la aplicación. `__name__` le dice a Flask dónde buscar las plantillas y los archivos estáticos (en la carpeta del módulo).
- `@app.route("/")` es un **decorador** (archivo 04) que asocia la URL `/` a la función siguiente.
- `def inicio():` es la **vista**: la función que responde a esa URL.
- `return "¡Hola, Flask!"` devuelve el contenido de la respuesta.
- `app.run(debug=True)` levanta el **servidor de desarrollo** con recarga automática (útil mientras programas).

**Visita `http://127.0.0.1:5000` en el navegador y verás el saludo.**

### 1.5. El servidor de desarrollo no es para producción

`app.run()` levanta un servidor pensado para **desarrollo**: es cómodo pero no seguro ni eficiente. En producción se usa **Gunicorn** o **uWSGI** (sección 15).

### 1.6. Errores comunes

**Error 1 — Usar `app.run()` en producción.**

*Solución:* usa Gunicorn/uWSGI detrás de Nginx.

**Error 2 — `debug=True` en producción.**

Expone información interna y permite ejecutar código. *Solución:* desactívalo.

**Error 3 — Nombrar mal el módulo.**

Si ejecutas `flask --app app`, el archivo debe llamarse `app.py` o exponer una variable `app`.

### 1.7. Checkpoint de comprensión

1. ¿Qué significa que Flask sea un "microframework"?
2. ¿Qué hace el decorador `@app.route`?
3. ¿Por qué no se debe usar `app.run()` en producción?

### 1.8. Ejercicio propuesto

**Ejercicio 1.1.** Crea una app Flask con una ruta `/saludo/<nombre>` que salude al nombre.

**Pista:** los parámetros de ruta se declaran entre `<>`: `@app.route("/saludo/<nombre>")`.

---

## 2. Instalación y estructura de proyecto

### 2.1. Crear el entorno e instalar

```bash
python -m venv .venv
source .venv/bin/activate        # Linux/macOS
pip install flask
pip freeze > requirements.txt
```

Recuerda del archivo 01: **siempre** un entorno virtual por proyecto.

### 2.2. Estructura de un proyecto pequeño

```text
mi_app/
├── .venv/
├── app.py
├── config.py
├── requirements.txt
├── static/
│   └── css/style.css
├── templates/
│   ├── base.html
│   └── index.html
└── tests/
    └── test_app.py
```

- `templates/`: plantillas HTML (Jinja2).
- `static/`: CSS, JavaScript, imágenes.
- `tests/`: pruebas.

### 2.3. Estructura de un proyecto grande (con blueprints)

Cuando la app crece, se usa el patrón **application factory**:

```text
mi_app/
├── mi_app/
│   ├── __init__.py      # fábrica create_app()
│   ├── routes.py
│   ├── models.py
│   ├── templates/
│   └── static/
├── tests/
├── config.py
└── wsgi.py
```

### 2.4. La fábrica de aplicaciones

```python
# mi_app/__init__.py
from flask import Flask


def create_app(config_object="config.Config") -> Flask:
    """Crea y configura la aplicación Flask."""
    app = Flask(__name__)
    app.config.from_object(config_object)

    from .routes import bp
    app.register_blueprint(bp)

    return app
```

**Explicación línea por línea:**
- `create_app` es una **fábrica**: crea una instancia nueva cada vez que se llama.
- `app.config.from_object(...)` carga la configuración desde un objeto/clase.
- `app.register_blueprint(bp)` registra un módulo de rutas (sección 8).

**¿Por qué usar una fábrica?** Porque permite crear varias instancias con configuraciones distintas (desarrollo, test, producción) y facilita el testing. Es el patrón recomendado en proyectos serios.

### 2.5. Errores comunes

**Error 1 — Todo en un archivo gigante.**

*Solución:* usa la fábrica y blueprints cuando crezca.

**Error 2 — No fijar las versiones.**

*Solución:* mantén `requirements.txt` actualizado con `pip freeze`.

### 2.6. Checkpoint de comprensión

1. ¿Qué carpetas especiales usa Flask por convención?
2. ¿Para qué sirve el patrón de fábrica `create_app()`?
3. ¿Qué ventaja tiene `requirements.txt`?

### 2.7. Ejercicio propuesto

**Ejercicio 2.1.** Reestructura la app del ejercicio 1.1 usando `create_app()`.

**Pista:** registra las rutas dentro de la fábrica.

---

## 3. Rutas y vistas

### 3.1. ¿Qué es una ruta?

Una **ruta** asocia una **URL** con una **función** (la **vista**) que genera la respuesta. Cuando el usuario visita `/productos`, Flask busca la vista registrada para esa ruta y la ejecuta.

### 3.2. Rutas con parámetros dinámicos

Los parámetros se declaran con `<nombre>` y pueden tener **convertidores** de tipo:

```python
from flask import Flask

app = Flask(__name__)


@app.route("/")
def inicio():
    return "Inicio"


@app.route("/usuario/<nombre>")
def perfil(nombre: str):
    return f"Perfil de {nombre}"


@app.route("/post/<int:post_id>")
def post(post_id: int):
    return f"Post número {post_id}"


@app.route("/archivo/<path:ruta>")
def archivo(ruta: str):
    return f"Archivo: {ruta}"
```

**Explicación línea por línea:**
- `@app.route("/usuario/<nombre>")` define una ruta con un segmento variable. Si visitas `/usuario/ana`, `nombre` valdrá `"ana"`.
- `<int:post_id>` convierte automáticamente el segmento a **entero**. Si visitas `/post/abc`, Flask devuelve un **404** (no coincide).
- `<path:ruta>` acepta barras, útil para rutas de archivos.

**Convertidores disponibles:**

| Convertidor | Acepta |
|---|---|
| `string` (por defecto) | Texto sin barras |
| `int` | Enteros |
| `float` | Decimales |
| `path` | Texto con barras |
| `uuid` | UUIDs |

### 3.3. Generar URLs con `url_for`

En lugar de escribir URLs a mano, **genera** las URLs a partir del **nombre de la función**:

```python
from flask import Flask, url_for

app = Flask(__name__)


@app.route("/usuario/<nombre>")
def perfil(nombre: str):
    return nombre


with app.test_request_context():
    print(url_for("perfil", nombre="ana"))
```

Salida esperada:

```text
/usuario/ana
```

**Explicación línea por línea:**
- `url_for("perfil", nombre="ana")` genera la URL de la vista `perfil` con ese parámetro.
- `test_request_context()` permite usar `url_for` fuera de una petición (útil en pruebas).

**¿Por qué no escribir las URLs a mano?** Porque si cambias la ruta, tendrías que actualizar todas las URLs. Con `url_for`, se actualizan solas. Además, maneja correctamente el *escaping* de caracteres.

### 3.4. Ejemplos progresivos

**Ejemplo 1 — Ruta con cálculo.**

```python
@app.route("/suma/<int:a>/<int:b>")
def suma(a: int, b: int):
    return {"resultado": a + b}
```

Visitar `/suma/2/3` devuelve:

```json
{"resultado": 5}
```

**Ejemplo 2 — Ruta con valor por defecto.**

```python
@app.route("/pagina/")
@app.route("/pagina/<int:num>")
def pagina(num: int = 1):
    return f"Página {num}"
```

**Explicación línea por línea:**
- Dos decoradores en la misma función permiten dos URLs: `/pagina/` y `/pagina/5`.
- Si no se pasa `num`, se usa el valor por defecto `1`.

### 3.5. Errores comunes

**Error 1 — Nombres de función duplicados.**

Flask usa el **nombre de la función** para `url_for`. Si dos vistas se llaman igual, la segunda sobrescribe a la primera.

**Error 2 — Rutas que chocan.**

`/usuario/<nombre>` y `/usuario/admin` chocan: `admin` se interpretaría como un nombre. *Solución:* define primero la ruta estática `/usuario/admin`.

**Error 3 — Escribir URLs a mano** en lugar de usar `url_for`.

### 3.6. Checkpoint de comprensión

1. ¿Qué diferencia hay entre `<nombre>` y `<int:nombre>`?
2. ¿Por qué se prefiere `url_for` a escribir URLs a mano?
3. ¿Qué pasa si dos rutas chocan?

### 3.7. Ejercicio propuesto

**Ejercicio 3.1.** Crea una ruta `/calculadora/<float:a>/<float:b>` que devuelva suma, resta y producto en JSON.

**Pista:** devuelve un `dict`; Flask lo serializa automáticamente a JSON.

---

## 4. Métodos HTTP

### 4.1. ¿Qué son los métodos HTTP?

Cuando un navegador (o un cliente) habla con un servidor, usa un **método** que indica la **intención** de la petición:

| Método | Uso |
|---|---|
| GET | Obtener un recurso (no debe modificar nada) |
| POST | Crear un recurso / enviar datos |
| PUT | Reemplazar un recurso completo |
| PATCH | Modificar parcialmente un recurso |
| DELETE | Eliminar un recurso |

**Por defecto, una ruta solo acepta `GET`.** Para aceptar otros, debes declararlo.

### 4.2. Aceptar varios métodos

```python
from flask import Flask, request

app = Flask(__name__)


@app.route("/tarea", methods=["GET", "POST"])
def tarea():
    if request.method == "POST":
        datos = request.get_json()
        return {"creada": datos}, 201
    return {"mensaje": "Lista de tareas"}


@app.route("/tarea/<int:tarea_id>", methods=["DELETE"])
def eliminar(tarea_id: int):
    return "", 204
```

**Explicación línea por línea:**
- `methods=["GET", "POST"]` declara qué métodos acepta la ruta.
- `request.method` dice qué método usó el cliente.
- `request.get_json()` lee el cuerpo de la petición como JSON.
- `return {"creada": datos}, 201` devuelve un **código de estado** 201 (creado).
- `return "", 204` devuelve vacío con estado 204 (sin contenido).

### 4.3. Códigos de estado HTTP

Los códigos comunican el resultado de la petición:

| Rango | Significado | Ejemplos |
|---|---|---|
| 2xx | Éxito | 200 OK, 201 Created, 204 No Content |
| 3xx | Redirección | 301, 302 |
| 4xx | Error del cliente | 400, 401, 403, 404 |
| 5xx | Error del servidor | 500 |

```python
@app.route("/ok")
def ok():
    return {"estado": "ok"}, 200


@app.route("/creado", methods=["POST"])
def creado():
    return {"id": 1}, 201


@app.route("/no-encontrado")
def no_encontrado():
    return {"error": "no encontrado"}, 404
```

### 4.4. Leer datos de la petición

```python
from flask import request


@app.route("/buscar")
def buscar():
    q = request.args.get("q", "")          # query string: /buscar?q=python
    return {"q": q}


@app.route("/enviar", methods=["POST"])
def enviar():
    nombre = request.form.get("nombre")    # formulario HTML
    return {"nombre": nombre}
```

**Explicación línea por línea:**
- `request.args` contiene los parámetros de la URL (`?q=...`).
- `request.form` contiene los datos de un formulario HTML.
- `request.get_json()` contiene un cuerpo JSON.

### 4.5. Errores comunes

**Error 1 — Olvidar declarar `methods=["POST"]`.**

```text
405 Method Not Allowed
```

**Error 2 — Leer `request.form` cuando el cliente envía JSON.**

*Solución:* usa `request.get_json()`.

**Error 3 — Devolver `200` al crear un recurso.**

*Solución:* usa `201`.

### 4.6. Checkpoint de comprensión

1. ¿Qué método HTTP usarías para crear un recurso? ¿Y para eliminarlo?
2. ¿Qué significa el código 404? ¿Y el 201?
3. ¿Dónde se leen los datos de un formulario? ¿Y los de un JSON?

### 4.7. Ejercicio propuesto

**Ejercicio 4.1.** Implementa un CRUD en memoria con GET, POST y DELETE.

**Pista:** usa una lista global como almacén (solo para practicar; en producción usa una base de datos).

---

## 5. Templates con Jinja2

### 5.1. Por qué separar HTML del código Python

Si generas HTML concatenando cadenas en Python, el código se vuelve un caos. Las **plantillas** (*templates*) separan la **presentación** (HTML) de la **lógica** (Python). Flask usa **Jinja2**.

**Analogía:** una plantilla es como un formulario con huecos. El HTML define la estructura con huecos (`{{ variable }}`), y el código Python rellena esos huecos con datos.

### 5.2. Sintaxis de Jinja2

- `{{ variable }}` → **muestra** un valor.
- `{% ... %}` → **lógica** (bucles, condicionales, herencia).
- `{# comentario #}` → comentario.

### 5.3. Herencia de plantillas

La **herencia** permite definir un esqueleto común y reutilizarlo.

**`templates/base.html`:**

```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <title>{% block title %}Mi App{% endblock %}</title>
</head>
<body>
    <nav>
        <a href="{{ url_for('inicio') }}">Inicio</a>
    </nav>
    <main>
        {% block contenido %}{% endblock %}
    </main>
</body>
</html>
```

**`templates/index.html`:**

```html
{% extends "base.html" %}

{% block title %}Inicio{% endblock %}

{% block contenido %}
<h1>Hola, {{ nombre }}</h1>
<ul>
    {% for item in items %}
        <li>{{ loop.index }}. {{ item }}</li>
    {% endfor %}
</ul>
{% endblock %}
```

**Explicación línea por línea:**
- `{% block title %}...{% endblock %}` define una **sección reemplazable**.
- `{% extends "base.html" %}` indica que esta plantilla **hereda** de `base.html`.
- `{% block contenido %}...{% endblock %}` rellena la sección correspondiente.
- `{{ nombre }}` inserta la variable.
- `{% for item in items %}` itera; `loop.index` es el número de iteración (empieza en 1).

### 5.4. Renderizar desde la vista

```python
from flask import render_template

@app.route("/")
def inicio():
    return render_template("index.html", nombre="Ana", items=["uno", "dos"])
```

**Explicación línea por línea:**
- `render_template("index.html", ...)` busca la plantilla en `templates/` y la renderiza con las variables indicadas.

### 5.5. Filtros

Los **filtros** transforman valores dentro de la plantilla:

```html
<p>{{ texto|upper }}</p>
<p>{{ precio|round(2) }}</p>
<p>{{ fecha|default("sin fecha") }}</p>
<p>{{ nombre|length }} caracteres</p>
```

**Explicación línea por línea:**
- `|upper` pone en mayúsculas; `|round(2)` redondea; `|default(...)` da un valor por defecto; `|length` cuenta.

### 5.6. Condicionales en plantillas

```html
{% if usuario %}
    <p>Bienvenido, {{ usuario }}</p>
{% else %}
    <p>Inicia sesión</p>
{% endif %}
```

### 5.7. Errores comunes

**Error 1 — Desactivar el autoescape.**

Jinja2 **escapa** el HTML por defecto, evitando ataques XSS. No uses `|safe` con datos del usuario.

**Error 2 — Poner lógica compleja en la plantilla.**

*Solución:* prepara los datos en la vista y pasa valores simples.

**Error 3 — Olvidar que las variables no están definidas.**

Si pasas una variable que la plantilla espera, obtienes un error. Pasa siempre lo que la plantilla usa.

### 5.8. Checkpoint de comprensión

1. ¿Qué diferencia hay entre `{{ }}` y `{% %}`?
2. ¿Para qué sirve `{% extends %}`?
3. ¿Por qué Jinja2 escapa el HTML por defecto?

### 5.9. Ejercicio propuesto

**Ejercicio 5.1.** Crea una plantilla que muestre una tabla de productos con cabecera y filas alternas.

**Pista:** usa `loop.index is even` para alternar estilos.

---

## 6. Formularios y validación (Flask-WTF)

### 6.1. El problema de los formularios

Un formulario HTML envía datos que **nunca** debes confiar: pueden estar vacíos, mal formados o ser maliciosos. Necesitas **validar** en el servidor. **Flask-WTF** integra **WTForms** con Flask y añade **protección CSRF**.

### 6.2. Instalación

```bash
pip install flask-wtf
```

### 6.3. Definir un formulario

```python
from flask_wtf import FlaskForm
from wtforms import PasswordField, StringField, SubmitField
from wtforms.validators import DataRequired, Email, Length


class RegistroForm(FlaskForm):
    """Formulario de registro."""

    email = StringField("Correo", validators=[DataRequired(), Email()])
    password = PasswordField("Contraseña", validators=[DataRequired(), Length(min=8)])
    enviar = SubmitField("Registrarse")
```

**Explicación línea por línea:**
- `FlaskForm` es la base de los formularios.
- Cada campo (`StringField`, `PasswordField`...) define un input.
- `validators=[...]` lista las reglas: `DataRequired` (no vacío), `Email` (formato válido), `Length(min=8)`.

### 6.4. Usar el formulario en la vista

```python
from flask import Flask, flash, redirect, render_template, url_for

app = Flask(__name__)
app.config["SECRET_KEY"] = "cambia-esto"


@app.route("/registro", methods=["GET", "POST"])
def registro():
    form = RegistroForm()
    if form.validate_on_submit():
        flash(f"Usuario {form.email.data} registrado", "success")
        return redirect(url_for("registro"))
    return render_template("registro.html", form=form)
```

**Explicación línea por línea:**
- `form.validate_on_submit()` devuelve `True` solo si es un POST **y** los datos son válidos.
- `form.email.data` accede al valor enviado.
- `flash(...)` guarda un mensaje para mostrarlo una vez.
- `redirect(url_for(...))` redirige a otra URL.

### 6.5. La plantilla del formulario

```html
<form method="post">
    {{ form.hidden_tag() }}
    <p>{{ form.email.label }} {{ form.email() }}</p>
    <p>{{ form.password.label }} {{ form.password() }}</p>
    <p>{{ form.enviar() }}</p>
</form>
```

**Explicación línea por línea:**
- `form.hidden_tag()` inserta el **token CSRF** (protección contra ataques de falsificación).
- `form.email()` genera el `<input>` del campo.
- `form.email.label` genera la etiqueta.

### 6.6. La protección CSRF

**CSRF** (*Cross-Site Request Forgery*) es un ataque donde un sitio malicioso hace que tu navegador envíe una petición a otro sitio donde estás autenticado. El token CSRF lo impide: el servidor verifica que la petición venga de su propio formulario.

**Requiere `SECRET_KEY`** configurada.

### 6.7. Errores comunes

**Error 1 — No configurar `SECRET_KEY`.**

*Síntoma:* `RuntimeError: The session is unavailable because no secret key was set`. CSRF y sesiones no funcionan.

**Error 2 — Confiar solo en la validación del cliente.**

La validación HTML (`required`, `type="email"`) es solo comodidad; se puede saltar. **Valida siempre en el servidor.**

**Error 3 — Mostrar los errores de validación.**

```html
{% for error in form.email.errors %}
    <span class="error">{{ error }}</span>
{% endfor %}
```

### 6.8. Checkpoint de comprensión

1. ¿Por qué hay que validar en el servidor si ya se valida en el navegador?
2. ¿Qué hace `form.validate_on_submit()`?
3. ¿Para qué sirve el token CSRF?

### 6.9. Ejercicio propuesto

**Ejercicio 6.1.** Añade validación de que la contraseña contenga al menos un número.

**Pista:** investiga el validador `Regexp` de `wtforms.validators`.

---

## 7. Sesiones y cookies

### 7.1. HTTP no recuerda nada

HTTP es **sin estado**: cada petición es independiente y el servidor no recuerda quién eres. Pero las aplicaciones web necesitan recordar (login, carrito de compra). Para eso existen las **cookies** y las **sesiones**.

- **Cookie:** un pequeño dato que el servidor envía al navegador, que lo guarda y lo reenvía en cada petición.
- **Sesión:** datos asociados a un usuario, normalmente identificados por una cookie.

### 7.2. Sesiones en Flask

Flask guarda la sesión en una cookie **firmada** (el cliente no puede modificarla sin invalidarla):

```python
from flask import Flask, redirect, session, url_for

app = Flask(__name__)
app.config["SECRET_KEY"] = "secreto"


@app.route("/login/<usuario>")
def login(usuario: str):
    session["usuario"] = usuario
    return redirect(url_for("perfil"))


@app.route("/perfil")
def perfil():
    usuario = session.get("usuario")
    if not usuario:
        return redirect(url_for("login", usuario="invitado"))
    return f"Sesión de {usuario}"


@app.route("/logout")
def logout():
    session.pop("usuario", None)
    return "Sesión cerrada"
```

**Explicación línea por línea:**
- `session["usuario"] = usuario` guarda un dato en la sesión.
- `session.get("usuario")` lo recupera (o `None` si no existe).
- `session.pop("usuario", None)` lo elimina al cerrar sesión.

### 7.3. Cookies manuales

```python
from flask import make_response

@app.route("/preferencias")
def preferencias():
    resp = make_response("Preferencias guardadas")
    resp.set_cookie("tema", "oscuro", max_age=60 * 60 * 24 * 30)
    return resp
```

**Explicación línea por línea:**
- `make_response(...)` crea una respuesta que puedes modificar.
- `.set_cookie(nombre, valor, max_age=...)` añade una cookie que dura 30 días.

### 7.4. Firmada no significa secreta

La sesión de Flask está **firmada** (no se puede alterar sin la clave), pero **no cifrada**: el cliente puede **leerla**. Por tanto, **nunca** guardes contraseñas ni datos sensibles en la sesión.

### 7.5. Errores comunes

**Error 1 — Guardar datos sensibles en la sesión.**

*Solución:* guarda solo identificadores; los datos sensibles van en el servidor.

**Error 2 — `SECRET_KEY` débil o en el código.**

*Solución:* usa una clave larga y aleatoria, cargada desde una variable de entorno.

**Error 3 — No invalidar la sesión al cerrar sesión.**

*Solución:* elimina las claves relevantes con `session.pop` o `session.clear()`.

### 7.6. Checkpoint de comprensión

1. ¿Qué diferencia hay entre una cookie y una sesión?
2. ¿Por qué no se deben guardar secretos en la sesión de Flask?
3. ¿Qué hace `SECRET_KEY`?

### 7.7. Ejercicio propuesto

**Ejercicio 7.1.** Implementa un contador de visitas por sesión.

**Pista:** incrementa `session["visitas"]`.

---

## 8. Blueprints y modularización

### 8.1. El problema de una app que crece

Cuando una aplicación tiene decenas de rutas, tenerlas todas en un archivo es inmanejable. Los **Blueprints** agrupan rutas, plantillas y estáticos en **módulos independientes**.

**Analogía:** un blueprint es como un **plano parcial** de una casa. En lugar de un plano gigante, tienes el plano de la cocina, el de los baños... y luego los unes.

### 8.2. Definir un blueprint

**`mi_app/usuarios.py`:**

```python
from flask import Blueprint, render_template

bp = Blueprint("usuarios", __name__, url_prefix="/usuarios")


@bp.route("/")
def lista():
    return render_template("usuarios/lista.html")


@bp.route("/<int:user_id>")
def detalle(user_id: int):
    return f"Usuario {user_id}"
```

**Explicación línea por línea:**
- `Blueprint("usuarios", __name__, url_prefix="/usuarios")` crea un blueprint con nombre y prefijo de URL.
- `@bp.route(...)` registra rutas en el blueprint, igual que con `app`.

### 8.3. Registrar el blueprint

**`mi_app/__init__.py`:**

```python
from flask import Flask
from .usuarios import bp as usuarios_bp


def create_app() -> Flask:
    app = Flask(__name__)
    app.register_blueprint(usuarios_bp)
    return app
```

### 8.4. `url_for` con blueprints

```python
url_for("usuarios.detalle", user_id=1)   # /usuarios/1
```

**Explicación línea por línea:**
- El nombre de la vista incluye el nombre del blueprint: `usuarios.detalle`.

### 8.5. Errores comunes

**Error 1 — Olvidar el prefijo** y provocar colisiones de rutas.

**Error 2 — Imports circulares** al registrar blueprints en el módulo equivocado.

**Error 3 — No usar `url_for` con el nombre del blueprint.**

### 8.6. Checkpoint de comprensión

1. ¿Qué agrupa un blueprint?
2. ¿Cómo se construye el nombre de una vista de un blueprint?
3. ¿Qué ventaja aporta `url_prefix`?

### 8.7. Ejercicio propuesto

**Ejercicio 8.1.** Divide la app en dos blueprints: `blog` y `tienda`.

**Pista:** cada uno con su `url_prefix`.

---

## 9. Middleware y hooks

### 9.1. ¿Qué son los hooks?

A veces necesitas ejecutar código **antes** o **después** de **cada** petición: registrar accesos, medir tiempos, abrir/cerrar conexiones. Flask ofrece **hooks**:

| Hook | Cuándo |
|---|---|
| `before_request` | Antes de la vista |
| `after_request` | Después, si no hubo error |
| `teardown_request` | Al final, siempre |

### 9.2. Ejemplo: medir tiempos

```python
from flask import Flask, g, request
import time

app = Flask(__name__)


@app.before_request
def iniciar_temporizador():
    g.inicio = time.perf_counter()


@app.after_request
def añadir_cabecera(response):
    duracion = time.perf_counter() - g.inicio
    response.headers["X-Duracion"] = f"{duracion:.4f}"
    return response


@app.route("/")
def inicio():
    return "Hola"
```

**Explicación línea por línea:**
- `g` es un objeto para datos que duran **solo la petición actual**.
- `before_request` guarda el tiempo de inicio en `g.inicio`.
- `after_request` calcula la duración y añade una **cabecera** a la respuesta.
- **Importante:** `after_request` **debe devolver** la respuesta.

### 9.3. Middleware WSGI

Para procesamiento más global, se puede envolver la aplicación WSGI:

```python
class SimpleMiddleware:
    def __init__(self, app):
        self.app = app

    def __call__(self, environ, start_response):
        environ["mi_cabecera"] = "valor"
        return self.app(environ, start_response)


app.wsgi_app = SimpleMiddleware(app.wsgi_app)
```

**Explicación línea por línea:**
- `__call__` intercepta cada petición WSGI.
- `environ` es el diccionario con la petición.
- Se envuelve `app.wsgi_app`.

### 9.4. Errores comunes

**Error 1 — No devolver la respuesta en `after_request`.**

*Síntoma:* `TypeError: view function did not return a valid response`. *Solución:* `return response`.

**Error 2 — Trabajo pesado en hooks** que afecta a todas las peticiones.

### 9.5. Checkpoint de comprensión

1. ¿Qué diferencia hay entre `before_request` y `after_request`?
2. ¿Para qué sirve el objeto `g`?
3. ¿Qué debe devolver `after_request`?

### 9.6. Ejercicio propuesto

**Ejercicio 9.1.** Registra en un logger la ruta y el método de cada petición con `before_request`.

**Pista:** usa `request.path` y `request.method`.

---

## 10. Manejo de errores

### 10.1. Errores HTTP personalizados

Puedes definir qué mostrar cuando ocurre un error HTTP:

```python
from flask import Flask, jsonify, render_template

app = Flask(__name__)


@app.errorhandler(404)
def no_encontrado(error):
    return render_template("404.html"), 404


@app.errorhandler(500)
def error_interno(error):
    return jsonify({"error": "Error interno"}), 500
```

**Explicación línea por línea:**
- `@app.errorhandler(404)` registra un manejador para el código 404.
- La función devuelve la respuesta y el código de estado.

### 10.2. Manejar excepciones propias

```python
class MiError(Exception):
    """Error de dominio."""


@app.errorhandler(MiError)
def manejar_mi_error(error):
    return jsonify({"error": str(error)}), 400
```

**Explicación línea por línea:**
- Se puede registrar un manejador para una **excepción propia** (archivo 06).
- Cuando la vista lanza `MiError`, este manejador responde con un 400.

### 10.3. Errores comunes

**Error 1 — Devolver el traceback al usuario.**

Expone detalles internos. *Solución:* devuelve un mensaje genérico y registra el detalle en los logs.

**Error 2 — No manejar 404/500** en producción.

### 10.4. Checkpoint de comprensión

1. ¿Cómo registras un manejador para un código HTTP?
2. ¿Puedes manejar excepciones propias con `errorhandler`?
3. ¿Por qué no devolver el traceback al usuario?

### 10.5. Ejercicio propuesto

**Ejercicio 10.1.** Crea un manejador para el error 403 con una plantilla.

**Pista:** `@app.errorhandler(403)`.

---

## 11. Archivos estáticos

### 11.1. Qué son los archivos estáticos

Los **archivos estáticos** (CSS, JavaScript, imágenes) no cambian dinámicamente. Flask los sirve automáticamente desde la carpeta `static/`.

**`static/css/style.css`:**

```css
body { font-family: sans-serif; }
```

**En un template:**

```html
<link rel="stylesheet" href="{{ url_for('static', filename='css/style.css') }}">
```

**Explicación línea por línea:**
- `url_for('static', filename='css/style.css')` genera la URL correcta del archivo.

### 11.2. Errores comunes

**Error 1 — Escribir rutas fijas `/static/...`.**

*Solución:* usa `url_for('static', ...)`.

**Error 2 — Servir archivos subidos por usuarios desde `static/`.**

Es un riesgo de seguridad. *Solución:* usa `send_from_directory` con validación.

### 11.3. Checkpoint de comprensión

1. ¿Dónde busca Flask los archivos estáticos?
2. ¿Cómo referencias un estático desde una plantilla?

### 11.4. Ejercicio propuesto

**Ejercicio 11.1.** Añade un logo `static/img/logo.png` a la plantilla base.

**Pista:** `<img src="{{ url_for('static', filename='img/logo.png') }}">`.

---

## 12. Configuración y variables de entorno

### 12.1. Por qué no hardcodear la configuración

Los datos como claves secretas, cadenas de conexión o rutas **no deben ir en el código**. Deben venir de **variables de entorno** o archivos de configuración, para no subirlos a Git y poder cambiar entre entornos (desarrollo/producción).

### 12.2. Un objeto de configuración

**`config.py`:**

```python
import os


class Config:
    """Configuración base."""

    SECRET_KEY = os.environ.get("SECRET_KEY", "dev")
    SQLALCHEMY_DATABASE_URI = os.environ.get("DATABASE_URL", "sqlite:///app.db")
    DEBUG = False


class DevelopmentConfig(Config):
    DEBUG = True


class ProductionConfig(Config):
    DEBUG = False
```

**Explicación línea por línea:**
- `os.environ.get("SECRET_KEY", "dev")` lee la variable de entorno o usa un valor por defecto.
- Las clases hijas ajustan valores según el entorno.

### 12.3. Cargar un archivo `.env`

```bash
pip install python-dotenv
```

**`.env`:**

```text
SECRET_KEY=super-secreto
DATABASE_URL=sqlite:///app.db
```

```python
from dotenv import load_dotenv

load_dotenv()
app.config.from_object("config.DevelopmentConfig")
```

**Explicación línea por línea:**
- `load_dotenv()` carga las variables del archivo `.env`.
- `.env` **nunca** se sube a Git (va en `.gitignore`).

### 12.4. Errores comunes

**Error 1 — Subir `.env` a Git.**

*Solución:* añádelo a `.gitignore`.

**Error 2 — Hardcodear secretos** en el código.

### 12.5. Checkpoint de comprensión

1. ¿Por qué usar variables de entorno para la configuración?
2. ¿Qué hace `load_dotenv()`?
3. ¿Por qué `.env` no va en Git?

### 12.6. Ejercicio propuesto

**Ejercicio 12.1.** Crea configuraciones de desarrollo, testing y producción.

**Pista:** usa herencia de clases.

---

## 13. Extensiones populares

### 13.1. El ecosistema de Flask

Flask crece mediante **extensiones**. Las más importantes:

| Extensión | Función |
|---|---|
| **Flask-SQLAlchemy** | ORM (archivo 10) |
| **Flask-Migrate** | Migraciones con Alembic |
| **Flask-Login** | Autenticación de usuarios |
| **Flask-WTF** | Formularios y CSRF |
| **Flask-Mail** | Envío de correos |
| **Flask-Caching** | Caché |
| **Flask-CORS** | Cabeceras CORS |

### 13.2. Ejemplo: Flask-Login

```python
from flask import Flask, redirect, url_for
from flask_login import LoginManager, UserMixin, login_user, login_required

app = Flask(__name__)
app.config["SECRET_KEY"] = "secreto"
login_manager = LoginManager(app)


class Usuario(UserMixin):
    def __init__(self, id: int, nombre: str) -> None:
        self.id = id
        self.nombre = nombre


@login_manager.user_loader
def cargar_usuario(user_id: str):
    return Usuario(int(user_id), "Ana")


@app.route("/privado")
@login_required
def privado():
    return "Solo para usuarios autenticados"
```

**Explicación línea por línea:**
- `UserMixin` aporta los métodos que Flask-Login espera.
- `@login_manager.user_loader` indica cómo cargar un usuario a partir de su id.
- `@login_required` protege la vista: redirige al login si no hay sesión.

### 13.3. Errores comunes

**Error 1 — Olvidar inicializar la extensión.**

*Solución:* `ext.init_app(app)` (patrón de fábrica).

**Error 2 — Mezclar versiones incompatibles.**

*Solución:* revisa la compatibilidad de las extensiones.

### 13.4. Checkpoint de comprensión

1. ¿Qué añade Flask-Login?
2. ¿Para qué sirve `@login_required`?
3. ¿Por qué las extensiones usan `init_app`?

### 13.5. Ejercicio propuesto

**Ejercicio 13.1.** Añade `Flask-Caching` a una ruta y mide la mejora.

**Pista:** usa `@cache.cached(timeout=60)`.

---

## 14. Testing en Flask

### 14.1. El cliente de pruebas

Flask incluye un **cliente de pruebas** que simula peticiones **sin levantar** el servidor:

```python
import pytest
from app import create_app


@pytest.fixture
def cliente():
    app = create_app()
    app.config["TESTING"] = True
    return app.test_client()


def test_inicio(cliente):
    respuesta = cliente.get("/")
    assert respuesta.status_code == 200


def test_crear_tarea(cliente):
    respuesta = cliente.post("/tarea", json={"titulo": "Comprar"})
    assert respuesta.status_code == 201
    assert respuesta.get_json()["creada"]["titulo"] == "Comprar"
```

Ejecución:

```bash
pytest -v
```

**Explicación línea por línea:**
- `app.test_client()` crea un cliente que simula un navegador.
- `cliente.get("/")` hace una petición GET.
- `respuesta.status_code` y `respuesta.get_json()` permiten verificar.
- El `fixture` crea una app nueva por test.

### 14.2. Errores comunes

**Error 1 — Probar contra la base de datos de producción.**

*Solución:* usa una BD de test (en memoria).

**Error 2 — No activar `TESTING=True`.**

Oculta errores y cambia el comportamiento.

### 14.3. Checkpoint de comprensión

1. ¿Qué hace `app.test_client()`?
2. ¿Por qué usar una BD de test?
3. ¿Qué ventaja tiene usar fixtures?

### 14.4. Ejercicio propuesto

**Ejercicio 14.1.** Escribe tests para una ruta que devuelve 404 con un ID inexistente.

**Pista:** `assert respuesta.status_code == 404`.

---

## 15. Despliegue

### 15.1. Desarrollo vs. producción

El servidor de desarrollo (`app.run`) **no** es para producción. En producción se usa un **servidor WSGI** (Gunicorn/uWSGI) detrás de un **proxy inverso** (Nginx).

```text
Navegador → Nginx (proxy inverso, estáticos) → Gunicorn (app Flask) → Base de datos
```

### 15.2. Gunicorn

```bash
pip install gunicorn
gunicorn --workers 4 --bind 0.0.0.0:8000 "mi_app:create_app()"
```

**Explicación línea por línea:**
- `--workers 4` lanza 4 procesos trabajadores (aprovecha varios núcleos).
- `"mi_app:create_app()"` llama a la fábrica para obtener la app.

### 15.3. Nginx como proxy inverso

```nginx
server {
    listen 80;
    server_name ejemplo.com;

    location / {
        proxy_pass http://127.0.0.1:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }

    location /static/ {
        alias /var/www/mi_app/static/;
    }
}
```

**Explicación línea por línea:**
- `proxy_pass` redirige las peticiones dinámicas a Gunicorn.
- `location /static/` sirve los archivos estáticos directamente (mucho más eficiente).

### 15.4. Docker

```dockerfile
FROM python:3.12-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
CMD ["gunicorn", "--bind", "0.0.0.0:8000", "mi_app:create_app()"]
```

### 15.5. Errores comunes

**Error 1 — Usar `flask run` en producción.**

**Error 2 — No servir estáticos con Nginx**, dejando a Gunicorn esa carga.

**Error 3 — No usar HTTPS.**

### 15.6. Checkpoint de comprensión

1. ¿Por qué no usar `app.run()` en producción?
2. ¿Qué papel juega Nginx?
3. ¿Por qué se sirven los estáticos con Nginx y no con Gunicorn?

### 15.7. Ejercicio propuesto

**Ejercicio 15.1.** Dockeriza la app y ejecútala con Gunicorn.

**Pista:** expón el puerto 8000 y prueba con `curl`.

---

## 16. API REST con Flask

### 16.1. Qué es una API REST

Una **API REST** expone **recursos** accesibles por URLs, usando los **métodos HTTP** para las operaciones (GET, POST, PUT, DELETE) y devolviendo **JSON**.

### 16.2. Un CRUD completo

```python
from flask import Flask, jsonify, request

app = Flask(__name__)

tareas: dict[int, dict] = {}
siguiente_id = 1


@app.get("/api/tareas")
def listar():
    return jsonify(list(tareas.values()))


@app.post("/api/tareas")
def crear():
    global siguiente_id
    datos = request.get_json()
    tarea = {"id": siguiente_id, "titulo": datos["titulo"], "hecha": False}
    tareas[siguiente_id] = tarea
    siguiente_id += 1
    return jsonify(tarea), 201


@app.put("/api/tareas/<int:tarea_id>")
def actualizar(tarea_id: int):
    tarea = tareas.get(tarea_id)
    if not tarea:
        return jsonify({"error": "No encontrada"}), 404
    tarea.update(request.get_json())
    return jsonify(tarea)


@app.delete("/api/tareas/<int:tarea_id>")
def eliminar(tarea_id: int):
    if tareas.pop(tarea_id, None) is None:
        return jsonify({"error": "No encontrada"}), 404
    return "", 204
```

**Explicación línea por línea:**
- `@app.get`, `@app.post`, `@app.put`, `@app.delete` son atajos para `methods=[...]`.
- `jsonify(...)` convierte un objeto Python en una respuesta JSON.
- `tareas.pop(tarea_id, None)` elimina y devuelve el valor, o `None` si no existe.
- Los códigos de estado (201, 404, 204) comunican el resultado.

### 16.3. Probar con curl

```bash
curl -X POST http://127.0.0.1:5000/api/tareas \
     -H "Content-Type: application/json" \
     -d '{"titulo": "Comprar pan"}'
```

```json
{"id": 1, "titulo": "Comprar pan", "hecha": false}
```

### 16.4. Alternativas para APIs grandes

Para APIs grandes, considera extensiones que aportan validación, serialización y documentación OpenAPI: **Flask-RESTful**, **Flask-Smorest**, **Flask-RESTX**.

### 16.5. Errores comunes

**Error 1 — No devolver códigos de estado correctos.**

**Error 2 — No validar el cuerpo de la petición.**

**Error 3 — Exponer datos internos** sin filtrar.

### 16.6. Checkpoint de comprensión

1. ¿Qué método HTTP corresponde a crear? ¿Y a actualizar?
2. ¿Qué hace `jsonify`?
3. ¿Por qué validar el cuerpo de la petición?

### 16.7. Ejercicio propuesto

**Ejercicio 16.1.** Añade validación: rechaza tareas sin `titulo` con un 400.

**Pista:** comprueba `"titulo" in datos`.

---

## 17. Buenas prácticas

1. **Usa la fábrica `create_app()`** y blueprints.
2. **Configura secretos por variables de entorno.**
3. **Valida en el servidor**, nunca confíes en el cliente.
4. **Usa `url_for`** para todas las URLs.
5. **Sirve estáticos con Nginx** en producción.
6. **Usa Gunicorn/uWSGI** detrás de un proxy.
7. **Escribe tests** con el cliente de pruebas.
8. **Activa CSRF** con Flask-WTF.
9. **Separa presentación, lógica y datos.**
10. **No guardes secretos en la sesión.**

---

## 18. Recursos

- **Documentación oficial:** <https://flask.palletsprojects.com/>
- **Jinja2:** <https://jinja.palletsprojects.com/>
- **Flask-WTF:** <https://flask-wtf.readthedocs.io/>
- **Flask-Login:** <https://flask-login.readthedocs.io/>
- **Gunicorn:** <https://gunicorn.org/>
- **Libro:** *Flask Web Development*, Miguel Grinberg.

---

## 19. Mini resumen final

- Flask es un **microframework** minimalista y extensible.
- Las **rutas** asocian URLs a vistas; `url_for` genera URLs.
- Los **métodos HTTP** definen operaciones; los códigos de estado, el resultado.
- **Jinja2** genera HTML con herencia de plantillas.
- **Flask-WTF** valida formularios y protege contra CSRF.
- **Sesiones** y **cookies** mantienen estado.
- Los **blueprints** modularizan; los **hooks** interceptan peticiones.
- **Gunicorn + Nginx** es el despliegue típico.
- Las **APIs REST** devuelven JSON y usan los métodos HTTP.

---

### 🎯 Retos opcionales

1. **Reto 1:** Añade autenticación con Flask-Login y una ruta protegida.
2. **Reto 2:** Integra Flask-SQLAlchemy (archivo 10) para persistir las tareas.
3. **Reto 3:** Documenta la API con `flask-smorest` y OpenAPI.

**Anterior:** [08_python_avanzado.md](08_python_avanzado.md) · **Siguiente:** [10_flask_sqlalchemy.md](10_flask_sqlalchemy.md)
