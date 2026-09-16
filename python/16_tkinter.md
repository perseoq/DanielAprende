# 16 · Tkinter

**Versión recomendada:** Tkinter (incluido en Python 3.12) · Tk 8.6
**Requisito:** [15_selenium.md](15_selenium.md) (o fundamentos)
**Nivel:** Intermedio
**Duración estimada de estudio:** 8–12 horas

---

## Índice de contenidos

1. [Introducción a Tkinter](#1-introducción-a-tkinter)
2. [Instalación y primera ventana](#2-instalación-y-primera-ventana)
3. [Widgets básicos](#3-widgets-básicos-label-button-entry-text)
4. [Gestores de geometría](#4-gestores-de-geometría-pack-grid-place)
5. [Eventos y callbacks](#5-eventos-y-callbacks)
6. [Variables de control](#6-variables-de-control-stringvar-intvar-etc)
7. [Menús y barras de herramientas](#7-menús-y-barras-de-herramientas)
8. [Diálogos](#8-diálogos-messagebox-filedialog-colorchooser)
9. [Canvas y dibujo](#9-canvas-y-dibujo)
10. [Frames y organización](#10-frames-y-organización)
11. [Notebook (pestañas)](#11-notebook-pestañas)
12. [Treeview y tablas](#12-treeview-y-tablas)
13. [Estilos con ttk](#13-estilos-con-ttk)
14. [Threading en Tkinter](#14-threading-en-tkinter)
15. [Empaquetado con PyInstaller](#15-empaquetado-con-pyinstaller)
16. [Patrones MVC](#16-buenas-prácticas-y-patrones-mvc)
17. [Recursos](#17-recursos)
18. [Mini resumen final](#18-mini-resumen-final)

---

## 1. Introducción a Tkinter

### 1.1. ¿Qué es Tkinter?

**Tkinter** es la biblioteca de **interfaces gráficas** (GUI) estándar de Python. Viene **incluida**, es multiplataforma (Windows, macOS, Linux) y sencilla de aprender. Es la forma más rápida de crear una aplicación de escritorio en Python.

**¿Qué es una GUI?** Una *Graphical User Interface* es una interfaz con ventanas, botones y campos, como cualquier programa de escritorio. Sin GUI, tus programas solo hablan por la terminal.

### 1.2. ¿Cuándo usarlo?

| Escenario | ¿Tkinter? |
|---|---|
| Herramienta interna pequeña | Sí |
| Prototipo de escritorio | Sí |
| App con muchos gráficos y efectos | Mejor PySide6 (archivo 17) |
| App táctil o móvil | Mejor Kivy |

### 1.3. Errores comunes

**Error 1 — Esperar un look moderno por defecto.**

Los widgets clásicos de Tk se ven anticuados. *Solución:* usa **`ttk`** (sección 13), que ofrece widgets nativos.

**Error 2 — Bloquear el bucle principal** con tareas largas (sección 14).

### 1.4. Checkpoint de comprensión

1. ¿Qué es una GUI?
2. ¿Por qué Tkinter es buena opción para empezar?

### 1.5. Ejercicio propuesto

**Ejercicio 1.1.** Enumera tres apps de escritorio que harías con Tkinter y tres que no.

**Pista:** piensa en complejidad visual y rendimiento.

---

## 2. Instalación y primera ventana

### 2.1. Tkinter viene con Python

No hay que instalar nada: se importa. En algunos Linux puede faltar el paquete del sistema:

```bash
sudo apt install python3-tk
```

### 2.2. La ventana mínima

```python
import tkinter as tk

raiz = tk.Tk()
raiz.title("Mi primera app")
raiz.geometry("300x200")

etiqueta = tk.Label(raiz, text="¡Hola, Tkinter!")
etiqueta.pack()

raiz.mainloop()
```

**Explicación línea por línea:**
- `import tkinter as tk` importa la librería (el alias `tk` es convención).
- `tk.Tk()` crea la **ventana raíz** (la principal).
- `raiz.title(...)` establece el título.
- `raiz.geometry("300x200")` fija el tamaño (ancho x alto).
- `tk.Label(raiz, text=...)` crea una etiqueta dentro de la raíz.
- `.pack()` la coloca en la ventana.
- `raiz.mainloop()` **inicia el bucle de eventos**: sin él, la ventana no aparece ni responde.

### 2.3. El bucle de eventos

`mainloop()` es el corazón de cualquier GUI. Es un bucle infinito que:

1. Espera eventos (clics, teclas).
2. Los distribuye a los widgets correspondientes.
3. Redibuja lo necesario.

**Analogía:** es como un recepcionista que espera llamadas y las dirige a quien corresponda, sin parar.

### 2.4. Errores comunes

**Error 1 — Olvidar `mainloop()`.**

*Síntoma:* la ventana no aparece o se cierra al instante.

**Error 2 — Crear varios `Tk()`.**

Solo debe haber **una** ventana raíz. *Solución:* usa `tk.Toplevel` para ventanas secundarias.

### 2.5. Checkpoint de comprensión

1. ¿Qué hace `mainloop()`?
2. ¿Cuántas ventanas raíz debe haber?

### 2.6. Ejercicio propuesto

**Ejercicio 2.1.** Crea una ventana con tu nombre como título y tamaño 400x300.

**Pista:** `geometry("400x300")`.

---

## 3. Widgets básicos (Label, Button, Entry, Text)

### 3.1. Los ladrillos de la interfaz

Un **widget** es un componente de la interfaz. Los más usados:

| Widget | Uso |
|---|---|
| `Label` | Texto o imagen |
| `Button` | Botón |
| `Entry` | Campo de una línea |
| `Text` | Área de texto multilínea |
| `Checkbutton` | Casilla |
| `Radiobutton` | Opción única |
| `Listbox` | Lista |
| `Scale` | Deslizador |

### 3.2. Un ejemplo interactivo

```python
import tkinter as tk


def saludar():
    nombre = entrada.get()
    etiqueta_resultado.config(text=f"Hola, {nombre}")


raiz = tk.Tk()
raiz.title("Saludo")

tk.Label(raiz, text="Tu nombre:").pack()
entrada = tk.Entry(raiz)
entrada.pack()

tk.Button(raiz, text="Saludar", command=saludar).pack()
etiqueta_resultado = tk.Label(raiz, text="")
etiqueta_resultado.pack()

raiz.mainloop()
```

**Explicación línea por línea:**
- `tk.Entry(raiz)` crea un campo de texto.
- `entrada.get()` lee lo que el usuario escribió.
- `tk.Button(..., command=saludar)` asocia el botón a la función `saludar` (el **callback**).
- `etiqueta_resultado.config(text=...)` **actualiza** el texto de la etiqueta.

**Importante:** cuando defines `command=saludar`, pasas la **función sin paréntesis**. Si escribieras `saludar()`, la ejecutarías al crear el botón, no al pulsarlo.

### 3.3. Text multilínea

```python
area = tk.Text(raiz, height=5, width=30)
area.pack()
area.insert("1.0", "Texto inicial\n")
print(area.get("1.0", tk.END))
```

**Explicación línea por línea:**
- `tk.Text(...)` crea un área de texto.
- `insert("1.0", ...)` inserta en la línea 1, carácter 0.
- `get("1.0", tk.END)` lee desde el inicio hasta el final.

### 3.4. Errores comunes

**Error 1 — No guardar referencia** a un widget que luego modificas.

**Error 2 — Usar `Text` para una línea.** Usa `Entry`.

**Error 3 — Llamar al callback con paréntesis** al asignarlo.

### 3.5. Checkpoint de comprensión

1. ¿Qué diferencia hay entre `Entry` y `Text`?
2. ¿Por qué `command=saludar` y no `command=saludar()`?

### 3.6. Ejercicio propuesto

**Ejercicio 3.1.** Crea una calculadora de suma con dos `Entry` y un botón.

**Pista:** convierte con `int()` o `float()`.

---

## 4. Gestores de geometría (pack, grid, place)

### 4.1. Cómo se colocan los widgets

Tkinter ofrece tres **gestores de geometría**:

- **`pack`:** apila widgets (arriba, abajo, izquierda, derecha).
- **`grid`:** coloca en una **rejilla** fila/columna (el más flexible).
- **`place`:** posición absoluta (poco recomendado).

### 4.2. `grid` en acción

```python
import tkinter as tk

raiz = tk.Tk()
raiz.title("Formulario")

tk.Label(raiz, text="Usuario:").grid(row=0, column=0, sticky="e", padx=5, pady=5)
tk.Entry(raiz).grid(row=0, column=1, padx=5, pady=5)

tk.Label(raiz, text="Contraseña:").grid(row=1, column=0, sticky="e", padx=5, pady=5)
tk.Entry(raiz, show="*").grid(row=1, column=1, padx=5, pady=5)

tk.Button(raiz, text="Entrar").grid(row=2, column=0, columnspan=2, pady=10)

raiz.mainloop()
```

**Explicación línea por línea:**
- `row` y `column` definen la celda.
- `sticky="e"` alinea el widget a la derecha (*east*).
- `padx`/`pady` añaden espacio alrededor.
- `show="*"` oculta la contraseña.
- `columnspan=2` hace que el botón ocupe dos columnas.

### 4.3. `pack` con opciones

```python
tk.Button(raiz, text="Arriba").pack(side="top", fill="x")
tk.Button(raiz, text="Izquierda").pack(side="left", expand=True, fill="both")
```

**Explicación línea por línea:**
- `side` indica de qué lado colocar.
- `fill="x"` estira horizontalmente.
- `expand=True` ocupa el espacio extra.

### 4.4. Errores comunes

**Error 1 — Mezclar `pack` y `grid`** en el mismo contenedor.

```text
_tkinter.TclError: cannot use geometry manager pack inside ...
```

*Solución:* usa uno solo por contenedor (puedes usar `Frame` para separar).

**Error 2 — Usar `place` para todo** y ventanas que no se redimensionan bien.

### 4.5. Checkpoint de comprensión

1. ¿Qué diferencia hay entre `pack`, `grid` y `place`?
2. ¿Por qué no mezclar `pack` y `grid`?

### 4.6. Ejercicio propuesto

**Ejercicio 4.1.** Crea una cuadrícula 3x3 de botones numerados.

**Pista:** usa `grid` con bucles.

---

## 5. Eventos y callbacks

### 5.1. El modelo de eventos

En una GUI, casi todo es reacción a **eventos**: clics, teclas, movimiento del ratón. Los eventos se asocian a funciones con `.bind()`.

### 5.2. Ejemplo

```python
import tkinter as tk


def al_pulsar_tecla(event):
    print(f"Tecla: {event.char}, código: {event.keysym}")


def al_hacer_clic(event):
    print(f"Clic en ({event.x}, {event.y})")


raiz = tk.Tk()

raiz.bind("<Key>", al_pulsar_tecla)
raiz.bind("<Button-1>", al_hacer_clic)

raiz.mainloop()
```

**Explicación línea por línea:**
- `.bind("<Key>", funcion)` asocia una tecla a una función.
- La función recibe un objeto `event` con información.
- `event.char` es el carácter; `event.keysym` el nombre de la tecla.
- `<Button-1>` es el clic izquierdo; `event.x`/`event.y` son las coordenadas.

### 5.3. Eventos comunes

| Evento | Descripción |
|---|---|
| `<Button-1>` | Clic izquierdo |
| `<Double-Button-1>` | Doble clic |
| `<Key>` | Tecla |
| `<Return>` | Enter |
| `<Motion>` | Movimiento |
| `<Enter>` / `<Leave>` | Entrar/salir del widget |

### 5.4. Errores comunes

**Error 1 — Olvidar el parámetro `event`** en el callback.

```python
def callback():   # ❌ bind pasa un argumento
    ...
```

*Solución:* `def callback(event):`.

**Error 2 — Bind a nivel global** cuando querías solo un widget.

### 5.5. Checkpoint de comprensión

1. ¿Qué hace `.bind()`?
2. ¿Qué información trae el objeto `event`?

### 5.6. Ejercicio propuesto

**Ejercicio 5.1.** Muestra en una etiqueta la tecla pulsada.

**Pista:** usa `<Key>` y `event.char`.

---

## 6. Variables de control (StringVar, IntVar, etc.)

### 6.1. Enlazar datos y widgets

Las **variables de control** (`StringVar`, `IntVar`, `DoubleVar`, `BooleanVar`) enlazan widgets y datos, actualizándose automáticamente.

**Analogía:** son como un cable entre el widget y tu variable: cambias uno y el otro se entera.

### 6.2. Ejemplo

```python
import tkinter as tk

raiz = tk.Tk()

nombre = tk.StringVar(value="")
resultado = tk.StringVar(value="Escribe algo...")

tk.Entry(raiz, textvariable=nombre).pack()


def actualizar():
    resultado.set(f"Hola, {nombre.get()}")


tk.Button(raiz, text="Actualizar", command=actualizar).pack()
tk.Label(raiz, textvariable=resultado).pack()

raiz.mainloop()
```

**Explicación línea por línea:**
- `tk.StringVar(value="")` crea una variable de texto.
- `textvariable=nombre` **enlaza** el campo de texto a la variable.
- `nombre.get()` lee el valor; `resultado.set(...)` lo escribe.
- El `Label` con `textvariable=resultado` se actualiza **solo**.

### 6.3. Reaccionar a cambios con `trace`

```python
def al_cambiar(*args):
    print("Nuevo valor:", nombre.get())


nombre.trace_add("write", al_cambiar)
```

**Explicación línea por línea:**
- `trace_add("write", ...)` ejecuta una función cada vez que la variable cambia.

### 6.4. Errores comunes

**Error 1 — Usar `StringVar` sin `master`** en apps multipágina.

**Error 2 — Operar con el objeto variable** en lugar de su valor (`.get()`).

### 6.5. Checkpoint de comprensión

1. ¿Qué ventaja aporta `textvariable`?
2. ¿Cómo lees y escribes una variable de control?

### 6.6. Ejercicio propuesto

**Ejercicio 6.1.** Enlaza un `IntVar` a un `Scale` y muestra el valor en un `Label`.

**Pista:** `textvariable` en ambos.

---

## 7. Menús y barras de herramientas

### 7.1. Construir menús

Los menús se construyen con `Menu` y `add_command`/`add_cascade`:

```python
import tkinter as tk
from tkinter import messagebox

raiz = tk.Tk()
raiz.title("Con menú")


def salir():
    if messagebox.askokcancel("Salir", "¿Seguro que quieres salir?"):
        raiz.destroy()


barra = tk.Menu(raiz)
archivo = tk.Menu(barra, tearoff=0)
archivo.add_command(label="Nuevo")
archivo.add_command(label="Abrir")
archivo.add_separator()
archivo.add_command(label="Salir", command=salir)
barra.add_cascade(label="Archivo", menu=archivo)

raiz.config(menu=barra)
raiz.mainloop()
```

**Explicación línea por línea:**
- `tk.Menu(raiz)` crea la barra de menú.
- `tk.Menu(barra, tearoff=0)` crea un menú desplegable (`tearoff=0` evita que se desprenda).
- `add_command` añade una opción; `add_separator` una línea divisoria.
- `add_cascade` añade el menú a la barra.
- `raiz.config(menu=barra)` lo instala en la ventana.

### 7.2. Menú contextual

```python
menu = tk.Menu(raiz, tearoff=0)
menu.add_command(label="Copiar")
raiz.bind("<Button-3>", lambda e: menu.tk_popup(e.x_root, e.y_root))
```

**Explicación línea por línea:**
- `tk_popup(x, y)` muestra el menú en esas coordenadas.
- `<Button-3>` es el clic derecho.

### 7.3. Errores comunes

**Error 1 — `tearoff=1` por defecto** (menús desprendibles). Usa `tearoff=0`.

### 7.4. Checkpoint de comprensión

1. ¿Qué hace `add_cascade`?
2. ¿Cómo se crea un menú contextual?

### 7.5. Ejercicio propuesto

**Ejercicio 7.1.** Añade un menú "Edición" con Copiar/Pegar.

**Pista:** `add_cascade`.

---

## 8. Diálogos (messagebox, filedialog, colorchooser)

### 8.1. Diálogos listos para usar

Tkinter ofrece diálogos estándar: mensajes, selección de archivos y colores.

```python
import tkinter as tk
from tkinter import colorchooser, filedialog, messagebox

raiz = tk.Tk()


def abrir_archivo():
    ruta = filedialog.askopenfilename(filetypes=[("Texto", "*.txt")])
    if ruta:
        messagebox.showinfo("Archivo", ruta)


def elegir_color():
    color = colorchooser.askcolor(title="Elige un color")
    print(color)


tk.Button(raiz, text="Abrir", command=abrir_archivo).pack()
tk.Button(raiz, text="Color", command=elegir_color).pack()

raiz.mainloop()
```

**Explicación línea por línea:**
- `filedialog.askopenfilename(...)` abre el diálogo de selección de archivo y devuelve la ruta.
- `colorchooser.askcolor()` devuelve el color elegido.
- `messagebox.showinfo(...)` muestra un mensaje.

### 8.2. Tipos de messagebox

```python
messagebox.showinfo("Info", "Mensaje")
messagebox.showwarning("Aviso", "Cuidado")
messagebox.showerror("Error", "Falló")
messagebox.askyesno("Pregunta", "¿Continuar?")
```

### 8.3. Errores comunes

**Error 1 — No comprobar el valor devuelto** de `askopenfilename` (puede ser `""` si el usuario cancela).

### 8.4. Checkpoint de comprensión

1. ¿Qué devuelve `askopenfilename` si el usuario cancela?
2. ¿Para qué sirve `askyesno`?

### 8.5. Ejercicio propuesto

**Ejercicio 8.1.** Pide confirmación antes de borrar una lista.

**Pista:** `askyesno`.

---

## 9. Canvas y dibujo

### 9.1. Un lienzo para dibujar

`Canvas` es un área para dibujar formas, imágenes y gráficos:

```python
import tkinter as tk

raiz = tk.Tk()
canvas = tk.Canvas(raiz, width=300, height=200, bg="white")
canvas.pack()

canvas.create_line(0, 0, 300, 200, fill="blue", width=2)
canvas.create_rectangle(50, 50, 150, 120, outline="red")
canvas.create_oval(180, 50, 260, 120, fill="green")
canvas.create_text(150, 160, text="Canvas", font=("Arial", 14))

raiz.mainloop()
```

**Explicación línea por línea:**
- `create_line(x1, y1, x2, y2)` dibuja una línea.
- `create_rectangle(x1, y1, x2, y2)` un rectángulo.
- `create_oval(...)` una elipse/círculo.
- `create_text(x, y, text=...)` texto.

### 9.2. Mover elementos

```python
circulo = canvas.create_oval(10, 10, 50, 50, fill="orange")
canvas.move(circulo, 5, 0)
canvas.after(100, lambda: canvas.move(circulo, -5, 0))
```

**Explicación línea por línea:**
- Los métodos devuelven un **id** del elemento.
- `canvas.move(id, dx, dy)` lo desplaza.
- `canvas.after(ms, funcion)` programa una función para después (para animar).

### 9.3. Errores comunes

**Error 1 — No guardar el id** si vas a modificar el elemento.

**Error 2 — Redibujar todo** en lugar de mover.

### 9.4. Checkpoint de comprensión

1. ¿Qué es un Canvas?
2. ¿Cómo mueves un elemento?

### 9.5. Ejercicio propuesto

**Ejercicio 9.1.** Dibuja un reloj analógico simple con líneas.

**Pista:** usa `create_line` y `after` para animar.

---

## 10. Frames y organización

### 10.1. Agrupar widgets

Los `Frame` agrupan widgets y dividen la ventana en zonas:

```python
import tkinter as tk

raiz = tk.Tk()
raiz.title("Con frames")

superior = tk.Frame(raiz, bg="lightblue", height=50)
superior.pack(fill="x")

inferior = tk.Frame(raiz, bg="lightgray")
inferior.pack(fill="both", expand=True)

tk.Label(superior, text="Cabecera", bg="lightblue").pack()
tk.Button(inferior, text="Botón inferior").pack()

raiz.mainloop()
```

**Explicación línea por línea:**
- `tk.Frame(...)` crea un contenedor.
- `pack(fill="x")` lo estira horizontalmente.
- `fill="both", expand=True` lo hace ocupar el espacio restante.

### 10.2. PanedWindow

```python
panel = tk.PanedWindow(raiz, orient="horizontal")
panel.pack(fill="both", expand=True)
izq = tk.Frame(panel, bg="white")
der = tk.Frame(panel, bg="gray")
panel.add(izq)
panel.add(der)
```

**Explicación línea por línea:**
- `PanedWindow` crea paneles ajustables por el usuario.

### 10.3. Errores comunes

**Error 1 — No usar frames** y tener un `grid` inmanejable.

### 10.4. Checkpoint de comprensión

1. ¿Para qué sirve un `Frame`?
2. ¿Qué diferencia hay entre `Frame` y `PanedWindow`?

### 10.5. Ejercicio propuesto

**Ejercicio 10.1.** Divide la ventana en cabecera, cuerpo y pie con frames.

**Pista:** `pack` con `fill` y `expand`.

---

## 11. Notebook (pestañas)

### 11.1. Interfaces con pestañas

`ttk.Notebook` crea pestañas:

```python
import tkinter as tk
from tkinter import ttk

raiz = tk.Tk()
raiz.title("Pestañas")

notebook = ttk.Notebook(raiz)
notebook.pack(fill="both", expand=True)

tab1 = ttk.Frame(notebook)
tab2 = ttk.Frame(notebook)
notebook.add(tab1, text="General")
notebook.add(tab2, text="Avanzado")

ttk.Label(tab1, text="Opciones generales").pack()
ttk.Label(tab2, text="Opciones avanzadas").pack()

raiz.mainloop()
```

**Explicación línea por línea:**
- `ttk.Notebook(...)` crea el contenedor de pestañas.
- `notebook.add(frame, text="...")` añade una pestaña.
- Los widgets se añaden a la **pestaña** (el frame), no al notebook.

### 11.2. Errores comunes

**Error 1 — Olvidar importar `ttk`.**

**Error 2 — Añadir widgets al `Notebook`** en lugar de a la pestaña.

### 11.3. Checkpoint de comprensión

1. ¿Qué hace `Notebook.add`?
2. ¿Dónde se añaden los widgets de una pestaña?

### 11.4. Ejercicio propuesto

**Ejercicio 11.1.** Crea tres pestañas con contenido distinto.

**Pista:** `notebook.add(frame, text="...")`.

---

## 12. Treeview y tablas

### 12.1. Mostrar datos tabulares

`ttk.Treeview` muestra datos en tabla (y jerárquicos):

```python
import tkinter as tk
from tkinter import ttk

raiz = tk.Tk()
raiz.title("Tabla")

columnas = ("nombre", "edad")
tabla = ttk.Treeview(raiz, columns=columnas, show="headings")
tabla.heading("nombre", text="Nombre")
tabla.heading("edad", text="Edad")
tabla.pack(fill="both", expand=True)

tabla.insert("", "end", values=("Ana", 30))
tabla.insert("", "end", values=("Luis", 25))

raiz.mainloop()
```

**Explicación línea por línea:**
- `columns=(...)` define las columnas.
- `show="headings"` oculta la columna del árbol.
- `tabla.insert("", "end", values=(...))` añade una fila.

### 12.2. Selección y eventos

```python
def al_seleccionar(event):
    item = tabla.selection()[0]
    print(tabla.item(item, "values"))


tabla.bind("<<TreeviewSelect>>", al_seleccionar)
```

**Explicación línea por línea:**
- `<<TreeviewSelect>>` se dispara al seleccionar una fila.
- `tabla.item(item, "values")` devuelve los valores de la fila.

### 12.3. Errores comunes

**Error 1 — No definir `columns` y `show="headings"`.**

**Error 2 — No usar `scrollbar`** con muchas filas.

### 12.4. Checkpoint de comprensión

1. ¿Qué muestra un `Treeview`?
2. ¿Cómo añades una fila?

### 12.5. Ejercicio propuesto

**Ejercicio 12.1.** Añade una barra de desplazamiento a la tabla.

**Pista:** `ttk.Scrollbar` + `yscrollcommand`.

---

## 13. Estilos con ttk

### 13.1. Widgets nativos y estilos

`ttk` ofrece widgets nativos y un sistema de **estilos** (`Style`):

```python
import tkinter as tk
from tkinter import ttk

raiz = tk.Tk()
estilo = ttk.Style()
estilo.configure("Peligro.TButton", foreground="white", background="red", padding=10)
estilo.map("Peligro.TButton", background=[("active", "darkred")])

ttk.Button(raiz, text="Eliminar", style="Peligro.TButton").pack()
raiz.mainloop()
```

**Explicación línea por línea:**
- `ttk.Style()` da acceso al sistema de estilos.
- `configure("Nombre.TButton", ...)` define un estilo.
- `map(...)` define el estilo para estados concretos (por ejemplo, al pasar el ratón).
- `style="Peligro.TButton"` aplica el estilo.

### 13.2. Temas

```python
print(estilo.theme_names())
estilo.theme_use("clam")
```

**Explicación línea por línea:**
- `theme_names()` lista los temas disponibles.
- `theme_use("clam")` cambia el tema.

### 13.3. Errores comunes

**Error 1 — Mezclar `tk.Button` y `ttk.Button`.** Los estilos solo aplican a `ttk`.

### 13.4. Checkpoint de comprensión

1. ¿Qué ventaja aporta `ttk`?
2. ¿Cómo aplicas un estilo a un widget?

### 13.5. Ejercicio propuesto

**Ejercicio 13.1.** Crea un estilo "Éxito" con fondo verde.

**Pista:** `estilo.configure`.

---

## 14. Threading en Tkinter

### 14.1. El problema de bloquear la interfaz

Tkinter **no es thread-safe**: solo el hilo principal debe tocar la GUI. Si ejecutas una tarea larga en el hilo principal, la ventana **se congela**.

**Solución:** ejecuta la tarea en un hilo y actualiza la GUI con `after()` o una cola.

### 14.2. Ejemplo

```python
import threading
import time
import tkinter as tk


def tarea_larga(cola):
    for i in range(1, 6):
        time.sleep(1)
        cola.append(f"Paso {i}")
    cola.append("FIN")


def revisar_cola():
    while cola:
        etiqueta.config(text=cola.pop(0))
    raiz.after(100, revisar_cola)


raiz = tk.Tk()
cola = []
etiqueta = tk.Label(raiz, text="Inicio")
etiqueta.pack()

tk.Button(raiz, text="Comenzar", command=lambda: threading.Thread(
    target=tarea_larga, args=(cola,), daemon=True).start()).pack()

raiz.after(100, revisar_cola)
raiz.mainloop()
```

**Explicación línea por línea:**
- La tarea corre en un **hilo** aparte.
- El hilo **no** toca la GUI: solo añade mensajes a una **cola**.
- `revisar_cola` (en el hilo principal) actualiza la etiqueta cada 100 ms con `after`.
- Así la ventana nunca se congela.

### 14.3. Errores comunes

**Error 1 — Actualizar widgets desde el hilo secundario** → cuelgues.

**Error 2 — Bloquear `mainloop`** con bucles largos.

### 14.4. Checkpoint de comprensión

1. ¿Por qué no se puede tocar la GUI desde otro hilo?
2. ¿Qué papel juega `after`?

### 14.5. Ejercicio propuesto

**Ejercicio 14.1.** Muestra una barra de progreso que avance desde un hilo.

**Pista:** usa `ttk.Progressbar` y `after`.

---

## 15. Empaquetado con PyInstaller

### 15.1. Convertir en ejecutable

**PyInstaller** convierte tu app en un **ejecutable autónomo** que funciona sin Python instalado.

```bash
pip install pyinstaller
pyinstaller --onefile --windowed mi_app.py
```

**Explicación línea por línea:**
- `--onefile` genera un único ejecutable.
- `--windowed` evita que se abra una consola.

### 15.2. Recursos e iconos

```bash
pyinstaller --onefile --windowed --icon=app.ico --add-data "datos.json:." mi_app.py
```

**Explicación línea por línea:**
- `--icon=app.ico` pone un icono.
- `--add-data` incluye archivos adicionales.

### 15.3. Errores comunes

**Error 1 — Rutas relativas** que fallan al empaquetar.

*Solución:* usa `sys._MEIPASS` para localizar recursos empaquetados.

**Error 2 — Antivirus** que marcan falsos positivos.

### 15.4. Checkpoint de comprensión

1. ¿Qué hace `--onefile`?
2. ¿Por qué fallan las rutas relativas al empaquetar?

### 15.5. Ejercicio propuesto

**Ejercicio 15.1.** Empaqueta la app de saludo en un ejecutable.

**Pista:** `--onefile --windowed`.

---

## 16. Buenas prácticas y patrones MVC

### 16.1. Separar lógica de interfaz

El patrón **MVC** separa **Modelo** (datos/lógica), **Vista** (widgets) y **Controlador** (eventos). Facilita el mantenimiento y los tests.

```python
import tkinter as tk


class Modelo:
    def __init__(self) -> None:
        self.valor = 0

    def incrementar(self) -> int:
        self.valor += 1
        return self.valor


class Vista:
    def __init__(self, raiz) -> None:
        self.label = tk.Label(raiz, text="0")
        self.label.pack()
        self.boton = tk.Button(raiz, text="+1")
        self.boton.pack()


class Controlador:
    def __init__(self, modelo, vista) -> None:
        self.modelo = modelo
        self.vista = vista
        self.vista.boton.config(command=self.incrementar)

    def incrementar(self) -> None:
        self.vista.label.config(text=str(self.modelo.incrementar()))


raiz = tk.Tk()
Controlador(Modelo(), Vista(raiz))
raiz.mainloop()
```

**Explicación línea por línea:**
- `Modelo` guarda el estado y la lógica.
- `Vista` construye los widgets.
- `Controlador` conecta ambos: el botón llama al modelo y actualiza la vista.

### 16.2. Buenas prácticas

1. **Separa lógica de UI.**
2. **Usa `ttk`** para look nativo.
3. **No bloquees `mainloop`.**
4. **Valida entradas** del usuario.
5. **Organiza con frames** y módulos.
6. **Usa variables de control** en lugar de leer widgets directamente.
7. **Cierra recursos** y usa `daemon=True` en hilos.

### 16.3. Ejercicio propuesto

**Ejercicio 16.1.** Refactoriza la calculadora del ejercicio 3.1 con MVC.

**Pista:** el modelo hace los cálculos; la vista solo muestra.

---

## 17. Recursos

- **Documentación oficial:** <https://docs.python.org/3/library/tkinter.html>
- **TkDocs:** <https://tkdocs.com/tutorial/index.html>
- **ttk widgets:** <https://docs.python.org/3/library/tkinter.ttk.html>
- **PyInstaller:** <https://pyinstaller.org/>
- **Libro:** *Python GUI Programming with Tkinter*, Alan Moore.

---

## 18. Mini resumen final

- Tkinter es la GUI **estándar** de Python, incluida y multiplataforma.
- Los **widgets** cubren etiquetas, botones, entradas, tablas y canvas.
- `pack`, `grid` y `place` colocan los widgets (no los mezcles).
- Los **eventos** y las **variables de control** conectan UI y datos.
- `ttk` aporta estilos nativos; `Treeview` y `Notebook` enriquecen la UI.
- El **threading** requiere actualizar la GUI solo desde el hilo principal.
- **PyInstaller** empaqueta la app en un ejecutable.
- El patrón **MVC** mantiene el código limpio.

---

### 🎯 Retos opcionales

1. **Reto 1:** Crea una agenda de contactos con Treeview y persistencia JSON.
2. **Reto 2:** Añade una barra de progreso en un hilo para una tarea simulada.
3. **Reto 3:** Empaqueta la agenda con PyInstaller y un icono.

**Anterior:** [15_selenium.md](15_selenium.md) · **Siguiente:** [17_pyside6.md](17_pyside6.md)
