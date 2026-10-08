# Laboratorio 2 — Decisiones, ciclos y listas en Python

En el laboratorio anterior construiste programas que reciben datos, realizan cálculos y muestran resultados. Ahora vas a trabajar con programas que eligen entre varias acciones, repiten instrucciones y conservan varios valores en una lista.

El reto final será un gestor de productos con menú. Comenzarás guardando únicamente los nombres de los productos; más adelante podrás ampliar esa información con cantidades, precios y otras características.

| Práctica | Tema |
|---|---|
| 2.1 | Comparaciones y decisiones con `if`, `elif` y `else` |
| 2.2 | Condiciones compuestas y validaciones |
| 2.3 | Ciclos `for` y `while` |
| 2.4 | Listas y recorrido de elementos |
| 2.5 | Reto integrador: gestor de productos |

> Lee el ejemplo, predice lo que hará, ejecútalo y modifica los valores. Cuando llegues a un reto, intenta resolverlo antes de consultar las pistas. Trabaja con Python 3 en VS Code o con la alternativa de ejecución que utilizaste en el Laboratorio 1.

---

## Práctica 2.1 — Comparaciones y decisiones

### Objetivos

Al finalizar esta práctica podrás:

- comparar números y textos;
- reconocer expresiones que producen `True` o `False`;
- ejecutar instrucciones según una condición;
- seleccionar entre dos o más caminos;
- revisar la indentación de un bloque de código.

### 1. Comparar valores

Una comparación responde una pregunta cuyo resultado es verdadero o falso.

| Operador | Significado | Ejemplo |
|---|---|---|
| `>` | mayor que | `edad > 18` |
| `<` | menor que | `precio < 50000` |
| `>=` | mayor o igual que | `nota >= 3.0` |
| `<=` | menor o igual que | `cantidad <= 10` |
| `==` | igual a | `opcion == 1` |
| `!=` | diferente de | `opcion != 0` |

Crea `comparaciones.py`:

~~~python
edad = 20

print(edad > 18)
print(edad < 18)
print(edad >= 20)
print(edad == 20)
print(edad != 20)
~~~

**Antes de ejecutar:** escribe los cinco resultados que esperas obtener. Después ejecuta el archivo y compáralos.

Observa que `=` asigna un valor, mientras que `==` compara dos valores.

### 2. Guardar el resultado de una comparación

Ejecuta:

~~~python
nota = 3.8
aprobo = nota >= 3.0

print("Nota:", nota)
print("¿Aprobó?", aprobo)
~~~

Cambia la nota por `2.9`, `3.0` y `4.5`. Observa cuándo cambia el resultado.

#### Ejercicio — Disponibilidad de un producto

Crea `disponibilidad.py`.

Utiliza:

~~~python
unidades_disponibles = 12
unidades_solicitadas = 8
~~~

Guarda en variables de tipo booleano y muestra:

- si hay suficientes unidades;
- si las cantidades son iguales;
- si se solicitaron más unidades de las disponibles.

Cambia `unidades_solicitadas` por `12` y luego por `15`. Verifica cada caso.

### 3. Primera decisión con `if`

Hasta ahora mostramos el resultado de las comparaciones. Con `if` podemos decidir qué instrucciones ejecutar.

Crea `validar_edad.py`:

~~~python
edad = int(input("Edad: "))

if edad >= 18:
    print("Es mayor de edad.")

print("Fin del programa.")
~~~

Ejecuta el programa con `16` y después con `21`.

Fíjate en los espacios al comienzo de la línea del `print`. En Python, la indentación indica qué instrucciones pertenecen al `if`.

### 4. Dos caminos con `else`

Modifica el programa:

~~~python
edad = int(input("Edad: "))

if edad >= 18:
    print("Es mayor de edad.")
else:
    print("Es menor de edad.")
~~~

Prueba `17`, `18` y `25`.

#### Ejercicio — Número positivo, negativo o cero

Crea `clasificar_numero.py`.

Solicita un número entero. El programa debe mostrar uno de estos mensajes:

~~~text
El número es positivo.
El número es negativo.
El número es cero.
~~~

Antes de programar, piensa por qué aquí existen tres posibilidades y no dos.

### 5. Varias alternativas con `elif`

Cuando hay más de dos opciones, podemos evaluar varias condiciones en orden.

Crea `clasificar_nota.py`:

~~~python
nota = float(input("Nota: "))

if nota >= 4.5:
    print("Desempeño sobresaliente")
elif nota >= 4.0:
    print("Desempeño alto")
elif nota >= 3.0:
    print("Aprobado")
else:
    print("No aprobado")
~~~

Prueba:

| Nota | Resultado esperado |
|---:|---|
| 4.8 | Desempeño sobresaliente |
| 4.2 | Desempeño alto |
| 3.0 | Aprobado |
| 2.9 | No aprobado |

**Observa:** solamente se ejecuta el primer bloque cuya condición se cumple.

Cambia temporalmente el orden de las condiciones para poner `nota >= 3.0` antes de `nota >= 4.5`. Ejecuta con `4.8`. ¿Qué ocurrió? Restaura el orden original.

### 6. Comparar texto

Python también permite comparar cadenas:

~~~python
respuesta = input("Escribe SI para continuar: ")

if respuesta == "SI":
    print("Continuando...")
else:
    print("Respuesta diferente de SI.")
~~~

Prueba `SI`, `si` y `Si`.

Para comparar sin distinguir mayúsculas de minúsculas, puedes normalizar el texto:

~~~python
respuesta = input("Escribe SI para continuar: ").strip().lower()

if respuesta == "si":
    print("Continuando...")
else:
    print("Respuesta diferente de SI.")
~~~

`strip()` elimina espacios al inicio y al final. `lower()` convierte el texto a minúsculas.

### Depuración — Revisa la indentación

Copia en `error_indentacion.py`:

~~~python
precio = 80000

if precio >= 50000:
print("La compra supera el valor indicado.")

print("Programa terminado.")
~~~

Ejecuta y lee el mensaje de error. Corrige la indentación.

Luego prueba colocar el último `print` dentro del bloque `if`. ¿Cómo cambia el comportamiento cuando el precio es `30000`?

### Reto — Calculadora de envío

Crea `costo_envio.py`.

Solicita el valor de una compra y calcula el costo de envío según estas reglas:

| Compra | Envío |
|---:|---:|
| Desde $200.000 | Gratis |
| Desde $100.000 hasta menos de $200.000 | $10.000 |
| Menos de $100.000 | $18.000 |

Muestra el valor de la compra, el costo del envío y el total que debe pagar el cliente.

Prueba los valores `99999`, `100000`, `199999` y `200000`.

### Comprobación

Antes de continuar verifica que puedes explicar la diferencia entre `=` y `==`, el papel de la indentación, cuándo usar `elif` y por qué el orden de las condiciones puede cambiar un resultado.

---

## Práctica 2.2 — Condiciones compuestas y validaciones

### Objetivos

Al finalizar esta práctica podrás:

- combinar condiciones con `and`, `or` y `not`;
- comprobar varios requisitos al mismo tiempo;
- identificar valores situados en los límites de una regla;
- rechazar una operación cuando los datos no cumplen una condición;
- organizar validaciones mediante `if` y `else`.

### 1. El operador `and`

Un usuario puede acceder a una actividad cuando está inscrito **y** tiene el documento requerido.

Crea `acceso_actividad.py`:

~~~python
esta_inscrito = True
tiene_documento = False

if esta_inscrito and tiene_documento:
    print("Puede ingresar.")
else:
    print("No cumple todos los requisitos.")
~~~

Prueba las cuatro combinaciones posibles de `True` y `False`.

Para que la condición sea verdadera, ambos requisitos deben cumplirse.

### 2. El operador `or`

En algunos casos basta con cumplir una de varias alternativas.

~~~python
tiene_carnet = False
tiene_autorizacion = True

if tiene_carnet or tiene_autorizacion:
    print("Puede ingresar.")
else:
    print("No puede ingresar.")
~~~

Prueba cuando ambos valores son falsos.

### 3. El operador `not`

`not` permite negar una expresión booleana.

~~~python
bloqueado = False

if not bloqueado:
    print("La cuenta está habilitada.")
else:
    print("La cuenta está bloqueada.")
~~~

### Antes de ejecutar — Predice

Analiza el siguiente código:

~~~python
edad = 17
acompanado = True

print(edad >= 18)
print(edad >= 18 or acompanado)
print(edad >= 18 and acompanado)
print(not acompanado)
~~~

Escribe los cuatro resultados antes de ejecutar.

### Ejercicio — Acceso a una sala

Crea `acceso_sala.py`.

Solicita la edad y pregunta si la persona tiene autorización. Para representar la autorización utiliza una respuesta de texto, como `si` o `no`.

Reglas:

- las personas de 18 años o más pueden ingresar;
- las menores pueden ingresar únicamente con autorización;
- en los demás casos, el acceso no se permite.

Prueba al menos cuatro combinaciones.

**Pista:** convierte la respuesta con `strip().lower()` antes de compararla.

### 4. Trabajar con rangos

Un valor puede necesitar estar dentro de un intervalo.

~~~python
nota = float(input("Nota: "))

if 0.0 <= nota <= 5.0:
    print("Nota dentro del rango.")
else:
    print("La nota está fuera del rango permitido.")
~~~

En Python podemos escribir comparaciones encadenadas como `0 <= nota <= 5`.

#### Ejercicio — Edad permitida

Crea `rango_edad.py`.

Solicita una edad e indica si está entre 18 y 65 años, incluidos ambos límites.

Comprueba `17`, `18`, `65` y `66`.

### 5. Validar antes de calcular

Retoma el cotizador del Laboratorio 1.

¿Qué ocurre si el usuario escribe una cantidad negativa o un descuento de 150 %? El código probablemente calcula un resultado, pero no uno que tenga sentido para la operación.

Crea `validar_compra.py`:

~~~python
precio = float(input("Precio: "))
cantidad = int(input("Cantidad: "))
descuento = float(input("Descuento (%): "))

if precio <= 0 or cantidad <= 0:
    print("El precio y la cantidad deben ser mayores que cero.")
elif descuento < 0 or descuento > 100:
    print("El descuento debe estar entre 0 y 100.")
else:
    subtotal = precio * cantidad
    valor_descuento = subtotal * descuento / 100
    total = subtotal - valor_descuento

    print("Subtotal:", subtotal)
    print("Descuento:", valor_descuento)
    print("Total:", total)
~~~

Ejecuta estos casos:

| Precio | Cantidad | Descuento | Comportamiento |
|---:|---:|---:|---|
| 100000 | 2 | 10 | Calcular compra |
| -50000 | 2 | 10 | Rechazar precio |
| 50000 | 0 | 5 | Rechazar cantidad |
| 80000 | 1 | 110 | Rechazar descuento |

Este programa comprueba reglas del negocio, pero todavía no maneja errores de conversión como escribir `abc` en un campo numérico. Ese problema se trabajará cuando estudiemos excepciones.

### Depuración — Una condición equivocada

Este programa debería permitir un descuento solamente si el cliente tiene membresía **y** la compra es de al menos $150.000:

~~~python
membresia = False
valor_compra = 200000

if membresia or valor_compra >= 150000:
    print("Aplica descuento.")
else:
    print("No aplica descuento.")
~~~

Ejecuta, encuentra el problema y corrige la condición.

### Reto — Clasificación de solicitudes

Crea `evaluar_solicitud.py`.

Solicita:

- edad;
- estado de inscripción (`si` o `no`);
- número de documentos entregados.

Reglas:

- la edad debe ser al menos 16 años;
- la persona debe estar inscrita;
- se requieren al menos 3 documentos.

El programa debe mostrar si la solicitud cumple todos los requisitos. Si no los cumple, indica qué requisitos faltan.

**Pista:** puedes utilizar varios `if` independientes para informar más de una observación, aunque la decisión final se base en una condición compuesta.

### Comprobación

Antes de continuar verifica que puedas explicar con un ejemplo la diferencia entre `and` y `or`, y por qué conviene validar los datos antes de realizar cálculos.

---

## Práctica 2.3 — Ciclos `for` y `while`

### Objetivos

Al finalizar esta práctica podrás:

- repetir instrucciones un número conocido de veces;
- utilizar `range()`;
- trabajar con contadores y acumuladores;
- repetir una acción mientras se cumpla una condición;
- detener correctamente un programa interactivo;
- reconocer la causa de un ciclo infinito.

### 1. Repetir con `for`

Crea `contador_for.py`:

~~~python
for numero in range(1, 6):
    print(numero)
~~~

Antes de ejecutar, predice cuántos números aparecerán.

En `range(1, 6)` se comienza en 1 y se llega hasta 5: el límite superior no se incluye.

Prueba también:

~~~python
for numero in range(5):
    print(numero)
~~~

Y:

~~~python
for numero in range(10, 0, -1):
    print(numero)
~~~

¿Qué representa el tercer valor en este último caso?

### Ejercicio — Tabla de multiplicar

Crea `tabla_multiplicar.py`.

Solicita un número entero y muestra su tabla del 1 al 10.

Ejemplo:

~~~text
Número: 7

7 x 1 = 7
7 x 2 = 14
...
7 x 10 = 70
~~~

No escribas diez instrucciones `print()` distintas. Utiliza `for`.

### 2. Contadores y acumuladores

Un contador registra cuántas veces ocurre algo. Un acumulador guarda un resultado que se va actualizando.

Crea `sumar_numeros.py`:

~~~python
suma = 0

for numero in range(1, 6):
    suma = suma + numero
    print("Número:", numero, "Acumulado:", suma)

print("Suma final:", suma)
~~~

Antes de ejecutar completa esta tabla:

| Número | Suma acumulada |
|---:|---:|
| 1 | ? |
| 2 | ? |
| 3 | ? |
| 4 | ? |
| 5 | ? |

Comprueba después.

### Ejercicio — Promedio de calificaciones

Crea `promedio_notas.py`.

El programa debe:

1. solicitar cuántas notas se van a registrar;
2. comprobar que la cantidad sea mayor que cero;
3. solicitar cada nota utilizando `for`;
4. sumar los valores;
5. mostrar el promedio.

Ejemplo:

~~~text
Cantidad de notas: 3
Nota 1: 4.0
Nota 2: 3.5
Nota 3: 4.5

Promedio: 4.0
~~~

**Pista:** comienza con `suma = 0`, actualízala dentro del ciclo y calcula el promedio una vez terminado.

### 3. Repetir con `while`

`while` permite repetir instrucciones mientras una condición sea verdadera.

Crea `contador_while.py`:

~~~python
numero = 1

while numero <= 5:
    print(numero)
    numero += 1
~~~

Compara este programa con el primer ejemplo de `for`.

`numero += 1` es una forma abreviada de escribir `numero = numero + 1`.

### Ejercicio — Leer hasta ingresar cero

Crea `hasta_cero.py`.

Solicita números enteros hasta que el usuario escriba `0`. Después termina.

Ejemplo:

~~~text
Número: 8
Número: 3
Número: 12
Número: 0
Programa terminado.
~~~

Agrega un contador que indique cuántos números diferentes de cero se ingresaron.

### 4. Un acumulador dentro de `while`

Crea `suma_interactiva.py`:

~~~python
total = 0
numero = int(input("Número (0 para terminar): "))

while numero != 0:
    total += numero
    print("Acumulado:", total)
    numero = int(input("Número (0 para terminar): "))

print("Total final:", total)
~~~

Ejecuta:

~~~text
10
25
-5
0
~~~

Antes de ejecutar, calcula manualmente el total esperado.

### 5. Construir un menú que se repite

En un programa interactivo, el ciclo puede continuar hasta que la persona elija salir.

Crea `menu_repetitivo.py`:

~~~python
opcion = ""

while opcion != "0":
    print()
    print("1. Saludar")
    print("2. Mostrar mensaje")
    print("0. Salir")

    opcion = input("Opción: ").strip()

    if opcion == "1":
        print("Hola.")
    elif opcion == "2":
        print("El programa sigue ejecutándose.")
    elif opcion == "0":
        print("Programa terminado.")
    else:
        print("Opción no válida.")
~~~

Prueba las opciones en este orden:

~~~text
1
2
9
0
~~~

Observa cómo `if` decide la acción y `while` decide cuándo se repite el menú.

Se utiliza una cadena para la opción. Así, si el usuario escribe una letra, el programa la tratará como opción desconocida en lugar de intentar convertirla a entero.

### Depuración — Un ciclo que no termina

Lee este programa:

~~~python
numero = 1

while numero <= 5:
    print(numero)
~~~

Antes de ejecutarlo, responde:

- ¿qué variable controla el ciclo?
- ¿esa variable cambia?
- ¿en algún momento la condición dejará de cumplirse?

Corrige el programa. Si un ciclo se queda ejecutándose, utiliza **Ctrl + C** en la terminal para detenerlo.

### Reto — Control de ahorros

Crea `control_ahorros.py`.

El programa debe permitir registrar varios depósitos. Cada vez que se ingrese un valor, muestra el ahorro acumulado. Cuando se escriba cero, termina y muestra:

- cantidad de depósitos realizados;
- total ahorrado.

Los valores negativos no deben sumarse ni contarse como depósitos.

Ejemplo:

~~~text
Depósito (0 para terminar): 10000
Ahorro acumulado: 10000

Depósito (0 para terminar): -5000
El depósito debe ser positivo.

Depósito (0 para terminar): 25000
Ahorro acumulado: 35000

Depósito (0 para terminar): 0

Depósitos válidos: 2
Total ahorrado: 35000
~~~

### Comprobación

Antes de continuar identifica cuándo tiene más sentido utilizar `for` y cuándo `while`. Comprueba también que tus ciclos actualicen las variables necesarias para terminar.

---

## Práctica 2.4 — Listas y recorrido de elementos

### Objetivos

Al finalizar esta práctica podrás:

- crear listas;
- agregar y consultar elementos;
- recorrer listas con `for`;
- contar elementos;
- buscar un valor;
- eliminar elementos existentes;
- reconocer los errores más frecuentes al acceder a posiciones.

Hasta ahora nuestras variables guardaban un valor cada una. Si necesitamos registrar varios productos, crear una variable diferente para cada producto se vuelve poco práctico.

### 1. Crear una lista

Crea `primera_lista.py`:

~~~python
productos = ["Teclado", "Mouse", "Monitor"]

print(productos)
print(type(productos))
~~~

Una lista puede guardar varios elementos en una sola estructura.

También podemos comenzar con una lista vacía:

~~~python
productos = []
~~~

### 2. Consultar elementos por su posición

En Python las posiciones comienzan en cero.

~~~python
productos = ["Teclado", "Mouse", "Monitor"]

print(productos[0])
print(productos[1])
print(productos[2])
~~~

Antes de ejecutar, predice qué nombre aparece en cada línea.

Prueba después:

~~~python
print(productos[-1])
~~~

Ese índice permite acceder al último elemento.

### Ejercicio — Lista de ciudades

Crea `ciudades.py`.

Guarda al menos cinco ciudades en una lista. Muestra:

- la primera;
- la tercera;
- la última;
- la cantidad de ciudades.

**Pista:** para conocer la cantidad de elementos se utiliza `len(lista)`.

### 3. Agregar elementos con `append()`

Ejecuta:

~~~python
productos = []

productos.append("Teclado")
productos.append("Mouse")
productos.append("Monitor")

print(productos)
print("Cantidad:", len(productos))
~~~

Ahora solicita un producto:

~~~python
nuevo_producto = input("Producto nuevo: ").strip()
productos.append(nuevo_producto)

print(productos)
~~~

### Ejercicio — Registro de invitados

Crea `invitados.py`.

Empieza con una lista vacía y solicita cinco nombres utilizando `for`. Agrega cada uno con `append()`.

Al terminar, muestra cuántos invitados hay y el contenido de la lista.

### 4. Recorrer una lista

Una lista puede recorrerse sin conocer sus posiciones.

~~~python
productos = ["Teclado", "Mouse", "Monitor"]

for producto in productos:
    print("Producto:", producto)
~~~

Ahora muestra un número consecutivo junto a cada producto:

~~~python
for posicion, producto in enumerate(productos, start=1):
    print(posicion, "-", producto)
~~~

`enumerate()` permite llevar una numeración mientras recorremos la lista.

### Ejercicio — Mostrar una lista ordenadamente

Crea `lista_materias.py`.

Declara una lista de asignaturas o temas que conozcas y muéstralos con numeración a partir de 1.

Luego agrega un elemento y vuelve a ejecutar.

### 5. Buscar un valor con `in`

Podemos preguntar si un elemento está presente:

~~~python
productos = ["Teclado", "Mouse", "Monitor"]

buscado = input("Buscar producto: ").strip()

if buscado in productos:
    print("Producto encontrado.")
else:
    print("Producto no registrado.")
~~~

Esta búsqueda diferencia mayúsculas y minúsculas. Por ahora puedes indicar que el nombre se escriba tal como aparece en la lista.

### Ejercicio — Evitar duplicados

Crea `sin_duplicados.py`.

Comienza con:

~~~python
productos = ["Teclado", "Mouse"]
~~~

Solicita el nombre de un producto nuevo.

- Si está vacío, informa que no puede registrarse.
- Si ya está en la lista, informa que está repetido.
- En otro caso, agrégalo.

Muestra la lista al final.

**Pista:** una cadena vacía se puede comprobar con `nombre == ""`.

### 6. Eliminar un elemento

`remove()` elimina un elemento por su valor:

~~~python
productos = ["Teclado", "Mouse", "Monitor"]

productos.remove("Mouse")

print(productos)
~~~

Pero si intentas eliminar un nombre inexistente, aparece un error. Antes de eliminar, comprueba si está presente:

~~~python
nombre = input("Producto a eliminar: ").strip()

if nombre in productos:
    productos.remove(nombre)
    print("Producto eliminado.")
else:
    print("No se encontró el producto.")
~~~

### Depuración — Índice inexistente

Analiza:

~~~python
productos = ["Teclado", "Mouse", "Monitor"]
print(productos[3])
~~~

¿Cuántos elementos tiene la lista? ¿Cuál es el índice del último elemento?

Ejecuta para reconocer el error y corrígelo.

### Reto — Lista de tareas

Crea `lista_tareas.py`.

Utiliza una lista y un menú para:

~~~text
1. Agregar tarea
2. Mostrar tareas
3. Consultar cantidad
0. Salir
~~~

El programa debe permanecer activo hasta seleccionar `0`.

Antes de pasar a la siguiente práctica, comprueba:

- lista vacía;
- una tarea;
- varias tareas;
- opción desconocida;
- salir.

### Comprobación

Asegúrate de poder explicar:

- por qué una lista resulta más útil que varias variables separadas;
- qué hace `append()`;
- qué devuelve `len()`;
- cómo se recorre una lista con `for`;
- qué comprueba `in`;
- por qué conviene verificar un elemento antes de aplicar `remove()`.

---

## Práctica 2.5 — Reto integrador: gestor de productos

En esta práctica vas a reunir condiciones, ciclos y listas. Construirás una aplicación de consola en la que el usuario podrá registrar, consultar, buscar y eliminar nombres de productos.

No utilizarás todavía diccionarios, archivos, bases de datos ni funciones propias. Por ahora resolverás el problema con las herramientas trabajadas en este laboratorio. En el siguiente laboratorio reorganizaremos el programa con funciones y ampliaremos la información de cada producto.

### Objetivo

Crear un gestor de productos con un menú que permanezca disponible hasta que el usuario decida salir.

### Archivo de trabajo

Crea:

~~~text
gestor_productos.py
~~~

### Requisitos

El programa debe mantener una lista de nombres:

~~~python
productos = []
~~~

El menú debe mostrar:

~~~text
=========================
    GESTOR DE PRODUCTOS
=========================
1. Agregar producto
2. Mostrar productos
3. Buscar producto
4. Eliminar producto
5. Consultar cantidad
0. Salir
~~~

#### Opción 1 — Agregar producto

Solicita un nombre y aplica estas reglas:

- no aceptar nombres vacíos;
- no registrar un nombre que ya exista;
- agregar los nombres válidos a la lista;
- informar si el registro fue exitoso.

Para este primer gestor se considerarán duplicados los nombres escritos exactamente igual. La comparación sin distinguir mayúsculas será un reto de ampliación.

#### Opción 2 — Mostrar productos

Muestra los productos numerados a partir de 1.

Si la lista está vacía, muestra:

~~~text
No hay productos registrados.
~~~

#### Opción 3 — Buscar producto

Solicita un nombre e indica si está registrado.

No agregues ni elimines elementos durante la búsqueda.

#### Opción 4 — Eliminar producto

Solicita el nombre que se desea eliminar.

- Si existe, elimínalo y confirma la operación.
- Si no existe, informa que no fue encontrado.
- No debe fallar cuando la lista esté vacía.

#### Opción 5 — Consultar cantidad

Muestra la cantidad de productos registrados utilizando `len()`.

#### Opción 0 — Salir

Finaliza el programa con un mensaje de despedida.

Cualquier opción diferente debe mostrar:

~~~text
Opción no válida.
~~~

### Antes de programar

Haz un pequeño esquema en comentarios:

~~~python
# Crear lista vacía
# Mientras no se seleccione salir:
#     Mostrar menú
#     Leer opción
#     Según la opción:
#         Agregar
#         Mostrar
#         Buscar
#         Eliminar
#         Consultar cantidad
~~~

Piensa qué operaciones modifican la lista y cuáles solamente consultan su contenido.

### Construcción por etapas

**Etapa 1. Menú.** Crea un ciclo `while` que muestre las opciones y termine con `0`. No programes todavía las otras operaciones.

**Etapa 2. Registro.** Agrega el código de la opción 1. Comprueba qué ocurre con un nombre válido, un nombre vacío y uno repetido.

**Etapa 3. Listado.** Muestra todos los nombres usando `for` y `enumerate()`. Prepara un mensaje cuando no haya productos.

**Etapa 4. Búsqueda.** Utiliza `in` para comprobar si el nombre ingresado ya existe.

**Etapa 5. Eliminación.** Comprueba la existencia del producto antes de utilizar `remove()`.

**Etapa 6. Conteo.** Muestra el valor devuelto por `len()`.

Ejecuta el programa después de terminar cada etapa. Es más fácil encontrar un error cuando sabes qué parte acabas de modificar.

### Esqueleto inicial

Puedes comenzar con este código. Los comportamientos del menú están pendientes:

~~~python
productos = []
opcion = ""

while opcion != "0":
    print()
    print("=========================")
    print("    GESTOR DE PRODUCTOS")
    print("=========================")
    print("1. Agregar producto")
    print("2. Mostrar productos")
    print("3. Buscar producto")
    print("4. Eliminar producto")
    print("5. Consultar cantidad")
    print("0. Salir")

    opcion = input("Opción: ").strip()

    if opcion == "1":
        # Registrar producto
        pass
    elif opcion == "2":
        # Mostrar productos
        pass
    elif opcion == "3":
        # Buscar producto
        pass
    elif opcion == "4":
        # Eliminar producto
        pass
    elif opcion == "5":
        # Mostrar cantidad
        pass
    elif opcion == "0":
        print("Programa finalizado.")
    else:
        print("Opción no válida.")
~~~

`pass` permite dejar temporalmente vacío un bloque de código. Debes sustituirlo por la operación correspondiente a cada opción.

### Pistas

Para guardar un nombre:

~~~python
productos.append(nombre)
~~~

Para comprobar si ya existe:

~~~python
if nombre in productos:
    print("El producto ya existe.")
~~~

Para mostrar elementos numerados:

~~~python
for numero, producto in enumerate(productos, start=1):
    print(numero, "-", producto)
~~~

Para eliminar un nombre que exista:

~~~python
productos.remove(nombre)
~~~

No copies todas las pistas en una sola opción. Decide dónde corresponde utilizar cada una.

### Ejemplo de ejecución

~~~text
=========================
    GESTOR DE PRODUCTOS
=========================
1. Agregar producto
2. Mostrar productos
3. Buscar producto
4. Eliminar producto
5. Consultar cantidad
0. Salir
Opción: 1
Nombre del producto: Teclado
Producto registrado.

Opción: 1
Nombre del producto: Mouse
Producto registrado.

Opción: 2
1 - Teclado
2 - Mouse

Opción: 3
Producto a buscar: Mouse
Producto encontrado.

Opción: 5
Cantidad de productos: 2

Opción: 4
Producto a eliminar: Teclado
Producto eliminado.

Opción: 2
1 - Mouse

Opción: 0
Programa finalizado.
~~~

No es necesario que el texto de todos los mensajes coincida exactamente. Lo importante es que el programa cumpla las reglas.

### Pruebas obligatorias

Prueba al menos los siguientes escenarios y verifica que el resultado sea coherente.

| Escenario | Resultado esperado |
|---|---|
| Mostrar sin haber registrado productos | Mensaje de lista vacía |
| Registrar `Teclado` | Se agrega a la lista |
| Registrar nuevamente `Teclado` | Se rechaza por duplicado |
| Registrar solamente espacios | Se rechaza como nombre vacío |
| Registrar `Mouse` y `Monitor` | La lista contiene tres productos distintos |
| Buscar `Mouse` | Producto encontrado |
| Buscar `Impresora` | Producto no encontrado |
| Eliminar `Mouse` | Se elimina y disminuye la cantidad |
| Eliminar `Impresora` | Mensaje informativo, sin error |
| Elegir `9` | Opción no válida |
| Elegir `0` | El programa termina |

Al hacer las pruebas, vuelve varias veces al listado y al conteo para comprobar que el estado de la lista corresponda con las operaciones realizadas.

### Revisión del código

Antes de terminar revisa:

- ¿el menú vuelve a aparecer después de cada operación?
- ¿la lista se crea una sola vez, antes del ciclo?
- ¿los nombres vacíos y repetidos se rechazan?
- ¿se puede mostrar una lista vacía sin errores?
- ¿la búsqueda no modifica la lista?
- ¿la eliminación comprueba primero que el producto exista?
- ¿el número mostrado por la opción 5 coincide con los elementos registrados?
- ¿el programa termina al seleccionar `0`?
- ¿puedes explicar qué hace cada bloque de código?

### Reto de ampliación

Si ya terminaste las pruebas, agrega una o varias mejoras:

**Búsqueda sin importar mayúsculas.** Permite encontrar `Teclado` cuando el usuario escriba `teclado`. Revisa cómo utilizar `lower()` dentro de un recorrido de la lista sin modificar los nombres almacenados.

**Mostrar la lista ordenada.** Investiga la función `sorted()` y presenta una versión alfabética sin alterar necesariamente el orden original de registro.

**Renombrar producto.** Agrega una opción para modificar el nombre de un producto existente. Comprueba que el nuevo nombre no esté vacío ni cause un duplicado exacto.

Estas ampliaciones son opcionales; no necesitan resolverlas para completar las funciones principales.

### Depuración

Haz una copia de `gestor_productos.py` e introduce, uno por uno, estos problemas:

1. vuelve a crear `productos = []` dentro del `while`;
2. intenta eliminar un nombre inexistente sin comprobarlo primero;
3. cambia una comparación `==` por una asignación `=` dentro de un `if`;
4. elimina la actualización de la opción que permite salir.

Corrige cada problema y registra en un comentario qué comportamiento observaste.

---

## Cierre del laboratorio

Al finalizar deberías poder:

- utilizar `if`, `elif` y `else`;
- construir condiciones con `and`, `or` y `not`;
- validar datos antes de realizar una operación;
- repetir instrucciones con `for` y `while`;
- trabajar con `range()`, contadores y acumuladores;
- crear listas y agregar elementos;
- recorrer listas y consultar su cantidad;
- buscar y eliminar valores;
- mantener activo un menú de consola;
- comprobar distintos escenarios y corregir errores.

Conserva `gestor_productos.py`. En el próximo laboratorio lo reorganizarás mediante funciones y comenzarás a representar productos con más de un dato.
