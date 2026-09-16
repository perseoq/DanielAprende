# 20 · Scikit-learn

**Versión recomendada:** scikit-learn 1.5 · Python 3.12
**Requisito:** [19_pandas.md](19_pandas.md)
**Nivel:** Intermedio → Avanzado
**Duración estimada de estudio:** 15–22 horas

---

## Índice de contenidos

1. [Introducción a Scikit-learn](#1-introducción-a-scikit-learn)
2. [Instalación y configuración](#2-instalación-y-configuración)
3. [Estructura de la API: fit, transform, predict](#3-estructura-de-la-api-fit-transform-predict)
4. [Datasets de ejemplo](#4-datasets-de-ejemplo)
5. [Preprocesamiento](#5-preprocesamiento)
6. [Selección de características](#6-selección-de-características)
7. [Reducción de dimensionalidad](#7-reducción-de-dimensionalidad-pca-t-sne-umap)
8. [Aprendizaje supervisado](#8-aprendizaje-supervisado)
9. [Aprendizaje no supervisado](#9-aprendizaje-no-supervisado)
10. [Pipelines](#10-pipelines)
11. [Validación cruzada](#11-validación-cruzada)
12. [Métricas de evaluación](#12-métricas-de-evaluación)
13. [Búsqueda de hiperparámetros](#13-búsqueda-de-hiperparámetros)
14. [Persistencia de modelos](#14-persistencia-de-modelos)
15. [Modelos de ensamblado](#15-modelos-de-ensamblado)
16. [Interpretabilidad](#16-interpretabilidad-shap-lime)
17. [Integración con Pandas y NumPy](#17-integración-con-pandas-y-numpy)
18. [Buenas prácticas en ML](#18-buenas-prácticas-en-ml)
19. [Recursos](#19-recursos)
20. [Mini resumen final](#20-mini-resumen-final)

---

## 1. Introducción a Scikit-learn

### 1.1. ¿Qué es el machine learning?

El **machine learning** (aprendizaje automático) es la disciplina que construye programas que **aprenden de datos** en lugar de ser programados con reglas explícitas. En lugar de decir "si el correo contiene estas palabras, es spam", le muestras miles de correos etiquetados y el modelo **descubre** los patrones.

**Analogía:** programar a mano es dar instrucciones exactas; el machine learning es como enseñar a un niño: le muestras ejemplos y aprende a reconocer patrones por sí solo.

### 1.2. ¿Qué es scikit-learn?

**scikit-learn** es la librería de referencia para **machine learning clásico** en Python. Ofrece:

- Preprocesamiento de datos.
- Modelos supervisados (predicen una etiqueta) y no supervisados (descubren estructura).
- Selección de características y reducción de dimensionalidad.
- Validación, métricas y búsqueda de hiperparámetros.
- **Pipelines** para encadenar pasos.

**No cubre** *deep learning* (para eso: PyTorch, TensorFlow).

### 1.3. Supervisado vs. no supervisado

- **Supervisado:** tienes datos **etiquetados** (con la respuesta correcta). El modelo aprende a predecir. Ejemplos: clasificar spam, predecir precios.
- **No supervisado:** no hay etiquetas. El modelo **descubre** estructura. Ejemplos: agrupar clientes, reducir dimensiones.

### 1.4. El flujo típico de ML

```text
Datos → Limpieza → Preprocesamiento → División train/test → Modelo → Entrenamiento → Evaluación → Ajuste → Predicción
```

### 1.5. Errores comunes

**Error 1 — Filtrar información del test** en el entrenamiento (*data leakage*).

**Error 2 — Evaluar con los datos de entrenamiento.** El modelo "se sabe las respuestas".

### 1.6. Checkpoint de comprensión

1. ¿Qué diferencia hay entre aprendizaje supervisado y no supervisado?
2. ¿Qué cubre scikit-learn y qué no?

### 1.7. Ejercicio propuesto

**Ejercicio 1.1.** Enumera las etapas de un proyecto de ML de principio a fin.

**Pista:** desde la recolección de datos hasta el despliegue.

---

## 2. Instalación y configuración

### 2.1. Instalación

```bash
pip install scikit-learn
```

### 2.2. Verificación

```python
import sklearn

print(sklearn.__version__)
```

Salida esperada:

```text
1.5.0
```

### 2.3. Convención de imports

```python
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import StandardScaler
from sklearn.linear_model import LogisticRegression
```

### 2.4. Errores comunes

**Error 1 — Nombrar tu archivo `sklearn.py`.**

**Error 2 — Dependencias desactualizadas** (NumPy/SciPy).

### 2.5. Checkpoint de comprensión

1. ¿De qué librerías depende scikit-learn?

### 2.6. Ejercicio propuesto

**Ejercicio 2.1.** Instala scikit-learn y comprueba la versión.

**Pista:** `sklearn.__version__`.

---

## 3. Estructura de la API: fit, transform, predict

### 3.1. Una API uniforme

Todos los estimadores de scikit-learn comparten la **misma interfaz**:

- **`fit(X, y)`:** entrena el modelo con datos.
- **`predict(X)`:** predice.
- **`transform(X)`:** transforma datos.
- **`fit_transform(X)`:** entrena y transforma.
- **`score(X, y)`:** evalúa.

Esta uniformidad es una de las mayores fortalezas: aprendes una vez y usas cientos de modelos.

### 3.2. Ejemplo completo

```python
from sklearn.datasets import load_iris
from sklearn.linear_model import LogisticRegression
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import StandardScaler

X, y = load_iris(return_X_y=True)
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

scaler = StandardScaler()
X_train = scaler.fit_transform(X_train)   # aprende y transforma
X_test = scaler.transform(X_test)         # solo transforma

modelo = LogisticRegression(max_iter=200)
modelo.fit(X_train, y_train)
print(modelo.score(X_test, y_test))
```

Salida esperada:

```text
0.9666...
```

**Explicación línea por línea:**
- `train_test_split` divide los datos en entrenamiento y test.
- `scaler.fit_transform(X_train)` **aprende** la media y desviación del train y transforma.
- `scaler.transform(X_test)` aplica **lo aprendido** al test. ¡No vuelvas a hacer `fit` con el test!
- `modelo.fit(...)` entrena; `modelo.score(...)` evalúa.

### 3.3. La regla de oro contra el data leakage

**Ajusta (`fit`) solo con los datos de entrenamiento.** Si usas el test para calcular la media, la desviación u otra transformación, estás filtrando información y tu evaluación será **optimista y falsa**.

### 3.4. Errores comunes

**Error 1 — `fit_transform` en el test.**

**Error 2 — Reutilizar un modelo entrenado con otro escalado.**

### 3.5. Checkpoint de comprensión

1. ¿Qué hace `fit`? ¿Y `transform`?
2. ¿Por qué no se hace `fit` con el test?

### 3.6. Ejercicio propuesto

**Ejercicio 3.1.** Entrena un modelo con iris y reporta el accuracy.

**Pista:** `modelo.score`.

---

## 4. Datasets de ejemplo

### 4.1. Datasets incluidos

`sklearn.datasets` incluye datasets clásicos y generadores:

```python
from sklearn.datasets import (
    load_diabetes,
    load_iris,
    make_classification,
    make_regression,
)

iris = load_iris()
print(iris.data.shape, iris.target_names)

diabetes = load_diabetes()
print(diabetes.data.shape)

X, y = make_classification(n_samples=1000, n_features=20, random_state=42)
print(X.shape, y.shape)

Xr, yr = make_regression(n_samples=500, n_features=10, noise=0.1, random_state=42)
```

**Explicación línea por línea:**
- `load_iris()` carga un dataset clásico de flores.
- `make_classification(...)` genera datos de clasificación sintéticos.
- `make_regression(...)` genera datos de regresión con ruido.

### 4.2. Dividir train/test

```python
from sklearn.model_selection import train_test_split

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, stratify=y, random_state=42)
```

**Explicación línea por línea:**
- `test_size=0.2` reserva el 20 % para test.
- `stratify=y` mantiene la proporción de clases (importante en clasificación desbalanceada).
- `random_state=42` fija la semilla para reproducibilidad.

### 4.3. Errores comunes

**Error 1 — No fijar `random_state`** → resultados no reproducibles.

**Error 2 — No usar `stratify`** en clasificación desbalanceada.

### 4.4. Checkpoint de comprensión

1. ¿Qué hace `stratify`?
2. ¿Por qué fijar `random_state`?

### 4.5. Ejercicio propuesto

**Ejercicio 4.1.** Genera un dataset de regresión con ruido y divídelo 80/20.

**Pista:** `make_regression`.

---

## 5. Preprocesamiento

### 5.1. Escalado

Muchos modelos (SVM, KNN, redes) son sensibles a la **escala** de las variables. Si una columna va de 0 a 1 y otra de 0 a 1 000 000, la segunda dominará.

```python
from sklearn.preprocessing import MinMaxScaler, RobustScaler, StandardScaler

StandardScaler()   # media 0, desviación 1
MinMaxScaler()     # escala a [0, 1]
RobustScaler()     # robusto a outliers
```

**Explicación línea por línea:**
- `StandardScaler` estandariza (lo más común).
- `MinMaxScaler` comprime a un rango.
- `RobustScaler` usa mediana y rango intercuartílico (menos sensible a valores extremos).

### 5.2. Normalización

```python
from sklearn.preprocessing import Normalizer

Normalizer()   # normaliza cada muestra a norma 1
```

**Explicación línea por línea:**
- Escalado actúa por **columna**; normalización, por **fila** (cada muestra).

### 5.3. Codificación de variables categóricas

```python
from sklearn.preprocessing import LabelEncoder, OneHotEncoder

le = LabelEncoder()
y = le.fit_transform(["gato", "perro", "gato"])

ohe = OneHotEncoder(sparse_output=False)
X = ohe.fit_transform([["rojo"], ["verde"], ["rojo"]])
```

**Explicación línea por línea:**
- `LabelEncoder` convierte etiquetas a números (útil para el **target**).
- `OneHotEncoder` crea columnas binarias (útil para **features**).

### 5.4. Imputación de valores faltantes

```python
from sklearn.impute import SimpleImputer

imputer = SimpleImputer(strategy="median")   # o "mean", "most_frequent"
X = imputer.fit_transform(X)
```

**Explicación línea por línea:**
- `SimpleImputer` rellena los nulos con una estrategia.

### 5.5. Discretización

```python
from sklearn.preprocessing import KBinsDiscretizer

kb = KBinsDiscretizer(n_bins=5, encode="ordinal", strategy="quantile")
```

**Explicación línea por línea:**
- `KBinsDiscretizer` convierte variables continuas en intervalos.

### 5.6. Errores comunes

**Error 1 — Escalar antes de dividir** → fuga de datos. Ajusta solo con train.

**Error 2 — One-hot en columnas de alta cardinalidad** (explosión de features).

### 5.7. Checkpoint de comprensión

1. ¿Por qué escalar?
2. ¿Qué diferencia hay entre `LabelEncoder` y `OneHotEncoder`?

### 5.8. Ejercicio propuesto

**Ejercicio 5.1.** Estandariza las features numéricas e imputa los nulos con la mediana.

**Pista:** encadena `SimpleImputer` + `StandardScaler`.

---

## 6. Selección de características

### 6.1. Reducir variables

Más variables no siempre es mejor: pueden añadir ruido y sobreajuste. La **selección** elige las más informativas:

```python
from sklearn.ensemble import RandomForestClassifier
from sklearn.feature_selection import RFE, SelectKBest, f_classif

selector = SelectKBest(score_func=f_classif, k=10)
X_new = selector.fit_transform(X, y)

rfe = RFE(RandomForestClassifier(n_estimators=50), n_features_to_select=5)
rfe.fit(X, y)
print(rfe.support_)
```

**Explicación línea por línea:**
- `SelectKBest` selecciona las `k` mejores según una prueba estadística.
- `RFE` (*Recursive Feature Elimination*) elimina recursivamente las menos importantes.

### 6.2. Importancia desde modelos

```python
modelo = RandomForestClassifier().fit(X, y)
print(modelo.feature_importances_)
```

**Explicación línea por línea:**
- Los modelos de árbol exponen la importancia de cada característica.

### 6.3. Errores comunes

**Error 1 — Seleccionar usando todo el dataset** antes de dividir.

**Error 2 — Eliminar features sin entender el dominio.**

### 6.4. Checkpoint de comprensión

1. ¿Por qué reducir características?
2. ¿Qué hace `SelectKBest`?

### 6.5. Ejercicio propuesto

**Ejercicio 6.1.** Selecciona las 5 mejores features con `SelectKBest`.

**Pista:** usa `f_classif`.

---

## 7. Reducción de dimensionalidad (PCA, t-SNE, UMAP)

### 7.1. Tres técnicas

- **PCA:** proyección **lineal** que maximiza la varianza.
- **t-SNE:** visualización **no lineal** (2D/3D).
- **UMAP:** alternativa moderna más rápida.

### 7.2. PCA

```python
from sklearn.decomposition import PCA
from sklearn.preprocessing import StandardScaler

X_scaled = StandardScaler().fit_transform(X)
pca = PCA(n_components=2)
X_pca = pca.fit_transform(X_scaled)
print(pca.explained_variance_ratio_)
```

Salida esperada:

```text
[0.55 0.23]
```

**Explicación línea por línea:**
- `PCA(n_components=2)` reduce a 2 dimensiones.
- `explained_variance_ratio_` indica qué fracción de la varianza conserva cada componente.

### 7.3. t-SNE

```python
from sklearn.manifold import TSNE

X_tsne = TSNE(n_components=2, perplexity=30, random_state=42).fit_transform(X_scaled)
```

**Explicación línea por línea:**
- `perplexity` controla el equilibrio entre estructura local y global.

### 7.4. Errores comunes

**Error 1 — Aplicar PCA sin escalar.**

**Error 2 — Usar t-SNE para clustering** (es para visualizar).

### 7.5. Checkpoint de comprensión

1. ¿Qué hace PCA?
2. ¿Por qué t-SNE es solo para visualizar?

### 7.6. Ejercicio propuesto

**Ejercicio 7.1.** Reduce iris a 2 componentes con PCA y reporta la varianza explicada.

**Pista:** `explained_variance_ratio_`.

---

## 8. Aprendizaje supervisado

### 8.1. Regresión lineal

```python
from sklearn.linear_model import Lasso, LinearRegression, Ridge

modelo = LinearRegression().fit(X_train, y_train)
ridge = Ridge(alpha=1.0).fit(X_train, y_train)
lasso = Lasso(alpha=0.1).fit(X_train, y_train)
```

**Explicación línea por línea:**
- `LinearRegression` es la regresión básica.
- `Ridge` añade regularización L2 (penaliza coeficientes grandes).
- `Lasso` añade regularización L1 (puede llevar coeficientes a cero).

### 8.2. Regresión logística

```python
from sklearn.linear_model import LogisticRegression

clf = LogisticRegression(max_iter=1000).fit(X_train, y_train)
print(clf.predict(X_test[:5]))
print(clf.predict_proba(X_test[:5]))
```

**Explicación línea por línea:**
- A pesar del nombre, es un modelo de **clasificación**.
- `predict` da la clase; `predict_proba` las probabilidades.

### 8.3. Árboles y bosques

```python
from sklearn.ensemble import RandomForestClassifier
from sklearn.tree import DecisionTreeClassifier

arbol = DecisionTreeClassifier(max_depth=4, random_state=42).fit(X_train, y_train)
bosque = RandomForestClassifier(n_estimators=200, random_state=42).fit(X_train, y_train)
```

**Explicación línea por línea:**
- `DecisionTreeClassifier` es un árbol de decisión.
- `RandomForestClassifier` combina muchos árboles (menos sobreajuste).

### 8.4. Gradient Boosting

```python
from sklearn.ensemble import GradientBoostingClassifier

gb = GradientBoostingClassifier(n_estimators=100, learning_rate=0.1).fit(X_train, y_train)
```

**Explicación línea por línea:**
- El *boosting* entrena modelos secuencialmente, corrigiendo errores.
- Alternativas: **XGBoost**, **LightGBM**, **CatBoost**.

### 8.5. SVM, KNN y Naive Bayes

```python
from sklearn.neighbors import KNeighborsClassifier
from sklearn.naive_bayes import GaussianNB
from sklearn.svm import SVC

svm = SVC(kernel="rbf").fit(X_train, y_train)
knn = KNeighborsClassifier(n_neighbors=5).fit(X_train, y_train)
nb = GaussianNB().fit(X_train, y_train)
```

### 8.6. Errores comunes

**Error 1 — No escalar** para SVM/KNN.

**Error 2 — Overfitting** con árboles profundos.

### 8.7. Checkpoint de comprensión

1. ¿Qué diferencia hay entre un árbol y un bosque?
2. ¿Qué modelos requieren escalado?

### 8.8. Ejercicio propuesto

**Ejercicio 8.1.** Compara regresión logística, árbol y bosque en un dataset de clasificación.

**Pista:** usa `accuracy_score` para cada uno.

---

## 9. Aprendizaje no supervisado

### 9.1. K-Means

```python
from sklearn.cluster import KMeans

kmeans = KMeans(n_clusters=3, random_state=42, n_init="auto")
etiquetas = kmeans.fit_predict(X_scaled)
print(kmeans.inertia_)
```

**Explicación línea por línea:**
- `n_clusters=3` busca 3 grupos.
- `inertia_` mide la dispersión dentro de los grupos (menor es mejor).

### 9.2. DBSCAN

```python
from sklearn.cluster import DBSCAN

db = DBSCAN(eps=0.5, min_samples=5)
etiquetas = db.fit_predict(X_scaled)
```

**Explicación línea por línea:**
- `eps` es el radio de vecindad; `min_samples`, el mínimo de puntos para formar un grupo.
- Detecta **outliers** (etiqueta -1).

### 9.3. Clustering jerárquico

```python
from scipy.cluster.hierarchy import linkage

Z = linkage(X_scaled, method="ward")
```

### 9.4. Gaussian Mixture Models

```python
from sklearn.mixture import GaussianMixture

gmm = GaussianMixture(n_components=3, random_state=42)
etiquetas = gmm.fit_predict(X_scaled)
```

### 9.5. Errores comunes

**Error 1 — No escalar** antes de K-Means/DBSCAN.

**Error 2 — Elegir `k` arbitrariamente** sin método del codo o silhouette.

### 9.6. Checkpoint de comprensión

1. ¿Qué es la inercia en K-Means?
2. ¿Qué detecta DBSCAN que K-Means no?

### 9.7. Ejercicio propuesto

**Ejercicio 9.1.** Aplica K-Means con k de 1 a 10 y dibuja la inercia (método del codo).

**Pista:** guarda `inertia_` en cada iteración.

---

## 10. Pipelines

### 10.1. Encadenar pasos

Un **Pipeline** encadena preprocesamiento y modelo, evitando fugas de datos y simplificando el código:

```python
from sklearn.impute import SimpleImputer
from sklearn.linear_model import LogisticRegression
from sklearn.pipeline import Pipeline
from sklearn.preprocessing import StandardScaler

pipeline = Pipeline([
    ("imputer", SimpleImputer(strategy="median")),
    ("scaler", StandardScaler()),
    ("clf", LogisticRegression(max_iter=1000)),
])

pipeline.fit(X_train, y_train)
print(pipeline.score(X_test, y_test))
```

**Explicación línea por línea:**
- Cada paso tiene un nombre y un transformador/estimador.
- Al llamar a `fit`, cada paso se ajusta **en orden**.
- En validación cruzada, el escalado se ajusta **solo** con el train de cada pliegue: **no hay fuga**.

### 10.2. `ColumnTransformer` para columnas distintas

```python
from sklearn.compose import ColumnTransformer
from sklearn.preprocessing import OneHotEncoder

preprocesador = ColumnTransformer([
    ("num", StandardScaler(), ["edad", "salario"]),
    ("cat", OneHotEncoder(), ["ciudad"]),
])
```

**Explicación línea por línea:**
- Aplica transformaciones distintas a grupos de columnas.

### 10.3. Errores comunes

**Error 1 — Escalar fuera del pipeline** → fuga en validación cruzada.

**Error 2 — Nombres de pasos duplicados.**

### 10.4. Checkpoint de comprensión

1. ¿Por qué un pipeline evita la fuga de datos?
2. ¿Para qué sirve `ColumnTransformer`?

### 10.5. Ejercicio propuesto

**Ejercicio 10.1.** Crea un pipeline con imputación, escalado y clasificador.

**Pista:** usa `Pipeline` y `ColumnTransformer`.

---

## 11. Validación cruzada

### 11.1. Más fiable que un solo split

La **validación cruzada** divide los datos en *k* pliegues y entrena/evalúa *k* veces, dando una estimación más estable:

```python
from sklearn.model_selection import cross_val_score

scores = cross_val_score(pipeline, X, y, cv=5, scoring="accuracy")
print(scores)
print(scores.mean(), scores.std())
```

**Explicación línea por línea:**
- `cv=5` usa 5 pliegues.
- `scores` es el resultado de cada pliegue; la media y la desviación resumen el rendimiento.

### 11.2. Estrategias

```python
from sklearn.model_selection import KFold, StratifiedKFold, TimeSeriesSplit

KFold(n_splits=5, shuffle=True, random_state=42)
StratifiedKFold(n_splits=5)   # mantiene proporción de clases
TimeSeriesSplit(n_splits=5)   # series temporales
```

**Explicación línea por línea:**
- `StratifiedKFold` mantiene la proporción de clases.
- `TimeSeriesSplit` respeta el orden temporal (no mezcla pasado y futuro).

### 11.3. Errores comunes

**Error 1 — Validación cruzada sin pipeline** → fuga de datos.

**Error 2 — Mezclar datos temporales** con `KFold`.

### 11.4. Checkpoint de comprensión

1. ¿Qué ventaja tiene la validación cruzada?
2. ¿Cuándo usar `TimeSeriesSplit`?

### 11.5. Ejercicio propuesto

**Ejercicio 11.1.** Evalúa un modelo con validación cruzada de 10 pliegues.

**Pista:** `cross_val_score(..., cv=10)`.

---

## 12. Métricas de evaluación

### 12.1. Clasificación

```python
from sklearn.metrics import (
    accuracy_score,
    classification_report,
    confusion_matrix,
    f1_score,
    precision_score,
    recall_score,
    roc_auc_score,
)

y_pred = modelo.predict(X_test)
print(accuracy_score(y_test, y_pred))
print(confusion_matrix(y_test, y_pred))
print(classification_report(y_test, y_pred))
print(roc_auc_score(y_test, modelo.predict_proba(X_test)[:, 1]))
```

**Explicación línea por línea:**
- `accuracy_score` es el porcentaje de aciertos.
- `confusion_matrix` muestra aciertos y errores por clase.
- `classification_report` da precision, recall y F1 por clase.
- `roc_auc_score` mide la capacidad de ranking.

### 12.2. Regresión

```python
import numpy as np
from sklearn.metrics import mean_absolute_error, mean_squared_error, r2_score

print(mean_absolute_error(y_test, y_pred))
print(np.sqrt(mean_squared_error(y_test, y_pred)))
print(r2_score(y_test, y_pred))
```

**Explicación línea por línea:**
- `MAE` es el error absoluto medio.
- `RMSE` penaliza más los errores grandes.
- `R²` mide qué fracción de la varianza se explica.

### 12.3. Cuándo usar cada una

| Métrica | Cuándo |
|---|---|
| Accuracy | Clases balanceadas |
| Precision/Recall/F1 | Clases desbalanceadas |
| ROC-AUC | Ranking de probabilidades |
| MAE/RMSE/R² | Regresión |

**Precision vs. recall:** *precision* = de los que dije positivos, cuántos lo eran; *recall* = de los positivos reales, cuántos detecté. En detección de fraude, priorizas recall; en filtros de spam, precision.

### 12.4. Errores comunes

**Error 1 — Usar accuracy** con clases muy desbalanceadas.

**Error 2 — No mirar la matriz de confusión.**

### 12.5. Checkpoint de comprensión

1. ¿Qué diferencia hay entre precision y recall?
2. ¿Qué métrica usas en regresión?

### 12.6. Ejercicio propuesto

**Ejercicio 12.1.** Genera un `classification_report` y explica precision vs recall.

**Pista:** usa un dataset desbalanceado.

---

## 13. Búsqueda de hiperparámetros

### 13.1. Ajustar el modelo

Los **hiperparámetros** son configuraciones que tú eliges (no se aprenden). Buscarlos mejora el rendimiento:

```python
from sklearn.model_selection import GridSearchCV

parametros = {
    "clf__C": [0.1, 1, 10],
    "clf__penalty": ["l2"],
}

busqueda = GridSearchCV(pipeline, parametros, cv=5, scoring="f1", n_jobs=-1)
busqueda.fit(X_train, y_train)
print(busqueda.best_params_)
print(busqueda.best_score_)
```

**Explicación línea por línea:**
- `parametros` lista las combinaciones a probar (`clf__C` se refiere al paso `clf`).
- `n_jobs=-1` usa todos los núcleos.
- `best_params_` y `best_score_` dan la mejor configuración.

### 13.2. Random search

```python
from scipy.stats import randint
from sklearn.model_selection import RandomizedSearchCV

distribuciones = {"clf__C": randint(1, 100)}
random_search = RandomizedSearchCV(pipeline, distribuciones, n_iter=20, cv=5, random_state=42)
```

**Explicación línea por línea:**
- `RandomizedSearchCV` muestrea combinaciones, más eficiente cuando hay muchos hiperparámetros.

### 13.3. Errores comunes

**Error 1 — Grid demasiado grande** (lento). Usa random o bayesiana.

**Error 2 — Buscar en el test set** (fuga). La búsqueda va en train.

### 13.4. Checkpoint de comprensión

1. ¿Qué es un hiperparámetro?
2. ¿Por qué `GridSearchCV` puede ser lento?

### 13.5. Ejercicio propuesto

**Ejercicio 13.1.** Ajusta `n_estimators` y `max_depth` de un bosque con `GridSearchCV`.

**Pista:** usa `n_jobs=-1`.

---

## 14. Persistencia de modelos

### 14.1. Guardar y cargar

Guarda modelos entrenados con **joblib** (recomendado para scikit-learn):

```python
import joblib

joblib.dump(pipeline, "modelo.joblib")
modelo_cargado = joblib.load("modelo.joblib")
print(modelo_cargado.predict(X_test[:3]))
```

**Explicación línea por línea:**
- `joblib.dump` serializa el modelo a disco.
- `joblib.load` lo recupera para predecir.

### 14.2. Errores comunes

**Error 1 — Cargar un modelo con distinta versión de scikit-learn.**

**Error 2 — Guardar con `pickle`** modelos grandes (usa `joblib`).

### 14.3. Checkpoint de comprensión

1. ¿Por qué usar `joblib` en lugar de `pickle`?
2. ¿Qué riesgo hay al cargar un modelo con otra versión?

### 14.4. Ejercicio propuesto

**Ejercicio 14.1.** Guarda y recarga un pipeline y verifica que predice igual.

**Pista:** `joblib.dump` / `joblib.load`.

---

## 15. Modelos de ensamblado

### 15.1. Combinar modelos

- **Bagging:** entrena modelos en subconjuntos (Random Forest).
- **Boosting:** entrena secuencialmente corrigiendo errores (Gradient Boosting).
- **Stacking:** combina modelos con un meta-modelo.
- **Voting:** vota entre modelos.

```python
from sklearn.ensemble import RandomForestClassifier, StackingClassifier, VotingClassifier
from sklearn.linear_model import LogisticRegression
from sklearn.svm import SVC

voting = VotingClassifier([
    ("lr", LogisticRegression(max_iter=1000)),
    ("svc", SVC(probability=True)),
], voting="soft")

stacking = StackingClassifier([
    ("lr", LogisticRegression(max_iter=1000)),
    ("rf", RandomForestClassifier()),
], final_estimator=LogisticRegression())
```

**Explicación línea por línea:**
- `VotingClassifier` combina predicciones por votación.
- `StackingClassifier` entrena un meta-modelo con las salidas de los base.

### 15.2. Errores comunes

**Error 1 — Ensamblar modelos correlacionados** (poco aporte).

**Error 2 — Ignorar el coste computacional.**

### 15.3. Checkpoint de comprensión

1. ¿Qué diferencia hay entre bagging y boosting?
2. ¿Qué es stacking?

### 15.4. Ejercicio propuesto

**Ejercicio 15.1.** Compara un `VotingClassifier` con sus componentes individuales.

**Pista:** usa `cross_val_score`.

---

## 16. Interpretabilidad (SHAP, LIME)

### 16.1. Entender el modelo

- **Importancias:** nativas en modelos de árbol.
- **Permutation importance:** mide el impacto en la métrica.
- **SHAP:** valores que explican cada predicción.
- **LIME:** explica predicciones locales.

### 16.2. Permutation importance

```python
from sklearn.inspection import permutation_importance

resultado = permutation_importance(modelo, X_test, y_test, n_repeats=10, random_state=42)
print(resultado.importances_mean)
```

**Explicación línea por línea:**
- Permuta cada característica y mide cuánto empeora la métrica. Si empeora mucho, era importante.

### 16.3. SHAP

```bash
pip install shap
```

```python
import shap

explainer = shap.Explainer(modelo, X_train)
valores = explainer(X_test)
shap.summary_plot(valores, X_test)
```

**Explicación línea por línea:**
- `shap.Explainer` calcula la contribución de cada característica a cada predicción.

### 16.4. Errores comunes

**Error 1 — Confundir correlación con causalidad** en las importancias.

**Error 2 — Interpretar SHAP sin contexto del dominio.**

### 16.5. Checkpoint de comprensión

1. ¿Para qué sirve permutation importance?
2. ¿Qué aporta SHAP frente a las importancias nativas?

### 16.6. Ejercicio propuesto

**Ejercicio 16.1.** Calcula permutation importance de un bosque y ordena las features.

**Pista:** `permutation_importance`.

---

## 17. Integración con Pandas y NumPy

### 17.1. Trabajar con DataFrames

scikit-learn acepta arrays NumPy y DataFrames; con `ColumnTransformer` maneja columnas por tipo:

```python
import pandas as pd
from sklearn.compose import ColumnTransformer
from sklearn.linear_model import LogisticRegression
from sklearn.pipeline import Pipeline
from sklearn.preprocessing import OneHotEncoder, StandardScaler

df = pd.DataFrame({
    "edad": [25, 30, 35, 40],
    "ciudad": ["ES", "MX", "ES", "MX"],
    "compra": [0, 1, 0, 1],
})

X = df.drop(columns="compra")
y = df["compra"]

pre = ColumnTransformer([
    ("num", StandardScaler(), ["edad"]),
    ("cat", OneHotEncoder(), ["ciudad"]),
])

pipeline = Pipeline([("pre", pre), ("clf", LogisticRegression())])
pipeline.fit(X, y)
```

**Explicación línea por línea:**
- `ColumnTransformer` aplica escalado a las numéricas y one-hot a las categóricas.
- El pipeline mantiene todo el flujo en orden.

### 17.2. Errores comunes

**Error 1 — Pasar columnas con distinto orden** entre train y predict.

**Error 2 — No capturar nombres de columnas** en producción.

### 17.3. Checkpoint de comprensión

1. ¿Por qué usar `ColumnTransformer` con DataFrames?
2. ¿Qué riesgo hay con el orden de columnas?

### 17.4. Ejercicio propuesto

**Ejercicio 17.1.** Construye un pipeline con columnas numéricas y categóricas de un DataFrame.

**Pista:** `ColumnTransformer`.

---

## 18. Buenas prácticas en ML

1. **Divide train/test** antes de cualquier transformación.
2. **Usa pipelines** para evitar fugas.
3. **Escala** para SVM/KNN/K-Means.
4. **Valida con cross-validation.**
5. **Elige métricas** acordes al problema.
6. **Fija `random_state`** para reproducibilidad.
7. **No ajustes hiperparámetros en el test.**
8. **Documenta y versiona** los modelos.
9. **Vigila el overfitting/underfitting.**
10. **Interpreta** antes de confiar.

---

## 19. Recursos

- **Documentación oficial:** <https://scikit-learn.org/stable/>
- **User Guide:** <https://scikit-learn.org/stable/user_guide.html>
- **Ejemplos:** <https://scikit-learn.org/stable/auto_examples/index.html>
- **SHAP:** <https://shap.readthedocs.io/>
- **Libro:** *Hands-On Machine Learning*, Aurélien Géron.

---

## 20. Mini resumen final

- scikit-learn cubre **ML clásico** con una API uniforme (`fit`/`predict`).
- El **preprocesamiento** escala, codifica e imputa datos.
- La **selección de características** y **PCA** reducen dimensionalidad.
- Hay modelos **supervisados** (regresión, clasificación) y **no supervisados** (clustering).
- Los **pipelines** evitan fugas y simplifican.
- La **validación cruzada** y las **métricas** evalúan el modelo.
- La **búsqueda de hiperparámetros** optimiza.
- **joblib** persiste modelos; **SHAP/LIME** los interpretan.

---

### 🎯 Retos opcionales

1. **Reto 1:** Construye un pipeline completo para un dataset real (limpieza → modelo → evaluación).
2. **Reto 2:** Compara 5 modelos con validación cruzada y elige el mejor.
3. **Reto 3:** Interpreta un bosque con permutation importance y SHAP.

**Anterior:** [19_pandas.md](19_pandas.md) · **Siguiente:** [21_opencv.md](21_opencv.md)
