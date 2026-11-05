# Laboratorio 8 — Autenticación, sesiones, grupos y permisos en Django

En el [Laboratorio 7](laboratorio-07.md) conectaste **ClassroomHub** a MySQL o MariaDB, creaste los modelos `Category` y `Resource` y sustituiste una lista de datos escrita en Python por registros persistentes. Ahora darás otro paso: distinguirás entre un visitante, una persona autenticada y una cuenta que dispone de permisos específicos.

El objetivo es utilizar **las herramientas de autenticación que ya incluye Django**. Construirás registro de cuentas, inicio y cierre de sesión, un panel privado y una vista de revisión del catálogo reservada a un grupo. El catálogo `/resources/` permanecerá público. No crearás todavía el CRUD público: ese tema se desarrollará después.

| Práctica | Tema | Resultado verificable |
|---|---|---|
| 8.1 | Autenticación, autorización y sesiones | Reconoces usuarios, contraseñas, cookies y tablas del framework |
| 8.2 | Usuarios y registro | Creas cuentas comunes mediante un formulario validado |
| 8.3 | Inicio y cierre de sesión | Utilizas `LoginView` y `LogoutView` con POST y CSRF |
| 8.4 | Panel privado y sesiones | Proteges una vista y compruebas el estado de sesión |
| 8.5 | Grupos y permisos | Permites una revisión de solo lectura únicamente a cuentas autorizadas |
| 8.6 | Reto integrador y pruebas | Compruebas registro, login, logout, 403 y compatibilidad con el catálogo |

> **Método de trabajo.** Sigue los ejemplos en orden, predice resultados antes de ejecutarlos, provoca errores controlados y explica cómo los corregiste. Si un bloque reemplaza el contenido completo de un archivo, se indica expresamente. Todos los nombres de código, rutas, clases e identificadores están en inglés.

## Alcance y preparación

Continúa **el mismo proyecto `classroom-hub`** de los laboratorios 6 y 7. No vuelvas a ejecutar `startproject` ni `startapp`.

Debes conservar `manage.py`, `config/`, la aplicación `pages`, las plantillas, los modelos `Category` y `Resource` y la conexión a **MySQL o MariaDB**. Se utilizan las mismas dependencias del Laboratorio 7 y el mismo backend `django.db.backends.mysql`: **no necesitas cambiar de base de datos**.

La versión de referencia es **Django 6.1.3**. Si tu proyecto utiliza una rama compatible de **Django 5.2 LTS**, mantén esa rama y consulta su documentación. Los componentes que aprenderás (`UserCreationForm`, `LoginView`, `LogoutView`, `login_required` y `permission_required`) están disponibles en ambas.

Comprueba el estado inicial:

~~~bash
python -m django --version
python manage.py check
python manage.py showmigrations auth sessions pages
~~~

Si hay migraciones integradas pendientes, comprueba la configuración de la base y ejecuta:

~~~bash
python manage.py migrate
~~~

Las aplicaciones predeterminadas `auth` y `sessions` suelen estar habilitadas desde `startproject` y se migraron al preparar Django Admin en el Laboratorio 7. No crees manualmente tablas SQL para las cuentas o las sesiones.

**Fuera de alcance:** OAuth, autenticación multifactor, recuperación de contraseña por correo, modelos de usuario personalizados, endpoints de edición pública y despliegue a producción. Este laboratorio enseña a emplear y verificar una base de autenticación, no a declarar segura una aplicación productiva.

---

## Práctica 8.1 — Identidad, contraseñas y sesiones

### Objetivos

Al finalizar podrás explicar la diferencia entre autenticación y autorización; distinguir el usuario SQL del usuario del sitio; identificar cómo Django verifica contraseñas y reconocer la relación entre cookie y sesión.

### 1. Dos identidades que no se deben confundir

| Identidad | Función |
|---|---|
| `classroomhub_user` | Cuenta de MySQL/MariaDB utilizada por la aplicación en `DATABASES` |
| Usuario de Django | Cuenta de una persona que navega por ClassroomHub |
| Superusuario Django | Cuenta con privilegios administrativos dentro de Django |
| Grupo Django | Conjunto de cuentas a las que puedes asignar permisos |

Cuando una persona inicia sesión, **no** se conecta al motor MySQL utilizando su propia contraseña SQL. Django usa la conexión configurada en `settings.py` y gestiona la identidad del visitante con `django.contrib.auth`.

### 2. Autenticación frente a autorización

**Autenticación** responde: «¿quién es esta persona?». Ejemplo: comprobar usuario y contraseña.

**Autorización** responde: «¿puede esta persona acceder a esta función?». Ejemplo: tener permiso para revisar información.

~~~text
Request /resources/review/
           |
           v
Authenticated?
     /           \
    No           Yes
    |             |
Login redirect    v
             Has view permission?
                /          \
               No          Yes
               |            |
            HTTP 403      HTTP 200
~~~

No basta con esconder un enlace en el menú. Una persona puede escribir la URL: la comprobación de permisos debe realizarse **en el servidor**.

### 3. ¿Dónde guarda Django la información?

En un proyecto con el usuario estándar, Django utiliza tablas habituales como:

~~~text
auth_user
auth_group
auth_permission
auth_user_groups
auth_group_permissions
django_session
~~~

Consulta la existencia de esas tablas en tu motor:

~~~sql
USE classroom_hub;
SHOW TABLES LIKE 'auth_%';
SHOW TABLES LIKE 'django_session';
~~~

Esas tablas se crean mediante migraciones. No insertes contraseñas ni asignaciones de permisos directamente con SQL: Django dispone de APIs para mantener sus reglas de seguridad.

### 4. Las contraseñas no se almacenan en texto plano

En el shell:

~~~bash
python manage.py shell
~~~

Prueba:

~~~python
from django.contrib.auth.hashers import make_password, check_password

encoded = make_password("ExamplePassword!2026")
print(encoded != "ExamplePassword!2026")
print(check_password("ExamplePassword!2026", encoded))
print(check_password("WrongPassword", encoded))
~~~

Resultados lógicos: `True`, `True`, `False`. La cadena de ejemplo **no es una contraseña para una cuenta real**.

Django guarda una representación segura con un hasher, no la contraseña original. Para crear o cambiar contraseñas utilizaremos `create_user()`, `set_password()` y `UserCreationForm`. Escribir directamente `user.password = "text"` no realiza el procesamiento correcto.

### 5. Sesión y cookie

Cuando el navegador envía credenciales válidas por POST, Django autentica al usuario y establece una sesión. En la configuración predeterminada, el estado de sesión se almacena en la base de datos y el navegador conserva una cookie que contiene su **identificador de sesión**, no la contraseña.

~~~text
Browser                       Django / MySQL-MariaDB
  | POST /accounts/login/                |
  |------------------------------------->|
  |                         Authenticate account
  |                         Save server-side session
  |<-------------------------------------|
  | Cookie: sessionid=...                |
  |                                      |
  | GET /dashboard/ + cookie            |
  |------------------------------------->|
  |                         Resolve request.user
  |<-------------------------------------|
  | Dashboard HTML                       |
~~~

Una cookie de sesión válida es sensible: **no la compartas** en capturas, archivos de registro públicos ni repositorios.

### 6. Verificar middleware y aplicaciones

En `config/settings.py` conserva las aplicaciones predeterminadas, incluyendo:

~~~python
"django.contrib.auth",
"django.contrib.contenttypes",
"django.contrib.sessions",
"pages",
~~~

Comprueba que `MIDDLEWARE` contiene, en el orden apropiado:

~~~python
"django.contrib.sessions.middleware.SessionMiddleware",
"django.middleware.csrf.CsrfViewMiddleware",
"django.contrib.auth.middleware.AuthenticationMiddleware",
~~~

Estos son **fragmentos para comprobar**, no reemplazos completos de `INSTALLED_APPS` o `MIDDLEWARE`. `SessionMiddleware` debe aparecer antes de `AuthenticationMiddleware`. En `TEMPLATES` conserva `django.contrib.auth.context_processors.auth`.

### Antes de ejecutar — Predice

1. ¿Qué usuario se conecta a MySQL cuando alguien abre `/resources/`?
2. ¿Por qué un visitante autenticado no tiene automáticamente permiso para revisar el catálogo?
3. ¿Qué diferencia existe entre la cookie y el registro de sesión?
4. ¿Por qué ocultar un enlace no protege una vista?

### Comprobación

Puedes explicar qué función cumplen `auth_user`, `django_session`, `request.user` y el usuario SQL, sin tratarlos como equivalentes.

---

## Práctica 8.2 — Cuentas y registro con `UserCreationForm`

### Objetivos

Consultar usuarios, crear una cuenta con contraseña correctamente procesada, utilizar un formulario de registro y comprobar que las cuentas nuevas no reciben privilegios administrativos.

### 1. Consultar el modelo de usuario

Abre el shell y ejecuta:

~~~python
from django.contrib.auth import get_user_model

User = get_user_model()
print(User.objects.count())
print(list(User.objects.values_list("username", flat=True)[:5]))
~~~

`get_user_model()` devuelve el modelo de usuario activo. ClassroomHub continúa con el modelo predeterminado de Django; no necesitas sustituirlo en este laboratorio.

### 2. Crear un usuario desde Python

Ejemplo local, diseñado para no sobrescribir una cuenta existente:

~~~python
user, created = User.objects.get_or_create(
    username="reader_demo",
)

if created:
    user.set_password("TemporaryExample!2026")
    user.save(update_fields=["password"])

print(user.is_staff)
print(user.is_superuser)
~~~

Un usuario recién creado mediante este ejemplo no es staff ni superusuario. Si ya existía, `get_or_create()` no cambia su contraseña ni sus atributos. No reutilices esta contraseña demostrativa en cuentas accesibles a otras personas.

También existe `User.objects.create_user(username=..., password=...)`. Es preferible a asignar texto directamente al campo `password` porque realiza el procesamiento necesario.

### 3. ¿Qué aporta `UserCreationForm`?

El formulario incluido en Django solicita nombre de usuario, contraseña y confirmación. Comprueba la coincidencia de contraseñas y utiliza los validadores configurados.

No incluiremos campos como `is_staff`, `is_superuser` ni `groups` en un formulario público. Un visitante **nunca debe poder elegir sus privilegios** durante el registro.

En `pages/views.py` añade los imports necesarios:

~~~python
from django.contrib.auth.forms import UserCreationForm
from django.shortcuts import redirect, render
~~~

Agrega la función, conservando `home`, `resources` y `about`:

~~~python
def signup(request):
    if request.user.is_authenticated:
        return redirect("pages:dashboard")

    if request.method == "POST":
        form = UserCreationForm(request.POST)
        if form.is_valid():
            form.save()
            return redirect("pages:login")
    else:
        form = UserCreationForm()

    return render(request, "pages/signup.html", {"form": form})
~~~

Si el formulario es válido, guarda la cuenta y redirige a la pantalla de login. **Crear la cuenta no significa iniciar sesión automáticamente**. Si el formulario es inválido, se presenta otra vez con los errores para que la persona pueda corregirlos.

### 4. Crear la plantilla

Crea `pages/templates/pages/signup.html`:

~~~html
{% extends "pages/base.html" %}

{% block title %}Sign up | ClassroomHub{% endblock %}

{% block content %}
    <h2>Create an account</h2>

    <form method="post">
        {% csrf_token %}
        {{ form.non_field_errors }}
        {{ form.as_p }}
        <button type="submit">Create account</button>
    </form>

    <p>Already have an account?
        <a href="{% url 'pages:login' %}">Log in</a>
    </p>
{% endblock %}
~~~

`{% csrf_token %}` protege formularios POST contra ataques CSRF. `form.as_p` presenta campos y errores del formulario sin que tengas que reimplementar la validación de contraseñas.

La URL y la vista de login se declararán en la práctica siguiente. **No intentes renderizar esta plantilla antes de agregar esa ruta**, porque el enlace podría provocar `NoReverseMatch`.

### 5. Analizar resultados incorrectos

Predice qué ocurre en cada situación:

- El nombre de usuario ya está registrado.
- Las contraseñas no coinciden.
- La contraseña incumple los validadores configurados.
- Una cuenta autenticada visita nuevamente `/accounts/signup/`.

En los primeros casos el formulario debe mostrar errores **sin guardar una cuenta**. En el último se redirigirá al panel privado, una vez creado.

### Reto — Usuario sin privilegios

Después de habilitar el registro, crea una cuenta normal. En el shell comprueba:

~~~python
User = get_user_model()
account = User.objects.get(username="your_username")

print(account.is_staff)
print(account.is_superuser)
print(account.groups.count())
~~~

Sustituye `your_username` por la cuenta creada. Los resultados esperados para un registro nuevo, sin asignaciones posteriores, son `False`, `False` y `0`.

### Comprobación

El registro crea usuarios normales, valida sus datos y nunca acepta privilegios de administración desde el formulario público.

---

## Práctica 8.3 — Login y logout con vistas integradas

### Objetivos

Configurar `LoginView` y `LogoutView`, comprender GET y POST, conservar un destino seguro después del login, e integrar formularios con protección CSRF.

### 1. Configurar destinos de autenticación

En `config/settings.py` agrega:

~~~python
LOGIN_URL = "pages:login"
LOGIN_REDIRECT_URL = "pages:dashboard"
LOGOUT_REDIRECT_URL = "pages:home"
~~~

Se utilizan **nombres de rutas**. `LOGIN_URL` permite redirigir a usuarios anónimos cuando una vista exige autenticación; `LOGIN_REDIRECT_URL` y `LOGOUT_REDIRECT_URL` definen destinos habituales tras login y logout.

### 2. Crear las rutas

En `pages/urls.py` conserva las rutas del Laboratorio 7 y agrega los imports:

~~~python
from django.contrib.auth import views as auth_views
from django.urls import path
~~~

Después de `path("about/", ...)` añade:

~~~python
path("accounts/signup/", views.signup, name="signup"),
path(
    "accounts/login/",
    auth_views.LoginView.as_view(template_name="pages/login.html"),
    name="login",
),
path(
    "accounts/logout/",
    auth_views.LogoutView.as_view(),
    name="logout",
),
~~~

Por ahora no agregues `dashboard` ni `resource_review`: lo harás cuando existan esas vistas. Al final del laboratorio encontrarás `pages/urls.py` completo.

### 3. Crear la plantilla del login

Crea `pages/templates/pages/login.html`:

~~~html
{% extends "pages/base.html" %}

{% block title %}Log in | ClassroomHub{% endblock %}

{% block content %}
    <h2>Log in</h2>

    {% if form.errors %}
        <p>The credentials were not accepted.</p>
    {% endif %}

    <form method="post">
        {% csrf_token %}
        {{ form.as_p }}
        <input type="hidden" name="next" value="{{ next }}">
        <button type="submit">Log in</button>
    </form>

    <p>New to ClassroomHub?
        <a href="{% url 'pages:signup' %}">Create an account</a>
    </p>
{% endblock %}
~~~

`LoginView` utiliza las herramientas de autenticación de Django. **No escribas una función propia que compare el texto de las contraseñas**.

El campo `next` permite regresar a la página solicitada antes del login. `LoginView` comprueba si la redirección es segura; no utilices `redirect(request.POST["next"])` sin validación.

### 4. Cerrar sesión mediante POST

En Django actual, `LogoutView` realiza el cierre de sesión por **POST**. No crees un enlace GET como `<a href="/accounts/logout/">` para salir.

El formulario correcto es:

~~~html
<form method="post" action="{% url 'pages:logout' %}">
    {% csrf_token %}
    <button type="submit">Log out</button>
</form>
~~~

Si abres `/accounts/logout/` directamente en el navegador mediante GET, el resultado esperado es **405 Method Not Allowed**. No desactives CSRF para evitar una respuesta 403 de un formulario incorrecto.

### 5. Actualizar la navegación de `base.html`

En `pages/templates/pages/base.html` reemplaza **únicamente el bloque de navegación** por esta versión **provisional**, que funciona sin las rutas de las prácticas siguientes:

~~~html
<nav aria-label="Main navigation">
    <a href="{% url 'pages:home' %}">Home</a>
    <a href="{% url 'pages:resources' %}">Resources</a>
    <a href="{% url 'pages:about' %}">About</a>

    {% if user.is_authenticated %}
        <form method="post" action="{% url 'pages:logout' %}">
            {% csrf_token %}
            <button type="submit">Log out</button>
        </form>
    {% else %}
        <a href="{% url 'pages:login' %}">Log in</a>
        <a href="{% url 'pages:signup' %}">Sign up</a>
    {% endif %}
</nav>
~~~

El procesador `django.contrib.auth.context_processors.auth` permite utilizar `user.is_authenticated` en las plantillas. **Este cambio solo adapta la presentación**; no sustituye las comprobaciones del servidor.

### 6. Comprobar el flujo

Ejecuta:

~~~bash
python manage.py check
python manage.py runserver
~~~

Prueba:

1. Abre `/accounts/signup/` y crea una cuenta normal.
2. Verifica que se redirige a `/accounts/login/`.
3. Envía una contraseña incorrecta y observa el error.
4. Envía las credenciales correctas. El destino `pages:dashboard` se habilitará al crear esa ruta en la práctica 8.4.
5. Envía el formulario `Log out`.
6. Verifica que el menú vuelve a mostrar `Log in` y `Sign up`.

**Nota de secuencia:** como `LOGIN_REDIRECT_URL` apunta al panel que construirás a continuación, **agrega la ruta del panel antes de probar un login exitoso**. El formulario, las validaciones y el registro sí pueden comprobarse con la versión actual.

### Laboratorio de errores

- **`TemplateDoesNotExist`:** revisa `pages/templates/pages/login.html` y la configuración de plantillas.
- **`NoReverseMatch`:** comprueba los nombres `pages:signup` y `pages:login`; antes de probar un login exitoso crea `pages:dashboard`.
- **`405` al cerrar sesión:** estás usando GET en vez del formulario POST.
- **`403 CSRF verification failed`:** falta el token, no se envía la cookie esperada o se deshabilitó la protección. No desactives CSRF como solución.

### Comprobación

Puedes distinguir la validación del registro, la autenticación durante el login y la terminación de sesión por POST.

---

## Práctica 8.4 — Un panel privado y el estado de sesión

### Objetivos

Proteger una vista con `login_required`, consultar `request.user`, guardar un contador didáctico en `request.session` y comprender el destino `next`.

### 1. Crear una vista privada

Añade a `pages/views.py`:

~~~python
from django.contrib.auth.decorators import login_required


@login_required
def dashboard(request):
    visit_count = request.session.get("dashboard_visits", 0) + 1
    request.session["dashboard_visits"] = visit_count

    return render(
        request,
        "pages/dashboard.html",
        {"visit_count": visit_count},
    )
~~~

`@login_required` exige que la solicitud corresponda a una cuenta autenticada. El contador es **un ejemplo de información temporal de sesión**, no una estadística permanente ni un dato que conceda permisos.

### 2. Crear `dashboard.html`

En `pages/templates/pages/dashboard.html`:

~~~html
{% extends "pages/base.html" %}

{% block title %}Dashboard | ClassroomHub{% endblock %}

{% block content %}
    <h2>Your dashboard</h2>
    <p>Signed in as: {{ user.username }}</p>
    <p>Visits in this session: {{ visit_count }}</p>
    <p>This page requires an authenticated account.</p>
{% endblock %}
~~~

### 3. Agregar la ruta

En `pages/urls.py` añade:

~~~python
path("dashboard/", views.dashboard, name="dashboard"),
~~~

En el bloque de usuario autenticado de `base.html` agrega el enlace al panel **antes del formulario de logout**:

~~~html
<a href="{% url 'pages:dashboard' %}">Dashboard</a>
~~~

### 4. Comprobar `next` y la sesión

Sin iniciar sesión, abre:

~~~text
http://127.0.0.1:8000/dashboard/
~~~

Django redirigirá a una dirección equivalente a:

~~~text
/accounts/login/?next=/dashboard/
~~~

Inicia sesión: `LoginView` debe respetar el destino permitido y devolver la navegación al panel. Recarga la página varias veces: aumentará el contador guardado en la sesión.

El diccionario `request.session` se comporta de forma parecida a:

~~~python
current = request.session.get("dashboard_visits", 0)
request.session["dashboard_visits"] = current + 1
~~~

Sin embargo, no es una variable global ni una lista guardada en `views.py`. Su información se almacena mediante el motor de sesiones, que en esta aplicación utiliza MySQL o MariaDB de manera predeterminada.

### 5. Configuraciones que debes reconocer

| Ajuste | Qué controla |
|---|---|
| `SESSION_ENGINE` | Motor de almacenamiento de sesiones |
| `SESSION_COOKIE_HTTPONLY` | Acceso de JavaScript a la cookie |
| `SESSION_COOKIE_SAMESITE` | Restricciones para solicitudes entre sitios |
| `SESSION_COOKIE_SECURE` | Envío de cookie mediante HTTPS |
| `SESSION_COOKIE_AGE` | Tiempo de vida predeterminado |
| `SESSION_EXPIRE_AT_BROWSER_CLOSE` | Comportamiento al cerrar el navegador |

Durante desarrollo local por HTTP no establezcas ciegamente `SESSION_COOKIE_SECURE=True`, porque la cookie podría dejar de enviarse. Un despliegue público necesita HTTPS y una configuración de seguridad revisada.

La tabla `django_session` puede acumular registros expirados. Django ofrece `python manage.py clearsessions` para eliminarlos. **No modifiques a mano claves de autenticación en la sesión** para implementar un logout.

### Antes de ejecutar — Predice

1. ¿Qué sucede con `/dashboard/` al cerrar sesión?
2. ¿Por qué dos navegadores no deben compartir automáticamente el mismo contador?
3. ¿Qué diferencia existe entre `request.user` y `request.session`?
4. ¿Por qué sería un error guardar `"is_reviewer": True` en la sesión y utilizarlo como única autorización?

### Ejercicio — Experimento de sesión

Guarda temporalmente un texto no secreto, por ejemplo `"preferred_section": "Resources"`, en `request.session` y muéstralo en el panel. Comprueba que persiste entre dos solicitudes y desaparece al cerrar sesión. Restablece el panel del ejemplo cuando termines.

### Comprobación

Una cuenta autenticada puede visitar el panel, un visitante anónimo es redirigido y los datos de sesión desaparecen al cerrar sesión correctamente.

---

## Práctica 8.5 — Grupos, permisos y revisión autorizada

### Objetivos

Distinguir `is_authenticated`, `is_staff` y `has_perm`; crear un grupo, asignarle un permiso existente, proteger una vista en el servidor y reconocer un HTTP 403.

### 1. Los permisos predeterminados de un modelo

Al aplicar las migraciones de `pages`, Django crea los permisos básicos de `Resource`:

| Permiso | Significado |
|---|---|
| `pages.view_resource` | Ver recursos en una función protegida por este permiso |
| `pages.add_resource` | Agregar recursos |
| `pages.change_resource` | Cambiar recursos |
| `pages.delete_resource` | Eliminar recursos |

Estos permisos no restringen automáticamente la página pública `/resources/`. **Cada vista debe implementar el control de acceso que corresponda**.

Una cuenta puede estar autenticada y no tener ninguno de esos permisos; también puede pertenecer a un grupo que se los otorgue.

### 2. Definir una regla concreta

Crearemos el grupo **Catalog reviewers**, con un único permiso: `pages.view_resource`.

| Ruta | Anónimo | Cuenta común | Revisor |
|---|---|---|---|
| `/resources/` | Acceso | Acceso | Acceso |
| `/dashboard/` | Login | Acceso | Acceso |
| `/resources/review/` | Login | HTTP 403 | Acceso |

No añadiremos edición pública ni permisos de administrador. Es suficiente para comprender la diferencia entre una página pública, una página privada y una vista autorizada por permisos.

### 3. Crear el grupo desde el shell

Puedes configurarlo desde Django Admin, en la administración de grupos, o ejecutar:

~~~bash
python manage.py shell
~~~

Dentro del shell:

~~~python
from django.contrib.auth.models import Group, Permission
from django.contrib.contenttypes.models import ContentType
from pages.models import Resource

content_type = ContentType.objects.get_for_model(Resource)
view_permission = Permission.objects.get(
    content_type=content_type,
    codename="view_resource",
)

reviewers, created = Group.objects.get_or_create(
    name="Catalog reviewers",
)
reviewers.permissions.add(view_permission)

print(list(reviewers.permissions.values_list("codename", flat=True)))
~~~

`get_or_create` evita crear grupos duplicados si vuelves a ejecutar el ejemplo. El permiso ya existe por las migraciones de Django: no hay que insertarlo manualmente en SQL.

### 4. Asociar una cuenta al grupo

Con una cuenta común creada en el registro, el administrador puede asignarle el grupo desde Django Admin. Alternativamente, si conservaste `reader_demo`:

~~~python
from django.contrib.auth import get_user_model

User = get_user_model()
reviewer = User.objects.get(username="reader_demo")
reviewer.groups.add(reviewers)

reviewer = User.objects.get(pk=reviewer.pk)
print(reviewer.has_perm("pages.view_resource"))
print(reviewer.has_perm("pages.change_resource"))
print(reviewer.is_staff)
~~~

Valores esperados: `True`, `False`, `False`. Si usaste otro nombre de usuario, sustituye `reader_demo` por una cuenta existente.

**¿Por qué volver a recuperar la instancia?** Django puede conservar resultados de permisos en caché en un objeto `User`; tras cambiar grupos o permisos conviene comprobarlos con una instancia nueva.

El grupo no concede `is_staff` y no debe hacerlo: el acceso al administrador es una responsabilidad diferente.

### 5. Crear una vista restringida

En `pages/views.py` añade el import:

~~~python
from django.contrib.auth.decorators import login_required, permission_required
~~~

Agrega:

~~~python
@login_required
@permission_required("pages.view_resource", raise_exception=True)
def resource_review(request):
    context = {
        "resources": Resource.objects.select_related("category").all(),
    }
    return render(request, "pages/review.html", context)
~~~

El orden de los decoradores permite **redirigir a login a usuarios anónimos** y devolver **HTTP 403 a cuentas autenticadas que carecen del permiso**. La vista solo lee recursos; no modifica la base de datos.

### 6. Crear la plantilla de revisión

Crea `pages/templates/pages/review.html`:

~~~html
{% extends "pages/base.html" %}

{% block title %}Catalog review | ClassroomHub{% endblock %}

{% block content %}
    <h2>Catalog review</h2>
    <p>This page is available to authorized reviewers.</p>

    {% for resource in resources %}
        <article class="resource">
            <h3>{{ resource.title }}</h3>
            <p>Category: {{ resource.category.name }}</p>
            <p>Status:
                {% if resource.available %}Available{% else %}Coming soon{% endif %}
            </p>
        </article>
    {% empty %}
        <p>No resources are available.</p>
    {% endfor %}
{% endblock %}
~~~

### 7. Crear la ruta y el enlace

En `pages/urls.py` agrega:

~~~python
path(
    "resources/review/",
    views.resource_review,
    name="resource-review",
),
~~~

En la sección del menú que se muestra a usuarios autenticados, agrega:

~~~html
{% if perms.pages.view_resource %}
    <a href="{% url 'pages:resource-review' %}">Review</a>
{% endif %}
~~~

Ese enlace mejora la navegación, pero **no es el control de seguridad**. El servidor debe aplicar `@permission_required` incluso si alguien escribe la URL directamente.

### 8. Comprobar las tres situaciones

1. **Anónimo:** visita `/resources/review/`. Debe ser redirigido al login.
2. **Cuenta normal sin permiso:** inicia sesión y visita la ruta manualmente. Debe recibir HTTP `403`.
3. **Miembro de Catalog reviewers:** inicia sesión con una cuenta autorizada. Debe obtener HTTP `200` y visualizar el catálogo.

En todos los casos, `/resources/` continúa pública.

### Laboratorio de errores

**Error 1 — El revisor recibe 403.** Comprueba el grupo, su permiso `pages.view_resource` y la identidad utilizada en la sesión. Verifica que la cuenta esté activa y que los permisos no se consulten desde una instancia obsoleta.

**Error 2 — Cualquier cuenta entra en la revisión.** Comprueba que la vista realmente tiene `@permission_required`. `@login_required` no basta.

**Error 3 — El enlace se oculta, pero la vista permite acceso.** La plantilla no sustituye el control del servidor. Nunca uses únicamente una condición HTML para autorizar.

**Error 4 — El revisor no puede entrar en Django Admin.** Es el comportamiento esperado: no posee `is_staff`, ni es necesario concedérselo.

### Reto — Comparar roles

Consulta los permisos `pages.view_resource` y `pages.change_resource` de una cuenta común y de una cuenta del grupo de revisores. Explica por qué solo el segundo puede ver la página de revisión y por qué ninguno recibe autorización de edición pública por ese hecho.

### Comprobación

Puedes demostrar con solicitudes reales la diferencia entre un visitante anónimo, una cuenta autenticada sin permisos y una cuenta con autorización.

---

## Práctica 8.6 — Reto integrador y pruebas automatizadas

### Objetivo

Conservar el catálogo persistente de ClassroomHub y completar el flujo de registro, login, logout, panel privado y revisión del catálogo, con pruebas que detecten fallos de autenticación y autorización.

### 1. Requisitos del proyecto

| Código | Requisito | Comprobación |
|---|---|---|
| RF01 | Registro público de cuentas normales | El usuario se crea sin privilegios |
| RF02 | Validación del formulario de registro | Se rechazan contraseñas distintas o usuarios repetidos |
| RF03 | Login con herramientas Django | Credenciales válidas crean sesión |
| RF04 | Logout mediante POST | La sesión deja de estar autenticada |
| RF05 | Catálogo público | `/resources/` funciona sin sesión |
| RF06 | Panel privado | `/dashboard/` exige autenticación |
| RF07 | Sesión en funcionamiento | El contador de visitas cambia |
| RF08 | Grupo de revisión | Permiso `pages.view_resource` asignado correctamente |
| RF09 | Control de acceso en el servidor | Un usuario sin permiso obtiene 403 |
| RF10 | Compatibilidad con MySQL/MariaDB | Se conserva la configuración del Laboratorio 7 |
| RF11 | Pruebas aisladas | El ejecutor utiliza la base `test_classroom_hub` |

### 2. Verificar el archivo completo `pages/views.py`

Al terminar las prácticas, el archivo debe tener este contenido funcional:

~~~python
from django.contrib.auth.decorators import login_required, permission_required
from django.contrib.auth.forms import UserCreationForm
from django.shortcuts import redirect, render

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


def signup(request):
    if request.user.is_authenticated:
        return redirect("pages:dashboard")

    if request.method == "POST":
        form = UserCreationForm(request.POST)
        if form.is_valid():
            form.save()
            return redirect("pages:login")
    else:
        form = UserCreationForm()

    return render(request, "pages/signup.html", {"form": form})


@login_required
def dashboard(request):
    visit_count = request.session.get("dashboard_visits", 0) + 1
    request.session["dashboard_visits"] = visit_count

    return render(
        request,
        "pages/dashboard.html",
        {"visit_count": visit_count},
    )


@login_required
@permission_required("pages.view_resource", raise_exception=True)
def resource_review(request):
    context = {
        "resources": Resource.objects.select_related("category").all(),
    }
    return render(request, "pages/review.html", context)
~~~

No introduzcas `authenticate()` ni `login()` manuales si las vistas integradas satisfacen el caso. Mantener las responsabilidades separadas evita código innecesario y facilita las pruebas.

### 3. Verificar `pages/urls.py` completo

~~~python
from django.contrib.auth import views as auth_views
from django.urls import path

from . import views

app_name = "pages"

urlpatterns = [
    path("", views.home, name="home"),
    path("resources/", views.resources, name="resources"),
    path(
        "resources/review/",
        views.resource_review,
        name="resource-review",
    ),
    path("about/", views.about, name="about"),
    path("accounts/signup/", views.signup, name="signup"),
    path(
        "accounts/login/",
        auth_views.LoginView.as_view(template_name="pages/login.html"),
        name="login",
    ),
    path(
        "accounts/logout/",
        auth_views.LogoutView.as_view(),
        name="logout",
    ),
    path("dashboard/", views.dashboard, name="dashboard"),
]
~~~

No cambies `config/urls.py` si conserva la llamada `include("pages.urls")` del Laboratorio 6.

### 4. Lista de comprobación manual

| Prueba | Acción | Resultado |
|---|---|---|
| P01 | Abrir catálogo sin iniciar sesión | 200 |
| P02 | Abrir registro | Formulario |
| P03 | Registrar cuenta normal | Redirección a login |
| P04 | Usar dos contraseñas distintas | Se rechaza el registro |
| P05 | Iniciar sesión con contraseña incorrecta | Se muestra error sin iniciar sesión |
| P06 | Iniciar sesión con credenciales válidas | Redirección al panel |
| P07 | Abrir panel sin autenticación | Redirección a login |
| P08 | Visitar dos veces el panel | Contador 1, después 2 |
| P09 | Abrir revisión como cuenta normal | 403 |
| P10 | Abrir revisión como revisor | 200 |
| P11 | Abrir logout por GET | 405 |
| P12 | Enviar logout por POST | Redirección y sesión invalidada |
| P13 | Abrir panel después de salir | Redirección a login |
| P14 | Consultar catálogo tras reiniciar | Los recursos persisten en la base |

### 5. Conservar las pruebas anteriores

El `pages/tests.py` del Laboratorio 7 contiene **diez pruebas** de modelos, relaciones y páginas. **No las elimines**: agrega pruebas nuevas al mismo archivo.

Incorpora estas importaciones si faltan, manteniendo `TestCase`, `reverse`, `Category`, `Resource` y `ProtectedError` ya utilizados:

~~~python
from django.contrib.auth import get_user_model
from django.contrib.auth.models import Group, Permission
from django.contrib.contenttypes.models import ContentType
from django.test import Client
~~~

No declares imports duplicados si ya existen.

### 6. Pruebas de autenticación

Agrega **al final de `pages/tests.py`**:

~~~python
class AuthenticationTests(TestCase):
    def setUp(self):
        self.password = "ExampleTestPassword!2026"
        self.user = get_user_model().objects.create_user(
            username="reader",
            password=self.password,
        )

    def test_signup_page_renders(self):
        response = self.client.get(reverse("pages:signup"))

        self.assertEqual(response.status_code, 200)
        self.assertTemplateUsed(response, "pages/signup.html")

    def test_signup_creates_regular_user(self):
        response = self.client.post(
            reverse("pages:signup"),
            {
                "username": "new_reader",
                "password1": "NewExamplePassword!2026",
                "password2": "NewExamplePassword!2026",
            },
        )

        self.assertRedirects(response, reverse("pages:login"))
        account = get_user_model().objects.get(username="new_reader")
        self.assertFalse(account.is_staff)
        self.assertFalse(account.is_superuser)
        self.assertTrue(account.check_password("NewExamplePassword!2026"))

    def test_signup_rejects_mismatched_passwords(self):
        response = self.client.post(
            reverse("pages:signup"),
            {
                "username": "invalid_reader",
                "password1": "ExamplePassword!2026",
                "password2": "DifferentPassword!2026",
            },
        )

        self.assertEqual(response.status_code, 200)
        self.assertFalse(
            get_user_model().objects.filter(
                username="invalid_reader"
            ).exists()
        )

    def test_signup_rejects_existing_username(self):
        response = self.client.post(
            reverse("pages:signup"),
            {
                "username": "reader",
                "password1": "ExamplePassword!2026",
                "password2": "ExamplePassword!2026",
            },
        )

        self.assertEqual(response.status_code, 200)
        self.assertEqual(
            get_user_model().objects.filter(username="reader").count(),
            1,
        )

    def test_login_rejects_wrong_password(self):
        response = self.client.post(
            reverse("pages:login"),
            {"username": "reader", "password": "wrong"},
        )

        self.assertEqual(response.status_code, 200)
        self.assertNotIn("_auth_user_id", self.client.session)

    def test_login_redirects_to_dashboard(self):
        response = self.client.post(
            reverse("pages:login"),
            {"username": "reader", "password": self.password},
        )

        self.assertRedirects(response, reverse("pages:dashboard"))
        self.assertIn("_auth_user_id", self.client.session)

    def test_login_respects_safe_next(self):
        destination = reverse("pages:dashboard")
        response = self.client.post(
            reverse("pages:login") + f"?next={destination}",
            {
                "username": "reader",
                "password": self.password,
                "next": destination,
            },
        )

        self.assertRedirects(response, destination)

    def test_anonymous_dashboard_redirects_to_login(self):
        response = self.client.get(reverse("pages:dashboard"))

        self.assertRedirects(
            response,
            reverse("pages:login") + "?next=/dashboard/",
        )

    def test_dashboard_session_counter_increments(self):
        self.client.force_login(self.user)

        first = self.client.get(reverse("pages:dashboard"))
        second = self.client.get(reverse("pages:dashboard"))

        self.assertEqual(first.context["visit_count"], 1)
        self.assertEqual(second.context["visit_count"], 2)

    def test_logout_get_is_not_allowed(self):
        self.client.force_login(self.user)

        response = self.client.get(reverse("pages:logout"))

        self.assertEqual(response.status_code, 405)
        self.assertIn("_auth_user_id", self.client.session)

    def test_logout_post_ends_session(self):
        self.client.force_login(self.user)

        response = self.client.post(reverse("pages:logout"))

        self.assertRedirects(response, reverse("pages:home"))
        self.assertNotIn("_auth_user_id", self.client.session)
        protected = self.client.get(reverse("pages:dashboard"))
        self.assertEqual(protected.status_code, 302)

    def test_login_post_without_csrf_is_rejected(self):
        csrf_client = Client(enforce_csrf_checks=True)
        csrf_client.get(reverse("pages:login"))

        response = csrf_client.post(
            reverse("pages:login"),
            {"username": "reader", "password": self.password},
        )

        self.assertEqual(response.status_code, 403)
~~~

Las contraseñas del bloque son **datos ficticios de pruebas**, nunca credenciales reales. La prueba de CSRF utiliza un cliente configurado expresamente para verificar el middleware; el cliente de pruebas ordinario no lo impone por defecto.

### 7. Pruebas de permisos y grupos

Agrega también:

~~~python
class ResourceReviewPermissionTests(TestCase):
    def setUp(self):
        self.user = get_user_model().objects.create_user(
            username="review_candidate",
            password="ExampleTestPassword!2026",
        )
        category = Category.objects.create(name="Programming")
        Resource.objects.create(
            title="Python basics",
            category=category,
        )

        content_type = ContentType.objects.get_for_model(Resource)
        permission = Permission.objects.get(
            content_type=content_type,
            codename="view_resource",
        )

        self.reviewers = Group.objects.create(
            name="Catalog reviewers",
        )
        self.reviewers.permissions.add(permission)

    def test_anonymous_review_redirects_to_login(self):
        response = self.client.get(reverse("pages:resource-review"))

        self.assertEqual(response.status_code, 302)
        self.assertIn(reverse("pages:login"), response.url)

    def test_regular_user_cannot_open_review(self):
        self.client.force_login(self.user)

        response = self.client.get(reverse("pages:resource-review"))

        self.assertEqual(response.status_code, 403)

    def test_reviewer_can_open_review(self):
        self.user.groups.add(self.reviewers)
        self.client.force_login(self.user)

        response = self.client.get(reverse("pages:resource-review"))

        self.assertEqual(response.status_code, 200)
        self.assertContains(response, "Python basics")
        self.assertTemplateUsed(response, "pages/review.html")

    def test_reviewer_does_not_become_staff(self):
        self.user.groups.add(self.reviewers)
        self.user.refresh_from_db()

        self.assertFalse(self.user.is_staff)
        self.assertFalse(self.user.is_superuser)

    def test_public_catalog_remains_available(self):
        response = self.client.get(reverse("pages:resources"))

        self.assertEqual(response.status_code, 200)
        self.assertContains(response, "Python basics")

    def test_typing_review_url_cannot_bypass_permission(self):
        self.client.force_login(self.user)

        response = self.client.get("/resources/review/")

        self.assertEqual(response.status_code, 403)
~~~

Ahora existen **18 pruebas nuevas** además de las diez del Laboratorio 7: el total esperado, si mantuviste las anteriores, es **28**.

### 8. Ejecutar pruebas con MySQL o MariaDB

En el Laboratorio 7 configuraste:

~~~text
Development database: classroom_hub
Test database:        test_classroom_hub
~~~

El ejecutor de Django crea y destruye la base de pruebas. **Reserva `test_classroom_hub` para datos descartables y no lo reemplaces por `classroom_hub`**. La cuenta SQL debe tener los permisos necesarios sobre el esquema de pruebas, sin privilegios globales indiscriminados.

Ejecuta:

~~~bash
python manage.py check
python manage.py makemigrations --check --dry-run
python manage.py test pages
~~~

**Resultado esperado:** veintiocho pruebas exitosas si no agregaste ni eliminaste otros tests. Verifica también que los registros de desarrollo siguen presentes en `classroom_hub`.

No afirmes que todo es seguro porque los tests pasan. Estas pruebas cubren los flujos concretos implementados, no una auditoría integral de seguridad.

### 9. Depuración de integración

**Caso A — `NoReverseMatch` tras iniciar sesión.** Verifica `LOGIN_REDIRECT_URL` y la ruta `pages:dashboard`.

**Caso B — Todos pueden entrar a la revisión.** Revisa el decorador `@permission_required`. Ocultar el enlace del menú no es suficiente.

**Caso C — Un revisor recibe 403.** Revisa su grupo, el permiso `pages.view_resource`, la cuenta activa y posibles resultados en caché de una instancia antigua de usuario.

**Caso D — Logout responde 405.** Estás utilizando GET en lugar del formulario POST.

**Caso E — Se muestra un error de CSRF.** Comprueba `{% csrf_token %}`, el envío de cookies y el middleware; no deshabilites la protección.

**Caso F — No puedes crear la base de pruebas.** Revisa los permisos SQL sobre `test_classroom_hub`, no sobre una base con datos importantes.

### 10. Reto adicional — Resumen privado

Crea una vista de **solo lectura** `/dashboard/summary/` que muestre el número total de recursos y el número de recursos disponibles mediante el ORM. Protégela únicamente con `@login_required`, sin exigir el permiso `view_resource`.

Agrega pruebas para el visitante anónimo y el usuario autenticado. Esta ampliación no requiere crear formularios de edición ni otro modelo.

### 11. Preguntas para explicar tu solución

1. ¿Qué diferencia hay entre autenticación y autorización?
2. ¿Por qué las personas no utilizan la cuenta SQL para iniciar sesión?
3. ¿Qué hace `UserCreationForm` y qué no debe permitir?
4. ¿Por qué `set_password()` es diferente de escribir una contraseña en un campo?
5. ¿Qué diferencia hay entre una cookie de sesión y `django_session`?
6. ¿Por qué `LoginView` usa POST para recibir credenciales?
7. ¿Por qué `LogoutView` no se invoca con GET?
8. ¿Qué función cumple `{% csrf_token %}`?
9. ¿Cómo utiliza `login_required` el parámetro `next`?
10. ¿Por qué `is_staff` y `has_perm()` son conceptos diferentes?
11. ¿Qué demuestra un HTTP 403 frente a una redirección al login?
12. ¿Por qué el permiso debe comprobarse en la vista y no solo en la plantilla?
13. ¿Por qué las pruebas necesitan una base diferente?
14. ¿Qué falta para convertir el catálogo en un CRUD público autorizado?

### 12. Revisión antes de compartir

- [ ] El proyecto conserva MySQL o MariaDB.
- [ ] Las migraciones de `auth` y `sessions` están aplicadas.
- [ ] El formulario de registro valida y crea cuentas sin privilegios.
- [ ] `LoginView` autentica credenciales válidas.
- [ ] `LogoutView` utiliza POST con CSRF.
- [ ] `/dashboard/` exige autenticación.
- [ ] El contador se conserva durante la sesión y desaparece después del logout.
- [ ] `Catalog reviewers` tiene el permiso `pages.view_resource`.
- [ ] `/resources/review/` devuelve 403 a cuentas sin permiso.
- [ ] El catálogo `/resources/` permanece público.
- [ ] Se conservan las diez pruebas del Laboratorio 7.
- [ ] Se añadieron las dieciocho pruebas del Laboratorio 8.
- [ ] Las pruebas se ejecutan contra la base descartable.
- [ ] `.env` y las credenciales no se incluyen en Git.
- [ ] El README explica rutas y creación de cuentas/grupos.

### 13. Documentación y Git

Actualiza el `README.md` de tu proyecto con las rutas `/accounts/signup/`, `/accounts/login/`, `/accounts/logout/`, `/dashboard/` y `/resources/review/`. Explica cómo preparar un grupo revisor mediante Django Admin, ejecutar migraciones y pruebas, y mantener las credenciales fuera del repositorio.

Ejemplos de mensajes de commit, únicamente si describen cambios que realmente realizaste:

~~~text
feat: add account signup form
feat: integrate login and POST logout
feat: protect dashboard with sessions
feat: restrict catalog review by permission
test: cover account and access-control flows
docs: explain account setup and testing
~~~

No compartas usuarios reales, contraseñas, cookies de sesión ni exportaciones SQL con datos privados.

---

## Cierre del laboratorio

ClassroomHub ahora permite registrar cuentas comunes, iniciar y cerrar sesiones, visitar un panel privado y acceder a una vista de revisión únicamente cuando una cuenta tiene el permiso adecuado. Estas funcionalidades reutilizan el sistema de autenticación de Django y la base MySQL o MariaDB preparada en el laboratorio anterior.

El aprendizaje central es que **estar autenticado no equivale a estar autorizado**. El siguiente laboratorio podrá incorporar **formularios, `ModelForm`, vistas de creación, edición y eliminación**, utilizando los modelos, las rutas y los permisos que ya has aprendido a manejar.

### Referencias oficiales

- [Django 6.1 — Autenticación, usuarios y permisos](https://docs.djangoproject.com/en/6.1/topics/auth/default/)
- [Django 6.1 — Sesiones](https://docs.djangoproject.com/en/6.1/topics/http/sessions/)
- [Django 6.1 — Protección CSRF](https://docs.djangoproject.com/en/6.1/howto/csrf/)
- [Django 6.1 — Contraseñas](https://docs.djangoproject.com/en/6.1/topics/auth/passwords/)
- [Django 6.1 — Pruebas](https://docs.djangoproject.com/en/6.1/topics/testing/overview/)
- [Django 6.1 — Seguridad](https://docs.djangoproject.com/en/6.1/topics/security/)
- [Django 5.2 — Autenticación](https://docs.djangoproject.com/en/5.2/topics/auth/default/)
