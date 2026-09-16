# 11 · Django y su ORM

**Versión recomendada:** Django 5.0 · Python 3.12
**Requisito:** [10_flask_sqlalchemy.md](10_flask_sqlalchemy.md)
**Nivel:** Intermedio → Avanzado
**Duración estimada de estudio:** 20–30 horas

---

## Índice de contenidos

1. [Introducción a Django](#1-introducción-a-django)
2. [Instalación y estructura de proyecto](#2-instalación-y-estructura-de-proyecto)
3. [Patrón MVT](#3-patrón-mvt-model-view-template)
4. [Configuración de settings.py](#4-configuración-de-settingspy)
5. [URLs y vistas](#5-urls-y-vistas)
6. [Templates de Django](#6-templates-de-django)
7. [Formularios y validación](#7-formularios-y-validación)
8. [Django ORM: modelos](#8-django-orm-modelos)
9. [Tipos de campos y opciones](#9-tipos-de-campos-y-opciones)
10. [Relaciones](#10-relaciones-foreignkey-onetoone-manytomany)
11. [Consultas con QuerySet](#11-consultas-con-queryset)
12. [F expressions y Q objects](#12-f-expressions-y-q-objects)
13. [Aggregation y annotation](#13-aggregation-y-annotation)
14. [Managers personalizados](#14-managers-personalizados)
15. [Migraciones](#15-migraciones)
16. [Admin de Django](#16-admin-de-django)
17. [Autenticación y autorización](#17-autenticación-y-autorización)
18. [Middleware](#18-middleware)
19. [Signals](#19-signals)
20. [Django REST Framework](#20-django-rest-framework)
21. [Django Channels](#21-django-channels-websockets)
22. [Caching](#22-caching)
23. [Internacionalización](#23-internacionalización)
24. [Testing](#24-testing-en-django)
25. [Despliegue](#25-despliegue-en-producción)
26. [Buenas prácticas](#26-buenas-prácticas)
27. [Recursos](#27-recursos)
28. [Mini resumen final](#28-mini-resumen-final)

---

## 1. Introducción a Django

### 1.1. ¿Qué es Django?

**Django** es un framework web **"todo incluido"**: trae ORM, panel de administración, autenticación, formularios, plantillas, seguridad y más. Su lema: *"The web framework for perfectionists with deadlines"* (el framework web para perfeccionistas con plazos).

Mientras Flask te da las piezas y tú las montas, Django te da la casa casi construida. A cambio, espera que sigas sus convenciones.

### 1.2. La filosofía de Django

- **DRY** (*Don't Repeat Yourself*): no repitas código.
- **Convención sobre configuración:** sigue las convenciones y escribes menos.
- **Baterías incluidas:** casi todo lo que necesitas ya está.
- **Seguro por defecto:** protección contra CSRF, XSS, inyección SQL desde el inicio.

### 1.3. ¿Cuándo usar Django?

| Escenario | ¿Django? |
|---|---|
| App con muchos datos y relaciones | Sí |
| Necesitas panel de administración | Sí |
| Equipo que valora estructura y convenciones | Sí |
| API mínima y rápida | Mejor Flask/FastAPI |
| Microservicio pequeño | Mejor Flask/FastAPI |

### 1.4. Errores comunes

**Error 1 — Usar Django para una API mínima.**

*Solución:* considera Flask o FastAPI si no necesitas ORM ni admin.

**Error 2 — Luchar contra las convenciones.**

Django premia seguir su estructura. Si te peleas con él, probablemente estés forzando el patrón equivocado.

### 1.5. Checkpoint de comprensión

1. ¿Qué diferencia a Django de Flask?
2. ¿Qué significa "baterías incluidas"?
3. ¿Cuándo elegirías Django sobre FastAPI?

### 1.6. Ejercicio propuesto

**Ejercicio 1.1.** Enumera tres proyectos donde Django sería la mejor opción y tres donde no.

**Pista:** piensa en la necesidad de admin, ORM y estructura.

---

## 2. Instalación y estructura de proyecto

### 2.1. Instalación

```bash
python -m venv .venv
source .venv/bin/activate
pip install django
django-admin --version
```

Salida esperada:

```text
5.0.6
```

### 2.2. Crear proyecto y app

```bash
django-admin startproject config .
python manage.py startapp blog
```

**Explicación línea por línea:**
- `startproject config .` crea el **proyecto** llamado `config` en la carpeta actual (el `.` evita crear una carpeta anidada).
- `startapp blog` crea una **app** llamada `blog`.

**Proyecto vs. app:** un **proyecto** es el sitio completo; una **app** es un módulo con una responsabilidad (un blog, una tienda...). Un proyecto contiene varias apps.

### 2.3. Estructura

```text
proyecto/
├── manage.py
├── config/                 # configuración del proyecto
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
├── blog/                   # una app
│   ├── models.py
│   ├── views.py
│   ├── urls.py
│   ├── admin.py
│   ├── apps.py
│   └── migrations/
├── templates/
├── static/
└── db.sqlite3
```

### 2.4. Registrar la app

**`config/settings.py`:**

```python
INSTALLED_APPS = [
    "django.contrib.admin",
    "django.contrib.auth",
    "django.contrib.contenttypes",
    "django.contrib.sessions",
    "django.contrib.messages",
    "django.contrib.staticfiles",
    "blog",   # tu app
]
```

**Explicación línea por línea:**
- Las primeras apps son de Django (admin, auth...).
- `"blog"` es tu app; hay que registrarla para que Django la reconozca.

### 2.5. Primer arranque

```bash
python manage.py migrate
python manage.py runserver
```

Salida esperada:

```text
Starting development server at http://127.0.0.1:8000/
```

**Explicación línea por línea:**
- `migrate` crea las tablas de las apps integradas (usuarios, sesiones...).
- `runserver` levanta el servidor de desarrollo.

### 2.6. Errores comunes

**Error 1 — `startproject` dentro de una carpeta con el mismo nombre.**

*Síntoma:* estructura doble (`proyecto/proyecto/`). *Solución:* usa `startproject config .`.

**Error 2 — Olvidar registrar la app** en `INSTALLED_APPS`.

### 2.7. Checkpoint de comprensión

1. ¿Qué diferencia hay entre un proyecto y una app?
2. ¿Para qué sirve `INSTALLED_APPS`?
3. ¿Qué hace `migrate`?

### 2.8. Ejercicio propuesto

**Ejercicio 2.1.** Crea el proyecto y una app `tienda`, y regístrala.

**Pista:** usa `startproject config .` para evitar anidamiento.

---

## 3. Patrón MVT (Model-View-Template)

### 3.1. Qué es MVT

Django organiza el código en tres capas:

- **Model:** los datos y su acceso (ORM).
- **View:** la lógica de la petición; devuelve una respuesta.
- **Template:** la presentación HTML.

Es una variante del clásico MVC, donde Django hace de "controlador" enrutando las URLs a las vistas.

### 3.2. El flujo de una petición

```text
Navegador → urls.py → view → model (ORM) → template → respuesta HTML
```

**Explicación línea por línea:**
1. El navegador pide una URL.
2. `urls.py` decide qué vista la atiende.
3. La vista consulta el modelo (base de datos).
4. La vista pasa los datos a una plantilla.
5. La plantilla genera el HTML.
6. Django devuelve el HTML al navegador.

### 3.3. Errores comunes

**Error 1 — Poner lógica de negocio en las vistas.**

*Solución:* muévela a modelos o a un módulo de servicios.

**Error 2 — Consultar la base de datos en la plantilla.**

*Solución:* prepara los datos en la vista.

### 3.4. Checkpoint de comprensión

1. ¿Qué representa cada letra de MVT?
2. ¿Qué papel juega `urls.py`?

### 3.5. Ejercicio propuesto

**Ejercicio 3.1.** Dibuja el flujo MVT para una página de detalle de producto.

**Pista:** identifica URL, vista, modelo y template.

---

## 4. Configuración de settings.py

### 4.1. El centro de configuración

`settings.py` centraliza la configuración del proyecto: apps, base de datos, plantillas, estáticos, idioma, seguridad.

### 4.2. Configuración esencial

```python
from pathlib import Path
import os

BASE_DIR = Path(__file__).resolve().parent.parent

SECRET_KEY = os.environ.get("DJANGO_SECRET_KEY", "dev-insegura")
DEBUG = os.environ.get("DJANGO_DEBUG", "True") == "True"
ALLOWED_HOSTS = ["localhost", "127.0.0.1"]

DATABASES = {
    "default": {
        "ENGINE": "django.db.backends.sqlite3",
        "NAME": BASE_DIR / "db.sqlite3",
    }
}

TEMPLATES = [
    {
        "BACKEND": "django.template.backends.django.DjangoTemplates",
        "DIRS": [BASE_DIR / "templates"],
        "APP_DIRS": True,
        "OPTIONS": {
            "context_processors": [
                "django.template.context_processors.request",
                "django.contrib.auth.context_processors.auth",
            ],
        },
    },
]

LANGUAGE_CODE = "es-es"
TIME_ZONE = "Europe/Madrid"
USE_I18N = True
USE_TZ = True
STATIC_URL = "static/"
DEFAULT_AUTO_FIELD = "django.db.models.BigAutoField"
```

**Explicación línea por línea:**
- `BASE_DIR` es la raíz del proyecto; se usa para construir rutas absolutas.
- `SECRET_KEY` viene de una variable de entorno (nunca hardcodeada en producción).
- `DEBUG` controla el modo de depuración; **debe ser `False` en producción**.
- `ALLOWED_HOSTS` lista los dominios permitidos (seguridad).
- `DATABASES` define la conexión; por defecto SQLite.
- `TEMPLATES` configura el motor de plantillas y sus carpetas.
- `LANGUAGE_CODE`, `TIME_ZONE`, `USE_I18N` configuran idioma y zona horaria.
- `DEFAULT_AUTO_FIELD` define el tipo de clave primaria automática.

### 4.3. PostgreSQL

```python
DATABASES = {
    "default": {
        "ENGINE": "django.db.backends.postgresql",
        "NAME": "app",
        "USER": "app",
        "PASSWORD": os.environ["DB_PASSWORD"],
        "HOST": "localhost",
        "PORT": "5432",
    }
}
```

### 4.4. Errores comunes

**Error 1 — `DEBUG=True` en producción.**

Expone información sensible (código, configuración, trazas). *Solución:* `DEBUG=False`.

**Error 2 — `SECRET_KEY` hardcodeada** o subida a Git.

### 4.5. Checkpoint de comprensión

1. ¿Por qué `DEBUG` debe ser `False` en producción?
2. ¿Para qué sirve `ALLOWED_HOSTS`?
3. ¿De dónde debe venir `SECRET_KEY`?

### 4.6. Ejercicio propuesto

**Ejercicio 4.1.** Configura la base de datos y el idioma desde variables de entorno.

**Pista:** usa `os.environ.get`.

---

## 5. URLs y vistas

### 5.1. Cómo se enrutan las peticiones

`urls.py` mapea rutas a vistas. Las apps incluyen sus propias URLs con `include`, manteniendo el proyecto modular.

### 5.2. URLs del proyecto

**`config/urls.py`:**

```python
from django.contrib import admin
from django.urls import include, path

urlpatterns = [
    path("admin/", admin.site.urls),
    path("blog/", include("blog.urls")),
]
```

**Explicación línea por línea:**
- `path("admin/", admin.site.urls)` monta el panel de administración.
- `path("blog/", include("blog.urls"))` incluye las URLs de la app `blog` bajo el prefijo `/blog/`.

### 5.3. URLs de la app

**`blog/urls.py`:**

```python
from django.urls import path
from . import views

app_name = "blog"

urlpatterns = [
    path("", views.lista_posts, name="lista"),
    path("<int:post_id>/", views.detalle_post, name="detalle"),
]
```

**Explicación línea por línea:**
- `app_name = "blog"` da un **espacio de nombres** a la app, evitando colisiones.
- `path("", views.lista_posts, name="lista")` asocia la URL raíz de la app a la vista.
- `<int:post_id>` captura un entero de la URL.
- `name="detalle"` permite referirse a esta URL por nombre.

### 5.4. Vistas por función

```python
from django.http import HttpResponse
from django.shortcuts import get_object_or_404, render
from .models import Post


def lista_posts(request):
    """Lista todos los posts publicados."""
    posts = Post.objects.filter(publicado=True)
    return render(request, "blog/lista.html", {"posts": posts})


def detalle_post(request, post_id: int):
    """Muestra un post concreto."""
    post = get_object_or_404(Post, id=post_id)
    return render(request, "blog/detalle.html", {"post": post})
```

**Explicación línea por línea:**
- Toda vista recibe `request` como primer argumento.
- `Post.objects.filter(...)` consulta la base (sección 11).
- `render(request, plantilla, contexto)` genera la respuesta HTML.
- `get_object_or_404` lanza un 404 si el objeto no existe.

### 5.5. Vistas basadas en clases (CBV)

```python
from django.views.generic import DetailView, ListView
from .models import Post


class PostListView(ListView):
    model = Post
    template_name = "blog/lista.html"
    context_object_name = "posts"
    queryset = Post.objects.filter(publicado=True)


class PostDetailView(DetailView):
    model = Post
    template_name = "blog/detalle.html"
```

**Explicación línea por línea:**
- `ListView` genera automáticamente una lista de objetos.
- `DetailView` muestra un objeto por su clave.
- Las CBV reducen código repetitivo para patrones comunes.

### 5.6. Generar URLs con `reverse` y `{% url %}`

```python
from django.urls import reverse

url = reverse("blog:detalle", args=[1])   # /blog/1/
```

```html
<a href="{% url 'blog:detalle' post.id %}">{{ post.titulo }}</a>
```

**Explicación línea por línea:**
- `reverse("blog:detalle", args=[1])` genera la URL por el nombre `blog:detalle`.
- En plantillas se usa `{% url %}`.

**¿Por qué no escribir URLs a mano?** Si cambias una ruta, todas las URLs se actualizan solas.

### 5.7. Errores comunes

**Error 1 — Colisión de nombres de URL** entre apps. *Solución:* usa `app_name`.

**Error 2 — Escribir rutas a mano** en las plantillas. *Solución:* usa `{% url %}`.

### 5.8. Checkpoint de comprensión

1. ¿Qué hace `include`?
2. ¿Para qué sirve `app_name`?
3. ¿Qué diferencia hay entre una vista por función y una CBV?

### 5.9. Ejercicio propuesto

**Ejercicio 5.1.** Crea una URL `/blog/<slug:slug>/` y su vista de detalle.

**Pista:** `get_object_or_404(Post, slug=slug)`.

---

## 6. Templates de Django

### 6.1. El lenguaje de plantillas

Django usa `{{ variable }}` para mostrar y `{% tag %}` para lógica. Soporta **herencia** con `{% extends %}`.

### 6.2. Template base

**`templates/base.html`:**

```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <title>{% block title %}Mi Sitio{% endblock %}</title>
</head>
<body>
    <nav><a href="{% url 'blog:lista' %}">Blog</a></nav>
    <main>{% block content %}{% endblock %}</main>
</body>
</html>
```

**`blog/templates/blog/lista.html`:**

```html
{% extends "base.html" %}

{% block title %}Posts{% endblock %}

{% block content %}
<h1>Posts</h1>
<ul>
    {% for post in posts %}
        <li>
            <a href="{% url 'blog:detalle' post.id %}">{{ post.titulo }}</a>
            <small>{{ post.creado_en|date:"d/m/Y" }}</small>
        </li>
    {% empty %}
        <li>No hay posts.</li>
    {% endfor %}
</ul>
{% endblock %}
```

**Explicación línea por línea:**
- `{% extends "base.html" %}` hereda del template base.
- `{% block content %}...{% endblock %}` rellena la sección definida en base.
- `{% for post in posts %}` itera.
- `{% empty %}` muestra algo si la lista está vacía.
- `{{ post.creado_en|date:"d/m/Y" }}` formatea la fecha con un filtro.

### 6.3. Filtros comunes

```html
{{ texto|upper }}
{{ precio|floatformat:2 }}
{{ fecha|date:"d/m/Y H:i" }}
{{ lista|length }}
{{ valor|default:"sin valor" }}
```

**Explicación línea por línea:**
- `|upper` mayúsculas; `|floatformat:2` dos decimales; `|date` formatea fecha; `|length` cuenta; `|default` valor por defecto.

### 6.4. Errores comunes

**Error 1 — Olvidar `{% csrf_token %}`** en formularios POST.

```text
403 Forbidden: CSRF verification failed
```

**Error 2 — Depender de `|safe`** con datos del usuario (riesgo de XSS). Django escapa por defecto; no lo desactives.

### 6.5. Checkpoint de comprensión

1. ¿Qué hace `{% extends %}`?
2. ¿Para qué sirve `{% empty %}`?
3. ¿Por qué Django escapa el HTML por defecto?

### 6.6. Ejercicio propuesto

**Ejercicio 6.1.** Crea un template que muestre el número total de posts con `{{ posts|length }}`.

**Pista:** usa el filtro `length`.

---

## 7. Formularios y validación

### 7.1. Por qué formularios de Django

Validar datos a mano es repetitivo y propenso a errores. Los **formularios de Django** validan, generan HTML y se integran con los modelos (`ModelForm`).

### 7.2. `forms.Form`

```python
from django import forms


class ContactoForm(forms.Form):
    """Formulario de contacto."""

    nombre = forms.CharField(max_length=80)
    email = forms.EmailField()
    mensaje = forms.CharField(widget=forms.Textarea)

    def clean_nombre(self):
        nombre = self.cleaned_data["nombre"]
        if len(nombre) < 3:
            raise forms.ValidationError("El nombre es demasiado corto")
        return nombre
```

**Explicación línea por línea:**
- Cada campo declara su tipo y reglas (`max_length`, `EmailField`...).
- `clean_nombre` es un método de **validación personalizada**: se llama tras la validación básica.
- `self.cleaned_data` contiene los datos ya validados.

### 7.3. `ModelForm`

```python
from django import forms
from .models import Post


class PostForm(forms.ModelForm):
    class Meta:
        model = Post
        fields = ["titulo", "contenido", "publicado"]
```

**Explicación línea por línea:**
- `ModelForm` genera el formulario **a partir del modelo**, con sus campos.

### 7.4. Usar el formulario en la vista

```python
from django.shortcuts import redirect, render
from .forms import PostForm


def crear_post(request):
    """Crea un nuevo post."""
    if request.method == "POST":
        form = PostForm(request.POST)
        if form.is_valid():
            form.save()
            return redirect("blog:lista")
    else:
        form = PostForm()
    return render(request, "blog/crear.html", {"form": form})
```

**Explicación línea por línea:**
- Si es POST, se rellena el formulario con los datos.
- `form.is_valid()` valida; `form.save()` guarda en la base.
- Si es GET, se muestra un formulario vacío.

### 7.5. Errores comunes

**Error 1 — No validar en el servidor.** `form.is_valid()` es obligatorio.

**Error 2 — Olvidar `{% csrf_token %}`** → error 403.

### 7.6. Checkpoint de comprensión

1. ¿Qué valida `is_valid()`?
2. ¿Qué diferencia hay entre `Form` y `ModelForm`?
3. ¿Para qué sirve `clean_<campo>`?

### 7.7. Ejercicio propuesto

**Ejercicio 7.1.** Añade una validación que impida títulos de menos de 5 caracteres.

**Pista:** define `clean_titulo`.

---

## 8. Django ORM: modelos

### 8.1. Clases que son tablas

Cada modelo hereda de `models.Model`. Cada atributo es una columna. Django crea la tabla a partir del modelo.

### 8.2. Un modelo completo

```python
from django.db import models
from django.utils import timezone


class Post(models.Model):
    """Entrada de blog."""

    titulo = models.CharField(max_length=200)
    contenido = models.TextField()
    publicado = models.BooleanField(default=False)
    creado_en = models.DateTimeField(default=timezone.now)
    actualizado_en = models.DateTimeField(auto_now=True)

    class Meta:
        ordering = ["-creado_en"]
        verbose_name = "entrada"
        verbose_name_plural = "entradas"

    def __str__(self) -> str:
        return self.titulo
```

**Explicación línea por línea:**
- `models.CharField(max_length=200)` es texto corto (obligatorio `max_length`).
- `models.TextField()` es texto largo.
- `default=timezone.now` asigna la fecha al crear.
- `auto_now=True` actualiza la fecha en cada guardado.
- `class Meta` configura orden por defecto (`-` significa descendente), nombres legibles.
- `__str__` mejora la representación en el admin y la consola.

### 8.3. Errores comunes

**Error 1 — Olvidar `__str__`.**

El admin muestra "Post object (1)".

**Error 2 — No definir `ordering`** y depender del orden arbitrario de la base.

### 8.4. Checkpoint de comprensión

1. ¿Cómo se mapea una clase a una tabla?
2. ¿Para qué sirve `class Meta`?
3. ¿Qué hace `auto_now`?

### 8.5. Ejercicio propuesto

**Ejercicio 8.1.** Crea un modelo `Categoria` con `nombre` y `slug`.

**Pista:** usa `SlugField`.

---

## 9. Tipos de campos y opciones

### 9.1. Campos comunes

| Campo | Uso |
|---|---|
| `CharField` | Texto corto (requiere `max_length`) |
| `TextField` | Texto largo |
| `IntegerField` | Entero |
| `DecimalField` | Decimal (dinero) |
| `BooleanField` | Booleano |
| `DateTimeField` | Fecha y hora |
| `EmailField` | Email validado |
| `SlugField` | URL amigable |
| `ImageField` / `FileField` | Archivos |
| `JSONField` | JSON |

### 9.2. Opciones de campo

```python
precio = models.DecimalField(max_digits=8, decimal_places=2, default=0)
slug = models.SlugField(unique=True, db_index=True)
email = models.EmailField(unique=True, null=False, blank=False)
```

**Explicación línea por línea:**
- `null=True` permite `NULL` en la base de datos.
- `blank=True` permite vacío en los formularios.
- `unique=True` impide duplicados.
- `db_index=True` crea un índice.

**Distinción clave `null` vs `blank`:**
- `null` es a nivel de **base de datos**.
- `blank` es a nivel de **validación de formularios**.

### 9.3. `choices`

```python
class Pedido(models.Model):
    ESTADOS = [
        ("pend", "Pendiente"),
        ("env", "Enviado"),
        ("ent", "Entregado"),
    ]
    estado = models.CharField(max_length=4, choices=ESTADOS, default="pend")
```

**Explicación línea por línea:**
- `choices` limita los valores posibles y ofrece una etiqueta legible.
- `default="pend"` establece el estado inicial.

### 9.4. Errores comunes

**Error 1 — Usar `FloatField` para dinero.** Usa `DecimalField`.

**Error 2 — Confundir `null` con `blank`.**

**Error 3 — `CharField` sin `max_length`** → error de migración.

### 9.5. Checkpoint de comprensión

1. ¿Qué diferencia hay entre `null` y `blank`?
2. ¿Por qué `DecimalField` para dinero?
3. ¿Para qué sirve `choices`?

### 9.6. Ejercicio propuesto

**Ejercicio 9.1.** Añade un campo `precio` con `DecimalField` y un `estado` con `choices`.

**Pista:** define la lista de tuplas.

---

## 10. Relaciones: ForeignKey, OneToOne, ManyToMany

### 10.1. Los tres tipos

| Relación | Campo |
|---|---|
| Muchos a uno | `ForeignKey` |
| Uno a uno | `OneToOneField` |
| Muchos a muchos | `ManyToManyField` |

### 10.2. Ejemplo completo

```python
class Autor(models.Model):
    nombre = models.CharField(max_length=80)

    def __str__(self) -> str:
        return self.nombre


class Libro(models.Model):
    titulo = models.CharField(max_length=120)
    autor = models.ForeignKey(Autor, on_delete=models.CASCADE, related_name="libros")
    categorias = models.ManyToManyField("Categoria", related_name="libros")


class Perfil(models.Model):
    usuario = models.OneToOneField("auth.User", on_delete=models.CASCADE)
    biografia = models.TextField(blank=True)
```

**Explicación línea por línea:**
- `models.ForeignKey(Autor, on_delete=models.CASCADE)` crea la relación muchos a uno: muchos libros pertenecen a un autor.
- `related_name="libros"` permite `autor.libros.all()`.
- `models.ManyToManyField(...)` crea la relación muchos a muchos (Django gestiona la tabla intermedia).
- `models.OneToOneField(...)` crea la relación uno a uno.

### 10.3. `on_delete`

Cuando se borra el objeto referenciado, ¿qué pasa con los relacionados?

- `CASCADE`: se borran también.
- `PROTECT`: impide el borrado.
- `SET_NULL`: pone `NULL` (requiere `null=True`).
- `SET_DEFAULT`: pone el valor por defecto.

**`on_delete` es obligatorio** en Django moderno.

### 10.4. Navegar las relaciones

```python
autor = Autor.objects.get(nombre="Borges")
print(autor.libros.all())          # related_name

libro = Libro.objects.first()
print(libro.autor.nombre)          # acceso directo al padre
```

### 10.5. Errores comunes

**Error 1 — Olvidar `on_delete`.** Es obligatorio.

**Error 2 — Problema N+1.**

*Solución:* usa `select_related` y `prefetch_related` (sección 11).

### 10.6. Checkpoint de comprensión

1. ¿Dónde va la `ForeignKey`?
2. ¿Para qué sirve `related_name`?
3. ¿Qué diferencia hay entre `CASCADE` y `PROTECT`?

### 10.7. Ejercicio propuesto

**Ejercicio 10.1.** Modela `Categoria` y relaciónala con `Post` como muchos a muchos.

**Pista:** `models.ManyToManyField`.

---

## 11. Consultas con QuerySet

### 11.1. Qué es un QuerySet

Un **QuerySet** es una colección **perezosa** (*lazy*) de objetos de la base. Se construye encadenando métodos y **solo se ejecuta** al iterar sobre él. Esto permite construir consultas complejas sin tocar la base hasta el final.

### 11.2. Filtros básicos

```python
Post.objects.all()
Post.objects.filter(publicado=True)
Post.objects.exclude(publicado=False)
Post.objects.get(id=1)
Post.objects.filter(titulo__icontains="python")
Post.objects.filter(creado_en__year=2026)
Post.objects.order_by("-creado_en")[:5]
```

**Explicación línea por línea:**
- `.all()` devuelve todos.
- `.filter(...)` filtra; `.exclude(...)` excluye.
- `.get(...)` devuelve **un** objeto (lanza error si no existe o hay varios).
- `titulo__icontains="python"` busca texto que contenga "python" (sin distinguir mayúsculas).
- `.order_by("-creado_en")[:5]` ordena y toma los 5 primeros.

### 11.3. Lookups (los `__`)

| Lookup | Significado |
|---|---|
| `__exact` | Igual (por defecto) |
| `__icontains` | Contiene (sin distinguir mayúsculas) |
| `__gt`, `__gte`, `__lt`, `__lte` | Comparaciones |
| `__in` | En una lista |
| `__isnull` | Es nulo |
| `__startswith` | Empieza por |

```python
Post.objects.filter(id__in=[1, 2, 3])
Post.objects.filter(contenido__isnull=True)
Post.objects.filter(titulo__startswith="Cómo")
```

### 11.4. Optimización: `select_related` y `prefetch_related`

```python
# select_related: para ForeignKey / OneToOne (usa JOIN)
Libro.objects.select_related("autor").all()

# prefetch_related: para ManyToMany / relaciones inversas (consulta aparte)
Autor.objects.prefetch_related("libros").all()
```

**Explicación línea por línea:**
- `select_related` hace un **JOIN** y trae los objetos relacionados en la misma consulta.
- `prefetch_related` hace **una consulta adicional** y une en Python.

**¿Por qué importa?** Sin ellos, acceder a cada relación lanza una consulta: el problema N+1.

### 11.5. Errores comunes

**Error 1 — Evaluar el QuerySet varias veces.**

```python
if Post.objects.filter(publicado=True):     # consulta 1
    for p in Post.objects.filter(publicado=True):  # consulta 2
```

*Solución:* guárdalo en una variable.

**Error 2 — `len(qs)` en lugar de `qs.count()`.**

`len` trae todos los objetos; `count()` solo cuenta.

**Error 3 — Problema N+1.**

### 11.6. Checkpoint de comprensión

1. ¿Qué significa que un QuerySet sea "perezoso"?
2. ¿Qué diferencia hay entre `filter` y `exclude`?
3. ¿Cuándo usas `select_related` y cuándo `prefetch_related`?

### 11.7. Ejercicio propuesto

**Ejercicio 11.1.** Obtén los 5 posts más recientes publicados.

**Pista:** `filter(publicado=True).order_by("-creado_en")[:5]`.

---

## 12. F expressions y Q objects

### 12.1. `F`: referirse a campos de la base

**`F`** permite referirse a un campo **sin traerlo a Python**, ideal para incrementos atómicos:

```python
from django.db.models import F

Post.objects.filter(id=1).update(visitas=F("visitas") + 1)
```

**Explicación línea por línea:**
- `F("visitas") + 1` se traduce a SQL `visitas = visitas + 1`, **en la base**.
- Evita condiciones de carrera que ocurrirían con leer-modificar-escribir en Python.

### 12.2. `Q`: combinar condiciones

**`Q`** permite combinar condiciones con `&` (AND), `|` (OR) y `~` (NOT):

```python
from django.db.models import Q

Post.objects.filter(Q(titulo__icontains="python") | Q(contenido__icontains="django"))
Post.objects.filter(~Q(publicado=True))
```

**Explicación línea por línea:**
- `Q(...) | Q(...)` busca posts que contengan "python" en el título **o** "django" en el contenido.
- `~Q(...)` niega la condición.

### 12.3. Errores comunes

**Error 1 — Usar `F` en Python** fuera de una expresión de base.

**Error 2 — Olvidar paréntesis** al combinar `Q`.

### 12.4. Checkpoint de comprensión

1. ¿Qué ventaja tiene `F` frente a leer-modificar-escribir?
2. ¿Para qué sirve `Q`?

### 12.5. Ejercicio propuesto

**Ejercicio 12.1.** Incrementa el contador de visitas de un post con `F`.

**Pista:** `.update(visitas=F("visitas") + 1)`.

---

## 13. Aggregation y annotation

### 13.1. La diferencia

- **`aggregate`**: calcula un valor **sobre todo** el QuerySet (un solo resultado).
- **`annotate`**: añade un valor calculado **a cada** objeto.

### 13.2. Ejemplos

```python
from django.db.models import Avg, Count, Sum

# aggregate: un valor para todo el conjunto
total = Post.objects.aggregate(Count("id"))
print(total)   # {'id__count': 42}

# annotate: un valor por cada autor
autores = Autor.objects.annotate(num_libros=Count("libros"))
for autor in autores:
    print(autor.nombre, autor.num_libros)
```

**Explicación línea por línea:**
- `aggregate(Count("id"))` cuenta todos los posts y devuelve un diccionario.
- `annotate(num_libros=Count("libros"))` añade a cada autor el número de libros.

### 13.3. Filtrar sobre anotaciones

```python
autores = Autor.objects.annotate(num_libros=Count("libros")).filter(num_libros__gt=2)
```

**Explicación línea por línea:**
- Se puede filtrar por el valor anotado, como si fuera un campo más.

### 13.4. Errores comunes

**Error 1 — Confundir `aggregate` con `annotate`.**

**Error 2 — Anotar y filtrar en el orden equivocado.**

### 13.5. Checkpoint de comprensión

1. ¿Qué diferencia hay entre `aggregate` y `annotate`?
2. ¿Puedes filtrar por una anotación?

### 13.6. Ejercicio propuesto

**Ejercicio 13.1.** Anota cada autor con la suma de páginas de sus libros.

**Pista:** `Sum("libros__paginas")`.

---

## 14. Managers personalizados

### 14.1. Qué es un Manager

El **Manager** es la interfaz de consultas de un modelo (`objects` por defecto). Puedes crear los tuyos con métodos reutilizables.

### 14.2. Ejemplo

```python
from django.db import models


class PostQuerySet(models.QuerySet):
    def publicados(self):
        return self.filter(publicado=True)

    def recientes(self):
        return self.order_by("-creado_en")


class Post(models.Model):
    titulo = models.CharField(max_length=200)
    publicado = models.BooleanField(default=False)
    creado_en = models.DateTimeField(auto_now_add=True)

    objects = PostQuerySet.as_manager()


# Uso
Post.objects.publicados().recientes()
```

**Explicación línea por línea:**
- `PostQuerySet` define métodos encadenables.
- `PostQuerySet.as_manager()` los expone en `objects`.
- Ahora las consultas frecuentes se leen como frases.

### 14.3. Errores comunes

**Error 1 — Sobrescribir `objects` sin querer.**

**Error 2 — Poner lógica de negocio compleja** en el manager en lugar de en servicios.

### 14.4. Checkpoint de comprensión

1. ¿Qué es un Manager?
2. ¿Qué ventaja aporta un QuerySet personalizado?

### 14.5. Ejercicio propuesto

**Ejercicio 14.1.** Añade un método `del_ano(year)` al QuerySet.

**Pista:** `filter(creado_en__year=year)`.

---

## 15. Migraciones

### 15.1. Sincronizar modelos y base

Django sincroniza el esquema con **migraciones**: archivos versionados que describen los cambios.

### 15.2. Comandos

```bash
python manage.py makemigrations
python manage.py migrate
python manage.py showmigrations
python manage.py sqlmigrate blog 0001
```

**Explicación línea por línea:**
- `makemigrations` genera archivos de migración a partir de los cambios en los modelos.
- `migrate` los aplica a la base.
- `showmigrations` muestra cuáles están aplicadas.
- `sqlmigrate` muestra el SQL que genera una migración.

### 15.3. El flujo correcto

1. Modificas el modelo.
2. `makemigrations`.
3. **Revisas** el archivo en `blog/migrations/`.
4. `migrate`.

### 15.4. Errores comunes

**Error 1 — Editar la base a mano.**

**Error 2 — No versionar `migrations/`.**

**Error 3 — Conflictos de migración** en equipos. Comunica los cambios.

### 15.5. Checkpoint de comprensión

1. ¿Qué hace `makemigrations`? ¿Y `migrate`?
2. ¿Por qué revisar las migraciones generadas?

### 15.6. Ejercicio propuesto

**Ejercicio 15.1.** Añade un campo `visitas` a `Post`, genera y aplica la migración.

**Pista:** revisa el SQL con `sqlmigrate`.

---

## 16. Admin de Django

### 16.1. El panel automático

El **admin** es un panel CRUD automático para tus modelos, personalizable.

### 16.2. Registrar y personalizar

```python
from django.contrib import admin
from .models import Post


@admin.register(Post)
class PostAdmin(admin.ModelAdmin):
    list_display = ("titulo", "publicado", "creado_en")
    list_filter = ("publicado", "creado_en")
    search_fields = ("titulo", "contenido")
    prepopulated_fields = {"slug": ("titulo",)}
    date_hierarchy = "creado_en"
```

**Explicación línea por línea:**
- `list_display` son las columnas de la lista.
- `list_filter` añade filtros laterales.
- `search_fields` habilita la búsqueda.
- `prepopulated_fields` rellena un campo a partir de otro.
- `date_hierarchy` añade navegación por fechas.

### 16.3. Crear un superusuario

```bash
python manage.py createsuperuser
```

Accede en `/admin/`.

### 16.4. Errores comunes

**Error 1 — Dejar el admin accesible sin HTTPS** en producción.

**Error 2 — No limitar permisos.** Configura grupos y permisos.

### 16.5. Checkpoint de comprensión

1. ¿Qué es el admin de Django?
2. ¿Cómo personalizas la lista de un modelo?

### 16.6. Ejercicio propuesto

**Ejercicio 16.1.** Personaliza el admin para editar `publicado` desde la lista.

**Pista:** `list_editable = ("publicado",)`.

---

## 17. Autenticación y autorización

### 17.1. El sistema integrado

Django trae usuarios, grupos y permisos. Los decoradores y mixins controlan el acceso.

### 17.2. Login/logout con vistas integradas

**`config/urls.py`:**

```python
from django.contrib.auth import views as auth_views
from django.urls import path

urlpatterns += [
    path("login/", auth_views.LoginView.as_view(), name="login"),
    path("logout/", auth_views.LogoutView.as_view(), name="logout"),
]
```

### 17.3. Proteger vistas

```python
from django.contrib.auth.decorators import login_required, permission_required


@login_required
def panel(request):
    return render(request, "panel.html")


@permission_required("blog.add_post", raise_exception=True)
def crear(request):
    ...
```

**Explicación línea por línea:**
- `@login_required` redirige al login si el usuario no está autenticado.
- `@permission_required("blog.add_post")` exige un permiso concreto.

**Con CBV:**

```python
from django.contrib.auth.mixins import LoginRequiredMixin
from django.views.generic import ListView


class PanelView(LoginRequiredMixin, ListView):
    model = Post
    template_name = "panel.html"
```

### 17.4. Modelo de usuario personalizado

Es buena práctica empezar el proyecto con un `AUTH_USER_MODEL` propio, porque cambiarlo a mitad es doloroso:

```python
class Usuario(models.Model):
    email = models.EmailField(unique=True)
    USERNAME_FIELD = "email"
```

### 17.5. Errores comunes

**Error 1 — Crear el usuario personalizado a mitad del proyecto.**

**Error 2 — Olvidar `@login_required`** en vistas sensibles.

### 17.6. Checkpoint de comprensión

1. ¿Qué diferencia hay entre autenticación y autorización?
2. ¿Por qué definir un usuario personalizado al inicio?

### 17.7. Ejercicio propuesto

**Ejercicio 17.1.** Protege la vista de creación de posts con `@login_required`.

**Pista:** importa el decorador.

---

## 18. Middleware

### 18.1. Qué es el middleware

El **middleware** procesa peticiones y respuestas **globalmente**, en cadena. Cada middleware puede modificar la petición, la respuesta o ambos.

### 18.2. Ejemplo

```python
import time


class TiempoMiddleware:
    """Añade una cabecera con el tiempo de respuesta."""

    def __init__(self, get_response):
        self.get_response = get_response

    def __call__(self, request):
        inicio = time.perf_counter()
        response = self.get_response(request)
        response["X-Duracion"] = f"{time.perf_counter() - inicio:.4f}"
        return response
```

**Explicación línea por línea:**
- `__init__` recibe la siguiente capa de la cadena (`get_response`).
- `__call__` procesa la petición, llama a la siguiente capa y modifica la respuesta.

**`settings.py`:**

```python
MIDDLEWARE = [
    "django.middleware.security.SecurityMiddleware",
    "django.contrib.sessions.middleware.SessionMiddleware",
    "django.middleware.common.CommonMiddleware",
    "django.middleware.csrf.CsrfViewMiddleware",
    "django.contrib.auth.middleware.AuthenticationMiddleware",
    "mi_app.middleware.TiempoMiddleware",
]
```

### 18.3. Errores comunes

**Error 1 — Middleware que hace consultas costosas** en cada petición.

**Error 2 — Mal orden** en `MIDDLEWARE` (el orden importa).

### 18.4. Checkpoint de comprensión

1. ¿Qué procesa el middleware?
2. ¿Por qué importa el orden en `MIDDLEWARE`?

### 18.5. Ejercicio propuesto

**Ejercicio 18.1.** Crea un middleware que registre en consola el método y la ruta.

**Pista:** impleméntalo en `__call__`.

---

## 19. Signals

### 19.1. Reaccionar a eventos

Las **signals** permiten reaccionar a eventos (guardado, borrado, login) sin acoplar código.

### 19.2. Ejemplo

```python
from django.db.models.signals import post_save
from django.dispatch import receiver
from django.contrib.auth.models import User
from .models import Perfil


@receiver(post_save, sender=User)
def crear_perfil(sender, instance, created, **kwargs):
    """Crea un perfil al registrar un usuario."""
    if created:
        Perfil.objects.create(usuario=instance)
```

**Explicación línea por línea:**
- `@receiver(post_save, sender=User)` se ejecuta tras guardar un `User`.
- `created` indica si es un usuario nuevo.
- Registra las señales en `apps.py` con `ready()`.

### 19.3. Errores comunes

**Error 1 — Abusar de signals.** Ocultan el flujo y dificultan el debugging.

**Error 2 — Signals que llaman a `save()`** y provocan recursión.

### 19.4. Checkpoint de comprensión

1. ¿Para qué sirve una signal?
2. ¿Por qué no abusar de ellas?

### 19.5. Ejercicio propuesto

**Ejercicio 19.1.** Crea un `post_delete` que registre en log el título borrado.

**Pista:** usa `@receiver(post_delete, sender=Post)`.

---

## 20. Django REST Framework

### 20.1. Qué es DRF

**DRF** construye APIs REST sobre Django con **serializadores**, **vistas** y **autenticación**.

### 20.2. Instalación

```bash
pip install djangorestframework
```

```python
INSTALLED_APPS += ["rest_framework"]
```

### 20.3. Un serializer

```python
from rest_framework import serializers
from .models import Post


class PostSerializer(serializers.ModelSerializer):
    class Meta:
        model = Post
        fields = ["id", "titulo", "contenido", "publicado", "creado_en"]
```

**Explicación línea por línea:**
- `ModelSerializer` genera el serializador a partir del modelo.
- `fields` lista los campos a incluir en la API.

### 20.4. Un ViewSet y un router

```python
from rest_framework import viewsets
from .models import Post
from .serializers import PostSerializer


class PostViewSet(viewsets.ModelViewSet):
    queryset = Post.objects.all()
    serializer_class = PostSerializer
```

**`urls.py`:**

```python
from rest_framework.routers import DefaultRouter
from .views import PostViewSet

router = DefaultRouter()
router.register("posts", PostViewSet)
urlpatterns = router.urls
```

**Explicación línea por línea:**
- `ModelViewSet` genera automáticamente listar, crear, ver, actualizar y borrar.
- El router crea todas las URLs REST.

### 20.5. Autenticación

```python
REST_FRAMEWORK = {
    "DEFAULT_AUTHENTICATION_CLASSES": [
        "rest_framework.authentication.SessionAuthentication",
        "rest_framework_simplejwt.authentication.JWTAuthentication",
    ],
    "DEFAULT_PERMISSION_CLASSES": [
        "rest_framework.permissions.IsAuthenticatedOrReadOnly",
    ],
}
```

### 20.6. Errores comunes

**Error 1 — Exponer todos los campos** sin pensar en datos sensibles.

**Error 2 — No limitar permisos** en la API.

### 20.7. Checkpoint de comprensión

1. ¿Qué hace un `ModelSerializer`?
2. ¿Qué genera un `ModelViewSet`?
3. ¿Por qué limitar permisos en la API?

### 20.8. Ejercicio propuesto

**Ejercicio 20.1.** Crea un ViewSet para `Autor` con paginación.

**Pista:** configura `DEFAULT_PAGINATION_CLASS`.

---

## 21. Django Channels (WebSockets)

### 21.1. Qué añade Channels

**Channels** extiende Django para manejar **WebSockets** y protocolos asíncronos, usando **ASGI**.

### 21.2. Un consumer básico

```python
from channels.generic.websocket import AsyncWebsocketConsumer
import json


class ChatConsumer(AsyncWebsocketConsumer):
    async def connect(self):
        self.sala = self.scope["url_route"]["kwargs"]["sala"]
        await self.channel_layer.group_add(self.sala, self.channel_name)
        await self.accept()

    async def disconnect(self, close_code):
        await self.channel_layer.group_discard(self.sala, self.channel_name)

    async def receive(self, text_data):
        datos = json.loads(text_data)
        await self.channel_layer.group_send(
            self.sala,
            {"type": "mensaje", "texto": datos["texto"]},
        )

    async def mensaje(self, event):
        await self.send(text_data=json.dumps({"texto": event["texto"]}))
```

**Explicación línea por línea:**
- `connect` une la conexión a un grupo (sala).
- `disconnect` la quita.
- `receive` recibe un mensaje y lo reenvía al grupo.
- `mensaje` envía el mensaje a cada cliente.

### 21.3. Routing ASGI

```python
from django.urls import path
from .consumers import ChatConsumer

websocket_urlpatterns = [
    path("ws/chat/<str:sala>/", ChatConsumer.as_asgi()),
]
```

### 21.4. Errores comunes

**Error 1 — Usar el servidor de desarrollo para WebSockets en producción.** Usa Daphne/Uvicorn.

**Error 2 — Bloquear el event loop** con código síncrono.

### 21.5. Checkpoint de comprensión

1. ¿Qué añade Channels a Django?
2. ¿Qué es un consumer?

### 21.6. Ejercicio propuesto

**Ejercicio 21.1.** Añade a un chat grupal la notificación de "usuario se conectó".

**Pista:** envía un mensaje en `connect`.

---

## 22. Caching

### 22.1. Por qué cachear

Consultar la base en cada petición es costoso. La **caché** guarda resultados temporalmente para servirlos más rápido.

### 22.2. Configuración

```python
CACHES = {
    "default": {
        "BACKEND": "django.core.cache.backends.redis.RedisCache",
        "LOCATION": "redis://127.0.0.1:6379/1",
    }
}
```

### 22.3. Uso manual

```python
from django.core.cache import cache


def obtener_posts():
    posts = cache.get("posts")
    if posts is None:
        posts = list(Post.objects.publicados())
        cache.set("posts", posts, timeout=300)
    return posts
```

**Explicación línea por línea:**
- `cache.get("posts")` intenta recuperar de la caché.
- Si no está (`None`), se consulta la base y se guarda con `cache.set(..., timeout=300)` (5 minutos).

### 22.4. Cachear una vista

```python
from django.views.decorators.cache import cache_page


@cache_page(60 * 5)
def lista_posts(request):
    ...
```

### 22.5. Errores comunes

**Error 1 — Cachear datos personalizados por usuario** sin clave por usuario.

**Error 2 — Invalidación olvidada** → datos obsoletos.

### 22.6. Checkpoint de comprensión

1. ¿Qué problema resuelve la caché?
2. ¿Qué riesgo hay si no invalidamos la caché?

### 22.7. Ejercicio propuesto

**Ejercicio 22.1.** Cachea la lista de posts durante 5 minutos e invalida al crear uno.

**Pista:** `cache.delete("posts")` en el guardado.

---

## 23. Internacionalización

### 23.1. Traducir la aplicación

Django permite traducir textos con `gettext` y `{% trans %}`.

### 23.2. Configuración

```python
LANGUAGE_CODE = "es"
USE_I18N = True
LANGUAGES = [("es", "Español"), ("en", "English")]
LOCALE_PATHS = [BASE_DIR / "locale"]
MIDDLEWARE.insert(1, "django.middleware.locale.LocaleMiddleware")
```

### 23.3. Uso

```python
from django.utils.translation import gettext as _

mensaje = _("Bienvenido")
```

```html
{% load i18n %}
<h1>{% trans "Bienvenido" %}</h1>
```

```bash
django-admin makemessages -l en
django-admin compilemessages
```

**Explicación línea por línea:**
- `_("...")` marca un texto como traducible.
- `makemessages` extrae los textos a archivos `.po`.
- `compilemessages` los compila a `.mo` para usarlos.

### 23.4. Errores comunes

**Error 1 — Olvidar `compilemessages`** tras editar un `.po`.

**Error 2 — No usar `{% load i18n %}`.**

### 23.5. Checkpoint de comprensión

1. ¿Qué hace `gettext`?
2. ¿Qué diferencia hay entre `makemessages` y `compilemessages`?

### 23.6. Ejercicio propuesto

**Ejercicio 23.1.** Traduce el título de la página principal a inglés.

**Pista:** usa `{% trans %}` y genera los mensajes.

---

## 24. Testing en Django

### 24.1. El TestCase integrado

Django incluye `TestCase` y un **cliente de pruebas** que crea una **base de datos de test temporal**.

### 24.2. Ejemplo

```python
from django.test import TestCase
from django.urls import reverse
from .models import Post


class PostTests(TestCase):
    def setUp(self):
        self.post = Post.objects.create(titulo="Test", contenido="...", publicado=True)

    def test_lista(self):
        respuesta = self.client.get(reverse("blog:lista"))
        self.assertEqual(respuesta.status_code, 200)
        self.assertContains(respuesta, "Test")

    def test_modelo_str(self):
        self.assertEqual(str(self.post), "Test")
```

Ejecución:

```bash
python manage.py test
```

**Explicación línea por línea:**
- `setUp` prepara datos antes de cada test.
- `self.client.get(...)` simula una petición.
- `assertEqual`, `assertContains` verifican resultados.

### 24.3. Errores comunes

**Error 1 — Tests que dependen del orden.** Usa `setUp`.

**Error 2 — No probar permisos y casos límite.**

### 24.4. Checkpoint de comprensión

1. ¿Qué hace `setUp`?
2. ¿Qué base de datos usan los tests?

### 24.5. Ejercicio propuesto

**Ejercicio 24.1.** Testea que un post no publicado no aparezca en la lista.

**Pista:** crea el post con `publicado=False`.

---

## 25. Despliegue en producción

### 25.1. La arquitectura típica

El despliegue habitual es **Gunicorn + Nginx**, con estáticos servidos por Nginx (o WhiteNoise) y `DEBUG=False`.

### 25.2. Checklist de seguridad

```python
DEBUG = False
ALLOWED_HOSTS = ["ejemplo.com"]
SECURE_SSL_REDIRECT = True
SESSION_COOKIE_SECURE = True
CSRF_COOKIE_SECURE = True
```

**Explicación línea por línea:**
- `DEBUG = False` desactiva el modo de depuración.
- `SECURE_SSL_REDIRECT = True` fuerza HTTPS.
- `SESSION_COOKIE_SECURE` y `CSRF_COOKIE_SECURE` envían las cookies solo por HTTPS.

### 25.3. Gunicorn y estáticos

```bash
pip install gunicorn
python manage.py collectstatic
gunicorn config.wsgi:application --bind 0.0.0.0:8000 --workers 3
```

**Explicación línea por línea:**
- `collectstatic` reúne todos los estáticos en una carpeta.
- Gunicorn sirve la aplicación WSGI.

### 25.4. Nginx

```nginx
server {
    listen 80;
    server_name ejemplo.com;

    location /static/ {
        alias /var/www/app/staticfiles/;
    }

    location / {
        proxy_pass http://127.0.0.1:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```

### 25.5. Errores comunes

**Error 1 — Dejar `DEBUG=True`** en producción.

**Error 2 — No ejecutar `collectstatic`.**

**Error 3 — No usar HTTPS.**

### 25.6. Checkpoint de comprensión

1. ¿Qué hace `collectstatic`?
2. ¿Por qué forzar HTTPS?

### 25.7. Ejercicio propuesto

**Ejercicio 25.1.** Prepara un `Dockerfile` y un `docker-compose.yml` con Postgres.

**Pista:** separa web y base de datos en servicios.

---

## 26. Buenas prácticas

1. **Un proyecto, varias apps pequeñas.**
2. **Usa `AUTH_USER_MODEL` personalizado** desde el inicio.
3. **Migraciones versionadas** siempre.
4. **`select_related`/`prefetch_related`** para evitar N+1.
5. **Mueve lógica de negocio a servicios/modelos.**
6. **`DEBUG=False` y HTTPS** en producción.
7. **Variables de entorno** para secretos.
8. **Tests** con `TestCase` y cliente de pruebas.
9. **DRF** para APIs, no vistas HTML.
10. **No abuses de signals.**

---

## 27. Recursos

- **Documentación oficial:** <https://docs.djangoproject.com/>
- **Django ORM (docs):** <https://docs.djangoproject.com/en/stable/topics/db/queries/>
- **DRF:** <https://www.django-rest-framework.org/>
- **Django Channels:** <https://channels.readthedocs.io/>
- **Libro:** *Two Scoops of Django*, Audrey & Daniel Feldroy.
- **Tutorial oficial:** <https://docs.djangoproject.com/en/stable/intro/tutorial01/>

---

## 28. Mini resumen final

- Django es un framework **todo incluido** con ORM, admin y seguridad.
- El patrón **MVT** separa modelo, vista y plantilla.
- Los **modelos** definen el esquema; las **migraciones** lo versionan.
- **QuerySet** ofrece filtros, lookups, `F`, `Q`, `annotate` y `aggregate`.
- Las **relaciones** usan `ForeignKey`, `OneToOneField` y `ManyToManyField`.
- El **admin** da CRUD automático; **auth** gestiona usuarios y permisos.
- **DRF** construye APIs REST; **Channels** añade WebSockets.
- **Caché, i18n y testing** son parte de la caja de herramientas.

---

### 🎯 Retos opcionales

1. **Reto 1:** Crea un blog completo con categorías, etiquetas y búsqueda.
2. **Reto 2:** Expón una API DRF con autenticación JWT.
3. **Reto 3:** Añade un chat en tiempo real con Channels.

**Anterior:** [10_flask_sqlalchemy.md](10_flask_sqlalchemy.md) · **Siguiente:** [12_fastapi_orm.md](12_fastapi_orm.md)
