# 17 · PySide6

**Versión recomendada:** PySide6 6.7 · Qt 6 · Python 3.12
**Requisito:** [16_tkinter.md](16_tkinter.md)
**Nivel:** Intermedio → Avanzado
**Duración estimada de estudio:** 15–22 horas

---

## Índice de contenidos

1. [Introducción a Qt y PySide6](#1-introducción-a-qt-y-pyside6)
2. [Instalación y configuración](#2-instalación-y-configuración)
3. [PyQt vs PySide](#3-diferencias-entre-pyqt-y-pyside)
4. [QApplication y ventanas principales](#4-qapplication-y-ventanas-principales)
5. [Widgets básicos](#5-widgets-básicos)
6. [Layouts](#6-layouts-qvboxlayout-qhboxlayout-qgridlayout)
7. [Señales y slots](#7-señales-y-slots)
8. [Eventos](#8-eventos)
9. [Diálogos](#9-diálogos)
10. [Menús, barras y acciones](#10-menús-barras-de-herramientas-y-acciones)
11. [Modelos y vistas](#11-modelos-y-vistas-qlistview-qtableview-qtreeview)
12. [Modelo MVC en Qt](#12-modelo-mvc-en-qt)
13. [QML y Qt Quick](#13-qml-y-qt-quick)
14. [Estilos y QSS](#14-estilos-y-qss)
15. [Gráficos con QtCharts](#15-gráficos-con-qtcharts)
16. [Multithreading con QThread](#16-multithreading-con-qthread)
17. [Networking](#17-networking)
18. [Bases de datos con QtSql](#18-bases-de-datos-con-qtsql)
19. [Multimedia](#19-multimedia)
20. [Internacionalización](#20-internacionalización)
21. [Empaquetado y distribución](#21-empaquetado-y-distribución)
22. [Qt Designer y archivos .ui](#22-qt-designer-y-archivos-ui)
23. [Buenas prácticas](#23-buenas-prácticas)
24. [Recursos](#24-recursos)
25. [Mini resumen final](#25-mini-resumen-final)

---

## 1. Introducción a Qt y PySide6

### 1.1. ¿Qué es Qt?

**Qt** es un framework para aplicaciones de escritorio (y móviles/embebidos) escrito en C++. Es uno de los más maduros y potentes que existen: lo usan programas como VLC, Autodesk Maya o KDE.

**PySide6** son los *bindings* oficiales de Qt 6 para Python, mantenidos por el propio proyecto Qt. Te permite usar toda la potencia de Qt desde Python.

### 1.2. ¿Qué ofrece Qt?

- Widgets nativos y modernos.
- Sistema de **señales y slots** (eventos).
- **Layouts** potentes y adaptables.
- **Modelos/vistas** para grandes cantidades de datos.
- Gráficos, multimedia, redes, bases de datos.
- **QML**, un lenguaje declarativo para interfaces fluidas.

### 1.3. ¿Cuándo usarlo?

| Escenario | ¿PySide6? |
|---|---|
| Aplicación de escritorio profesional | Sí |
| Interfaces complejas con muchas vistas | Sí |
| Apps multiplataforma con buen rendimiento | Sí |
| Herramienta interna muy simple | Mejor Tkinter |

### 1.4. Errores comunes

**Error 1 — Elegir PySide6 para una app mínima.**

Tiene más complejidad que Tkinter. *Solución:* valora si necesitas su potencia.

**Error 2 — Ignorar el modelo/vista** y llenar de código la UI.

### 1.5. Checkpoint de comprensión

1. ¿Qué es Qt y qué son los bindings?
2. ¿Qué ventaja tiene PySide6 frente a Tkinter?

### 1.6. Ejercicio propuesto

**Ejercicio 1.1.** Enumera tres apps comerciales construidas con Qt.

**Pista:** piensa en herramientas de escritorio conocidas.

---

## 2. Instalación y configuración

### 2.1. Instalación

```bash
pip install PySide6
```

### 2.2. Primera ventana

```python
import sys
from PySide6.QtWidgets import QApplication, QLabel

app = QApplication(sys.argv)

etiqueta = QLabel("¡Hola, PySide6!")
etiqueta.resize(300, 100)
etiqueta.show()

sys.exit(app.exec())
```

**Explicación línea por línea:**
- `QApplication(sys.argv)` crea la aplicación (gestiona el bucle de eventos). **Debe existir una sola.**
- `QLabel(...)` es un widget de texto.
- `.resize(...)` le da tamaño.
- `.show()` lo hace visible.
- `app.exec()` entra en el bucle de eventos (equivale a `mainloop` en Tkinter).
- `sys.exit(...)` asegura que el código de salida se propague.

### 2.3. Errores comunes

**Error 1 — No crear `QApplication`** antes de los widgets.

**Error 2 — Olvidar `sys.exit(app.exec())`** y no liberar recursos.

### 2.4. Checkpoint de comprensión

1. ¿Qué hace `QApplication`?
2. ¿Qué diferencia hay entre `show()` y `exec()`?

### 2.5. Ejercicio propuesto

**Ejercicio 2.1.** Crea una ventana con tu nombre en el título.

**Pista:** `setWindowTitle`.

---

## 3. Diferencias entre PyQt y PySide

### 3.1. Dos bindings de Qt

| Criterio | PyQt6 | PySide6 |
|---|---|---|
| Desarrollador | Riverbank | Qt Company |
| Licencia | GPL/comercial | **LGPL** (más permisiva) |
| Nombres de señales | `pyqtSignal` | `Signal` |
| Compatibilidad | Muy amplia | Oficial |
| API | Casi idéntica | Casi idéntica |

En este manual usamos **PySide6** por su licencia permisiva y soporte oficial.

### 3.2. La diferencia en el código

```python
# PyQt6
from PyQt6.QtCore import pyqtSignal

# PySide6
from PySide6.QtCore import Signal
```

**Explicación línea por línea:**
- La API es casi idéntica; cambian algunos nombres y los prefijos de importación.

### 3.3. Errores comunes

**Error 1 — Copiar código de PyQt sin cambiar los imports.**

**Error 2 — Mezclar PyQt y PySide** en el mismo proyecto.

### 3.4. Checkpoint de comprensión

1. ¿Qué diferencia de licencia hay entre PyQt y PySide?
2. ¿Por qué elegimos PySide6 en este manual?

### 3.5. Ejercicio propuesto

**Ejercicio 3.1.** Convierte un ejemplo de PyQt6 a PySide6.

**Pista:** cambia `pyqtSignal` por `Signal`.

---

## 4. QApplication y ventanas principales

### 4.1. La jerarquía de ventanas

- `QApplication`: el bucle de eventos (uno por proceso).
- `QWidget`: base de todos los widgets.
- `QMainWindow`: ventana con menú, barras de herramientas y área central.

### 4.2. Una ventana principal

```python
import sys
from PySide6.QtWidgets import QApplication, QLabel, QMainWindow


class VentanaPrincipal(QMainWindow):
    def __init__(self) -> None:
        super().__init__()
        self.setWindowTitle("Mi App")
        self.resize(600, 400)
        self.setCentralWidget(QLabel("Contenido central"))


app = QApplication(sys.argv)
ventana = VentanaPrincipal()
ventana.show()
sys.exit(app.exec())
```

**Explicación línea por línea:**
- Heredamos de `QMainWindow` para tener menús y barras.
- `super().__init__()` inicializa la base (patrón de herencia del archivo 05).
- `setCentralWidget(...)` define el widget central.
- `resize(600, 400)` establece el tamaño.

### 4.3. Errores comunes

**Error 1 — Instanciar varios `QApplication`.**

**Error 2 — Usar `QWidget` cuando necesitas menús/barras** (usa `QMainWindow`).

### 4.4. Checkpoint de comprensión

1. ¿Qué añade `QMainWindow` sobre `QWidget`?
2. ¿Qué hace `setCentralWidget`?

### 4.5. Ejercicio propuesto

**Ejercicio 4.1.** Crea una `QMainWindow` con un `QLabel` central.

**Pista:** `setCentralWidget`.

---

## 5. Widgets básicos

### 5.1. El catálogo de widgets

| Widget | Uso |
|---|---|
| `QLabel` | Texto/imagen |
| `QPushButton` | Botón |
| `QLineEdit` | Entrada de una línea |
| `QTextEdit` | Texto enriquecido |
| `QCheckBox` | Casilla |
| `QRadioButton` | Opción única |
| `QComboBox` | Desplegable |
| `QSpinBox` | Entero con flechas |
| `QSlider` | Deslizador |
| `QProgressBar` | Progreso |

### 5.2. Un formulario

```python
import sys
from PySide6.QtWidgets import (
    QApplication, QComboBox, QLineEdit, QPushButton, QVBoxLayout, QWidget,
)


class Ventana(QWidget):
    def __init__(self) -> None:
        super().__init__()
        self.setWindowTitle("Widgets")

        layout = QVBoxLayout(self)
        self.entrada = QLineEdit()
        self.entrada.setPlaceholderText("Escribe algo")
        self.combo = QComboBox()
        self.combo.addItems(["Opción A", "Opción B"])
        self.boton = QPushButton("Mostrar")

        layout.addWidget(self.entrada)
        layout.addWidget(self.combo)
        layout.addWidget(self.boton)

        self.boton.clicked.connect(self.mostrar)

    def mostrar(self) -> None:
        print(self.entrada.text(), self.combo.currentText())


app = QApplication(sys.argv)
ventana = Ventana()
ventana.show()
sys.exit(app.exec())
```

**Explicación línea por línea:**
- `QVBoxLayout(self)` organiza los widgets en vertical.
- `addWidget(...)` añade cada widget al layout.
- `setPlaceholderText(...)` muestra un texto de ayuda.
- `self.boton.clicked.connect(self.mostrar)` conecta la señal `clicked` al método `mostrar` (sección 7).
- `.text()` y `.currentText()` leen los valores.

### 5.3. Errores comunes

**Error 1 — No añadir los widgets al layout.**

*Síntoma:* no aparecen. *Solución:* `layout.addWidget(...)`.

### 5.4. Checkpoint de comprensión

1. ¿Qué hace un layout?
2. ¿Cómo lees el texto de un `QLineEdit`?

### 5.5. Ejercicio propuesto

**Ejercicio 5.1.** Crea un formulario con `QLineEdit`, `QSpinBox` y un botón.

**Pista:** usa `QVBoxLayout`.

---

## 6. Layouts (QVBoxLayout, QHBoxLayout, QGridLayout)

### 6.1. Organizar sin coordenadas

Los **layouts** organizan widgets y se adaptan al redimensionar:

- `QVBoxLayout`: vertical.
- `QHBoxLayout`: horizontal.
- `QGridLayout`: rejilla.
- `QFormLayout`: formularios.
- `QStackedLayout`: varias páginas.

### 6.2. `QGridLayout`

```python
import sys
from PySide6.QtWidgets import QApplication, QGridLayout, QLabel, QLineEdit, QWidget

app = QApplication(sys.argv)
ventana = QWidget()
grid = QGridLayout(ventana)

grid.addWidget(QLabel("Nombre:"), 0, 0)
grid.addWidget(QLineEdit(), 0, 1)
grid.addWidget(QLabel("Email:"), 1, 0)
grid.addWidget(QLineEdit(), 1, 1)

ventana.show()
sys.exit(app.exec())
```

**Explicación línea por línea:**
- `addWidget(widget, fila, columna)` coloca en la rejilla.

### 6.3. Anidar layouts

```python
from PySide6.QtWidgets import QHBoxLayout, QVBoxLayout

principal = QVBoxLayout()
fila = QHBoxLayout()
fila.addWidget(QLabel("A"))
fila.addWidget(QLabel("B"))
principal.addLayout(fila)
```

**Explicación línea por línea:**
- Se pueden anidar layouts con `addLayout`.

### 6.4. Errores comunes

**Error 1 — Usar posiciones absolutas** (`move`) en lugar de layouts.

**Error 2 — No asignar el layout al contenedor.**

### 6.5. Checkpoint de comprensión

1. ¿Qué ventaja aporta un layout frente a posiciones fijas?
2. ¿Cómo anidas layouts?

### 6.6. Ejercicio propuesto

**Ejercicio 6.1.** Crea una calculadora con `QGridLayout` (4x4).

**Pista:** botones en cada celda.

---

## 7. Señales y slots

### 7.1. El corazón de Qt

El sistema **señales y slots** es lo que hace a Qt tan potente. Un widget **emite** una señal (`clicked`, `textChanged`) y un slot (una función) la **recibe**. Emisor y receptor no se conocen: están **desacoplados**.

**Analogía:** es como una emisora de radio (señal) y receptores (slots). La emisora no sabe quién escucha; solo emite. Los receptores sintonizan si quieren.

### 7.2. Un ejemplo

```python
import sys
from PySide6.QtWidgets import QApplication, QLabel, QLineEdit, QVBoxLayout, QWidget

app = QApplication(sys.argv)
ventana = QWidget()
layout = QVBoxLayout(ventana)

entrada = QLineEdit()
etiqueta = QLabel("Escribe...")

entrada.textChanged.connect(lambda texto: etiqueta.setText(texto.upper()))

layout.addWidget(entrada)
layout.addWidget(etiqueta)
ventana.show()
sys.exit(app.exec())
```

**Explicación línea por línea:**
- `textChanged` es una señal que emite el nuevo texto cada vez que cambia.
- `.connect(...)` conecta la señal a un slot (aquí, una lambda).
- Cada vez que escribes, la etiqueta se actualiza en mayúsculas.

### 7.3. Señales personalizadas

```python
from PySide6.QtCore import QObject, Signal


class Emisor(QObject):
    mi_senal = Signal(str)


emisor = Emisor()
emisor.mi_senal.connect(lambda msg: print("Recibido:", msg))
emisor.mi_senal.emit("Hola")
```

Salida esperada:

```text
Recibido: Hola
```

**Explicación línea por línea:**
- `Signal(str)` define una señal que transporta un texto.
- `.emit(...)` la emite.
- `.connect(...)` la recibe.

### 7.4. Errores comunes

**Error 1 — Conectar una señal a un método que no existe.**

**Error 2 — Olvidar desconectar** señales de objetos destruidos.

### 7.5. Checkpoint de comprensión

1. ¿Qué es una señal? ¿Y un slot?
2. ¿Por qué el desacoplamiento es una ventaja?

### 7.6. Ejercicio propuesto

**Ejercicio 7.1.** Crea una señal personalizada que emita un número y muéstralo en un `QLabel`.

**Pista:** `Signal(int)`.

---

## 8. Eventos

### 8.1. Sobrescribir manejadores de eventos

Los **eventos** (ratón, teclado, pintura, redimensionado) se capturan sobrescribiendo métodos `event*`:

```python
import sys
from PySide6.QtWidgets import QApplication, QWidget


class Ventana(QWidget):
    def mousePressEvent(self, event):
        print(f"Clic en ({event.position().x()}, {event.position().y()})")

    def keyPressEvent(self, event):
        print(f"Tecla: {event.key()}")


app = QApplication(sys.argv)
ventana = Ventana()
ventana.show()
sys.exit(app.exec())
```

**Explicación línea por línea:**
- `mousePressEvent` se llama al pulsar el ratón.
- `keyPressEvent` al pulsar una tecla.
- `event.position()` da las coordenadas.

### 8.2. Eventos comunes

| Método | Evento |
|---|---|
| `mousePressEvent` | Clic de ratón |
| `keyPressEvent` | Tecla |
| `paintEvent` | Redibujado |
| `resizeEvent` | Cambio de tamaño |
| `closeEvent` | Cierre |

### 8.3. Errores comunes

**Error 1 — Olvidar llamar a `super().metodo(event)`** cuando haga falta.

**Error 2 — Hacer trabajo pesado en `paintEvent`.**

### 8.4. Checkpoint de comprensión

1. ¿Cómo capturas un evento de teclado?
2. ¿Por qué `paintEvent` debe ser ligero?

### 8.5. Ejercicio propuesto

**Ejercicio 8.1.** Muestra las coordenadas del ratón en el título de la ventana.

**Pista:** `mouseMoveEvent` + `setWindowTitle`.

---

## 9. Diálogos

### 9.1. Diálogos estándar

Qt incluye diálogos listos: mensajes, archivos, color, fuente, entrada.

```python
import sys
from PySide6.QtWidgets import (
    QApplication, QFileDialog, QMessageBox, QPushButton, QVBoxLayout, QWidget,
)


class Ventana(QWidget):
    def __init__(self) -> None:
        super().__init__()
        layout = QVBoxLayout(self)
        boton = QPushButton("Abrir")
        boton.clicked.connect(self.abrir)
        layout.addWidget(boton)

    def abrir(self) -> None:
        ruta, _ = QFileDialog.getOpenFileName(self, "Abrir archivo", "", "Texto (*.txt)")
        if ruta:
            QMessageBox.information(self, "Archivo", ruta)


app = QApplication(sys.argv)
Ventana().show()
sys.exit(app.exec())
```

**Explicación línea por línea:**
- `QFileDialog.getOpenFileName(...)` devuelve una tupla `(ruta, filtro)`.
- `QMessageBox.information(...)` muestra un mensaje.

### 9.2. Diálogo personalizado

```python
from PySide6.QtWidgets import QDialog, QDialogButtonBox, QLabel, QVBoxLayout


class MiDialogo(QDialog):
    def __init__(self, parent=None) -> None:
        super().__init__(parent)
        self.setWindowTitle("Diálogo")
        layout = QVBoxLayout(self)
        layout.addWidget(QLabel("¿Continuar?"))
        botones = QDialogButtonBox(QDialogButtonBox.Ok | QDialogButtonBox.Cancel)
        botones.accepted.connect(self.accept)
        botones.rejected.connect(self.reject)
        layout.addWidget(botones)
```

**Explicación línea por línea:**
- `QDialogButtonBox` crea los botones estándar.
- `accepted`/`rejected` se conectan a `accept`/`reject`.

### 9.3. Errores comunes

**Error 1 — No comprobar el resultado del diálogo.**

**Error 2 — Diálogos sin `parent`** que no se centran bien.

### 9.4. Checkpoint de comprensión

1. ¿Qué devuelve `getOpenFileName`?
2. ¿Para qué sirve `QDialogButtonBox`?

### 9.5. Ejercicio propuesto

**Ejercicio 9.1.** Pide confirmación antes de cerrar y muestra un mensaje.

**Pista:** `QMessageBox.question`.

---

## 10. Menús, barras de herramientas y acciones

### 10.1. QAction: la acción reutilizable

`QAction` representa una **acción** que puede ir en menús, barras de herramientas o atajos, evitando duplicar lógica:

```python
import sys
from PySide6.QtGui import QAction, QKeySequence
from PySide6.QtWidgets import QApplication, QMainWindow


class Ventana(QMainWindow):
    def __init__(self) -> None:
        super().__init__()
        self.setWindowTitle("Con menú")

        salir = QAction("Salir", self)
        salir.setShortcut(QKeySequence("Ctrl+Q"))
        salir.triggered.connect(self.close)

        menu = self.menuBar().addMenu("Archivo")
        menu.addAction(salir)

        barra = self.addToolBar("Principal")
        barra.addAction(salir)


app = QApplication(sys.argv)
Ventana().show()
sys.exit(app.exec())
```

**Explicación línea por línea:**
- `QAction("Salir", self)` crea la acción.
- `setShortcut(QKeySequence("Ctrl+Q"))` asigna un atajo.
- `triggered.connect(self.close)` conecta la acción a cerrar.
- La misma acción se añade al menú **y** a la barra: una sola lógica.

### 10.2. Errores comunes

**Error 1 — Duplicar lógica** en menú y barra. Usa `QAction`.

**Error 2 — Atajos en conflicto.**

### 10.3. Checkpoint de comprensión

1. ¿Qué ventaja aporta `QAction`?
2. ¿Cómo asignas un atajo?

### 10.4. Ejercicio propuesto

**Ejercicio 10.1.** Añade una acción "Abrir" con atajo `Ctrl+O`.

**Pista:** `QKeySequence("Ctrl+O")`.

---

## 11. Modelos y vistas (QListView, QTableView, QTreeView)

### 11.1. Separar datos de presentación

Qt separa **modelo** (datos) y **vista** (presentación). Esto escala a grandes conjuntos de datos.

```python
import sys
from PySide6.QtCore import QStringListModel
from PySide6.QtWidgets import QApplication, QListView

app = QApplication(sys.argv)
vista = QListView()
modelo = QStringListModel(["Ana", "Luis", "Eva"])
vista.setModel(modelo)
vista.show()
sys.exit(app.exec())
```

**Explicación línea por línea:**
- `QStringListModel` es un modelo simple de textos.
- `vista.setModel(modelo)` conecta vista y modelo.

### 11.2. Tabla con `QStandardItemModel`

```python
from PySide6.QtGui import QStandardItem, QStandardItemModel
from PySide6.QtWidgets import QTableView

modelo = QStandardItemModel()
modelo.setHorizontalHeaderLabels(["Nombre", "Edad"])
modelo.appendRow([QStandardItem("Ana"), QStandardItem("30")])
vista = QTableView()
vista.setModel(modelo)
vista.show()
```

**Explicación línea por línea:**
- `QStandardItemModel` es un modelo genérico.
- `appendRow([...])` añade una fila.

### 11.3. Errores comunes

**Error 1 — Manipular la vista en lugar del modelo.**

**Error 2 — Modelos sin datos válidos** para el rol solicitado.

### 11.4. Checkpoint de comprensión

1. ¿Qué ventaja tiene separar modelo y vista?
2. ¿Qué modelo usas para una lista simple?

### 11.5. Ejercicio propuesto

**Ejercicio 11.1.** Muestra una lista editable con `QListView`.

**Pista:** `vista.setEditTriggers(...)`.

---

## 12. Modelo MVC en Qt

### 12.1. El patrón Model/View

Qt implementa **Model/View** (variante de MVC): el modelo expone datos vía **roles**, la vista los muestra y los *delegates* personalizan la edición.

### 12.2. Modelo personalizado

```python
from PySide6.QtCore import QAbstractListModel, Qt


class TareasModel(QAbstractListModel):
    def __init__(self, tareas=None) -> None:
        super().__init__()
        self.tareas = tareas or []

    def rowCount(self, parent=None) -> int:
        return len(self.tareas)

    def data(self, index, role=Qt.DisplayRole):
        if role == Qt.DisplayRole:
            return self.tareas[index.row()]
        return None
```

**Explicación línea por línea:**
- `rowCount` indica cuántas filas hay.
- `data` devuelve el valor de una celda según el **rol** (aquí, el texto a mostrar).

### 12.3. Uso

```python
from PySide6.QtWidgets import QListView

modelo = TareasModel(["Comprar", "Estudiar"])
vista = QListView()
vista.setModel(modelo)
vista.show()
```

### 12.4. Errores comunes

**Error 1 — No emitir señales** al cambiar los datos (`dataChanged`).

**Error 2 — Ignorar los roles** de Qt.

### 12.5. Checkpoint de comprensión

1. ¿Qué es un rol en Qt?
2. ¿Qué dos métodos mínimos implementa un modelo de lista?

### 12.6. Ejercicio propuesto

**Ejercicio 12.1.** Añade un método `agregar(tarea)` al modelo y notifica a la vista.

**Pista:** usa `beginInsertRows`/`endInsertRows`.

---

## 13. QML y Qt Quick

### 13.1. Interfaces declarativas

**QML** es un lenguaje **declarativo** para interfaces fluidas y animadas, con lógica en JavaScript o Python. Es ideal para apps con muchas animaciones o táctiles.

### 13.2. Ejemplo mínimo

```qml
import QtQuick
import QtQuick.Controls

ApplicationWindow {
    visible: true
    width: 400
    height: 300
    title: "Hola QML"

    Button {
        text: "Pulsar"
        anchors.centerIn: parent
        onClicked: console.log("Pulsado")
    }
}
```

**Explicación línea por línea:**
- `ApplicationWindow` es la ventana.
- `Button` es un botón; `anchors.centerIn` lo centra.
- `onClicked` maneja el clic.

**Cargar desde Python:**

```python
import sys
from PySide6.QtGui import QGuiApplication
from PySide6.QtQml import QQmlApplicationEngine

app = QGuiApplication(sys.argv)
engine = QQmlApplicationEngine("main.qml")
sys.exit(app.exec())
```

### 13.3. Cuándo usar QML

- Interfaces con animaciones y efectos.
- Apps táctiles/móviles.
- Cuando los widgets clásicos no bastan.

### 13.4. Errores comunes

**Error 1 — Mezclar widgets y QML** sin planificación.

**Error 2 — Olvidar `visible: true`** en la ventana QML.

### 13.5. Checkpoint de comprensión

1. ¿Qué diferencia hay entre QML y los widgets clásicos?
2. ¿Cuándo elegirías QML?

### 13.6. Ejercicio propuesto

**Ejercicio 13.1.** Crea un botón en QML que cambie el texto de una etiqueta.

**Pista:** usa propiedades y `onClicked`.

---

## 14. Estilos y QSS

### 14.1. CSS para widgets

**QSS** (*Qt Style Sheets*) es un CSS para widgets Qt. Permite personalizar colores, bordes y fuentes:

```python
boton.setStyleSheet("""
    QPushButton {
        background-color: #2d89ef;
        color: white;
        border-radius: 6px;
        padding: 8px 16px;
    }
    QPushButton:hover {
        background-color: #1b5fae;
    }
""")
```

**Explicación línea por línea:**
- Los selectores son como CSS: `QPushButton` es el tipo de widget.
- `:hover` aplica al pasar el ratón.

### 14.2. Tema global

```python
app.setStyleSheet("""
    QWidget { font-size: 14px; }
    QLineEdit { border: 1px solid #ccc; padding: 4px; }
""")
```

### 14.3. Errores comunes

**Error 1 — QSS demasiado específico** que rompe estilos nativos.

**Error 2 — Abusar de estilos inline** en lugar de un archivo `.qss`.

### 14.4. Checkpoint de comprensión

1. ¿Qué es QSS?
2. ¿Cómo aplicas un estilo global?

### 14.5. Ejercicio propuesto

**Ejercicio 14.1.** Aplica un tema oscuro básico a tu app.

**Pista:** usa `app.setStyleSheet` con colores oscuros.

---

## 15. Gráficos con QtCharts

### 15.1. Gráficos integrados

**QtCharts** ofrece gráficos (líneas, barras, tartas) integrados.

```bash
pip install PySide6-QtCharts
```

```python
import sys
from PySide6.QtCharts import QChart, QChartView, QLineSeries
from PySide6.QtWidgets import QApplication, QMainWindow


class Ventana(QMainWindow):
    def __init__(self) -> None:
        super().__init__()
        serie = QLineSeries()
        serie.append(0, 6)
        serie.append(2, 4)
        serie.append(3, 8)
        serie.append(7, 4)

        chart = QChart()
        chart.addSeries(serie)
        chart.setTitle("Ejemplo")

        self.setCentralWidget(QChartView(chart))


app = QApplication(sys.argv)
Ventana().show()
sys.exit(app.exec())
```

**Explicación línea por línea:**
- `QLineSeries` es una serie de datos.
- `serie.append(x, y)` añade puntos.
- `chart.addSeries(serie)` añade la serie al gráfico.
- `QChartView(chart)` es el widget que lo muestra.

### 15.2. Errores comunes

**Error 1 — No añadir la serie al chart.**

**Error 2 — Datos mal formados** en `append`.

### 15.3. Checkpoint de comprensión

1. ¿Qué widget muestra el gráfico?
2. ¿Cómo añades puntos a una serie?

### 15.4. Ejercicio propuesto

**Ejercicio 15.1.** Crea un gráfico de barras con tres categorías.

**Pista:** usa `QBarSeries`.

---

## 16. Multithreading con QThread

### 16.1. Mantener la UI fluida

La UI debe actualizarse solo desde el **hilo principal**. Las tareas largas van en un `QThread` y comunican mediante **señales**.

```python
import sys
import time
from PySide6.QtCore import QThread, Signal
from PySide6.QtWidgets import QApplication, QLabel, QPushButton, QVBoxLayout, QWidget


class Trabajador(QThread):
    progreso = Signal(int)

    def run(self) -> None:
        for i in range(1, 11):
            time.sleep(0.5)
            self.progreso.emit(i * 10)


class Ventana(QWidget):
    def __init__(self) -> None:
        super().__init__()
        layout = QVBoxLayout(self)
        self.etiqueta = QLabel("0%")
        boton = QPushButton("Comenzar")
        boton.clicked.connect(self.iniciar)
        layout.addWidget(self.etiqueta)
        layout.addWidget(boton)
        self.hilo = Trabajador()
        self.hilo.progreso.connect(lambda v: self.etiqueta.setText(f"{v}%"))

    def iniciar(self) -> None:
        self.hilo.start()


app = QApplication(sys.argv)
Ventana().show()
sys.exit(app.exec())
```

**Explicación línea por línea:**
- `Trabajador` hereda de `QThread` y define `run` (lo que corre en el hilo).
- `progreso = Signal(int)` comunica el avance.
- El hilo **no** toca la UI: emite una señal.
- La conexión actualiza la etiqueta **en el hilo principal**.

### 16.2. Errores comunes

**Error 1 — Actualizar widgets desde el hilo** → bloqueos.

**Error 2 — Reutilizar un `QThread` ya terminado** sin recrearlo.

### 16.3. Checkpoint de comprensión

1. ¿Por qué el hilo no puede tocar la UI directamente?
2. ¿Qué papel juegan las señales?

### 16.4. Ejercicio propuesto

**Ejercicio 16.1.** Añade un botón para detener el hilo.

**Pista:** usa una bandera y `requestInterruption`.

---

## 17. Networking

### 17.1. HTTP asíncrono

Qt ofrece `QNetworkAccessManager` para HTTP asíncrono:

```python
import sys
from PySide6.QtCore import QUrl
from PySide6.QtNetwork import QNetworkAccessManager, QNetworkRequest
from PySide6.QtWidgets import QApplication


def al_terminar(respuesta) -> None:
    print(bytes(respuesta.readAll()).decode()[:200])
    app.quit()


app = QApplication(sys.argv)
manager = QNetworkAccessManager()
manager.finished.connect(al_terminar)
manager.get(QNetworkRequest(QUrl("https://example.com")))
sys.exit(app.exec())
```

**Explicación línea por línea:**
- `QNetworkAccessManager` gestiona las peticiones.
- `finished.connect(...)` se ejecuta al terminar.
- `manager.get(...)` lanza la petición.

### 17.2. Errores comunes

**Error 1 — Hacer peticiones bloqueantes** en el hilo de UI.

**Error 2 — No manejar errores de red.**

### 17.3. Checkpoint de comprensión

1. ¿Qué hace `QNetworkAccessManager`?
2. ¿Por qué es asíncrono?

### 17.4. Ejercicio propuesto

**Ejercicio 17.1.** Muestra en un `QLabel` el código de estado de una petición.

**Pista:** `respuesta.attribute(QNetworkRequest.HttpStatusCodeAttribute)`.

---

## 18. Bases de datos con QtSql

### 18.1. Acceso a bases de datos

`QtSql` ofrece acceso a bases de datos (SQLite, PostgreSQL, MySQL) con modelos listos para vistas:

```python
import sys
from PySide6.QtSql import QSqlDatabase, QSqlQuery, QSqlTableModel
from PySide6.QtWidgets import QApplication, QTableView

app = QApplication(sys.argv)
db = QSqlDatabase.addDatabase("QSQLITE")
db.setDatabaseName("app.db")
db.open()

query = QSqlQuery()
query.exec("CREATE TABLE IF NOT EXISTS personas (id INTEGER PRIMARY KEY, nombre TEXT)")
query.exec("INSERT INTO personas (nombre) VALUES ('Ana')")

modelo = QSqlTableModel()
modelo.setTable("personas")
modelo.select()

vista = QTableView()
vista.setModel(modelo)
vista.show()
sys.exit(app.exec())
```

**Explicación línea por línea:**
- `QSqlDatabase.addDatabase("QSQLITE")` registra el driver.
- `db.open()` abre la conexión.
- `QSqlTableModel` es un modelo que lee una tabla directamente.
- `modelo.select()` carga los datos.

### 18.2. Errores comunes

**Error 1 — No abrir la base** antes de consultar.

**Error 2 — Mezclar QtSql con SQLAlchemy** sin necesidad.

### 18.3. Checkpoint de comprensión

1. ¿Qué ventaja aporta `QSqlTableModel`?
2. ¿Cómo se abre una base de datos?

### 18.4. Ejercicio propuesto

**Ejercicio 18.1.** Inserta y muestra tres filas en un `QTableView`.

**Pista:** usa `QSqlTableModel` y `select()`.

---

## 19. Multimedia

### 19.1. Audio y vídeo

`QtMultimedia` reproduce audio y vídeo:

```python
import sys
from PySide6.QtCore import QUrl
from PySide6.QtMultimedia import QAudioOutput, QMediaPlayer
from PySide6.QtWidgets import QApplication


app = QApplication(sys.argv)
player = QMediaPlayer()
audio = QAudioOutput()
player.setAudioOutput(audio)
player.setSource(QUrl.fromLocalFile("cancion.mp3"))
player.play()
sys.exit(app.exec())
```

**Explicación línea por línea:**
- `QMediaPlayer` reproduce el medio.
- `QAudioOutput` gestiona la salida de audio.
- `setSource(...)` carga el archivo.

### 19.2. Errores comunes

**Error 1 — Codecs no instalados** para ciertos formatos.

**Error 2 — No gestionar el estado** del reproductor.

### 19.3. Checkpoint de comprensión

1. ¿Qué reproduce `QMediaPlayer`?
2. ¿Para qué sirve `QAudioOutput`?

### 19.4. Ejercicio propuesto

**Ejercicio 19.1.** Añade botones de play/pausa.

**Pista:** `player.play()` / `player.pause()`.

---

## 20. Internacionalización

### 20.1. Traducir la app

Qt usa archivos `.ts`/`.qm` con `tr()` para traducir:

```python
from PySide6.QtCore import QCoreApplication

texto = QCoreApplication.translate("Ventana", "Bienvenido")
```

```bash
pyside6-lupdate app.py -ts es.ts
pyside6-lrelease es.ts -qm es.qm
```

**Cargar la traducción:**

```python
from PySide6.QtCore import QTranslator

traductor = QTranslator()
traductor.load("es.qm")
app.installTranslator(traductor)
```

**Explicación línea por línea:**
- `pyside6-lupdate` extrae los textos a un archivo `.ts`.
- `pyside6-lrelease` lo compila a `.qm`.
- `app.installTranslator(...)` lo carga.

### 20.2. Errores comunes

**Error 1 — No envolver textos en `tr()`.**

**Error 2 — Olvidar cargar el `.qm`.**

### 20.3. Checkpoint de comprensión

1. ¿Qué hace `pyside6-lupdate`?
2. ¿Cómo se carga una traducción?

### 20.4. Ejercicio propuesto

**Ejercicio 20.1.** Traduce el título de tu ventana a inglés.

**Pista:** genera el `.ts` con `pyside6-lupdate`.

---

## 21. Empaquetado y distribución

### 21.1. Crear el ejecutable

Se empaqueta con **PyInstaller** o **pyside6-deploy**:

```bash
pyinstaller --onefile --windowed --name MiApp main.py
```

```bash
pip install pyside6-deploy
pyside6-deploy main.py
```

**Explicación línea por línea:**
- `--onefile` un solo ejecutable; `--windowed` sin consola.
- `pyside6-deploy` es la herramienta oficial de Qt.

### 21.2. Errores comunes

**Error 1 — Faltan plugins de Qt** al empaquetar. Usa los hooks oficiales.

**Error 2 — Tamaño del ejecutable** muy grande por incluir todo Qt.

### 21.3. Checkpoint de comprensión

1. ¿Qué dos herramientas empaquetan apps PySide6?
2. ¿Por qué el ejecutable es grande?

### 21.4. Ejercicio propuesto

**Ejercicio 21.1.** Empaqueta una app sencilla y ejecútala en otra máquina.

**Pista:** prueba `pyside6-deploy`.

---

## 22. Qt Designer y archivos .ui

### 22.1. Diseño visual

**Qt Designer** permite diseñar interfaces **visualmente** y guardarlas como `.ui` (XML). Se cargan con `QUiLoader` o se convierten a Python.

```python
import sys
from PySide6.QtUiTools import QUiLoader
from PySide6.QtWidgets import QApplication

app = QApplication(sys.argv)
loader = QUiLoader()
ventana = loader.load("ventana.ui")
ventana.show()
sys.exit(app.exec())
```

**Explicación línea por línea:**
- `QUiLoader().load("ventana.ui")` carga la interfaz diseñada.

**Convertir a Python:**

```bash
pyside6-uic ventana.ui -o ui_ventana.py
```

### 22.2. Errores comunes

**Error 1 — Editar el `.py` generado** y perder cambios al reconvertir.

*Solución:* separa lógica y UI.

**Error 2 — No distribuir el `.ui`** si lo cargas en runtime.

### 22.3. Checkpoint de comprensión

1. ¿Qué es Qt Designer?
2. ¿Qué hace `pyside6-uic`?

### 22.4. Ejercicio propuesto

**Ejercicio 22.1.** Diseña un formulario en Qt Designer y cárgalo en Python.

**Pista:** usa `QUiLoader`.

---

## 23. Buenas prácticas

1. **Separa lógica de UI** (modelo/vista o MVC).
2. **Usa layouts**, no posiciones absolutas.
3. **Señales y slots** para comunicar componentes.
4. **QThread** para tareas largas.
5. **QSS** centralizado para estilos.
6. **Modelos** para datos grandes, no listas de widgets.
7. **`tr()`** para todo texto traducible.
8. **Empaqueta con los hooks oficiales.**
9. **Una sola `QApplication`.**
10. **No actualices la UI desde hilos.**

---

## 24. Recursos

- **Documentación oficial:** <https://doc.qt.io/qtforpython-6/>
- **Qt Examples:** <https://doc.qt.io/qtforpython-6/examples/index.html>
- **Qt Designer:** <https://doc.qt.io/qt-6/qtdesigner-manual.html>
- **QML:** <https://doc.qt.io/qt-6/qmlapplications.html>
- **Libro:** *Create GUI Applications with Python & Qt6*, Martin Fitzpatrick.

---

## 25. Mini resumen final

- **PySide6** son los bindings oficiales de **Qt 6** para Python.
- `QApplication` gestiona el bucle de eventos; `QMainWindow` la ventana principal.
- Los **widgets** y **layouts** construyen la interfaz adaptable.
- **Señales y slots** desacoplan componentes; los **eventos** capturan interacción.
- **Modelos/vistas** escalan a grandes datos; **QML** permite UIs declarativas.
- **QSS** estiliza; **QtCharts**, **QtSql** y **QtMultimedia** amplían capacidades.
- **QThread** mantiene la UI fluida.
- **Qt Designer** y **PyInstaller/pyside6-deploy** aceleran diseño y distribución.

---

### 🎯 Retos opcionales

1. **Reto 1:** Crea un gestor de tareas con `QTableView` y persistencia SQLite vía QtSql.
2. **Reto 2:** Añade un gráfico QtCharts que se actualice desde un QThread.
3. **Reto 3:** Diseña la interfaz en Qt Designer y conéctala a la lógica.

**Anterior:** [16_tkinter.md](16_tkinter.md) · **Siguiente (Parte V):** [18_numpy.md](18_numpy.md)
