# Laboratorio 3 — Funciones, módulos y programación orientada a objetos en Python

En el [Laboratorio 2](laboratorio-02.md) construiste un gestor de inventario con condiciones, ciclos y colecciones. Cada producto se representaba mediante un diccionario y todas las operaciones estaban reunidas en un archivo.

En este laboratorio vas a reorganizar esa aplicación. Primero separarás tareas en funciones, luego distribuirás el código entre módulos y finalmente representarás los productos y el inventario mediante objetos. El objetivo no es escribir más archivos por obligación, sino conseguir que cada parte del programa tenga una responsabilidad clara.

| Práctica | Tema |
|---|---|
| 3.1 | Funciones, parámetros, retorno y alcance de variables |
| 3.2 | Módulos, importaciones y organización del código |
| 3.3 | Clases, objetos, `__init__` y `self` |
| 3.4 | Métodos, encapsulamiento, propiedades y colaboración |
| 3.5 | Reto integrador: gestor de inventario con POO |

> Ejecuta los ejemplos, predice resultados, modifica valores y resuelve los ejercicios antes de consultar las pistas. Cuando un fragmento de código deba agregarse a una clase o archivo existente, se indicará de forma explícita.

## Preparación

Conserva `gestor_inventario.py` del Laboratorio 2. No lo reemplaces; servirá para comparar la versión procedural con la orientada a objetos.

Crea esta estructura:

~~~text
python-laboratorio-03/
├── practica-3-1/
├── practica-3-2/
├── practica-3-3/
├── practica-3-4/
└── gestor_inventario/
~~~

Puedes utilizar Python 3 desde Visual Studio Code y una terminal. Ejecuta cada ejemplo desde la carpeta que contiene sus archivos:

~~~bash
python main.py
~~~

Según tu equipo, el comando puede ser `py main.py` o `python3 main.py`. Utiliza el que ya comprobaste en el Laboratorio 1.

Si trabajas en Google Colab por restricciones del computador, ejecuta normalmente los ejemplos de un solo archivo. Para los ejercicios con módulos, puedes crear cada archivo en una celda que comience con `%%writefile operaciones.py` (cambiando el nombre según corresponda) y ejecutar el principal en otra celda con `%run main.py`. Conserva una copia de los archivos porque los creados en una sesión temporal pueden perderse. No es necesario instalar paquetes externos.

---

## Práctica 3.1 — Funciones, parámetros, retorno y alcance

### Objetivos

Al finalizar esta práctica podrás:

- definir funciones con `def`;
- diferenciar parámetros y argumentos;
- devolver valores mediante `return`;
- utilizar argumentos predeterminados y con nombre;
- reconocer el alcance de una variable;
- utilizar funciones que consulten o modifiquen colecciones.

### 1. ¿Para qué sirve una función?

En el laboratorio anterior repetías instrucciones para mostrar mensajes, buscar artículos y calcular resultados. Con una función puedes asignar un nombre a una operación y reutilizarla.

Crea `primeras_funciones.py`:

~~~python
def mostrar_titulo():
    print("==========================")
    print("     INVENTARIO PYTHON")
    print("==========================")


mostrar_titulo()
print("Registro de productos")
mostrar_titulo()
~~~

Ejecuta. La función no se ejecuta simplemente por estar definida: las instrucciones se ejecutan cuando la llamas.

Observa cuatro detalles: `def` inicia la definición, los paréntesis reciben los parámetros, los dos puntos abren el bloque y la indentación indica qué instrucciones pertenecen a la función.

### Ejercicio — Encabezado y despedida

Crea `mensajes.py` con dos funciones: `mostrar_encabezado()` y `mostrar_despedida()`.

Llámales desde el programa y utiliza el encabezado dos veces. No dupliques las instrucciones `print()` que lo forman.

### 2. Parámetros y argumentos

Ejecuta:

~~~python
def saludar(nombre):
    print(f"Hola, {nombre}")


saludar("Laura")
saludar("Andrés")
~~~

`nombre` es un parámetro; `"Laura"` y `"Andrés"` son argumentos de llamadas diferentes.

Prueba ahora:

~~~python
def mostrar_producto(nombre, cantidad):
    print(f"Producto: {nombre}")
    print(f"Existencias: {cantidad}")


mostrar_producto("Teclado", 4)
mostrar_producto("Mouse", 7)
~~~

#### Antes de ejecutar — Predice

~~~python
def presentar(nombre, ciudad):
    print(f"{nombre} vive en {ciudad}")


presentar("Ana", "Bogotá")
presentar("Cali", "Luis")
~~~

¿La segunda llamada produce un error o una frase extraña? Escribe tu predicción, ejecuta y corrige el orden de los argumentos.

### 3. Devolver valores con `return`

Crea `calculos.py`:

~~~python
def calcular_subtotal(precio, cantidad):
    subtotal = precio * cantidad
    return subtotal


resultado = calcular_subtotal(85000, 3)
print("Subtotal:", resultado)
~~~

Resultado esperado:

~~~text
Subtotal: 255000
~~~

La función devuelve un resultado que puede utilizarse después.

Compara:

~~~python
def mostrar_total(precio, cantidad):
    print(precio * cantidad)


def obtener_total(precio, cantidad):
    return precio * cantidad


mostrar_total(10000, 3)
total = obtener_total(10000, 3)
print("Resultado reutilizable:", total)
~~~

En la primera función, la impresión es su efecto visible. La segunda entrega un valor al programa.

### 4. Una función sin `return` explícito

Ejecuta:

~~~python
def anunciar():
    print("Operación iniciada")


resultado = anunciar()
print("Valor devuelto:", resultado)
~~~

La función muestra un mensaje, pero la llamada devuelve `None`.

#### Depuración — Faltó devolver el dato

Crea `error_retorno.py`:

~~~python
def sumar(a, b):
    resultado = a + b


total = sumar(10, 20)
print(total)
~~~

Corrige el programa para que `total` almacene el número 30. No reemplaces el cálculo por un valor fijo.

### Ejercicio — Cotizador con funciones

Crea `cotizador_funciones.py` y define:

~~~python
def calcular_subtotal(precio, cantidad):
    # Completa el cálculo.
    pass


def calcular_descuento(subtotal, porcentaje):
    # El porcentaje 10 representa 10 %.
    pass


def calcular_impuesto(base, porcentaje):
    # Calcula el impuesto sobre la base recibida.
    pass
~~~

Completa las tres funciones. Utiliza:

~~~text
Precio: 100000
Cantidad: 2
Descuento: 10
Impuesto: 19
~~~

Los resultados deben ser:

~~~text
Subtotal: 200000
Descuento: 20000
Base después del descuento: 180000
Impuesto: 34200
Total: 214200
~~~

Guarda cada resultado en una variable antes de imprimirlo. No coloques todos los cálculos en una sola instrucción `print()`.

### 5. Valores predeterminados y argumentos con nombre

Crea `valores_predeterminados.py`:

~~~python
def calcular_total(precio, cantidad, descuento=0):
    subtotal = precio * cantidad
    return subtotal - subtotal * descuento / 100


print(calcular_total(50000, 2))
print(calcular_total(50000, 2, 10))
print(calcular_total(precio=50000, cantidad=2, descuento=20))
~~~

Predice las tres salidas. En la primera llamada no se especifica descuento y se utiliza cero.

#### Ejercicio — Costos de envío

Crea una función `calcular_cobro(valor_compra, envio=10000)` que devuelva el valor de la compra más el envío. Realiza una llamada con envío predeterminado y otra con envío gratuito.

### 6. Alcance de las variables

Crea `alcance.py`:

~~~python
def duplicar(numero):
    resultado = numero * 2
    return resultado


doble = duplicar(8)
print(doble)
~~~

Ahora agrega al final:

~~~python
print(resultado)
~~~

Lee el error. `resultado` es una variable local de la función; fuera de ella tienes el valor devuelto, almacenado en `doble`.

### 7. Funciones y listas

Una función puede recibir una lista de diccionarios:

~~~python
def calcular_unidades(productos):
    total = 0

    for producto in productos:
        total += producto["cantidad"]

    return total


inventario = [
    {"nombre": "Teclado", "cantidad": 4},
    {"nombre": "Mouse", "cantidad": 7}
]

print(calcular_unidades(inventario))
~~~

Resultado esperado: `11`.

Una función también puede modificar la lista recibida:

~~~python
def agregar(nombre, nombres):
    nombres.append(nombre)


productos = ["Teclado"]
agregar("Mouse", productos)

print(productos)
~~~

La lista original cambia porque ambas referencias permiten acceder al mismo objeto.

Si necesitas una lista nueva:

~~~python
def agregar_en_copia(nombre, nombres):
    copia = nombres.copy()
    copia.append(nombre)
    return copia


original = ["Teclado"]
nueva = agregar_en_copia("Mouse", original)

print("Original:", original)
print("Nueva:", nueva)
~~~

`copy()` hace una copia superficial. En este ejemplo, con cadenas de texto, permite ver claramente la diferencia.

### Ejercicio — Consultas de inventario

Crea una función `agotados(productos)` que reciba una lista de diccionarios y **devuelva** los nombres de productos cuya cantidad sea cero.

Haz primero la solución con `for` y `append()`. Luego intenta una comprensión de lista.

### Reto — Cuatro funciones sobre una colección

Crea `inventario_funciones.py` con una lista de tres productos.

Implementa:

- `mostrar_productos(productos)`: imprime los registros;
- `buscar_producto(productos, codigo)`: devuelve el diccionario encontrado o `None`;
- `valor_inventario(productos)`: devuelve el total de `precio * cantidad`;
- `productos_sin_stock(productos)`: devuelve una lista de nombres.

Llama a las cuatro funciones desde el programa principal. No necesitas un menú en este ejercicio.

### Comprobación

Explica la diferencia entre `print()` y `return`, entre parámetro y argumento, y entre modificar una lista que recibes y devolver una lista nueva.

---

## Práctica 3.2 — Módulos, importaciones y organización del código

### Objetivos

Al finalizar esta práctica podrás:

- usar funciones definidas en otro archivo;
- importar un módulo completo o funciones específicas;
- reconocer cuándo se ejecuta el código de un módulo;
- comprender `if __name__ == "__main__"`;
- distribuir tareas en archivos diferentes.

### 1. Primer proyecto con dos módulos

En `practica-3-2` crea:

~~~text
practica-3-2/
├── operaciones.py
└── main.py
~~~

En `operaciones.py` escribe:

~~~python
def calcular_subtotal(precio, cantidad):
    return precio * cantidad


def calcular_descuento(subtotal, porcentaje):
    return subtotal * porcentaje / 100
~~~

En `main.py`:

~~~python
import operaciones

subtotal = operaciones.calcular_subtotal(85000, 3)
descuento = operaciones.calcular_descuento(subtotal, 10)

print("Subtotal:", subtotal)
print("Descuento:", descuento)
print("Total:", subtotal - descuento)
~~~

Ejecuta `main.py`.

Resultado esperado:

~~~text
Subtotal: 255000
Descuento: 25500.0
Total: 229500.0
~~~

El punto en `operaciones.calcular_subtotal(...)` indica que la función pertenece al módulo `operaciones`.

### 2. Importar funciones específicas

También puedes escribir:

~~~python
from operaciones import calcular_subtotal, calcular_descuento

subtotal = calcular_subtotal(40000, 2)
descuento = calcular_descuento(subtotal, 5)

print(subtotal - descuento)
~~~

Ambas formas son válidas.

En estos ejercicios evita `from operaciones import *`, porque dificulta reconocer de dónde viene cada nombre.

### Ejercicio — Módulo de conversiones

Crea:

~~~text
conversiones/
├── unidades.py
└── main.py
~~~

Define en `unidades.py` una función para convertir kilómetros a metros y otra para convertir Celsius a Fahrenheit.

Desde `main.py` solicita los datos al usuario e imprime los resultados. `unidades.py` no debe utilizar `input()` ni mostrar los resultados: solo debe realizar cálculos.

### 3. Código que se ejecuta al importar

Crea `saludos.py`:

~~~python
def saludar(nombre):
    return f"Hola, {nombre}"


print("Se ejecutó el módulo saludos.")
~~~

En `main.py`:

~~~python
import saludos

print(saludos.saludar("Laura"))
~~~

Ejecuta `main.py` y observa que el mensaje colocado fuera de la función en `saludos.py` también aparece. Python ejecuta el código de nivel superior cuando importa el módulo, normalmente una vez por proceso.

En un archivo destinado a ser importado, conviene evitar que se inicien programas de demostración o lecturas de teclado inesperadas.

### 4. El punto de entrada

Modifica `saludos.py`:

~~~python
def saludar(nombre):
    return f"Hola, {nombre}"


if __name__ == "__main__":
    print(saludar("Prueba"))
~~~

Ejecuta primero `saludos.py` directamente y después `main.py`, que lo importa.

Cuando un archivo se ejecuta directamente, `__name__` vale `"__main__"`. Cuando se importa, tiene su nombre de módulo.

#### Antes de ejecutar — Predice

En `mensajes.py`:

~~~python
def despedir():
    return "Hasta luego"


if __name__ == "__main__":
    print("Prueba directa")
~~~

En `main.py`:

~~~python
from mensajes import despedir

print(despedir())
~~~

¿Qué aparece al ejecutar `main.py`? ¿Y al ejecutar `mensajes.py`?

### 5. Separar las validaciones

Crea `validaciones.py`:

~~~python
def precio_valido(precio):
    return precio > 0


def cantidad_valida(cantidad):
    return cantidad >= 0


def nombre_valido(nombre):
    return nombre.strip() != ""
~~~

En `main.py`:

~~~python
import validaciones

nombre = input("Nombre: ")
precio = float(input("Precio: "))
cantidad = int(input("Cantidad: "))

if (
    validaciones.nombre_valido(nombre)
    and validaciones.precio_valido(precio)
    and validaciones.cantidad_valida(cantidad)
):
    print("Datos aceptados.")
else:
    print("Revisa los datos.")
~~~

Por ahora suponemos que los datos numéricos pueden convertirse. Antes del reto final aprenderás a manejar una entrada incorrecta.

### Ejercicio — Categoría válida

Agrega una función `categoria_valida(categoria, categorias)` a `validaciones.py`.

Utiliza:

~~~python
CATEGORIAS = ("Periféricos", "Componentes", "Accesorios")
~~~

La función debe devolver `True` cuando la categoría se encuentre en la tupla y `False` cuando no esté presente.

Compruébala desde `main.py`.

### Depuración — Importación incorrecta

Si tu archivo contiene:

~~~python
import operaciones

print(calcular_subtotal(10000, 3))
~~~

¿por qué aparece un error si la función sí existe dentro de `operaciones.py`? Corrige la llamada sin copiar la función a `main.py`.

Evita además nombrar archivos `json.py`, `random.py` o `math.py`: podrían interferir con importaciones de módulos estándar de Python.

### Reto — Cotizador modular

Organiza el cotizador del Laboratorio 1 en:

~~~text
cotizador_modular/
├── main.py
├── calculos.py
└── validaciones.py
~~~

`calculos.py` debe contener subtotal, descuento e impuesto. `validaciones.py` comprobará precio, cantidad y porcentajes. `main.py` pedirá datos y mostrará el resultado.

Con precio `100000`, cantidad `2`, descuento `10` e impuesto `19`, el total final debe ser `214200`.

Comprueba que importar `calculos.py` no solicite entradas ni ejecute el cotizador.

### Comprobación

Debes poder explicar qué archivo se ejecuta, qué significa importar, cuándo elegir `import modulo` frente a `from modulo import funcion` y para qué sirve `if __name__ == "__main__"`.

---

## Práctica 3.3 — Clases, objetos, `__init__` y `self`

### Objetivos

Al finalizar esta práctica podrás:

- definir una clase;
- crear objetos;
- inicializar atributos con `__init__`;
- escribir métodos de instancia;
- utilizar `self`;
- comprobar que distintos objetos mantienen estados independientes.

### 1. Del diccionario al objeto

En el Laboratorio 2, un producto podía representarse así:

~~~python
producto = {
    "codigo": "P001",
    "nombre": "Teclado",
    "precio": 85000,
    "cantidad": 4
}
~~~

El diccionario agrupa datos. Pero también necesitamos calcular el valor del producto y cambiar sus existencias respetando reglas.

Una clase permite representar juntos los datos y comportamientos de ese concepto.

### 2. Primera clase

Crea `primer_producto.py`:

~~~python
class Producto:
    pass


teclado = Producto()
print(type(teclado))
~~~

`class` define la clase y `Producto()` crea un objeto. `pass` deja temporalmente vacío un bloque.

### 3. Inicializar atributos

Reemplaza el ejemplo:

~~~python
class Producto:
    def __init__(self, codigo, nombre, precio, cantidad):
        self.codigo = codigo
        self.nombre = nombre
        self.precio = precio
        self.cantidad = cantidad


teclado = Producto("P001", "Teclado", 85000, 4)

print(teclado.codigo)
print(teclado.nombre)
print(teclado.precio)
print(teclado.cantidad)
~~~

`__init__` inicializa los atributos de un objeto recién creado. `self` representa al objeto que ejecuta el método.

Observa la diferencia entre `self.nombre`, atributo del objeto, y `nombre`, parámetro recibido.

No escribes `self` al crear el objeto; Python lo proporciona automáticamente al invocar el inicializador.

### 4. Métodos de instancia

Agrega dentro de la clase anterior, al mismo nivel de indentación que `__init__`:

~~~python
    def mostrar_detalle(self):
        print("Código:", self.codigo)
        print("Producto:", self.nombre)
        print("Precio:", self.precio)
        print("Cantidad:", self.cantidad)

    def calcular_valor(self):
        return self.precio * self.cantidad
~~~

Después del bloque de clase, prueba:

~~~python
teclado.mostrar_detalle()
print("Valor:", teclado.calcular_valor())
~~~

Resultado esperado del valor: `340000`.

### 5. Dos objetos de la misma clase

Agrega:

~~~python
mouse = Producto("P002", "Mouse", 45000, 7)

print("Valor del teclado:", teclado.calcular_valor())
print("Valor del mouse:", mouse.calcular_valor())
~~~

Calcula primero ambos importes.

Después modifica:

~~~python
mouse.cantidad = 2
~~~

Vuelve a consultar la cantidad de `teclado`. Comprueba que no cambió.

### Ejercicio — Clase Estudiante

Crea `estudiante.py` y define `Estudiante` con nombre, ficha y promedio.

Incluye `mostrar_perfil()` y `aprobo()`, que debe devolver un booleano según si el promedio es al menos 3.0.

Crea dos objetos con promedios 2.8 y 4.1. Comprueba ambos resultados.

### 6. Función o método: decide según la responsabilidad

Compara:

~~~python
def calcular_valor(precio, cantidad):
    return precio * cantidad
~~~

con este método dentro de una clase:

~~~python
def calcular_valor(self):
    return self.precio * self.cantidad
~~~

La función recibe todos los datos como argumentos. El método trabaja con el estado de su objeto.

No todas las funciones deben convertirse en métodos. Conserva como funciones las tareas que no pertenecen naturalmente a un objeto concreto.

### 7. Atributo de clase y atributo de instancia

Ejecuta:

~~~python
class Registro:
    elementos = []


primero = Registro()
segundo = Registro()

primero.elementos.append("Teclado")
print(segundo.elementos)
~~~

¿Por qué aparece `Teclado` en el segundo objeto?

`elementos` es una lista mutable colocada como atributo de clase y compartida entre instancias que no tienen un atributo propio del mismo nombre.

Corrige con:

~~~python
class Registro:
    def __init__(self):
        self.elementos = []


primero = Registro()
segundo = Registro()

primero.elementos.append("Teclado")

print("Primero:", primero.elementos)
print("Segundo:", segundo.elementos)
~~~

Este detalle será fundamental para representar un inventario independiente.

### Depuración — Falta `self`

~~~python
class Saludo:
    def __init__(self, nombre):
        self.nombre = nombre

    def mostrar():
        print("Hola")


saludo = Saludo("Ana")
saludo.mostrar()
~~~

Ejecuta, lee el error y corrige la firma del método. Después haz que muestre el nombre.

### Depuración — Atributo inexistente

~~~python
class Persona:
    def __init__(self, nombre):
        self.nombre = nombre

    def mostrar(self):
        print(self.nombree)
~~~

Identifica el problema y corrige solamente el nombre del atributo.

### Reto — Billetera orientada a objetos

Crea una clase `Billetera` con titular, saldo inicial en cero y métodos para registrar ingresos, gastos y consultar saldo.

Rechaza montos no positivos y gastos superiores al saldo.

Crea dos objetos con titulares diferentes. Realiza varias operaciones y demuestra que el estado de uno no altera el del otro.

### Comprobación

Antes de continuar explica qué representa `self`, para qué se utiliza `__init__` y por qué `self.elementos = []` evita que dos objetos compartan accidentalmente una lista.

---

## Práctica 3.4 — Métodos, encapsulamiento, propiedades y colaboración

### Objetivos

Al finalizar esta práctica podrás:

- controlar modificaciones de los datos de un objeto;
- reconocer la convención de atributos internos con `_atributo`;
- consultar atributos mediante `@property`;
- validar modificaciones con métodos o setters;
- distribuir tareas entre dos clases;
- atender de forma básica una entrada numérica inválida.

### 1. ¿Qué ocurre con los cambios sin control?

En la primera clase `Producto` podías escribir:

~~~python
teclado.precio = -5000
teclado.cantidad = -10
~~~

Esas asignaciones son válidas para atributos públicos, pero no tienen sentido para un inventario.

En Python, un atributo cuyo nombre comienza con un guion bajo se considera **interno por convención**. No es inaccesible de forma estricta, pero otras partes del programa deberían utilizar las operaciones que ofrece la clase.

### 2. Primera versión con atributos internos

Crea `producto_seguro.py`:

~~~python
class Producto:
    def __init__(self, codigo, nombre, precio, cantidad):
        self._codigo = codigo
        self._nombre = nombre
        self._precio = precio
        self._cantidad = cantidad

    def calcular_valor(self):
        return self._precio * self._cantidad


producto = Producto("P001", "Teclado", 85000, 4)
print(producto.calcular_valor())
~~~

Todavía no estamos validando los datos iniciales. Ese será uno de los requisitos del reto final.

### 3. Propiedades de consulta

Dentro de `Producto`, debajo de `calcular_valor()`, agrega:

~~~python
    @property
    def codigo(self):
        return self._codigo

    @property
    def nombre(self):
        return self._nombre

    @property
    def cantidad(self):
        return self._cantidad
~~~

Después, fuera de la clase, prueba:

~~~python
print(producto.codigo)
print(producto.nombre)
print(producto.cantidad)
~~~

Las propiedades se consultan sin paréntesis.

Intenta:

~~~python
producto.codigo = "P999"
~~~

Sin un setter, esa propiedad no admite esa asignación. Así evitamos que el código identificador se cambie libremente mediante la interfaz pública.

### 4. Modificar existencias mediante un método

Agrega a la clase:

~~~python
    def actualizar_existencias(self, nueva_cantidad):
        if nueva_cantidad < 0:
            return False

        self._cantidad = nueva_cantidad
        return True
~~~

Comprueba:

~~~python
print(producto.actualizar_existencias(10))
print(producto.cantidad)

print(producto.actualizar_existencias(-5))
print(producto.cantidad)
~~~

El segundo intento debe rechazarse sin modificar la cantidad.

En este ejemplo se supone que el argumento ya es un entero. En la aplicación final, el programa principal será responsable de convertir los datos ingresados.

### 5. Un setter para el precio

Agrega a la clase:

~~~python
    @property
    def precio(self):
        return self._precio

    @precio.setter
    def precio(self, nuevo_precio):
        if nuevo_precio <= 0:
            raise ValueError("El precio debe ser mayor que cero.")

        self._precio = nuevo_precio
~~~

Luego prueba:

~~~python
producto.precio = 90000
print(producto.precio)
~~~

Intenta asignar un precio negativo y observa la excepción `ValueError`.

**Observación:** el setter controla cambios posteriores, pero el constructor de este ejemplo asigna `self._precio` directamente. En la actividad integradora deberás validar también los valores iniciales, porque un producto no debería nacer con precio o cantidad inválidos.

### Ejercicio — Cuenta de puntos

Crea `CuentaPuntos` con atributos internos para titular y puntos. Los puntos comienzan en cero.

Agrega propiedades de consulta y métodos `sumar(cantidad)` y `canjear(cantidad)` que devuelvan `True` o `False`.

Prueba:

~~~text
Sumar: 80     -> aceptado
Canjear: 30   -> aceptado
Canjear: 100  -> rechazado
Total: 50
~~~

### 6. Dos clases que colaboran

En una subcarpeta `relacion_objetos` crea:

~~~text
relacion_objetos/
├── producto.py
├── inventario.py
└── main.py
~~~

En `producto.py`:

~~~python
class Producto:
    def __init__(self, codigo, nombre):
        self.codigo = codigo
        self.nombre = nombre
~~~

En `inventario.py`:

~~~python
class Inventario:
    def __init__(self):
        self._productos = []

    def agregar(self, producto):
        self._productos.append(producto)

    def listar(self):
        return self._productos.copy()
~~~

En `main.py`:

~~~python
from producto import Producto
from inventario import Inventario

inventario = Inventario()

inventario.agregar(Producto("P001", "Teclado"))
inventario.agregar(Producto("P002", "Mouse"))

for producto in inventario.listar():
    print(producto.codigo, "-", producto.nombre)
~~~

Resultado esperado:

~~~text
P001 - Teclado
P002 - Mouse
~~~

`Inventario` conserva objetos de tipo `Producto`, no diccionarios. La copia de `listar()` evita que se cambie directamente la estructura de la lista interna, aunque los objetos contenidos siguen siendo los mismos.

### 7. Buscar un objeto por código

Dentro de `Inventario` agrega:

~~~python
    def buscar_por_codigo(self, codigo):
        for producto in self._productos:
            if producto.codigo == codigo:
                return producto

        return None
~~~

En `main.py`, después del código anterior:

~~~python
encontrado = inventario.buscar_por_codigo("P002")

if encontrado is not None:
    print("Encontrado:", encontrado.nombre)
else:
    print("Producto no registrado.")
~~~

La clase `Inventario` conoce cómo buscar. La clase `Producto` conoce los datos de cada artículo. `main.py` presenta el resultado.

### 8. Manejo básico de una entrada incorrecta

Crea `leer_precio.py`:

~~~python
try:
    precio = float(input("Precio: "))
    print("Precio ingresado:", precio)
except ValueError:
    print("Debes escribir un número válido.")
~~~

Ejecuta una vez con `85000` y otra con `abc`.

`try` intenta convertir y `except ValueError` atiende ese error concreto. No es necesario capturar indiscriminadamente cualquier tipo de error.

### Ejercicio — Reintentar una cantidad

Crea `leer_cantidad.py` con un `while` que vuelva a solicitar un entero hasta que pueda convertirse y sea no negativo.

Ejemplo esperado:

~~~text
Cantidad: abc
Debes escribir un entero.

Cantidad: -2
La cantidad no puede ser negativa.

Cantidad: 5
Cantidad aceptada: 5
~~~

### Depuración — Lista compartida

~~~python
class Inventario:
    productos = []

    def agregar(self, producto):
        self.productos.append(producto)


primero = Inventario()
segundo = Inventario()

primero.agregar("Teclado")
print(segundo.productos)
~~~

El segundo inventario debería estar vacío. Corrige la ubicación en la que se crea la lista.

### Reto — Inventario con reglas

Amplía el ejemplo de `relacion_objetos`:

- `agregar(producto)` no debe permitir códigos duplicados;
- `buscar_por_codigo(codigo)` devuelve un objeto o `None`;
- `eliminar(codigo)` debe quitar el objeto cuando exista y devolver un resultado booleano.

Prueba dos productos válidos, uno con código repetido y una eliminación de un código que no exista. No hagas todavía el menú completo.

### Comprobación

Debes poder explicar por qué `Inventario` conserva una colección de objetos, por qué `_productos` se considera interno y cuál es la diferencia entre asignar libremente un atributo y utilizar un método que comprueba reglas.

---

## Práctica 3.5 — Reto integrador: gestor de inventario orientado a objetos

Vas a transformar el gestor del Laboratorio 2 en una aplicación organizada mediante funciones, módulos y clases.

Antes guardabas productos como diccionarios dentro de una lista. Ahora cada registro será una instancia de `Producto` y su administración corresponderá a una instancia de `Inventario`.

### Objetivo

Construir un sistema de consola que registre, liste, busque, actualice y elimine productos, además de generar informes de inventario. Debe utilizar clases y métodos de instancia, propiedades, validaciones, funciones e importaciones entre módulos.

### 1. Archivos del proyecto

En la carpeta `gestor_inventario` crea:

~~~text
gestor_inventario/
├── main.py
├── producto.py
├── inventario.py
└── README.md
~~~

No necesitas instalar dependencias. Los archivos Python estarán en la misma carpeta.

### 2. Responsabilidad de cada archivo

| Archivo | Responsabilidad |
|---|---|
| `producto.py` | Clase `Producto`: datos, validaciones individuales y valor de existencias |
| `inventario.py` | Clase `Inventario`: colección de productos, operaciones y reportes |
| `main.py` | Funciones de interacción, menú, lectura y presentación de resultados |
| `README.md` | Descripción, ejecución y pruebas realizadas |

Ni `Producto` ni `Inventario` deben utilizar `input()` ni imprimir el menú. El usuario interactúa desde `main.py`.

### 3. Reglas de la clase `Producto`

Un producto debe tener:

- código;
- nombre;
- categoría;
- precio unitario;
- cantidad disponible.

Ejemplo de creación:

~~~python
producto = Producto(
    "P001",
    "Teclado",
    "Periféricos",
    85000,
    4
)
~~~

La clase debe:

1. inicializar sus datos mediante `__init__`;
2. rechazar códigos y nombres vacíos, incluidos los que contienen únicamente espacios;
3. rechazar categorías no autorizadas;
4. rechazar precios no positivos;
5. rechazar cantidades negativas;
6. ofrecer propiedades de consulta;
7. evitar que el código se cambie libremente;
8. permitir actualizar existencias mediante un método;
9. calcular `precio * cantidad` mediante un método de instancia.

Las validaciones deben aplicarse desde la creación del objeto, no únicamente cuando se actualizan sus atributos.

Puedes utilizar `ValueError` para indicar datos iniciales inválidos. `main.py` debe capturar ese error al registrar un producto y mostrar un mensaje comprensible.

### 4. Categorías permitidas

Conserva las mismas categorías del laboratorio anterior:

~~~python
CATEGORIAS = ("Periféricos", "Componentes", "Accesorios")
~~~

Puedes declararlas en `producto.py` y usarlas al construir objetos. Evita mantener varias copias con valores diferentes.

### 5. Reglas de la clase `Inventario`

Cada objeto de esta clase tendrá su propia lista:

~~~python
self._productos = []
~~~

Debe ofrecer operaciones semejantes a:

~~~python
registrar_producto(producto)
listar_productos()
buscar_por_codigo(codigo)
actualizar_existencias(codigo, nueva_cantidad)
eliminar_producto(codigo)
productos_sin_stock()
resumen()
~~~

Puedes elegir nombres equivalentes, siempre que sean claros.

Reglas:

- los códigos no pueden repetirse;
- una búsqueda debe devolver el objeto correspondiente o `None`;
- las operaciones de registro, actualización y eliminación deben indicar si fueron aceptadas;
- una consulta no debe agregar ni eliminar productos;
- las actualizaciones de existencias deben respetar las reglas de `Producto`;
- los datos de un inventario no deben mezclarse con los de otra instancia.

### 6. Menú principal

~~~text
================================
       GESTOR DE INVENTARIO
================================
1. Registrar producto
2. Listar productos
3. Buscar por código
4. Actualizar existencias
5. Eliminar producto
6. Consultar productos sin stock
7. Resumen del inventario
0. Salir
~~~

El menú debe mantenerse hasta seleccionar `0`.

### 7. Comportamientos esperados

**Registrar.** Solicita los cinco datos y rechaza códigos repetidos o cualquier dato inválido. No guardes registros parciales.

**Listar.** Presenta código, nombre, categoría, precio y cantidad. Muestra un mensaje cuando la colección esté vacía.

**Buscar.** Solicita código y presenta el producto o informa que no existe.

**Actualizar.** Localiza el producto, solicita nueva cantidad y cambia solo las existencias. Acepta cero, pero no números negativos.

**Eliminar.** Quita el producto encontrado. Cuando el código no exista, debe informar sin interrumpir la ejecución.

**Sin stock.** Obtiene los productos cuya cantidad es cero. Utiliza una comprensión de lista.

**Resumen.** Calcula cantidad de productos diferentes, unidades disponibles, valor total del inventario y categorías utilizadas. Utiliza una comprensión de conjunto para las categorías.

### 8. Antes de programar

Escribe en comentarios:

~~~python
# Producto:
#   representa un artículo y controla sus valores
#
# Inventario:
#   conserva varios objetos Producto
#   registra, busca, actualiza, elimina y consulta
#
# main:
#   solicita datos
#   muestra menú
#   informa los resultados
~~~

Responde antes de programar:

- ¿Qué clase debe evitar los códigos repetidos?
- ¿Qué clase debe comprobar que la cantidad sea válida?
- ¿Qué clase calcula el valor de un producto?
- ¿Dónde debe utilizarse `input()`?
- ¿Quién debe decidir qué mensaje se muestra después de una operación?

### 9. Esqueleto de `producto.py`

Este es un punto de partida **incompleto**. Debes escribir las validaciones y completar los métodos pendientes.

~~~python
class Producto:
    def __init__(self, codigo, nombre, categoria, precio, cantidad):
        # Validar los datos antes de aceptarlos.
        self._codigo = codigo
        self._nombre = nombre
        self._categoria = categoria
        self._precio = precio
        self._cantidad = cantidad

    @property
    def codigo(self):
        return self._codigo

    @property
    def nombre(self):
        return self._nombre

    @property
    def categoria(self):
        return self._categoria

    @property
    def precio(self):
        return self._precio

    @property
    def cantidad(self):
        return self._cantidad

    def actualizar_existencias(self, nueva_cantidad):
        # Rechaza valores negativos.
        # Si es válido, actualiza y devuelve True.
        pass

    def calcular_valor(self):
        # Devuelve precio por cantidad.
        pass
~~~

Recuerda que los `pass` no son soluciones y deben ser sustituidos.

### 10. Esqueleto de `inventario.py`

~~~python
class Inventario:
    def __init__(self):
        self._productos = []

    def registrar_producto(self, producto):
        # No aceptar códigos duplicados.
        pass

    def listar_productos(self):
        # Devolver la información sin exponer la lista para modificarla directamente.
        pass

    def buscar_por_codigo(self, codigo):
        # Devolver un Producto o None.
        pass

    def actualizar_existencias(self, codigo, nueva_cantidad):
        # Buscar y aplicar las reglas del Producto.
        pass

    def eliminar_producto(self, codigo):
        # Eliminar si existe.
        pass

    def productos_sin_stock(self):
        # Utilizar una comprensión de lista.
        pass

    def resumen(self):
        # Devolver cantidad, unidades, valor y categorías.
        pass
~~~

No coloques `_productos = []` como una lista mutable compartida en el cuerpo de la clase.

### 11. Esqueleto de `main.py`

~~~python
from producto import Producto
from inventario import Inventario


def mostrar_menu():
    print()
    print("================================")
    print("       GESTOR DE INVENTARIO")
    print("================================")
    print("1. Registrar producto")
    print("2. Listar productos")
    print("3. Buscar por código")
    print("4. Actualizar existencias")
    print("5. Eliminar producto")
    print("6. Consultar productos sin stock")
    print("7. Resumen del inventario")
    print("0. Salir")


def main():
    inventario = Inventario()
    opcion = ""

    while opcion != "0":
        mostrar_menu()
        opcion = input("Opción: ").strip()

        if opcion == "1":
            # Leer datos, construir Producto y registrarlo.
            pass
        elif opcion == "2":
            # Listar registros.
            pass
        elif opcion == "3":
            # Buscar por código.
            pass
        elif opcion == "4":
            # Actualizar existencias.
            pass
        elif opcion == "5":
            # Eliminar registro.
            pass
        elif opcion == "6":
            # Mostrar productos agotados.
            pass
        elif opcion == "7":
            # Mostrar resumen.
            pass
        elif opcion == "0":
            print("Programa finalizado.")
        else:
            print("Opción no válida.")


if __name__ == "__main__":
    main()
~~~

No intentes ejecutar todas las opciones sin antes implementar el comportamiento de las clases. Trabaja por etapas.

### 12. Leer números sin detener el programa

Puedes agregar esta función reutilizable en `main.py`:

~~~python
def leer_cantidad():
    while True:
        try:
            cantidad = int(input("Cantidad: "))
        except ValueError:
            print("Debes escribir un número entero.")
            continue

        if cantidad < 0:
            print("La cantidad no puede ser negativa.")
            continue

        return cantidad
~~~

`continue` vuelve al inicio del ciclo; `return` entrega el valor válido y termina la función.

Construye una función semejante para leer precios positivos mediante `float()`.

### 13. Construcción por etapas

**Etapa 1 — Producto.** Completa atributos, validaciones, propiedades y cálculo del valor. Comprueba un producto válido y uno con precio negativo.

**Etapa 2 — Inventario.** Implementa registro y búsqueda. Prueba que no puedan duplicarse códigos.

**Etapa 3 — Modificaciones.** Implementa actualización y eliminación. Revisa que una operación sobre un producto no cambie datos de otro.

**Etapa 4 — Informes.** Calcula productos sin stock, unidades, valor del inventario y categorías.

**Etapa 5 — Menú.** Conecta desde `main.py` las operaciones que ya probaste.

**Etapa 6 — Validaciones de entrada.** Comprueba entradas vacías, números inválidos y cantidades negativas.

**Etapa 7 — Independencia.** Crea dos inventarios y verifica que una operación sobre uno no modifique el otro.

Ejecuta después de cada etapa. Un error localizado al agregar una sola operación es más sencillo de corregir que varios errores mezclados.

### 14. Pistas

Para comprobar un código duplicado dentro de `Inventario`:

~~~python
if self.buscar_por_codigo(producto.codigo) is not None:
    return False
~~~

Para consultar nombres de productos agotados:

~~~python
agotados = [
    producto.nombre
    for producto in self._productos
    if producto.cantidad == 0
]
~~~

Para contar unidades:

~~~python
unidades = sum(producto.cantidad for producto in self._productos)
~~~

Para el valor del inventario:

~~~python
valor = sum(producto.calcular_valor() for producto in self._productos)
~~~

Para categorías utilizadas:

~~~python
categorias = {producto.categoria for producto in self._productos}
~~~

No copies todas estas pistas dentro de `main.py`. Determina cuál clase debe realizar cada operación.

### 15. Ejemplo de ejecución

~~~text
Opción: 1
Código: P001
Nombre: Teclado
Categoría: Periféricos
Precio: 85000
Cantidad: 4
Producto registrado.

Opción: 1
Código: P002
Nombre: Mouse
Categoría: Periféricos
Precio: 45000
Cantidad: 0
Producto registrado.

Opción: 2
P001 - Teclado - Periféricos - $85000.0 - 4 unidades
P002 - Mouse - Periféricos - $45000.0 - 0 unidades

Opción: 6
Sin existencias:
- Mouse

Opción: 7
Productos registrados: 2
Unidades disponibles: 4
Valor del inventario: $340000.0
Categorías utilizadas: Periféricos

Opción: 0
Programa finalizado.
~~~

### 16. Pruebas obligatorias

| Caso | Resultado esperado |
|---|---|
| Iniciar inventario vacío | Listado vacío y resumen en cero |
| Registrar P001 | Registro aceptado |
| Registrar nuevamente P001 | Registro rechazado |
| Registrar código o nombre vacío | Registro rechazado |
| Categoría no permitida | Registro rechazado |
| Precio cero o negativo | Registro rechazado |
| Cantidad negativa | Registro rechazado |
| Cantidad cero | Producto válido y sin existencias |
| Buscar P001 | Mostrar su información |
| Buscar P999 | Informar que no existe |
| Actualizar a 10 unidades | Modificar solo la cantidad |
| Actualizar a -3 unidades | Rechazar, conservar el dato anterior |
| Eliminar producto existente | Quitar el registro |
| Eliminar código inexistente | Informar sin fallar |
| Ingresar texto en un campo numérico | Mensaje y nueva oportunidad |
| Consultar resumen | Totales coherentes |
| Salir | Terminar el ciclo |

### 17. Comprobación numérica

Registra estos productos:

| Código | Precio | Cantidad | Categoría |
|---|---:|---:|---|
| P001 | 85000 | 4 | Periféricos |
| P002 | 45000 | 0 | Periféricos |
| P003 | 700000 | 2 | Componentes |

Resultados esperados:

~~~text
Productos registrados: 3
Unidades disponibles: 6
Valor del inventario: $1740000.0
Categorías utilizadas: 2
Producto sin stock: Mouse
~~~

Actualiza P002 a cinco unidades y vuelve a comprobar:

~~~text
Productos registrados: 3
Unidades disponibles: 11
Valor del inventario: $1965000.0
~~~

Si el resultado no coincide, revisa primero el objeto modificado y después el método que construye el resumen.

### 18. Prueba de independencia

Crea `pruebas_independencia.py`:

~~~python
from inventario import Inventario
from producto import Producto

primero = Inventario()
segundo = Inventario()

primero.registrar_producto(
    Producto("P001", "Teclado", "Periféricos", 85000, 4)
)

print("Primero:", len(primero.listar_productos()))
print("Segundo:", len(segundo.listar_productos()))
~~~

Resultado esperado:

~~~text
Primero: 1
Segundo: 0
~~~

Si ambos inventarios muestran un producto, revisa dónde inicializaste la colección.

### 19. Reto de ampliación

Después de superar las pruebas principales puedes:

- buscar productos por categoría;
- mostrar los productos ordenados por nombre;
- permitir actualizar precios con una validación;
- mostrar el producto con mayor valor de inventario;
- generar con una comprensión un diccionario de código a nombre;
- construir una función de reporte que presente los valores devueltos por `Inventario`.

No necesitas herencia, clases abstractas, bases de datos ni archivos JSON para completar este laboratorio.

### 20. Depuración

Trabaja sobre una copia del proyecto y provoca, uno por uno, estos errores:

1. crea `_productos` como lista compartida a nivel de clase;
2. intenta consultar una propiedad que no existe;
3. omite `self` en un método de instancia;
4. deja crear un producto con cantidad negativa;
5. elimina una instrucción de importación;
6. calcula el valor del inventario sumando precios sin multiplicar por cantidades.

Corrige cada caso y explica la causa mediante un comentario breve.

### 21. Revisión del proyecto

Antes de terminar comprueba que:

- `main.py` contiene la interacción con el usuario;
- `producto.py` representa cada producto y controla sus reglas;
- `inventario.py` gestiona una colección de objetos `Producto`;
- no se aceptan códigos duplicados;
- las operaciones rechazadas no modifican el inventario;
- las búsquedas no alteran los registros;
- los informes cambian cuando cambian las existencias;
- dos objetos `Inventario` mantienen datos independientes;
- el proyecto ejecuta desde una terminal limpia;
- puedes explicar la función de cada método y archivo.

### Evidencia de trabajo

Conserva los archivos de las prácticas y el proyecto final en tu repositorio de trabajo. Incluye en su `README.md`:

- descripción breve;
- estructura de archivos;
- comando de ejecución;
- responsabilidad de cada clase;
- pruebas realizadas y resultados observados.

No es necesario elaborar un informe PDF ni una presentación.

---

## Cierre del laboratorio

Al terminar debes poder definir funciones reutilizables, trabajar con valores de retorno, separar módulos, crear objetos e inicializarlos con `__init__`, comprender `self`, consultar datos mediante propiedades y distribuir las operaciones entre clases que colaboran.

Compara el nuevo gestor con el del Laboratorio 2. Las funciones principales son semejantes, pero ahora la lógica de cada producto y la administración del inventario están ubicadas en clases con responsabilidades distintas.

En próximos laboratorios podrás incorporar persistencia, pruebas automatizadas y otros conceptos de POO.

### Referencias

- [Tutorial de Python — Funciones](https://docs.python.org/3/tutorial/controlflow.html#defining-functions)
- [Tutorial de Python — Módulos](https://docs.python.org/3/tutorial/modules.html)
- [Tutorial de Python — Clases](https://docs.python.org/3/tutorial/classes.html)
- [Python — property](https://docs.python.org/3/library/functions.html#property)
