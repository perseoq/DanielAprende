# 15 · Selenium

**Versión recomendada:** Selenium 4.20+ · Python 3.12
**Requisito:** [14_sqlalchemy.md](14_sqlalchemy.md) (o fundamentos)
**Nivel:** Intermedio
**Duración estimada de estudio:** 8–12 horas

---

## Índice de contenidos

1. [Introducción a Selenium](#1-introducción-a-selenium-y-automatización-web)
2. [Instalación y WebDrivers](#2-instalación-y-webdrivers)
3. [Localizadores](#3-localizadores-id-name-class-xpath-css)
4. [Interacción con elementos](#4-interacción-con-elementos-click-send_keys-clear)
5. [Esperas implícitas y explícitas](#5-esperas-implícitas-y-explícitas-webdriverwait)
6. [Manejo de ventanas y pestañas](#6-manejo-de-ventanas-y-pestañas)
7. [Alertas, prompts y confirmaciones](#7-alertas-prompts-y-confirmaciones)
8. [Iframes y frames](#8-iframes-y-frames)
9. [Dropdowns y Select](#9-dropdowns-y-select)
10. [Acciones avanzadas](#10-acciones-avanzadas-actionchains)
11. [Capturas de pantalla](#11-capturas-de-pantalla)
12. [Ejecución de JavaScript](#12-ejecución-de-javascript)
13. [Cookies y sesiones](#13-cookies-y-sesiones)
14. [Page Object Model (POM)](#14-page-object-model-pom)
15. [Selenium Grid](#15-selenium-grid)
16. [Headless mode](#16-headless-mode)
17. [Integración con pytest](#17-integración-con-pytest)
18. [Web scraping ético y legal](#18-web-scraping-ético-y-legal)
19. [Alternativas](#19-alternativas-playwright-puppeteer)
20. [Buenas prácticas](#20-buenas-prácticas)
21. [Recursos](#21-recursos)
22. [Mini resumen final](#22-mini-resumen-final)

---

## 1. Introducción a Selenium y automatización web

### 1.1. ¿Qué es Selenium?

**Selenium** es una herramienta que **controla navegadores reales** (Chrome, Firefox, Edge) de forma programática. Abre el navegador, navega, hace clic, escribe texto, lee el contenido... como lo haría una persona.

**¿Por qué no usar simplemente `requests`?** Porque `requests` descarga el HTML **sin ejecutar JavaScript**. Las aplicaciones web modernas (React, Vue, Angular) construyen la página **con JavaScript** después de cargarla. `requests` vería una página casi vacía. Selenium, en cambio, usa un **navegador de verdad**, que ejecuta JavaScript y ve la página como un usuario.

### 1.2. Para qué sirve

- **Testing de aplicaciones web** (pruebas de extremo a extremo, E2E).
- **Automatización de tareas** repetitivas (rellenar formularios, descargar informes).
- **Web scraping** de contenido dinámico (con precauciones legales, sección 18).

### 1.3. Cómo funciona: el WebDriver

Selenium no habla directamente con el navegador. Usa un **WebDriver**: un programa intermedio que traduce tus comandos a acciones del navegador.

```text
Tu código Python → Selenium → WebDriver (ChromeDriver) → Chrome
```

**Analogía:** el WebDriver es como un **mando a distancia** universal. Tú pulsas botones en tu código; el WebDriver los traduce a señales que el navegador entiende.

### 1.4. Errores comunes

**Error 1 — Usar Selenium para APIs.**

Si solo necesitas descargar datos de una API, usa `requests`/`httpx`: es mucho más rápido y ligero.

**Error 2 — Ignorar los términos de servicio.**

Automatizar un sitio puede violar sus condiciones. Infórmate (sección 18).

### 1.5. Checkpoint de comprensión

1. ¿Qué diferencia hay entre usar Selenium y `requests`?
2. ¿Qué papel juega el WebDriver?

### 1.6. Ejercicio propuesto

**Ejercicio 1.1.** Abre una página y muestra su título.

**Pista:** `driver.title`.

---

## 2. Instalación y WebDrivers

### 2.1. Instalación

```bash
pip install selenium
```

### 2.2. Selenium Manager: sin descargas manuales

Antes había que descargar el ChromeDriver a mano y mantenerlo sincronizado con el navegador. Desde Selenium **4.6**, **Selenium Manager** lo hace automáticamente:

```python
from selenium import webdriver

driver = webdriver.Chrome()
driver.get("https://example.com")
print(driver.title)
driver.quit()
```

Salida esperada:

```text
Example Domain
```

**Explicación línea por línea:**
- `webdriver.Chrome()` abre una instancia de Chrome (y Selenium Manager descarga el driver si hace falta).
- `driver.get(url)` navega a la URL.
- `driver.title` devuelve el título de la página.
- `driver.quit()` **cierra el navegador**. Es imprescindible: si lo olvidas, quedan procesos abiertos consumiendo recursos.

### 2.3. Firefox

```python
from selenium import webdriver

driver = webdriver.Firefox()
```

### 2.4. Errores comunes

**Error 1 — Driver y navegador desincronizados.**

*Solución:* actualiza Selenium; Selenium Manager se encarga.

**Error 2 — Olvidar `driver.quit()`.**

*Síntoma:* procesos de Chrome colgados. *Solución:* usa `try/finally` o un fixture.

### 2.5. Checkpoint de comprensión

1. ¿Qué hace Selenium Manager?
2. ¿Por qué es obligatorio `quit()`?

### 2.6. Ejercicio propuesto

**Ejercicio 2.1.** Abre `example.com` en Chrome y Firefox y compara los títulos.

**Pista:** cierra cada driver con `quit()`.

---

## 3. Localizadores (id, name, class, XPath, CSS)

### 3.1. ¿Qué es un localizador?

Un **localizador** es la forma de **encontrar un elemento** en la página (un botón, un campo de texto...). Elegir buenos localizadores es la diferencia entre un test robusto y uno frágil.

### 3.2. Estrategias de localización

| Estrategia | Ejemplo |
|---|---|
| `By.ID` | `By.ID, "login"` |
| `By.NAME` | `By.NAME, "usuario"` |
| `By.CLASS_NAME` | `By.CLASS_NAME, "btn"` |
| `By.TAG_NAME` | `By.TAG_NAME, "a"` |
| `By.LINK_TEXT` | `By.LINK_TEXT, "Inicio"` |
| `By.CSS_SELECTOR` | `By.CSS_SELECTOR, "#login .btn"` |
| `By.XPATH` | `By.XPATH, "//button[@type='submit']"` |

### 3.3. Ejemplo

```python
from selenium import webdriver
from selenium.webdriver.common.by import By

driver = webdriver.Chrome()
driver.get("https://example.com")

titulo = driver.find_element(By.TAG_NAME, "h1")
print(titulo.text)

enlaces = driver.find_elements(By.TAG_NAME, "a")
print(len(enlaces))

driver.quit()
```

Salida esperada (varía):

```text
Example Domain
1
```

**Explicación línea por línea:**
- `find_element(...)` devuelve **un** elemento (el primero que coincide); lanza `NoSuchElementException` si no hay ninguno.
- `find_elements(...)` devuelve una **lista** (vacía si no hay coincidencias).

### 3.4. XPath vs. CSS

```python
# XPath por texto
driver.find_element(By.XPATH, "//a[text()='Más información']")

# CSS por atributo que empieza por
driver.find_element(By.CSS_SELECTOR, "a[href^='https']")
```

**¿Cuál preferir?** CSS suele ser más rápido y legible. XPath es más potente (puede subir en el árbol, buscar por texto). Úsalos con criterio.

### 3.5. Errores comunes

**Error 1 — XPath absoluto.**

```python
driver.find_element(By.XPATH, "/html/body/div[3]/div[2]/form/input[1]")
```

*Problema:* se rompe al menor cambio en el HTML. *Solución:* usa rutas relativas (`//input[@id='usuario']`) o IDs.

**Error 2 — `find_element` cuando el elemento no existe.**

*Solución:* usa esperas explícitas (sección 5) o `find_elements` y comprueba la lista.

### 3.6. Checkpoint de comprensión

1. ¿Qué diferencia hay entre `find_element` y `find_elements`?
2. ¿Por qué se evita el XPath absoluto?

### 3.7. Ejercicio propuesto

**Ejercicio 3.1.** Localiza todos los enlaces de una página y muestra sus textos.

**Pista:** `find_elements` + `element.text`.

---

## 4. Interacción con elementos (click, send_keys, clear)

### 4.1. Acciones sobre elementos

Los `WebElement` exponen métodos para interactuar:

- `.click()`: hacer clic.
- `.send_keys(texto)`: escribir.
- `.clear()`: vaciar un campo.
- `.submit()`: enviar un formulario.
- `.text`: leer el texto.
- `.get_attribute(nombre)`: leer un atributo.

### 4.2. Ejemplo: buscar en un sitio

```python
from selenium import webdriver
from selenium.webdriver.common.by import By

driver = webdriver.Chrome()
driver.get("https://duckduckgo.com")

caja = driver.find_element(By.NAME, "q")
caja.send_keys("Python")
caja.clear()
caja.send_keys("Selenium")
caja.submit()

driver.quit()
```

**Explicación línea por línea:**
- `send_keys("Python")` escribe "Python" en la caja.
- `clear()` la vacía.
- `send_keys("Selenium")` escribe "Selenium".
- `submit()` envía el formulario.

### 4.3. Propiedades útiles

```python
elemento = driver.find_element(By.ID, "boton")
print(elemento.text)
print(elemento.get_attribute("href"))
print(elemento.is_displayed())
print(elemento.is_enabled())
```

**Explicación línea por línea:**
- `.text` es el texto visible.
- `.get_attribute("href")` lee un atributo.
- `.is_displayed()` / `.is_enabled()` informan del estado.

### 4.4. Errores comunes

**Error 1 — Interactuar antes de que cargue.**

*Síntoma:* `NoSuchElementException`. *Solución:* esperas explícitas (sección 5).

**Error 2 — `ElementClickInterceptedException`.**

Otro elemento (un banner, un anuncio) tapa el botón. *Solución:* espera a que desaparezca o usa JavaScript para el clic.

### 4.5. Checkpoint de comprensión

1. ¿Qué hace `send_keys`? ¿Y `clear`?
2. ¿Por qué ocurre `ElementClickInterceptedException`?

### 4.6. Ejercicio propuesto

**Ejercicio 4.1.** Rellena un formulario de búsqueda y envíalo.

**Pista:** `send_keys` + `submit`.

---

## 5. Esperas implícitas y explícitas (WebDriverWait)

### 5.1. El problema del tiempo

Las páginas tardan en cargar. Si buscas un elemento antes de que exista, falla. Pero dormir un tiempo fijo (`time.sleep`) es lento y frágil. Selenium ofrece **esperas inteligentes**.

### 5.2. Espera implícita

```python
driver.implicitly_wait(5)   # espera global de hasta 5 s
```

**Explicación línea por línea:**
- Le dice a Selenium que espere hasta 5 segundos **al buscar** un elemento que aún no está.
- Es global (afecta a todas las búsquedas) y **poco precisa**.

### 5.3. Espera explícita (la recomendada)

```python
from selenium.webdriver.support import expected_conditions as EC
from selenium.webdriver.support.ui import WebDriverWait

elemento = WebDriverWait(driver, 10).until(
    EC.presence_of_element_located((By.TAG_NAME, "h1"))
)
print(elemento.text)
```

**Explicación línea por línea:**
- `WebDriverWait(driver, 10)` espera hasta 10 segundos.
- `.until(condicion)` repite la comprobación hasta que se cumple o expira.
- `EC.presence_of_element_located(...)` es la condición: que el elemento exista.

### 5.4. Condiciones comunes

```python
EC.element_to_be_clickable((By.ID, "boton"))       # clicable
EC.visibility_of_element_located((By.CSS_SELECTOR, ".cargando"))  # visible
EC.text_to_be_present_in_element((By.ID, "estado"), "Listo")      # texto
EC.invisibility_of_element_located((By.ID, "spinner"))            # desaparece
```

### 5.5. Errores comunes

**Error 1 — Mezclar esperas implícitas y explícitas.**

Puede causar comportamientos impredecibles. *Solución:* elige una; se recomienda la explícita.

**Error 2 — Usar `time.sleep`.**

```python
import time
time.sleep(5)   # ❌ siempre espera 5 s, aunque la página cargue en 0.2
```

*Solución:* usa `WebDriverWait`.

### 5.6. Checkpoint de comprensión

1. ¿Qué diferencia hay entre espera implícita y explícita?
2. ¿Por qué `time.sleep` es una mala práctica?

### 5.7. Ejercicio propuesto

**Ejercicio 5.1.** Espera a que un botón sea clicable y haz clic.

**Pista:** `EC.element_to_be_clickable`.

---

## 6. Manejo de ventanas y pestañas

### 6.1. Múltiples ventanas

Al abrir un enlace en una pestaña nueva, Selenium sigue "enfocado" en la original. Para trabajar con la nueva, debes **cambiar de contexto**.

```python
driver.get("https://example.com")

driver.execute_script("window.open('https://example.org', '_blank');")
ventanas = driver.window_handles

driver.switch_to.window(ventanas[1])
print(driver.title)

driver.close()
driver.switch_to.window(ventanas[0])
driver.quit()
```

**Explicación línea por línea:**
- `window_handles` lista los identificadores de todas las ventanas.
- `switch_to.window(handle)` cambia el foco a esa ventana.
- `close()` cierra la ventana actual.
- Hay que volver a la original antes de cerrar todo.

### 6.2. Errores comunes

**Error 1 — No volver a la ventana original** tras cerrar.

**Error 2 — Asumir un orden de handles.** Verifica por título o URL.

### 6.3. Checkpoint de comprensión

1. ¿Qué hace `window_handles`?
2. ¿Cómo cambias de ventana?

### 6.4. Ejercicio propuesto

**Ejercicio 6.1.** Abre una nueva pestaña, cambia a ella y vuelve.

**Pista:** guarda `driver.current_window_handle`.

---

## 7. Alertas, prompts y confirmaciones

### 7.1. Diálogos nativos

Los diálogos del navegador (`alert`, `confirm`, `prompt`) **no son parte del HTML**: son nativos. Se gestionan con `switch_to.alert`.

```python
from selenium import webdriver

driver = webdriver.Chrome()
driver.get("data:text/html,<button onclick=\"alert('Hola')\">Alerta</button>")
driver.find_element("tag name", "button").click()

alerta = driver.switch_to.alert
print(alerta.text)
alerta.accept()   # o alerta.dismiss()
driver.quit()
```

**Explicación línea por línea:**
- `switch_to.alert` obtiene el diálogo.
- `.text` lee su mensaje.
- `.accept()` lo acepta; `.dismiss()` lo cancela.

### 7.2. Prompt con texto

```python
alerta.send_keys("respuesta")
alerta.accept()
```

### 7.3. Errores comunes

**Error 1 — No gestionar la alerta.**

*Síntoma:* el driver se queda bloqueado. *Solución:* maneja la alerta o espera a que aparezca.

**Error 2 — `NoAlertPresentException`** si no hay alerta.

### 7.4. Checkpoint de comprensión

1. ¿Cómo se gestionan los diálogos nativos?
2. ¿Qué diferencia hay entre `accept` y `dismiss`?

### 7.5. Ejercicio propuesto

**Ejercicio 7.1.** Maneja un `confirm` y cancélalo con `dismiss()`.

**Pista:** usa `switch_to.alert`.

---

## 8. Iframes y frames

### 8.1. Contenido embebido

Un `<iframe>` incrusta una página dentro de otra. Selenium **no ve** el contenido del iframe hasta que **cambia de contexto**.

```python
from selenium import webdriver
from selenium.webdriver.common.by import By

driver = webdriver.Chrome()
driver.get("pagina_con_iframe.html")

iframe = driver.find_element(By.TAG_NAME, "iframe")
driver.switch_to.frame(iframe)

contenido = driver.find_element(By.TAG_NAME, "h1")
print(contenido.text)

driver.switch_to.default_content()   # volver al contexto principal
driver.quit()
```

**Explicación línea por línea:**
- `switch_to.frame(iframe)` entra en el iframe.
- Dentro, buscas elementos con normalidad.
- `default_content()` vuelve al documento principal.

### 8.2. Errores comunes

**Error 1 — Buscar elementos del iframe sin cambiar de contexto.**

*Síntoma:* `NoSuchElementException`.

**Error 2 — No volver a `default_content()`.**

### 8.3. Checkpoint de comprensión

1. ¿Por qué hay que cambiar de contexto para un iframe?
2. ¿Cómo vuelves al documento principal?

### 8.4. Ejercicio propuesto

**Ejercicio 8.1.** Interactúa con un botón dentro de un iframe y vuelve al contexto principal.

**Pista:** `switch_to.frame` / `default_content`.

---

## 9. Dropdowns y Select

### 9.1. Listas desplegables

La clase `Select` facilita trabajar con `<select>`:

```python
from selenium.webdriver.support.ui import Select

select = Select(driver.find_element(By.ID, "pais"))
select.select_by_visible_text("España")
select.select_by_value("es")
select.select_by_index(0)

print([o.text for o in select.options])
```

**Explicación línea por línea:**
- `select_by_visible_text`, `select_by_value` y `select_by_index` seleccionan de tres formas.
- `select.options` lista las opciones.

### 9.2. Errores comunes

**Error 1 — Usar `Select` en un `<div>` personalizado.**

*Solución:* los desplegables hechos con `<div>` se manejan con clics.

**Error 2 — No esperar a que se carguen las opciones.**

### 9.3. Checkpoint de comprensión

1. ¿Para qué sirve la clase `Select`?
2. ¿Cuándo no funciona `Select`?

### 9.4. Ejercicio propuesto

**Ejercicio 9.1.** Selecciona una opción por valor y verifica la selección.

**Pista:** `select.first_selected_option.text`.

---

## 10. Acciones avanzadas (ActionChains)

### 10.1. Gestos complejos

`ActionChains` permite **encadenar** gestos: hover, doble clic, arrastrar y soltar, pulsaciones de teclas.

```python
from selenium.webdriver.common.action_chains import ActionChains

acciones = ActionChains(driver)
acciones.move_to_element(menu).pause(1).click(submenu).perform()
```

**Explicación línea por línea:**
- `.move_to_element(menu)` mueve el ratón sobre el menú (hover).
- `.pause(1)` espera 1 segundo.
- `.click(submenu)` hace clic.
- `.perform()` **ejecuta** la secuencia. **Sin `perform`, no ocurre nada.**

### 10.2. Arrastrar y soltar

```python
origen = driver.find_element(By.ID, "origen")
destino = driver.find_element(By.ID, "destino")
ActionChains(driver).drag_and_drop(origen, destino).perform()
```

### 10.3. Errores comunes

**Error 1 — Olvidar `.perform()`.**

**Error 2 — No encadenar correctamente** las acciones.

### 10.4. Checkpoint de comprensión

1. ¿Qué permite `ActionChains`?
2. ¿Por qué es imprescindible `perform()`?

### 10.5. Ejercicio propuesto

**Ejercicio 10.1.** Haz hover sobre un menú y clic en una opción.

**Pista:** `move_to_element(...).click(...).perform()`.

---

## 11. Capturas de pantalla

### 11.1. Guardar imágenes

Útil sobre todo en fallos de test, para ver qué pasó.

```python
driver.get("https://example.com")
driver.save_screenshot("captura.png")

elemento = driver.find_element("tag name", "h1")
elemento.screenshot("elemento.png")
```

**Explicación línea por línea:**
- `save_screenshot(ruta)` guarda toda la ventana.
- `element.screenshot(ruta)` guarda solo el elemento.

### 11.2. Errores comunes

**Error 1 — Guardar sin extensión** o en carpeta inexistente.

### 11.3. Checkpoint de comprensión

1. ¿Para qué sirven las capturas en tests?

### 11.4. Ejercicio propuesto

**Ejercicio 11.1.** Captura la pantalla completa y el elemento principal.

**Pista:** `save_screenshot` y `element.screenshot`.

---

## 12. Ejecución de JavaScript

### 12.1. Cuando la API no basta

`execute_script` ejecuta JavaScript en la página. Útil para scroll, clics forzados o extraer datos.

```python
# scroll al final
driver.execute_script("window.scrollTo(0, document.body.scrollHeight);")

# obtener el título vía JS
titulo = driver.execute_script("return document.title;")
print(titulo)

# clic forzado
boton = driver.find_element("id", "boton")
driver.execute_script("arguments[0].click();", boton)
```

**Explicación línea por línea:**
- El script se ejecuta en la página.
- `arguments[0]` referencia el elemento que pasas como segundo argumento.

### 12.2. Errores comunes

**Error 1 — Usar JS para todo.** Pierdes la simulación real del usuario.

**Error 2 — Errores de sintaxis JS** que rompen el script.

### 12.3. Checkpoint de comprensión

1. ¿Cuándo usarías `execute_script`?
2. ¿Cómo pasas un elemento a un script JS?

### 12.4. Ejercicio propuesto

**Ejercicio 12.1.** Haz scroll hasta un elemento concreto.

**Pista:** `arguments[0].scrollIntoView()`.

---

## 13. Cookies y sesiones

### 13.1. Gestionar cookies

```python
driver.get("https://example.com")

cookies = driver.get_cookies()
print(cookies)

driver.add_cookie({"name": "tema", "value": "oscuro"})
driver.delete_all_cookies()
```

**Explicación línea por línea:**
- `get_cookies()` devuelve todas las cookies.
- `add_cookie(...)` añade una.
- `delete_all_cookies()` las borra.

### 13.2. Errores comunes

**Error 1 — Añadir cookies antes de navegar** al dominio.

*Solución:* visita primero el sitio para que el dominio esté establecido.

**Error 2 — Guardar cookies sensibles** en texto plano.

### 13.3. Checkpoint de comprensión

1. ¿Por qué hay que navegar antes de añadir cookies?

### 13.4. Ejercicio propuesto

**Ejercicio 13.1.** Guarda las cookies en JSON y recupéralas en otra sesión.

**Pista:** usa `json.dump`.

---

## 14. Page Object Model (POM)

### 14.1. El problema del código disperso

Si escribes localizadores y acciones directamente en cada test, cualquier cambio en la página rompe decenas de tests. El **Page Object Model** separa la **estructura de la página** de la **lógica del test**.

**Analogía:** un Page Object es como un mando a distancia bien etiquetado: en lugar de saber las coordenadas del botón "encender", pulsas `mando.encender()`.

### 14.2. Un Page Object

```python
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait


class PaginaLogin:
    """Page Object de la página de login."""

    USUARIO = (By.ID, "usuario")
    PASSWORD = (By.ID, "password")
    BOTON = (By.CSS_SELECTOR, "button[type='submit']")

    def __init__(self, driver) -> None:
        self.driver = driver
        self.wait = WebDriverWait(driver, 10)

    def cargar(self, url: str) -> None:
        self.driver.get(url)

    def login(self, usuario: str, password: str) -> None:
        self.driver.find_element(*self.USUARIO).send_keys(usuario)
        self.driver.find_element(*self.PASSWORD).send_keys(password)
        self.driver.find_element(*self.BOTON).click()
```

**Explicación línea por línea:**
- Los localizadores son **constantes de clase**: si cambian, se actualizan en un solo sitio.
- `*self.USUARIO` **desempaqueta** la tupla `(By.ID, "usuario")` en dos argumentos.
- Los métodos (`cargar`, `login`) encapsulan acciones.

**Uso:**

```python
pagina = PaginaLogin(driver)
pagina.cargar("https://miapp.com/login")
pagina.login("ana", "secreto")
```

### 14.3. Errores comunes

**Error 1 — Meter asserts en el Page Object.** El test debe verificar.

**Error 2 — Duplicar localizadores** en varios sitios.

### 14.4. Checkpoint de comprensión

1. ¿Qué problema resuelve el POM?
2. ¿Qué va en el Page Object y qué en el test?

### 14.5. Ejercicio propuesto

**Ejercicio 14.1.** Crea un Page Object para una página de búsqueda.

**Pista:** expón un método `buscar(termino)`.

---

## 15. Selenium Grid

### 15.1. Ejecución distribuida

**Selenium Grid** ejecuta tests en **varios navegadores y sistemas en paralelo**. Un *hub* central distribuye el trabajo a *nodos*.

### 15.2. Docker Compose

```yaml
services:
  selenium-hub:
    image: selenium/hub:4.20
    ports:
      - "4442:4442"
      - "4443:4443"
      - "4444:4444"
  chrome:
    image: selenium/node-chrome:4.20
    depends_on:
      - selenium-hub
    environment:
      - SE_EVENT_BUS_HOST=selenium-hub
```

### 15.3. Conectar

```python
from selenium import webdriver
from selenium.webdriver.chrome.options import Options

opciones = Options()
driver = webdriver.Remote(command_executor="http://localhost:4444", options=opciones)
driver.get("https://example.com")
print(driver.title)
driver.quit()
```

**Explicación línea por línea:**
- `webdriver.Remote(command_executor=...)` se conecta al Grid en lugar de abrir un navegador local.

### 15.4. Errores comunes

**Error 1 — No esperar a que el Grid esté listo.**

**Error 2 — Configuración de red incorrecta** entre contenedores.

### 15.5. Checkpoint de comprensión

1. ¿Qué ventaja aporta un Grid?
2. ¿Cómo te conectas a un Grid?

### 15.6. Ejercicio propuesto

**Ejercicio 15.1.** Levanta un Grid con Chrome y Firefox y ejecuta un test en cada uno.

**Pista:** usa `webdriver.Remote`.

---

## 16. Headless mode

### 16.1. Navegar sin interfaz

El modo **headless** ejecuta el navegador **sin ventana gráfica**: más rápido y apto para servidores CI.

```python
from selenium import webdriver
from selenium.webdriver.chrome.options import Options

opciones = Options()
opciones.add_argument("--headless=new")
opciones.add_argument("--no-sandbox")
opciones.add_argument("--disable-dev-shm-usage")

driver = webdriver.Chrome(options=opciones)
driver.get("https://example.com")
print(driver.title)
driver.quit()
```

**Explicación línea por línea:**
- `--headless=new` activa el modo sin ventana.
- `--no-sandbox` es necesario en contenedores Docker.
- `--disable-dev-shm-usage` evita problemas de memoria en entornos limitados.

### 16.2. Errores comunes

**Error 1 — Headless con capturas** puede requerir `--window-size`.

**Error 2 — No añadir `--no-sandbox`** en Docker → falla.

### 16.3. Checkpoint de comprensión

1. ¿Qué ventajas tiene el modo headless?
2. ¿Por qué `--no-sandbox` en Docker?

### 16.4. Ejercicio propuesto

**Ejercicio 16.1.** Ejecuta un test en headless y captura pantalla.

**Pista:** añade `--window-size=1920,1080`.

---

## 17. Integración con pytest

### 17.1. Fixtures para el driver

Se usa un *fixture* que crea y cierra el driver por test:

```python
import pytest
from selenium import webdriver
from selenium.webdriver.chrome.options import Options


@pytest.fixture
def driver():
    opciones = Options()
    opciones.add_argument("--headless=new")
    d = webdriver.Chrome(options=opciones)
    yield d
    d.quit()


def test_titulo(driver):
    driver.get("https://example.com")
    assert "Example" in driver.title
```

Ejecución:

```bash
pytest -v
pytest -n 4   # con pytest-xdist (paralelo)
```

**Explicación línea por línea:**
- `yield d` entrega el driver al test y ejecuta `d.quit()` al terminar.

### 17.2. Errores comunes

**Error 1 — Compartir el driver entre tests.** Aísla con fixtures.

**Error 2 — Tests frágiles por depender de datos externos.**

### 17.3. Checkpoint de comprensión

1. ¿Por qué usar un fixture para el driver?
2. ¿Qué hace `pytest-xdist`?

### 17.4. Ejercicio propuesto

**Ejercicio 17.1.** Escribe un test parametrizado que compruebe varios títulos.

**Pista:** usa `@pytest.mark.parametrize`.

---

## 18. Web scraping ético y legal

### 18.1. La responsabilidad

Automatizar un navegador **no** te exime de responsabilidades. Antes de hacer scraping, considera:

- **Términos de servicio** del sitio.
- **`robots.txt`**: qué rutas permite automatizar.
- **Límites de tasa**: no sobrecargar el servidor.
- **Datos personales**: cumple las leyes (GDPR y similares).
- **Derechos de autor** del contenido.

### 18.2. Buenas prácticas

```python
import time

for url in urls:
    driver.get(url)
    procesar(driver.page_source)
    time.sleep(1)   # sé amable con el servidor
```

- Identifícate con un `User-Agent` claro.
- Prefiere APIs oficiales si existen.
- No eludas medidas de seguridad.

### 18.3. Errores comunes

**Error 1 — Scraping agresivo** que tumba el sitio.

**Error 2 — Ignorar la ley** y recopilar datos personales sin base legal.

### 18.4. Checkpoint de comprensión

1. ¿Qué es `robots.txt`?
2. ¿Por qué añadir pausas entre peticiones?

### 18.5. Ejercicio propuesto

**Ejercicio 18.1.** Lee el `robots.txt` de un sitio y determina qué rutas están permitidas.

**Pista:** visita `sitio.com/robots.txt`.

---

## 19. Alternativas: Playwright, Puppeteer

### 19.1. Otras herramientas

- **Playwright** (Microsoft): API moderna, **auto-esperas**, multi-lenguaje.
- **Puppeteer:** Node.js, control de Chrome.
- **Selenium:** estándar, amplio soporte.

### 19.2. Playwright (Python)

```bash
pip install playwright
playwright install
```

```python
from playwright.sync_api import sync_playwright

with sync_playwright() as p:
    navegador = p.chromium.launch()
    pagina = navegador.new_page()
    pagina.goto("https://example.com")
    print(pagina.title())
    navegador.close()
```

**Explicación línea por línea:**
- `sync_playwright()` crea el contexto.
- Playwright incluye **auto-esperas**: espera automáticamente a que los elementos estén listos, lo que reduce la fragilidad.

### 19.3. Errores comunes

**Error 1 — Elegir herramienta por moda.** Valora el soporte y el equipo.

### 19.4. Checkpoint de comprensión

1. ¿Qué ventaja tiene Playwright frente a Selenium?
2. ¿Cuándo seguirías usando Selenium?

### 19.5. Ejercicio propuesto

**Ejercicio 19.1.** Reproduce un test simple en Selenium y Playwright y compara el código.

**Pista:** observa las esperas automáticas de Playwright.

---

## 20. Buenas prácticas

1. **Usa esperas explícitas**, nunca `sleep` fijo.
2. **Aplica Page Object Model.**
3. **Headless en CI**, con ventana en local si depuras.
4. **Cierra siempre el driver.**
5. **Localizadores estables** (id, data-*), evita XPath absoluto.
6. **Captura pantalla en fallos.**
7. **Aísla cada test** con fixtures.
8. **Respeta robots.txt y los términos legales.**
9. **Añade pausas** si haces scraping.
10. **Prefiere APIs oficiales** cuando existan.

---

## 21. Recursos

- **Documentación oficial:** <https://www.selenium.dev/documentation/>
- **Selenium Python:** <https://selenium-python.readthedocs.io/>
- **Playwright:** <https://playwright.dev/python/>
- **Libro:** *Hands-On Selenium WebDriver* (conceptos aplicables).

---

## 22. Mini resumen final

- **Selenium** controla navegadores reales mediante WebDriver.
- Los **localizadores** encuentran elementos; prefiere id/CSS a XPath absoluto.
- Las **esperas explícitas** evitan tests frágiles.
- Se gestionan ventanas, alertas, iframes, dropdowns y acciones complejas.
- **POM** separa estructura y lógica de test.
- **Grid** y **headless** escalan la ejecución.
- **Playwright** es una alternativa moderna con auto-esperas.
- El scraping debe ser **ético y legal**.

---

### 🎯 Retos opcionales

1. **Reto 1:** Automatiza un login completo con POM y captura en fallo.
2. **Reto 2:** Ejecuta la suite en Selenium Grid con Chrome y Firefox.
3. **Reto 3:** Reescribe un test en Playwright y compara la robustez.

**Anterior:** [14_sqlalchemy.md](14_sqlalchemy.md) · **Siguiente:** [16_tkinter.md](16_tkinter.md)
