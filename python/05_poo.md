# 05 · Programación Orientada a Objetos

**Versión recomendada:** Python 3.12
**Requisito:** [04_funciones.md](04_funciones.md)
**Nivel:** Intermedio
**Duración estimada de estudio:** 8–10 horas

---

## Índice de contenidos

1. [Clases y objetos](#1-clases-y-objetos)
2. [Atributos y métodos](#2-atributos-y-métodos)
3. [Herencia y polimorfismo](#3-herencia-y-polimorfismo)
4. [Encapsulamiento y propiedades](#4-encapsulamiento-y-propiedades)
5. [Métodos mágicos](#5-métodos-mágicos-dunder-methods)
6. [Clases abstractas e interfaces](#6-clases-abstractas-e-interfaces)
7. [Dataclasses](#7-dataclasses)
8. [Metaclases](#8-metaclases)
9. [Ejemplo integrador](#9-ejemplo-integrador)
10. [Buenas prácticas](#10-buenas-prácticas)
11. [Recursos](#11-recursos)
12. [Mini resumen final](#12-mini-resumen-final)

---

## 1. Clases y objetos

### 1.1. ¿Por qué agrupar datos y comportamiento?

En los archivos anteriores usabas datos (variables, diccionarios) y funciones por separado. Pero cuando un programa crece, conviene **agrupar** los datos y las operaciones que van juntos. Por ejemplo, para manejar "cuentas bancarias" no quieres un diccionario suelto y funciones dispersas: quieres un objeto "cuenta" que **sepa** su saldo y **sepa** depositar y retirar.

La **Programación Orientada a Objetos (POO)** es el paradigma que organiza el programa en **objetos** que combinan **datos** (atributos) y **comportamiento** (métodos).

**Analogía:** un objeto es como un **electrodoméstico**. Tiene un estado interno (por ejemplo, la temperatura de un horno) y unos botones que operan sobre ese estado. No manipulas los cables internos; usas su interfaz (los botones). Del mismo modo, un objeto expone métodos y guarda sus datos dentro.

### 1.2. Clase vs. objeto

- Una **clase** es la **plantilla** o el **plano**: define qué atributos y métodos tendrán sus instancias.
- Un **objeto** (o **instancia**) es un **ejemplar concreto** creado a partir de la clase.

**Analogía:** la clase es el plano de una casa; los objetos son las casas construidas con ese plano. Puedes construir muchas casas distintas (con distintos colores) a partir del mismo plano.

### 1.3. Tu primera clase

```python
class Perro:
    """Representa un perro."""

    def __init__(self, nombre: str, edad: int) -> None:
        """Inicializa el perro con nombre y edad."""
        self.nombre = nombre
        self.edad = edad

    def ladrar(self) -> str:
        """Devuelve un ladrido."""
        return f"{self.nombre} dice: ¡Guau!"


mi_perro = Perro("Rex", 3)
print(mi_perro.nombre)
print(mi_perro.ladrar())
```

Salida esperada:

```text
Rex
Rex dice: ¡Guau!
```

**Explicación línea por línea:**
- `class Perro:` define la clase. Por convención, el nombre va en **PascalCase**.
- `__init__` es el **constructor**: se ejecuta automáticamente al crear un objeto.
- `self` es una referencia **al objeto que se está creando/ usando**. Siempre es el primer parámetro de los métodos.
- `self.nombre = nombre` guarda el dato en el objeto (crea un **atributo de instancia**).
- `mi_perro = Perro("Rex", 3)` crea una instancia; `"Rex"` y `3` llegan a `__init__`.
- `mi_perro.ladrar()` llama al método; dentro, `self` es `mi_perro`.

### 1.4. ¿Qué es `self` exactamente?

`self` es el nombre convencional del primer parámetro de los métodos. **No es una palabra reservada**, pero úsalo siempre por convención. Representa "este objeto concreto".

```python
perro1 = Perro("Rex", 3)
perro2 = Perro("Luna", 1)

print(perro1.nombre)   # Rex
print(perro2.nombre)   # Luna
print(perro1.ladrar()) # Rex dice...
print(perro2.ladrar()) # Luna dice...
```

Cada objeto tiene sus **propios** atributos: `perro1.nombre` y `perro2.nombre` son independientes. El método `ladrar` es el mismo código, pero `self` apunta a un objeto distinto en cada llamada.

### 1.5. Clase vs. instancia

```python
print(type(mi_perro))
print(isinstance(mi_perro, Perro))

otro = Perro("Luna", 1)
print(mi_perro is otro)
```

Salida esperada:

```text
<class '__main__.Perro'>
True
False
```

**Explicación línea por línea:**
- `type(mi_perro)` muestra que es una instancia de `Perro`.
- `isinstance(obj, Clase)` comprueba si un objeto es de una clase (o subclase).
- `mi_perro is otro` es `False`: son objetos **distintos**, aunque de la misma clase.

### 1.6. Ejemplos progresivos

**Ejemplo 1 — Una clase simple.**

```python
class Rectangulo:
    """Representa un rectángulo."""

    def __init__(self, base: float, altura: float) -> None:
        self.base = base
        self.altura = altura

    def area(self) -> float:
        return self.base * self.altura

    def perimetro(self) -> float:
        return 2 * (self.base + self.altura)


r = Rectangulo(4, 5)
print(r.area())
print(r.perimetro())
```

Salida esperada:

```text
20
18
```

**Ejemplo 2 — Objetos que cambian de estado.**

```python
class Cuenta:
    """Cuenta con saldo."""

    def __init__(self, saldo_inicial: float = 0.0) -> None:
        self.saldo = saldo_inicial

    def depositar(self, monto: float) -> None:
        self.saldo += monto

    def retirar(self, monto: float) -> None:
        if monto > self.saldo:
            print("Fondos insuficientes")
            return
        self.saldo -= monto


cuenta = Cuenta(100)
cuenta.depositar(50)
cuenta.retirar(30)
print(cuenta.saldo)
```

Salida esperada:

```text
120
```

**Explicación línea por línea:**
- `self.saldo` es el **estado** del objeto y cambia con las operaciones.
- `depositar` y `retirar` **mutan** ese estado.
- La validación en `retirar` protege la integridad del objeto.

**Ejemplo 3 — Varios objetos independientes.**

```python
a = Cuenta(100)
b = Cuenta(500)
a.depositar(50)
print(a.saldo, b.saldo)
```

Salida esperada:

```text
150 500
```

Cada objeto tiene su propio `saldo`. No se mezclan.

### 1.7. Errores comunes

**Error 1 — Olvidar `self` en la definición.**

```python
class Perro:
    def ladrar():        # ❌ falta self
        return "Guau"
```

```text
TypeError: Perro.ladrar() takes 0 positional arguments but 1 was given
```

*Solución:* `def ladrar(self):`.

**Error 2 — Olvidar `self.` al asignar un atributo.**

```python
class Perro:
    def __init__(self, nombre):
        nombre = nombre   # ❌ crea una variable local, no un atributo
```

*Solución:* `self.nombre = nombre`.

**Error 3 — Definir atributos fuera de `__init__`.**

Aunque Python lo permite, dificulta saber qué atributos tiene un objeto. *Solución:* inicializa todos los atributos en `__init__`.

### 1.8. Checkpoint de comprensión

1. ¿Qué diferencia hay entre una clase y un objeto?
2. ¿Qué es `self` y por qué todos los métodos lo llevan?
3. ¿Por qué cada instancia tiene sus propios atributos?

### 1.9. Ejercicio propuesto

**Ejercicio 1.1.** Crea una clase `Rectangulo` con `base` y `altura` y un método `area()`.

**Pista:** el área es `base * altura`; inicializa los atributos en `__init__`.

---

## 2. Atributos y métodos

### 2.1. Atributos de instancia vs. de clase

Un **atributo de instancia** pertenece a cada objeto. Un **atributo de clase** se comparte entre **todas** las instancias.

```python
class Circulo:
    """Círculo con atributo de clase PI."""

    PI = 3.14159   # atributo de clase

    def __init__(self, radio: float) -> None:
        self.radio = radio   # atributo de instancia

    def area(self) -> float:
        return self.PI * self.radio ** 2


c1 = Circulo(2)
c2 = Circulo(5)
print(c1.area())
print(c2.area())
print(Circulo.PI)
```

Salida esperada:

```text
12.56636
78.53975
3.14159
```

**Explicación línea por línea:**
- `PI = 3.14159` es de clase: existe **una sola vez**, compartida por todas las instancias.
- `self.radio` es de instancia: cada círculo tiene el suyo.
- `Circulo.PI` accede al atributo de clase directamente.

### 2.2. El peligro de los atributos de clase mutables

```python
class A:
    valores = []   # ¡compartido por TODAS las instancias!


a, b = A(), A()
a.valores.append(1)
print(b.valores)
```

Salida esperada:

```text
[1]
```

**¿Por qué?** Porque `valores` es una lista de clase, y `a` y `b` comparten **la misma** lista. *Solución:* inicializa los mutables en `__init__` (`self.valores = []`).

### 2.3. Métodos de instancia, de clase y estáticos

Python tiene tres tipos de métodos:

| Tipo | Decorador | Primer parámetro | Uso |
|---|---|---|---|
| De instancia | (ninguno) | `self` | Operar sobre el objeto |
| De clase | `@classmethod` | `cls` | Constructores alternativos |
| Estático | `@staticmethod` | (ninguno) | Utilidades relacionadas |

```python
class Circulo:
    PI = 3.14159

    def __init__(self, radio: float) -> None:
        self.radio = radio

    def area(self) -> float:
        """Método de instancia."""
        return self.PI * self.radio ** 2

    @classmethod
    def desde_diametro(cls, diametro: float) -> "Circulo":
        """Constructor alternativo a partir del diámetro."""
        return cls(diametro / 2)

    @staticmethod
    def es_valido(radio: float) -> bool:
        """Comprueba si un radio es válido (no depende de la instancia)."""
        return radio > 0


c = Circulo.desde_diametro(10)
print(c.radio)
print(Circulo.es_valido(-1))
```

Salida esperada:

```text
5.0
False
```

**Explicación línea por línea:**
- `@classmethod` recibe `cls` (la clase) en lugar de `self`. Se usa para crear instancias de formas alternativas: `desde_diametro` construye un círculo a partir del diámetro.
- `@staticmethod` no recibe ni `self` ni `cls`: es una función normal que vive dentro de la clase por organización.
- `cls(diametro / 2)` equivale a `Circulo(diametro / 2)`.

### 2.4. Errores comunes

**Error 1 — Atributo de clase mutable compartido.** Como viste arriba.

**Error 2 — Confundir `@classmethod` con `@staticmethod`.**

`classmethod` recibe la clase (útil para herencia); `staticmethod` no recibe nada.

**Error 3 — Llamar a un método de instancia desde la clase sin instancia.**

```python
Circulo.area()   # ❌ falta el argumento self
```

*Solución:* crea una instancia o usa un `@staticmethod` si no necesitas el estado.

### 2.5. Checkpoint de comprensión

1. ¿Qué diferencia hay entre un atributo de clase y uno de instancia?
2. ¿Para qué sirve `@classmethod`?
3. ¿Cuándo usarías `@staticmethod`?

### 2.6. Ejercicio propuesto

**Ejercicio 2.1.** Añade a `Rectangulo` un `@classmethod desde_cuadrado(lado)`.

**Pista:** un cuadrado es un rectángulo con base = altura = lado.

---

## 3. Herencia y polimorfismo

### 3.1. ¿Por qué heredar?

Imagina que tienes clases `Perro`, `Gato` y `Vaca`. Todas comparten "tener nombre" y "ser un animal", pero cada una hace un sonido distinto. La **herencia** permite que una clase **hija** reutilice lo común de una clase **padre** y solo defina lo diferente.

**Analogía:** la herencia es como la genética: un hijo hereda rasgos de sus padres (color de ojos) y añade los suyos propios. No empieza de cero.

La relación de herencia se lee "**es un**": un perro **es un** animal.

### 3.2. Herencia básica

```python
class Animal:
    """Clase base."""

    def __init__(self, nombre: str) -> None:
        self.nombre = nombre

    def hablar(self) -> str:
        """Sonido genérico."""
        return "..."


class Perro(Animal):
    """Perro que hereda de Animal."""

    def hablar(self) -> str:
        """Sobrescribe el método de la base."""
        return "¡Guau!"


class Gato(Animal):
    """Gato que hereda de Animal."""

    def hablar(self) -> str:
        return "¡Miau!"


animales = [Perro("Rex"), Gato("Michi")]
for animal in animales:
    print(f"{animal.nombre}: {animal.hablar()}")
```

Salida esperada:

```text
Rex: ¡Guau!
Michi: ¡Miau!
```

**Explicación línea por línea:**
- `class Perro(Animal):` indica que `Perro` **hereda** de `Animal`. Entre paréntesis va la clase padre.
- `Perro` **no redefine** `__init__`: lo hereda tal cual, así que `Perro("Rex")` ejecuta el `__init__` de `Animal`.
- `Perro` **sobrescribe** `hablar` con su propia versión.
- En el bucle, aunque `animales` contiene objetos de distintas clases, todos responden a `.hablar()` y `.nombre`.

### 3.3. Polimorfismo

El **polimorfismo** ("muchas formas") es la capacidad de tratar objetos de distintas clases a través de una **interfaz común**. En el ejemplo anterior, el bucle llama a `animal.hablar()` sin saber si es un perro o un gato: cada objeto responde **a su manera**.

**¿Por qué es potente?** Porque puedes añadir una clase `Vaca` sin tocar el bucle. El código que usa la interfaz no necesita conocer todas las implementaciones.

### 3.4. `super()`: llamar al padre

Cuando una clase hija necesita extender el comportamiento del padre (no reemplazarlo), usa `super()`:

```python
class Vehiculo:
    def __init__(self, marca: str) -> None:
        self.marca = marca


class Coche(Vehiculo):
    def __init__(self, marca: str, puertas: int) -> None:
        super().__init__(marca)   # llama al constructor del padre
        self.puertas = puertas


c = Coche("Toyota", 5)
print(c.marca, c.puertas)
```

Salida esperada:

```text
Toyota 5
```

**Explicación línea por línea:**
- `super().__init__(marca)` ejecuta el `__init__` de `Vehiculo`, que asigna `self.marca`.
- Luego `Coche` añade su propio atributo `puertas`.
- Sin `super()`, `marca` nunca se asignaría.

### 3.5. Herencia múltiple y el MRO

Una clase puede heredar de **varias** clases. Cuando hay métodos con el mismo nombre, Python decide cuál usar según el **MRO** (*Method Resolution Order*):

```python
class A:
    def quien(self):
        return "A"


class B(A):
    def quien(self):
        return "B"


class C(A):
    def quien(self):
        return "C"


class D(B, C):
    pass


print(D().quien())
print([c.__name__ for c in D.__mro__])
```

Salida esperada:

```text
B
['D', 'B', 'C', 'A', 'object']
```

**Explicación línea por línea:**
- `D` hereda de `B` y `C`.
- El MRO es `D → B → C → A → object`: Python busca `quien` primero en `D`, luego en `B`, y lo encuentra ahí.
- `D.__mro__` muestra el orden de búsqueda.

**Consejo:** la herencia múltiple es potente pero complicada. Úsala con moderación.

### 3.6. Composición vs. herencia

No siempre la herencia es la respuesta. Si la relación no es "es un", usa **composición** ("tiene un"):

```python
# Herencia: un Coche ES UN Vehiculo
class Coche(Vehiculo):
    ...

# Composición: un Coche TIENE UN Motor
class Coche:
    def __init__(self):
        self.motor = Motor()
```

**Regla:** prefiere composición cuando la relación no sea claramente "es un". Es más flexible y evita jerarquías frágiles.

### 3.7. Errores comunes

**Error 1 — No llamar a `super().__init__()`.**

*Síntoma:* los atributos del padre no se inicializan y obtienes `AttributeError` al usarlos.

**Error 2 — Jerarquías de herencia demasiado profundas.**

*Síntoma:* cuesta seguir de dónde viene cada método. *Solución:* prefiere composición y jerarquías planas.

**Error 3 — Usar herencia para reutilizar código sin relación "es un".**

*Solución:* usa composición.

### 3.8. Checkpoint de comprensión

1. ¿Cómo se lee la relación de herencia?
2. ¿Qué es el polimorfismo y por qué es útil?
3. ¿Cuándo usarías composición en lugar de herencia?
4. ¿Qué es el MRO?

### 3.9. Ejercicio propuesto

**Ejercicio 3.1.** Crea una jerarquía `Figura` → `Circulo`, `Cuadrado`, cada una con `area()`.

**Pista:** sobrescribe `area()` en cada subclase.

---

## 4. Encapsulamiento y propiedades

### 4.1. ¿Qué es encapsular?

**Encapsular** significa **ocultar los detalles internos** de un objeto y exponer solo lo necesario. Así, quien usa el objeto no depende de su implementación: puedes cambiarla sin romper a nadie.

Python no tiene modificadores de acceso reales como `private` o `protected`, pero usa **convenciones**:

| Convención | Significado |
|---|---|
| `nombre` | Público (uso libre) |
| `_nombre` | "Protegido": uso interno, por convención no lo toques desde fuera |
| `__nombre` | Privado: Python aplica *name mangling* |

### 4.2. Propiedades con `@property`

Las **propiedades** permiten ejecutar lógica al **leer** o **asignar** un atributo, manteniendo una interfaz limpia (`obj.celsius` en lugar de `obj.get_celsius()`).

```python
class Temperatura:
    """Temperatura en grados Celsius."""

    def __init__(self, celsius: float) -> None:
        self._celsius = celsius

    @property
    def celsius(self) -> float:
        """Getter: se ejecuta al leer .celsius."""
        return self._celsius

    @celsius.setter
    def celsius(self, valor: float) -> None:
        """Setter: se ejecuta al asignar .celsius, con validación."""
        if valor < -273.15:
            raise ValueError("Por debajo del cero absoluto")
        self._celsius = valor

    @property
    def fahrenheit(self) -> float:
        """Propiedad calculada (solo lectura)."""
        return self._celsius * 9 / 5 + 32


t = Temperatura(25)
print(t.fahrenheit)
t.celsius = 30
print(t.fahrenheit)
```

Salida esperada:

```text
77.0
86.0
```

**Explicación línea por línea:**
- `self._celsius` guarda el valor interno (con guion bajo, "protegido").
- `@property` convierte el método `celsius` en un **atributo de solo lectura**: `t.celsius` llama al getter.
- `@celsius.setter` permite asignar `t.celsius = 30` ejecutando la validación.
- `fahrenheit` es una propiedad **calculada**: no se guarda, se calcula al leerla.

**¿Por qué no usar simplemente atributos públicos?** Porque a veces necesitas **validar** (no permitir temperaturas imposibles) o **calcular** (convertir unidades). Las propiedades dan esa flexibilidad sin cambiar la forma de usarlo.

### 4.3. Name mangling

Los atributos con **doble** guion bajo sufren *name mangling*: Python los renombra internamente para dificultar el acceso desde fuera.

```python
class Cuenta:
    def __init__(self) -> None:
        self.__saldo = 0   # se renombra a _Cuenta__saldo


c = Cuenta()
print(c._Cuenta__saldo)   # accesible, pero no se debe usar
# print(c.__saldo)        # ❌ AttributeError
```

**Explicación línea por línea:**
- `__saldo` se transforma internamente en `_Cuenta__saldo`.
- Acceder con el nombre original falla; con el nombre "mangled" funciona, pero es una señal de que estás rompiendo la encapsulación.

**¿Para qué sirve?** Para evitar **colisiones** en herencia más que para seguridad real.

### 4.4. Errores comunes

**Error 1 — Crear getters/setters para todo.**

En Python, expón atributos públicos salvo que necesites validación o cálculo. No escribas `get_x`/`set_x` por costumbre.

**Error 2 — Usar `__` sin entender el name mangling.**

Puede sorprender al heredar. Muchas veces `_` (un guion) es suficiente.

**Error 3 — Validar en el setter pero no en el constructor.**

```python
t = Temperatura(-500)   # si el constructor no valida, se cuela
```

*Solución:* haz que el constructor también pase por la validación, o valida en ambos sitios.

### 4.5. Checkpoint de comprensión

1. ¿Qué significa encapsular y por qué es útil?
2. ¿Qué hace `@property`?
3. ¿Qué diferencia hay entre `_nombre` y `__nombre`?

### 4.6. Ejercicio propuesto

**Ejercicio 4.1.** Crea una clase `CuentaBancaria` con `saldo` de solo lectura y métodos `depositar`/`retirar` con validación.

**Pista:** usa `@property` para `saldo` (sin setter).

---

## 5. Métodos mágicos (dunder methods)

### 5.1. ¿Qué son los métodos mágicos?

Los **métodos mágicos** (o *dunder*, por *double underscore*) permiten que tus objetos se comporten como los tipos integrados. ¿Quieres poder sumar dos objetos con `+`? Define `__add__`. ¿Quieres que `len(obj)` funcione? Define `__len__`. Son el mecanismo que hace que Python sea tan cohesivo.

**Analogía:** los métodos mágicos son los "enchufes" que conectan tu objeto con la sintaxis del lenguaje. Al implementarlos, tu objeto "habla" el mismo idioma que los tipos de Python.

### 5.2. La tabla de los principales

| Método | Se activa con |
|---|---|
| `__init__` | Construcción |
| `__repr__` | `repr(obj)`, consola |
| `__str__` | `str(obj)`, `print()` |
| `__len__` | `len(obj)` |
| `__eq__` | `==` |
| `__lt__` | `<` |
| `__add__` | `+` |
| `__getitem__` | `obj[i]` |
| `__iter__` | `for x in obj` |
| `__call__` | `obj()` |

### 5.3. Un ejemplo completo: la clase Vector

```python
class Vector:
    """Vector bidimensional."""

    def __init__(self, x: float, y: float) -> None:
        self.x = x
        self.y = y

    def __repr__(self) -> str:
        return f"Vector({self.x}, {self.y})"

    def __add__(self, otro: "Vector") -> "Vector":
        return Vector(self.x + otro.x, self.y + otro.y)

    def __eq__(self, otro: object) -> bool:
        if not isinstance(otro, Vector):
            return NotImplemented
        return self.x == otro.x and self.y == otro.y

    def __abs__(self) -> float:
        return (self.x ** 2 + self.y ** 2) ** 0.5


v1 = Vector(1, 2)
v2 = Vector(3, 4)
print(v1 + v2)
print(v1 == Vector(1, 2))
print(abs(v2))
```

Salida esperada:

```text
Vector(4, 6)
True
5.0
```

**Explicación línea por línea:**
- `__repr__` define cómo se representa el objeto (por ejemplo, al imprimirlo en la consola). Debe ser inequívoco.
- `__add__` permite `v1 + v2`; devuelve un **nuevo** `Vector`.
- `__eq__` permite comparar con `==`. Devolver `NotImplemented` (no `False`) cuando el otro objeto no es un `Vector` permite que Python intente la comparación por el otro lado.
- `__abs__` permite `abs(v2)`: la longitud del vector (teorema de Pitágoras).

### 5.4. `__str__` vs `__repr__`

Es importante distinguirlos:

- `__repr__`: representación **inequívoca**, pensada para **depuración**. Debería permitir reconstruir el objeto.
- `__str__`: representación **legible**, pensada para el **usuario final**.

```python
class Producto:
    def __init__(self, nombre, precio):
        self.nombre = nombre
        self.precio = precio

    def __repr__(self):
        return f"Producto({self.nombre!r}, {self.precio!r})"

    def __str__(self):
        return f"{self.nombre}: {self.precio} €"


p = Producto("Teclado", 49.99)
print(repr(p))
print(str(p))
```

Salida esperada:

```text
Producto('Teclado', 49.99)
Teclado: 49.99 €
```

**Regla:** si solo defines uno, define `__repr__`.

### 5.5. Haciendo tu objeto iterable e indexable

```python
class Mazo:
    """Mazo de cartas."""

    def __init__(self, cartas: list[str]) -> None:
        self.cartas = cartas

    def __len__(self) -> int:
        return len(self.cartas)

    def __getitem__(self, indice: int) -> str:
        return self.cartas[indice]


mazo = Mazo(["as", "rey", "reina"])
print(len(mazo))
print(mazo[0])

for carta in mazo:   # funciona gracias a __getitem__
    print(carta)
```

Salida esperada:

```text
3
as
as
rey
reina
```

**Explicación línea por línea:**
- `__len__` hace que `len(mazo)` funcione.
- `__getitem__` permite `mazo[0]` y, de paso, hace el objeto iterable con `for`.

### 5.6. Errores comunes

**Error 1 — Definir `__eq__` sin `__hash__`.**

Si haces un objeto igualable y quieres usarlo en `set` o como clave de diccionario, define también `__hash__`. Al definir `__eq__`, Python pone `__hash__ = None` por defecto y el objeto deja de ser hashable.

**Error 2 — `__repr__` que devuelve texto no reproducible.**

*Solución:* que `repr(obj)` se parezca a la llamada que lo crea.

**Error 3 — Confundir `__str__` y `__repr__`.**

*Solución:* `__repr__` para desarrolladores; `__str__` para usuarios.

### 5.7. Checkpoint de comprensión

1. ¿Qué método se activa con `len(obj)`?
2. ¿Qué diferencia hay entre `__str__` y `__repr__`?
3. ¿Por qué `__eq__` y `__hash__` van juntos?

### 5.8. Ejercicio propuesto

**Ejercicio 5.1.** Implementa `__len__` y `__getitem__` en una clase `Mazo` de cartas.

**Pista:** delega en una lista interna.

---

## 6. Clases abstractas e interfaces

### 6.1. ¿Qué es una clase abstracta?

Una **clase abstracta** define **métodos que las subclases deben implementar**, pero no los implementa ella misma. Sirve para establecer un **contrato**: "toda forma debe saber calcular su área".

**Analogía:** un contrato de trabajo. No dice *cómo* harás el trabajo, pero exige que lo hagas. Cada empleado lo cumple a su manera.

### 6.2. El módulo `abc`

Python usa el módulo `abc` (*Abstract Base Classes*):

```python
from abc import ABC, abstractmethod


class Forma(ABC):
    """Clase abstracta para figuras geométricas."""

    @abstractmethod
    def area(self) -> float:
        """Toda forma debe calcular su área."""

    @abstractmethod
    def perimetro(self) -> float:
        """Toda forma debe calcular su perímetro."""


class Circulo(Forma):
    def __init__(self, radio: float) -> None:
        self.radio = radio

    def area(self) -> float:
        return 3.14159 * self.radio ** 2

    def perimetro(self) -> float:
        return 2 * 3.14159 * self.radio


# forma = Forma()   # ❌ TypeError: Can't instantiate abstract class
c = Circulo(2)
print(round(c.area(), 2))
print(round(c.perimetro(), 2))
```

Salida esperada:

```text
12.57
12.57
```

**Explicación línea por línea:**
- `class Forma(ABC):` hereda de `ABC`, lo que la convierte en abstracta.
- `@abstractmethod` marca métodos que **deben** implementarse en las subclases.
- `Forma()` falla con `TypeError`: no puedes instanciar una clase abstracta.
- `Circulo` implementa los dos métodos y sí se puede instanciar.

### 6.3. ¿Para qué sirven?

- **Documentan el contrato:** queda claro qué debe implementar cada subclase.
- **Detectan errores temprano:** si olvidas implementar un método, falla al instanciar.
- **Permiten polimorfismo seguro:** todo objeto `Forma` tiene `area()` y `perimetro()`.

### 6.4. Duck typing vs. ABC

Python favorece el **duck typing** ("si camina como pato y grazna como pato, es un pato"): si un objeto tiene los métodos que necesitas, sirve, sin importar su clase. Las ABC son útiles cuando quieres **forzar** un contrato explícito.

```python
# Duck typing: funciona con cualquier objeto que tenga .area()
def mostrar_area(forma):
    print(forma.area())
```

### 6.5. Errores comunes

**Error 1 — Intentar instanciar una clase abstracta.**

```text
TypeError: Can't instantiate abstract class Forma with abstract methods area, perimetro
```

**Error 2 — Olvidar implementar todos los métodos abstractos.**

*Síntoma:* `TypeError` al instanciar la subclase. *Solución:* implementa todos.

### 6.6. Checkpoint de comprensión

1. ¿Qué es una clase abstracta y para qué sirve?
2. ¿Qué pasa si una subclase no implementa un método abstracto?
3. ¿Qué diferencia hay entre duck typing y usar ABC?

### 6.7. Ejercicio propuesto

**Ejercicio 6.1.** Crea una ABC `Notificador` con `enviar(mensaje)` e implementa `EmailNotificador` y `SMSNotificador`.

**Pista:** usa `@abstractmethod`.

---

## 7. Dataclasses

### 7.1. El problema que resuelven

Muchas clases solo sirven para **almacenar datos**: un producto con nombre y precio, un punto con x e y. Escribir `__init__`, `__repr__` y `__eq__` a mano es tedioso y repetitivo. Las **dataclasses** lo generan automáticamente.

### 7.2. Ejemplo

```python
from dataclasses import dataclass, field


@dataclass
class Producto:
    """Producto con nombre, precio y etiquetas."""

    nombre: str
    precio: float
    etiquetas: list[str] = field(default_factory=list)

    def precio_con_iva(self, iva: float = 0.21) -> float:
        return self.precio * (1 + iva)


p = Producto("Teclado", 49.99, ["hardware"])
print(p)
print(p.precio_con_iva())
```

Salida esperada:

```text
Producto(nombre='Teclado', precio=49.99, etiquetas=['hardware'])
60.4879
```

**Explicación línea por línea:**
- `@dataclass` genera automáticamente `__init__`, `__repr__` y `__eq__` a partir de los campos.
- Los campos se declaran con anotaciones de tipo: `nombre: str`, `precio: float`.
- `field(default_factory=list)` crea una lista nueva para cada instancia (¡evita el problema del mutable compartido!).
- Los métodos normales conviven perfectamente con `@dataclass`.

### 7.3. Opciones útiles

```python
from dataclasses import dataclass


@dataclass(frozen=True, order=True)
class Punto:
    """Punto inmutable y ordenable."""

    x: int
    y: int


p1 = Punto(1, 2)
p2 = Punto(3, 4)
print(p1 < p2)
# p1.x = 10   # ❌ FrozenInstanceError
```

Salida esperada:

```text
True
```

**Explicación línea por línea:**
- `frozen=True` hace la instancia **inmutable**: no puedes cambiar sus atributos.
- `order=True` genera automáticamente `__lt__`, `__le__`, etc., para poder ordenar.

### 7.4. Errores comunes

**Error 1 — Usar un mutable por defecto sin `field(default_factory=...)`.**

```python
@dataclass
class Mal:
    items: list = []   # ❌ ValueError: mutable default
```

*Solución:* `items: list = field(default_factory=list)`.

**Error 2 — Intentar mutar una dataclass `frozen=True`.**

```text
FrozenInstanceError: cannot assign to field 'x'
```

### 7.5. Checkpoint de comprensión

1. ¿Qué genera automáticamente `@dataclass`?
2. ¿Por qué se usa `field(default_factory=list)`?
3. ¿Qué hace `frozen=True`?

### 7.6. Ejercicio propuesto

**Ejercicio 7.1.** Convierte la clase `Rectangulo` del ejercicio 1.1 en una dataclass con `area()`.

**Pista:** los métodos normales conviven con `@dataclass`.

---

## 8. Metaclases

### 8.1. ¿Qué es una metaclase?

Una **metaclase** es "la clase de una clase". Así como un objeto se crea a partir de una clase, una clase se crea a partir de una **metaclase**. La metaclase por defecto es `type`.

⚠️ **Tema avanzado:** en el 99 % de los casos no necesitas metaclases. Se usan en frameworks (como el ORM de Django) para interceptar la creación de clases. Antes de usar una, considera alternativas como `__init_subclass__` o decoradores de clase.

### 8.2. Crear una clase dinámicamente con `type`

```python
Persona = type("Persona", (), {"saludo": lambda self: "Hola"})
p = Persona()
print(p.saludo())
```

Salida esperada:

```text
Hola
```

**Explicación línea por línea:**
- `type(nombre, bases, diccionario)` crea una clase al vuelo.
- `"Persona"` es el nombre; `()` las clases base; el diccionario, los atributos/métodos.
- Esto demuestra que las clases son objetos creados por `type`.

### 8.3. Una metaclase personalizada

```python
class Meta(type):
    """Metaclase que registra las clases creadas."""

    registro = []

    def __new__(mcs, nombre, bases, namespace):
        cls = super().__new__(mcs, nombre, bases, namespace)
        mcs.registro.append(nombre)
        return cls


class A(metaclass=Meta):
    pass


class B(metaclass=Meta):
    pass


print(Meta.registro)
```

Salida esperada:

```text
['A', 'B']
```

**Explicación línea por línea:**
- `Meta` hereda de `type`.
- `__new__` intercepta la creación de cada clase que use `metaclass=Meta`.
- Cada vez que se define una clase, se añade su nombre al registro.

### 8.4. `__init_subclass__`: la alternativa moderna

La mayoría de las veces no necesitas una metaclase; `__init_subclass__` es más simple:

```python
class Base:
    subclases = []

    def __init_subclass__(cls, **kwargs):
        super().__init_subclass__(**kwargs)
        Base.subclases.append(cls.__name__)


class X(Base):
    pass


class Y(Base):
    pass


print(Base.subclases)
```

Salida esperada:

```text
['X', 'Y']
```

**Explicación línea por línea:**
- `__init_subclass__` se ejecuta automáticamente cada vez que se define una subclase de `Base`.
- Es más legible que una metaclase para este tipo de tareas.

### 8.5. Errores comunes

**Error 1 — Usar metaclases cuando basta un decorador o `__init_subclass__`.**

**Error 2 — Olvidar que las metaclases se heredan.** Pueden causar conflictos inesperados.

### 8.6. Checkpoint de comprensión

1. ¿Qué es una metaclase?
2. ¿Por qué rara vez las necesitas?
3. ¿Qué alternativa moderna existe para registrar subclases?

### 8.7. Ejercicio propuesto

**Ejercicio 8.1.** Usa `__init_subclass__` para que toda subclase registre automáticamente su nombre en una lista.

**Pista:** imita el ejemplo 8.4.

---

## 9. Ejemplo integrador

Construiremos un **sistema de inventario** que combina ABC, herencia, `super()`, dataclasses, `__len__`, `__iter__` y polimorfismo.

```python
"""Inventario de productos con herencia y ABC."""

from abc import ABC, abstractmethod
from dataclasses import dataclass


class Item(ABC):
    """Item abstracto del inventario."""

    def __init__(self, nombre: str, precio: float) -> None:
        self.nombre = nombre
        self.precio = precio

    @abstractmethod
    def descripcion(self) -> str:
        """Descripción del item."""


class Electronico(Item):
    def __init__(self, nombre: str, precio: float, garantia: int) -> None:
        super().__init__(nombre, precio)
        self.garantia = garantia

    def descripcion(self) -> str:
        return f"{self.nombre} (garantía {self.garantia} meses)"


@dataclass
class Libro(Item):
    nombre: str
    precio: float
    autor: str = ""

    def descripcion(self) -> str:
        return f"{self.nombre} de {self.autor}"


class Inventario:
    """Colección de items."""

    def __init__(self) -> None:
        self._items: list[Item] = []

    def agregar(self, item: Item) -> None:
        self._items.append(item)

    def __len__(self) -> int:
        return len(self._items)

    def __iter__(self):
        return iter(self._items)

    def total(self) -> float:
        return sum(item.precio for item in self._items)


inv = Inventario()
inv.agregar(Electronico("Laptop", 1200, 24))
inv.agregar(Libro("Python", 39.9, "Guido"))
for item in inv:
    print("-", item.descripcion())
print("Items:", len(inv), "| Total:", inv.total())
```

Salida esperada:

```text
- Laptop (garantía 24 meses)
- Python de Guido
Items: 2 | Total: 1239.9
```

**Explicación línea por línea:**
- `Item` es una ABC con el método abstracto `descripcion`.
- `Electronico` hereda de `Item`, llama a `super().__init__` y sobrescribe `descripcion`.
- `Libro` es una dataclass que también hereda de `Item` (las dataclasses pueden heredar de ABC).
- `Inventario` implementa `__len__` (para `len`) y `__iter__` (para `for`), y usa polimorfismo en `total()` y en el bucle.
- El bucle llama a `descripcion()` sin saber si el item es un electrónico o un libro.

**Conceptos integrados:** ABC, herencia, `super()`, dataclass, `__len__`, `__iter__`, polimorfismo.

---

## 10. Buenas prácticas

1. **Una clase, una responsabilidad.**
2. **Prefiere composición sobre herencia** cuando no exista relación "es un".
3. **Define `__repr__`** en clases de datos.
4. **Usa dataclasses** para clases que solo almacenan datos.
5. **Valida en setters**, no expongas atributos mutables sin control si importa la integridad.
6. **Evita metaclases** salvo en frameworks.
7. **Documenta la clase y sus métodos** con docstrings.
8. **Inicializa todos los atributos en `__init__`.**
9. **No uses atributos de clase mutables.**

---

## 11. Recursos

- **Clases oficial:** <https://docs.python.org/3/tutorial/classes.html>
- **`dataclasses`:** <https://docs.python.org/3/library/dataclasses.html>
- **`abc`:** <https://docs.python.org/3/library/abc.html>
- **Libro:** *Fluent Python*, capítulos 11–13.
- **Libro:** *Python Object-Oriented Programming*, Steven Lott.

---

## 12. Mini resumen final

- Las **clases** son plantillas; los **objetos**, instancias.
- `self` referencia la instancia; `@classmethod` y `@staticmethod` definen métodos especiales.
- La **herencia** reutiliza y extiende; el **polimorfismo** usa una interfaz común.
- Las **propiedades** encapsulan lógica de acceso.
- Los **métodos mágicos** integran tus objetos con la sintaxis del lenguaje.
- Las **ABC** definen contratos; las **dataclasses** reducen código repetitivo.
- Las **metaclases** controlan la creación de clases (uso avanzado).

---

### 🎯 Retos opcionales

1. **Reto 1:** Implementa una clase `Fraccion` con `__add__`, `__eq__` y simplificación.
2. **Reto 2:** Crea una jerarquía de `Empleado` → `EmpleadoFijo`, `EmpleadoPorHoras` con cálculo de salario polimórfico.
3. **Reto 3:** Usa `__init_subclass__` para validar que cada subclase defina un atributo `codigo`.

**Anterior:** [04_funciones.md](04_funciones.md) · **Siguiente:** [06_errores_excepciones.md](06_errores_excepciones.md)
