# Laboratorio 4 — Excepciones, archivos, persistencia y POO en Python

En el [Laboratorio 3](laboratorio-03.md) construiste un gestor de inventario organizado mediante funciones, módulos y clases. Cada producto tenía sus propios datos y la clase `Inventario` administraba las operaciones.

El programa ya podía registrar, buscar, actualizar y eliminar productos. Sin embargo, al cerrarlo se perdían todos los datos. Ahora vas a resolver ese problema: primero aprenderás a reconocer y manejar errores, luego a leer y escribir archivos, y después a guardar y recuperar objetos usando JSON. También trabajarás con CSV para exportar reportes y con herencia y polimorfismo a través de ejemplos independientes.

La actividad final será una nueva versión del mismo gestor, capaz de conservar sus productos entre ejecuciones.

| Práctica | Tema |
|---|---|
| 4.1 | Excepciones, validaciones y recuperación de errores |
| 4.2 | Archivos de texto, rutas y `pathlib` |
| 4.3 | Persistencia con JSON y exportación a CSV |
| 4.4 | POO intermedia: herencia, sobrescritura, polimorfismo y composición |
| 4.5 | Reto integrador: gestor de inventario persistente |

> Trabaja los ejemplos en el orden propuesto. Antes de ejecutar, intenta anticipar qué ocurrirá; después modifica el código, provoca errores controlados y comprueba los resultados. Los fragmentos incompletos o con errores intencionales están identificados como tales.

## Preparación del entorno

Conserva el proyecto del Laboratorio 3. **No sobrescribas su versión anterior:** crea una copia dentro del directorio de este laboratorio, de manera que puedan compararse las dos implementaciones.

Organiza tus ejercicios de esta forma:

~~~text
python-laboratorio-04/
├── practica-4-1/
├── practica-4-2/
├── practica-4-3/
├── practica-4-4/
└── gestor_inventario/
~~~

Continúa utilizando Python 3 desde VS Code. Para ejecutar archivos desde la terminal utiliza el comando que funcione en tu equipo:

~~~bash
python main.py
~~~

Según la instalación también puede ser `py main.py` o `python3 main.py`.

No necesitas instalar paquetes ni trabajar con una base de datos. Utilizaremos módulos que vienen con Python: `pathlib`, `json` y `csv`.

Si trabajas en Google Colab por restricciones del ambiente, los ejemplos también pueden ejecutarse en celdas. Para los programas distribuidos en varios archivos, utiliza `%%writefile nombre.py` para crear cada módulo y `%run main.py` para ejecutar el principal. Recuerda guardar una copia de los archivos: el almacenamiento temporal de Colab puede desaparecer cuando termina la sesión.

---

## Práctica 4.1 — Excepciones, validaciones y recuperación de errores

### Objetivos

Al finalizar esta práctica podrás:

- reconocer qué es una excepción;
- distinguir una entrada inválida de un error de programación;
- utilizar `try`, `except`, `else` y `finally`;
- manejar errores concretos como `ValueError`, `TypeError` y `FileNotFoundError`;
- utilizar `raise` para rechazar datos;
- evitar que una operación fallida modifique incorrectamente el estado de la aplicación.

### 1. Cuando un programa se interrumpe

Crea `conversion_directa.py`:

~~~python
cantidad = int(input("Cantidad de productos: "))

print("Cantidad registrada:", cantidad)
~~~

Ejecuta primero con `5` y luego con `cinco`.

En el segundo caso, `int()` no puede convertir la cadena a entero y produce `ValueError`.

El mensaje suele indicar el tipo de excepción y la línea donde ocurrió. Léelo antes de cambiar el programa.

### 2. Recuperar el control con `try` y `except`

Crea `conversion_segura.py`:

~~~python
try:
    cantidad = int(input("Cantidad de productos: "))
    print("Cantidad registrada:", cantidad)
except ValueError:
    print("Debes escribir un número entero.")

print("Fin del programa.")
~~~

Ejecuta con `8` y con `abc`.

La diferencia es que ahora el programa puede mostrar un mensaje comprensible y continuar después del bloque.

**Observa:** `except ValueError` atiende ese error concreto. No significa que toda entrada convertida sea válida para el inventario. Por ejemplo, `-5` sí puede convertirse a entero, pero no representa una cantidad disponible aceptable.

### 3. Separar conversión y regla de negocio

Ejecuta:

~~~python
try:
    cantidad = int(input("Cantidad: "))
except ValueError:
    print("No es un número entero.")
else:
    if cantidad < 0:
        print("No se aceptan existencias negativas.")
    else:
        print("Cantidad aceptada:", cantidad)
~~~

`else` se ejecuta cuando las instrucciones del `try` terminan sin excepción.

Prueba `abc`, `-3`, `0` y `7`. Escribe qué mensaje debería aparecer en cada caso antes de ejecutar.

### Ejercicio — Edad válida

Crea `leer_edad.py`.

Solicita una edad y comprueba:

- que pueda convertirse a entero;
- que no sea negativa;
- que no sea superior a 120.

Muestra mensajes diferentes para errores de conversión y valores fuera del rango establecido. Prueba `veinte`, `-1`, `121` y `20`.

### 4. Reintentar una operación

Un formulario de consola puede repetir la pregunta hasta recibir un dato correcto.

Crea `lectura_repetida.py`:

~~~python
while True:
    try:
        precio = float(input("Precio: "))
    except ValueError:
        print("Debes escribir un número.")
        continue

    if precio <= 0:
        print("El precio debe ser positivo.")
        continue

    print("Precio aceptado:", precio)
    break
~~~

Prueba:

~~~text
Precio: abc
Debes escribir un número.
Precio: -100
El precio debe ser positivo.
Precio: 50000
Precio aceptado: 50000.0
~~~

`continue` inicia la siguiente iteración del ciclo. `break` lo termina cuando el valor ha sido aceptado.

**Advertencia para el siguiente laboratorio:** `float()` también reconoce valores especiales como `nan` e `inf`. Si vas a guardar precios o realizar cálculos monetarios, no basta con comprobar que sean mayores que cero. Más adelante utilizaremos `math.isfinite()` para aceptar únicamente números finitos.

### 5. Crear una excepción con `raise`

No todos los datos inválidos vienen de `input()`. Una clase también debe proteger sus propias reglas cuando otra parte del programa intenta crear un objeto incorrecto.

Crea `producto_validado.py`:

~~~python
class Producto:
    def __init__(self, nombre, precio):
        if nombre.strip() == "":
            raise ValueError("El nombre es obligatorio.")

        if precio <= 0:
            raise ValueError("El precio debe ser positivo.")

        self.nombre = nombre
        self.precio = precio


try:
    producto = Producto("Teclado", -5000)
    print(producto.nombre)
except ValueError as error:
    print("No se pudo crear:", error)
~~~

`raise ValueError(...)` comunica que el dato no cumple las reglas del constructor.

En este ejemplo los argumentos se consideran de los tipos previstos. Para validar también cadenas nulas o tipos incorrectos se necesitarían comprobaciones adicionales. El reto integrador se concentrará en las entradas habituales del menú y los datos leídos del archivo.

### 6. Diferenciar `ValueError` y `TypeError`

Prueba:

~~~python
try:
    cantidad = int("abc")
except ValueError:
    print("El contenido no representa un entero.")
~~~

Ahora:

~~~python
try:
    cantidad = int(None)
except TypeError:
    print("El tipo recibido no permite esa conversión.")
~~~

`ValueError` suele indicar un valor inadecuado para una operación; `TypeError` indica un tipo de dato incompatible con la operación realizada.

No es necesario capturar ambas excepciones siempre. Atiende los errores que esperas y sabes resolver.

### 7. `finally` y el cierre de operaciones

Crea `bloques_excepcion.py`:

~~~python
try:
    numero = int(input("Número: "))
except ValueError:
    print("Entrada no numérica.")
else:
    print("Número aceptado:", numero)
finally:
    print("Finalizó el intento de lectura.")
~~~

Prueba con `10` y `hola`.

El bloque `finally` se ejecuta al salir del `try`, haya ocurrido o no la excepción, en condiciones normales de ejecución.

Más adelante veremos que `with open(...)` resulta más cómodo para trabajar con archivos porque administra su cierre automáticamente.

### 8. Reconocer un archivo inexistente

Crea `archivo_inexistente.py`:

~~~python
from pathlib import Path

ruta = Path("archivo_que_no_existe.txt")

try:
    contenido = ruta.read_text(encoding="utf-8")
    print(contenido)
except FileNotFoundError:
    print("No se encontró el archivo solicitado.")
~~~

Ejecuta y observa que el programa controla la ausencia del archivo.

No confundas un archivo inexistente con uno que existe pero está dañado. En el primer caso podemos decidir crear información inicial; en el segundo no debemos sobrescribirla silenciosamente.

### Ejercicio — División segura

Crea `division_segura.py`.

Solicita dos números y calcula su división.

Debe diferenciar:

- entrada que no puede convertirse a número;
- divisor igual a cero;
- división válida.

Utiliza `try` y los bloques que consideres necesarios. Prueba `10 / 2`, `10 / 0` y una entrada `abc`.

### Depuración — Capturar lo que no corresponde

Analiza:

~~~python
try:
    numero = int("hola")
except ZeroDivisionError:
    print("No se pudo convertir.")
~~~

¿Por qué la excepción no se atiende?

Corrige el tipo de excepción y vuelve a ejecutar.

### Depuración — Se modificó el estado demasiado pronto

Observa este fragmento incompleto de una operación de inventario:

~~~python
producto._cantidad = nueva_cantidad

if nueva_cantidad < 0:
    print("Cantidad inválida.")
~~~

¿Por qué la comprobación llega demasiado tarde? Reordena el procedimiento para que una entrada rechazada no cambie los datos.

### Reto — Registro de compras con validaciones

Crea `compras_seguras.py`.

El programa debe permitir registrar varios importes de compra y terminar cuando el usuario escriba `0`.

- Acepta valores numéricos positivos.
- Rechaza texto no convertible.
- Rechaza valores negativos.
- Al terminar, muestra cuántas compras se aceptaron y la suma acumulada.

Prueba errores entre dos valores correctos. El error no debe borrar el acumulado ni terminar el ciclo.

### Comprobación

Antes de continuar verifica que puedas explicar:

- cuándo usar `try/except` y cuándo usar `if`;
- qué hace `raise`;
- para qué sirve `else`;
- qué comportamiento tiene `finally`;
- por qué una operación fallida no debe dejar datos parcialmente modificados.

---

## Práctica 4.2 — Archivos de texto, rutas y `pathlib`

### Objetivos

Al finalizar esta práctica podrás:

- crear, leer y sobrescribir archivos de texto;
- utilizar `with open(...)`;
- distinguir los modos `r`, `w` y `a`;
- trabajar con codificación UTF-8;
- construir rutas con `pathlib.Path`;
- reconocer los riesgos de utilizar una ruta relativa sin conocer la carpeta de ejecución.

### 1. Guardar texto

Crea `escribir_archivo.py`:

~~~python
from pathlib import Path

ruta = Path("mensaje.txt")
ruta.write_text("Primer archivo de Python.\n", encoding="utf-8")

print("Archivo guardado:", ruta)
~~~

Ejecuta dentro de `practica-4-2`.

Comprueba que se creó `mensaje.txt`. Ábrelo desde VS Code y revisa su contenido.

`Path.write_text()` crea el archivo o **reemplaza todo su contenido** si ya existe. Ejecútalo de nuevo con otra frase para observar el cambio.

### 2. Leer texto

Crea `leer_archivo.py` en la misma carpeta:

~~~python
from pathlib import Path

ruta = Path("mensaje.txt")

contenido = ruta.read_text(encoding="utf-8")

print("Contenido:")
print(contenido)
~~~

Si el archivo existe, la lectura mostrará su texto.

Si no existe, aparecerá `FileNotFoundError`. Atiende ese error como aprendiste en la práctica anterior.

### 3. Los modos de `open()`

La función `open()` permite controlar cómo se utiliza un archivo.

| Modo | Significado | Precaución |
|---|---|---|
| `"r"` | leer | falla si el archivo no existe |
| `"w"` | escribir | crea o reemplaza el contenido existente |
| `"a"` | agregar al final | crea el archivo si no existe |
| `"x"` | crear uno nuevo | falla si el archivo ya existe |

No utilices `"w"` para agregar información a un archivo que deseas conservar: reemplazaría su contenido anterior.

### 4. Utilizar `with open(...)`

Crea `notas_texto.py`:

~~~python
with open("notas.txt", "w", encoding="utf-8") as archivo:
    archivo.write("Primera observación\n")
    archivo.write("Segunda observación\n")

with open("notas.txt", "r", encoding="utf-8") as archivo:
    contenido = archivo.read()

print(contenido)
~~~

`with` se encarga de cerrar el archivo cuando termina el bloque, incluso si ocurre una excepción durante el trabajo con él.

### 5. Agregar sin reemplazar

Ejecuta en `agregar_notas.py`:

~~~python
with open("notas.txt", "a", encoding="utf-8") as archivo:
    archivo.write("Una observación adicional\n")
~~~

Abre `notas.txt` y confirma que se conservan las líneas anteriores.

Ejecuta el mismo script dos veces: ¿qué ocurre con la última línea?

### Ejercicio — Bitácora de pruebas

Crea `bitacora.py`.

Cada vez que se ejecute debe solicitar:

- nombre de la prueba;
- resultado: aprobada o fallida;
- observación breve.

Agrega los datos al final de `pruebas.txt`, en una línea por prueba.

Después ejecuta varias veces y comprueba que no se pierdan las entradas anteriores.

### 6. Leer por líneas

Crea `recorrer_lineas.py`:

~~~python
with open("pruebas.txt", "r", encoding="utf-8") as archivo:
    for numero, linea in enumerate(archivo, start=1):
        print(numero, "-", linea.strip())
~~~

Si completaste la bitácora anterior, podrás recorrer sus registros.

`strip()` elimina espacios sobrantes y el salto de línea final. No cambia el contenido del archivo; solamente modifica la cadena que se presenta.

### 7. Carpetas y rutas con `Path`

Crea `crear_carpeta.py`:

~~~python
from pathlib import Path

carpeta = Path("datos")
carpeta.mkdir(parents=True, exist_ok=True)

ruta = carpeta / "inventario.txt"
ruta.write_text("Inventario inicial\n", encoding="utf-8")

print("Ubicación:", ruta)
~~~

El operador `/` combina los componentes de una ruta de forma adecuada para el sistema operativo.

`mkdir(parents=True, exist_ok=True)` crea el directorio si es necesario y no falla porque ya exista.

### 8. Ruta relativa y ruta del proyecto

Una ruta como `Path("datos/inventario.txt")` depende del **directorio actual desde el cual ejecutas Python**.

Esto puede generar confusión cuando ejecutas `main.py` desde otra carpeta.

Para localizar un archivo tomando como referencia el módulo donde escribiste el código, puedes utilizar `__file__`:

~~~python
from pathlib import Path

BASE_DIR = Path(__file__).resolve().parent
RUTA_DATOS = BASE_DIR / "datos" / "inventario.txt"

RUTA_DATOS.parent.mkdir(parents=True, exist_ok=True)
RUTA_DATOS.write_text("Datos del proyecto\n", encoding="utf-8")

print(RUTA_DATOS)
~~~

Este ejemplo debe guardarse en un archivo `.py` y ejecutarse como tal. En una celda interactiva de Colab `__file__` no suele estar definido; cuando trabajes allí, crea el archivo con `%%writefile` y ejecútalo mediante `%run`.

### Ejercicio — Carpetas de reportes

Crea `generar_reporte.py`.

Debe:

1. crear una carpeta `reportes` si no existe;
2. escribir un archivo `resumen.txt` con tres líneas de información;
3. volver a leerlo y mostrarlo;
4. indicar la ruta donde quedó guardado.

Utiliza `Path` y `encoding="utf-8"`.

### Depuración — Sobrescritura accidental

Ejecuta:

~~~python
from pathlib import Path

ruta = Path("historial.txt")
ruta.write_text("Registro 1\n", encoding="utf-8")
ruta.write_text("Registro 2\n", encoding="utf-8")

print(ruta.read_text(encoding="utf-8"))
~~~

¿Por qué solo queda el segundo registro?

Corrige el programa usando `with open(..., "a", ...)` para agregar, o escribiendo ambas líneas en una misma operación.

### Reto — Historial persistente de operaciones

Crea `historial_operaciones.py`.

El programa debe mantener un menú:

~~~text
1. Registrar operación
2. Mostrar historial
0. Salir
~~~

Cada operación tendrá un concepto y un valor.

Guarda cada registro como una línea de texto en `operaciones.txt`. Al reiniciar el programa, el historial anterior debe seguir disponible.

Cuando todavía no exista el archivo, la opción de consulta debe mostrar un mensaje comprensible en lugar de detener la ejecución.

No utilices JSON todavía: el objetivo de esta práctica es reconocer cómo funcionan los archivos de texto y las rutas.

### Comprobación

Antes de continuar comprueba que puedes explicar:

- qué diferencia hay entre sobrescribir y agregar;
- por qué usar `with`;
- qué ocurre cuando falta un archivo de lectura;
- para qué sirve `encoding="utf-8"`;
- por qué conviene construir rutas a partir de la ubicación del módulo.

---

## Práctica 4.3 — Persistencia con JSON y exportación a CSV

### Objetivos

Al finalizar esta práctica podrás:

- convertir estructuras de Python a JSON;
- guardar y recuperar listas de diccionarios;
- distinguir los datos de un objeto de su representación serializable;
- reconocer errores de formato JSON;
- escribir y leer registros CSV;
- construir una exportación que pueda revisarse desde otra aplicación;
- evitar que un error de lectura destruya un archivo existente.

### 1. ¿Por qué JSON?

En el inventario tenemos una lista de productos con varios campos. Un archivo de texto plano podría guardar cada producto en una línea, pero después tendríamos que definir cómo interpretar cada parte.

JSON permite conservar datos estructurados como objetos, listas, cadenas, números, booleanos y valores nulos.

Crea `primer_json.py`:

~~~python
import json
from pathlib import Path

productos = [
    {"codigo": "P001", "nombre": "Teclado", "precio": 85000, "cantidad": 4},
    {"codigo": "P002", "nombre": "Mouse", "precio": 45000, "cantidad": 0}
]

ruta = Path("productos.json")

with ruta.open("w", encoding="utf-8") as archivo:
    json.dump(productos, archivo, ensure_ascii=False, indent=4)

print("Productos guardados.")
~~~

Abre el archivo resultante.

`ensure_ascii=False` permite escribir normalmente caracteres como á, é o ñ. `indent=4` organiza el contenido para facilitar su lectura.

### 2. Recuperar los datos

Crea `leer_json.py`:

~~~python
import json
from pathlib import Path

ruta = Path("productos.json")

with ruta.open("r", encoding="utf-8") as archivo:
    productos = json.load(archivo)

for producto in productos:
    print(producto["codigo"], "-", producto["nombre"])
~~~

Resultado:

~~~text
P001 - Teclado
P002 - Mouse
~~~

Después cierra el programa y vuelve a ejecutarlo: los productos siguen en el archivo.

### 3. `dump`, `load`, `dumps` y `loads`

| Función | Uso |
|---|---|
| `json.dump(datos, archivo)` | escribe JSON en un archivo abierto |
| `json.load(archivo)` | lee JSON desde un archivo abierto |
| `json.dumps(datos)` | produce una cadena de texto JSON |
| `json.loads(texto)` | interpreta una cadena de texto JSON |

Prueba en `json_cadenas.py`:

~~~python
import json

producto = {"nombre": "Cámara", "precio": 120000}

texto = json.dumps(producto, ensure_ascii=False)
recuperado = json.loads(texto)

print(texto)
print(recuperado["nombre"])
~~~

### Ejercicio — Catálogo de libros

Crea `libros_json.py`.

Guarda tres libros como una lista de diccionarios en `libros.json`. Cada registro tendrá código, título, autor y año.

Después crea un programa de lectura que muestre los libros guardados. Modifica uno de los registros del archivo y confirma qué ocurre al volver a cargarlo.

### 4. Los objetos de una clase no se guardan directamente

En el Laboratorio 3 creaste objetos `Producto`. No puedes asumir que `json.dump()` sabe cómo convertir cualquier instancia de una clase propia.

Analiza:

~~~python
import json

class Producto:
    def __init__(self, codigo, nombre):
        self.codigo = codigo
        self.nombre = nombre


producto = Producto("P001", "Teclado")
print(json.dumps(producto))
~~~

Ejecuta y observa el `TypeError`.

Para guardar un objeto necesitas convertir sus datos a una estructura compatible con JSON.

### 5. Convertir objetos a diccionarios

Completa el ejemplo anterior agregando este método **dentro de la clase `Producto`**:

~~~python
    def a_diccionario(self):
        return {
            "codigo": self.codigo,
            "nombre": self.nombre
        }
~~~

Y reemplaza la última línea por:

~~~python
print(json.dumps(producto.a_diccionario(), ensure_ascii=False))
~~~

Ahora sí obtendrás una cadena JSON.

### 6. Reconstruir un objeto

Agrega también **dentro de la clase `Producto`**:

~~~python
    @classmethod
    def desde_diccionario(cls, datos):
        return cls(
            datos["codigo"],
            datos["nombre"]
        )
~~~

Este método de clase permite crear una instancia a partir de un diccionario.

Pruébalo fuera de la clase:

~~~python
datos = {"codigo": "P002", "nombre": "Mouse"}

nuevo = Producto.desde_diccionario(datos)

print(type(nuevo))
print(nuevo.nombre)
~~~

`cls` representa la clase que recibe la llamada. En este caso se utiliza para crear un nuevo `Producto`.

**En el proyecto final**, `a_diccionario()` deberá incluir también categoría, precio y cantidad, y `desde_diccionario()` deberá reconstruir los cinco datos usando las validaciones del constructor.

### 7. Un JSON puede ser inválido

Abre una copia de `productos.json` y elimina intencionalmente una coma o una comilla.

Después prueba:

~~~python
import json
from pathlib import Path

ruta = Path("productos.json")

try:
    with ruta.open("r", encoding="utf-8") as archivo:
        productos = json.load(archivo)
except json.JSONDecodeError:
    print("El archivo no contiene JSON válido.")
~~~

`json.JSONDecodeError` indica un problema en el formato del documento JSON.

La ausencia del archivo produce otro error: `FileNotFoundError`.

### 8. Distinguir los estados de un archivo

Para la aplicación final conviene diferenciar tres casos:

| Situación | Comportamiento esperado |
|---|---|
| El archivo todavía no existe | Iniciar un inventario nuevo y vacío |
| El archivo existe y es válido | Recuperar los productos |
| El archivo existe, pero está dañado o contiene datos inválidos | Informar el problema y **no sobrescribirlo automáticamente** |

La tercera situación es fundamental.

No utilices una solución como esta:

~~~python
try:
    datos = cargar_archivo()
except Exception:
    datos = []
    guardar_archivo(datos)
~~~

**Este es un ejemplo de lo que no debes hacer.** Podría sustituir información dañada, pero recuperable, por una lista vacía.

### Ejercicio — Respetar un archivo dañado

Crea `lectura_protegida.py`.

Debe:

1. intentar cargar `productos.json`;
2. empezar con una lista vacía cuando el archivo no exista;
3. informar si hay un error de formato;
4. evitar guardar sobre el archivo problemático;
5. permitir revisar su contenido antes de tomar otra decisión.

Prueba los tres escenarios de la tabla anterior.

### 9. ¿Para qué sirve CSV?

CSV organiza datos tabulares en filas y columnas. Es útil para exportar informes que se abrirán con aplicaciones de hojas de cálculo.

Crea `exportar_csv.py`:

~~~python
import csv
from pathlib import Path

productos = [
    {"codigo": "P001", "nombre": "Teclado", "precio": 85000, "cantidad": 4},
    {"codigo": "P002", "nombre": "Mouse", "precio": 45000, "cantidad": 0}
]

ruta = Path("reporte.csv")

with ruta.open("w", newline="", encoding="utf-8") as archivo:
    campos = ["codigo", "nombre", "precio", "cantidad"]
    escritor = csv.DictWriter(archivo, fieldnames=campos)

    escritor.writeheader()
    escritor.writerows(productos)

print("Reporte exportado:", ruta)
~~~

`newline=""` es la configuración recomendada para escribir CSV con el módulo `csv`.

### 10. Leer un CSV con encabezados

Crea `leer_csv.py`:

~~~python
import csv
from pathlib import Path

ruta = Path("reporte.csv")

with ruta.open("r", newline="", encoding="utf-8") as archivo:
    lector = csv.DictReader(archivo)

    for producto in lector:
        print(producto["codigo"], producto["nombre"], producto["precio"])
~~~

Observa que los valores recuperados desde CSV son cadenas, aunque las columnas representen números. Si necesitas operar con precio o cantidad, conviértelos al tipo correspondiente.

### Ejercicio — Exportación de ventas

Crea `ventas_csv.py`.

Registra cuatro ventas con código, producto, cantidad y total. Exporta un CSV con encabezados.

Después léelo nuevamente y calcula el total vendido, convirtiendo a número los valores de la columna correspondiente.

### 11. JSON y CSV cumplen propósitos distintos

| JSON | CSV |
|---|---|
| Puede representar estructuras anidadas | Representa filas y columnas |
| Adecuado para guardar datos del inventario | Adecuado para exportar informes |
| Conserva tipos básicos de números y booleanos | Los campos leídos se obtienen normalmente como texto |
| Lo utilizaremos para recuperar la aplicación | Lo utilizaremos para generar reportes |

En este laboratorio el JSON será la fuente de datos persistente. El CSV será una **exportación**, no una segunda base de datos que deba mantenerse sincronizada manualmente.

### Depuración — Archivo JSON mal formado

Analiza este contenido:

~~~json
[
  {"codigo": "P001", "nombre": "Teclado"}
  {"codigo": "P002", "nombre": "Mouse"}
]
~~~

¿Por qué no puede interpretarse como JSON válido? Corrige el contenido de una copia y comprueba con `json.load()`.

### Reto — Inventario sencillo con JSON y CSV

Crea `inventario_archivos.py`.

Utiliza una lista de diccionarios y construye tres operaciones:

- guardar productos en `inventario.json`;
- recuperar los productos al ejecutar nuevamente;
- exportarlos a `inventario.csv`.

Prueba la recuperación después de cerrar el programa. Luego provoca un error de formato JSON en una copia para verificar que se informa sin reemplazar el archivo por una lista vacía.

Este ejercicio puede resolverse todavía sin clases propias. El reto integrador combinará el almacenamiento con los objetos creados en el Laboratorio 3.

### Comprobación

Antes de continuar comprueba que puedas explicar:

- por qué `json.dump(objeto)` no funciona automáticamente con cualquier objeto propio;
- cómo convertir un objeto en diccionario y reconstruirlo;
- qué diferencia hay entre un archivo inexistente y un JSON dañado;
- por qué JSON será el formato de persistencia y CSV el de reportes.

---

## Práctica 4.4 — POO intermedia: herencia, sobrescritura, polimorfismo y composición

### Objetivos

Al finalizar esta práctica podrás:

- crear una clase que hereda de otra;
- utilizar `super()` para reutilizar la inicialización;
- sobrescribir un método heredado;
- reconocer el polimorfismo en una llamada común;
- comparar herencia y composición;
- decidir cuándo no es necesario agregar una relación de herencia.

La herencia puede ser útil, pero **no es un requisito automático de todo proyecto orientado a objetos**. En esta práctica conocerás sus fundamentos con ejemplos concretos. El inventario final seguirá utilizando principalmente composición: un `Inventario` contiene objetos `Producto`.

### 1. Una clase base y una clase especializada

Crea `herencia_basica.py`:

~~~python
class Empleado:
    def __init__(self, nombre):
        self.nombre = nombre

    def describir(self):
        return f"Empleado: {self.nombre}"


class Instructor(Empleado):
    def explicar(self):
        return f"{self.nombre} está orientando una actividad."


persona = Instructor("Laura")

print(persona.describir())
print(persona.explicar())
~~~

`Instructor(Empleado)` indica que `Instructor` hereda de `Empleado`.

El objeto puede utilizar el método `describir()` recibido de la clase base y el método `explicar()` definido en la clase especializada.

### 2. Reutilizar el inicializador con `super()`

Ahora queremos que cada instructor también tenga una especialidad.

Crea `instructor.py`:

~~~python
class Empleado:
    def __init__(self, nombre):
        self.nombre = nombre


class Instructor(Empleado):
    def __init__(self, nombre, especialidad):
        super().__init__(nombre)
        self.especialidad = especialidad


instructor = Instructor("Ana", "Python")

print(instructor.nombre)
print(instructor.especialidad)
~~~

`super().__init__(nombre)` llama al inicializador de la clase base en este caso. La clase especializada agrega el atributo `especialidad`.

### Ejercicio — Vehículos

Crea una clase `Vehiculo` con marca y modelo. Después crea `Motocicleta` que herede de `Vehiculo` y agregue cilindraje.

Utiliza `super()` para inicializar marca y modelo.

Crea dos motocicletas y muestra todos sus atributos.

### 3. Sobrescritura de métodos

Una clase especializada puede ofrecer una versión propia de un método heredado.

Crea `sobrescritura.py`:

~~~python
class Notificacion:
    def enviar(self):
        return "Enviando notificación general"


class Correo(Notificacion):
    def enviar(self):
        return "Enviando correo electrónico"


class MensajeTexto(Notificacion):
    def enviar(self):
        return "Enviando mensaje de texto"


notificaciones = [Notificacion(), Correo(), MensajeTexto()]

for notificacion in notificaciones:
    print(notificacion.enviar())
~~~

Antes de ejecutar predice las tres líneas.

El mismo nombre de método produce resultados diferentes según la clase del objeto.

### 4. Polimorfismo

En el ejemplo anterior, el ciclo invoca `enviar()` sin tener que construir una condición para reconocer cada tipo.

Esa capacidad de trabajar con objetos que responden a una operación común es una forma de polimorfismo.

El polimorfismo en Python no depende exclusivamente de la herencia. También podemos escribir clases diferentes que implementen un método con el mismo propósito y utilizarlas de la misma manera.

### Ejercicio — Formatos de reporte

Crea dos clases:

- `ReporteTexto` con método `generar(datos)`;
- `ReporteResumen` con método `generar(datos)`.

Para la primera, `generar` devolverá una frase que incluya todos los datos. Para la segunda, devolverá una frase con la cantidad de registros.

Construye una lista con ambos objetos y recórrela llamando `generar()` sin distinguir los tipos mediante `if`.

### 5. Composición: un objeto utiliza otros objetos

En el gestor de inventario ya trabajaste con esta relación:

~~~python
class Inventario:
    def __init__(self):
        self._productos = []
~~~

Un inventario **contiene** productos; no es una especialización de producto. Por eso no tendría sentido declarar `class Inventario(Producto)`.

La relación adecuada se expresa al conservar instancias de `Producto` dentro de `Inventario`.

Otro ejemplo es un servicio que utiliza un objeto de almacenamiento:

~~~python
class Almacenamiento:
    def guardar(self, datos):
        print("Guardando", len(datos), "registros")


class ServicioInventario:
    def __init__(self, almacenamiento):
        self.almacenamiento = almacenamiento

    def guardar_inventario(self, productos):
        self.almacenamiento.guardar(productos)


servicio = ServicioInventario(Almacenamiento())
servicio.guardar_inventario(["Teclado", "Mouse"])
~~~

Este ejemplo simplificado imprime un mensaje; **no escribe un archivo**. Sirve para observar que un objeto puede delegar una responsabilidad en otro.

### 6. Herencia frente a composición

| Situación | Relación adecuada |
|---|---|
| `Instructor` es un tipo de `Empleado` | Herencia puede tener sentido |
| `Inventario` contiene productos | Composición |
| `ServicioInventario` utiliza almacenamiento | Composición |
| Varios objetos ofrecen `generar()` | Polimorfismo |

No utilices herencia para evitar unas pocas líneas repetidas si las clases no tienen una relación clara de especialización.

### Depuración — No se inicializó la clase base

Observa:

~~~python
class Persona:
    def __init__(self, nombre):
        self.nombre = nombre


class Aprendiz(Persona):
    def __init__(self, nombre, ficha):
        self.ficha = ficha


aprendiz = Aprendiz("Carlos", "123456")
print(aprendiz.nombre)
~~~

¿Por qué falla la última línea?

Corrige la inicialización mediante `super().__init__(nombre)`.

### Reto — Sistema de notificaciones

Crea una clase `Notificacion` y dos especializaciones, `NotificacionCorreo` y `NotificacionConsola`.

Cada una debe implementar `enviar(mensaje)` de manera diferente. Construye una colección con los dos objetos y recórrela llamando al mismo método.

Después explica qué beneficio tiene utilizar una operación común y qué responsabilidades deberían permanecer fuera de cada notificación.

### Comprobación

Antes de continuar comprueba que puedes explicar:

- qué significa heredar;
- para qué sirve `super()`;
- qué es sobrescribir un método;
- cómo se reconoce el polimorfismo;
- por qué el gestor de inventario utiliza composición y no necesita heredar de `Producto`.

---

## Práctica 4.5 — Reto integrador: gestor de inventario persistente

Vas a transformar el gestor orientado a objetos del Laboratorio 3 en una aplicación que conserve la información después de cerrarse.

La nueva versión seguirá utilizando las clases `Producto` e `Inventario`. Agregarás un módulo de almacenamiento para JSON y otro para exportar CSV. No necesitas crear una jerarquía de herencia artificial para implementar estas funciones.

### Objetivo

Construir un programa de consola que mantenga las operaciones de inventario del laboratorio anterior y añada:

- carga de productos desde JSON;
- guardado de los cambios realizados;
- exportación de reportes CSV;
- validaciones y manejo de errores de entrada y archivos;
- separación de responsabilidades entre clases y módulos.

### 1. Estructura propuesta

En la carpeta `gestor_inventario` crea o adapta:

~~~text
gestor_inventario/
├── main.py
├── producto.py
├── inventario.py
├── almacenamiento.py
├── reportes.py
├── datos/
│   └── productos.json
├── reportes/
│   └── inventario.csv
└── README.md
~~~

Los archivos `productos.json` e `inventario.csv` se generan al utilizar las funcionalidades correspondientes; no necesitas crearlos manualmente con contenido de ejemplo.

Puedes conservar una copia del proyecto del Laboratorio 3 y evolucionarla dentro de este nuevo directorio.

### 2. Distribución de responsabilidades

| Archivo | Responsabilidad |
|---|---|
| `producto.py` | Representa cada producto, valida sus datos y permite convertirlos a un diccionario |
| `inventario.py` | Administra objetos `Producto`: registrar, buscar, actualizar, eliminar y consultar |
| `almacenamiento.py` | Lee y escribe el JSON del inventario |
| `reportes.py` | Construye y exporta el reporte CSV |
| `main.py` | Muestra el menú, lee entradas, coordina operaciones y presenta mensajes |
| `README.md` | Explica el proyecto, ejecución, estado y comprobaciones |

Evita utilizar `input()` dentro de `Producto`, `Inventario`, `almacenamiento.py` o `reportes.py`.

### 3. Mantener las funcionalidades anteriores

El menú debe conservar las operaciones principales del Laboratorio 3:

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
8. Guardar inventario
9. Exportar reporte CSV
0. Salir
~~~

La opción `0` finaliza el programa.

Los datos se cargan al iniciar. Las operaciones de consulta no deben modificar el inventario.

### 4. Reglas de los productos

Cada producto mantiene:

- código único;
- nombre no vacío;
- categoría permitida;
- precio positivo;
- cantidad entera no negativa.

Conserva la tupla de categorías:

~~~python
CATEGORIAS = ("Periféricos", "Componentes", "Accesorios")
~~~

Rechaza:

- códigos duplicados;
- nombres vacíos;
- categorías desconocidas;
- precios cero, negativos o no finitos;
- cantidades negativas o de tipo incorrecto.

Para comprobar que el precio es finito puedes usar:

~~~python
import math

if not math.isfinite(precio):
    raise ValueError("El precio debe ser un número finito.")
~~~

Este fragmento debe formar parte de una validación en la que `precio` sea un valor numérico. Si viene de `input()`, conviértelo y atiende errores de conversión antes de usarlo.

### 5. Convertir objetos en datos

En `Producto` agrega:

~~~python
def a_diccionario(self):
    return {
        "codigo": self.codigo,
        "nombre": self.nombre,
        "categoria": self.categoria,
        "precio": self.precio,
        "cantidad": self.cantidad
    }
~~~

**Este fragmento se agrega dentro de la clase `Producto`.** Debe usar las propiedades de consulta definidas en el Laboratorio 3.

Para reconstruir un producto agrega otro método **dentro de esa misma clase**:

~~~python
@classmethod
def desde_diccionario(cls, datos):
    return cls(
        datos["codigo"],
        datos["nombre"],
        datos["categoria"],
        datos["precio"],
        datos["cantidad"]
    )
~~~

Comprueba primero con un solo producto:

1. crea un objeto `Producto` válido;
2. convierte sus datos con `a_diccionario()`;
3. construye otro objeto con `desde_diccionario()`;
4. muestra ambos y confirma que contienen los mismos datos.

No sustituyas el constructor por asignaciones directas a atributos internos: al reconstruir los datos también deben aplicarse las validaciones.

### 6. Exportar e importar el estado del inventario

En `Inventario` puedes agregar un método que devuelva una lista de diccionarios, con información de todos sus productos.

Interfaz sugerida:

~~~python
def exportar_datos(self):
    # Devuelve una lista de diccionarios.
    pass
~~~

Para recuperar un inventario desde datos ya validados, puedes construir objetos `Producto` y registrarlos uno por uno en una instancia nueva.

La carga debe cumplir dos reglas:

- no admitir códigos duplicados;
- no dejar una carga parcial si uno de los registros está incorrecto.

**Pista:** construye primero un inventario temporal con todos los registros. Solo cuando se compruebe que cada producto es válido, úsalo como inventario activo.

No reemplaces los datos que ya funcionan con una carga incompleta.

### 7. Ruta de almacenamiento

En `almacenamiento.py` utiliza `pathlib` para ubicar los archivos respecto del proyecto:

~~~python
from pathlib import Path

BASE_DIR = Path(__file__).resolve().parent
RUTA_JSON = BASE_DIR / "datos" / "productos.json"
~~~

Recuerda crear la carpeta `datos` si hace falta antes de guardar.

Así, el programa podrá encontrar el mismo archivo aunque lo ejecutes desde otra carpeta.

### 8. Guardar en JSON

En `almacenamiento.py` define una función como:

~~~python
def guardar_inventario(inventario):
    # Obtiene los datos mediante el método público del inventario.
    # Crea el directorio si no existe.
    # Escribe JSON con indentación y UTF-8.
    # Informa el resultado mediante retorno o excepción.
    pass
~~~

Puedes basarte en este ejemplo de escritura, que se incorpora **dentro de la función** cuando ya dispongas de `ruta` y `datos`:

~~~python
import json

with ruta.open("w", encoding="utf-8") as archivo:
    json.dump(datos, archivo, ensure_ascii=False, indent=4)
~~~

En esta etapa basta con guardar correctamente el estado y atender fallos de escritura. Una mejora posterior será escribir primero a un archivo temporal y reemplazar el original solo al completar el guardado.

### 9. Cargar desde JSON

Define otra función en `almacenamiento.py`:

~~~python
def cargar_inventario():
    # Si el archivo no existe, devuelve un inventario vacío.
    # Si existe, lee el JSON y valida los registros.
    # Reconstruye los objetos Producto.
    # No sobrescribe archivos dañados.
    pass
~~~

Al iniciar la aplicación:

- si no existe `productos.json`, comienza con un inventario vacío;
- si existe y es válido, recupera todos los productos;
- si tiene errores de formato o registros inválidos, informa el problema y **evita guardar automáticamente sobre ese archivo**.

Para evitar una pérdida accidental de datos, ante un archivo existente y dañado puedes impedir las operaciones de modificación hasta que se resuelva el problema. No hace falta construir un sistema de reparación de archivos.

### 10. Guardado después de modificaciones

La aplicación debe poder guardar desde el menú y debe conservar los cambios al finalizar una sesión normal.

Una estrategia sencilla es:

1. registrar, actualizar o eliminar un producto;
2. si la operación fue aceptada, intentar guardar el inventario actualizado;
3. informar si el guardado fue correcto;
4. si hubo un error de escritura, advertir que los cambios podrían existir solamente en memoria.

Si una operación se rechaza, no debe guardarse como si hubiera sido exitosa.

No anuncies que el inventario fue guardado hasta confirmar que la operación de escritura terminó correctamente.

### 11. Exportar un reporte CSV

En `reportes.py` implementa:

~~~python
def exportar_csv(inventario):
    # Obtener datos mediante la interfaz pública del inventario.
    # Crear carpeta de reportes.
    # Escribir CSV con encabezados.
    # Devolver la ruta del archivo generado.
    pass
~~~

Campos mínimos:

~~~text
codigo,nombre,categoria,precio,cantidad,valor_inventario
~~~

La columna `valor_inventario` es `precio * cantidad` para cada producto.

Puedes utilizar `csv.DictWriter` como en la práctica 4.3.

El CSV es una exportación. Si luego cambian las cantidades, el reporte anterior no tiene por qué actualizarse automáticamente; el usuario puede volver a exportarlo para obtener un archivo nuevo.

### 12. Ejemplo de datos persistidos

Después de registrar dos productos y guardar, el archivo JSON puede contener:

~~~json
[
    {
        "codigo": "P001",
        "nombre": "Teclado",
        "categoria": "Periféricos",
        "precio": 85000.0,
        "cantidad": 4
    },
    {
        "codigo": "P002",
        "nombre": "Mouse",
        "categoria": "Periféricos",
        "precio": 45000.0,
        "cantidad": 0
    }
]
~~~

Cierra el programa y vuelve a ejecutarlo. Los productos deben recuperarse con sus datos.

### 13. Construcción por etapas

**Etapa 1 — Copiar y verificar.** Conserva el Laboratorio 3 y ejecuta su gestor en la carpeta nueva antes de introducir cambios.

**Etapa 2 — Serialización.** Implementa `a_diccionario()` y `desde_diccionario()`. Comprueba que puedes reconstruir un objeto.

**Etapa 3 — Exportar estado.** Agrega a `Inventario` un método que obtenga los datos de todos los productos sin exponer la lista interna.

**Etapa 4 — Guardar.** Implementa `guardar_inventario()` y comprueba el contenido del JSON.

**Etapa 5 — Recuperar.** Implementa `cargar_inventario()` y reconstruye todos los objetos al iniciar.

**Etapa 6 — Errores de archivos.** Comprueba el archivo inexistente, el JSON mal formado y los registros inválidos. No permitas que estos casos destruyan los datos originales.

**Etapa 7 — Exportar CSV.** Incluye campos y cálculos del reporte. Ábrelo para comprobar sus columnas.

**Etapa 8 — Menú y validaciones.** Conecta las funciones al menú y controla entradas incorrectas sin interrumpir la sesión.

**Etapa 9 — Pruebas de reinicio.** Realiza cambios, guarda, termina el programa y vuelve a ejecutarlo para verificar que se conservaron.

### 14. Pistas para mantener las responsabilidades

`main.py` coordina:

~~~python
# Ejemplo de intención, no programa completo:
# inventario = cargar_inventario()
# opcion = leer_opcion()
# ejecutar_operacion(inventario, opcion)
# guardar_inventario(inventario)
~~~

La lógica de negocio permanece en `Inventario` y `Producto`.

`almacenamiento.py` no debe preguntar al usuario qué quiere hacer. `reportes.py` no debe cambiar existencias. `main.py` no debe modificar directamente `inventario._productos`.

No necesitas herencia para repartir estas responsabilidades: el inventario trabaja con productos y el programa utiliza funciones de almacenamiento y reportes.

### 15. Ejemplo de ejecución

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
8. Guardar inventario
9. Exportar reporte CSV
0. Salir

Opción: 1
Código: P001
Nombre: Teclado
Categoría: Periféricos
Precio: 85000
Cantidad: 4
Producto registrado.
Inventario guardado.

Opción: 1
Código: P002
Nombre: Mouse
Categoría: Periféricos
Precio: 45000
Cantidad: 0
Producto registrado.
Inventario guardado.

Opción: 7
Productos registrados: 2
Unidades disponibles: 4
Valor total: $340000.0

Opción: 9
Reporte exportado: reportes/inventario.csv

Opción: 0
Programa finalizado.
~~~

Después de cerrar la aplicación, vuelve a abrirla y consulta los productos.

### 16. Pruebas obligatorias

| Situación | Comportamiento esperado |
|---|---|
| Primera ejecución, JSON inexistente | Inventario vacío sin error |
| Guardar dos productos | Archivo JSON con ambos registros |
| Cerrar y reiniciar | Los dos productos se recuperan |
| Registrar código duplicado | Se rechaza y no duplica el JSON |
| Registrar categoría desconocida | Se rechaza |
| Registrar precio negativo | Se rechaza |
| Registrar `nan` o `inf` como precio | Se rechaza |
| Registrar cantidad negativa | Se rechaza |
| Registrar cantidad cero | Se acepta como producto sin existencias |
| Actualizar una cantidad | Se refleja tras reiniciar |
| Eliminar un producto | Sigue eliminado después de reiniciar |
| Introducir texto no numérico | Mensaje y nueva oportunidad |
| JSON con sintaxis incorrecta | Se informa sin sobrescribirlo |
| JSON con estructura inesperada | Se rechaza la carga y conserva el archivo |
| JSON con código repetido | No se acepta una carga parcial |
| Error de escritura | Se informa que no se confirmó el guardado |
| Exportar CSV | Archivo con encabezados y valores correctos |
| Consultar resumen | Totales correctos |
| Seleccionar 0 | Salida controlada |

Para las pruebas de archivos dañados trabaja **sobre copias de los datos**. No provoques daños sobre la única versión válida del inventario.

### 17. Prueba numérica

Registra:

| Código | Producto | Precio | Cantidad | Categoría |
|---|---|---:|---:|---|
| P001 | Teclado | 85000 | 4 | Periféricos |
| P002 | Mouse | 45000 | 0 | Periféricos |
| P003 | Monitor | 700000 | 2 | Componentes |

Resultados esperados:

~~~text
Productos: 3
Unidades: 6
Valor del inventario: $1740000.0
Sin existencias: Mouse
Categorías utilizadas: 2
~~~

Cierra el programa, vuelve a ejecutarlo y comprueba que aparecen los mismos resultados.

Después cambia la cantidad del Mouse a `5`. Guarda y reinicia:

~~~text
Productos: 3
Unidades: 11
Valor del inventario: $1965000.0
~~~

Exporta el CSV. La fila correspondiente a P002 deberá incluir un valor de inventario de `225000.0`.

### 18. Prueba de archivos dañados

Realiza este procedimiento con una copia del proyecto:

1. guarda un inventario válido;
2. haz otra copia de `productos.json`;
3. elimina una coma del JSON utilizado por la aplicación;
4. ejecuta nuevamente;
5. comprueba que se detecte el error;
6. confirma que el archivo dañado no fue sustituido por `[]`;
7. restaura la copia válida y reinicia;
8. confirma que los productos vuelvan a cargarse.

Registra los resultados de esta prueba para el informe de entrega.

### 19. Reto de ampliación

Si superaste las pruebas principales, puedes incorporar:

- copia de seguridad del JSON antes de sobrescribirlo;
- guardado mediante archivo temporal y reemplazo para reducir el riesgo de dejar un JSON incompleto;
- exportación de productos agotados a un CSV independiente;
- reporte de valor del inventario por categoría;
- filtrado por categoría;
- un objeto de almacenamiento que reciba la ruta en su constructor, si deseas practicar composición.

No necesitas usar herencia en el gestor por obligación. Las prácticas de la sección 4.4 permiten aprender el concepto sin introducir una jerarquía artificial en la aplicación.

### 20. Depuración

Crea una copia del proyecto y reproduce, uno por uno, estos problemas:

1. olvidar el parámetro `encoding="utf-8"` al guardar texto con caracteres especiales;
2. utilizar una ruta relativa que apunte a otra carpeta;
3. intentar guardar directamente un objeto `Producto` con `json.dump()`;
4. cargar un JSON con código repetido sin validar;
5. sobrescribir automáticamente un JSON dañado con una lista vacía;
6. exportar el CSV sumando precios en lugar de multiplicarlos por cantidades;
7. modificar los datos sin guardar y asumir que los cambios ya quedaron persistidos.

Corrige cada caso y explica mediante comentarios o notas de prueba qué cambiaste.

### 21. Revisión del proyecto

Antes de finalizar, comprueba:

- `Producto` conserva sus validaciones y permite obtener datos serializables;
- `Inventario` administra una colección propia de objetos `Producto`;
- la carga reconstruye objetos y valida todos los registros;
- un archivo dañado no se sobrescribe sin intervención;
- la ruta JSON se determina desde la ubicación del módulo;
- las operaciones válidas pueden guardarse;
- un fallo de escritura no se presenta como éxito;
- `reportes.py` produce CSV con encabezados y valores correctos;
- `main.py` concentra la interacción con el usuario;
- el inventario se recupera después de cerrar y volver a abrir;
- puedes explicar qué módulos conocen el sistema de archivos y cuáles no.

---

## Entrega individual y seguimiento en GitHub

Este laboratorio se entrega **de manera individual** mediante el repositorio personal de GitHub y un informe en PDF.

### 1. Repositorio acumulativo

Utiliza el **mismo repositorio personal** empleado desde el Laboratorio 1. Conserva los trabajos de los laboratorios anteriores, sus ejercicios y sus proyectos finales.

La estructura general puede seguir este ejemplo:

~~~text
mi-repositorio-python/
├── README.md
├── laboratorio-01/
├── laboratorio-02/
├── laboratorio-03/
└── laboratorio-04/
    ├── practicas/
    └── gestor_inventario/
~~~

No reemplaces las versiones anteriores por la solución más reciente. El repositorio debe permitir comparar los avances desde los fundamentos de Python hasta el inventario orientado a objetos con persistencia.

Actualiza el `README.md` principal para que enlace los cuatro laboratorios e indique cómo ejecutar el proyecto de cada etapa.

### 2. Commits con trazabilidad

Registra los avances mediante commits descriptivos a medida que desarrollas.

Los mensajes deben explicar el cambio realizado, por ejemplo:

~~~text
feat: validar productos al cargarlos desde JSON
feat: conservar inventario entre ejecuciones
fix: evitar sobrescribir JSON con formato incorrecto
feat: exportar inventario en CSV
docs: documentar pruebas de persistencia
~~~

Estos son ejemplos de mensajes, no commits que debas copiar. Cada commit debe corresponder con modificaciones reales.

La revisión del repositorio debe permitir distinguir qué se agregó, corrigió o probó. No prepares toda la aplicación en un único commit al finalizar si has realizado varias etapas de desarrollo.

### 3. Informe obligatorio en PDF

El informe debe contener:

1. **Identificación:** aprendiz, ficha y nombre del laboratorio.
2. **Repositorio:** enlace directo al repositorio personal en GitHub.
3. **Estado del proyecto:** funcionalidades terminadas, pendientes, dificultades y limitaciones.
4. **Seguimiento del desarrollo:** selección de commits reales, explicación de qué cambió en cada uno, motivo técnico y resultado obtenido.
5. **Evidencias de comprobación:** resultados de pruebas de guardado, recuperación, errores de JSON y exportación CSV.
6. **Comparación de avances:** explica cómo evolucionó el proyecto desde los laboratorios 01, 02 y 03 hasta esta nueva versión.
7. **Análisis situacional:** responde y justifica las cinco preguntas siguientes.

No basta con colocar capturas de pantalla o una lista de hashes. Relaciona cada commit seleccionado con una decisión, una corrección o una mejora que pueda comprobarse en el historial.

Puedes utilizar esta tabla en el informe:

| Commit | Cambio realizado | Razón del cambio | Comprobación |
|---|---|---|---|
| Hash real | Funcionalidad o corrección | Problema o necesidad | Prueba o resultado |

### 4. Cinco preguntas de análisis situacional

**Pregunta 1.** ¿Qué harías si el programa se cierra inesperadamente y, al volver a ejecutarlo, los productos registrados ya no aparecen?

Explica cómo comprobarías si los cambios fueron guardados, qué revisarías en las rutas y cómo demostrarías que la recuperación funciona.

**Pregunta 2.** ¿Qué harías si `productos.json` existe, pero tiene una coma faltante y el programa no puede leerlo?

Explica cómo identificarías el error, por qué no deberías reemplazar el archivo automáticamente por uno vacío y qué procedimiento seguirías para recuperar la aplicación.

**Pregunta 3.** ¿Qué harías si el inventario guarda correctamente un producto, pero al recuperar el JSON aparecen dos registros con el mismo código?

Explica dónde comprobarías la unicidad, cómo evitarías una carga parcial y cómo diseñarías una prueba para verificar la corrección.

**Pregunta 4.** ¿Qué harías si un reporte CSV muestra el precio unitario en la columna del valor total de inventario, aunque cada producto tenga varias unidades?

Explica qué cálculo revisarías, en qué módulo debería realizarse y cómo comprobarías los resultados con datos conocidos.

**Pregunta 5.** ¿Qué harías si, después de separar el programa en varias clases y módulos, una corrección al guardado provoca que el inventario deje de cargar datos?

Explica cómo utilizarías el historial de commits de GitHub para identificar el cambio, reproducir el problema, justificar la corrección y dejar evidencia de las pruebas realizadas.

En cada respuesta explica **qué harías, por qué, cómo lo implementarías y cómo comprobarías que funciona**. Relaciona tus respuestas con el código desarrollado y, cuando corresponda, con commits reales.

### 5. Entrega del laboratorio

Adjunta el informe PDF y proporciona el enlace al repositorio personal de GitHub.

La entrega debe corresponder al trabajo individual. El repositorio debe permitir revisar los ejercicios y proyectos de los cuatro laboratorios, así como el historial progresivo de desarrollo.

---

## Cierre del laboratorio

Al finalizar deberías poder manejar excepciones concretas, leer y escribir archivos, construir rutas con `pathlib`, guardar y recuperar datos JSON, exportar reportes CSV y reconocer diferencias entre herencia, polimorfismo y composición.

El gestor de inventario ahora conserva su información entre ejecuciones. Compara esta versión con la del Laboratorio 3 y explica qué responsabilidades nuevas aparecieron y por qué conviene mantenerlas en módulos diferentes.

### Referencias

- [Python — Errores y excepciones](https://docs.python.org/3/tutorial/errors.html)
- [Python — Entrada y salida](https://docs.python.org/3/tutorial/inputoutput.html)
- [Python — pathlib](https://docs.python.org/3/library/pathlib.html)
- [Python — json](https://docs.python.org/3/library/json.html)
- [Python — csv](https://docs.python.org/3/library/csv.html)
- [Python — Clases](https://docs.python.org/3/tutorial/classes.html)
