# 21 · OpenCV

**Versión recomendada:** OpenCV 4.10 (opencv-python) · Python 3.12
**Requisito:** [20_scikit_learn.md](20_scikit_learn.md)
**Nivel:** Intermedio → Avanzado
**Duración estimada de estudio:** 15–22 horas

---

## Índice de contenidos

1. [Introducción a OpenCV y visión por computadora](#1-introducción-a-opencv-y-visión-por-computadora)
2. [Instalación y configuración](#2-instalación-y-configuración)
3. [Lectura, escritura y visualización de imágenes](#3-lectura-escritura-y-visualización-de-imágenes)
4. [Espacios de color](#4-espacios-de-color-bgr-rgb-hsv-escala-de-grises)
5. [Operaciones básicas con imágenes](#5-operaciones-básicas-con-imágenes)
6. [ROI y manipulación de píxeles](#6-roi-y-manipulación-de-píxeles)
7. [Dibujo sobre imágenes](#7-dibujo-sobre-imágenes)
8. [Transformaciones geométricas](#8-transformaciones-geométricas)
9. [Filtros y convoluciones](#9-filtros-y-convoluciones)
10. [Detección de bordes](#10-detección-de-bordes-canny-sobel-laplaciano)
11. [Umbralización](#11-umbralización-thresholding)
12. [Morfología matemática](#12-morfología-matemática)
13. [Detección de contornos](#13-detección-de-contornos)
14. [Detección de líneas y círculos](#14-detección-de-líneas-y-círculos-hough)
15. [Detección de esquinas](#15-detección-de-esquinas-harris-shi-tomasi)
16. [Características y descriptores](#16-características-y-descriptores-sift-orb-surf)
17. [Matching de características](#17-matching-de-características)
18. [Homografía y transformaciones](#18-homografía-y-transformaciones)
19. [Detección de rostros](#19-detección-de-rostros-haar-cascades-dnn)
20. [Detección de objetos](#20-detección-de-objetos)
21. [Tracking de objetos](#21-tracking-de-objetos)
22. [Procesamiento de video](#22-procesamiento-de-video)
23. [Captura desde webcam](#23-captura-desde-webcam)
24. [Calibración de cámara](#24-calibración-de-cámara)
25. [Estereovisión](#25-estereovisión)
26. [Aprendizaje profundo con OpenCV DNN](#26-aprendizaje-profundo-con-opencv-dnn)
27. [Integración con NumPy](#27-integración-con-numpy)
28. [Aplicaciones prácticas](#28-aplicaciones-prácticas)
29. [Buenas prácticas](#29-buenas-prácticas)
30. [Recursos](#30-recursos)
31. [Mini resumen final](#31-mini-resumen-final)

---

## 1. Introducción a OpenCV y visión por computadora

### 1.1. ¿Qué es la visión por computadora?

La **visión por computadora** es la disciplina que enseña a las máquinas a **interpretar imágenes y vídeos**: detectar objetos, reconocer caras, medir distancias, seguir movimientos. Es la contrapartida artificial de la visión humana.

**OpenCV** (*Open Source Computer Vision Library*) es la librería de visión por computadora más usada del mundo. Permite procesar imágenes y vídeo, detectar bordes y formas, y ejecutar redes neuronales.

### 1.2. La clave: las imágenes son arrays

Aquí está la idea fundamental que conecta OpenCV con todo lo aprendido: **una imagen es un array NumPy** (archivo 18). Su forma es `(alto, ancho, canales)`.

- Una imagen en color tiene **3 canales** (B, G, R).
- Una imagen en escala de grises tiene **1 canal**.
- Cada valor es un número entre 0 y 255 (uint8).

**Consecuencia:** todas las operaciones de NumPy (slicing, máscaras, vectorización) funcionan sobre imágenes. Eso hace a OpenCV rapidísimo.

### 1.3. Errores comunes

**Error 1 — Confundir el orden de canales.** OpenCV usa **BGR**, no RGB.

**Error 2 — Usar OpenCV para tareas de deep learning complejas** en vez de PyTorch.

### 1.4. Checkpoint de comprensión

1. ¿Qué es una imagen para OpenCV?
2. ¿Qué significa BGR?

### 1.5. Ejercicio propuesto

**Ejercicio 1.1.** Enumera tres aplicaciones reales de visión por computadora.

**Pista:** control de calidad, seguridad, conducción autónoma.

---

## 2. Instalación y configuración

### 2.1. Instalación

```bash
pip install opencv-python          # módulos principales
pip install opencv-contrib-python  # con módulos extra (SIFT, trackers)
```

### 2.2. Verificación

```python
import cv2

print(cv2.__version__)
```

Salida esperada:

```text
4.10.0
```

### 2.3. Errores comunes

**Error 1 — Instalar ambas variantes** (`opencv-python` y `opencv-contrib-python`) → conflictos.

**Error 2 — Falta de `libGL`** en servidores sin GUI. Usa `opencv-python-headless`.

### 2.4. Checkpoint de comprensión

1. ¿Qué añade `opencv-contrib-python`?
2. ¿Cuándo usarías la versión headless?

### 2.5. Ejercicio propuesto

**Ejercicio 2.1.** Instala OpenCV y muestra su versión.

**Pista:** `cv2.__version__`.

---

## 3. Lectura, escritura y visualización de imágenes

### 3.1. Las tres operaciones básicas

```python
import cv2

img = cv2.imread("foto.jpg")
print(img.shape)   # (alto, ancho, canales)

cv2.imshow("Foto", img)
cv2.waitKey(0)
cv2.destroyAllWindows()

cv2.imwrite("copia.png", img)
```

**Explicación línea por línea:**
- `cv2.imread("foto.jpg")` lee la imagen como un array NumPy.
- `img.shape` da `(alto, ancho, canales)`.
- `cv2.imshow("Foto", img)` abre una ventana con la imagen.
- `cv2.waitKey(0)` espera indefinidamente hasta que pulses una tecla. **Sin esto, la ventana no se muestra.**
- `cv2.destroyAllWindows()` cierra las ventanas.
- `cv2.imwrite(...)` guarda la imagen en disco.

### 3.2. Leer en escala de grises

```python
gris = cv2.imread("foto.jpg", cv2.IMREAD_GRAYSCALE)
print(gris.shape)   # (alto, ancho) — sin canales
```

**Explicación línea por línea:**
- `IMREAD_GRAYSCALE` lee la imagen como un solo canal.

### 3.3. Errores comunes

**Error 1 — `imread` devuelve `None`** si la ruta es incorrecta.

*Solución:* comprueba siempre:
```python
img = cv2.imread("foto.jpg")
if img is None:
    raise FileNotFoundError("No se pudo leer la imagen")
```

**Error 2 — Olvidar `waitKey`** y no ver la ventana.

### 3.4. Checkpoint de comprensión

1. ¿Qué devuelve `imread` si la ruta no existe?
2. ¿Para qué sirve `waitKey`?

### 3.5. Ejercicio propuesto

**Ejercicio 3.1.** Lee una imagen, muestra sus dimensiones y guárdala en gris.

**Pista:** `IMREAD_GRAYSCALE` e `imwrite`.

---

## 4. Espacios de color (BGR, RGB, HSV, escala de grises)

### 4.1. Los cuatro espacios

- **BGR:** el orden por defecto de OpenCV.
- **RGB:** el que usan Matplotlib y PIL.
- **HSV:** tono (*hue*), saturación, valor. Ideal para **segmentar por color**.
- **Gris:** un solo canal.

### 4.2. Conversiones

```python
import cv2

img = cv2.imread("foto.jpg")
rgb = cv2.cvtColor(img, cv2.COLOR_BGR2RGB)
gris = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
hsv = cv2.cvtColor(img, cv2.COLOR_BGR2HSV)
```

**Explicación línea por línea:**
- `cvtColor` convierte entre espacios de color con el código correspondiente.

### 4.3. Segmentar por color en HSV

Aquí está la aplicación práctica. HSV separa el **color** (H) de la **intensidad** (V), así que funciona mejor que BGR para aislar objetos por su color:

```python
import cv2
import numpy as np

hsv = cv2.cvtColor(img, cv2.COLOR_BGR2HSV)
inferior = np.array([100, 100, 100])
superior = np.array([130, 255, 255])
mascara = cv2.inRange(hsv, inferior, superior)
resultado = cv2.bitwise_and(img, img, mask=mascara)
```

**Explicación línea por línea:**
- `inferior` y `superior` definen el rango de color a aislar.
- `cv2.inRange(hsv, inf, sup)` crea una **máscara**: blanco donde el color está en el rango, negro fuera.
- `bitwise_and(img, img, mask=mascara)` conserva solo los píxeles de la máscara.

### 4.4. Errores comunes

**Error 1 — Mostrar con Matplotlib una imagen BGR** → colores invertidos (los azules salen rojos).

*Solución:* convierte a RGB antes.

**Error 2 — Rangos HSV mal elegidos** → máscaras vacías.

### 4.5. Checkpoint de comprensión

1. ¿Por qué HSV es mejor para segmentar por color?
2. ¿Qué hace `inRange`?

### 4.6. Ejercicio propuesto

**Ejercicio 4.1.** Segmenta un objeto azul de una imagen usando HSV.

**Pista:** ajusta los rangos con una herramienta de color.

---

## 5. Operaciones básicas con imágenes

### 5.1. Redimensionar, recortar, voltear

```python
import cv2

img = cv2.imread("foto.jpg")

# Redimensionar
redim = cv2.resize(img, (300, 200))
redim2 = cv2.resize(img, None, fx=0.5, fy=0.5)

# Recortar (slicing de NumPy)
recorte = img[50:200, 100:300]

# Voltear
volteada = cv2.flip(img, 1)   # 1 horizontal, 0 vertical, -1 ambos
```

**Explicación línea por línea:**
- `resize(img, (ancho, alto))` redimensiona. **Ojo:** aquí el orden es (ancho, alto), al revés que `shape`.
- `resize(img, None, fx=0.5, fy=0.5)` escala al 50 %.
- `img[50:200, 100:300]` recorta con slicing (fila, columna) = (y, x).
- `flip(img, 1)` voltea horizontalmente.

### 5.2. Operaciones aritméticas

```python
suma = cv2.add(img, 50)            # satura en 255
mezcla = cv2.addWeighted(img, 0.7, img2, 0.3, 0)
diferencia = cv2.absdiff(img, img2)
```

**Explicación línea por línea:**
- `cv2.add` **satura** en 255 (no se desborda).
- `addWeighted` mezcla dos imágenes con pesos.
- `absdiff` calcula la diferencia absoluta (útil para detectar cambios).

### 5.3. Errores comunes

**Error 1 — Sumar con `+` de NumPy** (módulo 256, da la vuelta).

```python
img + 50   # ❌ se desborda
cv2.add(img, 50)   # ✅ satura
```

**Error 2 — Mezclar imágenes de distinto tamaño** sin redimensionar.

### 5.4. Checkpoint de comprensión

1. ¿Qué diferencia hay entre `img + 50` y `cv2.add(img, 50)`?
2. ¿En qué orden van las dimensiones en `resize`?

### 5.5. Ejercicio propuesto

**Ejercicio 5.1.** Redimensiona una imagen al 50 % y voltéala horizontalmente.

**Pista:** `resize` con `fx`/`fy` y `flip`.

---

## 6. ROI y manipulación de píxeles

### 6.1. Regiones de interés

Un **ROI** (*Region of Interest*) es una subimagen. Los píxeles se acceden como en NumPy, pero en orden **BGR**:

```python
import cv2

img = cv2.imread("foto.jpg")

# Leer un píxel (B, G, R)
b, g, r = img[100, 200]
print(b, g, r)

# Modificar un píxel
img[100, 200] = [255, 0, 0]   # azul

# ROI
roi = img[50:150, 100:200]
roi[:] = [0, 255, 0]   # pinta el ROI de verde
```

**Explicación línea por línea:**
- `img[100, 200]` accede al píxel en la fila 100, columna 200.
- Devuelve los tres canales en orden **BGR**.
- `roi[:] = [0, 255, 0]` pinta **todo** el ROI de verde.
- **Importante:** `roi` es una **vista** (archivo 18): modificar `roi` modifica `img`.

### 6.2. Separar canales

```python
b, g, r = cv2.split(img)
img_merge = cv2.merge([b, g, r])
```

**Explicación línea por línea:**
- `split` separa los canales; `merge` los recombina.

### 6.3. Errores comunes

**Error 1 — Confundir `(x, y)` con `[fila, columna]`.** En arrays es `[y, x]`.

**Error 2 — Modificar una ROI creyendo que es copia** (es vista).

### 6.4. Checkpoint de comprensión

1. ¿En qué orden se accede a un píxel?
2. ¿Qué devuelve `img[100, 200]`?

### 6.5. Ejercicio propuesto

**Ejercicio 6.1.** Pinta de rojo un cuadrado en el centro de una imagen.

**Pista:** usa slicing con `img[y1:y2, x1:x2]`.

---

## 7. Dibujo sobre imágenes

### 7.1. Formas y texto

```python
import cv2

img = cv2.imread("foto.jpg")

cv2.line(img, (0, 0), (200, 200), (0, 255, 0), 3)
cv2.rectangle(img, (50, 50), (150, 120), (255, 0, 0), 2)
cv2.circle(img, (250, 150), 40, (0, 0, 255), -1)
cv2.putText(img, "OpenCV", (30, 300), cv2.FONT_HERSHEY_SIMPLEX, 1, (255, 255, 255), 2)
```

**Explicación línea por línea:**
- `line(img, inicio, fin, color, grosor)` dibuja una línea.
- `rectangle(img, esquina1, esquina2, color, grosor)`.
- `circle(img, centro, radio, color, grosor)`; grosor `-1` rellena.
- `putText(img, texto, posicion, fuente, escala, color, grosor)`.

### 7.2. Errores comunes

**Error 1 — Grosor `-1`** rellena la figura.

**Error 2 — Coordenadas fuera de la imagen** → no se dibuja.

### 7.3. Checkpoint de comprensión

1. ¿Qué hace el grosor `-1`?
2. ¿Qué parámetros recibe `putText`?

### 7.4. Ejercicio propuesto

**Ejercicio 7.1.** Dibuja un rectángulo y escribe "objeto" encima.

**Pista:** `rectangle` + `putText`.

---

## 8. Transformaciones geométricas

### 8.1. Mover, rotar, deformar

```python
import cv2
import numpy as np

img = cv2.imread("foto.jpg")
alto, ancho = img.shape[:2]

# Traslación
M = np.float32([[1, 0, 100], [0, 1, 50]])
trasladada = cv2.warpAffine(img, M, (ancho, alto))

# Rotación
M = cv2.getRotationMatrix2D((ancho / 2, alto / 2), 45, 1.0)
rotada = cv2.warpAffine(img, M, (ancho, alto))

# Perspectiva
origen = np.float32([[0, 0], [ancho, 0], [0, alto], [ancho, alto]])
destino = np.float32([[0, 0], [ancho - 50, 20], [30, alto], [ancho, alto]])
M = cv2.getPerspectiveTransform(origen, destino)
perspectiva = cv2.warpPerspective(img, M, (ancho, alto))
```

**Explicación línea por línea:**
- `warpAffine` aplica una transformación afín (traslación, rotación...).
- `getRotationMatrix2D(centro, angulo, escala)` crea la matriz de rotación.
- `getPerspectiveTransform` calcula la transformación de perspectiva a partir de 4 puntos.
- `warpPerspective` la aplica.

### 8.2. Errores comunes

**Error 1 — Dimensiones invertidas** en `warpAffine` (`(ancho, alto)`).

**Error 2 — Matriz de transformación mal formada.**

### 8.3. Checkpoint de comprensión

1. ¿Qué hace `warpAffine`?
2. ¿Cuántos puntos necesitas para una transformación de perspectiva?

### 8.4. Ejercicio propuesto

**Ejercicio 8.1.** Rota una imagen 90 grados.

**Pista:** `getRotationMatrix2D` con ángulo 90.

---

## 9. Filtros y convoluciones

### 9.1. Suavizar y realzar

Los filtros modifican la imagen mediante **convolución** (aplicar un kernel a cada píxel):

```python
import cv2

img = cv2.imread("foto.jpg")

desenfoque = cv2.blur(img, (5, 5))
gaussiano = cv2.GaussianBlur(img, (7, 7), 0)
mediana = cv2.medianBlur(img, 5)
bilateral = cv2.bilateralFilter(img, 9, 75, 75)
```

**Explicación línea por línea:**
- `blur` es un desenfoque simple (media).
- `GaussianBlur` usa una campana de Gauss.
- `medianBlur` toma la mediana (excelente para ruido sal-y-pimienta).
- `bilateralFilter` suaviza **preservando los bordes**.

### 9.2. Kernel personalizado

```python
import numpy as np

kernel = np.array([[0, -1, 0], [-1, 5, -1], [0, -1, 0]])
nitida = cv2.filter2D(img, -1, kernel)
```

**Explicación línea por línea:**
- Este kernel **realza** la nitidez (aumenta el contraste en los bordes).

### 9.3. Errores comunes

**Error 1 — Tamaños de kernel pares** (deben ser impares).

**Error 2 — Confundir desenfoque con nitidez.**

### 9.4. Checkpoint de comprensión

1. ¿Qué filtro preserva los bordes?
2. ¿Qué hace un kernel?

### 9.5. Ejercicio propuesto

**Ejercicio 9.1.** Compara el desenfoque gaussiano y el bilateral en una imagen con bordes.

**Pista:** observa la preservación de bordes del bilateral.

---

## 10. Detección de bordes (Canny, Sobel, Laplaciano)

### 10.1. Encontrar los contornos

- **Canny:** detector robusto con umbrales dobles.
- **Sobel:** gradientes horizontal/vertical.
- **Laplaciano:** segunda derivada.

```python
import cv2

gris = cv2.cvtColor(cv2.imread("foto.jpg"), cv2.COLOR_BGR2GRAY)

bordes = cv2.Canny(gris, 100, 200)

sobel_x = cv2.Sobel(gris, cv2.CV_64F, 1, 0, ksize=5)
sobel_y = cv2.Sobel(gris, cv2.CV_64F, 0, 1, ksize=5)

laplaciano = cv2.Laplacian(gris, cv2.CV_64F)
```

**Explicación línea por línea:**
- `Canny(gris, umbral_bajo, umbral_alto)` detecta bordes.
- `Sobel(gris, tipo, dx, dy, ksize)` calcula el gradiente en una dirección.
- `CV_64F` permite valores negativos (gradientes).

### 10.2. Errores comunes

**Error 1 — Umbrales mal ajustados** en Canny → demasiado o poco ruido.

**Error 2 — No convertir a gris** antes de Canny.

### 10.3. Checkpoint de comprensión

1. ¿Qué hace Canny?
2. ¿Por qué usar `CV_64F` en Sobel?

### 10.4. Ejercicio propuesto

**Ejercicio 10.1.** Aplica Canny con varios umbrales y compara.

**Pista:** prueba `(50, 150)` y `(100, 200)`.

---

## 11. Umbralización (thresholding)

### 11.1. Blanco y negro

```python
import cv2

gris = cv2.cvtColor(cv2.imread("foto.jpg"), cv2.COLOR_BGR2GRAY)

_, binaria = cv2.threshold(gris, 127, 255, cv2.THRESH_BINARY)
_, inversa = cv2.threshold(gris, 127, 255, cv2.THRESH_BINARY_INV)

_, otsu = cv2.threshold(gris, 0, 255, cv2.THRESH_BINARY + cv2.THRESH_OTSU)

adaptativa = cv2.adaptiveThreshold(
    gris, 255, cv2.ADAPTIVE_THRESH_GAUSSIAN_C, cv2.THRESH_BINARY, 11, 2
)
```

**Explicación línea por línea:**
- `threshold(gris, umbral, max, tipo)` convierte a binario.
- `THRESH_BINARY` deja blanco lo que supera el umbral; `INV` lo invierte.
- `THRESH_OTSU` **calcula automáticamente** el mejor umbral.
- `adaptiveThreshold` usa un umbral distinto para cada zona (ideal con iluminación desigual).

### 11.2. Errores comunes

**Error 1 — Umbral fijo** en imágenes con iluminación desigual. Usa Otsu o adaptativa.

**Error 2 — Olvidar el `_`** del valor de retorno.

### 11.3. Checkpoint de comprensión

1. ¿Qué hace Otsu?
2. ¿Cuándo usar `adaptiveThreshold`?

### 11.4. Ejercicio propuesto

**Ejercicio 11.1.** Aplica Otsu y umbralización adaptativa a una imagen con sombras.

**Pista:** compara resultados.

---

## 12. Morfología matemática

### 12.1. Operaciones sobre formas

- **Erosión:** adelgaza (elimina ruido blanco).
- **Dilatación:** engorda (rellena huecos).
- **Apertura:** erosión + dilatación (elimina ruido).
- **Cierre:** dilatación + erosión (rellena agujeros).

```python
import cv2
import numpy as np

mascara = cv2.imread("mascara.png", 0)
kernel = np.ones((5, 5), np.uint8)

erosion = cv2.erode(mascara, kernel, iterations=1)
dilatacion = cv2.dilate(mascara, kernel, iterations=1)
apertura = cv2.morphologyEx(mascara, cv2.MORPH_OPEN, kernel)
cierre = cv2.morphologyEx(mascara, cv2.MORPH_CLOSE, kernel)
```

**Explicación línea por línea:**
- `erode`/`dilate` aplican la operación básica.
- `morphologyEx(..., MORPH_OPEN, ...)` hace la apertura; `MORPH_CLOSE`, el cierre.
- El `kernel` define el tamaño del efecto.

### 12.2. Errores comunes

**Error 1 — Kernel demasiado grande** que destruye detalles.

**Error 2 — Confundir apertura con cierre.**

### 12.3. Checkpoint de comprensión

1. ¿Qué elimina la apertura?
2. ¿Qué rellena el cierre?

### 12.4. Ejercicio propuesto

**Ejercicio 12.1.** Elimina ruido de una máscara binaria con apertura.

**Pista:** `MORPH_OPEN`.

---

## 13. Detección de contornos

### 13.1. Delimitar objetos

Los **contornos** son las curvas que delimitan objetos. Se detectan sobre imágenes **binarias**:

```python
import cv2

img = cv2.imread("foto.jpg")
gris = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
_, binaria = cv2.threshold(gris, 127, 255, cv2.THRESH_BINARY)

contornos, _ = cv2.findContours(binaria, cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE)

for contorno in contornos:
    area = cv2.contourArea(contorno)
    if area > 100:
        x, y, w, h = cv2.boundingRect(contorno)
        cv2.rectangle(img, (x, y), (x + w, y + h), (0, 255, 0), 2)
```

**Explicación línea por línea:**
- `findContours(binaria, modo, metodo)` devuelve los contornos.
- `RETR_EXTERNAL` solo los contornos externos.
- `contourArea(contorno)` calcula el área.
- `boundingRect(contorno)` da el rectángulo que lo encierra.

### 13.2. Propiedades de los contornos

```python
perimetro = cv2.arcLength(contorno, True)
aproximacion = cv2.approxPolyDP(contorno, 0.02 * perimetro, True)
```

**Explicación línea por línea:**
- `arcLength` da el perímetro.
- `approxPolyDP` simplifica el contorno (útil para reconocer formas por su número de lados).

### 13.3. Errores comunes

**Error 1 — Detectar contornos sobre imágenes en color** (usa binaria).

**Error 2 — No filtrar por área** y obtener miles de contornos.

### 13.4. Checkpoint de comprensión

1. ¿Sobre qué tipo de imagen se detectan contornos?
2. ¿Para qué sirve `approxPolyDP`?

### 13.5. Ejercicio propuesto

**Ejercicio 13.1.** Cuenta cuántos objetos hay en una imagen binaria.

**Pista:** filtra contornos por área.

---

## 14. Detección de líneas y círculos (Hough)

### 14.1. Formas geométricas

La **transformada de Hough** detecta formas paramétricas: líneas y círculos.

```python
import cv2
import numpy as np

gris = cv2.cvtColor(cv2.imread("foto.jpg"), cv2.COLOR_BGR2GRAY)
bordes = cv2.Canny(gris, 50, 150)

lineas = cv2.HoughLinesP(bordes, 1, np.pi / 180, threshold=100,
                         minLineLength=100, maxLineGap=10)
for linea in lineas:
    x1, y1, x2, y2 = linea[0]
    cv2.line(gris, (x1, y1), (x2, y2), (255, 0, 0), 2)
```

**Explicación línea por línea:**
- `HoughLinesP` detecta líneas (versión probabilística).
- `threshold` es el mínimo de votos; `minLineLength` la longitud mínima.

### 14.2. Círculos

```python
circulos = cv2.HoughCircles(
    gris, cv2.HOUGH_GRADIENT, dp=1.2, minDist=30,
    param1=100, param2=30, minRadius=10, maxRadius=100,
)
```

**Explicación línea por línea:**
- `minDist` es la distancia mínima entre centros.
- `param2` controla la sensibilidad (menor = más círculos).

### 14.3. Errores comunes

**Error 1 — Parámetros mal ajustados** → falsos positivos.

**Error 2 — No suavizar antes** de HoughCircles.

### 14.4. Checkpoint de comprensión

1. ¿Qué detecta Hough?
2. ¿Qué controla `param2` en HoughCircles?

### 14.5. Ejercicio propuesto

**Ejercicio 14.1.** Detecta monedas en una imagen con HoughCircles.

**Pista:** ajusta `minRadius`/`maxRadius`.

---

## 15. Detección de esquinas (Harris, Shi-Tomasi)

### 15.1. Puntos de interés

Las **esquinas** son puntos de alto interés para tracking y matching:

```python
import cv2
import numpy as np

gris = cv2.cvtColor(cv2.imread("foto.jpg"), cv2.COLOR_BGR2GRAY)

esquinas = cv2.goodFeaturesToTrack(gris, maxCorners=100, qualityLevel=0.01, minDistance=10)
for esquina in esquinas:
    x, y = esquina.ravel()
    cv2.circle(gris, (int(x), int(y)), 3, 255, -1)

destino = cv2.cornerHarris(np.float32(gris), 2, 3, 0.04)
```

**Explicación línea por línea:**
- `goodFeaturesToTrack` (Shi-Tomasi) encuentra las mejores esquinas.
- `cornerHarris` calcula la respuesta de Harris (requiere `float32`).

### 15.2. Errores comunes

**Error 1 — Demasiadas esquinas** por umbral bajo.

**Error 2 — No convertir a `float32`** para Harris.

### 15.3. Checkpoint de comprensión

1. ¿Para qué sirven las esquinas?
2. ¿Qué tipo requiere `cornerHarris`?

### 15.4. Ejercicio propuesto

**Ejercicio 15.1.** Detecta y marca las 50 mejores esquinas de una imagen.

**Pista:** `goodFeaturesToTrack`.

---

## 16. Características y descriptores (SIFT, ORB, SURF)

### 16.1. Describir regiones

Los **descriptores** resumen regiones de la imagen para compararlas entre fotos, independientemente de la escala y la rotación:

- **SIFT:** robusto, más lento.
- **ORB:** rápido y libre.
- **SURF:** en módulos contrib.

```python
import cv2

gris = cv2.cvtColor(cv2.imread("foto.jpg"), cv2.COLOR_BGR2GRAY)

sift = cv2.SIFT_create()
puntos, descriptores = sift.detectAndCompute(gris, None)

orb = cv2.ORB_create()
puntos_orb, desc_orb = orb.detectAndCompute(gris, None)

img_kp = cv2.drawKeypoints(gris, puntos, None, color=(0, 255, 0))
```

**Explicación línea por línea:**
- `detectAndCompute` encuentra puntos clave y calcula sus descriptores.
- `drawKeypoints` los dibuja.

### 16.2. Errores comunes

**Error 1 — Usar SIFT sin `opencv-contrib`** (en algunas versiones).

**Error 2 — Comparar descriptores de distinto tipo** (SIFT vs ORB).

### 16.3. Checkpoint de comprensión

1. ¿Qué es un descriptor?
2. ¿Qué diferencia hay entre SIFT y ORB?

### 16.4. Ejercicio propuesto

**Ejercicio 16.1.** Detecta keypoints con ORB y dibújalos.

**Pista:** `drawKeypoints`.

---

## 17. Matching de características

### 17.1. Emparejar imágenes

El **matching** compara descriptores de dos imágenes:

```python
import cv2

img1 = cv2.imread("a.jpg", 0)
img2 = cv2.imread("b.jpg", 0)

orb = cv2.ORB_create()
kp1, des1 = orb.detectAndCompute(img1, None)
kp2, des2 = orb.detectAndCompute(img2, None)

bf = cv2.BFMatcher(cv2.NORM_HAMMING, crossCheck=True)
coincidencias = bf.match(des1, des2)
coincidencias = sorted(coincidencias, key=lambda m: m.distance)

resultado = cv2.drawMatches(img1, kp1, img2, kp2, coincidencias[:20], None)
```

**Explicación línea por línea:**
- `BFMatcher` es un emparejador por fuerza bruta.
- `NORM_HAMMING` es la distancia adecuada para ORB.
- Se ordenan por distancia (menor = mejor coincidencia).

### 17.2. Ratio test de Lowe

```python
bf = cv2.BFMatcher()
matches = bf.knnMatch(des1, des2, k=2)
buenos = [m for m, n in matches if m.distance < 0.75 * n.distance]
```

**Explicación línea por línea:**
- `knnMatch(k=2)` devuelve las dos mejores coincidencias.
- El **ratio test** conserva solo las que son claramente mejores que la segunda.

### 17.3. Errores comunes

**Error 1 — No ordenar/filtrar** coincidencias → muchas falsas.

**Error 2 — Usar `NORM_HAMMING` con SIFT** (usa `NORM_L2`).

### 17.4. Checkpoint de comprensión

1. ¿Qué hace `BFMatcher`?
2. ¿Para qué sirve el ratio test?

### 17.5. Ejercicio propuesto

**Ejercicio 17.1.** Encuentra un objeto en una escena con ORB + BFMatcher.

**Pista:** usa el ratio test.

---

## 18. Homografía y transformaciones

### 18.1. Mapear planos

La **homografía** mapea un plano a otro; permite "enderezar" objetos y hacer realidad aumentada:

```python
import cv2
import numpy as np

origen = np.float32([[56, 65], [368, 52], [28, 387], [389, 390]])
destino = np.float32([[0, 0], [300, 0], [0, 300], [300, 300]])

M, _ = cv2.findHomography(origen, destino)
rectificado = cv2.warpPerspective(img, M, (300, 300))
```

**Explicación línea por línea:**
- `findHomography(origen, destino)` calcula la matriz que transforma unos puntos en otros.
- `warpPerspective` aplica la transformación.

### 18.2. Errores comunes

**Error 1 — Menos de 4 puntos** de correspondencia.

**Error 2 — Puntos mal ordenados** → deformación.

### 18.3. Checkpoint de comprensión

1. ¿Cuántos puntos necesita una homografía?
2. ¿Para qué sirve?

### 18.4. Ejercicio propuesto

**Ejercicio 18.1.** Rectifica una hoja de papel inclinada.

**Pista:** selecciona las 4 esquinas.

---

## 19. Detección de rostros (Haar Cascades, DNN)

### 19.1. Dos enfoques

- **Haar Cascades:** método clásico, rápido, menos preciso.
- **DNN:** redes preentrenadas más precisas.

```python
import cv2

clasificador = cv2.CascadeClassifier(
    cv2.data.haarcascades + "haarcascade_frontalface_default.xml"
)

gris = cv2.cvtColor(cv2.imread("grupo.jpg"), cv2.COLOR_BGR2GRAY)
rostros = clasificador.detectMultiScale(gris, scaleFactor=1.1, minNeighbors=5)

for x, y, w, h in rostros:
    cv2.rectangle(gris, (x, y), (x + w, y + h), (0, 255, 0), 2)
```

**Explicación línea por línea:**
- `CascadeClassifier(...)` carga el clasificador preentrenado.
- `detectMultiScale` busca rostros a distintas escalas.
- `scaleFactor` y `minNeighbors` controlan la sensibilidad.

### 19.2. Errores comunes

**Error 1 — `scaleFactor` mal ajustado** → detecciones perdidas o falsas.

**Error 2 — Usar Haar para perfiles** (funciona mejor frontal).

### 19.3. Checkpoint de comprensión

1. ¿Qué diferencia hay entre Haar y DNN?
2. ¿Qué controla `minNeighbors`?

### 19.4. Ejercicio propuesto

**Ejercicio 19.1.** Detecta rostros en una foto grupal y cuéntalos.

**Pista:** `len(rostros)`.

---

## 20. Detección de objetos

### 20.1. Modelos preentrenados

Detección de objetos con modelos como YOLO, SSD o MobileNet vía `cv2.dnn`:

```python
import cv2
import numpy as np

net = cv2.dnn.readNet("yolov4.weights", "yolov4.cfg")
capas = net.getUnconnectedOutLayersNames()

img = cv2.imread("escena.jpg")
blob = cv2.dnn.blobFromImage(img, 1/255.0, (416, 416), swapRB=True, crop=False)
net.setInput(blob)
detecciones = net.forward(capas)
```

**Explicación línea por línea:**
- `readNet` carga el modelo.
- `blobFromImage` prepara la imagen (normaliza y redimensiona).
- `swapRB=True` cambia BGR a RGB (los modelos esperan RGB).
- `net.forward` ejecuta la inferencia.

### 20.2. Errores comunes

**Error 1 — Modelos muy pesados** para CPU en tiempo real.

**Error 2 — No aplicar supresión de no-máximos (NMS)** → cajas duplicadas.

### 20.3. Checkpoint de comprensión

1. ¿Qué hace `blobFromImage`?
2. ¿Por qué `swapRB=True`?

### 20.4. Ejercicio propuesto

**Ejercicio 20.1.** Ejecuta YOLO sobre una imagen y dibuja las cajas con confianza > 0.5.

**Pista:** usa `NMSBoxes`.

---

## 21. Tracking de objetos

### 21.1. Seguir en vídeo

El **tracking** sigue objetos a lo largo de un vídeo:

```python
import cv2

cap = cv2.VideoCapture("video.mp4")
ok, frame = cap.read()

bbox = cv2.selectROI("Selecciona", frame, fromCenter=False)
tracker = cv2.TrackerCSRT_create()
tracker.init(frame, bbox)

while True:
    ok, frame = cap.read()
    if not ok:
        break
    ok, bbox = tracker.update(frame)
    if ok:
        x, y, w, h = [int(v) for v in bbox]
        cv2.rectangle(frame, (x, y), (x + w, y + h), (0, 255, 0), 2)
    cv2.imshow("Tracking", frame)
    if cv2.waitKey(30) & 0xFF == 27:
        break
cap.release()
cv2.destroyAllWindows()
```

**Explicación línea por línea:**
- `selectROI` permite seleccionar el objeto a seguir con el ratón.
- `TrackerCSRT_create()` crea el tracker.
- `tracker.update(frame)` devuelve la nueva posición.

### 21.2. Errores comunes

**Error 1 — Pérdida del objeto** por oclusión. Re-detecta periódicamente.

**Error 2 — Tracker inadecuado** para objetos rápidos.

### 21.3. Checkpoint de comprensión

1. ¿Qué hace `selectROI`?
2. ¿Qué pasa si el objeto se ocluye?

### 21.4. Ejercicio propuesto

**Ejercicio 21.1.** Sigue un objeto seleccionado en un video y muestra su centro.

**Pista:** calcula `(x + w//2, y + h//2)`.

---

## 22. Procesamiento de video

### 22.1. Leer y escribir vídeo

`VideoCapture` lee; `VideoWriter` escribe. Los frames son imágenes:

```python
import cv2

cap = cv2.VideoCapture(0)   # 0 = webcam; o una ruta de archivo
fourcc = cv2.VideoWriter_fourcc(*"mp4v")
out = cv2.VideoWriter("salida.mp4", fourcc, 20.0, (640, 480))

while True:
    ok, frame = cap.read()
    if not ok:
        break
    out.write(frame)
    cv2.imshow("Video", frame)
    if cv2.waitKey(1) & 0xFF == ord("q"):
        break

cap.release()
out.release()
cv2.destroyAllWindows()
```

**Explicación línea por línea:**
- `VideoCapture(fuente)` abre la cámara o un archivo.
- `cap.read()` devuelve `(ok, frame)`.
- `VideoWriter(...)` escribe el vídeo de salida.
- `cap.release()` y `out.release()` **liberan** los recursos. **Nunca lo olvides.**

### 22.2. Propiedades

```python
fps = cap.get(cv2.CAP_PROP_FPS)
ancho = int(cap.get(cv2.CAP_PROP_FRAME_WIDTH))
alto = int(cap.get(cv2.CAP_PROP_FRAME_HEIGHT))
```

### 22.3. Errores comunes

**Error 1 — Olvidar `release()`** → archivo corrupto o cámara ocupada.

**Error 2 — FourCC no soportado** en el sistema.

### 22.4. Checkpoint de comprensión

1. ¿Qué devuelve `cap.read()`?
2. ¿Por qué es crítico `release()`?

### 22.5. Ejercicio propuesto

**Ejercicio 22.1.** Convierte un video a escala de grises y guárdalo.

**Pista:** aplica `cvtColor` a cada frame.

---

## 23. Captura desde webcam

### 23.1. La cámara en vivo

`VideoCapture(0)` abre la webcam; se leen frames en bucle:

```python
import cv2

cap = cv2.VideoCapture(0)

while True:
    ok, frame = cap.read()
    if not ok:
        break
    cv2.imshow("Webcam", frame)
    if cv2.waitKey(1) & 0xFF == ord("q"):
        break

cap.release()
cv2.destroyAllWindows()
```

**Explicación línea por línea:**
- `waitKey(1)` espera 1 ms y captura la tecla pulsada.
- `ord("q")` compara con la tecla 'q' para salir.

### 23.2. Errores comunes

**Error 1 — Índice de cámara incorrecto** → `ok` siempre `False`.

**Error 2 — Permisos de cámara** denegados.

### 23.3. Checkpoint de comprensión

1. ¿Qué índice usa la webcam por defecto?
2. ¿Cómo sales del bucle?

### 23.4. Ejercicio propuesto

**Ejercicio 23.1.** Captura un frame con la webcam y guárdalo al pulsar `s`.

**Pista:** `cv2.imwrite` dentro del bucle.

---

## 24. Calibración de cámara

### 24.1. Corregir la distorsión

La **calibración** corrige la distorsión de la lente usando un tablero de ajedrez y estima la matriz intrínseca:

```python
import cv2
import numpy as np

patron = (9, 6)
criterio = (cv2.TERM_CRITERIA_EPS + cv2.TERM_CRITERIA_MAX_ITER, 30, 0.001)

puntos_obj = np.zeros((patron[0] * patron[1], 3), np.float32)
puntos_obj[:, :2] = np.mgrid[0:patron[0], 0:patron[1]].T.reshape(-1, 2)

# Recoge puntos de varias imágenes y luego:
# ret, matriz, distorsion, rvecs, tvecs = cv2.calibrateCamera(...)
```

**Explicación línea por línea:**
- Se preparan las coordenadas 3D del tablero.
- Se detectan las esquinas en varias imágenes y se llama a `calibrateCamera`.

### 24.2. Corregir una imagen

```python
imagen_corregida = cv2.undistort(img, matriz, distorsion)
```

### 24.3. Errores comunes

**Error 1 — Pocas imágenes** de calibración → mala estimación.

**Error 2 — Patrón no detectado** en algunas imágenes.

### 24.4. Checkpoint de comprensión

1. ¿Para qué sirve la calibración?
2. ¿Cómo corriges una imagen distorsionada?

### 24.5. Ejercicio propuesto

**Ejercicio 24.1.** Calibra una cámara con 10 fotos del tablero y corrige la distorsión.

**Pista:** usa `findChessboardCorners`.

---

## 25. Estereovisión

### 25.1. Estimar profundidad

La **estereovisión** usa dos cámaras para estimar profundidad mediante **disparidad** (la diferencia de posición del mismo objeto en cada imagen):

```python
import cv2

stereo = cv2.StereoSGBM_create(minDisparity=0, numDisparities=64, blockSize=11)
disparidad = stereo.compute(izquierda, derecha)
```

**Explicación línea por línea:**
- `StereoSGBM_create` crea el algoritmo de correspondencia.
- `compute(izq, der)` calcula el mapa de disparidad, del que se deriva la profundidad.

### 25.2. Errores comunes

**Error 1 — Cámaras no rectificadas** → mala profundidad.

**Error 2 — `numDisparities` no múltiplo de 16.**

### 25.3. Checkpoint de comprensión

1. ¿Qué es la disparidad?
2. ¿Por qué hay que rectificar las cámaras?

### 25.4. Ejercicio propuesto

**Ejercicio 25.1.** Calcula el mapa de disparidad de un par estéreo.

**Pista:** normaliza el resultado para visualizarlo.

---

## 26. Aprendizaje profundo con OpenCV DNN

### 26.1. Ejecutar modelos sin frameworks pesados

`cv2.dnn` carga modelos de Caffe, TensorFlow, ONNX, Darknet y ejecuta inferencia sin instalar el framework original:

```python
import cv2
import numpy as np

net = cv2.dnn.readNetFromONNX("modelo.onnx")

img = cv2.imread("imagen.jpg")
blob = cv2.dnn.blobFromImage(img, 1/255.0, (224, 224), swapRB=True)
net.setInput(blob)
salida = net.forward()
clase = int(np.argmax(salida))
```

**Explicación línea por línea:**
- `readNetFromONNX` carga el modelo.
- `blobFromImage` prepara la imagen.
- `net.forward()` devuelve las predicciones.
- `np.argmax(salida)` da la clase con mayor probabilidad.

### 26.2. Errores comunes

**Error 1 — No normalizar** el blob como el modelo espera.

**Error 2 — Olvidar `swapRB=True`** para modelos entrenados en RGB.

### 26.3. Checkpoint de comprensión

1. ¿Qué ventaja tiene `cv2.dnn`?
2. ¿Por qué normalizar el blob?

### 26.4. Ejercicio propuesto

**Ejercicio 26.1.** Carga un modelo ONNX de clasificación y predice la clase de una imagen.

**Pista:** `blobFromImage` + `forward`.

---

## 27. Integración con NumPy

### 27.1. La imagen es un array

Como las imágenes son arrays NumPy, puedes aplicar operaciones vectorizadas directamente:

```python
import cv2
import numpy as np

img = cv2.imread("foto.jpg")

# Ajuste de brillo
brillo = np.clip(img.astype(np.int16) + 50, 0, 255).astype(np.uint8)

# Máscara booleana
mascara = img[:, :, 0] > 128
img[mascara] = [255, 255, 255]

# Estadísticas
print(img.mean(axis=(0, 1)))
```

**Explicación línea por línea:**
- `astype(np.int16) + 50` sube el brillo; `np.clip` evita el desbordamiento.
- `img[:, :, 0] > 128` crea una máscara del canal azul.
- `img.mean(axis=(0, 1))` da la media por canal.

### 27.2. Errores comunes

**Error 1 — Desbordamiento de `uint8`** al sumar. Convierte a `int16`.

**Error 2 — Modificar una ROI** y alterar la original.

### 27.3. Checkpoint de comprensión

1. ¿Por qué convertir a `int16` para sumar?
2. ¿Cómo aplicas una máscara a una imagen?

### 27.4. Ejercicio propuesto

**Ejercicio 27.1.** Aumenta el brillo un 20 % con NumPy y compara con `cv2.add`.

**Pista:** usa `np.clip`.

---

## 28. Aplicaciones prácticas

### 28.1. Combinar todo

Combinando lo anterior: OCR, realidad aumentada, control de calidad, vigilancia.

### 28.2. OCR con Tesseract

```bash
pip install pytesseract
sudo apt install tesseract-ocr
```

```python
import cv2
import pytesseract

gris = cv2.cvtColor(cv2.imread("texto.png"), cv2.COLOR_BGR2GRAY)
_, binaria = cv2.threshold(gris, 0, 255, cv2.THRESH_BINARY + cv2.THRESH_OTSU)
texto = pytesseract.image_to_string(binaria, lang="spa")
print(texto)
```

**Explicación línea por línea:**
- Se preprocesa la imagen (gris + Otsu) para mejorar el OCR.
- `image_to_string` extrae el texto.

### 28.3. Realidad aumentada

Detectar un marcador, calcular la homografía y superponer una imagen.

### 28.4. Errores comunes

**Error 1 — OCR sin preprocesar** → texto erróneo.

**Error 2 — No calibrar** en aplicaciones de medición.

### 28.5. Checkpoint de comprensión

1. ¿Por qué preprocesar antes del OCR?
2. ¿Qué se necesita para realidad aumentada?

### 28.6. Ejercicio propuesto

**Ejercicio 28.1.** Extrae el texto de una imagen de un documento.

**Pista:** preprocesa con Otsu antes de Tesseract.

---

## 29. Buenas prácticas

1. **Convierte BGR↔RGB** al usar Matplotlib.
2. **Comprueba `None`** tras `imread`.
3. **Libera recursos** (`release`, `destroyAllWindows`).
4. **Preprocesa** (gris, desenfoque) antes de detectar.
5. **Filtra contornos por área.**
6. **Usa NumPy vectorizado** para operaciones de píxeles.
7. **Ajusta parámetros** con datos reales, no a ciegas.
8. **Headless** en servidores sin GUI.
9. **Cuidado con el desbordamiento** de uint8.
10. **Documenta los rangos** de color y parámetros usados.

---

## 30. Recursos

- **Documentación oficial:** <https://docs.opencv.org/>
- **Tutoriales OpenCV-Python:** <https://docs.opencv.org/4.x/d6/d00/tutorial_py_root.html>
- **PyImageSearch:** <https://pyimagesearch.com/>
- **Libro:** *Learning OpenCV 4*, Gary Bradski & Adrian Kaehler.
- **pytesseract:** <https://github.com/madmaze/pytesseract>

---

## 31. Mini resumen final

- OpenCV procesa **imágenes y vídeo** como arrays NumPy.
- Los **espacios de color** (BGR, RGB, HSV, gris) permiten segmentar.
- Las **transformaciones** y **filtros** preparan las imágenes.
- **Canny, umbralización y morfología** resaltan estructuras.
- **Contornos, Hough y esquinas** detectan formas y puntos.
- **SIFT/ORB** y el **matching** comparan imágenes; la **homografía** las rectifica.
- **Haar/DNN** detectan caras y objetos; los **trackers** los siguen en video.
- `cv2.dnn` ejecuta modelos de deep learning.
- Todo se integra con **NumPy** de forma vectorizada.

---

### 🎯 Retos opcionales

1. **Reto 1:** Cuenta objetos en una cinta transportadora con contornos y área.
2. **Reto 2:** Detecta y sigue un objeto de color en un video en tiempo real.
3. **Reto 3:** Rectifica un documento fotografiado y aplica OCR.

**Anterior:** [20_scikit_learn.md](20_scikit_learn.md) · **Siguiente (Parte VI):** [22_integracion.md](22_integracion.md)
