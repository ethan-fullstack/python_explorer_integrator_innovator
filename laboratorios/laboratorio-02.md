# Laboratorio 2 — Decisiones, ciclos y colecciones en Python

En el laboratorio anterior construiste programas que reciben datos y realizan cálculos. Ahora vas a tomar decisiones, repetir operaciones y guardar conjuntos de información. Trabajarás con las cuatro colecciones integradas más utilizadas en Python: listas, tuplas, conjuntos y diccionarios.

También aprenderás a crear colecciones a partir de otras mediante **comprensiones (comprehensions)**. Primero resolverás cada problema con instrucciones conocidas; después compararás esa solución con una comprensión.

El reto final será un gestor de inventario que mantenga varios productos, sus datos y algunos informes. No utilizaremos todavía archivos ni bases de datos: la información permanecerá en memoria mientras el programa esté abierto.

| Práctica | Tema |
|---|---|
| 2.1 | Comparaciones y decisiones con `if`, `elif` y `else` |
| 2.2 | Condiciones compuestas y validaciones |
| 2.3 | Ciclos `for` y `while` |
| 2.4 | Listas: acceso, modificaciones y recorridos |
| 2.5 | Tuplas: datos agrupados que no cambian |
| 2.6 | Conjuntos (`set`): valores únicos y operaciones |
| 2.7 | Diccionarios y colecciones anidadas |
| 2.8 | Comprensiones de listas, conjuntos y diccionarios |
| 2.9 | Reto integrador: gestor de inventario |

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

### 7. Reemplazar, insertar y extraer valores

Además de agregar elementos al final, podemos cambiar la información de una lista.

Crea `operaciones_lista.py`:

~~~python
productos = ["Teclado", "Mouse", "Monitor"]

productos[1] = "Mouse inalámbrico"
productos.insert(1, "Cámara web")

eliminado = productos.pop()

print("Elementos:", productos)
print("Extraído:", eliminado)
~~~

Observa las diferencias:

| Operación | Uso |
|---|---|
| `lista[indice] = valor` | reemplaza el elemento de una posición |
| `append(valor)` | agrega un elemento al final |
| `insert(indice, valor)` | inserta en una posición |
| `remove(valor)` | elimina por su contenido |
| `pop()` | extrae y devuelve el último elemento |
| `pop(indice)` | extrae y devuelve el elemento de una posición |
| `extend(otra_lista)` | agrega varios elementos |
| `clear()` | vacía la lista |

No confundas `remove()` con `pop()`: uno recibe un valor y el otro trabaja con una posición.

### Ejercicio — Cambios de inventario

Comienza con:

~~~python
productos = ["Teclado", "Mouse", "Monitor"]
nuevos = ["Impresora", "Cámara"]
~~~

Haz lo siguiente, ejecutando después de cada paso:

1. reemplaza `Mouse` por `Mouse inalámbrico`;
2. inserta `Parlantes` en la primera posición;
3. añade los dos elementos de `nuevos` con `extend()`;
4. elimina `Monitor`;
5. extrae el último elemento con `pop()` y muéstralo.

Compara el resultado final con la secuencia de operaciones que realizaste.

### 8. Obtener partes de una lista y ordenar

Los cortes o *slices* permiten consultar varios elementos sin escribir cada índice.

~~~python
productos = ["Teclado", "Mouse", "Monitor", "Cámara", "Parlantes"]

print(productos[0:2])
print(productos[2:])
print(productos[:3])
print(productos[-2:])
~~~

El extremo final de un corte no se incluye.

También puedes ordenar:

~~~python
productos = ["Monitor", "Cámara", "Teclado"]

ordenados = sorted(productos)

print("Original:", productos)
print("Ordenados:", ordenados)
~~~

`sorted()` produce una lista nueva. En cambio, `productos.sort()` cambia la lista existente.

Prueba ambas alternativas.

### 9. Copiar no es lo mismo que compartir la lista

Analiza:

~~~python
original = ["Teclado", "Mouse"]
otra_referencia = original

otra_referencia.append("Monitor")

print(original)
~~~

Después ejecuta:

~~~python
original = ["Teclado", "Mouse"]
copia = original.copy()

copia.append("Monitor")

print("Original:", original)
print("Copia:", copia)
~~~

En el primer caso ambos nombres se refieren a la misma lista. En el segundo se crea una copia superficial: para estas listas de cadenas, las modificaciones de una no cambian la otra. Más adelante veremos qué ocurre cuando hay estructuras anidadas.

### Ejercicio — Lista de calificaciones

Crea `calificaciones_lista.py` con seis calificaciones.

Muestra:

- las tres primeras;
- las dos últimas;
- la cantidad total;
- las notas ordenadas de menor a mayor, sin modificar el orden original;
- cuántas veces aparece una nota concreta mediante `count()`.

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

## Práctica 2.5 — Tuplas: datos agrupados que no cambian

### Objetivos

Al finalizar esta práctica podrás:

- crear tuplas y consultar sus elementos;
- distinguir una tupla de una lista;
- desempacar valores en varias variables;
- utilizar tuplas para representar datos que no necesitan modificación;
- reconocer un error al intentar cambiar un elemento.

### 1. Tuplas y listas

Una tupla agrupa valores en un orden determinado. Se parece a una lista, pero no permite reemplazar, agregar ni eliminar elementos directamente.

Crea `primeras_tuplas.py`:

~~~python
coordenadas = (4.6097, -74.0817)
meses = ("enero", "febrero", "marzo")

print(coordenadas)
print(coordenadas[0])
print(meses[-1])
print(len(meses))
~~~

Las tuplas mantienen el orden y admiten índices y cortes, igual que las listas.

### 2. La diferencia: mutabilidad

Compara:

~~~python
colores_lista = ["rojo", "verde", "azul"]
colores_lista[0] = "amarillo"

print(colores_lista)
~~~

con:

~~~python
colores_tupla = ("rojo", "verde", "azul")
colores_tupla[0] = "amarillo"
~~~

Ejecuta el segundo fragmento para reconocer el error. Después elimina la línea que intenta modificar la tupla.

| Característica | Lista | Tupla |
|---|---|---|
| Sintaxis habitual | `[1, 2, 3]` | `(1, 2, 3)` |
| Conserva el orden | Sí | Sí |
| Permite elementos repetidos | Sí | Sí |
| Acceso por índice | Sí | Sí |
| Se puede modificar directamente | Sí | No |

Una tupla es inmutable como contenedor, aunque puede contener objetos mutables. No necesitaremos ese caso para los ejercicios iniciales.

### 3. Tupla con un solo elemento

Ejecuta:

~~~python
valor = (10)
tupla = (10,)

print(type(valor))
print(type(tupla))
~~~

La coma es importante: `(10)` es un entero entre paréntesis; `(10,)` es una tupla de un elemento.

Para crear una tupla vacía se utiliza `()`.

### 4. Desempaquetar una tupla

Ejecuta:

~~~python
producto = ("P001", "Teclado", 85000)

codigo, nombre, precio = producto

print("Código:", codigo)
print("Nombre:", nombre)
print("Precio:", precio)
~~~

Al desempaquetar, la cantidad de variables debe coincidir con la cantidad de valores.

### Ejercicio — Ubicación

Crea `ubicaciones.py`.

Registra tres coordenadas en tuplas separadas, cada una con latitud y longitud. Muestra los valores desempaquetándolos en variables con nombres claros.

Luego crea una tupla con tres datos de una sede: código, nombre y ciudad. Muéstralos sin acceder por índices.

### 5. Una colección fija de opciones

Las tuplas son útiles cuando necesitamos un grupo de opciones que no cambia durante el programa.

~~~python
categorias = ("Periféricos", "Componentes", "Accesorios")

for categoria in categorias:
    print(categoria)

elegida = input("Categoría: ").strip()

if elegida in categorias:
    print("Categoría válida.")
else:
    print("Categoría no registrada.")
~~~

### Depuración — Desempaquetado incorrecto

Ejecuta:

~~~python
producto = ("P001", "Teclado", 85000)
codigo, nombre = producto
~~~

Lee el error y corrige la asignación sin eliminar información de la tupla.

### Reto — Catálogo de categorías

Crea `categorias_tupla.py`.

Declara una tupla con cuatro categorías de productos. El programa debe:

1. mostrar las categorías numeradas;
2. solicitar una categoría por su nombre;
3. indicar si pertenece a las opciones permitidas;
4. mostrar la cantidad de categorías disponibles.

Prueba una categoría válida, una desconocida y una escrita con espacios adicionales.

### Comprobación

Antes de continuar verifica por qué usarías una tupla para categorías fijas y una lista para productos que vas a registrar o eliminar.

---

## Práctica 2.6 — Conjuntos (`set`): valores únicos y operaciones

### Objetivos

Al finalizar esta práctica podrás:

- crear conjuntos;
- eliminar valores duplicados mediante `set()`;
- agregar y quitar elementos;
- comprobar pertenencia;
- utilizar unión, intersección y diferencia;
- distinguir un conjunto de una lista o una tupla.

### 1. Crear un conjunto

Un conjunto almacena elementos únicos. No ofrece posiciones numéricas para acceder a cada elemento y no garantiza un orden de presentación.

Crea `primer_conjunto.py`:

~~~python
categorias = {"Periféricos", "Accesorios", "Componentes", "Periféricos"}

print(categorias)
print("Cantidad:", len(categorias))
~~~

¿Cuántos elementos esperas encontrar? Ejecuta y comprueba.

El orden al imprimir un conjunto puede variar.

### 2. Crear un conjunto vacío

Compara:

~~~python
vacio = set()
otro = {}

print(type(vacio))
print(type(otro))
~~~

`set()` crea un conjunto vacío. `{}` crea un diccionario vacío, que veremos a continuación.

### 3. Eliminar duplicados de una lista

Ejecuta:

~~~python
ciudades = ["Bogotá", "Cali", "Bogotá", "Medellín", "Cali"]
unicas = set(ciudades)

print("Total de registros:", len(ciudades))
print("Ciudades diferentes:", len(unicas))
print(unicas)
~~~

Prueba también:

~~~python
lista_unicas = list(unicas)
print(lista_unicas)
~~~

El cambio de tipo no recupera el orden original. Si necesitas mantener ese orden, esta conversión no es suficiente.

### Ejercicio — Etiquetas sin repetidos

Crea `etiquetas.py` con una lista que contenga nombres de etiquetas repetidas. Muestra la cantidad de registros originales y cuántas etiquetas diferentes existen.

### 4. Agregar y eliminar elementos

~~~python
categorias = {"Periféricos", "Accesorios"}

categorias.add("Componentes")
categorias.update(["Papelería", "Accesorios"])

print(categorias)

categorias.discard("Papelería")
print(categorias)
~~~

Diferencia importante:

- `add()` agrega un elemento;
- `update()` incorpora los elementos de otro iterable;
- `discard()` no falla si el valor no existe;
- `remove()` produce un error si el valor no está presente.

### Ejercicio — Participantes únicos

Crea `participantes.py`.

Parte de dos listas de nombres que incluyen repetidos. Convierte la información a un conjunto y muestra cuántas personas diferentes aparecen.

Agrega luego una persona con `add()` y elimina otra con `discard()`.

### 5. Operaciones entre conjuntos

Supongamos que hay dos grupos de aprendices:

~~~python
grupo_a = {"Ana", "Carlos", "Laura", "Sara"}
grupo_b = {"Laura", "Sara", "David", "Pablo"}
~~~

Ejecuta:

~~~python
print("Unión:", grupo_a | grupo_b)
print("Intersección:", grupo_a & grupo_b)
print("Solo A:", grupo_a - grupo_b)
print("Diferencia simétrica:", grupo_a ^ grupo_b)
~~~

| Operación | Símbolo | Qué devuelve |
|---|---|---|
| Unión | `|` | elementos de cualquiera de los conjuntos |
| Intersección | `&` | elementos comunes |
| Diferencia | `-` | elementos del primero que no están en el segundo |
| Diferencia simétrica | `^` | elementos que no están en ambos a la vez |

### Antes de ejecutar — Predice

Con los conjuntos anteriores, escribe qué nombres deberían aparecer en:

- la intersección;
- la diferencia `grupo_a - grupo_b`;
- la diferencia simétrica.

Luego ejecuta y compara sin depender del orden en que se impriman los nombres.

### Reto — Inscripciones en talleres

Crea `inscripciones_talleres.py`.

Define dos conjuntos: aprendices inscritos a Python y aprendices inscritos a Java.

El programa debe mostrar:

1. inscritos en al menos uno de los talleres;
2. inscritos en ambos;
3. inscritos únicamente en Python;
4. inscritos únicamente en Java;
5. inscritos en uno solo de los talleres.

Incluye al menos dos nombres compartidos entre ambos conjuntos para poder verificar las operaciones.

### Error frecuente

No intentes acceder a `categorias[0]` en un conjunto. No tiene índices. Si necesitas mantener orden y consultar posiciones, utiliza una lista o una tupla.

### Comprobación

Antes de continuar distingue entre:

- valores únicos y valores que pueden repetirse;
- `{}` y `set()`;
- `remove()` y `discard()`;
- unión e intersección.

---

## Práctica 2.7 — Diccionarios y colecciones anidadas

### Objetivos

Al finalizar esta práctica podrás:

- representar datos mediante pares clave–valor;
- consultar, agregar, modificar y eliminar campos;
- utilizar `get()` para claves opcionales;
- recorrer claves, valores y pares;
- guardar diccionarios dentro de listas;
- recorrer una lista de registros y calcular resultados.

### 1. Un producto con varios datos

Una lista es adecuada para guardar varios nombres. Pero un producto también puede tener código, precio, categoría y cantidad.

Crea `primer_diccionario.py`:

~~~python
producto = {
    "codigo": "P001",
    "nombre": "Teclado",
    "precio": 85000.0,
    "cantidad": 4,
    "categoria": "Periféricos"
}

print(producto)
print(producto["nombre"])
print(producto["precio"])
~~~

En un diccionario consultamos la información mediante claves, no mediante posiciones.

### 2. Consultar y modificar campos

Ejecuta:

~~~python
producto["cantidad"] = 8
producto["activo"] = True

print(producto)
~~~

Se puede modificar una clave existente y agregar una nueva.

Para consultar una clave que podría no existir:

~~~python
print(producto.get("marca"))
print(producto.get("marca", "Sin marca"))
~~~

`get()` evita el error que aparecería al acceder con `producto["marca"]` si esa clave no existe.

### Ejercicio — Perfil de aprendiz

Crea `perfil_diccionario.py`.

Representa en un diccionario:

- ficha;
- nombre;
- trimestre;
- promedio;
- activo.

Muestra el nombre, cambia el trimestre y agrega una clave para el correo electrónico.

### 3. Recorrer un diccionario

Ejecuta:

~~~python
producto = {
    "codigo": "P001",
    "nombre": "Teclado",
    "precio": 85000.0
}

for clave in producto:
    print(clave)

for valor in producto.values():
    print(valor)

for clave, valor in producto.items():
    print(clave, ":", valor)
~~~

Los diccionarios conservan el orden de inserción de las claves, pero se consultan principalmente por su clave.

### 4. Eliminar y actualizar

Ejecuta:

~~~python
producto = {
    "codigo": "P001",
    "nombre": "Teclado",
    "precio": 85000.0,
    "cantidad": 4
}

producto.update({"precio": 80000.0, "cantidad": 5})
cantidad_eliminada = producto.pop("cantidad")

print(producto)
print("Cantidad eliminada:", cantidad_eliminada)
~~~

`update()` actualiza o agrega pares. `pop()` puede extraer una clave y su valor, pero si la clave no existe produce un error a menos que se proporcione un valor predeterminado.

### Ejercicio — Ajuste de precio

Crea `actualizar_producto.py`.

Parte de un diccionario con código, nombre, precio y cantidad. Solicita un nuevo precio y actualiza únicamente ese campo si el valor es mayor que cero.

Después muestra todos los pares clave–valor con `items()`.

### 5. Una lista de diccionarios

Ahora combinaremos dos colecciones:

~~~python
productos = [
    {"codigo": "P001", "nombre": "Teclado", "precio": 85000.0, "cantidad": 4},
    {"codigo": "P002", "nombre": "Mouse", "precio": 45000.0, "cantidad": 7},
    {"codigo": "P003", "nombre": "Monitor", "precio": 700000.0, "cantidad": 2}
]

for producto in productos:
    print(producto["codigo"], "-", producto["nombre"])
~~~

La lista agrupa productos y cada diccionario contiene los datos de un producto.

### 6. Calcular sobre una lista de diccionarios

Utiliza los mismos datos:

~~~python
valor_inventario = 0

for producto in productos:
    valor_inventario += producto["precio"] * producto["cantidad"]

print("Valor del inventario:", valor_inventario)
~~~

Antes de ejecutar, calcula manualmente el valor esperado:

- Teclados: 85.000 × 4
- Mouse: 45.000 × 7
- Monitores: 700.000 × 2

### Ejercicio — Agregar un registro

Crea `catalogo_diccionarios.py`.

Comienza con la lista anterior y solicita los datos de un cuarto producto. Construye un nuevo diccionario y agrégalo con `append()`.

Muestra todos los productos, incluyendo el nuevo.

### 7. Estructuras anidadas

Un diccionario también puede contener listas o incluso otros diccionarios:

~~~python
curso = {
    "nombre": "Fundamentos de Python",
    "aprendices": ["Ana", "Luis", "Camila"],
    "instructor": {
        "nombre": "María",
        "area": "Software"
    }
}

print(curso["nombre"])
print(curso["aprendices"][1])
print(curso["instructor"]["nombre"])
~~~

Para leer correctamente una estructura anidada, avanza un nivel a la vez: primero la clave, luego la posición o la siguiente clave.

### Ejercicio — Información de un curso

Crea `curso_anidado.py`.

Representa un curso con nombre, código, una lista de tres temas y un diccionario con los datos de su instructor. Muestra:

- nombre del curso;
- segundo tema;
- nombre del instructor;
- cantidad de temas.

### 8. Buscar un producto por código

En una lista de diccionarios no podemos buscar un código escribiendo solamente `codigo in productos`, porque los elementos de la lista son diccionarios completos.

Recórrela:

~~~python
codigo_buscado = input("Código: ").strip()
encontrado = False

for producto in productos:
    if producto["codigo"] == codigo_buscado:
        print("Encontrado:", producto["nombre"])
        encontrado = True
        break

if not encontrado:
    print("No se encontró el producto.")
~~~

`break` permite terminar un ciclo antes de que recorra todos los elementos cuando ya tenemos el resultado.

### Depuración — Clave inexistente

Ejecuta:

~~~python
producto = {"codigo": "P001", "nombre": "Teclado"}

print(producto["precio"])
~~~

Observa el error. Corrígelo con `get()` para mostrar un valor predeterminado cuando el precio todavía no esté registrado.

### Reto — Registro de libros

Crea `catalogo_libros.py`.

Guarda al menos tres libros en una lista de diccionarios. Cada libro debe tener código, título, autor y año.

Permite solicitar un código y mostrar los datos del libro encontrado; si no existe, informa que no fue localizado.

Como ampliación, solicita un nuevo libro y comprueba que no se repita su código antes de agregarlo.

### Comprobación

Antes de continuar verifica que puedas explicar cuándo necesitas una lista, cuándo un diccionario y por qué una lista de diccionarios permite manejar varios registros con estructura similar.

---

## Práctica 2.8 — Comprensiones de listas, conjuntos y diccionarios

### Objetivos

Al finalizar esta práctica podrás:

- reconocer la estructura de una comprensión;
- transformar y filtrar elementos con comprensiones de listas;
- generar conjuntos de elementos únicos mediante comprensiones;
- construir diccionarios mediante comprensiones;
- recorrer colecciones de registros;
- decidir cuándo un ciclo tradicional es más fácil de leer.

Las comprensiones (*comprehensions*) permiten construir una colección nueva a partir de otra. No reemplazan todos los ciclos: son útiles cuando queremos obtener un resultado que pueda expresarse claramente en una sola construcción.

### 1. Primero con `for`

Crea `cuadrados.py`:

~~~python
numeros = [1, 2, 3, 4, 5]
cuadrados = []

for numero in numeros:
    cuadrados.append(numero ** 2)

print(cuadrados)
~~~

Resultado:

~~~text
[1, 4, 9, 16, 25]
~~~

### 2. La misma operación con una comprensión

~~~python
numeros = [1, 2, 3, 4, 5]
cuadrados = [numero ** 2 for numero in numeros]

print(cuadrados)
~~~

La forma general es:

~~~python
nueva_lista = [expresion for elemento in coleccion]
~~~

Se crea una lista nueva; la original permanece igual.

### Ejercicio — Precios con impuesto

Crea `precios_con_impuesto.py`.

Parte de:

~~~python
precios = [10000, 20000, 50000, 80000]
~~~

Construye una lista nueva que contenga cada precio aumentado en 19 %. Primero hazlo con `for` y `append()`; después mediante una comprensión.

Comprueba que obtienes resultados equivalentes.

### 3. Filtrar mediante `if`

Crea `filtrar_numeros.py`:

~~~python
numeros = [3, 8, 12, 5, 20, 7]

mayores = [numero for numero in numeros if numero >= 10]

print(mayores)
~~~

Resultado:

~~~text
[12, 20]
~~~

La condición está al final de la comprensión porque decide qué elementos se incluyen.

### Ejercicio — Notas aprobadas

Parte de:

~~~python
notas = [2.5, 3.0, 4.8, 1.9, 3.7, 5.0]
~~~

Construye:

- una lista de notas aprobadas (3.0 o más);
- otra con notas inferiores a 3.0;
- otra con todas las notas multiplicadas por 2.

Utiliza tres comprensiones independientes.

### 4. Transformar y filtrar al mismo tiempo

~~~python
nombres = ["  ana ", "CARLOS", " Laura ", ""]

normalizados = [
    nombre.strip().title()
    for nombre in nombres
    if nombre.strip() != ""
]

print(normalizados)
~~~

Resultado:

~~~text
['Ana', 'Carlos', 'Laura']
~~~

La transformación aparece al comienzo, antes de `for`; la condición de filtrado aparece al final.

### Ejercicio — Códigos normalizados

Crea `normalizar_codigos.py`.

Parte de:

~~~python
codigos = [" p001 ", "P002", "", " p003", "  "]
~~~

Obtén una lista sin elementos vacíos ni espacios sobrantes, con todos los códigos en mayúscula.

### 5. Una condición que produce valores diferentes

No confundas filtrar con transformar condicionalmente.

~~~python
notas = [2.5, 3.8, 4.1]

estados = ["Aprobado" if nota >= 3.0 else "No aprobado" for nota in notas]

print(estados)
~~~

Aquí se produce un elemento de salida por cada nota. No se descarta ninguna.

### 6. Comprensión de conjuntos

Las llaves también permiten producir un conjunto:

~~~python
categorias = ["Periféricos", "Accesorios", "Periféricos", "Componentes"]

unicas = {categoria for categoria in categorias}

print(unicas)
~~~

Los valores duplicados desaparecen y el orden al imprimir no está garantizado.

### Ejercicio — Categorías distintas

Utiliza una lista de diccionarios:

~~~python
productos = [
    {"nombre": "Teclado", "categoria": "Periféricos"},
    {"nombre": "Mouse", "categoria": "Periféricos"},
    {"nombre": "Disco", "categoria": "Componentes"}
]
~~~

Construye un conjunto con todas las categorías diferentes utilizando una comprensión.

### 7. Comprensión de diccionarios

Una comprensión de diccionario utiliza pares `clave: valor`:

~~~python
numeros = [1, 2, 3, 4]

cuadrados = {numero: numero ** 2 for numero in numeros}

print(cuadrados)
~~~

Resultado:

~~~text
{1: 1, 2: 4, 3: 9, 4: 16}
~~~

### Ejercicio — Catálogo por código

Parte de:

~~~python
productos = [
    {"codigo": "P001", "nombre": "Teclado"},
    {"codigo": "P002", "nombre": "Mouse"},
    {"codigo": "P003", "nombre": "Monitor"}
]
~~~

Crea un diccionario donde:

- cada clave sea el código;
- cada valor sea el nombre.

El resultado esperado es:

~~~python
{"P001": "Teclado", "P002": "Mouse", "P003": "Monitor"}
~~~

Asume que no hay códigos repetidos. Si los hubiera, una clave repetida conservaría el último valor asociado.

### 8. Comprensión sobre una lista de diccionarios

Partimos de:

~~~python
productos = [
    {"nombre": "Teclado", "precio": 85000, "cantidad": 4},
    {"nombre": "Mouse", "precio": 45000, "cantidad": 0},
    {"nombre": "Monitor", "precio": 700000, "cantidad": 2}
]
~~~

Crea una lista de nombres con existencias:

~~~python
disponibles = [
    producto["nombre"]
    for producto in productos
    if producto["cantidad"] > 0
]

print(disponibles)
~~~

Después calcula:

~~~python
valores = [
    producto["precio"] * producto["cantidad"]
    for producto in productos
]

print("Valor total:", sum(valores))
~~~

### Ejercicio — Informes de inventario

Con los datos anteriores construye:

- una lista de productos sin existencias;
- una lista de nombres con precio superior a $100.000;
- un conjunto de los valores diferentes de cantidad;
- un diccionario que relacione nombre y precio.

### 9. Comprensiones anidadas y expresiones generadoras

Una comprensión puede contener más de un `for`.

~~~python
grupos = [[1, 2], [3, 4], [5, 6]]

numeros = [numero for grupo in grupos for numero in grupo]

print(numeros)
~~~

Resultado:

~~~text
[1, 2, 3, 4, 5, 6]
~~~

**No confundas una comprensión de tupla con una expresión generadora.** Los paréntesis en:

~~~python
cuadrados = (numero ** 2 for numero in range(1, 5))

print(type(cuadrados))
print(list(cuadrados))
~~~

crean un generador, no una tupla. Puedes crear una tupla a partir de ese generador:

~~~python
tupla_cuadrados = tuple(numero ** 2 for numero in range(1, 5))
print(tupla_cuadrados)
~~~

El estudio detallado de generadores se hará más adelante; aquí basta con reconocer la diferencia.

### Depuración — Sintaxis de una comprensión

Corrige este código:

~~~python
numeros = [1, 2, 3, 4, 5]
pares = [numero for numero in numeros numero % 2 == 0]

print(pares)
~~~

Antes de consultar otro ejemplo, identifica qué palabra falta para expresar el filtro.

### Reto — Informe con tres comprensiones

Crea `informe_comprensiones.py`.

Utiliza esta información:

~~~python
ventas = [
    {"producto": "Teclado", "categoria": "Periféricos", "total": 150000},
    {"producto": "Mouse", "categoria": "Periféricos", "total": 70000},
    {"producto": "Disco SSD", "categoria": "Componentes", "total": 280000},
    {"producto": "Memoria USB", "categoria": "Accesorios", "total": 45000}
]
~~~

Obtén:

1. una lista con los nombres de productos cuyas ventas superen $100.000;
2. un conjunto de categorías diferentes;
3. un diccionario que relacione producto y valor de venta;
4. la suma de todas las ventas utilizando `sum()` sobre una lista de valores.

Primero resuelve una de las transformaciones con un ciclo `for` y después con una comprensión. Compara ambas opciones.

### Comprobación

Antes de continuar asegúrate de entender la diferencia entre:

- una transformación y un filtro;
- una comprensión de lista (`[]`), una de conjunto (`{}`) y una de diccionario (`{clave: valor}`);
- una tupla y una expresión generadora;
- una solución breve y una solución fácil de leer.

No conviertas cualquier ciclo a comprensión por obligación. Si necesitas múltiples pasos, modificar estructuras existentes o mostrar mensajes durante el recorrido, un `for` puede expresar mejor lo que hace el programa.

---

## Práctica 2.9 — Reto integrador: gestor de inventario

Construirás una aplicación de consola que combine condiciones, ciclos, listas, tuplas, conjuntos, diccionarios y comprensiones. El resultado será un inventario pequeño que permite registrar, consultar y actualizar productos.

No utilizarás todavía funciones propias, archivos ni bases de datos. Los datos estarán disponibles solamente mientras el programa permanezca abierto.

### Objetivo

Gestionar un inventario en memoria y producir consultas e informes a partir de los registros.

### Datos del programa

Cada producto se representará con un diccionario:

~~~python
producto = {
    "codigo": "P001",
    "nombre": "Teclado",
    "categoria": "Periféricos",
    "precio": 85000.0,
    "cantidad": 4
}
~~~

Todos los productos estarán dentro de una lista:

~~~python
productos = []
~~~

Las categorías permitidas estarán guardadas en una tupla:

~~~python
CATEGORIAS = ("Periféricos", "Componentes", "Accesorios")
~~~

En los informes utilizarás conjuntos y comprensiones para obtener información derivada sin cambiar los registros.

### Menú

El programa debe mantener este menú hasta seleccionar `0`:

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

### Reglas de funcionamiento

**Registrar producto.** Solicita código, nombre, categoría, precio y cantidad. No permitas códigos repetidos ni campos de texto vacíos. La categoría debe estar en `CATEGORIAS`, el precio debe ser mayor que cero y la cantidad no puede ser negativa.

**Listar productos.** Muestra código, nombre, categoría, precio y cantidad de cada producto. Si la lista está vacía, informa que no hay registros.

**Buscar por código.** Solicita un código y muestra los datos del producto encontrado; si no existe, informa al usuario.

**Actualizar existencias.** Busca un producto por código y solicita la nueva cantidad disponible. Rechaza números negativos. No necesitas sumar o restar movimientos: en esta versión se reemplaza la cantidad actual por la nueva.

**Eliminar producto.** Busca por código, elimina el diccionario correspondiente de la lista si existe y confirma la operación. Si no existe, muestra un mensaje sin producir error.

**Productos sin stock.** Muestra los nombres de los productos cuya cantidad sea cero. Utiliza una comprensión de lista.

**Resumen del inventario.** Muestra cantidad de productos registrados, unidades disponibles en total, valor económico del inventario y categorías efectivamente utilizadas. Utiliza una comprensión de conjunto para las categorías.

### Antes de escribir el programa

Dibuja la estructura en comentarios:

~~~python
# Lista de diccionarios para productos
# Tupla de categorías válidas
# Ciclo del menú
#   Registrar
#   Listar
#   Buscar
#   Actualizar
#   Eliminar
#   Filtrar sin stock
#   Mostrar resumen
~~~

Identifica qué opciones cambian la lista y cuáles solamente leen información.

### Construcción por etapas

**Etapa 1 — Menú.** Crea el `while` y confirma que la opción 0 termina la ejecución.

**Etapa 2 — Registro.** Solicita datos, verifica las reglas y agrega un diccionario a la lista.

**Etapa 3 — Listado y búsqueda.** Recorre la lista de diccionarios, primero para mostrarlos y luego para localizar un código.

**Etapa 4 — Actualización y eliminación.** Trabaja con el diccionario encontrado. Comprueba qué ocurre cuando el código no existe.

**Etapa 5 — Informes.** Aplica comprensiones de listas y conjuntos para construir consultas.

Después de cada etapa realiza una ejecución completa. Si algo falla, revisa la operación que acabas de incorporar.

### Esqueleto inicial

Crea `gestor_inventario.py`:

~~~python
CATEGORIAS = ("Periféricos", "Componentes", "Accesorios")

productos = []
opcion = ""

while opcion != "0":
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

    opcion = input("Opción: ").strip()

    if opcion == "1":
        # Registrar y validar un diccionario
        pass
    elif opcion == "2":
        # Recorrer y mostrar productos
        pass
    elif opcion == "3":
        # Buscar por código
        pass
    elif opcion == "4":
        # Actualizar cantidad
        pass
    elif opcion == "5":
        # Eliminar producto
        pass
    elif opcion == "6":
        # Filtrar los productos sin existencias
        pass
    elif opcion == "7":
        # Calcular y mostrar el resumen
        pass
    elif opcion == "0":
        print("Programa finalizado.")
    else:
        print("Opción no válida.")
~~~

`pass` solamente mantiene la estructura mientras construyes cada operación. Sustitúyelo con tu código.

### Pistas

**Comprobar códigos repetidos.** Puedes recorrer la lista y comparar `producto["codigo"]` con el código ingresado.

**Buscar.** Conserva una referencia al diccionario cuando encuentres el código:

~~~python
encontrado = None

for producto in productos:
    if producto["codigo"] == codigo_buscado:
        encontrado = producto
        break
~~~

Después comprueba `encontrado is not None` antes de trabajar con él.

**Filtrar sin existencias.** Usa una comprensión:

~~~python
agotados = [
    producto["nombre"]
    for producto in productos
    if producto["cantidad"] == 0
]
~~~

**Categorías utilizadas.** Utiliza un conjunto:

~~~python
categorias_utilizadas = {
    producto["categoria"]
    for producto in productos
}
~~~

**Valor del inventario.** Para cada producto, multiplica precio por cantidad y suma esos subtotales. Puedes hacerlo con un acumulador dentro de `for` o con `sum()` y una comprensión.

Los errores de conversión numérica por entradas como `abc` todavía quedan fuera del alcance. El programa debe validar las reglas indicadas suponiendo que, cuando se solicita un número, el usuario introduce un valor numérico.

### Ejemplo de ejecución

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
Productos: 2
Unidades: 4
Valor del inventario: $340000.0
Categorías utilizadas: Periféricos

Opción: 0
Programa finalizado.
~~~

### Pruebas obligatorias

| Prueba | Comportamiento esperado |
|---|---|
| Listar sin productos | Mostrar mensaje de inventario vacío |
| Registrar P001 | Se agrega |
| Registrar P001 nuevamente | Rechazar código repetido |
| Registrar nombre vacío o solo espacios | Rechazar |
| Registrar categoría fuera de la tupla | Rechazar |
| Registrar precio negativo | Rechazar |
| Registrar cantidad negativa | Rechazar |
| Registrar cantidad cero | Aceptar como producto sin stock |
| Buscar P001 | Mostrar su registro |
| Buscar código desconocido | Informar que no existe |
| Actualizar a 10 unidades | Cambiar solamente la cantidad |
| Actualizar a -3 unidades | Conservar la cantidad anterior |
| Eliminar un producto existente | Borrar el registro |
| Eliminar código inexistente | No fallar |
| Consultar resumen | Reflejar los datos actuales |
| Salir | Finalizar el ciclo |

### Comprobación numérica

Para estos productos:

| Código | Precio | Cantidad | Categoría |
|---|---:|---:|---|
| P001 | 85.000 | 4 | Periféricos |
| P002 | 45.000 | 0 | Periféricos |
| P003 | 700.000 | 2 | Componentes |

El programa debe mostrar:

- 3 productos registrados;
- 6 unidades;
- valor total de inventario de $1.740.000;
- 2 categorías utilizadas;
- P002 como producto sin stock.

Calcula estos resultados manualmente antes de ejecutar.

### Reto de ampliación

Si todas las pruebas anteriores funcionan:

1. agrega una opción para aumentar o disminuir existencias mediante una operación, sin permitir que queden negativas;
2. muestra los productos ordenados por nombre sin cambiar la lista original;
3. crea un diccionario mediante comprensión que relacione código y nombre;
4. permite buscar productos por categoría;
5. muestra cuál es el producto con mayor valor de inventario (`precio * cantidad`).

### Depuración

Crea una copia del programa. Provoca y corrige:

- un ciclo que recrea `productos = []` en cada vuelta;
- un intento de consultar una clave inexistente;
- un código que duplica registros;
- una condición que permite una cantidad negativa;
- un informe que calcula mal el total al sumar precios sin multiplicar por cantidades.

Escribe un comentario breve que explique la causa de cada error.

### Revisión del código

Comprueba que:

- la lista de productos no se reinicia dentro del menú;
- cada producto es un diccionario con las mismas claves;
- las categorías permitidas se mantienen en una tupla;
- los códigos no se duplican;
- las consultas no cambian registros;
- las actualizaciones conservan los demás campos;
- el informe utiliza valores actuales del inventario;
- puedes explicar por qué utilizaste listas, tuplas, conjuntos y diccionarios en distintas partes.

---

## Cierre del laboratorio

Al finalizar deberías poder utilizar decisiones, condiciones compuestas y ciclos para construir un programa de consola; trabajar con listas, tuplas, conjuntos y diccionarios; reconocer cuáles estructuras pueden modificarse; recorrer colecciones anidadas; crear comprensiones de listas, conjuntos y diccionarios; y comprobar que los resultados de una aplicación coincidan con las reglas indicadas.

Conserva `gestor_inventario.py`. En el siguiente laboratorio podrás reorganizar esta aplicación en funciones con parámetros y valores de retorno, y trabajar con una estructura de código más sencilla de mantener.

### Consulta adicional

- [Tutorial oficial de Python — Estructuras de datos](https://docs.python.org/3/tutorial/datastructures.html)
- [Documentación de Python — Tipos integrados](https://docs.python.org/3/library/stdtypes.html)
- [Documentación de Python — Módulo collections](https://docs.python.org/3/library/collections.html)

El módulo `collections` ofrece otras estructuras especializadas, como `deque` y `Counter`, que puedes consultar más adelante. En este laboratorio trabajamos las colecciones integradas `list`, `tuple`, `set` y `dict`, que son la base para entenderlas.
