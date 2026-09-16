# Manual de Bash — Edición pedagógica

Este manual sigue la plantilla didáctica definida en `AGENT.md`. Cada lección incluye:

1. **Objetivo de aprendizaje**
2. **Explicación teórica breve**
3. **Ejemplo de código comentado**
4. **Explicación línea por línea**
5. **Errores comunes**
6. **Ejercicio propuesto** (con pista, sin solución)
7. **Mini resumen**

**Intérprete de referencia:** Bash 5.x. Cuando una construcción no sea POSIX se
indicará expresamente. Todos los ejemplos están pensados para ser ejecutables tal
cual; puedes validar la sintaxis de un script con `bash -n script.sh`.

---

## Lección 1. Qué es Bash y estructura de un comando

### Objetivo de aprendizaje

Entender qué es un shell, diferenciarlo de la terminal y reconocer las tres partes
de un comando: comando, opciones y argumentos.

### Explicación teórica

Imagina un restaurante: la **terminal** es el mostrador donde pides, el **shell** es
el mesero que traduce tu pedido a la cocina, y el **kernel** es la cocina que lo
prepara. Bash (*Bourne Again SHell*) es uno de esos meseros: un intérprete que lee
tus comandos y los ejecuta.

Un comando se compone de:

```text
comando   opciones        argumentos
  ls        -l        /home/usuario
```

- **Comando**: el programa, función o builtin que quieres ejecutar.
- **Opciones**: modifican su comportamiento; suelen empezar por `-` o `--`.
- **Argumentos**: los datos sobre los que actúa.

Bash guarda el resultado del último comando en la variable especial `$?`
(0 = éxito, distinto de 0 = error).

### Ejemplo de código comentado

```bash
#!/usr/bin/env bash
# hola.sh - primer script de ejemplo (Bash 5.x)

echo "Hola, $USER"          # imprime un saludo usando la variable de entorno USER
echo "Directorio: $PWD"     # muestra el directorio actual
ls -l /tmp | head -n 3      # lista /tmp en formato largo y muestra 3 líneas
echo "Código de salida: $?" # código del último comando (0 si todo fue bien)
```

### Explicación línea por línea

- `#!/usr/bin/env bash`: **shebang**; indica al sistema qué intérprete usar. La
  forma `env` busca `bash` en el `PATH` y es más portable.
- `echo "Hola, $USER"`: `echo` es un builtin que imprime; `$USER` se expande a tu
  nombre de usuario. Las comillas dobles permiten la expansión.
- `ls -l /tmp | head -n 3`: el pipe `|` envía la salida de `ls` a `head`, que
  muestra solo 3 líneas.
- `$?`: tras el pipe vale 0 si el último comando (`head`) terminó bien.

### Errores comunes

- **Escribir `#!/bin/bash` sin que exista** en sistemas donde está en otra ruta.
  Solución: usa `#!/usr/bin/env bash`.
- **Confundir `$?` con el resultado del primer comando del pipe**: `$?` refleja el
  último comando, salvo que actives `set -o pipefail`.
- **Olvidar los permisos de ejecución**: `./hola.sh` falla con "Permission denied"
  si no ejecutaste `chmod +x hola.sh`.

### Ejercicio propuesto

Crea `quien.sh` que imprima tu usuario, tu directorio actual y el número de
archivos en `/etc`.

> **Pista:** `ls /etc | wc -l` cuenta líneas. Usa `$USER` y `$PWD`.

### Mini resumen

- Bash es un shell: interpreta y ejecuta comandos.
- Un comando = comando + opciones + argumentos.
- El shebang define el intérprete; `$?` guarda el último código de salida.

---

## Lección 2. Navegación y manejo de archivos

### Objetivo de aprendizaje

Moverte por el sistema de archivos y crear, copiar, mover y eliminar archivos y
directorios con seguridad.

### Explicación teórica

El sistema de archivos es como un árbol invertido: la raíz `/` está arriba y todo
cuelga de ella. Tu **directorio actual** (o *working directory*) es el punto del
árbol donde estás parado. Bash ofrece rutas **absolutas** (empiezan por `/`) y
**relativas** (respecto al directorio actual).

Atajos útiles: `.` es el directorio actual, `..` el padre, `~` tu home y `-` el
directorio anterior.

### Ejemplo de código comentado

```bash
#!/usr/bin/env bash
# organizar.sh - crea una estructura de proyecto (Bash 5.x)
set -euo pipefail                  # aborta ante errores y variables sin definir

proyecto="demo"                    # nombre del proyecto
mkdir -p "$proyecto"/{src,test}    # crea el proyecto y sus subcarpetas
touch "$proyecto"/src/main.sh      # crea un archivo vacío
cp "$proyecto"/src/main.sh "$proyecto"/src/main.bak   # copia de respaldo
mv "$proyecto"/src/main.bak "$proyecto"/test/         # mueve el respaldo a test/
find "$proyecto" -type f           # lista todos los archivos creados
rm -r "$proyecto"                  # elimina el árbol completo
```

### Explicación línea por línea

- `set -euo pipefail`: `-e` aborta si un comando falla, `-u` trata variables no
  definidas como error, `pipefail` propaga errores dentro de un pipe.
- `mkdir -p "$proyecto"/{src,test}`: `-p` crea directorios intermedios; las llaves
  expanden a `demo/src` y `demo/test`.
- `touch`: crea el archivo si no existe.
- `cp` y `mv`: copian y mueven/renombran respectivamente.
- `find ... -type f`: busca solo archivos regulares.
- `rm -r`: elimina de forma recursiva (necesario para directorios).

### Errores comunes

- **`rm -rf "$dir"/` con `$dir` vacío**: borraría desde la raíz. Con `set -u` y
  comillas evitas sustos; verifica siempre con `echo` antes.
- **`cp` sin `-r` sobre un directorio**: falla con "omitting directory". Usa `cp -r`.
- **Rutas con espacios sin comillas**: `cd Mis Documentos` falla; escribe
  `cd "Mis Documentos"`.

### Ejercicio propuesto

Crea un script que reciba un nombre de carpeta y, si no existe, la cree con
subcarpetas `img` y `docs`, y muestre el árbol resultante.

> **Pista:** comprueba con `[[ -d "$1" ]]` y usa `find "$1"` o `ls -R`.

### Mini resumen

- Rutas absolutas vs. relativas; atajos `.`, `..`, `~`, `-`.
- `mkdir -p`, `cp -r`, `mv`, `rm -r`, `find`, `ln -s`.
- Cita siempre las variables y activa `set -euo pipefail`.

---

## Lección 3. Entrada/Salida y redirecciones

### Objetivo de aprendizaje

Redirigir la entrada y la salida de los comandos, separar errores y encadenar
procesos con pipes.

### Explicación teórica

Todo programa recibe y entrega datos por tres canales llamados **descriptores**:

| Descriptor | Nombre | Uso |
|-----------:|--------|-----|
| 0 | stdin  | entrada |
| 1 | stdout | salida normal |
| 2 | stderr | errores |

Por defecto los tres van a la terminal. La **redirección** cambia ese destino y el
**pipe** conecta la salida de un comando con la entrada de otro.

### Ejemplo de código comentado

```bash
#!/usr/bin/env bash
# redireccion.sh - demuestra redirecciones y pipes (Bash 5.x)
set -euo pipefail

log="salida.log"
{ echo "inicio"; ls /etc; echo "fin"; } > "$log" 2>&1   # stdout+stderr al archivo
errores=$(ls /ruta/inexistente 2>&1 || true)            # captura el error
printf 'Errores capturados: %s\n' "$errores" >&2         # lo manda a stderr
cat "$log" | tee "$log.copia" | wc -l                    # bifurca y cuenta líneas
rm -f "$log" "$log.copia"                                # limpia
```

### Explicación línea por línea

- `{ ...; } > "$log" 2>&1`: agrupa comandos; `>` redirige stdout y `2>&1` hace que
  stderr vaya al mismo destino.
- `2>&1 || true`: captura el mensaje de error y evita que `set -e` aborte.
- `printf ... >&2`: envía el texto al descriptor 2 (stderr).
- `tee`: escribe en el archivo **y** deja pasar el flujo; `wc -l` cuenta líneas.

### Errores comunes

- **Orden incorrecto `2>&1 > archivo`**: primero une stderr a la terminal y luego
  redirige stdout; el orden correcto es `> archivo 2>&1`.
- **Usar `>` cuando querías añadir**: `>>` añade, `>` sobrescribe.
- **Confundir `|` con `>`**: el pipe conecta a otro comando; `>` escribe a archivo.

### Ejercicio propuesto

Ejecuta `ls /etc` guardando la salida normal en `ok.txt` y los errores en
`err.txt`, y luego muestra cuántas líneas tiene cada archivo.

> **Pista:** `comando > ok.txt 2> err.txt` y `wc -l ok.txt err.txt`.

### Mini resumen

- Descriptores 0, 1 y 2; redirecciones `>`, `>>`, `<`, `2>`, `2>&1`, `&>`.
- Pipes con `|`; `tee` para bifurcar; `/dev/null` para descartar.

---

## Lección 4. Variables y entorno

### Objetivo de aprendizaje

Declarar variables, exportarlas al entorno, usar variables especiales y aplicar
correctamente las comillas.

### Explicación teórica

Una variable es una caja con etiqueta. Las **locales** solo existen en el shell
actual; las **de entorno** (exportadas) se heredan a los procesos hijos. Piensa en
el entorno como una mochila que pasas a cada programa que lanzas.

Regla de oro: `nombre=valor` **sin espacios** alrededor del `=`. Para usar el
contenido, antepón `$`.

### Ejemplo de código comentado

```bash
#!/usr/bin/env bash
# entorno.sh - variables locales y de entorno (Bash 5.x)
set -euo pipefail

nombre="Ana"                 # variable local (no se hereda)
export EDITOR="nano"         # variable de entorno (sí se hereda)
fecha=$(date +%F)            # sustitución de comandos
printf 'Hola %s, hoy es %s\n' "$nombre" "$fecha"

bash -c 'echo "EDITOR en el hijo: $EDITOR"'   # el hijo ve EDITOR
unset EDITOR                                  # la elimina
echo "Estado del último comando: $?"          # código de salida
```

### Explicación línea por línea

- `nombre="Ana"`: asigna sin espacios; las comillas permiten espacios internos.
- `export EDITOR="nano"`: marca la variable para que los hijos la hereden.
- `$(date +%F)`: ejecuta `date` y guarda su salida (forma recomendada).
- `printf ... "$nombre"`: `printf` es más predecible que `echo`; `%s` es el hueco.
- `bash -c '...'`: lanza un shell hijo que sí ve `EDITOR` por estar exportada.
- `unset`: borra la variable; `$?` muestra el resultado del comando previo.

### Errores comunes

- **`nombre = "Ana"` con espacios**: Bash interpreta `nombre` como comando.
- **Usar `$nombre` sin comillas** cuando contiene espacios: se divide en palabras.
  Usa `"$nombre"` (y `"${nombre}"` para delimitar, p. ej. `"${nombre}s"`).
- **Creer que una local se hereda**: los hijos no la ven; expórtala si la necesitas.

### Ejercicio propuesto

Pide tu nombre con `read` y saluda mostrando además la fecha en formato
`AAAA-MM-DD`.

> **Pista:** `read -r nombre` y `date +%F`; usa `printf` para formatear.

### Mini resumen

- Locales vs. de entorno (`export`); `unset`, `env`, `printenv`.
- Variables especiales: `$?`, `$$`, `$!`, `$#`, `$@`, `$0`.
- Cita siempre: `"$var"`; usa `$(...)` en lugar de backticks.

---

## Lección 5. Operadores y control de flujo

### Objetivo de aprendizaje

Tomar decisiones y repetir acciones con `if`, `case`, `for`, `while` y `until`,
usando comparaciones correctas.

### Explicación teórica

El control de flujo es como un semáforo para tus comandos: `if` decide, `case`
elige entre varias opciones y los bucles repiten. Las condiciones se evalúan con
`[[ ]]` (recomendado en Bash) o `[ ]` (POSIX).

Comparaciones numéricas: `-eq`, `-ne`, `-lt`, `-le`, `-gt`, `-ge`.
De cadenas: `==`, `!=`, `-z` (vacía), `-n` (no vacía).
De archivos: `-e`, `-f`, `-d`, `-r`, `-w`, `-x`.

### Ejemplo de código comentado

```bash
#!/usr/bin/env bash
# flujo.sh - clasifica números del 1 al 5 (Bash 5.x)
set -euo pipefail

for n in 1 2 3 4 5; do                 # recorre los números
    if (( n % 2 == 0 )); then          # aritmética: ¿par?
        echo "$n es par"
    else
        echo "$n es impar"
    fi
done

case "$1" in                           # primer argumento del script
    start)  echo "Arrancando" ;;
    stop)   echo "Deteniendo" ;;
    "")     echo "Uso: $0 {start|stop}" >&2; exit 1 ;;
    *)      echo "Opción desconocida: $1" >&2; exit 1 ;;
esac
```

### Explicación línea por línea

- `for n in 1 2 3 4 5; do ... done`: itera sobre una lista literal.
- `(( n % 2 == 0 ))`: evaluación aritmética; devuelve éxito si es verdadero.
- `case "$1" in ... esac`: compara `$1` con patrones; `;;` cierra cada rama.
- `*)`: comodín que captura cualquier otro valor (rama por defecto).

### Errores comunes

- **Usar `==` en `[ ]`**: en POSIX se usa `=`; en `[[ ]]` sí vale `==`.
- **Olvidar `then` o `fi`**: producen errores de sintaxis.
- **No citar la variable en `case`**: `case $1 in` falla si `$1` está vacío; usa
  `case "$1" in`.

### Ejercicio propuesto

Escribe un script que recorra los archivos `.txt` del directorio actual y diga
cuántas líneas tiene cada uno, saltando los vacíos.

> **Pista:** `for f in *.txt; do [[ -s "$f" ]] || continue; ...; done` y `wc -l`.

### Mini resumen

- `if/elif/else`, `case`; `[[ ]]` para condiciones seguras.
- Bucles `for`, `while`, `until`, `select`; control con `break`/`continue`.
- Operadores lógicos `&&`, `||`, `;`.

---

## Lección 6. Funciones

### Objetivo de aprendizaje

Escribir funciones reutilizables, pasarles argumentos, usar variables locales y
devolver resultados.

### Explicación teórica

Una función es una receta con nombre: agrupas instrucciones y las invocas cuando
las necesitas. En Bash los argumentos llegan como `$1`, `$2`, ... y `$@`; la
función solo puede devolver un **código de estado** (0-255), así que para devolver
datos se usa `echo` y se captura con `$(...)`.

Las variables son **globales por defecto**; usa `local` para aislarlas.

### Ejemplo de código comentado

```bash
#!/usr/bin/env bash
# funciones.sh - utilidades reutilizables (Bash 5.x)
set -euo pipefail

sumar() {                       # función que suma dos números
    local a="$1" b="$2"         # variables locales
    echo $(( a + b ))           # "devuelve" el resultado por stdout
}

es_par() {                      # devuelve estado: 0 = par, 1 = impar
    (( $1 % 2 == 0 ))
}

resultado=$(sumar 3 4)          # captura el valor devuelto
echo "3 + 4 = $resultado"

if es_par 8; then
    echo "8 es par"
fi
```

### Explicación línea por línea

- `sumar() { ... }`: sintaxis de definición; también vale `function sumar {`.
- `local a="$1" b="$2"`: variables visibles solo dentro de la función.
- `echo $(( a + b ))`: imprime la suma; el llamador la captura con `$(...)`.
- `(( $1 % 2 == 0 ))`: el resultado aritmético se convierte en código de salida.
- `resultado=$(sumar 3 4)`: ejecuta la función y guarda su salida.

### Errores comunes

- **Esperar `return` con un valor**: `return` solo acepta enteros 0-255. Usa `echo`
  para devolver datos.
- **Olvidar `local`**: la función modifica variables globales sin querer.
- **No citar `"$@"`** al reenviar argumentos: se pierden los que tienen espacios.

### Ejercicio propuesto

Crea una función `mayor` que reciba dos números e imprima el mayor.

> **Pista:** usa `(( a > b ? a : b ))` o un `if`; devuelve con `echo`.

### Mini resumen

- Definición `nombre() { ... }`; parámetros `$1`, `$@`, `$#`.
- `local` para ámbito reducido; `echo` para devolver datos, `return` para estado.

---

## Lección 7. Scripting

### Objetivo de aprendizaje

Estructurar un script robusto con shebang, manejo de errores, `trap` y opciones de
línea de comandos.

### Explicación teórica

Un script es un programa de texto que Bash ejecuta línea a línea. Un buen script
es **defensivo**: falla rápido, limpia al salir y valida sus argumentos. Las
herramientas clave son `set -euo pipefail`, `trap` y `getopts`.

`trap` es como un seguro: si el script recibe una señal o termina, ejecuta una
función de limpieza.

### Ejemplo de código comentado

```bash
#!/usr/bin/env bash
# contar.sh - cuenta líneas de un archivo (Bash 5.x)
set -euo pipefail

limpiar() { rm -f "${tmp:-}"; }         # limpieza segura
trap limpiar EXIT INT TERM              # se ejecuta al salir o ser interrumpido

verbose=0                               # opción por defecto
while getopts ":hv" opt; do             # parsea -h y -v
    case "$opt" in
        h) echo "Uso: $0 [-v] archivo"; exit 0 ;;
        v) verbose=1 ;;
        \?) echo "Opción inválida: -$OPTARG" >&2; exit 1 ;;
    esac
done
shift $((OPTIND - 1))                   # descarta las opciones ya leídas

archivo="${1:?Falta el archivo}"
tmp=$(mktemp)
[[ -f "$archivo" ]] || { echo "No existe: $archivo" >&2; exit 1; }
wc -l < "$archivo" > "$tmp"
(( verbose )) && echo "Archivo analizado: $archivo" >&2
cat "$tmp"
```

### Explicación línea por línea

- `trap limpiar EXIT INT TERM`: registra la función para la salida y las señales.
- `getopts ":hv" opt`: recorre las opciones; el `:` inicial silencia errores.
- `$OPTARG`: valor de la opción que requiere argumento; `$OPTIND` índice actual.
- `"${1:?Falta el archivo}"`: si `$1` no existe, imprime el mensaje y sale.
- `mktemp`: crea un archivo temporal único y seguro.

### Errores comunes

- **No usar `set -e`**: el script continúa tras un fallo y produce daños.
- **Referenciar variables sin definir**: `set -u` lo detecta a tiempo.
- **Ignorar el código de salida**: revisa `$?` o usa `if ! comando; then`.

### Ejercicio propuesto

Escribe `suma.sh` que acepte `-h` (ayuda) y un número variable de enteros, y
muestre la suma total.

> **Pista:** tras `shift $((OPTIND-1))`, recorre `"$@"` con un bucle y acumula.

### Mini resumen

- Cabecera `#!/usr/bin/env bash` + `set -euo pipefail`.
- `trap` para limpieza; `getopts` y `shift` para argumentos.
- Valida entradas y devuelve códigos de salida coherentes.

---

## Lección 8. Procesamiento de texto

### Objetivo de aprendizaje

Extraer, transformar y resumir texto combinando `grep`, `sed`, `awk` y utilidades
de filtrado.

### Explicación teórica

Los comandos de texto son como una línea de montaje: cada uno hace una tarea
pequeña y pasa el resultado al siguiente. `grep` filtra líneas, `sed` sustituye,
`awk` trabaja por columnas y `sort`/`uniq`/`cut` completan el trabajo.

### Ejemplo de código comentado

```bash
#!/usr/bin/env bash
# top_ips.sh - ranking de IPs en un log de acceso (Bash 5.x)
set -euo pipefail

log="${1:?Uso: $0 access.log}"

# 1) extrae la IP (campo 1)  2) ordena  3) cuenta únicas  4) ordena desc  5) top 5
awk '{print $1}' "$log" \
    | sort \
    | uniq -c \
    | sort -rn \
    | head -n 5

# Sustituye localhost por 127.0.0.1 y muestra solo las líneas con error 500
sed -n '/ 500 /{s/localhost/127.0.0.1/g; p}' "$log"
```

### Explicación línea por línea

- `awk '{print $1}'`: imprime la primera columna de cada línea.
- `sort | uniq -c`: agrupa duplicados adyacentes y los cuenta.
- `sort -rn`: orden numérico inverso (de mayor a menor).
- `sed -n '/ 500 /{...; p}'`: `-n` silencia la salida; `p` imprime solo lo filtrado.
- `s/localhost/127.0.0.1/g`: sustitución global dentro de la línea.

### Errores comunes

- **`uniq` sin `sort` previo**: solo elimina duplicados **adyacentes**.
- **Olvidar `-r` en `sort`** para datos numéricos: ordena como texto (`10` < `9`).
- **Editar con `sed -i` sin respaldo**: usa `sed -i.bak` si el cambio es delicado.

### Ejercicio propuesto

Dado `/etc/passwd`, lista los usuarios (campo 1) ordenados alfabéticamente y
muestra cuántos hay.

> **Pista:** `cut -d: -f1 /etc/passwd | sort` y `wc -l`.

### Mini resumen

- `grep` filtra, `sed` sustituye, `awk` procesa columnas.
- `cut`, `sort`, `uniq`, `tr`, `wc` como apoyo.
- El pipe convierte comandos simples en potentes procesadores de texto.

---

## Lección 9. Gestión de procesos

### Objetivo de aprendizaje

Inspeccionar procesos, enviarles señales y controlar trabajos en primer y segundo
plano.

### Explicación teórica

Cada programa en ejecución es un **proceso** con un identificador (`PID`). Las
**señales** son mensajes que le envías: `SIGTERM` (15) pide terminar con orden,
`SIGKILL` (9) lo mata sin apelación. Los trabajos lanzados desde tu shell se
gestionan con `jobs`, `fg` y `bg`.

### Ejemplo de código comentado

```bash
#!/usr/bin/env bash
# procesos.sh - ejecuta tareas en paralelo y controla su fin (Bash 5.x)
set -euo pipefail

sleep 5 &                    # lanza en segundo plano
pid=$!                       # guarda su PID
echo "Lanzado con PID $pid"
jobs -l                      # lista los trabajos con PID
if kill -0 "$pid" 2>/dev/null; then
    echo "Sigue vivo; lo terminamos con SIGTERM"
    kill "$pid"              # solicitud amable de terminación
fi
wait "$pid" 2>/dev/null || true   # espera su final
echo "Listo"
```

### Explicación línea por línea

- `sleep 5 &`: el `&` envía el proceso al fondo.
- `$!`: PID del último proceso lanzado en background.
- `jobs -l`: muestra los trabajos del shell actual con su PID.
- `kill -0`: no mata, solo comprueba si el proceso existe.
- `wait "$pid"`: espera a que termine; `|| true` evita que `set -e` aborte.

### Errores comunes

- **Usar `kill -9` de entrada**: no deja limpiar recursos. Prueba primero `kill`.
- **Confundir PID con nombre**: `pkill -f` busca por la línea de comandos.
- **Olvidar que `nohup` redirige a `nohup.out`** si no indicas salida.

### Ejercicio propuesto

Lanza tres `sleep 10` en paralelo y termínalos todos de golpe.

> **Pista:** guárdalos en un array de PIDs y recórrelo con `kill`.

### Mini resumen

- `ps`, `top`, `htop`, `pgrep`, `pidof` para observar procesos.
- `kill`, `killall`, `pkill` para señales; `&`, `jobs`, `fg`, `bg` para trabajos.
- `wait` sincroniza; `nohup`/`disown` desvinculan del shell.

---

## Lección 10. Expansiones y globbing

### Objetivo de aprendizaje

Aprovechar las expansiones de Bash (comodines, llaves, aritmética y procesos) para
escribir menos y hacer más.

### Explicación teórica

Bash transforma tu línea **antes** de ejecutarla. A eso se le llama expansión:

- **Globbing**: `*`, `?`, `[...]` generan listas de archivos.
- **Brace expansion**: `{a,b,c}` genera combinaciones.
- **Tilde**: `~` se convierte en tu home.
- **Aritmética**: `$(( ... ))` calcula.
- **Sustitución de procesos**: `<( ... )` trata la salida como un archivo.

### Ejemplo de código comentado

```bash
#!/usr/bin/env bash
# expansiones.sh - demuestra expansiones de Bash (Bash 5.x)
set -euo pipefail

mkdir -p demo/{src,test,docs}         # brace expansion: tres carpetas
cp /etc/hostname demo/src/host.txt     # copia un archivo real

echo "Potencias:" {1..4}              # lista 1 2 3 4
echo "2^10 = $(( 2 ** 10 ))"          # aritmética

for f in demo/src/*; do               # globbing
    [[ -e "$f" ]] || continue         # evita literal '*' si no hay coincidencias
    echo "Archivo: $f"
done

diff <(ls demo/src) <(ls demo/test)   # sustitución de procesos
rm -r demo
```

### Explicación línea por línea

- `demo/{src,test,docs}`: Bash genera las tres rutas antes de llamar a `mkdir`.
- `{1..4}`: secuencia; también `{a..d}` o `{01..10}`.
- `$(( 2 ** 10 ))`: potencia aritmética entera.
- `for f in demo/src/*`: el glob se expande a la lista de archivos.
- `<(ls ...)`: sustitución de procesos; `diff` recibe dos "archivos" temporales.

### Errores comunes

- **Glob sin coincidencias** deja el `*` literal; activa `shopt -s nullglob`.
- **Comillas que desactivan el glob**: `"*.txt"` no expande; `*.txt` sí.
- **Confundir `{1..4}` con un rango de variables**: solo genera texto.

### Ejercicio propuesto

Crea un script que genere los nombres `informe_2024_Q1.txt` ... `Q4` usando brace
expansion y cree los archivos vacíos.

> **Pista:** `touch informe_2024_Q{1..4}.txt` o un bucle `for q in {1..4}`.

### Mini resumen

- Comodines `*`, `?`, `[...]`; llaves `{a,b}`, `{1..9}`.
- `~`, `$(( ... ))` y `<( ... )` completan las expansiones.
- Activa `nullglob`/`globstar` según necesites.

---

## Lección 11. Historial y atajos de teclado

### Objetivo de aprendizaje

Reutilizar comandos anteriores y moverte por la línea de comandos con atajos que
ahorran tiempo.

### Explicación teórica

Bash recuerda los comandos que escribes en un **historial**. Es como el "deshacer"
de un editor, pero para toda tu sesión: puedes repetir, buscar y reutilizar
fragmentos. Los atajos de teclado, heredados de Emacs, evitan que sueltes el
teclado para usar el ratón.

### Ejemplo de código comentado

```bash
#!/usr/bin/env bash
# historial.sh - configuración y uso del historial (Bash 5.x)
set -euo pipefail

export HISTSIZE=10000                 # líneas en memoria
export HISTFILESIZE=20000             # líneas guardadas en disco
export HISTCONTROL=ignoredups:erasedups
export HISTIGNORE="ls:cd:pwd:exit"    # no guardar estos comandos
export HISTTIMEFORMAT="%F %T "        # marca de tiempo
shopt -s histappend                   # añade al archivo, no lo sobrescribe

history | tail -n 5                   # últimos 5 comandos
echo "Para repetir el último comando: !!"   # (solo funciona interactivo)
```

### Explicación línea por línea

- `HISTSIZE`/`HISTFILESIZE`: cuántos comandos se recuerdan en memoria y en disco.
- `HISTCONTROL=erasedups`: elimina duplicados del historial.
- `HISTIGNORE`: lista de comandos que no se registran.
- `shopt -s histappend`: varias terminales no se pisan el historial.
- `history | tail`: muestra los últimos comandos registrados.

### Errores comunes

- **Esperar que `!!` funcione en un script**: la expansión del historial está
  desactivada en scripts no interactivos.
- **No hacer `histappend`**: al cerrar dos terminales, una sobreescribe a la otra.
- **Guardar secretos**: evita escribir contraseñas; considera `HISTIGNORE`.

### Ejercicio propuesto

Configura el historial para que ignore `ls`, `cd` y `exit`, y comprueba que
`Ctrl+R` encuentra un comando antiguo.

> **Pista:** añade los `export` a `~/.bashrc` y ejecuta `source ~/.bashrc`.

### Mini resumen

- `history`, `!!`, `!n`, `!$`; `Ctrl+R` para búsqueda inversa.
- Variables `HISTSIZE`, `HISTCONTROL`, `HISTIGNORE`, `HISTTIMEFORMAT`.
- `shopt -s histappend` para no perder entradas.

---

## Lección 12. Personalización del entorno

### Objetivo de aprendizaje

Configurar el prompt, los aliases y los archivos de inicio para adaptar Bash a tu
flujo de trabajo.

### Explicación teórica

Bash lee archivos de configuración al arrancar. Para un shell **interactivo** lee
`~/.bashrc`; para un **login shell** lee `~/.bash_profile` (o `~/.profile`). Es
como preparar tu escritorio al llegar: defines colores, atajos y rutas.

### Ejemplo de código comentado

```bash
# fragmento para ~/.bashrc  (Bash 5.x)

# Prompt: verde usuario@host, azul ruta, símbolo final
PS1='\[\e[32m\]\u@\h\[\e[0m\]:\[\e[34m\]\w\[\e[0m\]\$ '

alias ll='ls -lah --color=auto'      # listado cómodo
alias gs='git status'
alias ..='cd ..'

mkcd() { mkdir -p "$1" && cd "$1"; } # crea y entra en una carpeta

extract() {                          # descomprime según la extensión
    case "$1" in
        *.tar.gz|*.tgz) tar xzf "$1" ;;
        *.zip)          unzip "$1" ;;
        *) echo "Formato no soportado" >&2; return 1 ;;
    esac
}
```

### Explicación línea por línea

- `PS1=...`: define el prompt; `\u` usuario, `\h` host, `\w` ruta, `\$` `$`/`#`.
- `\[\e[32m\]` y `\[\e[0m\]`: activan color verde y lo resetean; los `\[ \]` evitan
  que Bash cuente mal el ancho.
- `alias`: atajo de teclado para un comando.
- `mkcd() { ... }`: función que combina creación y navegación.
- `case "$1"`: elige el descompresor según la extensión.

### Errores comunes

- **Poner configuración en `~/.bash_profile` esperando que se lea en cada terminal
  nueva**: los shells interactivos no-login leen `~/.bashrc`. Encadena uno con otro.
- **Olvidar `source ~/.bashrc`**: los cambios no aplican hasta reiniciar el shell.
- **Colores sin `\[ \]`**: el prompt se descuadra al editar líneas largas.

### Ejercicio propuesto

Añade un alias `ports` que muestre los puertos a la escucha y una función `backup`
que copie un archivo añadiéndole la fecha al nombre.

> **Pista:** `alias ports='ss -tulpn'`; en la función usa `cp "$1" "$1.$(date +%F)"`.

### Mini resumen

- `~/.bashrc` (interactivo) y `~/.bash_profile` (login); `source` para recargar.
- `PS1`/`PS2` personalizan el prompt; aliases y funciones agilizan tareas.
- Frameworks: Oh My Bash, Bash-it.

---

## Lección 13. Manejo de jobs y sesiones

### Objetivo de aprendizaje

Mantener procesos vivos más allá de la sesión y organizar tu trabajo con
multiplexores de terminal.

### Explicación teórica

Cuando cierras una terminal, los procesos hijos reciben `SIGHUP` y mueren. Para
evitarlo puedes **desvincularlos** (`nohup`, `disown`) o usar un **multiplexor**
(`tmux`, `screen`), que es como tener una oficina con varias ventanas que siguen
abiertas aunque te vayas.

### Ejemplo de código comentado

```bash
#!/usr/bin/env bash
# persistente.sh - lanza una tarea que sobrevive a la sesión (Bash 5.x)
set -euo pipefail

salida="$HOME/tarea.log"
nohup bash -c 'for i in {1..5}; do echo "latido $i $(date +%T)"; sleep 2; done' \
    > "$salida" 2>&1 &                 # ignora SIGHUP y redirige salida
pid=$!
disown "$pid" 2>/dev/null || true      # lo saca de la tabla de trabajos
echo "Tarea en marcha (PID $pid). Log: $salida"
wait "$pid"                            # en un script real, podríamos esperar
tail -n 5 "$salida"
```

### Explicación línea por línea

- `nohup`: hace que el proceso ignore `SIGHUP` al cerrar la terminal.
- `> "$salida" 2>&1 &`: redirige salida y error, y lo manda al fondo.
- `disown`: quita el trabajo de la lista del shell.
- `wait "$pid"`: espera su finalización (útil si el script debe bloquear).
- `tail`: muestra las últimas líneas del log.

### Errores comunes

- **`nohup` sin redirigir**: la salida va a `nohup.out`, que puede crecer sin
  control.
- **Cerrar tmux con `exit` dentro de un panel**: cierra solo ese panel, no la
  sesión; usa `Ctrl+b d` para desconectar.
- **Confundir `disown` con `kill`**: `disown` solo desvincula, no termina.

### Ejercicio propuesto

Lanza en segundo plano un `sleep 60` que sobreviva al cierre de la terminal y
comprueba con `ps` que sigue vivo.

> **Pista:** `nohup sleep 60 >/dev/null 2>&1 & disown` y luego `pgrep -a sleep`.

### Mini resumen

- `&`, `jobs`, `fg`, `bg`, `Ctrl+Z` para trabajos.
- `nohup` y `disown` para persistir; `tmux`/`screen` para sesiones duraderas.

---

## Lección 14. Redes y descargas

### Objetivo de aprendizaje

Descargar recursos, transferir archivos de forma segura y consultar el estado de
la red.

### Explicación teórica

La red es el sistema circulatorio de las máquinas: `ping` comprueba si hay latido,
`curl`/`wget` traen o envían datos, `ssh`/`scp`/`rsync` mueven archivos y
`ss`/`ip` muestran el estado de las conexiones.

### Ejemplo de código comentado

```bash
#!/usr/bin/env bash
# red.sh - descarga y verifica un recurso (Bash 5.x)
set -euo pipefail

url="https://api.github.com"
destino="respuesta.json"

# -s silencioso, -S muestra errores, -f falla con códigos HTTP >= 400, -L sigue redirecciones
curl -sSfL "$url" -o "$destino"
echo "Descargado $(wc -c < "$destino") bytes"

# Comprobar conectividad sin inundar la red
ping -c 3 1.1.1.1 >/dev/null && echo "Red OK"

# Copiar el archivo a un host remoto (ejemplo; requiere acceso)
# scp "$destino" usuario@host:/tmp/
# rsync -avz "$destino" usuario@host:/tmp/
```

### Explicación línea por línea

- `curl -sSfL`: silencioso, con errores, falla en HTTP erróneo y sigue redirecciones.
- `-o "$destino"`: guarda la respuesta en el archivo indicado.
- `wc -c < "$destino"`: cuenta bytes leyendo por stdin.
- `ping -c 3`: envía 3 paquetes y termina; `&&` encadena si hubo respuesta.
- `rsync -avz`: archivo, verbose y compresión; ideal para sincronizar.

### Errores comunes

- **`curl` sin `-f`**: un error 404 se guarda como si fuera una página válida.
- **Credenciales en la línea de comandos**: quedan en el historial. Usa variables
  de entorno o archivos de configuración.
- **`scp` sin comprobar la clave del host**: la primera conexión pide confirmar.

### Ejercicio propuesto

Descarga `https://example.com` en `pagina.html`, muestra sus primeras 5 líneas y
cuenta cuántos enlaces `<a href` contiene.

> **Pista:** `curl -sSfL ... -o pagina.html`, `head -n 5` y
> `grep -c '<a href' pagina.html`.

### Mini resumen

- `curl`/`wget` para descargas; `ping` para conectividad.
- `ssh`, `scp`, `rsync` para transferencias seguras.
- `ss`, `netstat`, `ip` para inspeccionar la red.

---

## Lección 15. Administración del sistema

### Objetivo de aprendizaje

Gestionar usuarios, discos, paquetes, registros y tareas programadas.

### Explicación teórica

Administrar un sistema es como llevar un edificio: hay inquilinos (usuarios),
almacenes (discos), suministros (paquetes), un libro de incidencias (logs) y
tareas periódicas (cron). La mayoría de estas operaciones requieren privilegios de
root (`sudo`).

### Ejemplo de código comentado

```bash
#!/usr/bin/env bash
# salud.sh - informe básico del sistema (Bash 5.x)
set -euo pipefail

echo "== Uso de disco =="
df -h / | awk 'NR==2 {print "Raíz:", $5, "usado"}'

echo "== Top 3 directorios de /var =="
du -h --max-depth=1 /var 2>/dev/null | sort -h | tail -n 4

echo "== Paquetes actualizables (Debian) =="
if command -v apt >/dev/null; then
    apt list --upgradable 2>/dev/null | tail -n +2 | wc -l
fi

echo "== Errores recientes del sistema =="
journalctl -p err -n 5 --no-pager 2>/dev/null || echo "journalctl no disponible"
```

### Explicación línea por línea

- `df -h /` con `awk 'NR==2'`: toma la línea de la raíz y su porcentaje de uso.
- `du -h --max-depth=1 | sort -h | tail`: los directorios más grandes.
- `command -v apt`: comprueba si el comando existe antes de usarlo.
- `journalctl -p err -n 5`: últimos 5 mensajes de nivel error.
- `|| echo ...`: alternativa si el comando no está disponible.

### Errores comunes

- **Ejecutar `apt upgrade` sin `sudo`**: falla por permisos.
- **`crontab` con rutas relativas**: cron usa un entorno mínimo; usa rutas
  absolutas y redirige la salida.
- **Borrar logs en uso**: usa `journalctl --vacuum-time` o trunca con `: > log`.

### Ejercicio propuesto

Escribe una entrada de cron que ejecute cada día a las 3:00 un script de backup y
guarde su salida en un log.

> **Pista:** `0 3 * * * /ruta/backup.sh >> /var/log/backup.log 2>&1`.

### Mini resumen

- Usuarios/grupos: `useradd`, `usermod`, `passwd`, `id`.
- Discos: `df`, `du`, `lsblk`, `mount`. Paquetes: `apt`, `dnf`, `pacman`.
- Logs y programación: `journalctl`, `crontab`, `at`.

---

## Lección 16. Seguridad

### Objetivo de aprendizaje

Aplicar permisos especiales, usar `sudo`/`su` con criterio, gestionar claves SSH y
escribir scripts que validen sus entradas.

### Explicación teórica

La seguridad es como las cerraduras de una casa: los permisos normales son la
puerta, los especiales (SUID/SGID/sticky) son llaves maestras y `sudo` es el
conserje que decide quién entra. Un principio clave: **mínimo privilegio** — da
solo el acceso necesario.

- **SUID (4)**: el programa se ejecuta con los permisos del dueño.
- **SGID (2)**: en directorios, los nuevos archivos heredan el grupo.
- **Sticky (1)**: solo el dueño puede borrar sus archivos (típico en `/tmp`).

### Ejemplo de código comentado

```bash
#!/usr/bin/env bash
# valida.sh - valida entradas antes de usarlas (Bash 5.x)
set -euo pipefail

nombre="${1:?Uso: $0 <nombre>}"

# Solo letras, números, punto, guion y guion bajo
if [[ ! "$nombre" =~ ^[A-Za-z0-9._-]+$ ]]; then
    echo "Nombre inválido: $nombre" >&2
    exit 1
fi

ruta="/var/datos/$nombre"
if [[ -e "$ruta" ]]; then
    echo "Ya existe: $ruta" >&2
    exit 1
fi

tmp=$(mktemp)                 # archivo temporal seguro
trap 'rm -f "$tmp"' EXIT      # se limpia siempre al salir
printf 'Contenido para %s\n' "$nombre" > "$tmp"
echo "Preparado $ruta desde $tmp"
```

### Explicación línea por línea

- `"${1:?Uso: ...}"`: exige el argumento o aborta con el mensaje.
- `[[ ! "$nombre" =~ ^[A-Za-z0-9._-]+$ ]]`: valida con expresión regular.
- `"$ruta"`: evita inyección al no interpretar metacaracteres.
- `mktemp`: crea un temporal con nombre impredecible (evita ataques de symlink).
- `trap ... EXIT`: garantiza la limpieza aunque el script falle.

### Errores comunes

- **`eval "$entrada"`**: ejecuta código arbitrario. Evítalo siempre.
- **`chmod 777`**: da permisos totales a todos; usa el mínimo necesario.
- **Claves SSH con permisos amplios**: `~/.ssh/id_*` debe ser `600`; si no, SSH las
  rechaza.
- **Concatenar variables en SQL o rutas** sin validar: riesgo de inyección.

### Ejercicio propuesto

Crea una función que reciba un número entero y devuelva error si la entrada no es
un entero positivo.

> **Pista:** `[[ "$1" =~ ^[0-9]+$ ]] && (( $1 > 0 ))`.

### Mini resumen

- SUID, SGID y sticky bit; `find -perm -4000` para auditarlos.
- `sudo`/`su` y `visudo`; mínimo privilegio.
- Valida entradas, usa `mktemp` y evita `eval`.

---

## Lección 17. Bash avanzado

### Objetivo de aprendizaje

Usar arrays, here documents, `coproc`, aritmética y fechas para resolver problemas
más complejos.

### Explicación teórica

Los **arrays** son listas con índice (o diccionarios, si son asociativos). Un
**here document** es un texto multilínea que entregas como entrada. `coproc` crea
un proceso cooperativo con el que conversas. Todo esto convierte a Bash en algo más
que un simple intérprete.

### Ejemplo de código comentado

```bash
#!/usr/bin/env bash
# avanzado.sh - arrays, here document y fechas (Bash 5.x)
set -euo pipefail

declare -A stock                     # array asociativo (Bash 4+)
stock[manzanas]=10
stock[peras]=5

for fruta in "${!stock[@]}"; do      # recorre las claves
    printf '%-10s %s\n' "$fruta" "${stock[$fruta]}"
done

fecha=$(date +%F)                    # 2026-09-16
cat <<EOF > inventario.txt           # here document con expansión
Inventario del $fecha
Manzanas: ${stock[manzanas]}
Peras:    ${stock[peras]}
EOF

cat inventario.txt
rm -f inventario.txt
```

### Explicación línea por línea

- `declare -A stock`: declara un array asociativo (clave → valor).
- `"${!stock[@]}"`: devuelve todas las **claves**.
- `"${stock[$fruta]}"`: valor asociado a esa clave.
- `printf '%-10s %s\n'`: alinea a la izquierda en 10 caracteres.
- `<<EOF ... EOF`: here document; las variables se expanden salvo que uses `<<'EOF'`.

### Errores comunes

- **Usar arrays sin Bash 4+**: los asociativos requieren Bash 4 o superior.
- **`${array[@]}` sin comillas**: los elementos con espacios se separan.
- **`declare -A` sin inicializar**: algunas versiones antiguas necesitan
  `declare -A arr=()`.

### Ejercicio propuesto

Guarda en un array asociativo tres países y sus capitales, y recórrelo imprimiendo
`País -> Capital`.

> **Pista:** `declare -A cap; cap[España]=Madrid;` y recorre `"${!cap[@]}"`.

### Mini resumen

- Arrays indexados y asociativos; `declare` con `-a`, `-A`, `-i`, `-r`.
- Here documents `<<` y here strings `<<<`; `coproc` para procesos cooperativos.
- Aritmética `$(( ))` y fechas con `date`.

---

## Lección 18. Buenas prácticas y estilo

### Objetivo de aprendizaje

Escribir scripts legibles, portables y verificables, y comprobarlos con
herramientas de linting y testing.

### Explicación teórica

Un script no solo debe funcionar: debe poder leerlo otra persona (o tú mismo en
seis meses). Las buenas prácticas son como las normas de tráfico: evitan choques.
Además, herramientas como `shellcheck` actúan de revisor automático.

### Ejemplo de código comentado

```bash
#!/usr/bin/env bash
#
# nombre: contar_palabras.sh
# descripción: Cuenta palabras de un archivo de texto.
# uso: ./contar_palabras.sh <archivo>
#
set -euo pipefail

readonly PROG="${0##*/}"             # nombre del script sin ruta

uso() {
    printf 'Uso: %s <archivo>\n' "$PROG" >&2
    exit 1
}

main() {
    (($# == 1)) || uso                  # exactamente un argumento
    local archivo="$1"
    [[ -f "$archivo" ]] || { echo "No es un archivo: $archivo" >&2; exit 1; }
    local palabras
    palabras=$(wc -w < "$archivo")
    printf '%s contiene %s palabras\n' "$archivo" "$palabras"
}

main "$@"
```

### Explicación línea por línea

- `readonly PROG="${0##*/}"`: guarda el nombre del script sin la ruta (`##*/`).
- `uso()`: centraliza el mensaje de ayuda.
- `(($# == 1)) || uso`: si no hay un argumento, llama a `uso`.
- `local`: limita el alcance de las variables dentro de `main`.
- `main "$@"`: patrón común; reenvía todos los argumentos citados.

### Errores comunes

- **Nombres genéricos** (`a`, `tmp`, `data`): dificultan la lectura. Usa nombres
  descriptivos.
- **Código duplicado**: extrae funciones.
- **No comprobar con `shellcheck`**: deja pasar errores sutiles de citado.

### Ejercicio propuesto

Toma el script de la lección y pásalo por `shellcheck`. Corrige todo lo que
reporte.

> **Pista:** instala con `apt install shellcheck` y ejecuta `shellcheck script.sh`.

### Mini resumen

- Nombres claros, `readonly`, `local`, función `main`.
- Portabilidad: `#!/bin/sh` + POSIX o `bash` con aviso.
- Linting con `shellcheck`; testing con `bats` o `shunit2`.

---

## Lección 19. Ejemplos prácticos

### Objetivo de aprendizaje

Integrar todo lo aprendido en scripts completos de uso real.

### Explicación teórica

Aquí no hay conceptos nuevos: combinamos piezas (funciones, control de flujo,
procesamiento de texto, redirecciones) para resolver tareas del mundo real.

### Ejemplo de código comentado

```bash
#!/usr/bin/env bash
# backup.sh - copia de seguridad con rotación (Bash 5.x)
set -euo pipefail

origen="${1:?Uso: $0 <origen> [destino]}"
destino="${2:-$HOME/backups}"
retencion=7

mkdir -p "$destino"
marca=$(date +%F_%H-%M-%S)
archivo="$destino/backup_$(basename "$origen")_$marca.tar.gz"

tar czf "$archivo" -C "$(dirname "$origen")" "$(basename "$origen")"
printf 'Backup creado: %s\n' "$archivo"

# Rotación: conserva solo los 7 más recientes
mapfile -t antiguos < <(ls -1t "$destino"/backup_*.tar.gz | tail -n +$((retencion + 1)))
((${#antiguos[@]})) && rm -f -- "${antiguos[@]}"
echo "Backups antiguos eliminados: ${#antiguos[@]}"
```

### Explicación línea por línea

- `"${2:-$HOME/backups}"`: usa el segundo argumento o un valor por defecto.
- `basename`/`dirname`: separan nombre y carpeta de una ruta.
- `tar czf ... -C dir nombre`: comprime sin guardar rutas absolutas.
- `mapfile -t`: lee líneas a un array (Bash 4+).
- `"${antiguos[@]}"`: elimina todos los antiguos de una vez.

### Errores comunes

- **No comprobar que el origen existe**: añade `[[ -d "$origen" ]] || exit 1`.
- **Rotación con `rm` sin `--`**: un archivo que empiece por `-` se interpreta como
  opción.
- **Backups en el mismo disco**: para seguridad real, copia a otro medio.

### Ejercicio propuesto

Adapta el script para que, además, comprima el backup final con `gzip -9` y
verifique la integridad con `tar tzf`.

> **Pista:** `tar tzf "$archivo" >/dev/null && echo "OK"`.

### Mini resumen

- Los scripts reales combinan validación, funciones y manejo de errores.
- Patrones útiles: backup con rotación, monitorización, parsing de logs y APIs.

---

## Lección 20. Apéndices

### Tabla de códigos de salida

| Código | Significado |
|-------:|-------------|
| 0 | Éxito |
| 1 | Error general |
| 2 | Uso incorrecto de builtin / shell |
| 126 | Comando no ejecutable |
| 127 | Comando no encontrado |
| 130 | Terminado por Ctrl+C (SIGINT) |
| 137 | Terminado por SIGKILL (128+9) |
| 143 | Terminado por SIGTERM (128+15) |

### Cheat sheet de comandos

```text
NAVEGACIÓN      pwd ls cd tree
ARCHIVOS        cp mv rm mkdir rmdir touch ln find
VER CONTENIDO   cat less head tail nl
TEXTO           grep sed awk cut sort uniq tr wc diff
PERMISOS        chmod chown chgrp umask
PROCESOS        ps top htop kill pkill jobs fg bg
RED             ping curl wget ssh scp rsync ss ip
DISCOS          df du lsblk mount umount
SISTEMA         systemctl journalctl cron apt dnf pacman
COMPRESIÓN      tar gzip zip unzip
HISTORIAL       history !! !$ Ctrl+R
AYUDA           man comando --help info
```

### Recursos y enlaces útiles

- GNU Bash Manual: <https://www.gnu.org/software/bash/manual/>
- Bash Hackers Wiki: <https://wiki.bash-hackers.org/>
- ShellCheck: <https://www.shellcheck.net/>
- Explain Shell: <https://explainshell.com/>
- Google Shell Style Guide: <https://google.github.io/styleguide/shellguide.html>
- Pure Bash Bible: <https://github.com/dylanaraps/pure-bash-bible>

### Glosario de términos

| Término | Definición |
|---------|------------|
| **Shell** | Intérprete de comandos. |
| **Builtin** | Comando interno del shell (ej. `cd`, `echo`). |
| **Pipe** | Conexión de la salida de un comando con la entrada de otro (`\|`). |
| **Descriptor** | Número que identifica un flujo de E/S (0, 1, 2). |
| **Globbing** | Expansión de comodines (`*`, `?`). |
| **Shebang** | Primera línea `#!` que indica el intérprete. |
| **PID** | Identificador de proceso. |
| **SUID** | Bit que ejecuta un archivo con permisos del dueño. |
| **Here document** | Bloque de entrada literal delimitado por una marca. |
| **Job** | Proceso o pipeline gestionado por el shell. |
| **POSIX** | Estándar que define la interfaz portable de los shells. |

---

*Fin del manual. Cada lección incluye un ejercicio propuesto: resuélvelo antes de
avanzar a la siguiente.*



