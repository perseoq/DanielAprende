# 19 · Pandas

**Versión recomendada:** Pandas 2.2 · Python 3.12
**Requisito:** [18_numpy.md](18_numpy.md)
**Nivel:** Intermedio
**Duración estimada de estudio:** 12–16 horas

---

## Índice de contenidos

1. [Introducción a Pandas](#1-introducción-a-pandas)
2. [Instalación y configuración](#2-instalación-y-configuración)
3. [Series y DataFrame](#3-series-y-dataframe)
4. [Creación de DataFrames](#4-creación-de-dataframes)
5. [Indexación y selección](#5-indexación-y-selección-loc-iloc-at-iat)
6. [Lectura y escritura de datos](#6-lectura-y-escritura-de-datos)
7. [Limpieza de datos](#7-limpieza-de-datos)
8. [Valores nulos y duplicados](#8-valores-nulos-y-duplicados)
9. [Filtrado y consultas](#9-filtrado-y-consultas)
10. [Ordenamiento y ranking](#10-ordenamiento-y-ranking)
11. [Agrupación (groupby)](#11-agrupación-groupby)
12. [Agregaciones y transformaciones](#12-agregaciones-y-transformaciones)
13. [Pivot tables y crosstab](#13-pivot-tables-y-crosstab)
14. [Merge, join y concat](#14-merge-join-y-concat)
15. [Series temporales](#15-series-temporales)
16. [Ventanas móviles](#16-ventanas-móviles-rolling-expanding)
17. [Apply, map y applymap](#17-apply-map-y-applymap)
18. [MultiIndex](#18-multiindex)
19. [Categorical data](#19-categorical-data)
20. [Optimización de memoria](#20-optimización-de-memoria)
21. [Visualización con Pandas](#21-visualización-con-pandas)
22. [Integración con NumPy y Matplotlib](#22-integración-con-numpy-y-matplotlib)
23. [Pandas 2.0 y Arrow backend](#23-pandas-20-y-arrow-backend)
24. [Buenas prácticas](#24-buenas-prácticas)
25. [Recursos](#25-recursos)
26. [Mini resumen final](#26-mini-resumen-final)

---

## 1. Introducción a Pandas

### 1.1. Del array a la tabla

NumPy es excelente para arrays numéricos homogéneos, pero la mayoría de los datos del mundo real son **tablas heterogéneas**: columnas de texto, números y fechas mezcladas, con etiquetas. Para eso está **Pandas**.

**Pandas** es la librería de manipulación de **datos tabulares** de Python. Sus dos estructuras son:

- **Series:** un array **etiquetado** unidimensional (como una columna con índice).
- **DataFrame:** una **tabla** bidimensional con columnas etiquetadas (como una hoja de cálculo).

**Analogía:** un DataFrame es como una **hoja de cálculo** en memoria, con nombres de columnas y un índice de filas, sobre la que puedes hacer consultas y transformaciones potentes.

### 1.2. Errores comunes

**Error 1 — Usar Pandas para cálculos numéricos puros.** Usa NumPy.

**Error 2 — Iterar filas** en lugar de vectorizar.

### 1.3. Checkpoint de comprensión

1. ¿Qué diferencia hay entre una Series y un DataFrame?
2. ¿Por qué Pandas sobre NumPy?

### 1.4. Ejercicio propuesto

**Ejercicio 1.1.** Enumera tres tareas típicas de Pandas en un análisis de datos.

**Pista:** limpieza, agrupación, unión.

---

## 2. Instalación y configuración

### 2.1. Instalación

```bash
pip install pandas
```

### 2.2. La convención `pd`

```python
import pandas as pd
import numpy as np

print(pd.__version__)
```

Salida esperada:

```text
2.2.0
```

### 2.3. Opciones de visualización

```python
pd.set_option("display.max_rows", 100)
pd.set_option("display.width", 120)
pd.set_option("display.precision", 2)
```

**Explicación línea por línea:**
- Controlan cuántas filas se muestran, el ancho y la precisión decimal.

### 2.4. Errores comunes

**Error 1 — Nombrar tu archivo `pandas.py`.**

### 2.5. Checkpoint de comprensión

1. ¿Por qué se usa el alias `pd`?

### 2.6. Ejercicio propuesto

**Ejercicio 2.1.** Instala Pandas y muestra su versión.

**Pista:** `pd.__version__`.

---

## 3. Series y DataFrame

### 3.1. Series: una columna etiquetada

Una **Series** tiene valores + índice:

```python
import pandas as pd

s = pd.Series([10, 20, 30], index=["a", "b", "c"], name="ventas")
print(s)
print(s["b"])
print(s.mean())
```

Salida esperada:

```text
a    10
b    20
c    30
Name: ventas, dtype: int64
20
20.0
```

**Explicación línea por línea:**
- `pd.Series([...], index=[...])` crea la serie con etiquetas.
- `s["b"]` accede por **etiqueta**.
- `s.mean()` calcula la media.

### 3.2. DataFrame: una tabla

```python
df = pd.DataFrame({
    "nombre": ["Ana", "Luis", "Eva"],
    "edad": [30, 25, 35],
    "ciudad": ["Madrid", "Lima", "Bogotá"],
})
print(df)
print(df.shape)
print(df.columns.tolist())
print(df.dtypes)
```

Salida esperada:

```text
  nombre  edad  ciudad
0    Ana    30  Madrid
1   Luis    25    Lima
2    Eva    35  Bogotá
(3, 3)
['nombre', 'edad', 'ciudad']
nombre    object
edad       int64
ciudad    object
dtype: object
```

**Explicación línea por línea:**
- `pd.DataFrame({...})` crea una tabla a partir de un diccionario de columnas.
- `df.shape` es `(filas, columnas)`.
- `df.columns` son los nombres de columna.
- `df.dtypes` los tipos de cada columna.

### 3.3. Errores comunes

**Error 1 — Confundir el índice con las columnas.**

**Error 2 — Asumir que el índice es 0..n** (puede ser cualquier cosa).

### 3.4. Checkpoint de comprensión

1. ¿Qué diferencia hay entre el índice y las columnas?
2. ¿Qué atributo da los tipos de columna?

### 3.5. Ejercicio propuesto

**Ejercicio 3.1.** Crea una Serie con tus 3 frutas favoritas y sus precios.

**Pista:** usa `index=`.

---

## 4. Creación de DataFrames

### 4.1. Distintas fuentes

```python
import pandas as pd

# Desde diccionario de listas
df = pd.DataFrame({"a": [1, 2], "b": [3, 4]})

# Desde lista de diccionarios
df2 = pd.DataFrame([
    {"nombre": "Ana", "edad": 30},
    {"nombre": "Luis", "edad": 25},
])

# Desde array NumPy
import numpy as np
df3 = pd.DataFrame(np.arange(6).reshape(2, 3), columns=["x", "y", "z"])

# Con índice personalizado
df4 = pd.DataFrame({"v": [1, 2]}, index=["fila1", "fila2"])
```

**Explicación línea por línea:**
- Un diccionario de listas crea columnas.
- Una lista de diccionarios crea filas.
- Un array NumPy crea la tabla con columnas nombradas.

### 4.2. Columnas calculadas

```python
df = pd.DataFrame({"precio": [10, 20], "cantidad": [2, 3]})
df["total"] = df["precio"] * df["cantidad"]
print(df)
```

Salida esperada:

```text
   precio  cantidad  total
0      10         2     20
1      20         3     60
```

**Explicación línea por línea:**
- `df["total"] = ...` crea una columna nueva calculada a partir de otras. La operación es **vectorizada**.

### 4.3. Errores comunes

**Error 1 — Listas de distinta longitud** en el diccionario → error.

**Error 2 — Mezclar tipos sin querer** en una columna.

### 4.4. Checkpoint de comprensión

1. ¿Cómo creas una columna calculada?
2. ¿Qué diferencia hay entre diccionario de listas y lista de diccionarios?

### 4.5. Ejercicio propuesto

**Ejercicio 4.1.** Crea un DataFrame de productos con precio y stock, y añade el valor total.

**Pista:** columna calculada.

---

## 5. Indexación y selección (loc, iloc, at, iat)

### 5.1. Cuatro formas de acceder

| Método | Selecciona por |
|---|---|
| `[]` | Columna(s) |
| `.loc` | **Etiqueta** |
| `.iloc` | **Posición** |
| `.at` | Etiqueta (escalar) |
| `.iat` | Posición (escalar) |

### 5.2. Ejemplos

```python
import pandas as pd

df = pd.DataFrame({
    "nombre": ["Ana", "Luis", "Eva"],
    "edad": [30, 25, 35],
}, index=["a", "b", "c"])

print(df["nombre"])
print(df.loc["b", "edad"])
print(df.iloc[0, 1])
print(df.loc["a":"b", ["nombre"]])
print(df.iloc[0:2, 0:2])
```

Salida esperada:

```text
a     Ana
b    Luis
c     Eva
Name: nombre, dtype: object
25
30
  nombre
a    Ana
b   Luis
   nombre  edad
a    Ana    30
b   Luis    25
```

**Explicación línea por línea:**
- `df["nombre"]` selecciona la columna.
- `df.loc["b", "edad"]` por **etiqueta**: fila "b", columna "edad".
- `df.iloc[0, 1]` por **posición**: fila 0, columna 1.
- `df.loc["a":"b", ...]` el rango **incluye** "b" (a diferencia del slicing de listas).
- `df.iloc[0:2, 0:2]` el rango **excluye** el final.

**La diferencia clave:** `loc` usa etiquetas y es **inclusivo** en rangos; `iloc` usa posiciones y es **exclusivo**.

### 5.3. Escalares con `at` e `iat`

```python
print(df.at["a", "nombre"])
print(df.iat[0, 0])
```

**Explicación línea por línea:**
- `at`/`iat` son versiones rápidas para acceder a un **único** valor.

### 5.4. Errores comunes

**Error 1 — Confundir `loc` con `iloc`.**

**Error 2 — Encadenar indexación.**

```python
df["nombre"]["a"] = "X"   # ❌ SettingWithCopyWarning
df.loc["a", "nombre"] = "X"   # ✅
```

*Solución:* usa `loc` para asignar.

### 5.5. Checkpoint de comprensión

1. ¿Qué diferencia hay entre `loc` e `iloc`?
2. ¿Por qué evitar el encadenamiento?

### 5.6. Ejercicio propuesto

**Ejercicio 5.1.** Selecciona la fila 2 y la columna "precio" con `loc` e `iloc`.

**Pista:** compara ambos resultados.

---

## 6. Lectura y escritura de datos

### 6.1. Muchos formatos

Pandas lee y escribe CSV, Excel, JSON, SQL, Parquet, etc.

### 6.2. CSV

```python
df = pd.read_csv("datos.csv")
df.to_csv("salida.csv", index=False)
```

**Explicación línea por línea:**
- `read_csv` lee un CSV en un DataFrame.
- `to_csv(..., index=False)` lo guarda sin la columna del índice.

### 6.3. Excel

```python
df = pd.read_excel("datos.xlsx", sheet_name="Hoja1")
df.to_excel("salida.xlsx", index=False)
```

### 6.4. JSON

```python
df = pd.read_json("datos.json")
df.to_json("salida.json", orient="records", force_ascii=False)
```

### 6.5. SQL

```python
from sqlalchemy import create_engine

engine = create_engine("sqlite:///datos.db")
df = pd.read_sql("SELECT * FROM ventas", engine)
df.to_sql("resumen", engine, if_exists="replace", index=False)
```

### 6.6. Parquet (eficiente)

```python
df.to_parquet("datos.parquet", index=False)
df = pd.read_parquet("datos.parquet")
```

**Explicación línea por línea:**
- Parquet es un formato **columnar** y comprimido, ideal para datos grandes.

### 6.7. Errores comunes

**Error 1 — CSV con separador `;`.** Usa `sep=";"`.

**Error 2 — Olvidar `encoding`** con acentos.

**Error 3 — `read_excel` sin `openpyxl`** instalado.

### 6.8. Checkpoint de comprensión

1. ¿Qué formato es más eficiente para datos grandes?
2. ¿Cómo evitas guardar el índice?

### 6.9. Ejercicio propuesto

**Ejercicio 6.1.** Exporta un DataFrame a CSV, Parquet y Excel.

**Pista:** compara tamaños de archivo.

---

## 7. Limpieza de datos

### 7.1. El 80 % del trabajo

La limpieza de datos (renombrar, cambiar tipos, normalizar texto) suele ser la mayor parte del análisis.

```python
import pandas as pd

df = pd.DataFrame({
    " Nombre ": [" ana ", "LUIS", "Eva"],
    "Edad": ["30", "25", "35"],
})

df.columns = df.columns.str.strip().str.lower()
df["nombre"] = df["nombre"].str.strip().str.title()
df["edad"] = df["edad"].astype(int)
print(df)
```

Salida esperada:

```text
  nombre  edad
0    Ana    30
1   Luis    25
2    Eva    35
```

**Explicación línea por línea:**
- `df.columns.str.strip().str.lower()` limpia los nombres de columna.
- `.str.strip().str.title()` normaliza el texto: quita espacios y pone formato título.
- `.astype(int)` convierte el texto a entero.

### 7.2. Reemplazos y renombrado

```python
df = df.rename(columns={"nombre": "cliente"})
df["cliente"] = df["cliente"].replace({"Eva": "Eva María"})
```

**Explicación línea por línea:**
- `rename(columns={...})` cambia nombres de columna.
- `replace({...})` sustituye valores.

### 7.3. Errores comunes

**Error 1 — Convertir a numérico con valores no numéricos.**

```python
pd.to_numeric(df["col"], errors="coerce")   # convierte lo que pueda a NaN
```

**Error 2 — No normalizar mayúsculas/espacios.**

### 7.4. Checkpoint de comprensión

1. ¿Cómo limpias los nombres de columna?
2. ¿Qué hace `errors="coerce"`?

### 7.5. Ejercicio propuesto

**Ejercicio 7.1.** Limpia una columna de teléfonos eliminando guiones y espacios.

**Pista:** `.str.replace`.

---

## 8. Valores nulos y duplicados

### 8.1. Detectar ausencias

Pandas representa ausencias como `NaN`/`NaT`/`None`:

```python
import pandas as pd
import numpy as np

df = pd.DataFrame({"a": [1, np.nan, 3], "b": [np.nan, np.nan, 6]})

print(df.isna())
print(df.isna().sum())
print(df.isna().mean() * 100)
```

Salida esperada:

```text
       a      b
0  False   True
1   True   True
2  False  False
a    1
b    2
dtype: int64
a    33.33
b    66.67
dtype: float64
```

**Explicación línea por línea:**
- `df.isna()` devuelve una máscara de nulos.
- `.sum()` cuenta los nulos por columna.
- `.mean() * 100` da el porcentaje de nulos.

### 8.2. Tratar nulos

```python
df.dropna()                       # elimina filas con nulos
df.dropna(axis=1)                 # elimina columnas con nulos
df.fillna(0)                      # rellena con 0
df["a"] = df["a"].fillna(df["a"].mean())   # rellena con la media
df.ffill()                        # propaga el último valor válido
```

**Explicación línea por línea:**
- `dropna` elimina; `fillna` rellena; `ffill` propaga hacia delante.

### 8.3. Duplicados

```python
df = pd.DataFrame({"a": [1, 1, 2]})
print(df.duplicated())
df_sin_dup = df.drop_duplicates()
```

**Explicación línea por línea:**
- `duplicated()` marca las filas repetidas.
- `drop_duplicates()` las elimina.

### 8.4. Errores comunes

**Error 1 — `fillna` con la media global** cuando debería ser por grupo.

**Error 2 — Eliminar nulos sin analizar por qué faltan.**

### 8.5. Checkpoint de comprensión

1. ¿Cómo cuentas los nulos por columna?
2. ¿Qué diferencia hay entre `dropna` y `fillna`?

### 8.6. Ejercicio propuesto

**Ejercicio 8.1.** Rellena los nulos de una columna numérica con su mediana.

**Pista:** `.fillna(df["col"].median())`.

---

## 9. Filtrado y consultas

### 9.1. Máscaras booleanas

El filtrado usa máscaras y operadores `&`, `|`, `~`:

```python
import pandas as pd

df = pd.DataFrame({
    "nombre": ["Ana", "Luis", "Eva", "Pablo"],
    "edad": [30, 25, 35, 28],
    "ciudad": ["Madrid", "Lima", "Madrid", "Bogotá"],
})

print(df[df["edad"] > 27])
print(df[(df["edad"] > 27) & (df["ciudad"] == "Madrid")])
print(df[df["ciudad"].isin(["Madrid", "Lima"])])
print(df.query("edad > 27 and ciudad == 'Madrid'"))
```

**Explicación línea por línea:**
- `df[df["edad"] > 27]` filtra por la condición.
- `&` combina condiciones (con paréntesis).
- `.isin([...])` filtra por pertenencia.
- `.query("...")` permite escribir la condición como texto.

### 9.2. `between` y `str.contains`

```python
print(df[df["edad"].between(25, 30)])
print(df[df["nombre"].str.contains("a", case=False)])
```

**Explicación línea por línea:**
- `.between(25, 30)` filtra por rango.
- `.str.contains("a", case=False)` busca texto sin distinguir mayúsculas.

### 9.3. Errores comunes

**Error 1 — Usar `and`/`or`** en lugar de `&`/`|`.

**Error 2 — Máscaras con nulos** que dan resultados inesperados.

### 9.4. Checkpoint de comprensión

1. ¿Por qué usar `&` en lugar de `and`?
2. ¿Qué hace `.isin`?

### 9.5. Ejercicio propuesto

**Ejercicio 9.1.** Filtra los productos con precio entre 10 y 50 y stock positivo.

**Pista:** `.between` y `&`.

---

## 10. Ordenamiento y ranking

### 10.1. Ordenar

```python
import pandas as pd

df = pd.DataFrame({"nombre": ["Ana", "Luis", "Eva"], "nota": [8, 9, 7]})

print(df.sort_values("nota", ascending=False))
print(df.sort_values(["nota", "nombre"]))
df["puesto"] = df["nota"].rank(ascending=False, method="min")
print(df)
```

**Explicación línea por línea:**
- `sort_values("nota", ascending=False)` ordena de mayor a menor.
- `sort_values([...])` ordena por varias columnas.
- `rank(...)` asigna posiciones (no reordena).

### 10.2. Errores comunes

**Error 1 — Olvidar `ascending=False`** para descendente.

**Error 2 — Confundir `rank` con `sort`.**

### 10.3. Checkpoint de comprensión

1. ¿Cómo ordenas por dos columnas?
2. ¿Qué hace `rank`?

### 10.4. Ejercicio propuesto

**Ejercicio 10.1.** Ordena un DataFrame por dos columnas en direcciones opuestas.

**Pista:** `ascending=[False, True]`.

---

## 11. Agrupación (groupby)

### 11.1. Split-apply-combine

`groupby` implementa el patrón **dividir-aplicar-combinar**: divide por claves, aplica una función y combina.

```python
import pandas as pd

df = pd.DataFrame({
    "ciudad": ["Madrid", "Lima", "Madrid", "Lima"],
    "ventas": [100, 200, 150, 50],
})

print(df.groupby("ciudad")["ventas"].sum())
print(df.groupby("ciudad")["ventas"].mean())
print(df.groupby("ciudad").agg(["sum", "mean", "count"]))
```

Salida esperada:

```text
ciudad
Lima      250
Madrid    250
Name: ventas, dtype: int64
```

**Explicación línea por línea:**
- `df.groupby("ciudad")` agrupa por la columna.
- `["ventas"].sum()` suma dentro de cada grupo.
- `.agg([...])` aplica varias funciones a la vez.

### 11.2. Agrupar por varias columnas

```python
df.groupby(["ciudad", "vendedor"])["ventas"].sum()
```

### 11.3. `transform` y `filter`

```python
df["pct"] = df.groupby("ciudad")["ventas"].transform(lambda x: x / x.sum())
df_filtrado = df.groupby("ciudad").filter(lambda g: g["ventas"].sum() > 200)
```

**Explicación línea por línea:**
- `transform` devuelve un resultado del **mismo tamaño** que el original.
- `filter` conserva los grupos que cumplen una condición.

### 11.4. Errores comunes

**Error 1 — `groupby` con claves con nulos** (se descartan por defecto).

**Error 2 — Aplicar funciones lentas por grupo.**

### 11.5. Checkpoint de comprensión

1. ¿Qué hace `groupby`?
2. ¿Qué diferencia hay entre `agg` y `transform`?

### 11.6. Ejercicio propuesto

**Ejercicio 11.1.** Calcula el total y la media de ventas por ciudad y vendedor.

**Pista:** `groupby([...]).agg([...])`.

---

## 12. Agregaciones y transformaciones

### 12.1. `agg` vs. `transform`

```python
import pandas as pd

df = pd.DataFrame({
    "grupo": ["A", "A", "B", "B"],
    "valor": [1, 2, 3, 4],
})

print(df.groupby("grupo")["valor"].agg(["sum", "mean", "std"]))
df["desviacion"] = df.groupby("grupo")["valor"].transform(lambda x: x - x.mean())
print(df)
```

Salida esperada:

```text
       sum  mean       std
grupo
A        3   1.5  0.707107
B        7   3.5  0.707107
  grupo  valor  desviacion
0     A      1        -0.5
1     A      2         0.5
2     B      3        -0.5
3     B      4         0.5
```

**Explicación línea por línea:**
- `agg` **reduce** cada grupo a un valor.
- `transform` devuelve un valor por **fila**, alineado con el original.

### 12.2. Agregación con nombres

```python
resultado = df.groupby("grupo").agg(
    total=("valor", "sum"),
    promedio=("valor", "mean"),
)
```

**Explicación línea por línea:**
- `nombre=(columna, función)` produce columnas con nombres claros.

### 12.3. Errores comunes

**Error 1 — Confundir `agg` con `transform`.**

**Error 2 — Funciones personalizadas lentas.**

### 12.4. Checkpoint de comprensión

1. ¿Qué diferencia de tamaño tienen `agg` y `transform`?

### 12.5. Ejercicio propuesto

**Ejercicio 12.1.** Normaliza cada grupo restando su media con `transform`.

**Pista:** `x - x.mean()`.

---

## 13. Pivot tables y crosstab

### 13.1. Reestructurar datos

```python
import pandas as pd

df = pd.DataFrame({
    "fecha": ["2026-01", "2026-01", "2026-02", "2026-02"],
    "producto": ["A", "B", "A", "B"],
    "ventas": [10, 20, 30, 40],
})

tabla = df.pivot_table(index="fecha", columns="producto", values="ventas", aggfunc="sum")
print(tabla)

print(pd.crosstab(df["fecha"], df["producto"]))
```

Salida esperada:

```text
producto   A   B
fecha
2026-01   10  20
2026-02   30  40
```

**Explicación línea por línea:**
- `pivot_table(index, columns, values, aggfunc)` reestructura y agrega.
- `crosstab` cuenta frecuencias entre dos columnas.

### 13.2. Errores comunes

**Error 1 — Claves duplicadas** sin `aggfunc` → error.

**Error 2 — Confundir `pivot` (sin agregación) con `pivot_table`.**

### 13.3. Checkpoint de comprensión

1. ¿Para qué sirve `pivot_table`?
2. ¿Qué diferencia hay entre `pivot` y `pivot_table`?

### 13.4. Ejercicio propuesto

**Ejercicio 13.1.** Crea una tabla dinámica de ventas por mes y producto.

**Pista:** `pivot_table` con `aggfunc="sum"`.

---

## 14. Merge, join y concat

### 14.1. Combinar tablas

- **`merge`:** une por columnas (como SQL JOIN).
- **`join`:** une por índice.
- **`concat`:** apila DataFrames.

### 14.2. `merge`

```python
import pandas as pd

clientes = pd.DataFrame({"id": [1, 2, 3], "nombre": ["Ana", "Luis", "Eva"]})
pedidos = pd.DataFrame({"id": [1, 1, 2], "total": [50, 30, 20]})

print(pd.merge(clientes, pedidos, on="id", how="inner"))
print(pd.merge(clientes, pedidos, on="id", how="left"))
```

Salida esperada:

```text
   id nombre  total
0   1    Ana     50
1   1    Ana     30
2   2   Luis     20
```

**Explicación línea por línea:**
- `on="id"` indica la columna común.
- `how="inner"` solo coincidencias; `how="left"` todas las de la izquierda.

### 14.3. Tipos de join

```python
how="inner"   # solo coincidencias
how="left"    # todas las filas de la izquierda
how="right"   # todas las de la derecha
how="outer"   # todas
```

### 14.4. `concat`

```python
a = pd.DataFrame({"x": [1, 2]})
b = pd.DataFrame({"x": [3, 4]})
print(pd.concat([a, b], ignore_index=True))
print(pd.concat([a, b], axis=1))
```

**Explicación línea por línea:**
- `concat([...], ignore_index=True)` apila **verticalmente** y reindexa.
- `axis=1` apila **horizontalmente**.

### 14.5. Errores comunes

**Error 1 — Claves con tipos distintos** (`int` vs `str`) → sin coincidencias.

**Error 2 — Duplicar filas** por un merge mal planteado.

### 14.6. Checkpoint de comprensión

1. ¿Qué diferencia hay entre `merge` y `concat`?
2. ¿Qué hace `how="left"`?

### 14.7. Ejercicio propuesto

**Ejercicio 14.1.** Une clientes con pedidos por `id` y calcula el total por cliente.

**Pista:** `merge` + `groupby`.

---

## 15. Series temporales

### 15.1. Fechas como índice

Pandas maneja fechas con `DatetimeIndex`, re-muestreo y desplazamientos:

```python
import pandas as pd

fechas = pd.date_range("2026-01-01", periods=6, freq="D")
serie = pd.Series([1, 3, 2, 5, 4, 6], index=fechas)
print(serie)

print(serie.resample("W").sum())          # semanal
print(serie.rolling(window=2).mean())     # media móvil
print(serie.shift(1))                     # desplazamiento
```

**Explicación línea por línea:**
- `pd.date_range(...)` genera un rango de fechas.
- `.resample("W").sum()` agrega por semana.
- `.rolling(window=2).mean()` calcula la media móvil de 2 periodos.
- `.shift(1)` desplaza los valores una posición (para comparar con el periodo anterior).

### 15.2. Acceso por fecha

```python
print(serie["2026-01"])
print(serie["2026-01-01":"2026-01-03"])
```

**Explicación línea por línea:**
- Se puede indexar por año-mes o por rango de fechas.

### 15.3. Zonas horarias

```python
serie.index = serie.index.tz_localize("UTC").tz_convert("Europe/Madrid")
```

### 15.4. Errores comunes

**Error 1 — Índice no temporal** al re-muestrear.

**Error 2 — Frecuencias mal escritas** (`"D"`, `"W"`, `"M"`).

### 15.5. Checkpoint de comprensión

1. ¿Qué hace `resample`?
2. ¿Para qué sirve `shift`?

### 15.6. Ejercicio propuesto

**Ejercicio 15.1.** Crea una serie diaria de 30 días y calcula la media semanal.

**Pista:** `.resample("W").mean()`.

---

## 16. Ventanas móviles (rolling, expanding)

### 16.1. Suavizar y acumular

- **`rolling(window=n)`:** ventana de tamaño fijo.
- **`expanding()`:** ventana que crece.
- **`ewm`:** media exponencial.

```python
import pandas as pd

s = pd.Series([1, 2, 3, 4, 5])
print(s.rolling(window=2).mean())
print(s.expanding().sum())
print(s.ewm(span=2).mean())
```

Salida esperada:

```text
0    NaN
1    1.5
2    2.5
3    3.5
4    4.5
dtype: float64
```

**Explicación línea por línea:**
- `rolling(2).mean()` calcula la media de cada par consecutivo.
- El primer valor es `NaN` porque no hay suficientes datos.
- `expanding().sum()` da la suma acumulada.

### 16.2. Errores comunes

**Error 1 — `window` mayor que los datos** → todo `NaN`.

**Error 2 — No usar `min_periods`** cuando quieres resultados parciales.

### 16.3. Checkpoint de comprensión

1. ¿Qué diferencia hay entre `rolling` y `expanding`?
2. ¿Por qué el primer valor es `NaN`?

### 16.4. Ejercicio propuesto

**Ejercicio 16.1.** Calcula la media móvil de 7 días de una serie de precios.

**Pista:** `.rolling(7).mean()`.

---

## 17. Apply, map y applymap

### 17.1. Aplicar funciones

- **`map`:** aplica a cada elemento de una Series.
- **`apply`:** aplica a filas/columnas de un DataFrame o a una Series.
- **`DataFrame.map`:** aplica a cada celda.

```python
import pandas as pd

s = pd.Series(["ana", "luis"])
print(s.map(str.upper))

df = pd.DataFrame({"a": [1, 2], "b": [3, 4]})
print(df.apply(lambda col: col.max(), axis=0))
print(df.apply(lambda fila: fila.sum(), axis=1))
```

Salida esperada:

```text
0    ANA
1   LUIS
dtype: object
a    2
b    4
dtype: int64
0    4
1    6
dtype: int64
```

**Explicación línea por línea:**
- `s.map(str.upper)` aplica a cada elemento.
- `df.apply(..., axis=0)` aplica por **columna**.
- `df.apply(..., axis=1)` aplica por **fila**.

### 17.2. `map` con diccionario

```python
s = pd.Series(["A", "B", "C"])
print(s.map({"A": "Alto", "B": "Medio", "C": "Bajo"}))
```

**Explicación línea por línea:**
- `map({...})` traduce valores según un diccionario.

### 17.3. Errores comunes

**Error 1 — `apply` fila a fila** cuando hay operación vectorizada.

**Error 2 — Confundir `map` de Series con `DataFrame.map`.**

### 17.4. Checkpoint de comprensión

1. ¿Qué hace `axis=0` en `apply`?
2. ¿Para qué sirve `map` con diccionario?

### 17.5. Ejercicio propuesto

**Ejercicio 17.1.** Mapea una columna de códigos a etiquetas legibles.

**Pista:** `.map({...})`.

---

## 18. MultiIndex

### 18.1. Índices jerárquicos

Un **MultiIndex** tiene varios niveles de índice:

```python
import pandas as pd

df = pd.DataFrame({
    "pais": ["ES", "ES", "MX", "MX"],
    "ciudad": ["Madrid", "Barcelona", "CDMX", "GDL"],
    "ventas": [100, 90, 120, 80],
})
df = df.set_index(["pais", "ciudad"])
print(df)
print(df.loc["ES"])
print(df.xs("Madrid", level="ciudad"))
```

Salida esperada:

```text
                ventas
pais ciudad
ES   Madrid        100
     Barcelona      90
MX   CDMX          120
     GDL            80
```

**Explicación línea por línea:**
- `set_index(["pais", "ciudad"])` crea el MultiIndex.
- `df.loc["ES"]` accede a un nivel.
- `df.xs("Madrid", level="ciudad")` selecciona por un nivel concreto.

### 18.2. Resetear y ordenar

```python
df = df.reset_index()
df = df.set_index(["pais", "ciudad"]).sort_index()
```

### 18.3. Errores comunes

**Error 1 — `loc` incompleto** con MultiIndex.

**Error 2 — No ordenar el índice** antes de ciertas operaciones.

### 18.4. Checkpoint de comprensión

1. ¿Qué es un MultiIndex?
2. ¿Qué hace `xs`?

### 18.5. Ejercicio propuesto

**Ejercicio 18.1.** Crea un MultiIndex (año, mes) y extrae un año completo.

**Pista:** `.loc[2026]`.

---

## 19. Categorical data

### 19.1. Ahorrar memoria y ordenar

El tipo **category** ahorra memoria y acelera operaciones en columnas con pocos valores repetidos:

```python
import pandas as pd

s = pd.Series(["bajo", "alto", "medio", "alto"], dtype="category")
print(s)
print(s.cat.categories)

s = s.cat.reorder_categories(["bajo", "medio", "alto"], ordered=True)
print(s.min(), s.max())
```

Salida esperada:

```text
0     bajo
1     alto
2    medio
3     alto
dtype: category
Categories (3, object): ['alto', 'bajo', 'medio']
bajo alto
```

**Explicación línea por línea:**
- `dtype="category"` convierte a categórica.
- `.cat.categories` lista las categorías.
- `reorder_categories(..., ordered=True)` establece un **orden** (útil para comparar).

### 19.2. Ventajas de memoria

```python
s.memory_usage(deep=True)   # mucho menor que object
```

### 19.3. Errores comunes

**Error 1 — Categorías con demasiados valores únicos** (pierde la ventaja).

**Error 2 — Comparaciones con categorías no ordenadas.**

### 19.4. Checkpoint de comprensión

1. ¿Qué ventaja aporta `category`?
2. ¿Para qué sirve `ordered=True`?

### 19.5. Ejercicio propuesto

**Ejercicio 19.1.** Convierte una columna de tallas (S/M/L) a `category` ordenada.

**Pista:** `reorder_categories(..., ordered=True)`.

---

## 20. Optimización de memoria

### 20.1. Reducir el consumo

Reduce memoria con tipos adecuados (`int32`, `float32`, `category`) y carga por trozos:

```python
import pandas as pd

df = pd.DataFrame({"a": [1, 2, 3], "b": [1.0, 2.0, 3.0]})
print(df.memory_usage(deep=True))

df["a"] = df["a"].astype("int8")
df["b"] = df["b"].astype("float32")
print(df.memory_usage(deep=True))
```

**Explicación línea por línea:**
- `memory_usage(deep=True)` mide el consumo real.
- `astype("int8")` y `astype("float32")` reducen el tamaño de cada valor.

### 20.2. Carga por trozos

```python
for trozo in pd.read_csv("grande.csv", chunksize=100_000):
    procesar(trozo)
```

**Explicación línea por línea:**
- `chunksize` procesa el archivo por partes, sin cargarlo entero.

### 20.3. Errores comunes

**Error 1 — `object` para texto repetido.** Usa `category`.

**Error 2 — Cargar un CSV enorme de golpe.**

### 20.4. Checkpoint de comprensión

1. ¿Cómo reduces la memoria de una columna de texto repetido?
2. ¿Qué hace `chunksize`?

### 20.5. Ejercicio propuesto

**Ejercicio 20.1.** Reduce la memoria de un DataFrame cambiando dtypes.

**Pista:** compara `memory_usage(deep=True)`.

---

## 21. Visualización con Pandas

### 21.1. Gráficos rápidos

Pandas integra Matplotlib con `.plot()`:

```python
import pandas as pd

df = pd.DataFrame({"a": [1, 3, 2, 5], "b": [4, 2, 6, 1]})
df.plot()                 # líneas
df.plot(kind="bar")       # barras
df["a"].plot(kind="hist") # histograma
```

```python
import matplotlib.pyplot as plt
plt.show()
```

**Explicación línea por línea:**
- `.plot()` dibuja líneas por defecto.
- `kind="bar"`, `"hist"`, `"scatter"`, etc., cambian el tipo.

### 21.2. Errores comunes

**Error 1 — No importar Matplotlib** y no ver el gráfico.

**Error 2 — Usar Pandas para gráficos complejos.** Usa Matplotlib/Seaborn directamente.

### 21.3. Checkpoint de comprensión

1. ¿Cómo haces un gráfico de barras con Pandas?
2. ¿Qué librería usa Pandas por debajo?

### 21.4. Ejercicio propuesto

**Ejercicio 21.1.** Dibuja un gráfico de barras de ventas por ciudad.

**Pista:** `df.plot(kind="bar")`.

---

## 22. Integración con NumPy y Matplotlib

### 22.1. Convertir y graficar

Pandas se apoya en NumPy; `.to_numpy()` y `.values` convierten a arrays. Con Matplotlib se personalizan gráficos:

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

df = pd.DataFrame({"x": np.linspace(0, 10, 100)})
df["y"] = np.sin(df["x"])

plt.plot(df["x"], df["y"])
plt.title("Seno")
plt.xlabel("x")
plt.ylabel("sin(x)")
plt.show()
```

**Explicación línea por línea:**
- Se crea una columna `y` con el seno, usando NumPy.
- `plt.plot(...)` dibuja la serie.
- `plt.title`, `xlabel`, `ylabel` añaden etiquetas.

### 22.2. Errores comunes

**Error 1 — Modificar el array devuelto por `.values`** creyendo que es copia.

**Error 2 — Mezclar índices de Pandas y arrays NumPy.**

### 22.3. Checkpoint de comprensión

1. ¿Cómo conviertes un DataFrame a array?
2. ¿Qué librería se usa para personalizar gráficos?

### 22.4. Ejercicio propuesto

**Ejercicio 22.1.** Grafica una serie temporal de Pandas con Matplotlib y añade título.

**Pista:** `plt.plot(serie.index, serie.values)`.

---

## 23. Pandas 2.0 y Arrow backend

### 23.1. El backend de Apache Arrow

Pandas 2.0 introdujo el **backend de Apache Arrow**, que aporta mejor rendimiento y manejo de nulos:

```python
import pandas as pd

df = pd.DataFrame({"a": [1, 2, None]}, dtype="int64[pyarrow]")
print(df)
print(df.dtypes)
```

Salida esperada:

```text
      a
0     1
1     2
2  <NA>
a    int64[pyarrow]
dtype: object
```

**Explicación línea por línea:**
- `dtype="int64[pyarrow]"` usa el backend de Arrow.
- Los nulos se representan como `<NA>` en lugar de `NaN`, y el tipo se mantiene entero.

### 23.2. Conversión

```python
df = df.convert_dtypes(dtype_backend="pyarrow")
```

### 23.3. Errores comunes

**Error 1 — Usar PyArrow sin instalarlo.** `pip install pyarrow`.

**Error 2 — Mezclar dtypes de NumPy y Arrow** sin control.

### 23.4. Checkpoint de comprensión

1. ¿Qué ventaja aporta el backend Arrow?
2. ¿Cómo se representan los nulos con Arrow?

### 23.5. Ejercicio propuesto

**Ejercicio 23.1.** Convierte un DataFrame con nulos a dtypes Arrow y observa el resultado.

**Pista:** `convert_dtypes(dtype_backend="pyarrow")`.

---

## 24. Buenas prácticas

1. **Vectoriza** y evita `apply` fila a fila.
2. **Usa `loc`** para asignaciones, evita el encadenamiento.
3. **Revisa nulos y duplicados** al cargar.
4. **Elige dtypes** eficientes; usa `category`.
5. **Usa Parquet** para almacenamiento eficiente.
6. **Encadena métodos** para pipelines legibles.
7. **No cargues datasets enormes** de golpe.
8. **Usa `groupby` para agregaciones**, no bucles.
9. **Trabaja con fechas como índice** para series temporales.
10. **Verifica el resultado** de los merges (duplicados).

---

## 25. Recursos

- **Documentación oficial:** <https://pandas.pydata.org/docs/>
- **10 minutes to pandas:** <https://pandas.pydata.org/docs/user_guide/10min.html>
- **Cookbook:** <https://pandas.pydata.org/docs/user_guide/cookbook.html>
- **Libro:** *Python for Data Analysis*, Wes McKinney.

---

## 26. Mini resumen final

- **Series** y **DataFrame** son las estructuras centrales.
- Se leen/escriben múltiples formatos (CSV, Excel, JSON, SQL, Parquet).
- `loc`/`iloc`/`at`/`iat` seleccionan datos; el filtrado usa máscaras.
- `groupby` divide-aplica-combina; `agg`/`transform` resumen.
- `merge`/`join`/`concat` combinan tablas.
- Las **series temporales** usan `DatetimeIndex` y `resample`.
- `rolling`/`expanding` calculan ventanas móviles.
- `category` y dtypes adecuados optimizan memoria.
- Pandas 2.0 añade el backend **Arrow**.

---

### 🎯 Retos opcionales

1. **Reto 1:** Limpia un CSV real con nulos, duplicados y tipos incorrectos.
2. **Reto 2:** Construye un análisis de ventas por región y mes con `pivot_table`.
3. **Reto 3:** Calcula la media móvil de 7 días de un precio y detecta tendencias.

**Anterior:** [18_numpy.md](18_numpy.md) · **Siguiente:** [20_scikit_learn.md](20_scikit_learn.md)
