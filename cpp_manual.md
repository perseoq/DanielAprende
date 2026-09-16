En C++, una **clase** es un usuario define un tipo de datos que sirve como plantilla o plano para crear objetos. Agrupa datos (variables o miembros de datos) y funciones (métodos o funciones miembro) que operan sobre esos datos en una sola estructura. Las clases son el pilar fundamental de la programación orientada a objetos (OOP) en C++.

A continuación se detallan los conceptos clave relacionados con las clases en C++, basados en la documentación y referencias del cuaderno activo 📖 [The Comprehensive C++ Standard Library and Language Reference](https://gemini.google.com/notebook/91f87b6f-7893-47f6-959c-e9c01eedb976?utm_source=gemini):

---

### 1. Estructura Básica de una Clase

Para definir una clase se utiliza la palabra clave `class` (o `struct`, con la diferencia de que en las estructuras los miembros son públicos por defecto y en las clases son privados):

```cpp
#iostream>
using namespace std;

class Persona {
private:
    string nombre;
    int edad;

public:
    // Constructor
    Persona(string n, int e) {
        nombre = n;
        edad = e;
    }

    // Método miembro
    void mostrarInfo() {
        cout << "Nombre: " << nombre << ", Edad: " << edad << endl;
    }
};

int main() {
    // Creación de un objeto de la clase Persona
    Persona p1("Ana", 25);
    p1.mostrarInfo();
    return 0;
}

```

---

### 2. Modificadores de Acceso (Access Modifiers)

Los modificadores de acceso determinan la visibilidad y accesibilidad de los miembros de la clase (atributos y métodos):

* **`private`**: Los miembros son accesibles únicamente desde dentro de la misma clase (por defecto en las clases).
* **`public`**: Los miembros son accesibles desde cualquier parte del programa donde el objeto sea visible.
* **`protected`**: Los miembros son accesibles dentro de la propia clase y por las clases derivadas (herencia).

---

### 3. Constructores y Destructores

* **Constructores**: Son funciones miembro especiales que se ejecutan automáticamente cuando se crea un objeto de la clase. Sirven para inicializar los atributos. Pueden ser por defecto, parametrizados, de copia, o usar listas de inicialización.


* **Destructores**: Funciones miembro especiales (con el símbolo `~` antes del nombre de la clase) que se ejecutan automáticamente cuando un objeto sale de ámbito o es destruido, liberando recursos o memoria asignada.

---

### 4. Características Avanzadas de Clases en C++

C++ ofrece herramientas potentes para trabajar con clases y objetos orientados a objetos:

* **Puntero `this**`: Un puntero implícito disponible dentro de las funciones miembro no estáticas que apunta al objeto actual que invocó la función.
* **Miembros estáticos (`static`)**: Variables o funciones compartidas por todas las instancias de la clase en lugar de pertenecer a un objeto en particular.


* **Herencia (`Inheritance`)**: Permite crear nuevas clases (clases derivadas o hijas) basadas en clases existentes (clases base o padres), promoviendo la reutilización de código. Puede ser simple o múltiple.
* **Polimorfismo y Funciones Virtuales**: Permite invocar métodos de clases derivadas a través de punteros o referencias de la clase base, usando funciones virtuales (`virtual`) para asegurar la vinculación dinámica en tiempo de ejecución.


* **Clases Abstractas**: Clases que contienen al menos una función virtual pura (`= 0`) y no pueden ser instanciadas directamente, sirviendo como interfaces o contratos para otras clases.

La **herencia** es uno de los pilares fundamentales de la programación orientada a objetos (OOP) en C++. Permite crear nuevas clases (conocidas como **clases derivadas** o hijas) a partir de clases ya existentes (llamadas **clases base** o padres).

Mediante la herencia, la clase derivada **hereda** automáticamente las propiedades (atributos) y los comportamientos (métodos) de la clase base, lo que promueve la reutilización del código y facilita la organización jerárquica de los programas.

---

### 1. Sintaxis Básica de la Herencia

Para indicar que una clase hereda de otra, se utiliza dos puntos (`:`) seguidos del modificador de acceso de la herencia y el nombre de la clase base:

```cpp
#include <iostream>
using namespace std;

// Clase Base (Padre)
class Animal {
public:
    void comer() {
        cout << "Este animal está comiendo." << endl;
    }
};

// Clase Derivada (Hija) que hereda públicamente de Animal
class Perro : public Animal {
public:
    void ladrar() {
        cout << "El perro ladra: ¡Guau guau!" << endl;
    }
};

int main() {
    Perro miPerro;
    
    // El objeto de la clase derivada puede acceder a los métodos de la clase base
    miPerro.comer();  
    // Y también a sus propios métodos
    miPerro.ladrar(); 
    
    return 0;
}

```

---

### 2. Modificadores de Acceso en la Herencia

El tipo de acceso con el que se hereda (`public`, `protected` o `private`) determina cómo se comportan los miembros heredados dentro de la clase derivada:

* **Herencia `public**` (la más común): Los miembros públicos de la clase base siguen siendo públicos en la derivada, y los protegidos siguen siendo protegidos.
* **Herencia `protected**`: Los miembros públicos y protegidos de la clase base se convierten en protegidos dentro de la clase derivada.
* **Herencia `private**`: Los miembros públicos y protegidos de la clase base se convierten en privados en la clase derivada.

> **Nota sobre `protected**`: Este modificador es clave en la herencia. Permite que los atributos o métodos de la clase base sean accesibles directamente por sus clases derivadas, pero sigan estando ocultos para el código externo (el `main`, por ejemplo).

---

### 3. Tipos de Herencia en C++

C++ soporta diferentes tipos de jerarquías de herencia:

1. **Herencia Simple**: Una clase derivada hereda de una única clase base (como en el ejemplo de `Perro` que hereda de `Animal`).
2. **Herencia Múltiple**: Una clase derivada puede heredar de más de una clase base al mismo tiempo.

```cpp
class Derivada : public Base1, public Base2 {
    // ...
};

```


3. **Herencia Multivel**: Una clase se deriva de otra que a su vez ya es derivada (ej. la clase `C` hereda de `B`, y `B` hereda de `A`).
4. **Herencia Jerárquica**: Múltiples clases derivadas heredan de una misma clase base.

---

### 4. Constructores y Destructores en la Herencia

Cuando se instancia un objeto de una clase derivada, el orden de ejecución sigue reglas estrictas:

* **Constructores**: Primero se ejecuta el constructor de la **clase base** y luego el constructor de la **clase derivada**. Si la clase base requiere parámetros, deben pasarse explícitamente desde la lista de inicialización del constructor derivado.
* **Destructores**: Se ejecutan en orden inverso; primero se destruye la **clase derivada** y al final el de la **clase base**.

Para asegurar una correcta destrucción de objetos polimórficos, es una buena práctica declarar el destructor de la clase base como `virtual`.

El **polimorfismo** (que significa "muchas formas") es otro de los pilares fundamentales de la programación orientada a objetos en C++. Permite que objetos de diferentes clases respondan a una misma llamada a un método, ejecutando comportamientos específicos de cada clase.

En C++, el polimorfismo se divide principalmente en dos tipos:

---

### 1. Polimorfismo en Tiempo de Compilación (Estático)

Ocurre antes de que el programa se ejecute. El compilador determina qué función ejecutar basándose en los tipos de datos o en los argumentos proporcionados. Se logra mediante:

* **Sobrecarga de funciones (*Function Overloading*):** Definir múltiples funciones con el mismo nombre pero con diferentes parámetros (número o tipo) dentro del mismo ámbito.
* **Sobrecarga de operadores (*Operator Overloading*):** Dar un significado personalizado a los operadores estándar (como `+`, `-`, `==`) para que funcionen con objetos creados por el usuario.

---

### 2. Polimorfismo en Tiempo de Ejecución (Dinámico)

Ocurre mientras el programa está en marcha (*runtime*). Se implementa utilizando **punteros o referencias** a la clase base, **funciones virtuales** (`virtual`) y la anulación de funciones (*override*).

Esto permite tratar objetos de clases derivadas de manera uniforme a través de una interfaz común.

#### Ejemplo Práctico de Polimorfismo Dinámico:

```cpp
#include <iostream>
using namespace std;

// Clase Base
class Figura {
public:
    // Función virtual que permite el polimorfismo dinámicamente
    virtual void dibujar() {
        cout << "Dibujando una figura genérica." << endl;
    }
    
    // Es una buena práctica declarar un destructor virtual en clases base
    virtual ~Figura() {}
};

// Clase Derivada 1
class Circulo : public Figura {
public:
    void dibujar() override { // 'override' indica que se está sobrescribiendo un método virtual
        cout << "Dibujando un círculo ⭕" << endl;
    }
};

// Clase Derivada 2
class Rectangulo : public Figura {
public:
    void dibujar() override {
        cout << "Dibujando un rectángulo ⬛" << endl;
    }
};

int main() {
    // Punteros de la clase base apuntando a objetos de las clases derivadas
    Figura* fig1 = new Circulo();
    Figura* fig2 = new Rectangulo();

    // Gracias al polimorfismo, se ejecuta el método correspondiente al objeto real en tiempo de ejecución
    fig1->dibujar(); // Salida: Dibujando un círculo ⭕
    fig2->dibujar(); // Salida: Dibujando un rectángulo ⬛

    // Liberación de memoria
    delete fig1;
    delete fig2;

    return 0;
}

```

---

### Conceptos Clave Relacionados:

* **Funciones Virtuales (`virtual`):** Le indican al compilador que no resuelva la llamada a la función en tiempo de compilación, sino que espere hasta el tiempo de ejecución para determinar a qué objeto pertenece realmente el puntero o referencia.
* **Especificador `override`:** Una palabra clave introducida en C++11 para asegurar explícitamente que una función miembro de una clase derivada está sobrescribiendo correctamente a un método virtual de su clase base, previniendo errores tipográficos.
* **Bajo el capó:** A nivel de implementación de bajo nivel, C++ suele utilizar una **tabla de funciones virtuales (vtable)** y un puntero a dicha tabla (`vptr`) para resolver dinámicamente las llamadas a los métodos en tiempo de ejecución.

A continuación, se presenta un **ejemplo aplicado, completo y complejo** en C++ que integra los conceptos fundamentales y avanzados de la Programación Orientada a Objetos (POO):

* Encapsulamiento y modificadores de acceso (`private`, `protected`, `public`).


* Constructores, listas de inicialización y destructores virtuales.


* El puntero `this`.


* Miembros estáticos (`static`).


* Herencia (simple y multivel).


* Polimorfismo dinámico (`virtual`, `override`).


* Clases abstractas y funciones virtuales puras.



### Contexto del Ejemplo: Sistema de Gestión para una Universidad 🏫

El programa modela una jerarquía de usuarios dentro de una institución educativa: una clase abstracta base `Persona`, de la cual derivan `Profesor` y `Estudiante`. A su vez, implementa atributos estáticos para llevar la contabilidad global de usuarios activos, control de memoria segura y polimorfismo en tiempo de ejecución.

```cpp
#include <iostream>
#include <string>
#include <vector>

using namespace std;

// ==========================================
// 1. CLASE ABSTRACTA BASE (Abstracción)
// ==========================================
class Persona {
protected:
    string nombre;
    int id;
    static int contadorGlobalPersonas; // Variable estática compartida

public:
    // Constructor con lista de inicialización
    Persona(string nombre, int id) : nombre(nombre), id(id) {
        contadorGlobalPersonas++;
    }

    // Destructor virtual (indispensable para evitar fugas de memoria en herencia)
    virtual ~Persona() {
        contadorGlobalPersonas--;
    }

    // Función virtual pura (convierte a Persona en Clase Abstracta)
    virtual void mostrarRol() const = 0;

    // Método común (Encapsulamiento)
    string getNombre() const {
        return this->nombre; // Uso del puntero 'this'
    }

    int getId() const {
        return this->id;
    }

    // Método Estático (Static Member Function)
    static int obtenerTotalPersonas() {
        return contadorGlobalPersonas;
    }
};

// Inicialización del miembro estático fuera de la clase
int Persona::contadorGlobalPersonas = 0;

// ==========================================
// 2. CLASE DERIVADA: Estudiante (Herencia)
// ==========================================
class Estudiante : public Persona {
private:
    double promedio;
    vector<string> materiasInscritas;

public:
    // Constructor que reutiliza el de la clase base mediante lista de inicialización
    Estudiante(string nombre, int id, double promedio) 
        : Persona(nombre, id), promedio(promedio) {}

    // Sobrescribiendo la función virtual pura (Polimorfismo / Override)
    void mostrarRol() const override {
        cout << "[Estudiante] Nombre: " << getNombre() 
             << " | ID: " << getId() 
             << " | Promedio: " << promedio << endl;
    }

    void inscribirMateria(const string& materia) {
        materiasInscritas.push_back(materia);
        cout << "-> " << getNombre() << " se inscribio en: " << materia << endl;
    }
};

// ==========================================
// 3. CLASE DERIVADA: Profesor (Herencia)
// ==========================================
class Profesor : public Persona {
private:
    string departamento;
    double salario;

public:
    Profesor(string nombre, int id, string departamento, double salario)
        : Persona(nombre, id), departamento(departamento), salario(salario) {}

    void mostrarRol() const override {
        cout << "[Profesor]   Nombre: " << getNombre() 
             << " | ID: " << getId() 
             << " | Depto: " << departamento 
             << " | Salario: $" << salario << endl;
    }

    void calificarExamen() const {
        cout << "-> El profesor " << getNombre() << " esta evaluando examenes del departamento de " << departamento << "." << endl;
    }
};

// ==========================================
// 4. FUNCIÓN PRINCIPAL (MAIN)
// ==========================================
int main() {
    cout << "=== SISTEMA DE GESTION UNIVERSITARIA ===" << endl;
    cout << "Personas activas iniciales: " << Persona::obtenerTotalPersonas() << "\n\n";

    // Uso de Polimorfismo Dinámico: Punteros de la clase base apuntando a objetos derivados
    // Se gestiona la memoria de forma dinámica con polimorfismo.
    vector<Persona*> comunidadU;

    comunidadU.push_back(new Estudiante("Carlos Gomez", 1001, 9.2));
    comunidadU.push_back(new Profesor("Dra. Elena Ruiz", 501, "Matematicas", 45000.0));
    comunidadU.push_back(new Estudiante("Sofia Martinez", 1002, 8.8));

    cout << "--- ESTADO ACTUAL DE LA COMUNIDAD (Polimorfismo en accion) ---" << endl;
    for (const auto& persona : comunidadU) {
        // En tiempo de ejecución se invoca el 'mostrarRol' correspondiente a cada tipo de objeto
        persona->mostrarRol();
    }

    cout << "\nPersonas activas despues de creaciones: " << Persona::obtenerTotalPersonas() << "\n\n";

    cout << "--- OPERACIONES ESPECIFICAS DE CADA CLASE ---" << endl;
    
    // Casteo seguro o uso directo para métodos propios de las clases derivadas
    Estudiante* est = dynamic_cast<Estudiante*>(comunidadU[0]);
    if (est != nullptr) {
        est->inscribirMateria("Calculo Avanzado");
        est->inscribirMateria("Fisica II");
    }

    Profesor* prof = dynamic_cast<Profesor*>(comunidadU[1]);
    if (prof != nullptr) {
        prof->calificarExamen();
    }

    cout << "\n--- LIBERANDO RECURSOS (Destructores Virtuales) ---" << endl;
    // Liberar la memoria dinámica de los punteros para prevenir fugas de memoria (Memory Leaks)
    for (auto persona : comunidadU) {
        delete persona;
    }
    comunidadU.clear();

    cout << "Personas activas tras limpiar memoria: " << Persona::obtenerTotalPersonas() << endl;

    return 0;
}

```

### Características Clave Demostradas en el Código:

1. **Clases Abstractas e Interfaces**: La clase `Persona` contiene una función virtual pura (`virtual void mostrarRol() const = 0;`), lo que impide que se creen objetos directamente de tipo `Persona`. Funciona como plantilla obligatoria.
2. **Herencia y Modificador `protected**`: Las clases `Estudiante` y `Profesor` heredan de `Persona`, heredando acceso directo a los campos protegidos (`nombre`, `id`) y reutilizando lógica común.
3. **Polimorfismo Dinámico**: El vector `vector<Persona*>` almacena punteros de la clase base. Al invocar `persona->mostrarRol()`, C++ utiliza una tabla de funciones virtuales (*vtable*) para ejecutar la versión correcta del método dependiendo de si el objeto es un `Estudiante` o un `Profesor` en tiempo de ejecución.


4. **Miembros Estáticos (`static`)**: `contadorGlobalPersonas` y la función `obtenerTotalPersonas()` pertenecen a la clase en conjunto, permitiendo llevar la cuenta exacta de cuántas instancias existen sin importar el tipo de subclase.
5. **Puntero `this**`: Utilizado explícitamente en `getNombre()` (`return this->nombre;`) para referenciar al objeto invocador actual.


6. **Destructores Virtuales**: Evitan comportamientos indefinidos y fugas de memoria al destruir objetos derivados a través de punteros base.


A continuación se presenta un sistema completo y avanzado en C++ para la gestión de un restaurante que aplica **clases** en conjunto con estructuras de datos de la **Standard Template Library (STL)**: **Listas (`std::list`)**, **Colas (`std::queue`)** y **Pilas (`std::stack`)**.

### Estructura y Aplicación de las Estructuras de Datos:

1. **`std::list` (Lista):** Utilizada para gestionar el menú del restaurante. Permite inserciones, búsquedas y eliminaciones dinámicas de platillos de forma eficiente.
2. **`std::queue` (Cola - FIFO):** Utilizada para la gestión de los pedidos de los clientes. El primer pedido en entrar (First In, First Out) es el primero en ser atendido por la cocina.
3. **`std::stack` (Pila - LIFO):** Utilizada para la gestión de platos limpios/sucios. El último plato en lavarse y apilarse es el primero en ser tomado para servir (Last In, First Out).

---

### Código Completo en C++

```cpp
#include <iostream>
#include <string>
#include <list>
#include <queue>
#include <stack>

using namespace std;

// ==========================================
// 1. CLASE PLATILLO (Modelo base para el menú)
// ==========================================
class Platillo {
private:
    string nombre;
    double precio;
    string categoria;

public:
    Platillo(string nombre, double precio, string categoria) 
        : nombre(nombre), precio(precio), categoria(categoria) {}

    string getNombre() const { return nombre; }
    double getPrecio() const { return precio; }
    string getCategoria() const { return categoria; }

    void mostrarPlatillo() const {
        cout << "- " << nombre << " (" << categoria << ") - $" << precio << endl;
    }
};

// ==========================================
// 2. CLASE PEDIDO (Usa Cola)
// ==========================================
class Pedido {
private:
    int idPedido;
    string cliente;
    string descripcionPlatillo;

public:
    Pedido(int id, string cliente, string platillo) 
        : idPedido(id), cliente(cliente), descripcionPlatillo(platillo) {}

    int getId() const { return idPedido; }
    string getCliente() const { return cliente; }
    string getPlatillo() const { return descripcionPlatillo; }

    void mostrarPedido() const {
        cout << "[Pedido #" << idPedido << "] Cliente: " << cliente << " | Platillo: " << descripcionPlatillo << endl;
    }
};

// ==========================================
// 3. CLASE RESTAURANTE (Integra Listas, Colas y Pilas)
// ==========================================
class Restaurante {
private:
    string nombreRestaurante;
    // 1. LISTA para el menú de platillos disponibles
    list<Platillo> menu;
    
    // 2. COLA (FIFO) para los pedidos pendientes de la cocina
    queue<Pedido> colaPedidos;
    
    // 3. PILA (LIFO) para el inventario de platos limpios
    stack<string> pilaPlatosLimpios;
    
    int contadorPedidos;

public:
    Restaurante(string nombre) : nombreRestaurante(nombre), contadorPedidos(1) {
        // Inicializar algunos platos en la pila de platos limpios
        for(int i = 1; i <= 5; i++) {
            pilaPlatosLimpios.push("Plato hondo #" + to_string(i));
        }
    }

    // --- GESTIÓN DE LISTA (MENÚ) ---
    void agregarPlatilloAlMenu(const Platillo& p) {
        menu.push_back(p);
        cout << "-> Platillo '" << p.getNombre() << "' agregado al menu.\n";
    }

    void mostrarMenu() const {
        cout << "\n=== MENU DE " << nombreRestaurante << " ===" << endl;
        if (menu.empty()) {
            cout << "El menu esta vacio.\n";
            return;
        }
        for (const auto& p : menu) {
            p.mostrarPlatillo();
        }
    }

    // --- GESTIÓN DE COLA (PEDIDOS DE CLIENTES) ---
    void registrarPedido(string cliente, string nombrePlatillo) {
        // Validar si el platillo existe en la lista del menú
        bool encontrado = false;
        for (const auto& p : menu) {
            if (p.getNombre() == nombrePlatillo) {
                encontrado = true;
                break;
            }
        }

        if (!encontrado) {
            cout << "-> Error: El platillo '" << nombrePlatillo << "' no existe en el menu.\n";
            return;
        }

        Pedido nuevoPedido(contadorPedidos++, cliente, nombrePlatillo);
        colaPedidos.push(nuevoPedido);
        cout << "-> Pedido registrado exitosamente para " << cliente << ".\n";
    }

    void procesarSiguientePedido() {
        if (colaPedidos.empty()) {
            cout << "-> No hay pedidos pendientes en la cola.\n";
            return;
        }

        // Tomar el primer pedido de la cola (FIFO)
        Pedido actual = colaPedidos.front();
        colaPedidos.pop();

        cout << "\n[COCINA] Preparando pedido:\n";
        actual.mostrarPedido();
        
        // Simular el uso de un plato limpio de la Pila
        if (!pilaPlatosLimpios.empty()) {
            cout << "  (Se utilizo el " << pilaPlatosLimpios.top() << " para servir)\n";
            pilaPlatosLimpios.pop(); // Se retira el plato limpio para usarlo
        } else {
            cout << "  (¡Alerta! No hay platos limpios disponibles. Lavando platos urgente...)\n";
        }
        cout << "-> ¡Pedido listo para entregar!\n";
    }

    // --- GESTIÓN DE PILA (LAVADO Y CONTROL DE PLATOS) ---
    void lavarYApilarPlato(string descripcionPlato) {
        pilaPlatosLimpios.push(descripcionPlato);
        cout << "-> Plato lavado y apilado: " << descripcionPlato << " (Total limpios: " << pilaPlatosLimpios.size() << ")\n";
    }

    void verEstadoRestaurante() const {
        cout << "\n========================================" << endl;
        cout << "ESTADO ACTUAL DEL RESTAURANTE: " << nombreRestaurante << endl;
        cout << "Platillos en Menu (Lista): " << menu.size() << endl;
        cout << "Pedidos pendientes en Cocina (Cola): " << colaPedidos.size() << endl;
        cout << "Platos limpios apilados (Pila): " << pilaPlatosLimpios.size() << endl;
        cout << "========================================" << endl;
    }
};

// ==========================================
// 4. FUNCIÓN PRINCIPAL (SIMULACIÓN)
// ==========================================
int main() {
    // Instanciar el restaurante
    Restaurante miRestaurante("La Tasca de C++");

    // 1. Usando Listas para configurar el Menú
    cout << "--- 1. CONFIGURANDO EL MENU (Listas) ---" << endl;
    miRestaurante.agregarPlatilloAlMenu(Platillo("Tacos al Pastor", 85.50, "Comida Mexicana"));
    miRestaurante.agregarPlatilloAlMenu(Platillo("Pizza Margarita", 140.00, "Comida Italiana"));
    miRestaurante.agregarPlatilloAlMenu(Platillo("Ensalada Caesar", 95.00, "Ensaladas"));
    
    miRestaurante.mostrarMenu();

    // 2. Usando Colas para los Pedidos de los Clientes
    cout << "\n--- 2. REGISTRANDO PEDIDOS (Colas - FIFO) ---" << endl;
    miRestaurante.registrarPedido("Alejandro", "Tacos al Pastor");
    miRestaurante.registrarPedido("Mariana", "Pizza Margarita");
    miRestaurante.registrarPedido("Carlos", "Ensalada Caesar");

    miRestaurante.verEstadoRestaurante();

    // 3. Procesando pedidos en orden de llegada (Cocina) y usando Pilas para los platos
    cout << "\n--- 3. COCINA ATENDIENDO PEDIDOS Y USANDO PLATOS (Pilas - LIFO) ---" << endl;
    miRestaurante.procesarSiguientePedido(); // Atiende a Alejandro
    miRestaurante.procesarSiguientePedido(); // Atiende a Mariana

    miRestaurante.verEstadoRestaurante();

    // 4. Lavando platos (Agregando de vuelta a la pila LIFO)
    cout << "\n--- 4. LAVANDERIA DE PLATOS (Pila LIFO) ---" << endl;
    miRestaurante.lavarYApilarPlato("Plato hondo #1");
    miRestaurante.lavarYApilarPlato("Plato hondo #2");

    // 5. Atender el último pedido restante
    cout << "\n--- 5. CONTINUANDO CON LA COCINA ---" << endl;
    miRestaurante.procesarSiguientePedido(); // Atiende a Carlos

    miRestaurante.verEstadoRestaurante();

    return 0;
}

```

### Explicación del Funcionamiento:

* **Lista (`std::list<Platillo> menu`)**: Mantiene los platillos disponibles de manera dinámica. Al momento de registrar un pedido, el sistema recorre la lista para verificar si el platillo existe y se puede preparar.
* **Cola (`std::queue<Pedido> colaPedidos`)**: Funciona bajo la estructura **FIFO** (*First In, First Out*). Los clientes que llegan primero (`Alejandro`, luego `Mariana`, luego `Carlos`) son los primeros en ser atendidos por la cocina en ese orden estricto.
* **Pila (`std::stack<string> pilaPlatosLimpios`)**: Funciona bajo la estructura **LIFO** (*Last In, First Out*). Los platos limpios se apilan unos sobre otros; al momento de servir un platillo, se toma obligatoriamente el plato que quedó hasta arriba en la pila (`top()`), y al lavar nuevos platos se colocan en la cima.

Para estructurar correctamente el sistema del restaurante en archivos de cabecera (`.h` o `.hpp`) y archivos de implementación (`.cpp`), se divide el código de acuerdo con las clases creadas. Esto facilita la compilación separada y el mantenimiento del código.

---

### 1. Archivo: `Platillo.h`

Define la estructura básica de los platillos del menú.

```cpp
#ifndef PLATILLO_H
#define PLATILLO_H

#include <string>

class Platillo {
private:
    std::string nombre;
    double precio;
    std::string categoria;

public:
    Platillo(std::string nombre, double precio, std::string categoria);

    std::string getNombre() const;
    double getPrecio() const;
    std::string getCategoria() const;

    void mostrarPlatillo() const;
};

#endif

```

#### `Platillo.cpp`

```cpp
#include "Platillo.h"
#include <iostream>

Platillo::Platillo(std::string nombre, double precio, std::string categoria) 
    : nombre(nombre), precio(precio), categoria(categoria) {}

std::string Platillo::getNombre() const { return nombre; }
double Platillo::getPrecio() const { return precio; }
std::string Platillo::getCategoria() const { return categoria; }

void Platillo::mostrarPlatillo() const {
    std::cout << "- " << nombre << " (" << categoria << ") - $" << precio << std::endl;
}

```

---

### 2. Archivo: `Pedido.h`

Maneja la información de cada orden de los clientes.

```cpp
#ifndef PEDIDO_H
#define PEDIDO_H

#include <string>

class Pedido {
private:
    int idPedido;
    std::string cliente;
    std::string descripcionPlatillo;

public:
    Pedido(int id, std::string cliente, std::string platillo);

    int getId() const;
    std::string getCliente() const;
    std::string getPlatillo() const;

    void mostrarPedido() const;
};

#endif

```

#### `Pedido.cpp`

```cpp
#include "Pedido.h"
#include <iostream>

Pedido::Pedido(int id, std::string cliente, std::string platillo) 
    : idPedido(id), cliente(cliente), descripcionPlatillo(platillo) {}

int Pedido::getId() const { return idPedido; }
std::string Pedido::getCliente() const { return cliente; }
std::string Pedido::getPlatillo() const { return descripcionPlatillo; }

void Pedido::mostrarPedido() const {
    std::cout << "[Pedido #" << idPedido << "] Cliente: " << cliente << " | Platillo: " << descripcionPlatillo << std::endl;
}

```

---

### 3. Archivo: `Restaurante.h`

Integra la **Lista** (`std::list`) para el menú, la **Cola** (`std::queue`) para los pedidos y la **Pila** (`std::stack`) para los platos limpios.

```cpp
#ifndef RESTAURANTE_H
#define RESTAURANTE_H

#include <string>
#include <list>
#include <queue>
#include <stack>
#include "Platillo.h"
#include "Pedido.h"

class Restaurante {
private:
    std::string nombreRestaurante;
    std::list<Platillo> menu;
    std::queue<Pedido> colaPedidos;
    std::stack<std::string> pilaPlatosLimpios;
    int contadorPedidos;

public:
    Restaurante(std::string nombre);

    void agregarPlatilloAlMenu(const Platillo& p);
    void mostrarMenu() const;

    void registrarPedido(std::string cliente, std::string nombrePlatillo);
    void procesarSiguientePedido();

    void lavarYApilarPlato(std::string descripcionPlato);
    void verEstadoRestaurante() const;
};

#endif

```

#### `Restaurante.cpp`

```cpp
#include "Restaurante.h"
#include <iostream>

Restaurante::Restaurante(std::string nombre) : nombreRestaurante(nombre), contadorPedidos(1) {
    for(int i = 1; i <= 5; i++) {
        pilaPlatosLimpios.push("Plato hondo #" + std::to_string(i));
    }
}

void Restaurante::agregarPlatilloAlMenu(const Platillo& p) {
    menu.push_back(p);
    std::cout << "-> Platillo '" << p.getNombre() << "' agregado al menu.\n";
}

void Restaurante::mostrarMenu() const {
    std::cout << "\n=== MENU DE " << nombreRestaurante << " ===" << std::endl;
    if (menu.empty()) {
        std::cout << "El menu esta vacio.\n";
        return;
    }
    for (const auto& p : menu) {
        p.mostrarPlatillo();
    }
}

void Restaurante::registrarPedido(std::string cliente, std::string nombrePlatillo) {
    bool encontrado = false;
    for (const auto& p : menu) {
        if (p.getNombre() == nombrePlatillo) {
            encontrado = true;
            break;
        }
    }

    if (!encontrado) {
        std::cout << "-> Error: El platillo '" << nombrePlatillo << "' no existe en el menu.\n";
        return;
    }

    Pedido nuevoPedido(contadorPedidos++, cliente, nombrePlatillo);
    colaPedidos.push(nuevoPedido);
    std::cout << "-> Pedido registrado exitosamente para " << cliente << ".\n";
}

void Restaurante::procesarSiguientePedido() {
    if (colaPedidos.empty()) {
        std::cout << "-> No hay pedidos pendientes en la cola.\n";
        return;
    }

    Pedido actual = colaPedidos.front();
    colaPedidos.pop();

    std::cout << "\n[COCINA] Preparando pedido:\n";
    actual.mostrarPedido();
    
    if (!pilaPlatosLimpios.empty()) {
        std::cout << "  (Se utilizo el " << pilaPlatosLimpios.top() << " para servir)\n";
        pilaPlatosLimpios.pop();
    } else {
        std::cout << "  (¡Alerta! No hay platos limpios disponibles. Lavando platos urgente...)\n";
    }
    std::cout << "-> ¡Pedido listo para entregar!\n";
}

void Restaurante::lavarYApilarPlato(std::string descripcionPlato) {
    pilaPlatosLimpios.push(descripcionPlato);
    std::cout << "-> Plato lavado y apilado: " << descripcionPlato << " (Total limpios: " << pilaPlatosLimpios.size() << ")\n";
}

void Restaurante::verEstadoRestaurante() const {
    std::cout << "\n========================================" << std::endl;
    std::cout << "ESTADO ACTUAL DEL RESTAURANTE: " << nombreRestaurante << std::endl;
    std::cout << "Platillos en Menu (Lista): " << menu.size() << std::endl;
    std::cout << "Pedidos pendientes en Cocina (Cola): " << colaPedidos.size() << endl;
    std::cout << "Platos limpios apilados (Pila): " << pilaPlatosLimpios.size() << std::endl;
    std::cout << "========================================" << std::endl;
}

```

---

### 4. Archivo Principal: `main.cpp`

Contiene la lógica de ejecución del programa.

```cpp
#include "Restaurante.h"
#include "Platillo.h"
#include <iostream>

int main() {
    Restaurante miRestaurante("La Tasca de C++");

    std::cout << "--- 1. CONFIGURANDO EL MENU (Listas) ---" << std::endl;
    miRestaurante.agregarPlatilloAlMenu(Platillo("Tacos al Pastor", 85.50, "Comida Mexicana"));
    miRestaurante.agregarPlatilloAlMenu(Platillo("Pizza Margarita", 140.00, "Comida Italiana"));
    miRestaurante.agregarPlatilloAlMenu(Platillo("Ensalada Caesar", 95.00, "Ensaladas"));
    
    miRestaurante.mostrarMenu();

    std::cout << "\n--- 2. REGISTRANDO PEDIDOS (Colas - FIFO) ---" << std::endl;
    miRestaurante.registrarPedido("Alejandro", "Tacos al Pastor");
    miRestaurante.registrarPedido("Mariana", "Pizza Margarita");
    miRestaurante.registrarPedido("Carlos", "Ensalada Caesar");

    miRestaurante.verEstadoRestaurante();

    std::cout << "\n--- 3. COCINA ATENDIENDO PEDIDOS Y USANDO PLATOS (Pilas - LIFO) ---" << std::endl;
    miRestaurante.procesarSiguientePedido();
    miRestaurante.procesarSiguientePedido();

    miRestaurante.verEstadoRestaurante();

    std::cout << "\n--- 4. LAVANDERIA DE PLATOS (Pila LIFO) ---" << std::endl;
    miRestaurante.lavarYApilarPlato("Plato hondo #1");
    miRestaurante.lavarYApilarPlato("Plato hondo #2");

    std::cout << "\n--- 5. CONTINUANDO CON LA COCINA ---" << std::endl;
    miRestaurante.procesarSiguientePedido();

    miRestaurante.verEstadoRestaurante();

    return 0;
}

```

---

### Compilación (ejemplo con g++)

Para compilar todos los archivos juntos desde la terminal, puedes ejecutar el siguiente comando:

```bash
g++ main.cpp Platillo.cpp Pedido.cpp Restaurante.cpp -o restaurante

```

Y luego ejecutar el binario generado:

```bash
./restaurante

```


Para implementar una aplicación de inventario en C++ conectada a una base de datos MySQL, el estándar de la industria es utilizar la biblioteca oficial **MySQL Connector/C++** (la cual provee una interfaz orientada a objetos compatible con C++ moderno).

A continuación se muestra el diseño completo y estructurado de la aplicación utilizando **clases**, separación en **archivos de cabecera e implementación**, y las operaciones CRUD básicas (Crear, Leer, Actualizar y Eliminar).

---

### Paso 0: Base de datos en MySQL

Primero, crea la base de datos y la tabla ejecutando este script SQL en tu gestor de MySQL:

```sql
CREATE DATABASE IF NOT EXISTS inventario_db;
USE inventario_db;

CREATE TABLE IF NOT EXISTS productos (
    id INT AUTO_INCREMENT PRIMARY KEY,
    nombre VARCHAR(100) NOT NULL,
    cantidad INT NOT NULL,
    precio DOUBLE NOT NULL
);

```

---

### 1. Archivo de Cabecera: `Database.h`

Esta clase se encarga exclusivamente de manejar la conexión con MySQL utilizando el conector oficial.

```cpp
#ifndef DATABASE_H
#define DATABASE_H

#include <mysql_driver.h>
#include <mysql_connection.h>
#include <cppconn/statement.h>
#include <cppconn/prepared_statement.h>
#include <cppconn/resultset.h>
#include <string>

class Database {
private:
    sql::mysql::MySQL_Driver* driver;
    sql::Connection* con;

public:
    Database(std::string host, std::string user, std::string pass, std::string db);
    ~Database();

    sql::Connection* getConnection();
};

#endif

```

#### `Database.cpp`

```cpp
#include "Database.h"
#include <iostream>

Database::Database(std::string host, std::string user, std::string pass, std::string db) {
    try {
        driver = sql::mysql::get_mysql_driver_instance();
        con = driver->connect(host, user, pass);
        con->setSchema(db);
    } catch (sql::SQLException &e) {
        std::cerr << "Error al conectar a la base de datos: " << e.what() << std::endl;
    }
}

Database::~Database() {
    delete con;
}

sql::Connection* Database::getConnection() {
    return con;
}

```

---

### 2. Archivo de Cabecera: `Inventario.h`

Esta clase representa la lógica de negocio del inventario y realiza las consultas a la base de datos mediante declaraciones preparadas (`PreparedStatement`) para mayor seguridad.

```cpp
#ifndef INVENTARIO_H
#define INVENTARIO_H

#include "Database.h"
#include <string>

class Inventario {
private:
    Database& db;

public:
    Inventario(Database& database);

    void agregarProducto(std::string nombre, int cantidad, double precio);
    void mostrarInventario();
    void actualizarStock(int id, int nuevaCantidad);
    void eliminarProducto(int id);
};

#endif

```

#### `Inventario.cpp`

```cpp
#include "Inventario.h"
#include <iostream>

Inventario::Inventario(Database& database) : db(database) {}

void Inventario::agregarProducto(std::string nombre, int cantidad, double precio) {
    try {
        std::unique_ptr<sql::PreparedStatement> pstmt(
            db.getConnection()->prepareStatement("INSERT INTO productos (nombre, cantidad, precio) VALUES (?, ?, ?)")
        );
        pstmt->setString(1, nombre);
        pstmt->setInt(2, cantidad);
        pstmt->setDouble(3, precio);
        pstmt->execute();
        std::cout << "[Éxito] Producto agregado correctamente.\n";
    } catch (sql::SQLException &e) {
        std::cerr << "Error al agregar producto: " << e.what() << std::endl;
    }
}

void Inventario::mostrarInventario() {
    try {
        std::unique_ptr<sql::Statement> stmt(db.getConnection()->createStatement());
        std::unique_ptr<sql::ResultSet> res(stmt->executeQuery("SELECT id, nombre, cantidad, precio FROM productos"));
        
        std::cout << "\n========================================\n";
        std::cout << "          INVENTARIO ACTUAL             \n";
        std::cout << "========================================\n";
        while (res->next()) {
            std::cout << "ID: " << res->getInt("id") 
                      << " | Nombre: " << res->getString("nombre") 
                      << " | Cantidad: " << res->getInt("cantidad") 
                      << " | Precio: $" << res->getDouble("precio") << std::endl;
        }
        std::cout << "========================================\n";
    } catch (sql::SQLException &e) {
        std::cerr << "Error al consultar inventario: " << e.what() << std::endl;
    }
}

void Inventario::actualizarStock(int id, int nuevaCantidad) {
    try {
        std::unique_ptr<sql::PreparedStatement> pstmt(
            db.getConnection()->prepareStatement("UPDATE productos SET cantidad = ? WHERE id = ?")
        );
        pstmt->setInt(1, nuevaCantidad);
        pstmt->setInt(2, id);
        pstmt->execute();
        std::cout << "[Éxito] Stock actualizado correctamente.\n";
    } catch (sql::SQLException &e) {
        std::cerr << "Error al actualizar stock: " << e.what() << std::endl;
    }
}

void Inventario::eliminarProducto(int id) {
    try {
        std::unique_ptr<sql::PreparedStatement> pstmt(
            db.getConnection()->prepareStatement("DELETE FROM productos WHERE id = ?")
        );
        pstmt->setInt(1, id);
        pstmt->execute();
        std::cout << "[Éxito] Producto eliminado del inventario.\n";
    } catch (sql::SQLException &e) {
        std::cerr << "Error al eliminar producto: " << e.what() << std::endl;
    }
}

```

---

### 3. Archivo Principal: `main.cpp`

Interfaz de consola interactiva para gestionar el inventario conectándose a MySQL.

```cpp
#include "Database.h"
#include "Inventario.h"
#include <iostream>

using namespace std;

void mostrarMenu() {
    cout << "\n--- SISTEMA DE INVENTARIO C++ & MYSQL ---\n";
    cout << "1. Ver inventario\n";
    cout << "2. Agregar producto\n";
    cout << "3. Actualizar cantidad de producto\n";
    cout << "4. Eliminar producto\n";
    cout << "5. Salir\n";
    cout << "Seleccione una opcion: ";
}

int main() {
    // Configura tus credenciales de MySQL aquí (host, usuario, contraseña, base de datos)
    Database db("tcp://127.0.0.1:3306", "root", "tu_contraseña", "inventario_db");
    Inventario inventario(db);

    int opcion;
    do {
        mostrarMenu();
        cin >> opcion;

        switch (opcion) {
            case 1:
                inventario.mostrarInventario();
                break;
            case 2: {
                string nombre;
                int cantidad;
                double precio;
                cout << "Ingrese nombre del producto: ";
                cin >> ws; // Limpiar buffer
                getline(cin, nombre);
                cout << "Ingrese cantidad: ";
                cin >> cantidad;
                cout << "Ingrese precio: ";
                cin >> precio;
                inventario.agregarProducto(nombre, cantidad, precio);
                break;
            }
            case 3: {
                int id, nuevaCantidad;
                cout << "Ingrese ID del producto a actualizar: ";
                cin >> id;
                cout << "Ingrese la nueva cantidad: ";
                cin >> nuevaCantidad;
                inventario.actualizarStock(id, nuevaCantidad);
                break;
            }
            case 4: {
                int id;
                cout << "Ingrese ID del producto a eliminar: ";
                cin >> id;
                inventario.eliminarProducto(id);
                break;
            }
            case 5:
                cout << "Saliendo del sistema...\n";
                break;
            default:
                cout << "Opcion invalida. Intente de nuevo.\n";
        }
    } while (opcion != 5);

    return 0;
}

```

---

### Compilación y Requisitos Previos:

1. Asegúrate de tener instalado el paquete de desarrollo del conector de MySQL para C++ (`libmysqlcppconn-dev` en sistemas basados en Debian/Ubuntu o configurado en Visual Studio mediante vcpkg).
2. Compila los archivos ejecutando el siguiente comando en la terminal:

```bash
g++ main.cpp Database.cpp Inventario.cpp -lmysqlcppconn -o sistema_inventario

```

