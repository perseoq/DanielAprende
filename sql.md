Para crear un usuario y asignarle permisos en **MySQL**, debes conectarte a tu servidor como administrador (`root`) y seguir estos pasos básicos:

---

### 1. Iniciar sesión en MySQL

Abre tu terminal o consola y conéctate al servidor con el usuario root:

```bash
mysql -u root -p

```

Ingresa tu contraseña de administrador cuando se te solicite.

---

### 2. Crear un nuevo usuario

Utiliza la instrucción `CREATE USER`. Puedes definir si el usuario se conectará localmente (`'localhost'`) o desde cualquier equipo (`'%'`):

```sql
CREATE USER 'nombre_usuario'@'localhost' IDENTIFIED BY 'tu_contraseña';

```

---

### 3. Otorgar permisos al usuario

Puedes conceder privilegios específicos sobre una base de datos utilizando el comando `GRANT`.

* **Dar todos los privilegios sobre una base de datos específica:**
```sql
GRANT ALL PRIVILEGES ON nombre_base_de_datos.* TO 'nombre_usuario'@'localhost';

```


* **Dar permisos específicos (ej. solo lectura y escritura: `SELECT`, `INSERT`, `UPDATE`):**
```sql
GRANT SELECT, INSERT, UPDATE ON nombre_base_de_datos.* TO 'nombre_usuario'@'localhost';

```


* **Dar privilegios globales (sobre todo el servidor, úsalo con precaución):**
```sql
GRANT ALL PRIVILEGES ON *.* TO 'nombre_usuario'@'localhost';

```



---

### 4. Actualizar los privilegios

Una vez asignados los permisos, es recomendable recargar la tabla de privilegios de MySQL para que los cambios surtan efecto de inmediato:

```sql
FLUSH PRIVILEGES;

```

---

### Comprobación adicional

Puedes verificar los permisos asignados a un usuario ejecutando:

```sql
SHOW GRANTS FOR 'nombre_usuario'@'localhost';

```

Para crear una base de datos en español en **MySQL** (es decir, que soporte correctamente tildes, eñes y caracteres especiales del idioma español), debes asegurarte de asignarle el conjunto de caracteres (`CHARACTER SET`) y el cotejamiento (`COLLATE`) adecuados, comúnmente `utf8mb4` con una secuencia de ordenamiento en español.

Aquí tienes los pasos detallados:

---

### 1. Conectarte a MySQL

Abre tu terminal y conéctate como administrador:

```bash
mysql -u root -p

```

---

### 2. Crear la base de datos con soporte en español

Puedes utilizar el cotejamiento moderno universal de Unicode adaptado para español (`utf8mb4_unicode_ci`) o el específico para el idioma (`utf8mb4_spanish_ci`, que ordena de forma tradicional la "ch" y la "ll" si lo requieres).

```sql
CREATE DATABASE nombre_base_de_datos 
CHARACTER SET utf8mb4 
COLLATE utf8mb4_unicode_ci;

```

O si prefieres el ordenamiento tradicional en español:

```sql
CREATE DATABASE nombre_base_de_datos 
CHARACTER SET utf8mb4 
COLLATE utf8mb4_spanish_ci;

```

---

### 3. Verificar la configuración

Para comprobar que la base de datos se ha creado correctamente con la codificación en español, puedes ejecutar:

```sql
SHOW CREATE DATABASE nombre_base_de_datos;

```

---

### Consejos adicionales:

* **`utf8mb4`**: Es preferible sobre `utf8` tradicional en MySQL, ya que soporta todos los caracteres Unicode de 4 bytes (incluyendo emojis y símbolos especiales sin pérdida de datos).
* **Collation (`_ci`)**: Significa *Case-Insensitive* (insensible a mayúsculas y minúsculas), lo que evita problemas al realizar búsquedas donde "María" y "maria" deban coincidir.

Para insertar datos en una tabla de **MySQL**, se utiliza principalmente la sentencia `INSERT`. Las opciones más comunes son:

* **Inserción básica especificando columnas**: Se enlistan explícitamente las columnas y los valores correspondientes.


```sql
INSERT INTO person (first_name, last_name) VALUES ('John', 'Doe');

```


* **Insertar múltiples filas**: Se pueden agregar varios registros en una sola consulta separándolos por comas para optimizar el proceso.


```sql
INSERT INTO tbl_name VALUES (1, "row 1"), (2, "row 2");

```


* **Usando la cláusula `SET**`: Permite asignar los valores directamente a las columnas mediante asignaciones.


```sql
INSERT INTO person SET first_name = 'John', last_name = 'Doe';

```


* **Insertar mediante una consulta (`INSERT ... SELECT`)**: Permite poblar una tabla utilizando los datos resultantes de una consulta a otra tabla.


```sql
INSERT INTO contractor SELECT * FROM person WHERE status = 'c';

```

Para **actualizar datos** (modificar registros existentes) en una tabla de MySQL, se utiliza la sentencia `UPDATE`.

---

### Sintaxis Básica (Una sola tabla)

Para actualizar los datos de una tabla de forma segura, es fundamental utilizar la cláusula `WHERE` para indicar exactamente qué filas deseas modificar. Si omites el `WHERE`, se actualizarán **todos** los registros de la tabla.

```sql
UPDATE nombre_tabla 
SET columna1 = nuevo_valor1, columna2 = nuevo_valor2 
WHERE condicion;

```

---

### Ejemplos Prácticos

1. **Actualizar una sola columna con una condición específica:**
Modificar el precio de un producto cuyo identificador sea 100:


```sql
UPDATE products 
SET price = 10 
WHERE id = 100;

```


2. **Actualizar múltiples columnas:**
Puedes modificar varios campos en una sola instrucción separándolos por comas:


```sql
UPDATE empleados 
SET salario = 3000, puesto = 'Senior' 
WHERE id_empleado = 5;

```


3. **Actualizar utilizando el valor actual de la columna:**
También puedes realizar operaciones matemáticas basadas en los datos que ya existen en la fila:


```sql
UPDATE products 
SET price = price * 1.10 
WHERE price <= 99.99;

```



---

### Consideraciones Importantes

* **Privilegios:** Necesitas tener privilegios de `UPDATE` sobre las columnas que vas a modificar, así como privilegios de `SELECT` para las columnas que utilices dentro de la condición `WHERE`.


* **Uso de `LIMIT` y `ORDER BY`:** En consultas de una sola tabla en MySQL, puedes limitar la cantidad de filas afectadas o establecer un orden específico mediante `ORDER BY` y `LIMIT` si lo requieres.

Para **eliminar datos** (filas o registros) en una tabla de MySQL, se utiliza principalmente la sentencia `DELETE`.

---

### 1. Sintaxis Básica de `DELETE`

Para borrar registros de forma segura, es fundamental utilizar la cláusula `WHERE` para especificar qué filas deseas eliminar.

> **Advertencia:** Si ejecutas un `DELETE` sin la cláusula `WHERE`, **se eliminarán todos los registros** de la tabla.
> 
> 

```sql
DELETE FROM nombre_tabla 
WHERE condicion;

```

---

### 2. Ejemplos Prácticos

* **Eliminar filas específicas:**
Borrar todos los productos cuyo precio sea igual a 10:


```sql
DELETE FROM products 
WHERE price = 10;

```


* **Eliminar usando operadores lógicos:**
Borrar los empleados cuyo salario sea menor a 1500:
```sql
DELETE FROM empleados 
WHERE salario < 1500;

```


* **Eliminar todos los registros de una tabla:**
Si deseas vaciar la tabla por completo manteniendo su estructura:


```sql
DELETE FROM products;

```



---

### 3. Alternativa Rápida: `TRUNCATE TABLE`

Si necesitas **borrar absolutamente todos los registros** de una tabla de manera masiva y rápida (sin importar el número de filas ni registrar la eliminación fila por fila), puedes usar `TRUNCATE`:

```sql
TRUNCATE TABLE nombre_tabla;

```

* **Diferencia clave:** `TRUNCATE` es una operación de tipo estructural (DDL) que reinicia los contadores de `AUTO_INCREMENT` y es mucho más veloz que un `DELETE` masivo, pero **no** permite el uso de cláusulas `WHERE`.



---

### Consideraciones de Seguridad

* **Privilegios necesarios:** Necesitas contar con el privilegio `DELETE` sobre la tabla. Si tu cláusula `WHERE` evalúa columnas para filtrar, también requerirás el privilegio `SELECT` sobre esas columnas.


* **Llaves foráneas:** Si la tabla está relacionada con otras mediante restricciones de clave foránea (`FOREIGN KEY`), MySQL podría rechazar la eliminación si existen registros dependientes en otra tabla para evitar romper la integridad de los datos.

Para **leer o consultar datos** en MySQL (o sistemas compatibles como MariaDB), se utiliza la sentencia `SELECT`. Esta instrucción es la herramienta principal para recuperar información de las tablas de la base de datos.

---

### 1. Sintaxis Básica de `SELECT`

La estructura fundamental para consultar información es la siguiente:

```sql
SELECT columnas 
FROM nombre_tabla 
WHERE condicion;

```

* **`SELECT`**: Define qué columnas específicas deseas mostrar (o un asterisco `*` para seleccionar todas las columnas).


* **`FROM`**: Especifica la tabla o tablas de donde se obtendrán los datos.


* **`WHERE`**: Permite filtrar las filas basándose en criterios o condiciones específicas (es opcional; si se omite, se devuelven todos los registros).



---

### 2. Ejemplos Prácticos

* **Consultar todas las columnas de una tabla:**
```sql
SELECT * FROM empleados;

```


* **Consultar columnas específicas:**
```sql
SELECT first_name, last_name FROM person;

```


* **Filtrar datos usando la cláusula `WHERE`:**
```sql
SELECT f1, f2 FROM t1 WHERE (f3 < 10) AND (f4 = 'y');

```


* **Unir datos de múltiples tablas (`JOIN`):**
Puedes combinar información de varias tablas relacionadas utilizando un `INNER JOIN` u otros tipos de uniones:


```sql
SELECT Employees.ID, Employees.First_Name, Hours.Clock_In 
FROM Employees 
INNER JOIN Hours ON Employees.ID = Hours.ID;

```



Para **modificar la estructura de una tabla** existente en MySQL (añadir columnas, borrarlas, cambiar tipos de datos, renombrarlas, etc.), se utiliza la sentencia `ALTER TABLE`.

A continuación, te muestro las operaciones más comunes:

---

### 1. Añadir una nueva columna (`ADD`)

Permite agregar una columna al final de la tabla (o en una posición específica con `AFTER` o `FIRST`).

```sql
ALTER TABLE nombre_tabla 
ADD COLUMN nueva_columna VARCHAR(100);

```

---

### 2. Eliminar una columna (`DROP`)

Permite borrar una columna y todos los datos que contenga.

```sql
ALTER TABLE nombre_tabla 
DROP COLUMN columna_a_borrar;

```

---

### 3. Modificar el tipo de dato o atributos de una columna (`MODIFY`)

Sirve para cambiar el tipo de dato, restricciones (como `NOT NULL`, `DEFAULT`, etc.) de una columna existente.

```sql
ALTER TABLE nombre_tabla 
MODIFY COLUMN columna_existente INT NOT NULL;

```

---

### 4. Cambiar el nombre y tipo de una columna (`CHANGE`)

Permite renombrar una columna y, al mismo tiempo, redefinir su tipo de dato y atributos.

```sql
ALTER TABLE nombre_tabla 
CHANGE COLUMN viejo_nombre nuevo_nombre VARCHAR(50);

```

---

### 5. Renombrar una columna de forma sencilla (`RENAME COLUMN`)

En versiones modernas, puedes cambiar el nombre de una columna directamente sin necesidad de reescribir su tipo:

```sql
ALTER TABLE nombre_tabla 
RENAME COLUMN viejo_nombre TO nuevo_nombre;

```

---

### 6. Renombrar la tabla completa (`RENAME TO`)

Si necesitas cambiar el nombre de la tabla entera:

```sql
ALTER TABLE viejo_nombre_tabla 
RENAME TO nuevo_nombre_tabla;

```

---

### 7. Combinar múltiples modificaciones

Puedes realizar varias acciones en una sola instrucción `ALTER TABLE` separándolas con comas:

```sql
ALTER TABLE nombre_tabla 
ADD COLUMN telefono VARCHAR(20), 
DROP COLUMN fax, 
MODIFY COLUMN status INT DEFAULT 1;

```

Para **borrar (eliminar) una tabla completa** en MySQL (lo que elimina tanto su estructura como todos los datos que contiene y sus activadores asociados), se utiliza la sentencia `DROP TABLE`.

---

### 1. Sintaxis Básica

```sql
DROP TABLE nombre_tabla;

```

Si necesitas eliminar **varias tablas a la vez**, puedes especificarlas separadas por comas:

```sql
DROP TABLE tabla1, tabla2, tabla3;

```

---

### 2. Evitar errores si la tabla no existe (`IF EXISTS`)

Si intentas eliminar una tabla que no existe, MySQL devolverá un error. Para evitarlo y hacer que el sistema simplemente emita una nota informativa en su lugar, puedes agregar la cláusula `IF EXISTS`:

```sql
DROP TABLE IF EXISTS nombre_tabla;

```

Esta opción es muy útil en scripts de bases de datos para asegurar que el código se ejecute sin detenerse aunque la tabla ya haya sido borrada previamente.

---

### 3. Eliminar tablas temporales

Si la tabla que deseas borrar fue creada como temporal durante tu sesión actual, puedes utilizar la variante `TEMPORARY`:

```sql
DROP TEMPORARY TABLE nombre_tabla_temporal;

```

---

### ⚠️ Consideraciones Importantes:

* **¡Acción irreversible!** Una vez ejecutado el comando `DROP TABLE`, los datos y la definición de la tabla se eliminan permanentemente, por lo que debes tener mucha precaución.


* **Privilegios:** Necesitas contar con el privilegio `DROP` sobre cada una de las tablas que deseas eliminar.


* **Llaves foráneas:** Si otra tabla tiene una restricción de clave foránea (`FOREIGN KEY`) que depende de la tabla que intentas borrar, MySQL rechazará la operación para mantener la integridad de los datos. Deberás eliminar o modificar la relación primero.


Para **mostrar la estructura de una tabla** en MySQL (ver sus columnas, tipos de datos, restricciones y llaves), cuentas con varias opciones muy prácticas:

---

### 1. El comando `DESCRIBE` (o `DESC`)

Es el método más rápido y utilizado. Muestra un resumen claro de las columnas, tipos de datos, si permiten valores nulos, si son llaves y sus valores por defecto o extras:

```sql
DESCRIBE nombre_tabla;

```

O su forma abreviada:

```sql
DESC nombre_tabla;

```

---

### 2. El comando `SHOW COLUMNS`

Tiene una función muy similar a `DESCRIBE` y permite consultar los campos de una tabla:

```sql
SHOW COLUMNS FROM nombre_tabla;

```

Si además deseas ver información detallada como la codificación (collation), los comentarios de las columnas y los privilegios que posees sobre ellas, puedes añadir la palabra clave `FULL`:

```sql
SHOW FULL COLUMNS FROM nombre_tabla;

```

---

### 3. Ver la sentencia exacta de creación (`SHOW CREATE TABLE`)

Si necesitas conocer la estructura completa junto con el motor de almacenamiento utilizado, las restricciones de claves foráneas y los cotejamientos exactos con los que fue creada la tabla, puedes utilizar:

```sql
SHOW CREATE TABLE nombre_tabla;

```

Para trabajar con una tabla en MySQL, el término "seleccionar" puede referirse a dos acciones distintas dependiendo de lo que necesites hacer:

---

### 1. Seleccionar la Base de Datos (para trabajar con sus tablas)

Antes de poder consultar o modificar una tabla específica, primero debes indicarle a MySQL qué base de datos vas a utilizar mediante el comando `USE`:

```sql
USE nombre_base_de_datos;

```

---

### 2. Seleccionar (Consultar) los datos de una tabla

Para recuperar y visualizar los registros almacenados dentro de una tabla, se utiliza la sentencia `SELECT`.

* **Seleccionar todas las columnas y filas de una tabla:**
```sql
SELECT * FROM nombre_tabla;

```


*(El asterisco `*` funciona como una abreviatura para "todas las columnas")*

* **Seleccionar columnas específicas:**
```sql
SELECT columna1, columna2 FROM nombre_tabla;

```


* **Seleccionar filas filtrando por una condición (`WHERE`):**
```sql
SELECT * FROM nombre_tabla WHERE condicion = valor;

```

Para seleccionar o utilizar una base de datos en MySQL (o sistemas compatibles), se utiliza la sentencia `USE`. Este comando le indica al servidor cuál será la base de datos predeterminada para las siguientes consultas o instrucciones que ejecutes en tu sesión.

### Sintaxis Básica

```sql
USE nombre_de_la_base;
```

### Ejemplo Práctico:
Si tienes una base de datos llamada `blog`, puedes seleccionarla de la siguiente manera:
```sql
USE blog;
```

Una vez seleccionada, ya no necesitas anteponer el nombre de la base de datos cada vez que consultes una tabla interna (por ejemplo, puedes hacer `SELECT * FROM mi_tabla;` en lugar de `SELECT * FROM blog.mi_tabla;`) .

### Funciones útiles relacionadas:

*Saber qué base de datos estás usando actualmente:** Puedes consultarlo ejecutando:

```sql
  SELECT DATABASE();

```

**Restricción de privilegios:** Ten en cuenta que no puedes usar `USE` en una base de datos si no cuentas con los permisos necesarios para acceder a ella; por seguridad, el sistema no te permitirá siquiera saber si existe.

En MySQL (y sistemas relacionales basados en SQL), las **llaves primarias** y las **llaves foráneas** son los pilares fundamentales para garantizar la integridad de los datos y estructurar las relaciones entre las diferentes tablas.

---

### 1. Llave Primaria (`PRIMARY KEY`)

Una llave primaria es una columna (o un grupo de columnas) que identifica de forma única cada fila dentro de una tabla.

* **Características principales:**
* **Valores únicos:** No puede haber dos filas con el mismo valor en la columna de la llave primaria.
* **No nula (`NOT NULL`):** Por definición, una llave primaria **nunca** puede contener valores nulos (`NULL`).


* **Límite:** Cada tabla puede tener **máximo una** llave primaria.


* **Índice automático:** MySQL crea automáticamente un índice único optimizado (tipo B-tree) para la llave primaria, lo que acelera notablemente las búsquedas.





#### Ejemplo de Creación (Llave Primaria simple):

```sql
CREATE TABLE clientes (
    cliente_id INT NOT NULL AUTO_INCREMENT,
    nombre VARCHAR(50) NOT NULL,
    PRIMARY KEY (cliente_id)
);

```

---

### 2. Llave Foránea (`FOREIGN KEY`)

Una llave foránea es una columna (o conjunto de columnas) en una tabla (conocida como tabla hija) cuyos valores deben coincidir con la llave primaria (o una columna única) de otra tabla (conocida como tabla padre). Su propósito es mantener la **integridad referencial** entre ambas tablas.

* **Características principales:**
* **Relación:** Conecta lógicamente dos tablas (por ejemplo, relacionar las compras de un cliente con la tabla de clientes).
* **Reglas de acción en Cascada (`ON DELETE` / `ON UPDATE`):** Permiten definir qué debe suceder en la tabla hija cuando un registro de la tabla padre es eliminado o modificado. Las opciones más comunes son:
* `RESTRICT` o `NO ACTION` (Por defecto): Rechaza la operación si hay datos relacionados para evitar romper la integridad.


* `CASCADE`: Si se borra o actualiza el registro padre, automáticamente se borran o actualizan los registros dependientes en la tabla hija.


* `SET NULL`: Si se borra o actualiza el registro padre, los campos de la llave foránea en la tabla hija se establecen en `NULL` (requiere que la columna permita nulos).


#### Ejemplo de Creación (Llave Foránea):

```sql
CREATE TABLE pedidos (
    pedido_id INT NOT NULL AUTO_INCREMENT,
    fecha DATE,
    cliente_id INT NOT NULL,
    PRIMARY KEY (pedido_id),
    CONSTRAINT fk_cliente_pedido 
    FOREIGN KEY (cliente_id) REFERENCES clientes(cliente_id)
    ON DELETE CASCADE 
    ON UPDATE CASCADE
);

```

---

### Resumen de diferencias clave

| Característica | Llave Primaria (`PRIMARY KEY`) | Llave Foránea (`FOREIGN KEY`) |
| --- | --- | --- |
| **Propósito** | Identificar inequívocamente cada fila de la propia tabla. | Relacionar los datos de una tabla con los de otra tabla principal. |
| **Cantidad por tabla** | Máximo **una** por tabla. | Puede haber **múltiples** (tantas como relaciones necesites). |
| **Permite valores Nulos** | No (es `NOT NULL` por defecto). | Sí, a menos que se restrinja explícitamente con `NOT NULL`. |
| **Origen de los datos** | Generados internamente o asignados de manera única en la tabla. | Vienen de otra tabla (hacen referencia a una llave primaria ajena).|

En MySQL y sistemas compatibles, los tipos de datos utilizados para las **llaves primarias (`PRIMARY KEY`)** y las **llaves foráneas (`FOREIGN KEY`)** corresponden principalmente a tipos numéricos y de cadenas de caracteres. Para que una relación de llave foránea funcione correctamente, el tipo de dato de la columna en la tabla hija debe coincidir con el tipo de dato de la columna referenciada en la tabla padre.

A continuación se detallan los tipos de datos disponibles y las reglas clave según la documentación:

### 1. Tipos de Datos Numéricos

Son los más recomendados y utilizados para claves (especialmente cuando se combinan con atributos como `AUTO_INCREMENT`):

* **`INT` / `INTEGER**`: Enteros estándar de 4 bytes, ideales para la mayoría de las llaves primarias y foráneas.


* **`BIGINT`**: Enteros grandes de 8 bytes, utilizados cuando se espera un volumen masivo de registros.


* **`SMALLINT`**: Enteros pequeños de 2 bytes.


* **`MEDIUMINT`**: Enteros de tamaño medio de 3 bytes.


* **`TINYINT`**: Enteros muy pequeños de 1 byte.


* **`DECIMAL`**: Números decimales exactos, empleados en casos donde la clave requiera precisión numérica específica.



### 2. Tipos de Datos de Cadenas (String)

Se utilizan cuando las claves están basadas en códigos de texto, identificadores alfanuméricos o strings únicos:

* **`VARCHAR`**: Cadenas de caracteres de longitud variable.


* **`CHAR`**: Cadenas de caracteres de longitud fija.



---

### Características Principales de las Llaves

* **Llave Primaria (`PRIMARY KEY`)**:
* Define una columna o conjunto de columnas como un identificador único para las filas.


* Solo puede existir **como máximo una** llave primaria por tabla.


* Se marca de forma implícita como `NOT NULL`.




* **Llave Foránea (`FOREIGN KEY`)**:
* Requiere una definición de referencia que apunte a una tabla de destino (padre) y a una columna indexada cuyos valores deben coincidir con los de la llave foránea.


* Permite configurar reglas de integridad referencial para los eventos de eliminación o actualización mediante cláusulas como `CASCADE`, `RESTRICT`, `SET NULL` y `NO ACTION`.


En MySQL y sistemas compatibles como MariaDB, los tipos de datos para gestionar fechas y horas, así como sus configuraciones asociadas, permiten un control preciso de la información temporal y del comportamiento del servidor mediante modos y variables.

---

### 1. Tipos de Datos para Fechas y Horas

Los tipos principales disponibles son:

* **`DATE`**: Almacena exclusivamente fechas (sin componente de hora) en el formato estándar `YYYY-MM-DD`. El rango típico abarca desde el año 1000 hasta el 9999 (o se permiten valores especiales como `'0000-00-00'` dependiendo de la configuración).


* **`TIME`**: Almacena la hora del día o un intervalo de tiempo en formato `HH:MM:SS` (con soporte opcional para fracciones de segundo o microsegundos).


* **`DATETIME`**: Combina fecha y hora en el formato `YYYY-MM-DD HH:MM:SS`. Es independiente de la zona horaria del servidor.


* **`TIMESTAMP`**: Una marca de tiempo que representa una fecha y hora combinadas. A diferencia de `DATETIME`, suele convertirse automáticamente a la zona horaria actual al almacenarse y recuperarse, y se actualiza frecuentemente de forma automática.


* **`YEAR`**: Almacena un año en formato de 4 dígitos (por defecto) o de 2 dígitos.

---

### 2. Configuraciones y el impacto del `SQL_MODE` en las Fechas

El comportamiento de los tipos de datos temporales está estrechamente ligado a la configuración del **`SQL_MODE`** del servidor, la cual determina cuán estrictas son las validaciones:

* **`NO_ZERO_DATE`**: Si está activada, el servidor no permite insertar fechas o marcas de tiempo formadas completamente por ceros (como `'0000-00-00'` o `'0000-00-00 00:00:00'`), devolviendo un error o un valor nulo.


* **`ALLOW_INVALID_DATES`**: Si se configura, permite el almacenamiento de fechas parciales o lógicamente inválidas (por ejemplo, el 30 de febrero).


* **`NO_ZERO_IN_DATE`**: Evita que subpartes específicas de una fecha u hora (como el mes o el día) se establezcan en `0`.


Para almacenar **cantidades de dinero** en MySQL de forma precisa y evitar los errores de redondeo típicos de los números de punto flotante (`FLOAT` o `DOUBLE`), se recomienda utilizar tipos de datos numéricos exactos.

---

### 1. El Tipo de Dato Ideal: `DECIMAL` (o `NUMERIC`)

El tipo **`DECIMAL`** (también conocido como `NUMERIC` o `FIXED`) es el estándar de la industria para datos monetarios y financieros, ya que almacena los valores de forma exacta sin perder decimales.

* **Sintaxis:**
```sql
DECIMAL(precisión, escala)

```


* **Precisión (`M`):** Es el número total máximo de dígitos que se pueden almacenar (tanto a la izquierda como a la derecha del punto decimal). Su valor máximo es 65.
* **Escala (`D`):** Es el número de dígitos permitidos después del punto decimal. Su valor máximo es 30.



#### Ejemplo práctico para dinero:

Si vas a registrar precios o montos de hasta 99,999,999.99 (8 dígitos enteros y 2 decimales), la definición correcta sería:

```sql
precio DECIMAL(10, 2) NOT NULL

```

* **Ventaja:** Garantiza que operaciones como sumas, restas y multiplicaciones de dinero mantengan una precisión matemática exacta, lo cual es fundamental en entornos contables.

---

### 2. ¿Por qué evitar `FLOAT` o `DOUBLE` en dinero?

Los tipos de punto flotante (`FLOAT` y `DOUBLE`) son aproximados y utilizan representaciones binarias del estándar IEEE 754. Debido a esto, al realizar operaciones aritméticas repetidas con monedas, pueden acumular pequeños errores de redondeo (por ejemplo, terminar con un valor como `10.000000001` en lugar de `10.00`).

---

### 3. Configuraciones Relacionadas en el Servidor

Al trabajar con cálculos financieros y tipos decimales, es útil tener en cuenta ciertas configuraciones del servidor (`SQL_MODE` y variables de sesión):

* **Modo Estricto (`STRICT_TRANS_TABLES`):** Es altamente recomendable mantenerlo activo. Si intentas insertar un valor que excede la escala o precisión permitida en una columna `DECIMAL`, el modo estricto rechazará la inserción y emitirá un error en lugar de truncar el dato silenciosamente.
* **Control de Divisiones (`ERROR_FOR_DIVISION_BY_ZERO`):** Al calcular porcentajes, impuestos o divisiones de montos monetarios, esta configuración ayuda a manejar correctamente las operaciones inválidas o divisiones entre cero, evitando resultados inesperados.
* **Incremento de Precisión en Divisiones (`div_precision_increment`):** Esta variable global o de sesión determina cuántos dígitos decimales se muestran por defecto al realizar una operación de división (`/`) entre números que no son enteros exactos.


El uso de la cláusula **`WHERE`** en una consulta `SELECT` sirve para especificar las condiciones que deben cumplir las filas de una tabla para ser seleccionadas y devueltas en el resultado.

A continuación se detallan su funcionamiento, reglas y operadores principales:

---

### 1. ¿Cómo funciona?

* **Filtro de filas:** La cláusula `WHERE` contiene una expresión booleana (una condición que se evalúa como verdadera, falsa o desconocida).


* **Evaluación:** Cada fila de la tabla resultante del `FROM` se evalúa individualmente. Si la condición se cumple (`TRUE`), la fila se mantiene; si el resultado es falso (`FALSE`) o nulo (`NULL`), la fila se descarta.


* **Sin `WHERE`:** Si se omite la cláusula `WHERE`, la sentencia seleccionará **todas** las filas de la tabla.


* **Orden de ejecución:** El `WHERE` se aplica antes de las cláusulas de agrupación como `GROUP BY`. Por lo tanto, filtra las filas individuales antes de que sean agrupadas (para filtrar después de agrupar se utiliza `HAVING`).



---

### 2. Operadores comunes en el `WHERE`

Dentro del `WHERE` puedes utilizar funciones y una gran variedad de operadores de comparación y lógicos:

* **Operadores de Comparación:**
* `=` (Igual a)


* `!=` o `<>` (Diferente de)


* `<` (Menor que), `<=` (Menor o igual que)


* `>` (Mayor que), `>=` (Mayor o igual que)




* **Operadores de Conjuntos y Rangos:**
* **`BETWEEN min AND max`**: Selecciona valores dentro de un rango específico.


* **`IN (valor1, valor2, ...)`**: Comprueba si el valor de la columna coincide con alguno de los elementos provistos en una lista.




* **Operadores para Valores Nulos:**
* **`IS NULL`**: Comprueba si una columna contiene un valor nulo.


* **`IS NOT NULL`**: Comprueba que la columna no esté vacía o nula.




* **Operadores Lógicos (para combinar condiciones):**
* **`AND`**: Requiere que se cumplan todas las condiciones conectadas.


* **`OR`**: Requiere que se cumpla al menos una de las condiciones.


* **`NOT`**: Niega o invierte una condición booleana.





---

### 3. Ejemplos Prácticos

* Filtrar por una condición numérica simple:


```sql
SELECT * 
FROM empleados 
WHERE salario > 3000;

```


* **Combinar múltiples condiciones con `AND` y `OR`:**
```sql
SELECT nombre, departamento, salario 
FROM empleados 
WHERE (departamento = 'Ventas') AND (salario >= 2500);

```


* Utilizar el operador `IN` para buscar coincidencias en una lista:


```sql
SELECT producto, stock 
FROM inventario 
WHERE categoria_id IN (1, 3, 5);

```


* Filtrar utilizando rangos con `BETWEEN`:


```sql
SELECT * 
FROM pedidos 
WHERE fecha_pedido BETWEEN '2026-01-01' AND '2026-03-31';

```

En la cláusula `WHERE` de MySQL se pueden realizar diversas operaciones utilizando operadores lógicos, de comparación, de rangos y de evaluación de nulos para filtrar con precisión los registros de una consulta.

A continuación se detallan las operaciones y operadores principales disponibles:

---

### 1. Operadores de Comparación

Permiten relacionar una columna o expresión con un valor específico:

* **Igualdad y Desigualdad:** `=` (igual) y `!=` o `<>` (diferente).


* **Menor o Mayor que:** `<` (menor que), `<=` (menor o igual), `>` (mayor que) y `>=` (mayor o igual).


* **Operador seguro para nulos (`<=>`):** Realiza una comparación de igualdad similar a `=`, pero devuelve `1` en lugar de `NULL` si ambos operandos son nulos, y `0` si uno de ellos lo es.



### 2. Operaciones de Rangos y Conjuntos

* **`BETWEEN min AND max`:** Evalúa si una expresión se encuentra dentro de un rango determinado de dos valores (de forma inclusiva).


* **`IN (valor1, valor2, ...)`:** Comprueba si el valor evaluado coincide con alguno de los elementos especificados dentro de un conjunto o lista.



### 3. Evaluación de Valores Nulos y Booleanos

* **`IS` / `IS NOT`:** Permiten comprobar si una expresión o valor equivale a `TRUE`, `FALSE` o `UNKNOWN`.


* **`IS NULL`:** Verifica de forma estricta si una columna o expresión contiene un valor nulo (`NULL`).


* **`IS NOT NULL`:** Comprueba que el valor evaluado **no** sea nulo.



### 4. Operadores Lógicos (Combinación de condiciones)

Permiten unir múltiples expresiones dentro del `WHERE`:

* **`AND` (Conjunción):** Requiere que todas las condiciones unidas sean verdaderas para que la fila sea seleccionada.


* **`OR` (Disyunción):** Requiere que se cumpla al menos una de las condiciones evaluadas.


* **`NOT` (Negación):** Invierte el valor de verdad de una condición.



---

### Ejemplo de comparación avanzada (Comparación de filas)

MySQL también permite realizar comparaciones entre conjuntos de columnas (tuplas) directamente en el `WHERE`:

```sql
SELECT * 
FROM t1 
WHERE (t1.a, t1.b) <= (10, 20);

```

Esta operación evalúa de manera conjunta que las filas cumplan con la relación de orden establecida entre ambos grupos de valores.


Para organizar y ordenar los resultados devueltos por una consulta `SELECT` en MySQL (o sistemas compatibles como MariaDB), se utiliza la cláusula **`ORDER BY`**.

---

### 1. Sintaxis y Opciones Principales

La estructura general permite ordenar utilizando columnas, expresiones o posiciones numéricas:

```sql
SELECT columna1, columna2 
FROM nombre_tabla 
ORDER BY expresion1 [ASC | DESC], expresion2 [ASC | DESC];

```

* **Dirección del ordenamiento:**
* **`ASC`**: Ordena de forma ascendente (de menor a mayor). Es el valor predeterminado si se omite.
* **`DESC`**: Ordena de forma descendente (de mayor a menor).


* **Múltiples expresiones:** Se pueden separar varias columnas por comas; si hay valores iguales en la primera columna, se ordenarán según la siguiente columna especificada.
* **Posición de columna:** Es posible ordenar utilizando el número entero `n` que representa la posición de la columna dentro de la lista del `SELECT` (por ejemplo, `ORDER BY 1`).

---

### 2. Ejemplos Prácticos

* **Orden alfabético ascendente:**
```sql
SELECT * FROM members ORDER BY name;
```


* **Orden descendente:**
  
```sql
SELECT * FROM seq ORDER BY i DESC;
``` 


* **Ordenamiento por múltiples columnas:**
```sql
SELECT * FROM seq ORDER BY x, i;
``` 


* **Combinación con `LIMIT` para restringir resultados:**
```sql
SELECT * FROM members ORDER BY name LIMIT 2;
``` 



Para utilizar **comodines** en una consulta `SELECT` en MySQL, se combina la cláusula `WHERE` con el operador **`LIKE`**. Los comodines permiten buscar patrones específicos dentro de cadenas de texto en lugar de coincidencias exactas.

---

### Principales Caracteres Comodín

Los dos comodines estándar admitidos son:

* **`%` (Porcentaje):** Coincide con cualquier cantidad de caracteres, incluidos cero caracteres.


* **`_` (Guión bajo):** Coincide exactamente con un **único** carácter.



---

### Ejemplos Prácticos

1. **Buscar registros que comienzan con una letra específica (`%`):**
Para seleccionar los días que empiezan con la letra "T":
```sql
SELECT * FROM t1 WHERE d LIKE "T%";
``` 



2. **Buscar una subcadena en cualquier posición (`%`):**
Para encontrar registros que contengan las letras "es" en cualquier parte del texto:
```sql
SELECT * FROM t1 WHERE d LIKE "%es%";
``` 



3. **Buscar un número exacto de caracteres desconocido (`_`):**
Para buscar valores que terminen en "day" y tengan exactamente tres caracteres antes de esa palabra:
```sql
SELECT * FROM t1 WHERE d LIKE "___day";
``` 


---

### Consideraciones Adicionales

* **Sensibilidad a mayúsculas y minúsculas:** Por defecto, con las colecciones estándar (`collations`), el operador `LIKE` realiza búsquedas que no distinguen entre mayúsculas y minúsculas. Si necesitas una coincidencia estricta, puedes utilizar la cláusula `COLLATE` para forzar una comparación binaria.


* **Valores Nulos:** Si la expresión o el patrón evaluado es `NULL`, el resultado de la consulta devolverá `NULL`.


El **`LEFT JOIN`** (también conocido como `LEFT OUTER JOIN`) es un tipo de unión en MySQL que garantiza que **todas las filas de la tabla ubicada a la izquierda** del operador aparezcan en el conjunto de resultados al menos una vez.

---

### ¿Cómo funciona?

* Si una fila de la tabla izquierda encuentra una coincidencia en la tabla de la derecha basándose en la condición de unión, ambas filas se combinan con normalidad.


* Si **no existe coincidencia** en la tabla de la derecha, la fila de la izquierda se muestra de todas formas, rellenando las columnas faltantes de la tabla derecha con valores nulos (`NULL`).



---

### Sintaxis Básica

Existen dos formas habituales de estructurar un `LEFT JOIN`:

1. **Usando la cláusula `ON**` (permite definir la condición de comparación explícitamente):


```sql
SELECT * 
FROM tabla1 
LEFT JOIN tabla2 ON tabla1.id = tabla2.id;

```


2. **Usando la cláusula `USING**` (es un atajo válido cuando la columna de relación se llama exactamente igual en ambas tablas):


```sql
SELECT * 
FROM tabla1 
LEFT JOIN tabla2 USING (id);
```[cite: 1]


```



---

### Ejemplo Práctico

Si se tienen dos tablas (`t1` y `t2`) y se ejecuta la siguiente consulta:

```sql
SELECT * 
FROM t1 
LEFT JOIN t2 ON t1.num = t2.num;
```

El resultado arrojará todas las filas de `t1`. Aquellos registros de `t1` que no tengan un equivalente en `t2` aparecerán en el resultado con campos vacíos o `NULL` en las columnas correspondientes a `t2`[cite: 1].

---

### Caso de Uso Frecuente: Detectar registros sin coincidencias

Una aplicación muy práctica del `LEFT JOIN` es encontrar elementos que están presentes en la tabla principal (izquierda) pero que **no tienen registros asociados** en la tabla secundaria (derecha). Esto se logra combinando el `LEFT JOIN` con una condición `WHERE ... IS NULL`[cite: 1, 2]:

```sql
SELECT table1.* 
FROM table1 
LEFT JOIN table2 ON table1.id = table2.id 
WHERE table2.id IS NULL;
```



Cuando utilizas un `LEFT JOIN` en MySQL, puedes asignar nombres temporales o **alias** a las tablas involucradas en la consulta. Esto es especialmente útil para simplificar nombres de tablas muy largos o cuando necesitas cruzar una tabla consigo misma.

---

### Reglas Clave al Usar Alias

* **Nuevo identificador:** El alias se convierte en el nombre oficial de la referencia de la tabla únicamente durante la ejecución de esa consulta.


* **Prohibición del nombre original:** Una vez que defines un alias para una tabla, **no está permitido** referirse a ella por su nombre original en ninguna otra parte de la sentencia (como en el `SELECT`, `WHERE` o `ON`).



---

### Sintaxis Básica

La estructura general utilizando alias (`AS` es opcional) se implementa de la siguiente manera:

```sql
SELECT a.columna1, b.columna2 
FROM tabla_larga_1 AS a 
LEFT JOIN tabla_larga_2 AS b ON a.id = b.id;

```

---

### Ejemplo Práctico: Auto-Unión (*Self-Join*)

El uso de alias con `LEFT JOIN` es obligatorio cuando realizas un *self-join* (conectar una tabla con ella misma) para distinguir claramente el lado izquierdo del derecho:

```sql
SELECT mother.name AS madre, child.name AS hijo 
FROM people AS mother 
LEFT JOIN people AS child ON mother.id = child.mother_id;
```

* **Funcionamiento:** Aquí la tabla `people` se referencia dos veces usando los alias `mother` y `child`[cite: 1]. Gracias al `LEFT JOIN`, se listarán todas las madres, mostrando un valor `NULL` en los datos del hijo si no tienen registros asociados

¿Estás intentando aplicar esto en un escenario de auto-unión o con tablas de nombres complejos?




El **`RIGHT JOIN`** (o `RIGHT OUTER JOIN`) es un tipo de unión en MySQL que actúa de forma inversa al `LEFT JOIN`. Garantiza que **todas las filas de la tabla ubicada a la derecha** del operador aparezcan en el resultado final.

---

### ¿Cómo funciona?

1. Primero se realiza una unión interna (*inner join*) de ambas tablas.


2. A continuación, para cada fila de la tabla de la **derecha** que **no** encuentre una coincidencia en la tabla de la **izquierda** según la condición especificada, se agrega una fila con valores nulos (`NULL`) en las columnas correspondientes a la tabla izquierda.


3. Como resultado, el conjunto de datos final contendrá siempre todas las filas de la tabla derecha.



---

### Sintaxis Básica

```sql
SELECT * 
FROM tabla1 
RIGHT JOIN tabla2 ON tabla1.id = tabla2.id;

```

---

### Ejemplo Práctico

Considerando dos tablas de ejemplo:

* **`t1`** (`num | name`): `(1, a)`, `(2, b)`, `(3, c)`

* **`t2`** (`num | value`): `(1, xxx)`, `(3, yyy)`, `(5, zzz)`


Si ejecutas la consulta utilizando un `RIGHT JOIN`:

```sql
SELECT * 
FROM t1 
RIGHT JOIN t2 ON t1.num = t2.num;

```

El resultado obtenido será:

```text
num | name | num | value 
----+------+-----+------- 
  1 | a    |   1 | xxx 
  3 | c    |   3 | yyy 
    |      |   5 | zzz 
(3 rows)

```

**Explicación del resultado:**

* Las filas con `num = 1` y `num = 3` coinciden en ambas tablas, por lo que se combinan con normalidad.


* El registro con `num = 5` de la tabla derecha (`t2`) no tiene ninguna coincidencia en la tabla izquierda (`t1`). Por lo tanto, se incluye en el resultado rellenando con valores `NULL` las columnas de la izquierda.


El operador **`UNION`** en MySQL se utiliza para combinar los resultados de múltiples sentencias `SELECT` en un único conjunto de resultados.

---

### 1. Sintaxis Básica

La estructura general para utilizar `UNION` es la siguiente:

```sql
SELECT columna1, columna2 FROM tabla1
UNION [ALL | DISTINCT]
SELECT columna1, columna2 FROM tabla2;
```

### 2. Reglas Clave a Considerar

* **Nombres de columnas:** Los nombres de las columnas devueltas en el resultado final siempre están determinados por los nombres especificados en la **primera** sentencia `SELECT`[cite: 1].
* **Tipos de datos:** Las columnas listadas en las posiciones correspondientes de cada `SELECT` deben tener el mismo tipo de datos (o tipos compatibles)[cite: 1].
* **Manejo de duplicados:** 
  * **`UNION` (o `UNION DISTINCT`):** Es el comportamiento predeterminado si se omite la palabra clave. **Elimina las filas duplicadas** del resultado[cite: 1].
  * **`UNION ALL`:** Conserva todas las filas, **incluyendo los duplicados**, lo cual suele ser más eficiente ya que evita que el servidor cree una tabla temporal para filtrar duplicados[cite: 1].

---

### 3. Ejemplos Prácticos

#### Ejemplo A: Unir tablas con nombres de columnas diferentes usando alias
Si necesitas combinar datos de dos tablas distintas (por ejemplo, empleados y clientes) que tienen nombres de columnas diferentes, puedes usar alias en la primera consulta para alinearlas[cite: 1]:

```sql
(SELECT e_name AS name, email FROM employees)
UNION
(SELECT c_name AS name, email FROM customers);
```

#### Ejemplo B: Diferencia entre `UNION` y `UNION ALL`
Tomando una tabla numérica llamada `seqs`, puedes observar cómo reacciona el operador al duplicar registros:

* **Usando `UNION` (elimina duplicados):**
  ```sql
  SELECT i FROM seqs WHERE i <= 3 
  UNION 
  SELECT i FROM seqs WHERE i >= 3;
  ```
  *Este resultado eliminará los valores repetidos que coincidan en ambas condiciones.*

* **Usando `UNION ALL` (conserva duplicados):**
  ```sql
  SELECT i FROM seqs WHERE i <= 3 
  UNION ALL 
  SELECT i FROM seqs WHERE i >= 3;
  ```
  *Este resultado mantendrá intactas todas las filas de ambos conjuntos, incluyendo las repeticiones[cite: 1].*

---

### 4. Ordenamiento global (`ORDER BY`) y límites (`LIMIT`)
Si deseas ordenar o limitar el resultado final de la unión completa, puedes aplicar las cláusulas al final de toda la sentencia[cite: 1]:

```sql
(SELECT name, email FROM employees)
UNION
(SELECT name, email FROM customers)
ORDER BY name DESC
LIMIT 10;
```

