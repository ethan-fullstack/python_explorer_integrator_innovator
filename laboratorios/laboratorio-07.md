# Laboratorio 7 — Persistencia de datos con Django y MySQL o MariaDB

En el [Laboratorio 6](laboratorio-06.md) construiste **ClassroomHub**, un portal con rutas, vistas y plantillas. Los recursos aparecían porque estaban escritos como diccionarios en `views.py`. Ahora vas a convertir ese catálogo en una aplicación con datos persistentes: podrás crear recursos en el administrador, almacenarlos en **MySQL o MariaDB**, consultarlos desde el navegador y conservarlos después de reiniciar el servidor.

**No necesitas instalar ambos motores.** Escoge uno que sea compatible con tu versión de Django. Utilizaremos el mismo backend (`django.db.backends.mysql`), los mismos modelos y los mismos comandos en los dos casos.

| Práctica | Tema | Resultado |
|---|---|---|
| 7.1 | MySQL o MariaDB, conexión y configuración segura | Django se conecta a una base de desarrollo |
| 7.2 | Modelos, campos y relaciones | Defines `Category` y `Resource` |
| 7.3 | Migraciones y evolución del esquema | Creas tablas y agregas un campo sin borrar registros |
| 7.4 | ORM: altas, consultas, cambios, eliminaciones | Administras datos desde Python |
| 7.5 | Django Admin | Gestionas recursos desde una interfaz interna |
| 7.6 | Reto integrador | Conectas el catálogo público y ejecutas pruebas aisladas |

> Trabaja paso a paso: predice qué ocurrirá, ejecuta el ejemplo, modifica algo y comprueba el resultado. Si aparece un error, identifica su causa antes de reemplazar archivos o borrar bases de datos.

## Preparación y alcance

Continúa el proyecto `classroom-hub` del Laboratorio 6. Conserva `config/`, `pages/`, `manage.py`, las rutas y las tres plantillas. **No ejecutes de nuevo** `startproject` ni `startapp`.

Necesitas Python, Django, un entorno virtual y un servidor de **MySQL o MariaDB** instalado localmente o accesible mediante una configuración de desarrollo. También necesitarás permisos para crear una base de datos y una cuenta SQL local.

### Versiones compatibles

| Serie de Django | MySQL | MariaDB |
|---|---|---|
| **Django 6.1** | **8.4 o superior** | **10.11 o superior** |
| **Django 5.2 LTS** | **8.0.11 o superior** | **10.5 o superior** |

El laboratorio toma como referencia Django **6.1.3**; si continúas con **5.2 LTS** por compatibilidad de Python, conserva esa rama y consulta su documentación. Los mínimos de esta tabla reflejan compatibilidad de Django; usa una versión del servidor que reciba mantenimiento de seguridad. Comprueba la versión instalada con `python -m django --version` y la versión **del servidor** SQL mediante `SELECT VERSION();`.

La instalación del servicio cambia según el sistema operativo. No se incluyen comandos universales para instalar servidores o cambiar servicios porque podrían ser inapropiados para tu equipo. Consulta las instrucciones oficiales de MySQL o MariaDB para tu plataforma.

### Lo que aprenderás y lo que queda para después

Al finalizar existirán dos tablas propias, una relación uno a muchos, un panel de administración y una página pública que consulta el ORM. **No** implementarás todavía inicio de sesión propio, formularios públicos, CRUD desde vistas personalizadas ni API REST. Esas responsabilidades merecen laboratorios distintos.

---

## Práctica 7.1 — Preparar MySQL o MariaDB y conectar Django

### Objetivos

- Identificar servidor, base de datos, cuenta y controlador.
- Crear una base con codificación UTF-8.
- Conectar Django sin guardar contraseñas en Git.
- Distinguir base de desarrollo y base de pruebas.
- Diagnosticar fallos de conexión.

### 1. Comprender las piezas

~~~text
Django views
    |
    v
Django ORM
    |
    v
django.db.backends.mysql
    |
    v
mysqlclient
    |
    v
MySQL or MariaDB server
    |
    v
classroom_hub database
~~~

El **modelo** describe los datos, el **ORM** traduce operaciones a consultas, `mysqlclient` permite conectar desde Python y el **servidor** conserva físicamente la información. El controlador no reemplaza al servidor.

### 2. Comprobar el motor y su versión

Puedes utilizar el cliente `mysql` o `mariadb` que corresponda a tu instalación. Por ejemplo:

~~~bash
mysql -u root -p
~~~

En algunas instalaciones de MariaDB sobre Linux se utiliza `sudo mariadb` para administración local. No asumas que esa alternativa funciona en Windows. Si utilizas un servidor remoto, consulta sus parámetros de conexión.

Dentro de la sesión administrativa:

~~~sql
SELECT VERSION();
SELECT @@version_comment;
SHOW DATABASES;
~~~

**No confundas** `mysql --version` con `SELECT VERSION()`: el primero puede informar la versión del cliente; el segundo consulta el servidor.

### 3. Crear la base de desarrollo

Desde una cuenta que pueda administrar bases:

~~~sql
CREATE DATABASE IF NOT EXISTS classroom_hub
    CHARACTER SET utf8mb4
    COLLATE utf8mb4_unicode_ci;

SHOW CREATE DATABASE classroom_hub;
~~~

El juego de caracteres `utf8mb4` permite almacenar texto Unicode. La colación influye en comparaciones y ordenación. Todavía **no crees** tablas de categorías o recursos: las generará Django con migraciones.

### 4. Crear una cuenta para la aplicación

Ejecuta el SQL con privilegios administrativos. **Sustituye la contraseña demostrativa antes de ejecutarlo.**

~~~sql
CREATE USER IF NOT EXISTS 'classroomhub_user'@'127.0.0.1'
    IDENTIFIED BY 'replace_with_a_private_password';

GRANT ALL PRIVILEGES ON classroom_hub.*
    TO 'classroomhub_user'@'127.0.0.1';

GRANT ALL PRIVILEGES ON test_classroom_hub.*
    TO 'classroomhub_user'@'127.0.0.1';
~~~

Los permisos están limitados a **dos esquemas concretos**. El primero se usa para desarrollo; el segundo será descartable y permitirá al ejecutor de pruebas crear, migrar y eliminar su base. No concedas `ALL ON *.*` ni utilices `root` en la aplicación.

**Atención:** `CREATE USER IF NOT EXISTS` no cambia la contraseña si la cuenta ya existe. Si fue creada antes, revisa la cuenta y sus permisos de forma explícita. Usamos `127.0.0.1` porque conectaremos por TCP; una cuenta `'user'@'localhost'` no tiene necesariamente el mismo comportamiento que `'user'@'127.0.0.1'`.

Cierra la conexión administrativa y verifica la cuenta:

~~~bash
mysql -h 127.0.0.1 -P 3306 -u classroomhub_user -p classroom_hub
~~~

En el cliente:

~~~sql
SELECT DATABASE();
SELECT CURRENT_USER();
SHOW TABLES;
~~~

El resultado debe señalar `classroom_hub`. Que aún no aparezcan tablas propias es correcto.

### 5. Instalar el controlador

Activa el entorno virtual de ClassroomHub. Con una versión de Python compatible:

~~~bash
python -m django --version
python -m pip install "mysqlclient>=2.2.1,<3" "python-dotenv>=1,<2"
python -c "import MySQLdb; import dotenv; print('Database packages available')"
python -m pip freeze > requirements.txt
~~~

El paquete `mysqlclient` es el controlador recomendado por Django para estos motores. Si falla su instalación, revisa las instrucciones oficiales del paquete: en algunas plataformas necesitarás encabezados del cliente, compiladores o `pkg-config`. **No** cambies de backend ni instales paquetes globalmente como solución improvisada.

### 6. Configuración local sin contraseñas publicadas

En la raíz del proyecto crea `.env.example`:

~~~dotenv
DB_NAME=classroom_hub
DB_USER=classroomhub_user
DB_PASSWORD=replace_with_a_private_password
DB_HOST=127.0.0.1
DB_PORT=3306
~~~

Copia ese archivo como `.env` y escribe **allí** la contraseña verdadera. No publiques `.env`. Comprueba `.gitignore`:

~~~gitignore
.venv/
.env
__pycache__/
*.py[cod]
*.log
db.sqlite3
~~~

La plantilla `.env.example` sí se versiona porque solo contiene valores demostrativos. Si ya publicaste una contraseña real, debes **rotarla en el servidor** además de retirarla del repositorio; `.gitignore` no borra el historial.

### 7. Modificar `config/settings.py`

Mantén los demás ajustes existentes. Cerca de los imports, incorpora:

~~~python
import os
from pathlib import Path

from dotenv import load_dotenv
~~~

Después de la definición **existente** de `BASE_DIR`, añade:

~~~python
load_dotenv(BASE_DIR / ".env")
~~~

No declares `BASE_DIR` dos veces. Busca la configuración inicial `DATABASES` de SQLite y **reemplázala** por:

~~~python
DATABASES = {
    "default": {
        "ENGINE": "django.db.backends.mysql",
        "NAME": os.environ["DB_NAME"],
        "USER": os.environ["DB_USER"],
        "PASSWORD": os.environ["DB_PASSWORD"],
        "HOST": os.getenv("DB_HOST", "127.0.0.1"),
        "PORT": os.getenv("DB_PORT", "3306"),
        "OPTIONS": {
            "charset": "utf8mb4",
        },
        "TEST": {
            "NAME": "test_classroom_hub",
        },
    },
}
~~~

**Puntos clave:**

- `ENGINE` es idéntico para MySQL y MariaDB.
- Las claves obligatorias usan `os.environ[...]` para detectar configuración ausente.
- `TEST.NAME` identifica una **base separada** que Django puede crear y destruir durante las pruebas.
- Esta es una configuración de desarrollo local, no una receta de producción expuesta a Internet.

### 8. Verificar la conexión real

~~~bash
python manage.py check
python manage.py shell
~~~

En el shell:

~~~python
from django.db import connection

connection.ensure_connection()

with connection.cursor() as cursor:
    cursor.execute("SELECT VERSION(), DATABASE()")
    print(cursor.fetchone())
~~~

Debe aparecer la versión del motor y `classroom_hub`. Termina con `exit()`. **`manage.py check` por sí solo no demuestra que el servidor SQL acepte conexiones**: por eso ejecutamos una consulta real.

### Antes de ejecutar — Predice

1. ¿Qué ocurre si detienes MySQL/MariaDB y consultas datos?
2. ¿Por qué no configuramos `USER="root"`?
3. ¿En qué se diferencian `classroom_hub` y `test_classroom_hub`?
4. ¿Por qué no tiene sentido subir `.env` a Git?

### Laboratorio de errores

- **`No module named MySQLdb`**: comprueba que `mysqlclient` está instalado en el intérprete usado por `manage.py`.
- **`Access denied`**: revisa contraseña, usuario, host y permisos.
- **`Can't connect to MySQL server`**: revisa servicio, dirección TCP y puerto.
- **`Unknown database`**: confirma que el esquema existe y `DB_NAME` es correcto.
- **`KeyError: DB_PASSWORD`**: revisa las variables en `.env` y la llamada a `load_dotenv`.

### Comprobación

La cuenta de ClassroomHub debe conectar tanto desde el cliente SQL como desde Django; las credenciales reales deben existir solo en el entorno local.

---

## Práctica 7.2 — Modelos y relaciones

### Objetivos

Definir modelos, reconocer claves primarias y foráneas, comprender tipos de campo, establecer una relación uno a muchos y explicar por qué un modelo no crea por sí solo una tabla.

### 1. Diseñar antes de escribir código

Necesitamos separar categorías y recursos para no repetir el nombre de una categoría en cada registro:

~~~text
Category (one)
  id (PK)
  name
  description
      |
      v
Resource (many)
  id (PK)
  title
  description
  available
  category_id (FK)
  created_at
~~~

**PK** es la clave primaria de cada fila; **FK** referencia la categoría correspondiente. Cada recurso tendrá una categoría y una categoría podrá tener muchos recursos.

### 2. Crear `pages/models.py`

Sustituye el archivo por:

~~~python
from django.db import models


class Category(models.Model):
    name = models.CharField(max_length=80, unique=True)
    description = models.TextField(blank=True)

    class Meta:
        ordering = ["name"]
        verbose_name_plural = "categories"

    def __str__(self):
        return self.name


class Resource(models.Model):
    title = models.CharField(max_length=150)
    description = models.TextField(blank=True)
    available = models.BooleanField(default=True)
    category = models.ForeignKey(
        Category,
        on_delete=models.PROTECT,
        related_name="resources",
    )
    created_at = models.DateTimeField(auto_now_add=True)

    class Meta:
        ordering = ["title"]

    def __str__(self):
        return self.title
~~~

### 3. Leer los campos

| Campo | Explicación |
|---|---|
| `CharField(max_length=...)` | Texto corto con longitud máxima |
| `TextField(blank=True)` | Texto opcional en formularios |
| `BooleanField(default=True)` | Valor booleano con predeterminado |
| `DateTimeField(auto_now_add=True)` | Fecha de creación asignada por Django |
| `ForeignKey(Category, ...)` | Relación con una categoría |
| `unique=True` | Restricción de unicidad según la colación de la base |
| `ordering` | Orden predeterminado de consultas |
| `__str__` | Representación legible del objeto |

Django genera `id` automáticamente si no declaras una clave primaria. `blank=True` se relaciona con validación de formularios, mientras que `null=True` permite `NULL` en la base; no son equivalentes.

La política `on_delete=models.PROTECT` impide eliminar una categoría **mediante el ORM** cuando todavía tiene recursos. `related_name="resources"` permite consultar `category.resources.all()`.

### Antes de ejecutar — Predice

1. ¿Cuántas categorías necesitas para cinco recursos de `Programming`?
2. ¿Podría existir un recurso sin categoría con este modelo?
3. ¿Por qué permitimos descripciones vacías?
4. ¿Qué pasaría si dos categorías tienen el mismo nombre?

### Ejercicio — Modificar sin adelantar contenido

Cambia temporalmente el valor predeterminado de `available` y analiza qué ocurriría en un registro nuevo. Restablece `default=True` antes de continuar. No agregues todavía nuevos modelos.

### Comprobación

Los modelos están definidos, pero **las tablas aún no existen**. Ese es el objetivo de la práctica siguiente.

---

## Práctica 7.3 — Migraciones y esquema de MySQL/MariaDB

### Objetivos

Generar migraciones, inspeccionar su contenido, aplicarlas, consultar tablas desde el motor SQL y evolucionar el esquema sin borrar datos.

### 1. Separar las operaciones

| Comando | Para qué sirve |
|---|---|
| `makemigrations pages` | Genera un archivo de cambios |
| `showmigrations` | Muestra las migraciones aplicadas o pendientes |
| `migrate` | Aplica cambios sobre la base configurada |
| `sqlmigrate` | Presenta el SQL generado para una migración |
| `makemigrations --check --dry-run` | Detecta cambios aún sin migración |

**Una migración no es un registro de datos.** Es una descripción versionada de la evolución de la estructura.

### 2. Generar la migración inicial

~~~bash
python manage.py makemigrations pages
python manage.py showmigrations pages
python manage.py migrate --plan
~~~

Abre `pages/migrations/0001_initial.py` y localiza las operaciones `CreateModel`. Antes de aplicar la migración, la salida de `showmigrations` mostrará la inicial pendiente.

### 3. Aplicarla en MySQL/MariaDB

~~~bash
python manage.py migrate
python manage.py showmigrations pages
~~~

Debes observar la migración marcada con `[X]`. El mismo comando aplicará las migraciones necesarias de las aplicaciones integradas de Django, como autenticación y sesiones.

Conéctate mediante el cliente SQL y ejecuta:

~~~sql
USE classroom_hub;
SHOW TABLES;
DESCRIBE pages_category;
DESCRIBE pages_resource;
~~~

Comprueba la columna `category_id` en `pages_resource`. Inspecciona también el motor de las tablas:

~~~sql
SELECT TABLE_NAME, ENGINE
FROM information_schema.TABLES
WHERE TABLE_SCHEMA = 'classroom_hub'
  AND TABLE_NAME IN ('pages_category', 'pages_resource');
~~~

**InnoDB** es el motor recomendado para transacciones y claves foráneas. MyISAM no ofrece esas garantías.

### 4. Consultar el SQL

~~~bash
python manage.py sqlmigrate pages 0001
~~~

Localiza en la salida las tablas, columnas e índices de la relación. Compara el SQL con tus modelos; no es necesario memorizar la sintaxis interna del motor.

### 5. Evolucionar el modelo

Agrega **dentro de `Resource`**, sin eliminar los campos existentes:

~~~python
resource_type = models.CharField(max_length=40, blank=True)
~~~

Luego:

~~~bash
python manage.py makemigrations pages
python manage.py migrate
python manage.py makemigrations --check --dry-run
~~~

Se habrá creado una segunda migración (su nombre puede variar). El campo es opcional para no exigir un valor a registros ya existentes. **Conserva este campo** en los pasos siguientes, aunque todavía no lo mostremos en la página.

### Laboratorio de errores

- **La tabla no existe:** revisa `showmigrations`, el archivo de migración y la base configurada; no modifiques la consulta a ciegas.
- **Añadiste un campo, pero no lo migraste:** el modelo y el esquema pueden diferir. Compara `makemigrations` y `migrate`.
- **Quieres borrar toda la base para solucionar un error:** antes inspecciona el error, los archivos y el historial. Borrar tablas destruye datos y oculta la causa.

### Reto — Explica dos migraciones

Señala qué migración crea tablas, cuál agrega la columna `resource_type` y cómo lo comprobaste con `DESCRIBE`.

### Comprobación

La base contiene las tablas propias y las migraciones están aplicadas; los archivos generados se conservan para Git.

---

## Práctica 7.4 — ORM: crear, consultar y relacionar

### Objetivos

Crear registros, consultar QuerySets, actualizar datos persistentes, recorrer relaciones y tratar errores de consulta o integridad.

### 1. Abrir el shell

~~~bash
python manage.py shell
~~~

Dentro:

~~~python
from pages.models import Category, Resource
~~~

### 2. Crear categorías

~~~python
programming = Category.objects.create(
    name="Programming",
    description="Core programming topics",
)
collaboration = Category.objects.create(
    name="Collaboration",
    description="Version control and teamwork",
)
web_development = Category.objects.create(
    name="Web development",
    description="Web application topics",
)

print(Category.objects.count())
~~~

**Resultado esperado:** tres categorías si la base está vacía. No repitas las inserciones ciegamente: `name` es único y una segunda ejecución puede generar `IntegrityError`.

### 3. Crear recursos

~~~python
Resource.objects.create(
    title="Python basics",
    description="Variables, functions, and collections",
    available=True,
    category=programming,
)

Resource.objects.create(
    title="Git and GitHub",
    description="Version control fundamentals",
    available=True,
    category=collaboration,
)

Resource.objects.create(
    title="Django models",
    description="Data models and migrations",
    available=False,
    category=web_development,
)
~~~

El argumento `category` recibe un objeto relacionado; Django guarda internamente la referencia `category_id`.

### 4. Leer y filtrar

~~~python
Resource.objects.all()
Resource.objects.count()
Resource.objects.filter(available=True)
Resource.objects.filter(category=programming)
Resource.objects.filter(category__name="Programming")
Resource.objects.filter(title__icontains="django")
Resource.objects.order_by("-created_at")
~~~

`all()` y `filter()` producen **QuerySets**, no listas creadas manualmente. Los QuerySets se evalúan cuando Django necesita sus resultados. Las comparaciones de texto dependen también de las reglas de colación de MySQL/MariaDB.

### 5. Buscar uno solo

~~~python
resource = Resource.objects.get(title="Python basics")
print(resource.title, resource.category.name)
~~~

`get()` exige exactamente un registro. Si no existe, lanza `Resource.DoesNotExist`; si hay varios, `Resource.MultipleObjectsReturned`. Cuando no encontrar un elemento sea normal, compara:

~~~python
resource = Resource.objects.filter(title="Unknown").first()
print(resource)
~~~

Aquí obtendrás `None` en lugar de una excepción.

### 6. Cambiar un dato y comprobar persistencia

~~~python
resource = Resource.objects.get(title="Python basics")
resource.available = False
resource.save(update_fields=["available"])
~~~

Cierra el shell y vuelve a abrirlo. Consulta:

~~~python
from pages.models import Resource

resource = Resource.objects.get(title="Python basics")
print(resource.available)
~~~

Debe mostrar `False`. El valor procede de MySQL/MariaDB, no de una variable que haya sobrevivido al cierre de Python. Restáuralo:

~~~python
resource.available = True
resource.save(update_fields=["available"])
~~~

### 7. Eliminar únicamente un registro temporal

~~~python
from pages.models import Category, Resource

category = Category.objects.get(name="Programming")
temporary = Resource.objects.create(
    title="Temporary example",
    category=category,
)
temporary.delete()
print(Resource.objects.filter(title="Temporary example").exists())
~~~

El resultado esperado es `False`. No borres los registros principales del laboratorio.

### 8. Relaciones en ambos sentidos

~~~python
resource = Resource.objects.get(title="Git and GitHub")
print(resource.category.name)

category = Category.objects.get(name="Collaboration")
for item in category.resources.all():
    print(item.title)
~~~

`category.resources` existe gracias a `related_name="resources"`.

### 9. Proteger categorías referenciadas

~~~python
from django.db.models.deletion import ProtectedError
from pages.models import Category

category = Category.objects.get(name="Programming")

try:
    category.delete()
except ProtectedError:
    print("Category has related resources")
~~~

Las políticas de eliminación tienen consecuencias diferentes:

| Política | Comportamiento en Django |
|---|---|
| `PROTECT` | Impide eliminar el objeto referenciado |
| `CASCADE` | Elimina objetos dependientes al eliminar el relacionado |
| `SET_NULL` | Deja la relación en `NULL` cuando el campo lo permite |

**No cambies `PROTECT` para evitar el error**. Es una regla deliberada para evitar la pérdida accidental de relaciones.

### 10. Consultas relacionadas

Si recorres muchos recursos y accedes a `resource.category.name`, podrías disparar consultas adicionales. Usa:

~~~python
for resource in Resource.objects.select_related("category"):
    print(resource.title, resource.category.name)
~~~

`select_related()` obtiene anticipadamente relaciones de clave foránea. Es suficiente conocer este caso; la optimización avanzada del ORM se tratará después.

### Ejercicios

1. Recupera los recursos disponibles.
2. Cuenta los no disponibles.
3. Busca recursos cuyo título contenga `Python`.
4. Muestra todos los recursos de `Programming`.
5. Prueba `get()` con un título inexistente y compara con `first()`.
6. Cambia un estado, comprueba que se guardó y restáuralo.

### Depuración

Provoca y explica **sin dejar datos dañados**: una categoría duplicada, una búsqueda `get()` sin resultados, un cambio de atributo sin `save()` y un intento de borrar una categoría protegida.

### Comprobación

Puedes explicar cómo un modelo se convierte en registro y cómo consultas, actualizaciones y relaciones persisten en la base.

---

## Práctica 7.5 — Administrador integrado de Django

### Objetivos

Crear un superusuario local, registrar modelos, configurar columnas, búsquedas y filtros, y distinguir el administrador del sitio público.

### 1. Preparar el administrador

Primero confirma que las migraciones integradas están aplicadas:

~~~bash
python manage.py migrate
python manage.py createsuperuser
~~~

El superusuario es una **cuenta de Django**, no el usuario SQL `classroomhub_user`. Sus credenciales deben permanecer privadas. En este laboratorio utilizarás el administrador para gestionar datos; la autenticación de usuarios ordinarios se abordará por separado.

### 2. Configurar `pages/admin.py`

~~~python
from django.contrib import admin

from .models import Category, Resource


@admin.register(Category)
class CategoryAdmin(admin.ModelAdmin):
    list_display = ("name", "description")
    search_fields = ("name",)


@admin.register(Resource)
class ResourceAdmin(admin.ModelAdmin):
    list_display = ("title", "category", "available", "created_at")
    list_filter = ("available", "category")
    search_fields = ("title", "description", "category__name")
    list_select_related = ("category",)
    list_per_page = 20
~~~

`list_display` define columnas; `list_filter`, filtros; `search_fields`, búsqueda; `list_select_related`, recuperación eficiente de categorías; y `list_per_page`, paginación.

### 3. Utilizar la interfaz

~~~bash
python manage.py runserver
~~~

Abre `http://127.0.0.1:8000/admin/`. Inicia sesión y realiza estas comprobaciones:

1. Encuentra `Categories` y `Resources`.
2. Crea una categoría.
3. Crea un recurso en esa categoría.
4. Cambia su disponibilidad.
5. Busca el recurso por nombre.
6. Filtra por categoría o disponibilidad.
7. Comprueba los mismos datos mediante el shell.

Prueba además eliminar una categoría que tenga recursos asociados. La protección debe impedir una eliminación incorrecta; no necesitas modificar la política de borrado.

### Reto — Administración sin duplicación

Crea un recurso desde Admin y verifica su existencia en el shell mediante `Resource.objects.filter(...).exists()`. Explica por qué no tuviste que modificar la vista para añadirlo.

### Comprobación

Puedes administrar los dos modelos desde `/admin/`, y los cambios permanecen al reiniciar la aplicación.

---

## Práctica 7.6 — Reto integrador: catálogo persistente

### Objetivo

Eliminar la lista fija del Laboratorio 6 y hacer que `/resources/` consulte MySQL/MariaDB mediante el ORM, sin perder la navegación, las plantillas compartidas ni las pruebas automatizadas.

### 1. Requisitos

| Código | Comprobación |
|---|---|
| RF01 | Django conecta a MySQL o MariaDB con un usuario limitado |
| RF02 | Las tablas se generan y evolucionan mediante migraciones |
| RF03 | Cada recurso pertenece a una categoría |
| RF04 | Las categorías no pueden borrarse mientras estén referenciadas |
| RF05 | Django Admin permite administrar recursos y categorías |
| RF06 | `/resources/` muestra registros obtenidos mediante el ORM |
| RF07 | La plantilla presenta la categoría y disponibilidad |
| RF08 | Los registros permanecen después de reiniciar |
| RF09 | El catálogo vacío muestra un mensaje comprensible |
| RF10 | Las pruebas trabajan en una base separada |

### 2. Código final de `pages/views.py`

Conservamos las páginas del Laboratorio 6 y sustituimos únicamente el origen de los recursos:

~~~python
from django.shortcuts import render

from .models import Resource


def home(request):
    context = {
        "welcome_message": "Explore learning resources and project ideas.",
    }
    return render(request, "pages/home.html", context)


def resources(request):
    context = {
        "resources": Resource.objects.select_related("category").all(),
    }
    return render(request, "pages/resources.html", context)


def about(request):
    context = {
        "purpose": "Practice URL routing, views, and templates.",
    }
    return render(request, "pages/about.html", context)
~~~

La expresión `Resource.objects.select_related("category").all()` reemplaza la lista de diccionarios del laboratorio anterior. La base de datos es ahora la fuente de información.

### 3. Actualizar `pages/templates/pages/resources.html`

~~~html
{% extends "pages/base.html" %}

{% block title %}Resources | ClassroomHub{% endblock %}

{% block content %}
    <h2>Learning resources</h2>
    <p>Browse the available learning materials.</p>

    {% for resource in resources %}
        <article class="resource">
            <h3>{{ resource.title }}</h3>
            <p>{{ resource.description }}</p>
            <p>Category: {{ resource.category.name }}</p>

            {% if resource.available %}
                <p>Available</p>
            {% else %}
                <p>Coming soon</p>
            {% endif %}
        </article>
    {% empty %}
        <p>No resources are available.</p>
    {% endfor %}
{% endblock %}
~~~

No necesitas cambiar `base.html`, `home.html`, `about.html` ni las rutas. Esto permite comprobar cómo la separación de responsabilidades evita rehacer una aplicación completa al cambiar su almacenamiento.

### 4. Recorrido manual

1. Ejecuta `python manage.py check` y `python manage.py runserver`.
2. Abre `/resources/` y confirma que muestra los registros existentes.
3. Crea un recurso nuevo desde `/admin/`.
4. Recarga `/resources/` y observa que el recurso aparece.
5. Cambia `available` desde Admin y comprueba el nuevo texto.
6. Detén y reinicia el servidor de desarrollo: el recurso debe permanecer.
7. Verifica que `/about/`, `/` y los enlaces del menú siguen funcionando.
8. Visita una ruta inexistente y comprueba el resultado HTTP `404`.

### 5. Adaptar las pruebas del Laboratorio 6

En el laboratorio anterior usaste `SimpleTestCase` porque las vistas no consultaban la base. Ahora debes utilizar **`TestCase`**, que permite trabajar con una base temporal y registros creados por la propia prueba.

Sustituye completamente `pages/tests.py` por:

~~~python
from django.db.models.deletion import ProtectedError
from django.test import TestCase
from django.urls import reverse

from .models import Category, Resource


class ClassroomHubTests(TestCase):
    @classmethod
    def setUpTestData(cls):
        cls.category = Category.objects.create(
            name="Programming",
            description="Core programming topics",
        )
        cls.resource = Resource.objects.create(
            title="Python basics",
            description="Variables and functions",
            available=True,
            category=cls.category,
        )
        Resource.objects.create(
            title="Django models",
            description="Database fundamentals",
            available=False,
            category=cls.category,
        )

    def test_home_page_renders(self):
        response = self.client.get(reverse("pages:home"))

        self.assertEqual(response.status_code, 200)
        self.assertTemplateUsed(response, "pages/home.html")
        self.assertContains(response, "Welcome")

    def test_resources_page_displays_database_records(self):
        response = self.client.get(reverse("pages:resources"))

        self.assertEqual(response.status_code, 200)
        self.assertTemplateUsed(response, "pages/resources.html")
        self.assertContains(response, "Python basics")
        self.assertContains(response, "Django models")
        self.assertContains(response, "Coming soon")

    def test_resource_displays_related_category(self):
        response = self.client.get(reverse("pages:resources"))

        self.assertContains(response, "Category: Programming")

    def test_about_page_renders(self):
        response = self.client.get(reverse("pages:about"))

        self.assertEqual(response.status_code, 200)
        self.assertTemplateUsed(response, "pages/about.html")

    def test_home_navigation_links_to_resources(self):
        response = self.client.get(reverse("pages:home"))

        self.assertContains(
            response,
            f'href="{reverse("pages:resources")}"',
        )

    def test_unknown_page_returns_not_found(self):
        response = self.client.get("/not-found/")

        self.assertEqual(response.status_code, 404)

    def test_resource_updates_are_persisted(self):
        self.resource.available = False
        self.resource.save(update_fields=["available"])
        self.resource.refresh_from_db()

        self.assertFalse(self.resource.available)

    def test_category_protects_related_resources(self):
        with self.assertRaises(ProtectedError):
            self.category.delete()

        self.assertTrue(
            Category.objects.filter(pk=self.category.pk).exists()
        )

    def test_empty_catalog_shows_message(self):
        Resource.objects.all().delete()

        response = self.client.get(reverse("pages:resources"))

        self.assertContains(response, "No resources are available.")

    def test_category_reverse_relation_contains_resources(self):
        titles = set(
            self.category.resources.values_list("title", flat=True)
        )

        self.assertEqual(titles, {"Python basics", "Django models"})
~~~

Las diez pruebas cubren: navegación, contenido consultado desde la base, relación con categoría, ruta inexistente, persistencia tras `save()`, protección frente a borrado, catálogo vacío y relación inversa.

### 6. Base separada de pruebas: cuidado con los datos

La configuración `TEST.NAME="test_classroom_hub"` de la práctica 7.1 evita que el ejecutor de pruebas apunte a la base de desarrollo. Django **crea y elimina** la base de pruebas de acuerdo con su configuración; por eso debe contener únicamente datos descartables.

Ejecuta:

~~~bash
python manage.py test pages
~~~

**Resultado esperado:** diez pruebas terminan con `OK`. No uses una base que contenga información importante como nombre de prueba; tampoco concedas permisos globales como atajo.

Si aparece un error `Access denied` al intentar crear `test_classroom_hub`, revisa los permisos SQL concedidos al usuario de aplicación sobre **ese esquema específico**. No cambies la configuración para que las pruebas usen `classroom_hub`.

Comprueba luego que los registros de desarrollo siguen presentes: los datos que crean las pruebas deben estar aislados.

### 7. Depuración guiada

**Caso A — Django conecta, pero falta `pages_resource`.** Revisa `showmigrations pages`, `migrate` y la base usada por `DATABASES`. No crees manualmente una tabla parcial.

**Caso B — Admin muestra recursos, pero la página no.** Comprueba la vista `resources`, su QuerySet, la plantilla y la conexión. Evita duplicar registros fijos para ocultar el error.

**Caso C — La categoría aparece vacía.** Revisa la clave foránea `category`, la migración y el acceso `resource.category.name` en la plantilla.

**Caso D — `IntegrityError` por un nombre duplicado.** Examina la restricción `unique=True` y la colación de la base; no elimines restricciones útiles para hacer funcionar una inserción incorrecta.

**Caso E — Las pruebas no pueden crear la base.** Verifica `test_classroom_hub`, el usuario SQL y los permisos específicos. Nunca soluciones el fallo con `GRANT ALL ON *.*`.

**Caso F — El proyecto funciona en otra máquina, pero no en la tuya.** Compara versiones de Python/Django, controlador `mysqlclient`, servicio SQL, `.env` y migraciones.

### 8. Reto adicional — Consulta de disponibilidad

Crea una ruta pública de **solo lectura** `/resources/available/` que consulte `Resource.objects.filter(available=True)` y reutilice una plantilla existente. No introduzcas formularios, edición ni eliminación pública.

Escribe una prueba que cree recursos disponibles y no disponibles y verifique que solo aparecen los primeros. Recuerda que la ruta `resources/available/` debe colocarse antes de rutas parametrizadas que pudieran capturarla, si agregaste alguna.

### 9. Preguntas para explicar tu solución

1. ¿Qué diferencia existe entre servidor, esquema, cuenta SQL y modelo Django?
2. ¿Por qué MariaDB utiliza el backend `django.db.backends.mysql`?
3. ¿Para qué sirven `.env` y `.env.example`?
4. ¿Qué hacen `makemigrations` y `migrate`, y por qué no son intercambiables?
5. ¿Qué guarda la columna `category_id`?
6. ¿Qué diferencia hay entre `all()`, `filter()` y `get()`?
7. ¿Por qué `save()` es necesario para guardar cambios de una instancia modificada?
8. ¿Qué protege `on_delete=models.PROTECT`?
9. ¿Qué hace `select_related("category")`?
10. ¿Qué distingue Django Admin de una interfaz pública?
11. ¿Por qué las pruebas usan `TestCase` y una base distinta?
12. ¿Qué deberías agregar para permitir que una persona cree recursos desde la propia página web?

### 10. Comprobación final

- [ ] El motor SQL cumple los mínimos de tu versión de Django.
- [ ] La base `classroom_hub` y el usuario de aplicación están configurados.
- [ ] La conexión utiliza `django.db.backends.mysql` y `mysqlclient`.
- [ ] `.env` no aparece en Git y `.env.example` no contiene secretos reales.
- [ ] Las tablas y migraciones se crearon desde Django.
- [ ] `Category` y `Resource` tienen una relación válida.
- [ ] El ORM permite consultar, guardar y eliminar registros de prueba.
- [ ] La protección de categorías funciona.
- [ ] Django Admin permite mantener el catálogo.
- [ ] `/resources/` consulta la base y presenta la categoría.
- [ ] Los cambios se conservan después de reiniciar Django.
- [ ] Las diez pruebas automatizadas están definidas.
- [ ] Las pruebas utilizan la base descartable `test_classroom_hub`.
- [ ] El README explica cómo reconstruir el proyecto.

### 11. Compartir el proyecto

Actualiza el `README.md` de ClassroomHub con requisitos, conexión, creación de base, instalación de dependencias, configuración de `.env`, migraciones, creación del superusuario, arranque y pruebas. Versiona los **archivos de migración**, no el contenido real de la base ni las contraseñas.

Puedes organizar los cambios mediante commits como:

~~~text
feat: configure MySQL or MariaDB database
feat: define category and resource models
feat: create migrations and admin configuration
feat: display persisted resources
test: verify models and database views
docs: explain database setup
~~~

Los commits deben corresponder a trabajo realizado; no reconstruyas artificialmente el historial. No se requiere exportar una base con datos personales ni entregar capturas de contraseñas.

---

## Cierre del laboratorio

ClassroomHub ahora representa recursos mediante modelos Django, crea y evoluciona sus tablas con migraciones, guarda información en MySQL o MariaDB y recupera registros desde el ORM. El administrador integrado permite mantener el catálogo sin editar las listas de Python.

El siguiente paso será comprender **autenticación, sesiones, usuarios, grupos y permisos**. Después podrás construir formularios y un CRUD público sobre los mismos modelos, sin rehacer la capa de persistencia.

### Referencias oficiales

- [Django 6.1 — Bases de datos, MariaDB y MySQL](https://docs.djangoproject.com/en/6.1/ref/databases/)
- [Django 5.2 — Bases de datos compatibles](https://docs.djangoproject.com/en/5.2/ref/databases/)
- [Django 6.1 — Modelos](https://docs.djangoproject.com/en/6.1/topics/db/models/)
- [Django 6.1 — Migraciones](https://docs.djangoproject.com/en/6.1/topics/migrations/)
- [Django 6.1 — Consultas y relaciones](https://docs.djangoproject.com/en/6.1/topics/db/queries/)
- [Django 6.1 — Django Admin](https://docs.djangoproject.com/en/6.1/ref/contrib/admin/)
- [Django 6.1 — Pruebas](https://docs.djangoproject.com/en/6.1/topics/testing/overview/)
- [mysqlclient — Instalación y requisitos](https://pypi.org/project/mysqlclient/)
- [python-dotenv — Documentación](https://pypi.org/project/python-dotenv/)
