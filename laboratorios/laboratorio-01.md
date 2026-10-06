# Laboratorio 1 — Primeros pasos con Python

En este laboratorio vas a comprobar que el entorno está listo y a construir tus primeros programas en Python. La meta es que puedas crear un archivo, ejecutarlo, trabajar con datos, realizar cálculos y recibir información desde la terminal.

| Práctica | Tema |
|---|---|
| 1.1 | Verificación del entorno y primera ejecución |
| 1.2 | Variables y tipos de datos |
| 1.3 | Operadores y expresiones |
| 1.4 | Entrada de datos con `input()` |
| 1.5 | Reto integrador: cotizador de compra |

> Ejecuta cada ejemplo antes de modificarlo. Si algo falla, lee el mensaje de error e intenta identificar la causa antes de cambiar varias líneas a la vez.

---

## Práctica 1.1 — Verificación del entorno y primera ejecución

### Objetivos

Al finalizar esta práctica podrás:

- comprobar que Python está disponible;
- verificar que Visual Studio Code puede trabajar con archivos Python;
- crear y ejecutar un archivo `.py`;
- ejecutar el mismo programa desde la terminal;
- reconocer algunos errores básicos.

La mayor parte de los equipos del ambiente ya debería contar con las herramientas necesarias. Antes de instalar o modificar algo, verifica lo que ya está disponible.

### 1. Verificar Python

Abre Visual Studio Code y luego una terminal desde:

`Terminal > New Terminal`

También puedes abrirla con el atajo **Ctrl + `**.

Prueba primero:

```bash
python --version
```

En algunos equipos el comando disponible puede ser:

```bash
py --version
```

o:

```bash
python3 --version
```

Si alguno muestra una versión de **Python 3**, continúa con ese comando durante el laboratorio.

Ejemplo:

```text
Python 3.x.x
```

Si ninguno funciona, no instales ni actualices software por tu cuenta. Informa al instructor y utiliza la alternativa de ejecución indicada para el ambiente.

### Alternativa desde el navegador

Si el equipo no permite ejecutar Python localmente, puedes trabajar desde [Google Colab](https://colab.research.google.com/).

Crea un cuaderno nuevo y utiliza una celda de código para ejecutar los ejemplos. En Colab no necesitas instalar Python en el equipo.

Cuando trabajes en Colab:

- cada celda puede contener código Python;
- ejecuta la celda con el botón de reproducción;
- la salida aparece debajo de la celda;
- conserva el orden de los ejercicios para evitar confusiones.

La ruta principal del laboratorio será VS Code. Colab funciona como alternativa cuando el equipo no permite ejecutar Python localmente.

### 2. Verificar el soporte de Python en VS Code

Abre la vista de extensiones con:

**Ctrl + Shift + X**

Busca la extensión:

- **Python**, publicada por Microsoft.

Si ya está instalada, continúa.

Si no está instalada y VS Code permite agregarla, puedes hacerlo. Si la instalación está restringida, todavía puedes editar el archivo en VS Code y ejecutarlo desde la terminal siempre que Python esté disponible.

### 3. Crear el primer programa

Crea una carpeta para el laboratorio:

```text
python-laboratorio-01/
```

Ábrela desde VS Code y crea:

```text
hola_python.py
```

Escribe:

```python
print("Python está funcionando.")
```

Guarda el archivo.

### 4. Ejecutar desde VS Code

Si la extensión de Python está disponible, puedes utilizar **Run Python File**.

La salida debe incluir:

```text
Python está funcionando.
```

### 5. Ejecutar desde la terminal

Ubícate en la carpeta donde guardaste `hola_python.py`.

Si tu equipo usa `python`:

```bash
python hola_python.py
```

Si usa el lanzador de Windows:

```bash
py hola_python.py
```

Si usa `python3`:

```bash
python3 hola_python.py
```

Resultado:

```text
Python está funcionando.
```

A diferencia de Java, en esta etapa no necesitas ejecutar un comando de compilación por separado.

### Ejercicio — Modificar la salida

Modifica el programa para obtener algo semejante a:

```text
=========================
     PRIMER PROGRAMA
=========================
Nombre: Laura
Programa: ADSO
Trimestre: 5

Entorno Python listo.
```

Utiliza varias instrucciones `print(...)`.

### Laboratorio de errores

Los mensajes de error aportan información sobre lo que Python no pudo interpretar o ejecutar. Vas a provocar algunos errores de manera intencional.

Haz cada cambio por separado y corrígelo antes de pasar al siguiente.

#### Error 1 — Paréntesis incompleto

Prueba:

```python
print("Hola Python"
```

Ejecuta el archivo y localiza en el mensaje la línea donde aparece el problema.

Después corrige el código.

#### Error 2 — Texto sin comillas

Prueba:

```python
print(Hola Python)
```

Observa el mensaje y vuelve a escribir el texto correctamente.

#### Error 3 — Nombre que no existe

Prueba:

```python
mensaje = "Hola"
print(mensage)
```

Ejecuta el programa.

Compara `mensaje` con `mensage`. Corrige el nombre y ejecuta de nuevo.

### Reto

Crea:

```text
presentacion.py
```

El programa debe mostrar una presentación breve con al menos cuatro datos. Diseña tú mismo la distribución de la salida.

Ejecuta el archivo tanto desde VS Code como desde la terminal cuando ambas opciones estén disponibles.

### Comprobación

Antes de continuar, responde con tus propias palabras:

- ¿qué extensión tienen los archivos de Python?
- ¿qué comando estás usando para ejecutarlos?
- ¿dónde aparece la salida?
- ¿qué información útil encontraste en un mensaje de error?
- ¿Python diferencia nombres escritos de forma distinta?

### Referencias

- [Python — documentación oficial](https://docs.python.org/3/)
- [Getting Started with Python in VS Code](https://code.visualstudio.com/docs/python/python-tutorial)
- [Google Colab](https://colab.research.google.com/)

---

## Práctica 1.2 — Variables y tipos de datos

### Objetivos

Al finalizar esta práctica podrás:

- crear variables;
- reconocer tipos de datos básicos;
- consultar el tipo de un valor;
- modificar el contenido de una variable;
- construir salidas utilizando datos almacenados.

### 1. Guardar información

Crea:

```text
datos_basicos.py
```

Escribe:

```python
producto = "Teclado"
cantidad = 3
precio = 85000.0
disponible = True

print(producto)
print(cantidad)
print(precio)
print(disponible)
```

Ejecuta el programa.

En Python no escribimos el tipo antes del nombre de la variable. El tipo está relacionado con el valor almacenado.

### 2. Consultar el tipo

Agrega:

```python
print(type(producto))
print(type(cantidad))
print(type(precio))
print(type(disponible))
```

La salida mostrará tipos como:

```text
<class 'str'>
<class 'int'>
<class 'float'>
<class 'bool'>
```

| Tipo | Ejemplo | Uso |
|---|---|---|
| `str` | `"Monitor"` | texto |
| `int` | `25` | números enteros |
| `float` | `19.95` | números con decimales |
| `bool` | `True` | verdadero o falso |

Observa que `True` y `False` se escriben con mayúscula inicial.

### 3. Mostrar texto y variables

Puedes combinar texto y valores de varias maneras.

Una forma directa:

```python
nombre = "Laura"
edad = 20

print("Nombre:", nombre)
print("Edad:", edad)
```

También puedes utilizar una **f-string**:

```python
print(f"Nombre: {nombre}")
print(f"Edad: {edad}")
```

Prueba ambas opciones.

### Ejercicio — Perfil de aprendiz

Crea:

```text
perfil_aprendiz.py
```

Declara variables para:

- nombre;
- edad;
- trimestre;
- promedio;
- estado activo.

Muestra una ficha semejante a:

```text
-------------------------
    PERFIL DEL APRENDIZ
-------------------------
Nombre: Andrea
Edad: 20
Trimestre: 5
Promedio: 4.2
Activo: True
```

Utiliza al menos dos f-strings.

### 4. Reasignar valores

Ejecuta:

```python
cantidad = 10

print(cantidad)

cantidad = 15

print(cantidad)
```

Ahora prueba:

```python
dato = 10
print(dato)
print(type(dato))

dato = "diez"
print(dato)
print(type(dato))
```

Observa qué ocurre con el tipo.

No necesitas memorizar todavía cómo funciona internamente. Lo importante es reconocer que una variable puede recibir un valor diferente durante la ejecución.

### Ejercicio — Estado de un inventario

Crea:

```text
inventario.py
```

Comienza con:

```python
producto = "Monitor"
unidades = 8
precio = 720000.0
disponible = True
```

Muestra un primer estado.

Luego cambia:

```python
unidades = 5
precio = 699000.0
```

Muestra el estado actualizado.

La salida debe permitir distinguir claramente ambos momentos.

### 5. Nombres de variables

Utiliza nombres que indiquen qué representa cada dato.

Evita:

```python
x = "Teclado"
a = 85000
b = 3
```

Prefiere:

```python
nombre_producto = "Teclado"
precio_unitario = 85000
cantidad = 3
```

En Python es habitual separar las palabras con guion bajo.

### 6. Valores que no deberían cambiar

Python no impide reasignar una variable, pero existe una convención para indicar que un valor debería mantenerse constante: escribir su nombre en mayúsculas.

Ejemplo:

```python
IVA = 0.19
MESES_ANIO = 12
```

Esto no bloquea el cambio del valor; comunica la intención del programa.

### Antes de ejecutar — Predice la salida

Lee este programa:

```python
nombre = "Ana"
edad = 19

print(nombre)
print(edad)

edad = 20

print(edad)
```

Escribe primero las tres líneas que esperas obtener.

Luego ejecuta y compara.

### Depuración — Corrige el programa

Crea:

```text
error_variables.py
```

Copia:

```python
nombre = "Carlos"
edad = 20
promedio = 4.5
activo = true

print("Nombre:", nombre)
print("Edad:", edadd)
print("Promedio:", promedio)
print("Activo:", activo)
```

El programa contiene errores.

Corrígelos uno a uno. Ejecuta después de cada corrección y utiliza los mensajes para localizar el siguiente problema.

### Reto

Representa los datos básicos de un producto utilizando variables:

- código;
- nombre;
- categoría;
- precio;
- cantidad disponible;
- estado activo.

Agrega además:

```python
IMPUESTO = 0.19
```

Muestra una ficha semejante a:

```text
-------------------------
       PRODUCTO
-------------------------
Código: 105
Nombre: Mouse inalámbrico
Categoría: Periféricos
Precio: $78000.0
Cantidad: 12
Activo: True
Impuesto: 0.19
```

### Comprobación

Antes de continuar asegúrate de distinguir:

- variable;
- valor;
- tipo de dato;
- reasignación;
- f-string;
- convención de constantes.

---

## Práctica 1.3 — Operadores y expresiones

### Objetivos

Al finalizar esta práctica podrás:

- realizar operaciones aritméticas;
- utilizar suma, resta, multiplicación, división y residuo;
- reconocer diferencias entre `/` y `//`;
- controlar el orden de las operaciones;
- almacenar resultados intermedios.

### 1. Operadores aritméticos

| Operador | Operación |
|---|---|
| `+` | suma |
| `-` | resta |
| `*` | multiplicación |
| `/` | división |
| `//` | división entera |
| `%` | residuo |
| `**` | potencia |

Crea:

```text
operadores.py
```

Escribe:

```python
a = 10
b = 3

print(a + b)
print(a - b)
print(a * b)
print(a / b)
print(a // b)
print(a % b)
print(a ** b)
```

### Antes de ejecutar

Escribe primero el resultado que esperas en cada línea.

Luego ejecuta.

Presta especial atención a:

```python
a / b
a // b
a % b
```

### 2. División normal y división entera

Prueba:

```python
print(5 / 2)
print(5 // 2)
print(5 % 2)
```

Explica con una frase qué hace cada operador.

### 3. Usar división y residuo juntos

Una aplicación necesita convertir minutos en horas y minutos restantes.

Ejecuta:

```python
total_minutos = 135

horas = total_minutos // 60
minutos = total_minutos % 60

print("Horas:", horas)
print("Minutos restantes:", minutos)
```

Resultado:

```text
Horas: 2
Minutos restantes: 15
```

### Ejercicio — Conversión de tiempo

Crea:

```text
conversion_tiempo.py
```

Usa inicialmente:

```python
total_minutos = 367
```

Calcula:

- horas completas;
- minutos restantes.

Después prueba al menos tres valores diferentes.

### 4. Orden de las operaciones

Antes de ejecutar, predice:

```python
resultado_1 = 10 + 5 * 2
resultado_2 = (10 + 5) * 2

print(resultado_1)
print(resultado_2)
```

Ejecuta y compara.

Los paréntesis permiten hacer explícito el orden que quieres aplicar.

### Ejercicio — Promedio

Crea:

```text
calculo_promedio.py
```

Declara tres notas y calcula el promedio.

Ejemplo:

```python
nota_1 = 4.2
nota_2 = 3.8
nota_3 = 4.5
```

Muestra las tres notas y el promedio.

Prueba con otros valores.

### 5. Construir un cálculo por etapas

Crea:

```text
calculo_compra.py
```

Escribe:

```python
precio = 85000.0
cantidad = 3
porcentaje_descuento = 0.10

subtotal = precio * cantidad
descuento = subtotal * porcentaje_descuento
total = subtotal - descuento

print("Precio unitario: $", precio)
print("Cantidad:", cantidad)
print("Subtotal: $", subtotal)
print("Descuento: $", descuento)
print("Total: $", total)
```

Modifica precio, cantidad y descuento. Ejecuta nuevamente y revisa cada resultado.

### Ejercicio — Cálculo de salario

Crea:

```text
calculo_salario.py
```

Utiliza variables para:

- nombre del trabajador;
- horas trabajadas;
- valor por hora.

Calcula el pago total.

Luego agrega:

```python
APORTE = 0.04
```

Calcula cuánto representa el 4 % del pago.

El ejercicio busca practicar operaciones; no representa reglas laborales reales.

### Antes de ejecutar — Lee el cálculo

Analiza:

```python
unidades = 4
precio = 25000.0
descuento = 0.20

subtotal = unidades * precio
valor_descuento = subtotal * descuento
total = subtotal - valor_descuento

print(total)
```

Responde primero:

1. ¿cuál es el subtotal?
2. ¿cuánto vale el descuento?
3. ¿qué imprimirá el programa?

Después ejecútalo.

### Reto — Factura simple

Crea:

```text
factura_simple.py
```

Define:

- nombre del producto;
- precio unitario;
- cantidad;
- porcentaje de descuento.

Calcula y muestra:

- subtotal;
- valor del descuento;
- total después del descuento.

No hagas todos los cálculos dentro de un único `print`. Guarda cada resultado en una variable.

Prueba:

```text
Precio: 100000
Cantidad: 2
Descuento: 0.10
```

Resultado esperado:

```text
Subtotal: $200000.0
Descuento: $20000.0
Total: $180000.0
```

### Reto adicional

Agrega un porcentaje de impuesto y calcula:

- base después del descuento;
- valor del impuesto;
- total final.

---

## Práctica 1.4 — Entrada de datos con `input()`

Hasta ahora los valores han estado escritos directamente en el código. A partir de esta práctica los programas recibirán información escrita por quien los ejecuta.

### Objetivos

Al finalizar esta práctica podrás:

- leer texto con `input()`;
- convertir entradas a `int` y `float`;
- utilizar datos ingresados en expresiones;
- reconocer errores comunes de conversión;
- transformar ejercicios anteriores en programas interactivos.

### 1. Leer texto

Crea:

```text
saludo.py
```

Escribe:

```python
nombre = input("Escribe tu nombre: ")

print("Hola,", nombre)
```

Ejecuta varias veces utilizando nombres diferentes.

### 2. Todo lo que entra con `input()` comienza como texto

Crea:

```text
tipo_entrada.py
```

Escribe:

```python
edad = input("Edad: ")

print(edad)
print(type(edad))
```

Escribe:

```text
20
```

y observa el tipo.

Aunque hayas escrito números, `input()` entrega texto.

### 3. Convertir la entrada

Para trabajar con un entero:

```python
edad = int(input("Edad: "))
```

Para un decimal:

```python
precio = float(input("Precio: "))
```

Prueba:

```python
edad = int(input("Edad: "))
anio_siguiente = edad + 1

print("El próximo año tendrás", anio_siguiente, "años.")
```

### Laboratorio de errores — Conversión inválida

Ejecuta:

```python
edad = int(input("Edad: "))
print(edad)
```

Cuando el programa solicite la edad, escribe:

```text
veinte
```

Lee el mensaje de error.

No necesitas resolver todavía este tipo de error con código. Más adelante trabajaremos el manejo de excepciones. Por ahora debes reconocer que `int()` espera un valor que pueda convertirse a entero.

### Ejercicio — Datos de usuario

Crea:

```text
datos_usuario.py
```

Solicita:

- nombre;
- ciudad;
- edad;
- estatura.

Muestra una ficha con todos los datos.

Utiliza conversiones adecuadas para edad y estatura.

### 4. Utilizar entradas en operaciones

Crea:

```text
suma_interactiva.py
```

Solicita dos números enteros y muestra:

- suma;
- resta;
- multiplicación;
- división;
- división entera;
- residuo.

Antes de ejecutar con nuevos valores, intenta predecir la salida.

### Ejercicio — Conversor de temperatura

Crea:

```text
conversor_temperatura.py
```

Solicita una temperatura en grados Celsius.

Calcula Fahrenheit con:

```text
°F = (°C × 9 / 5) + 32
```

Ejemplo:

```text
Temperatura en °C: 25
25.0 °C equivalen a 77.0 °F
```

Prueba:

- 0 °C;
- 25 °C;
- 100 °C.

### Ejercicio — Salario interactivo

Transforma el ejercicio de salario.

El programa debe solicitar:

```text
Nombre del trabajador:
Horas trabajadas:
Valor por hora:
```

y mostrar un resumen semejante a:

```text
-------------------------
RESUMEN DE PAGO
-------------------------
Trabajador: Sara
Horas: 36
Valor por hora: $20000.0
Pago total: $720000.0
```

### Depuración — Programa incompleto

Completa:

```python
producto = ______________________________
precio = ________________________________
cantidad = ______________________________

subtotal = ______________________________

print("Producto:", producto)
print("Subtotal: $", subtotal)
```

El programa debe pedir los tres datos al usuario y calcular el subtotal.

Intenta resolverlo sin consultar los ejemplos anteriores.

### Reto — Conversión de unidades

Crea:

```text
conversion_unidades.py
```

Solicita una distancia en kilómetros.

Calcula:

- metros;
- centímetros;
- millas aproximadas, utilizando `1 km = 0.621371 millas`.

Muestra todos los resultados en una salida ordenada.

### Comprobación

Antes de continuar, asegúrate de poder explicar:

- qué devuelve `input()`;
- para qué sirven `int()` y `float()`;
- qué ocurre si intentas convertir texto no numérico a entero;
- por qué un programa interactivo no necesita que edites el código cada vez que cambian los datos.

---

## Práctica 1.5 — Reto integrador: cotizador de compra

En esta práctica vas a reunir lo trabajado en el laboratorio. No hay una solución completa para copiar.

### Objetivo

Construir una aplicación de consola que solicite los datos de una compra, realice los cálculos y presente un resumen ordenado.

### Requisitos

Crea:

```text
cotizador_compra.py
```

El programa debe solicitar:

- nombre del cliente;
- nombre del producto;
- precio unitario;
- cantidad;
- porcentaje de descuento;
- porcentaje de impuesto.

Los porcentajes se ingresarán como números enteros. Por ejemplo:

```text
10
```

representa 10 %.

A partir de esos datos debes obtener:

- subtotal;
- valor del descuento;
- base después del descuento;
- valor del impuesto;
- total final.

No escribas resultados calculados directamente en la salida. Cada resultado debe almacenarse primero en una variable.

### Ejemplo de ejecución

```text
=============================
      COTIZADOR PYTHON
=============================

Nombre del cliente: Laura
Producto: Teclado mecánico
Precio unitario: 180000
Cantidad: 2
Descuento (%): 10
Impuesto (%): 19

-----------------------------
RESUMEN DE COMPRA
-----------------------------
Cliente: Laura
Producto: Teclado mecánico
Precio unitario: $180000.0
Cantidad: 2
Subtotal: $360000.0
Descuento: $36000.0
Base: $324000.0
Impuesto: $61560.0
TOTAL: $385560.0
```

El impuesto de este ejercicio se aplica sobre el valor obtenido después del descuento. Se utiliza únicamente para practicar expresiones aritméticas; no representa una regla tributaria o comercial.

### Antes de programar

Escribe en comentarios, con tus propias palabras, los cálculos que necesitas realizar y el orden en que deben ocurrir.

Ejemplo:

```python
# 1. Calcular ...
# 2. Calcular ...
# 3. ...
```

Primero organiza el problema. Después escribe las expresiones.

### Construcción por etapas

Trabaja en este orden:

1. solicita los datos;
2. muestra los datos sin hacer cálculos;
3. calcula únicamente el subtotal;
4. comprueba el subtotal;
5. agrega el descuento;
6. calcula la base después del descuento;
7. agrega el impuesto;
8. organiza la salida final.

Ejecuta después de cada etapa.

### Pistas

Para convertir un porcentaje entero a una proporción decimal:

```python
porcentaje = valor_ingresado / 100
```

Utiliza nombres que expliquen qué contiene cada variable:

```python
subtotal
valor_descuento
base
valor_impuesto
total
```

Si el resultado no coincide con lo esperado, imprime temporalmente los valores intermedios.

### Pruebas

Comprueba el programa con distintos escenarios.

#### Caso 1

```text
Precio: 100000
Cantidad: 2
Descuento: 10
Impuesto: 0
```

Resultado final esperado:

```text
180000.0
```

#### Caso 2

```text
Precio: 50000
Cantidad: 3
Descuento: 0
Impuesto: 0
```

Resultado final esperado:

```text
150000.0
```

#### Caso 3

```text
Precio: 80000
Cantidad: 5
Descuento: 25
Impuesto: 0
```

Resultado final esperado:

```text
300000.0
```

#### Caso 4

Utiliza:

```text
Precio: 100000
Cantidad: 1
Descuento: 0
Impuesto: 19
```

Calcula primero el resultado manualmente. Después ejecuta el programa y compara.

### Revisión del código

Antes de terminar, revisa:

- ¿los nombres de las variables permiten entender qué almacenan?
- ¿convertiste a número los datos que necesitan operaciones?
- ¿los cálculos están separados en variables?
- ¿el usuario puede cambiar todos los datos sin editar el código?
- ¿la salida permite entender cómo se obtuvo el total?
- ¿puedes explicar cada línea que escribiste?

### Reto de ampliación

Agrega al cotizador:

- costo de envío;
- nombre del vendedor;
- código de la cotización.

El costo de envío se suma después de calcular el impuesto.

Muestra todos los datos en el resumen.

### Reto de depuración

Crea una copia del programa y provoca intencionalmente tres problemas:

1. un error de sintaxis;
2. un error por utilizar un nombre de variable inexistente;
3. un cálculo que se ejecute pero produzca un resultado incorrecto.

Corrige los tres y deja un comentario explicando qué ocurría en cada caso.

---

## Cierre del laboratorio

Al finalizar deberías poder:

- crear y ejecutar un archivo Python;
- trabajar con VS Code, terminal o una alternativa web cuando sea necesario;
- crear, consultar y modificar variables;
- reconocer tipos de datos básicos;
- utilizar f-strings para construir salidas;
- realizar operaciones aritméticas;
- distinguir `/`, `//` y `%`;
- recibir datos con `input()`;
- convertir texto a valores numéricos;
- interpretar errores sencillos;
- construir un programa de consola que reciba datos, procese información y presente un resultado.

Conserva los archivos creados durante las prácticas. Servirán como referencia en los siguientes laboratorios.
