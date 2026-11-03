# Laboratorio 6 — Fundamentos de Django y primera aplicación web

En el [Laboratorio 5](laboratorio-05.md) analizaste cómo un equipo organiza el desarrollo con Scrum y GitHub, desde la necesidad hasta un incremento verificable. En los laboratorios anteriores construiste aplicaciones de consola con Python, funciones, módulos y clases.

Ahora vas a dar el primer paso hacia una **aplicación web**. Construirás **ClassroomHub**, un pequeño portal de recursos de aprendizaje con tres páginas navegables. Aprenderás qué hace Django, cómo se organiza un proyecto, qué ocurre cuando el navegador solicita una página y cómo utilizar plantillas HTML sin repetir toda la estructura del sitio.

**Este laboratorio es la primera parte de una secuencia sobre Django.** No construirás todavía una base de datos propia, formularios, inicio de sesión ni un CRUD. Esos temas corresponden a los laboratorios siguientes. El propósito es comprender y comprobar la relación entre **URL → vista → plantilla → respuesta** antes de agregar más responsabilidades.

| Práctica | Tema | Resultado |
|---|---|---|
| 6.1 | Introducción al desarrollo web y arquitectura MTV | Explicas cómo llega una petición hasta una página |
| 6.2 | Entorno virtual e instalación de Django | Ejecutas Django desde un entorno aislado |
| 6.3 | Primer proyecto y primera aplicación | Identificas los archivos y ejecutas el servidor |
| 6.4 | URLs y primeras vistas | Publicas rutas que responden a diferentes direcciones |
| 6.5 | Plantillas HTML y contexto | Renderizas páginas con una estructura compartida |
| 6.6 | Reto integrador: ClassroomHub | Entregas un sitio navegable y verificable en GitHub |

> **Cómo trabajar.** Lee cada explicación, predice el resultado, ejecuta el ejemplo, modifica algo y comprueba el cambio. Los fragmentos de código que se sustituyen durante el laboratorio están identificados. En los retos, intenta resolver primero el problema antes de revisar las pistas.

## Alcance y preparación

Este laboratorio corresponde al bloque introductorio previsto en el cronograma de la ficha 3388087: **29 de octubre (2,5 horas de Django)** y **3 de noviembre (3 horas de Django)**. Estas horas son de acompañamiento técnico de Django dentro de sesiones que también incluyen otros componentes formativos. El trabajo autónomo sirve para practicar o completar pasos pendientes.

Usaremos **Python 3.12 o superior y Django 6.1.2** como referencia común. Si el equipo dispone únicamente de Python 3.10 o 3.11, debe acordar con el instructor el uso de **Django 5.2 LTS** y consultar la documentación de esa versión. No mezcles instrucciones ni versiones dentro del mismo proyecto. Antes de instalar, verifica las versiones realmente disponibles en el ambiente.

La aplicación de práctica se desarrollará en un repositorio personal y no necesita reemplazar el proyecto productivo que cada equipo desarrolla para su formación. El cronograma contempla Django como framework enseñado de manera presencial; Flask es una alternativa de aprendizaje autónomo.

### ¿Qué vas a construir?

ClassroomHub será un sitio pequeño con:

- una página principal en `/`;
- una lista de recursos en `/resources/`;
- una página informativa en `/about/`;
- navegación mediante enlaces con nombres de rutas;
- una plantilla HTML base compartida;
- contenido de ejemplo enviado desde vistas de Django;
- verificaciones manuales y una prueba automatizada sencilla.

Al terminar, podrás explicar qué archivo interviene cuando abres cada página.

---

## Práctica 6.1 — Del programa de consola a una aplicación web

### Objetivos

Al finalizar esta práctica podrás:

- diferenciar un programa de consola de una aplicación web;
- reconocer el papel del navegador, el servidor, HTTP y las URLs;
- explicar la organización MTV de Django;
- distinguir un proyecto Django de una aplicación Django;
- anticipar qué ocurre si una ruta no existe.

### 1. ¿Qué cambia al utilizar un navegador?

Hasta ahora ejecutabas programas como este:

~~~python
print("Welcome to ClassroomHub")
~~~

La salida aparece en la terminal. El programa recibe entradas, las procesa y muestra resultados en el mismo entorno donde se ejecuta.

En una aplicación web intervienen dos componentes:

1. **Cliente:** normalmente un navegador que solicita un recurso.
2. **Servidor:** una aplicación que recibe la solicitud, decide cómo responder y devuelve información.

El navegador y el servidor pueden estar en el mismo computador durante el desarrollo; eso no significa que sean el mismo programa.

Una interacción habitual es:

~~~text
Browser
  |
  | GET /resources/
  v
Django server
  |
  | Find the matching URL
  v
View function
  |
  | Prepare data and render a template
  v
HTTP response (HTML)
  |
  v
Browser displays the page
~~~

**HTTP** es el protocolo empleado para intercambiar solicitudes y respuestas. Una solicitud `GET` suele utilizarse para consultar un recurso; la respuesta contiene un código de estado y, según el caso, un cuerpo como HTML.

| Situación | Respuesta esperada |
|---|---|
| La dirección corresponde a una página existente | `200 OK` |
| La dirección no coincide con ninguna ruta | `404 Not Found` |
| Ocurre una excepción no controlada en el servidor | `500 Internal Server Error` |

Estos códigos no sustituyen la lectura del error: indican una categoría de resultado.

### 2. ¿Qué aporta un framework?

Podrías crear un servidor HTTP utilizando herramientas más básicas, pero tendrías que construir muchas piezas por tu cuenta. **Django** ofrece una estructura y componentes para organizar proyectos web: resolución de URLs, vistas, plantillas, modelos, autenticación, administrador y otras herramientas.

No necesitas aprender todas esas herramientas al mismo tiempo. En este laboratorio utilizarás las primeras tres: rutas, vistas y plantillas.

### 3. Comprender MTV sin memorizar siglas

Django suele describirse mediante **Model–Template–View (MTV)**:

| Pieza | Responsabilidad | En ClassroomHub |
|---|---|---|
| **Model** | Representar y gestionar datos de la aplicación | Se estudiará en el Laboratorio 7 |
| **Template** | Definir la presentación que recibirá el navegador | Archivos HTML en `templates/` |
| **View** | Recibir una solicitud y producir una respuesta, utilizando datos y plantillas cuando corresponda | Funciones en `pages/views.py` |

La **configuración de URLs** identifica qué vista debe atender cada dirección. No es una cuarta letra del patrón MTV, pero resulta indispensable para comprender el recorrido de una solicitud.

El nombre *view* puede confundirte: en Django, una vista no es el archivo HTML. La **vista procesa la solicitud**; la **plantilla organiza la presentación**.

### 4. Proyecto y aplicación no significan lo mismo

- Un **proyecto Django** reúne la configuración general: aplicaciones instaladas, rutas principales y otros parámetros.
- Una **aplicación Django** agrupa una responsabilidad funcional reutilizable. Un proyecto puede contener una o varias aplicaciones.

En nuestro caso:

~~~text
ClassroomHub project
├── config/       # Project configuration
└── pages/        # Public pages application
~~~

Más adelante podrían existir otras aplicaciones, pero no hay razón para crearlas mientras no aporten responsabilidades diferentes.

### Antes de ejecutar — Predice

Responde antes de avanzar:

1. Si visitas `/resources/`, ¿qué componente decide a qué función llegar?
2. Si una vista envía una lista de recursos, ¿qué componente construye el HTML visible?
3. Si visitas `/unknown/` y no existe una ruta, ¿esperas un error de Python o una respuesta HTTP `404`?
4. ¿Necesitas crear un modelo para mostrar tres páginas con contenido fijo? Justifica.

### Ejercicio — Dibuja la solicitud

En una hoja o en una nota breve, dibuja este recorrido y explica cada paso con tus propias palabras:

~~~text
GET /about/ -> URL configuration -> view -> template -> HTTP response
~~~

**Comprobación.** Debes poder explicar la diferencia entre `urls.py`, `views.py` y un archivo de plantilla sin usar expresiones como «todos muestran la página».

---

## Práctica 6.2 — Entorno virtual e instalación de Django

### Objetivos

Al finalizar esta práctica podrás:

- verificar la versión de Python;
- crear y utilizar un entorno virtual;
- instalar y comprobar la versión de Django;
- registrar dependencias y excluir archivos locales del repositorio;
- reconocer problemas de instalación relacionados con el intérprete.

### 1. Verificar el entorno

Abre Visual Studio Code y una terminal. Ejecuta:

~~~bash
python --version
~~~

En algunos equipos Windows el comando disponible es:

~~~powershell
py --version
~~~

Para la instalación principal del laboratorio, el intérprete debe ser Python 3.12 o posterior. Si el equipo no cumple esa condición, no intentes forzar la instalación de Django 6.1.2: consulta la alternativa compatible descrita al inicio.

Comprueba también:

~~~bash
python -m pip --version
git --version
~~~

El comando `python -m pip` ayuda a utilizar `pip` asociado al intérprete de Python que acabas de ejecutar.

### 2. Crear un directorio de trabajo

Desde una carpeta donde guardas tus proyectos:

~~~bash
mkdir classroom-hub
cd classroom-hub
~~~

No crees un directorio llamado `django` ni `test` como nombre de proyecto: puede entrar en conflicto con módulos de Python o Django.

### 3. Crear el entorno virtual

~~~bash
python -m venv .venv
~~~

El directorio `.venv` contiene el entorno aislado. No es necesario subirlo a GitHub; cada persona puede reconstruirlo a partir de las dependencias.

**Activación en Windows PowerShell:**

~~~powershell
.\.venv\Scripts\Activate.ps1
~~~

**Activación en Linux/macOS:**

~~~bash
source .venv/bin/activate
~~~

En Windows, si PowerShell impide ejecutar el script de activación, no cambies globalmente la política de seguridad por costumbre. Puedes seleccionar el intérprete del entorno en VS Code o usar directamente `.venv\Scripts\python.exe` en los comandos posteriores.

En VS Code, utiliza **Python: Select Interpreter** y elige el Python ubicado dentro de `.venv`.

Verifica el intérprete activo:

~~~bash
python -c "import sys; print(sys.executable)"
~~~

**Resultado esperado:** la ruta corresponde al entorno `.venv`. Si muestra otro Python, corrige la selección antes de instalar paquetes.

### 4. Instalar una versión concreta de Django

Con el entorno activo:

~~~bash
python -m pip install Django==6.1.2
python -m django --version
~~~

El segundo comando debe mostrar:

~~~text
6.1.2
~~~

> **Alternativa por compatibilidad.** Con Python 3.10 o 3.11, acuerda el uso de la última actualización disponible de la serie Django 5.2 LTS. Consulta [la tabla oficial de compatibilidad](https://docs.djangoproject.com/en/6.1/faq/install/) y trabaja con [la documentación de Django 5.2](https://docs.djangoproject.com/en/5.2/). El resto de este laboratorio utiliza características comunes a ambas series, pero los ejemplos se validan tomando 6.1.2 como referencia.

### 5. Registrar dependencias

Ejecuta:

~~~bash
python -m pip freeze > requirements.txt
~~~

Abre el archivo y comprueba que contiene Django y las dependencias instaladas en el entorno. El archivo permite reinstalarlas en otra máquina:

~~~bash
python -m pip install -r requirements.txt
~~~

Este último comando es una **comprobación de reproducibilidad**; no necesitas instalar todo nuevamente en el mismo entorno.

### 6. Evitar archivos locales en GitHub

Crea `.gitignore` en la raíz del proyecto:

~~~gitignore
.venv/
__pycache__/
*.py[cod]
*.log
.env
db.sqlite3
~~~

En este laboratorio no usaremos la base de datos de la aplicación. El archivo `db.sqlite3` puede aparecer cuando Django utiliza SQLite en prácticas posteriores; no se debe subir por accidente si contiene información local.

Crea también un `README.md` breve que indique qué es ClassroomHub y cómo instalarlo. Lo ampliarás en la práctica integradora.

### Laboratorio de errores

**Error 1 — `No module named django`.** Comprueba si ejecutas el Python del entorno virtual correcto y si instalaste Django allí.

**Error 2 — `python` no se reconoce.** Comprueba la instalación y la configuración de Python. En Windows prueba `py`; en VS Code selecciona explícitamente el intérprete.

**Error 3 — Fallo de permisos durante la activación.** Usa el intérprete dentro de `.venv` sin activar o solicita orientación sobre la política del equipo. No instales dependencias globalmente solo para omitir el problema.

### Ejercicio — Reproducir el entorno

Explica en una nota:

1. Para qué sirve `.venv`.
2. Por qué `requirements.txt` sí debe versionarse.
3. Por qué `.venv/` no debe formar parte del repositorio.
4. Qué revisarías si dos terminales muestran versiones de Django diferentes.

### Comprobación

Puedes continuar cuando:

- `python --version` cumple los requisitos de la versión elegida;
- `python -m django --version` responde sin errores;
- el proyecto tiene `requirements.txt` y `.gitignore`;
- sabes identificar cuál intérprete utiliza VS Code.

---

## Práctica 6.3 — Crear el proyecto y conocer sus archivos

### Objetivos

Al finalizar esta práctica podrás:

- utilizar `startproject` y `startapp`;
- identificar los archivos principales de Django;
- ejecutar el servidor de desarrollo;
- registrar una aplicación en `INSTALLED_APPS`;
- diferenciar la configuración general de las funcionalidades de una app.

### 1. Crear el proyecto en el directorio actual

Desde `classroom-hub/`, con el entorno listo, ejecuta:

~~~bash
python -m django startproject config .
~~~

El punto final es importante: indica que el proyecto se crea **en la carpeta actual**, sin agregar otra carpeta exterior.

Django crea una estructura similar a:

~~~text
classroom-hub/
├── .venv/
├── .gitignore
├── requirements.txt
├── README.md
├── manage.py
└── config/
    ├── __init__.py
    ├── settings.py
    ├── urls.py
    ├── asgi.py
    └── wsgi.py
~~~

No necesitas memorizar todos los archivos. Concéntrate en sus responsabilidades:

| Archivo | Para qué sirve |
|---|---|
| `manage.py` | Ejecuta comandos administrativos del proyecto |
| `config/settings.py` | Guarda configuraciones del proyecto |
| `config/urls.py` | Define las rutas generales |
| `config/asgi.py` y `config/wsgi.py` | Puntos de entrada para servidores compatibles; no los modificaremos aquí |
| `config/__init__.py` | Identifica el directorio como paquete Python |

### 2. Verificar la configuración

Ejecuta:

~~~bash
python manage.py check
~~~

Una comprobación correcta debe terminar con un mensaje equivalente a:

~~~text
System check identified no issues (0 silenced).
~~~

Este comando revisa la configuración; no significa que ya hayas probado cada pantalla de la aplicación.

### 3. Ejecutar el servidor de desarrollo

~~~bash
python manage.py runserver
~~~

Abre en el navegador:

~~~text
http://127.0.0.1:8000/
~~~

Verás la página inicial que Django muestra cuando aún no has configurado una página propia.

El servidor permanece ejecutándose en la terminal. Para detenerlo, presiona `Ctrl+C`.

> **Importante.** `runserver` está pensado para desarrollo local, no para publicar una aplicación en producción. Deja `DEBUG=True` únicamente en el entorno de aprendizaje. No compartas contraseñas ni utilices la clave de desarrollo generada por Django como secreto de producción.

Es normal que una instalación inicial muestre un aviso de **migraciones pendientes** de aplicaciones integradas. Todavía no trabajaremos con modelos ni migraciones; ese contenido corresponde al siguiente laboratorio. El aviso no impide estudiar URLs, vistas y plantillas.

### 4. Crear la aplicación `pages`

Detén el servidor y ejecuta:

~~~bash
python manage.py startapp pages
~~~

Ahora tendrás:

~~~text
pages/
├── __init__.py
├── admin.py
├── apps.py
├── migrations/
│   └── __init__.py
├── models.py
├── tests.py
└── views.py
~~~

Aunque se hayan creado archivos relacionados con administración y modelos, **no necesitas completarlos ahora**. Django genera una estructura reutilizable para diferentes tipos de aplicaciones.

### 5. Registrar la aplicación

En `config/settings.py` busca `INSTALLED_APPS` y agrega `pages`, conservando las aplicaciones existentes:

~~~python
INSTALLED_APPS = [
    "django.contrib.admin",
    "django.contrib.auth",
    "django.contrib.contenttypes",
    "django.contrib.sessions",
    "django.contrib.messages",
    "django.contrib.staticfiles",
    "pages",
]
~~~

No reemplaces otras configuraciones del archivo. En `TEMPLATES` comprueba que el motor predeterminado contiene `"APP_DIRS": True`: esto permitirá encontrar las plantillas que coloques dentro de la aplicación.

### Antes de ejecutar — Predice

1. ¿Qué sucedería si ejecutaras `startproject config` sin el punto final?
2. ¿Es lo mismo `config/urls.py` que `pages/views.py`?
3. ¿Por qué `pages` debe aparecer en `INSTALLED_APPS`?
4. ¿Qué puedes afirmar realmente cuando `python manage.py check` termina bien?

### Ejercicio — Reconoce responsabilidades

Completa esta correspondencia antes de consultar la tabla:

~~~text
Global settings -> ?
Main URL configuration -> ?
Page behavior -> ?
Application registration -> ?
Development commands -> ?
~~~

### Depuración — Aplicación no registrada

Elimina **temporalmente** `"pages"` de `INSTALLED_APPS`, ejecuta `python manage.py check` y observa el resultado. Después vuelve a agregarla.

Puede que la comprobación no muestre un error inmediato en este punto, porque aún no hay plantillas ni modelos propios. **No concluyas por ello que el registro es innecesario:** la diferencia será importante cuando Django deba localizar recursos de la aplicación.

### Comprobación

El proyecto debe contener `manage.py`, `config/` y `pages/`; el servidor debe iniciar y `python manage.py check` no debe informar problemas de configuración.

---

## Práctica 6.4 — URLs y primeras vistas

### Objetivos

Al finalizar esta práctica podrás:

- relacionar una dirección con una función de vista;
- utilizar `HttpResponse` para comprobar el flujo;
- dividir las rutas entre el proyecto y la aplicación;
- utilizar `path()`, `include()`, `app_name` y nombres de rutas;
- interpretar una respuesta `404`.

### 1. Crear una primera vista

Abre `pages/views.py` y reemplaza su contenido por:

~~~python
from django.http import HttpResponse


def home(request):
    return HttpResponse("Welcome to ClassroomHub")
~~~

La función recibe `request`, que representa la solicitud HTTP, y devuelve una respuesta. No hemos utilizado todavía una plantilla: primero comprobaremos la conexión entre la URL y la vista.

### 2. Crear las URLs de la aplicación

Crea un archivo nuevo llamado `pages/urls.py`:

~~~python
from django.urls import path

from . import views

app_name = "pages"

urlpatterns = [
    path("", views.home, name="home"),
]
~~~

Observa los elementos:

- `""`: ruta vacía dentro del prefijo asignado a la aplicación;
- `views.home`: función que atenderá la solicitud;
- `name="home"`: nombre de la ruta que usaremos para construir enlaces;
- `app_name="pages"`: espacio de nombres que evita confusiones con rutas de otras aplicaciones.

### 3. Conectar las URLs principales

Abre `config/urls.py` y deja:

~~~python
from django.contrib import admin
from django.urls import include, path

urlpatterns = [
    path("admin/", admin.site.urls),
    path("", include("pages.urls")),
]
~~~

Django revisa las rutas de `config/urls.py`; cuando encuentra `include("pages.urls")`, delega las rutas correspondientes al archivo de la aplicación.

Inicia el servidor:

~~~bash
python manage.py runserver
~~~

Visita:

~~~text
http://127.0.0.1:8000/
~~~

**Resultado esperado:** aparece el texto `Welcome to ClassroomHub`.

### 4. Crear más vistas

Sustituye el contenido de `pages/views.py` por:

~~~python
from django.http import HttpResponse


def home(request):
    return HttpResponse("Welcome to ClassroomHub")


def resources(request):
    return HttpResponse("Learning resources")


def about(request):
    return HttpResponse("About ClassroomHub")
~~~

Actualiza `pages/urls.py`:

~~~python
from django.urls import path

from . import views

app_name = "pages"

urlpatterns = [
    path("", views.home, name="home"),
    path("resources/", views.resources, name="resources"),
    path("about/", views.about, name="about"),
]
~~~

Sin detener el servidor, abre las tres direcciones:

~~~text
http://127.0.0.1:8000/
http://127.0.0.1:8000/resources/
http://127.0.0.1:8000/about/
~~~

| Dirección | Vista | Texto esperado |
|---|---|---|
| `/` | `home` | Welcome to ClassroomHub |
| `/resources/` | `resources` | Learning resources |
| `/about/` | `about` | About ClassroomHub |

### 5. Comprender las barras y los nombres

En `path("resources/", ...)` **no** escribimos la barra inicial: Django construye el patrón desde la raíz correspondiente.

Por ahora utiliza las direcciones completas terminadas en `/`. Django dispone de un comportamiento configurable para añadir barras finales en determinadas situaciones, pero no debes depender de él para ocultar rutas mal escritas.

Los nombres `home`, `resources` y `about` permiten construir enlaces sin escribir las direcciones literalmente en cada plantilla. Lo veremos en la práctica 6.5.

### Antes de ejecutar — Predice

¿Qué obtendrás si escribes `http://127.0.0.1:8000/contact/` sin haber definido esa ruta? ¿Qué archivo inspeccionarías primero?

### Laboratorio de errores

**Error 1 — `404 Not Found`.** Visita `/contact/` intencionalmente. Compara la ruta solicitada con las declaradas en `pages/urls.py`.

**Error 2 — Vista mal escrita.** Cambia temporalmente `views.about` por `views.abot` en `pages/urls.py`. Observa el error en la terminal y restablece el nombre. ¿Por qué no es equivalente al error anterior?

**Error 3 — Archivo no conectado.** Quita temporalmente `include("pages.urls")` de `config/urls.py`. ¿Qué sucede al visitar `/resources/`? Restablece la configuración.

> No conserves los errores provocados. Después de cada experimento vuelve al estado funcional y confirma el resultado en el navegador.

### Reto — Una cuarta ruta sin HTML

Agrega temporalmente una vista `contact` que responda `"Contact information"` y publica la dirección `/contact/`. Hazlo sin copiar todo el archivo de configuración. Después decide si conservarás esa ruta o si la retirarás para mantener el alcance de las tres páginas del reto final.

### Comprobación

Debes tener tres páginas que devuelvan contenido, y poder señalar el recorrido exacto: `config/urls.py` → `pages/urls.py` → `pages/views.py` → `HttpResponse`.

---

## Práctica 6.5 — Plantillas, contexto y navegación

### Objetivos

Al finalizar esta práctica podrás:

- explicar por qué no conviene construir el HTML completo en `HttpResponse`;
- utilizar `render()` para devolver una plantilla;
- ubicar correctamente plantillas de una aplicación;
- enviar datos mediante un diccionario de contexto;
- usar `{{ ... }}`, `{% ... %}`, herencia y nombres de rutas;
- identificar errores de plantillas.

### 1. ¿Por qué usar plantillas?

La respuesta de `HttpResponse("Learning resources")` permite comprobar que una ruta funciona, pero no es una forma cómoda de construir un sitio completo.

Si escribes una página HTML diferente para cada URL, repetirás encabezados, navegación y estructura. Django permite separar la **presentación** de la función que procesa la solicitud.

Una plantilla puede mostrar datos proporcionados por una vista, sin contener la lógica principal de la aplicación.

### 2. Preparar la estructura de plantillas

Crea estas carpetas y archivos manualmente desde VS Code:

~~~text
pages/
├── templates/
│   └── pages/
│       ├── base.html
│       ├── home.html
│       ├── resources.html
│       └── about.html
├── urls.py
└── views.py
~~~

El segundo directorio `pages/` es intencional. El patrón `pages/templates/pages/` evita colisiones de nombres entre plantillas de diferentes aplicaciones.

En `config/settings.py` ya verificaste `"APP_DIRS": True`; no necesitas configurar `DIRS` para este caso.

### 3. Crear la plantilla base

Escribe en `pages/templates/pages/base.html`:

~~~html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{% block title %}ClassroomHub{% endblock %}</title>
    <style>
        body {
            max-width: 900px;
            margin: 0 auto;
            padding: 24px;
            font-family: Arial, sans-serif;
            line-height: 1.5;
            color: #17202a;
        }
        nav {
            display: flex;
            gap: 20px;
            border-bottom: 1px solid #d5d8dc;
            padding-bottom: 16px;
        }
        nav a {
            color: #1b4f72;
            text-decoration: none;
        }
        main {
            padding: 20px 0;
        }
        .resource {
            border: 1px solid #d5d8dc;
            border-radius: 6px;
            padding: 14px;
            margin-bottom: 12px;
        }
    </style>
</head>
<body>
    <header>
        <h1>ClassroomHub</h1>
        <nav aria-label="Main navigation">
            <a href="{% url 'pages:home' %}">Home</a>
            <a href="{% url 'pages:resources' %}">Resources</a>
            <a href="{% url 'pages:about' %}">About</a>
        </nav>
    </header>

    <main>
        {% block content %}{% endblock %}
    </main>

    <footer>
        <small>ClassroomHub — Learning project</small>
    </footer>
</body>
</html>
~~~

Dos conceptos nuevos:

- `{% block ... %}` define una región que las páginas pueden reemplazar.
- `{% url 'pages:resources' %}` construye la URL a partir de su nombre, en lugar de escribir `/resources/` manualmente.

Los estilos CSS están dentro de la plantilla **solo para mantener el primer ejercicio autocontenido**. Más adelante podrán trasladarse a archivos estáticos; no necesitas aprender ese sistema antes de dominar las rutas y plantillas.

### 4. Construir la página principal

Escribe en `pages/templates/pages/home.html`:

~~~html
{% extends "pages/base.html" %}

{% block title %}Home | ClassroomHub{% endblock %}

{% block content %}
    <h2>Welcome</h2>
    <p>{{ welcome_message }}</p>
    <p>
        <a href="{% url 'pages:resources' %}">
            Explore resources
        </a>
    </p>
{% endblock %}
~~~

`{% extends ... %}` indica que esta página utiliza la estructura de `base.html`. El marcador `{{ welcome_message }}` representa un valor que la vista proporcionará al renderizar la plantilla.

### 5. Enviar contexto desde una vista

Sustituye **solo la función** `home` de `pages/views.py` por la siguiente versión. Conserva temporalmente `resources` y `about` con `HttpResponse`:

~~~python
from django.http import HttpResponse
from django.shortcuts import render


def home(request):
    context = {
        "welcome_message": "Explore learning resources and project ideas.",
    }
    return render(request, "pages/home.html", context)


def resources(request):
    return HttpResponse("Learning resources")


def about(request):
    return HttpResponse("About ClassroomHub")
~~~

El método `render()` recibe la solicitud, el nombre de la plantilla y un diccionario de contexto; devuelve la respuesta HTML correspondiente.

Visita nuevamente `/`. Debes ver la página con encabezado, navegación, mensaje y pie compartido.

**No confundas:** `{{ welcome_message }}` es sintaxis de plantilla; `context["welcome_message"]` es un dato de Python.

### 6. Construir una plantilla con una lista

Crea `pages/templates/pages/resources.html`:

~~~html
{% extends "pages/base.html" %}

{% block title %}Resources | ClassroomHub{% endblock %}

{% block content %}
    <h2>Learning resources</h2>
    <p>Browse the available learning materials.</p>

    {% for resource in resources %}
        <article class="resource">
            <h3>{{ resource.title }}</h3>
            <p>Category: {{ resource.category }}</p>

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

Aquí aparecen tres estructuras de Django Template Language:

- `{% for ... %}` recorre la lista proporcionada por la vista.
- `{% if ... %}` muestra contenido según una condición.
- `{% empty %}` permite mostrar un mensaje cuando la lista está vacía.

Estas etiquetas **no son instrucciones de Python ejecutadas libremente dentro del HTML**; son parte del lenguaje de plantillas de Django.

### 7. Enviar una lista desde la vista

Reemplaza la función `resources`:

~~~python
def resources(request):
    context = {
        "resources": [
            {
                "title": "Python basics",
                "category": "Programming",
                "available": True,
            },
            {
                "title": "Git and GitHub",
                "category": "Collaboration",
                "available": True,
            },
            {
                "title": "Django models",
                "category": "Web development",
                "available": False,
            },
        ],
    }
    return render(request, "pages/resources.html", context)
~~~

Visita `/resources/`. Debes ver tres recursos; uno aparecerá con el estado `Coming soon`.

Los datos están en una lista de diccionarios dentro de la vista **porque todavía no hemos estudiado modelos ni bases de datos**. No agregues archivos JSON ni consultas SQL para este ejemplo.

### 8. Construir la página informativa

Crea `pages/templates/pages/about.html`:

~~~html
{% extends "pages/base.html" %}

{% block title %}About | ClassroomHub{% endblock %}

{% block content %}
    <h2>About this project</h2>
    <p>{{ purpose }}</p>
    <p>This site was built as a Django learning exercise.</p>
{% endblock %}
~~~

Reemplaza la función `about`:

~~~python
def about(request):
    context = {
        "purpose": "Practice URL routing, views, and templates.",
    }
    return render(request, "pages/about.html", context)
~~~

Ya tienes las tres páginas renderizadas con plantillas. En la siguiente práctica encontrarás el código de las vistas consolidado para comprobar que no quedaron versiones mezcladas.

### Antes de ejecutar — Predice

1. ¿Qué ocurrirá si eliminas `welcome_message` del contexto pero conservas `{{ welcome_message }}` en la plantilla?
2. ¿Qué cambiará si `"available": False` pasa a `True`?
3. ¿Qué mostrará `resources.html` si envías una lista vacía?
4. Si cambias el nombre de una ruta, ¿por qué conviene actualizar el nombre en un solo lugar en lugar de escribir enlaces literales?

### Ejercicio — Modifica datos, no HTML repetido

Agrega dos recursos en el diccionario de contexto de `resources`. Cambia el estado de disponibilidad de uno de los existentes. Verifica que la plantilla los presenta sin agregar manualmente otro bloque `<article>` por cada recurso.

### Laboratorio de errores

**Error 1 — Plantilla inexistente.** Cambia temporalmente `"pages/home.html"` por `"pages/start.html"` en `render()`. ¿Qué excepción aparece? Corrige la ruta.

**Error 2 — Nombre de URL inexistente.** Cambia en `base.html` `'pages:resources'` por `'pages:materials'`. Identifica el error de resolución de nombres y restablece el nombre correcto.

**Error 3 — Etiqueta de plantilla sin cerrar.** En una copia de `resources.html`, elimina temporalmente `{% endfor %}`. Observa el mensaje del motor de plantillas y vuelve a cerrar el bloque.

**Error 4 — Lista vacía.** Envía `"resources": []` desde la vista. No es un error técnico: verifica el mensaje diseñado para esa situación y restaura la lista.

### Reto — Personaliza sin duplicar

Agrega a la página principal una breve descripción del propósito del portal. Cambia el texto del pie en `base.html` y comprueba que la modificación aparece en las tres páginas. Explica por qué sería más costoso si cada página tuviera una copia completa del HTML.

### Comprobación

Confirma que las tres páginas:

- usan la misma navegación;
- poseen un título específico en la pestaña del navegador;
- muestran datos enviados desde sus vistas;
- mantienen la estructura común sin repetir el HTML base.

---

## Práctica 6.6 — Reto integrador: ClassroomHub

### Objetivo

Consolidar lo aprendido construyendo y verificando un sitio funcional con una aplicación Django, tres rutas públicas, vistas que renderizan plantillas y una estructura que puedas explicar a otra persona.

Este reto se realiza sobre el **mismo proyecto** de las prácticas anteriores. No crees un segundo proyecto para copiar el código: revisa y mejora el que ya construiste.

### 1. Requisitos funcionales

| Código | Requisito | Comprobación |
|---|---|---|
| RF01 | Abrir la página principal | `GET /` muestra encabezado y bienvenida |
| RF02 | Consultar los recursos | `GET /resources/` muestra la lista de recursos |
| RF03 | Consultar información del sitio | `GET /about/` muestra el propósito |
| RF04 | Navegar entre páginas | Los enlaces funcionan desde cualquier página |
| RF05 | Reutilizar el diseño | Las tres páginas heredan de `base.html` |
| RF06 | Distinguir disponibilidad | Cada recurso muestra su estado |
| RF07 | Responder a rutas inexistentes | Una dirección desconocida produce `404` |

Los recursos son datos de ejemplo, no registros persistidos. En esta etapa **no** se requiere que un usuario cree, edite o elimine recursos.

### 2. Estructura final esperada

La estructura relevante debe ser:

~~~text
classroom-hub/
├── .gitignore
├── README.md
├── requirements.txt
├── manage.py
├── config/
│   ├── __init__.py
│   ├── settings.py
│   ├── urls.py
│   ├── asgi.py
│   └── wsgi.py
└── pages/
    ├── __init__.py
    ├── admin.py
    ├── apps.py
    ├── migrations/
    │   └── __init__.py
    ├── models.py
    ├── tests.py
    ├── urls.py
    ├── views.py
    └── templates/
        └── pages/
            ├── base.html
            ├── home.html
            ├── resources.html
            └── about.html
~~~

`.venv/` debe existir localmente, pero no debe aparecer en GitHub.

### 3. Verificar el código final de las vistas

El archivo completo `pages/views.py` debe quedar así:

~~~python
from django.shortcuts import render


def home(request):
    context = {
        "welcome_message": "Explore learning resources and project ideas.",
    }
    return render(request, "pages/home.html", context)


def resources(request):
    context = {
        "resources": [
            {
                "title": "Python basics",
                "category": "Programming",
                "available": True,
            },
            {
                "title": "Git and GitHub",
                "category": "Collaboration",
                "available": True,
            },
            {
                "title": "Django models",
                "category": "Web development",
                "available": False,
            },
        ],
    }
    return render(request, "pages/resources.html", context)


def about(request):
    context = {
        "purpose": "Practice URL routing, views, and templates.",
    }
    return render(request, "pages/about.html", context)
~~~

Este ejemplo es pequeño a propósito. Cada vista tiene una función clara y no realiza acceso a bases de datos ni contiene lógica de negocio compleja.

### 4. Verificar las rutas

El archivo completo `pages/urls.py`:

~~~python
from django.urls import path

from . import views

app_name = "pages"

urlpatterns = [
    path("", views.home, name="home"),
    path("resources/", views.resources, name="resources"),
    path("about/", views.about, name="about"),
]
~~~

El archivo completo `config/urls.py`:

~~~python
from django.contrib import admin
from django.urls import include, path

urlpatterns = [
    path("admin/", admin.site.urls),
    path("", include("pages.urls")),
]
~~~

Las plantillas son las construidas en la práctica 6.5. No sustituyas los nombres de rutas por enlaces literales solo para hacer funcionar la navegación.

### 5. Pruebas manuales reproducibles

Ejecuta en la raíz del proyecto:

~~~bash
python manage.py check
python manage.py runserver
~~~

Abre cada página y anota qué observaste:

| Prueba | Acción | Resultado esperado |
|---|---|---|
| P01 | Abrir `/` | La página principal responde y muestra la bienvenida |
| P02 | Abrir `/resources/` | Se muestran los tres recursos |
| P03 | Abrir `/about/` | Se muestra el propósito del proyecto |
| P04 | Hacer clic en los tres enlaces desde varias páginas | La navegación conserva la estructura y abre la ruta correcta |
| P05 | Abrir `/not-found/` | Se obtiene una respuesta `404` |
| P06 | Cambiar temporalmente un recurso a no disponible | El texto cambia a `Coming soon` |
| P07 | Enviar temporalmente una lista vacía | Se muestra `No resources are available.` |

Restablece los datos de ejemplo después de las pruebas P06 y P07.

### 6. Primera prueba automatizada

Una aplicación puede parecer correcta en el navegador y romperse tras modificar una ruta o plantilla. Para detectar esos cambios, crea una prueba sencilla con las herramientas de Django.

Reemplaza `pages/tests.py` por:

~~~python
from django.test import SimpleTestCase
from django.urls import reverse


class PageViewsTests(SimpleTestCase):
    def test_home_page_renders(self):
        response = self.client.get(reverse("pages:home"))

        self.assertEqual(response.status_code, 200)
        self.assertTemplateUsed(response, "pages/home.html")
        self.assertContains(response, "Welcome")

    def test_resources_page_shows_example_data(self):
        response = self.client.get(reverse("pages:resources"))

        self.assertEqual(response.status_code, 200)
        self.assertTemplateUsed(response, "pages/resources.html")
        self.assertContains(response, "Python basics")
        self.assertContains(response, "Coming soon")

    def test_about_page_renders(self):
        response = self.client.get(reverse("pages:about"))

        self.assertEqual(response.status_code, 200)
        self.assertTemplateUsed(response, "pages/about.html")

    def test_unknown_page_returns_not_found(self):
        response = self.client.get("/not-found/")

        self.assertEqual(response.status_code, 404)
~~~

Detén el servidor si deseas utilizar la misma terminal y ejecuta:

~~~bash
python manage.py test pages
~~~

**Resultado esperado:** cuatro pruebas exitosas.

Los nombres de rutas se resuelven mediante `reverse()`, de manera coherente con los enlaces de plantilla. `SimpleTestCase` permite realizar estas verificaciones sin crear datos de prueba en una base de datos.

**Experimento:** cambia temporalmente `"pages/resources.html"` por `"pages/missing.html"` en la vista. Ejecuta las pruebas, observa cuál falla y corrige el código. Una prueba útil debe poder detectar un defecto real.

### 7. Depuración — Problemas que debes saber resolver

**Caso A — El servidor inicia, pero `/resources/` devuelve 404.**

Revisa, en este orden:

1. la URL escrita en el navegador;
2. `config/urls.py` y su llamada a `include()`;
3. `pages/urls.py` y el patrón `resources/`;
4. que el servidor está ejecutando este proyecto y no otro.

**Caso B — La vista existe, pero Django no encuentra `resources.html`.**

Revisa `pages/templates/pages/resources.html`, el nombre indicado en `render()`, `APP_DIRS` y `INSTALLED_APPS`. No dupliques plantillas en otras carpetas hasta comprender el error.

**Caso C — La página aparece, pero no se muestra la lista.**

Revisa la clave `"resources"` del contexto y la variable `resources` del `for`. Comprueba primero qué datos envía la vista. Recuerda que un nombre ausente en una plantilla no siempre produce una excepción visible.

**Caso D — Un enlace del menú falla después de renombrar una ruta.**

Compara el valor de `name` y `app_name` en `pages/urls.py` con lo utilizado por `{% url ... %}`.

### 8. Organización y seguimiento en GitHub

Utiliza un repositorio personal para el proyecto **ClassroomHub**. En el repositorio deben encontrarse los archivos fuente y las instrucciones de ejecución, no el entorno virtual.

Una secuencia posible de commits es:

~~~text
chore: set up Django environment and project
feat: add pages app and URL routes
feat: render pages with shared templates
test: verify page responses and navigation
docs: explain local setup and verification
~~~

Los commits deben describir cambios **reales**. Si desarrollaste todo antes de crear el repositorio, no inventes una historia de trabajo que no ocurrió: registra honestamente el estado y documenta cómo lo comprobaste.

El `README.md` debe explicar, al menos:

- qué hace ClassroomHub;
- qué versiones de Python y Django utiliza el proyecto;
- cómo crear y activar el entorno;
- cómo instalar dependencias;
- cómo ejecutar el servidor;
- cuáles son las tres rutas públicas;
- cómo ejecutar las pruebas;
- qué funcionalidades **todavía no** incluye.

Una forma breve de documentar los comandos es:

~~~bash
python -m venv .venv
python -m pip install -r requirements.txt
python manage.py check
python manage.py test pages
python manage.py runserver
~~~

Antes de utilizar los comandos, activa el entorno o selecciona su intérprete. En un equipo que utiliza el lanzador `py` u otro nombre de ejecutable, adapta los comandos coherentemente.

### 9. Reto de ampliación — Página de preguntas frecuentes

Después de completar y comprobar las tres páginas, crea una **cuarta página opcional** en `/faq/`:

1. define una función `faq` en `pages/views.py`;
2. declara una ruta con `name="faq"` en `pages/urls.py`;
3. crea `pages/templates/pages/faq.html` heredando `pages/base.html`;
4. agrega un enlace en la navegación común;
5. incorpora una prueba que compruebe el código `200` y la plantilla utilizada.

La página debe responder al menos dos preguntas acerca de ClassroomHub. No utilices formularios, modelos ni bases de datos: el objetivo sigue siendo practicar la relación entre rutas, vistas y plantillas.

### 10. Preguntas de sustentación

Responde con tus palabras y, cuando sea posible, señala el archivo correspondiente:

1. ¿Qué diferencia existe entre el proyecto `config` y la aplicación `pages`?
2. ¿Qué ocurre, desde que escribes `/resources/`, hasta que ves la lista en el navegador?
3. ¿Qué ventaja aporta `include()` frente a colocar todas las rutas en un único archivo?
4. ¿Por qué utilizamos `render()` en vez de crear todo el HTML con `HttpResponse`?
5. ¿Qué problema resuelve `base.html` y qué ocurre si se modifica su navegación?
6. ¿Qué diferencia hay entre una respuesta `404` y un error `TemplateDoesNotExist`?
7. ¿Por qué los recursos se pierden o reinician cuando modificas el código o reinicias el proceso? ¿Qué tema deberá abordar el siguiente laboratorio?
8. ¿Qué demuestra `python manage.py check` y qué demuestran las pruebas automatizadas? ¿Por qué no son equivalentes?

### 11. Revisión final antes de compartir

Verifica todos los puntos:

- [ ] Se puede reconstruir el entorno desde `requirements.txt`.
- [ ] `.venv/` no está versionado.
- [ ] La aplicación `pages` está registrada.
- [ ] `/`, `/resources/` y `/about/` funcionan.
- [ ] Los enlaces usan nombres de ruta.
- [ ] Las páginas comparten `base.html`.
- [ ] Los datos de las vistas aparecen en las plantillas.
- [ ] Una ruta inexistente devuelve `404`.
- [ ] `python manage.py check` termina correctamente.
- [ ] `python manage.py test pages` supera las cuatro pruebas.
- [ ] El `README.md` explica cómo ejecutar y verificar el sitio.
- [ ] No se subieron credenciales, secretos reales ni archivos locales innecesarios.

### 12. Entrega de la práctica

Comparte el **enlace al repositorio personal de GitHub** y asegúrate de que el `README.md` permite reproducir el resultado. En la clase presencial, demuestra el funcionamiento y explica qué archivos intervienen en una de las solicitudes.

Este laboratorio es una **práctica formativa**. No crea por sí mismo un nuevo código oficial de evidencia ni sustituye la evidencia del proyecto indicada en el cronograma. Si se requiere un registro en Moodle, puede utilizarse una Tarea para recibir el enlace y registrar la retroalimentación, sin exigir un PDF adicional por defecto.

---

## Cierre del laboratorio

Al finalizar deberías poder crear un entorno virtual, instalar Django, diferenciar un proyecto de una aplicación, configurar rutas, implementar vistas sencillas, renderizar plantillas y construir un sitio web con navegación común.

Tu aplicación ya tiene una estructura reconocible, pero sus recursos son **datos de ejemplo definidos en el código**. Aún no puedes administrarlos ni conservar cambios de usuarios. Esa limitación abre el camino para el **Laboratorio 7: modelos, ORM, migraciones y administrador de Django**.

Conserva ClassroomHub y su historial. Podrás reutilizar la estructura en los siguientes ejercicios sin rehacer la configuración inicial.

### Referencias

- [Django 6.1 — Tutorial 1: proyecto, aplicaciones y URLs](https://docs.djangoproject.com/en/6.1/intro/tutorial01/)
- [Django 6.1 — Tutorial 3: vistas y plantillas](https://docs.djangoproject.com/en/6.1/intro/tutorial03/)
- [Django 6.1 — Templates](https://docs.djangoproject.com/en/6.1/topics/templates/)
- [Django 6.1 — URL dispatcher](https://docs.djangoproject.com/en/6.1/topics/http/urls/)
- [Django 6.1 — Testing tools](https://docs.djangoproject.com/en/6.1/topics/testing/tools/)
- [Django — Compatibilidad de Python](https://docs.djangoproject.com/en/6.1/faq/install/)
- [Python — Entornos virtuales (`venv`)](https://docs.python.org/3/library/venv.html)
- [GitHub Docs — Ignoring files](https://docs.github.com/en/get-started/getting-started-with-git/ignoring-files)
