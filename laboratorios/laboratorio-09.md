# Laboratorio 9 — Formularios y CRUD con Django: vistas, ModelForm y permisos

En el [Laboratorio 8](laboratorio-08.md) agregaste autenticación, sesiones y permisos a **ClassroomHub**. Los visitantes pueden consultar el catálogo de recursos; las cuentas autenticadas tienen un panel privado; y el grupo `Catalog reviewers` puede acceder a una vista de revisión de solo lectura.

En este laboratorio vas a incorporar **operaciones de creación, consulta, actualización y eliminación (CRUD)** desde las páginas de la aplicación. Hasta ahora, para mantener los recursos había que utilizar Django Admin. Ahora las personas autorizadas podrán crear, editar y eliminar recursos desde una interfaz propia de ClassroomHub, mientras que cualquier visitante podrá seguir consultando el catálogo.

Aprenderás a procesar formularios con Django, crear un `ModelForm`, utilizar vistas genéricas basadas en clases y proteger **cada operación** con permisos. Conservarás los modelos `Category` y `Resource`, las migraciones, las sesiones y la conexión a **MySQL o MariaDB**.

| Práctica | Tema | Resultado |
|---|---|---|
| 9.1 | Formularios, GET, POST, validación y CSRF | Comprendes cómo se recibe y valida información |
| 9.2 | `ModelForm` y validaciones | Construyes un formulario reutilizable para `Resource` |
| 9.3 | Consultar y crear recursos | Implementas detalle público y creación protegida |
| 9.4 | Actualizar recursos | Editas registros existentes sin duplicar formularios |
| 9.5 | Eliminar recursos | Confirmas la eliminación mediante POST y compruebas permisos |
| 9.6 | Reto integrador y pruebas | Completa el CRUD y verifica comportamientos normales y de error |

> **Cómo trabajar.** Revisa el objetivo de cada práctica, predice qué ocurrirá, ejecuta los ejemplos y modifica datos de prueba. Los errores intencionales deben corregirse al terminar cada experimento. Cuando se indique «reemplaza el archivo completo», conserva una copia de tu versión anterior en Git antes de sustituirla.

## Preparación y alcance

### Proyecto que vas a continuar

Mantén la aplicación `classroom-hub` de los laboratorios 6, 7 y 8. Comprueba:

- `config/settings.py` todavía utiliza `django.db.backends.mysql` para MySQL o MariaDB;
- `pages/models.py` contiene `Category` y `Resource`;
- la migración que añadió `Resource.resource_type` está aplicada;
- `pages/views.py` conserva las páginas públicas, el registro, el panel privado y la revisión;
- `pages/urls.py` conserva las rutas públicas y de autenticación;
- `pages/base.html` incluye el menú que responde a la sesión;
- `pages/tests.py` conserva las pruebas de los laboratorios anteriores.

**No vuelvas a ejecutar** `startproject` ni `startapp`. No cambies la base a SQLite ni borres las tablas para iniciar el CRUD.

La referencia técnica es **Django 6.1.3**. Si continúas con una actualización compatible de **Django 5.2 LTS**, conserva esa versión y consulta su documentación. Los componentes de formularios y vistas genéricas utilizados aquí están disponibles en ambas ramas.

Desde la raíz del proyecto ejecuta:

~~~bash
python -m django --version
python manage.py check
python manage.py showmigrations pages
~~~

Si hay migraciones pendientes, revisa qué modifican antes de ejecutar `python manage.py migrate`. El CRUD utilizará los modelos existentes: **no necesita crear un modelo nuevo**.

### Alcance funcional y control de acceso

Mantendremos tres niveles:

| Operación | Visitante anónimo | Cuenta común | Catalog reviewers | Catalog editors |
|---|---|---|---|---|
| Ver lista y detalle de recursos | Sí | Sí | Sí | Sí |
| Entrar al panel personal | No | Sí | Sí | Sí |
| Abrir revisión del catálogo | No | No | Sí | Sí |
| Crear un recurso | No | No | No | Sí |
| Editar un recurso | No | No | No | Sí |
| Eliminar un recurso | No | No | No | Sí |

Crearemos el grupo **Catalog editors**, separado de **Catalog reviewers**. El formulario público de registro del Laboratorio 8 seguirá creando cuentas **sin privilegios**. Los permisos de edición se asignarán únicamente por un administrador autorizado.

No agregaremos todavía CRUD de categorías en páginas públicas; las categorías seguirán gestionándose en Django Admin. Tampoco abordaremos cargas de archivos, interfaces JavaScript, APIs REST, paginación, filtros avanzados ni despliegue. Son temas posteriores.

---

## Práctica 9.1 — Formularios, GET, POST y validación

### Objetivos

Al finalizar podrás:

- reconocer qué ocurre cuando se solicita un formulario con GET y se envía con POST;
- explicar el papel de `request.POST`, `is_valid()` y `cleaned_data`;
- diferenciar un formulario de Django de un formulario HTML aislado;
- identificar por qué se utiliza `{% csrf_token %}`;
- comprender el patrón POST → redirección → GET.

### 1. ¿Qué cambia al pasar de consultar a modificar?

En los laboratorios anteriores la mayoría de las páginas respondían a solicitudes **GET**. Para añadir información necesitas recibir datos enviados por el navegador y procesarlos.

Una interacción típica es:

~~~text
Browser                       Django
   | GET /resources/new/          |
   |----------------------------->|
   |<-----------------------------|
   | HTML with a form             |
   |                              |
   | POST /resources/new/         |
   | title=... category=...       |
   |----------------------------->|
   |                              | Validate fields and permissions
   |                              | Save using the ORM if valid
   |<-----------------------------|
   | HTTP 302 to /resources/      |
   |                              |
   | GET /resources/              |
   |----------------------------->|
   |<-----------------------------|
   | Updated resource list        |
~~~

| Método | Propósito en este laboratorio |
|---|---|
| `GET` | Consultar páginas y mostrar formularios sin modificar registros |
| `POST` | Enviar información para crear, modificar o eliminar un registro |

Un enlace de navegación no debe eliminar datos simplemente porque una persona lo visite. Las acciones que cambian el estado requieren métodos apropiados y validación en el servidor.

### 2. Probar un formulario de Django en el shell

Django permite construir formularios sin asociarlos todavía a un modelo. Abre:

~~~bash
python manage.py shell
~~~

Ejecuta:

~~~python
from django import forms


class ResourceSearchForm(forms.Form):
    query = forms.CharField(max_length=80)


valid_form = ResourceSearchForm({"query": "Django"})
print(valid_form.is_valid())
print(valid_form.cleaned_data)

invalid_form = ResourceSearchForm({"query": ""})
print(invalid_form.is_valid())
print(invalid_form.errors)
~~~

Resultados esperados:

- `valid_form.is_valid()` devuelve `True` y deja `{"query": "Django"}` en `cleaned_data`.
- `invalid_form.is_valid()` devuelve `False` y registra el error de campo obligatorio.

El ejemplo no consulta ni modifica la base. Sirve para entender **validación y normalización** antes de añadir persistencia.

### 3. Formularios sin datos y con datos

Un formulario puede estar:

- **Sin datos asociados (unbound):** se presenta inicialmente para que una persona lo complete.
- **Con datos asociados (bound):** se creó con información enviada o proporcionada, y puede validarse.
- **Válido:** los campos cumplen las reglas y puedes consultar `cleaned_data`.
- **Inválido:** contiene errores que deben mostrarse sin guardar el objeto.

**Importante:** nunca confíes únicamente en el atributo HTML `required` o en una validación de JavaScript. El navegador puede omitir esas verificaciones; **Django debe validar del lado del servidor**.

### 4. Comprender el token CSRF

Un formulario que modifica datos debería incluir:

~~~html
<form method="post">
    {% csrf_token %}
    <button type="submit">Submit</button>
</form>
~~~

Django utiliza el token CSRF junto con sus mecanismos de protección para reducir el riesgo de que otro sitio provoque una solicitud de modificación no deseada utilizando la sesión del navegador.

En `config/settings.py` debes conservar `django.middleware.csrf.CsrfViewMiddleware`. **No utilices `csrf_exempt` como solución a un formulario que devuelve 403**; comprueba primero el token y las cookies.

### 5. ¿Por qué redirigir después de guardar?

Si después de un POST correcto devuelves directamente una página de confirmación desde la misma solicitud, recargarla podría volver a enviar los datos. El patrón **Post/Redirect/Get (PRG)** responde al POST exitoso con una redirección y presenta después una página mediante GET.

En Django, `CreateView` y `UpdateView` pueden aplicar este patrón utilizando `success_url`. En nuestro caso redirigiremos a `pages:resources`.

No confundas:

- **Formulario inválido:** se vuelve a mostrar el formulario con errores, sin guardar.
- **Formulario válido:** se guarda el registro y se redirige.
- **Usuario no autorizado:** no se procesa la operación, aunque haya enviado datos correctos.

### Antes de ejecutar — Predice

1. ¿Qué método debería utilizarse para mostrar inicialmente el formulario?
2. ¿Qué debería ocurrir al enviar una categoría inexistente?
3. ¿Por qué no debes crear un registro aunque `form.is_valid()` devuelva `False`?
4. ¿Qué ventaja ofrece redirigir después de un POST correcto?
5. ¿Por qué ocultar un botón no impide que alguien envíe una solicitud POST manual?

### Ejercicio — Identifica el flujo

Explica con tus palabras el orden de las comprobaciones cuando alguien envía una solicitud para crear un recurso:

~~~text
Permission -> Form validation -> Model save -> Redirect
~~~

¿Qué debe suceder si falla alguna de las dos primeras comprobaciones?

### Comprobación

Sabes distinguir GET y POST, validar un formulario, interpretar errores y justificar el uso de CSRF y PRG.

---

## Práctica 9.2 — Construir un `ModelForm` para `Resource`

### Objetivos

Al finalizar podrás:

- diferenciar `forms.Form` de `forms.ModelForm`;
- generar campos de formulario a partir de un modelo;
- controlar explícitamente qué campos puede modificar una persona;
- agregar una regla de validación;
- explicar por qué un formulario no sustituye las restricciones del modelo o de la base.

### 1. Recuperar el modelo existente

El Laboratorio 7 dejó un modelo `Resource` con esta estructura esencial:

~~~python
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
    resource_type = models.CharField(max_length=40, blank=True)
~~~

Este fragmento es un **recordatorio de campos**, no un reemplazo completo de `pages/models.py`. Conserva también `Category`, `Meta`, `__str__` y las migraciones creadas.

La tabla de `Resource` ya existe en MySQL/MariaDB. No necesitas añadir otro modelo para construir un formulario de creación.

### 2. Crear `pages/forms.py`

Añade un archivo nuevo llamado `pages/forms.py`:

~~~python
from django import forms

from .models import Resource


class ResourceForm(forms.ModelForm):
    class Meta:
        model = Resource
        fields = [
            "title",
            "description",
            "resource_type",
            "category",
            "available",
        ]
        widgets = {
            "description": forms.Textarea(attrs={"rows": 4}),
        }

    def clean_title(self):
        title = self.cleaned_data["title"].strip()

        if len(title) < 3:
            raise forms.ValidationError(
                "Title must contain at least 3 characters."
            )

        return title
~~~

### 3. Entender `Meta` y los campos permitidos

| Instrucción | Función |
|---|---|
| `model = Resource` | Indica qué modelo utilizar |
| `fields = [...]` | Define los campos editables |
| `widgets = {...}` | Personaliza la representación HTML de un campo |
| `clean_title()` | Valida y normaliza un título |
| `return title` | Entrega el valor ya validado para guardar |

**No utilices `fields = "__all__"` por comodidad en una interfaz que evoluciona.** Una lista explícita evita exponer accidentalmente campos internos cuando más adelante amplíes el modelo.

No incluimos `id` ni `created_at` porque una persona no debe asignar identificadores ni modificar manualmente la fecha de creación.

El campo `category` se mostrará como una selección de categorías existentes. Si no hay categorías en la base, crea primero algunas desde Django Admin. **No permitirás crear una categoría arbitraria escribiendo un ID en el navegador.**

### 4. ¿Qué valida `ModelForm` automáticamente?

Django utiliza información del modelo para validar tipos, campos obligatorios, longitudes y claves foráneas. Por ejemplo:

- Un título vacío no cumple el campo requerido.
- Un título de más de 150 caracteres excede la longitud definida.
- Una categoría inexistente no corresponde a una opción válida.
- `available` se convierte a un valor booleano.
- `clean_title()` agrega una regla didáctica: mínimo tres caracteres.

**Nota de arquitectura:** esta longitud mínima está validada en el formulario, no en toda la capa de dominio. Una inserción directa con el ORM no utiliza automáticamente esta regla de `ResourceForm`. Si esa regla fuera un invariante obligatorio para todos los puntos de entrada, habría que definir y comprobar una estrategia de validación en el modelo y los demás límites del sistema. En este laboratorio sirve para aprender las validaciones propias de un formulario.

### 5. Experimentar con el formulario en el shell

~~~bash
python manage.py shell
~~~

Ejecuta:

~~~python
from pages.forms import ResourceForm
from pages.models import Category

category = Category.objects.first()
print(category)
~~~

Si el resultado es `None`, registra antes una categoría desde Admin. Con una categoría existente:

~~~python
form = ResourceForm(
    data={
        "title": "Web testing",
        "description": "Introduction to automated tests",
        "resource_type": "Guide",
        "category": category.pk,
        "available": "on",
    }
)

print(form.is_valid())
print(form.errors)
~~~

Deberías obtener `True` y ningún error, siempre que el registro de categoría exista. **Validar el formulario no crea el recurso**: solo se guarda cuando el código llama a `save()`.

Ahora prueba un título demasiado corto:

~~~python
invalid_form = ResourceForm(
    data={
        "title": "ab",
        "category": category.pk,
        "available": "on",
    }
)

print(invalid_form.is_valid())
print(invalid_form.errors)
~~~

El resultado debe ser `False` y contener el mensaje de la validación del título.

### 6. Formulario para crear y formulario para editar

El mismo `ResourceForm` sirve para ambas operaciones:

~~~python
create_form = ResourceForm()

existing_resource = Resource.objects.first()
if existing_resource is not None:
    update_form = ResourceForm(instance=existing_resource)
~~~

En creación no hay instancia previa. En actualización, `instance=...` indica el registro que se está editando. No necesitamos dos clases de formulario casi idénticas.

### Laboratorio de errores

**Error 1 — El formulario no encuentra una categoría.** Comprueba si existen categorías y si el valor enviado coincide con una PK real.

**Error 2 — El campo `resource_type` no existe.** Verifica que completaste la segunda migración del Laboratorio 7 y que `Resource` mantiene ese campo. No lo elimines del formulario solo para ocultar la inconsistencia.

**Error 3 — El título corto se acepta.** Comprueba el nombre `clean_title` y que devuelve el valor normalizado. No escribas la validación en una vista y otra distinta en el formulario.

**Error 4 — Se aceptan campos adicionales enviados por POST.** `ModelForm` solo vincula los campos definidos; enviar claves como `is_staff` o `created_at` no les concede significado ni permisos. La protección de la vista sigue siendo necesaria.

### Reto — Comparar `Form` y `ModelForm`

Explica qué partes de `ResourceForm` tendrías que escribir manualmente si utilizaras `forms.Form`. Argumenta por qué `ModelForm` evita duplicar la definición de campos que ya existe en `Resource`.

### Comprobación

`pages/forms.py` contiene un único formulario reutilizable, con campos explícitos y validación de título.

---

## Práctica 9.3 — Consultar y crear recursos

### Objetivos

Al finalizar podrás:

- crear una vista de detalle sin modificar la lista pública;
- utilizar `DetailView` y `CreateView`;
- configurar una ruta con identificador `pk`;
- limitar la creación a cuentas con un permiso específico;
- reutilizar una plantilla común de formulario.

### 1. ¿Qué son las vistas genéricas?

Django ofrece vistas basadas en clases para operaciones frecuentes. Las vistas del Laboratorio 8 se implementaron como funciones, lo cual es adecuado para flujos específicos; ahora queremos aprovechar comportamientos de formulario ya disponibles.

| Vista genérica | Responsabilidad |
|---|---|
| `DetailView` | Mostrar un objeto |
| `CreateView` | Mostrar formulario y crear un objeto |
| `UpdateView` | Mostrar formulario y modificar un objeto |
| `DeleteView` | Mostrar confirmación y eliminar mediante POST |

No necesitas reemplazar todas las funciones por clases. **Utiliza la herramienta que corresponda a cada responsabilidad.**

### 2. Importar las herramientas

En `pages/views.py` agrega:

~~~python
from django.contrib.auth.mixins import (
    LoginRequiredMixin,
    PermissionRequiredMixin,
)
from django.urls import reverse_lazy
from django.views.generic import (
    CreateView,
    DeleteView,
    DetailView,
    UpdateView,
)

from .forms import ResourceForm
~~~

Estos imports se integran con los ya existentes (`UserCreationForm`, `render`, `Resource` y los decoradores de seguridad). **No borres las funciones del Laboratorio 8.**

### 3. Crear una vista de detalle público

Agrega:

~~~python
class ResourceDetailView(DetailView):
    model = Resource
    template_name = "pages/resource_detail.html"
    context_object_name = "resource"
~~~

La vista obtiene un objeto a partir de su clave primaria. Cuando no existe, Django responde con **404**; no tienes que escribir una búsqueda manual que silencie los errores.

Crea `pages/templates/pages/resource_detail.html`:

~~~html
{% extends "pages/base.html" %}

{% block title %}{{ resource.title }} | ClassroomHub{% endblock %}

{% block content %}
    <article>
        <h2>{{ resource.title }}</h2>
        <p>{{ resource.description }}</p>
        <p>Category: {{ resource.category.name }}</p>
        <p>Type: {{ resource.resource_type|default:"Not specified" }}</p>

        {% if resource.available %}
            <p>Available</p>
        {% else %}
            <p>Coming soon</p>
        {% endif %}
    </article>

    {% if perms.pages.change_resource %}
        <a href="{% url 'pages:resource-update' resource.pk %}">
            Edit resource
        </a>
    {% endif %}

    {% if perms.pages.delete_resource %}
        <a href="{% url 'pages:resource-delete' resource.pk %}">
            Delete resource
        </a>
    {% endif %}

    <p>
        <a href="{% url 'pages:resources' %}">Back to resources</a>
    </p>
{% endblock %}
~~~

Los enlaces a editar y eliminar se resolverán en las prácticas siguientes. **No abras esta plantilla antes de registrar esas rutas**, o podrían producirse errores `NoReverseMatch` para usuarios con permisos de edición. Para comprobar el detalle de inmediato, puedes omitir temporalmente ambos bloques y agregarlos cuando estén disponibles.

### 4. Proteger la creación con permisos

Agrega en `pages/views.py`:

~~~python
class ResourceCreateView(
    LoginRequiredMixin,
    PermissionRequiredMixin,
    CreateView,
):
    model = Resource
    form_class = ResourceForm
    template_name = "pages/resource_form.html"
    permission_required = "pages.add_resource"
    success_url = reverse_lazy("pages:resources")
~~~

Observa el orden:

1. `LoginRequiredMixin` comprueba la autenticación.
2. `PermissionRequiredMixin` comprueba `pages.add_resource`.
3. `CreateView` se ocupa de mostrar, validar y guardar el formulario.

El comportamiento previsto con los mixins de Django es:

- visitante anónimo → redirección al login;
- usuario autenticado sin permiso → HTTP 403;
- usuario con permiso → formulario de creación y POST autorizado.

No utilizamos `raise_exception=True` aquí porque con `AccessMixin` ese atributo también podría convertir el rechazo anónimo en 403, cuando queremos ofrecerle el login.

### 5. Crear la plantilla de formulario reutilizable

Crea `pages/templates/pages/resource_form.html`:

~~~html
{% extends "pages/base.html" %}

{% block title %}
    {% if object %}Edit resource{% else %}Create resource{% endif %}
    | ClassroomHub
{% endblock %}

{% block content %}
    {% if object %}
        <h2>Edit resource</h2>
    {% else %}
        <h2>Create resource</h2>
    {% endif %}

    <form method="post">
        {% csrf_token %}
        {{ form.non_field_errors }}
        {{ form.as_p }}
        <button type="submit">Save resource</button>
    </form>

    <p>
        <a href="{% url 'pages:resources' %}">Cancel</a>
    </p>
{% endblock %}
~~~

El mismo archivo servirá para crear y editar. El valor `object` existe cuando `UpdateView` está editando un registro. El formulario muestra los errores sin perder los datos válidos que el navegador había enviado.

### 6. Declarar las rutas

En `pages/urls.py` agrega estas rutas **antes de cualquier ruta parametrizada de captura amplia**:

~~~python
path(
    "resources/new/",
    views.ResourceCreateView.as_view(),
    name="resource-create",
),
path(
    "resources/<int:pk>/",
    views.ResourceDetailView.as_view(),
    name="resource-detail",
),
~~~

La ruta `resources/<int:pk>/` espera un número entero. Ejemplo: `/resources/3/`. Usa el nombre de ruta para construir enlaces; no dependas de que el registro tenga un ID fijo.

### 7. Vincular el catálogo con el detalle

En `pages/templates/pages/resources.html` conserva el bucle de recursos, pero convierte el título en un enlace:

~~~html
<h3>
    <a href="{% url 'pages:resource-detail' resource.pk %}">
        {{ resource.title }}
    </a>
</h3>
~~~

Colócalo **dentro del bloque `{% for resource in resources %}`**, sustituyendo el título simple anterior. No dupliques otro bucle.

El resto de la plantilla del Laboratorio 7 puede permanecer igual: descripción, categoría, disponibilidad y estado vacío.

### 8. Crear un grupo de editores

El grupo `Catalog reviewers` del Laboratorio 8 tiene permiso de lectura. Ahora crearemos otro llamado **Catalog editors**, con permisos de ver, añadir, cambiar y eliminar recursos.

Puedes crearlo desde Django Admin, utilizando una cuenta administradora autorizada, o desde el shell:

~~~bash
python manage.py shell
~~~

~~~python
from django.contrib.auth.models import Group, Permission
from django.contrib.contenttypes.models import ContentType
from pages.models import Resource

content_type = ContentType.objects.get_for_model(Resource)
required_codes = [
    "view_resource",
    "add_resource",
    "change_resource",
    "delete_resource",
]

permissions = list(
    Permission.objects.filter(
        content_type=content_type,
        codename__in=required_codes,
    )
)

if len(permissions) != len(required_codes):
    raise RuntimeError("Expected resource permissions were not found")

editors, created = Group.objects.get_or_create(
    name="Catalog editors",
)
editors.permissions.add(*permissions)
~~~

No agregues automáticamente a todos los usuarios al grupo. Desde Admin asigna el grupo únicamente a una **cuenta de demostración** elegida para editar. Si el grupo ya existía, revisa también sus permisos anteriores; `add()` no elimina permisos que ya tuviera.

**No marques `is_staff` para hacer funcionar el CRUD público.** Las vistas utilizarán los permisos correspondientes, no la condición de administrador.

### 9. Probar la creación

1. Abre `/resources/new/` sin iniciar sesión. Debes llegar al login.
2. Inicia sesión con una cuenta común sin permisos. Debes recibir 403.
3. Inicia sesión con una cuenta de `Catalog editors`. Debes ver el formulario.
4. Introduce un título válido, descripción, tipo de recurso y una categoría existente.
5. Envía el formulario. Debes regresar a `/resources/`.
6. Comprueba que el recurso aparece en el catálogo y en Django Admin.
7. Reinicia el servidor y confirma que el registro persiste en MySQL o MariaDB.

### Laboratorio de errores

**Error 1 — El formulario no muestra categorías.** Registra algunas mediante Django Admin y comprueba que la conexión apunta a la base correcta.

**Error 2 — GET funciona, pero POST devuelve 403.** Verifica CSRF y permisos. No elimines el token ni la protección del servidor.

**Error 3 — Un usuario normal puede guardar recursos.** Revisa `permission_required = "pages.add_resource"` y que la vista utiliza los mixins correctos.

**Error 4 — `NoReverseMatch` después de guardar.** Comprueba que existe `pages:resources` y que usaste `reverse_lazy()` en el atributo de la clase.

### Comprobación

Cualquier persona puede consultar un recurso, pero solo una cuenta con `pages.add_resource` puede crear otro mediante una vista pública de la aplicación.

---

## Práctica 9.4 — Actualizar registros sin duplicar formularios

### Objetivos

Al finalizar podrás:

- diferenciar crear de modificar un objeto existente;
- utilizar `UpdateView` con un `ModelForm`;
- autorizar la actualización por separado;
- explicar qué sucede ante datos inválidos o un ID inexistente;
- comprobar que una actualización se almacena en MySQL o MariaDB.

### 1. Crear la vista de edición

En `pages/views.py` agrega:

~~~python
class ResourceUpdateView(
    LoginRequiredMixin,
    PermissionRequiredMixin,
    UpdateView,
):
    model = Resource
    form_class = ResourceForm
    template_name = "pages/resource_form.html"
    permission_required = "pages.change_resource"
    success_url = reverse_lazy("pages:resources")
~~~

La clase se parece a `ResourceCreateView`, pero su responsabilidad es diferente: `UpdateView` busca el recurso indicado por la ruta, presenta sus datos existentes y guarda los cambios en **ese mismo registro**.

No necesitas crear `ResourceUpdateForm` si los campos y las reglas son iguales a los de creación. Reutiliza `ResourceForm`.

### 2. Agregar la ruta de edición

En `pages/urls.py` agrega:

~~~python
path(
    "resources/<int:pk>/edit/",
    views.ResourceUpdateView.as_view(),
    name="resource-update",
),
~~~

La URL contiene el identificador del recurso que deseas modificar.

Por ejemplo, la ruta de un recurso podría verse así:

~~~text
/resources/4/edit/
~~~

El número del ejemplo es solo ilustrativo. En las plantillas utiliza siempre `resource.pk`.

### 3. ¿Qué ocurre al editar?

~~~text
GET /resources/4/edit/
       |
       v
Check change_resource permission
       |
       v
Load Resource(pk=4)
       |
       v
Show ResourceForm(instance=resource)
       |
       v
POST updated fields
       |
       v
Validate -> Save existing row -> Redirect
~~~

La vista debe rechazar al usuario no autorizado **antes de modificar los datos**, aunque la solicitud contenga todos los campos correctos.

### 4. Probar cambios

Con una cuenta del grupo `Catalog editors`:

1. Abre el detalle de un recurso.
2. Selecciona `Edit resource`.
3. Cambia el título y la descripción.
4. Modifica la categoría, utilizando una categoría existente.
5. Desmarca `available` si estaba activado.
6. Guarda y comprueba los cambios en `/resources/`.
7. Consulta el mismo recurso desde Django Admin.

La edición debe actualizar el registro existente, **no crear un duplicado**.

### 5. Confirmar que la PK permanece igual

En el shell:

~~~python
from pages.models import Resource

resource = Resource.objects.get(pk=4)
print(resource.pk, resource.title, resource.available)
~~~

Adapta `4` al ID real de tu recurso. Compara el identificador antes y después de editar: debe permanecer igual.

### 6. Verificar los errores

Envía un título de dos caracteres desde el formulario:

~~~text
ab
~~~

Como `ResourceForm.clean_title()` exige al menos tres caracteres, la edición debe mostrar un error **sin guardar los cambios**. Comprueba el título anterior desde otra pestaña o desde el shell.

Si intentas editar `/resources/999999/edit/` y no existe ese recurso, la vista debería responder 404 **cuando quien la solicita tiene permiso**. Un usuario sin permiso debe ser rechazado de acuerdo con la autorización y no necesita conocer si el recurso existe.

### Antes de ejecutar — Predice

1. ¿Cuál es la diferencia entre `CreateView` y `UpdateView`?
2. ¿Por qué el formulario necesita la instancia existente al editar?
3. ¿Qué sucede si envías una categoría inexistente?
4. ¿Por qué el permiso `add_resource` no debería autorizar automáticamente `change_resource`?

### Ejercicio — Detectar un error de integración

Cambia temporalmente `permission_required` de `ResourceUpdateView` por un permiso inexistente y verifica el resultado con una cuenta de prueba. Restablece el valor correcto y comprueba que la edición vuelve a funcionar.

### Comprobación

La cuenta editora modifica un recurso existente y Django conserva su identificador. Una cuenta normal no puede modificarlo, aunque conozca la URL.

---

## Práctica 9.5 — Eliminación controlada y confirmación

### Objetivos

Al finalizar podrás:

- utilizar `DeleteView`;
- diferenciar mostrar una confirmación de eliminar;
- realizar eliminaciones mediante POST protegido por CSRF;
- comprobar autorizaciones antes de borrar;
- explicar cómo evitar que una navegación GET provoque pérdida de datos.

### 1. ¿Por qué no eliminar con un enlace?

Una dirección como `/resources/4/delete/` puede mostrar un formulario de confirmación. **Abrirla no debería borrar el recurso**: un navegador, un rastreador o una persona podría seguir el enlace sin intención de eliminar información.

El flujo correcto es:

~~~text
GET /resources/4/delete/
  -> Show confirmation (no deletion)

POST /resources/4/delete/ + CSRF token
  -> Check permission
  -> Delete Resource(pk=4)
  -> Redirect to /resources/
~~~

### 2. Crear la vista

En `pages/views.py` agrega:

~~~python
class ResourceDeleteView(
    LoginRequiredMixin,
    PermissionRequiredMixin,
    DeleteView,
):
    model = Resource
    template_name = "pages/resource_confirm_delete.html"
    permission_required = "pages.delete_resource"
    success_url = reverse_lazy("pages:resources")
~~~

A diferencia de las vistas de creación y actualización, no necesita `ResourceForm`: la confirmación identifica el objeto existente y su eliminación se procesa mediante la acción POST.

### 3. Registrar la ruta

En `pages/urls.py` agrega:

~~~python
path(
    "resources/<int:pk>/delete/",
    views.ResourceDeleteView.as_view(),
    name="resource-delete",
),
~~~

### 4. Crear la plantilla de confirmación

Crea `pages/templates/pages/resource_confirm_delete.html`:

~~~html
{% extends "pages/base.html" %}

{% block title %}Delete resource | ClassroomHub{% endblock %}

{% block content %}
    <h2>Delete resource</h2>

    <p>
        Are you sure you want to delete
        <strong>{{ object.title }}</strong>?
    </p>

    <p>This action removes the resource from the catalog.</p>

    <form method="post">
        {% csrf_token %}
        <button type="submit">Confirm deletion</button>
    </form>

    <p>
        <a href="{% url 'pages:resource-detail' object.pk %}">
            Cancel
        </a>
    </p>
{% endblock %}
~~~

La opción **Cancel** es un enlace GET a la página de detalle. La opción **Confirm deletion** envía un POST al servidor. Son operaciones distintas y deben tener comportamientos distintos.

### 5. Realizar una prueba sin perder datos importantes

1. Crea un recurso llamado `Temporary deletion test`.
2. Anota su identificador.
3. Abre su página de confirmación de eliminación.
4. Regresa al catálogo sin confirmar y verifica que el recurso **todavía existe**.
5. Vuelve a la confirmación y envía el formulario POST.
6. Comprueba que regresas al catálogo.
7. Abre la URL antigua del detalle: debe responder 404.
8. Confirma que los demás recursos siguen presentes.

**No uses un recurso importante** para este experimento. El laboratorio no implementa papelera ni recuperación de registros eliminados.

### 6. ¿Qué sucede con la categoría?

`Resource` tiene una clave foránea hacia `Category`, con `on_delete=models.PROTECT` para proteger las categorías referenciadas.

Eliminar **un recurso** no implica eliminar su categoría. En cambio, intentar eliminar una categoría con recursos asociados seguirá protegido. No modifiques `on_delete` ni elimines categorías desde esta interfaz.

### 7. Pruebas de autorización

Verifica:

- visitante anónimo → se redirige al login;
- cuenta común autenticada sin permiso → HTTP 403;
- revisor de solo lectura → HTTP 403;
- editor con `delete_resource` → puede ver confirmación y eliminar mediante POST;
- GET de confirmación → no elimina el recurso;
- POST sin el token CSRF requerido → debe rechazarse cuando el cliente aplica CSRF.

### Laboratorio de errores

**Error 1 — El recurso desaparece al abrir la confirmación.** No debería suceder. Busca código propio que ejecute `delete()` durante GET.

**Error 2 — La ruta es accesible para cualquier cuenta.** Comprueba `PermissionRequiredMixin` y `pages.delete_resource`.

**Error 3 — La confirmación devuelve 403 incluso con permisos.** Distingue un rechazo por autorización de uno provocado por CSRF; no deshabilites protecciones para «arreglar» el formulario.

**Error 4 — Se eliminó otro recurso.** Comprueba la ruta y el identificador que recibe `DeleteView`. No uses valores fijos de `pk` en la plantilla.

### Comprobación

La eliminación exige una acción deliberada POST y el permiso correspondiente. GET presenta una confirmación sin modificar la base.

---

## Práctica 9.6 — Reto integrador: ClassroomHub con CRUD protegido

### Objetivo

Integrar las vistas de detalle, creación, edición y eliminación de recursos sin perder el registro de usuarios, las sesiones, los permisos, las páginas públicas ni la conexión MySQL/MariaDB.

### 1. Requisitos verificables

| Código | Requisito |
|---|---|
| RF01 | La lista `/resources/` sigue siendo pública |
| RF02 | El detalle `/resources/<pk>/` es público |
| RF03 | El enlace de detalle utiliza nombres de rutas y la PK del recurso |
| RF04 | Crear recursos exige `pages.add_resource` |
| RF05 | Editar recursos exige `pages.change_resource` |
| RF06 | Eliminar recursos exige `pages.delete_resource` |
| RF07 | El formulario permite únicamente campos autorizados |
| RF08 | Los datos inválidos se rechazan sin guardar |
| RF09 | La creación y la edición reutilizan `ResourceForm` y una plantilla |
| RF10 | GET de eliminación no borra datos |
| RF11 | POST de eliminación autorizado borra el recurso solicitado |
| RF12 | El catálogo refleja altas, ediciones y eliminaciones |
| RF13 | Las categorías siguen gestionándose desde Django Admin |
| RF14 | El grupo `Catalog reviewers` conserva solo su función de lectura |
| RF15 | Las pruebas utilizan una base de prueba aislada |

### 2. Estructura del proyecto

La estructura relevante, sin incluir archivos del entorno virtual:

~~~text
classroom-hub/
├── .env
├── .env.example
├── .gitignore
├── README.md
├── requirements.txt
├── manage.py
├── config/
│   ├── settings.py
│   └── urls.py
└── pages/
    ├── admin.py
    ├── forms.py
    ├── models.py
    ├── views.py
    ├── urls.py
    ├── tests.py
    ├── migrations/
    └── templates/
        └── pages/
            ├── base.html
            ├── home.html
            ├── about.html
            ├── resources.html
            ├── resource_detail.html
            ├── resource_form.html
            ├── resource_confirm_delete.html
            ├── signup.html
            ├── login.html
            ├── dashboard.html
            └── review.html
~~~

No hay nuevos modelos ni migraciones: `pages/forms.py` y las vistas usan las tablas ya existentes.

### 3. Código consolidado de `pages/views.py`

Este bloque muestra **el archivo completo** después de integrar los cuatro tipos de operación. Compáralo con el tuyo para corregir imports y funciones duplicadas:

~~~python
from django.contrib.auth.decorators import login_required, permission_required
from django.contrib.auth.forms import UserCreationForm
from django.contrib.auth.mixins import (
    LoginRequiredMixin,
    PermissionRequiredMixin,
)
from django.shortcuts import redirect, render
from django.urls import reverse_lazy
from django.views.generic import (
    CreateView,
    DeleteView,
    DetailView,
    UpdateView,
)

from .forms import ResourceForm
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


class ResourceDetailView(DetailView):
    model = Resource
    template_name = "pages/resource_detail.html"
    context_object_name = "resource"


class ResourceCreateView(
    LoginRequiredMixin,
    PermissionRequiredMixin,
    CreateView,
):
    model = Resource
    form_class = ResourceForm
    template_name = "pages/resource_form.html"
    permission_required = "pages.add_resource"
    success_url = reverse_lazy("pages:resources")


class ResourceUpdateView(
    LoginRequiredMixin,
    PermissionRequiredMixin,
    UpdateView,
):
    model = Resource
    form_class = ResourceForm
    template_name = "pages/resource_form.html"
    permission_required = "pages.change_resource"
    success_url = reverse_lazy("pages:resources")


class ResourceDeleteView(
    LoginRequiredMixin,
    PermissionRequiredMixin,
    DeleteView,
):
    model = Resource
    template_name = "pages/resource_confirm_delete.html"
    permission_required = "pages.delete_resource"
    success_url = reverse_lazy("pages:resources")
~~~

La vista de revisión `resource_review` conserva el permiso del Laboratorio 8. Las vistas CRUD comprueban **permisos diferentes para cada operación**.

### 4. Código consolidado de `pages/urls.py`

Este es el archivo completo:

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
    path(
        "resources/new/",
        views.ResourceCreateView.as_view(),
        name="resource-create",
    ),
    path(
        "resources/<int:pk>/",
        views.ResourceDetailView.as_view(),
        name="resource-detail",
    ),
    path(
        "resources/<int:pk>/edit/",
        views.ResourceUpdateView.as_view(),
        name="resource-update",
    ),
    path(
        "resources/<int:pk>/delete/",
        views.ResourceDeleteView.as_view(),
        name="resource-delete",
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

Las rutas del administrador continúan en `config/urls.py`, de modo que `/admin/` sigue funcionando.

### 5. Integrar los enlaces al catálogo

En `pages/templates/pages/resources.html`, dentro del bloque `content` y **antes del bucle de recursos**, puedes añadir:

~~~html
{% if perms.pages.add_resource %}
    <p>
        <a href="{% url 'pages:resource-create' %}">
            Add resource
        </a>
    </p>
{% endif %}
~~~

Dentro del bucle, el título se enlaza con el detalle:

~~~html
<h3>
    <a href="{% url 'pages:resource-detail' resource.pk %}">
        {{ resource.title }}
    </a>
</h3>
~~~

En `pages/templates/pages/resource_detail.html` están los enlaces para editar y eliminar, visibles solo con los permisos correspondientes.

**Los enlaces condicionales no reemplazan los controles de las vistas**. La prueba fundamental es escribir manualmente una URL de edición o eliminación estando autenticado sin permisos: el servidor debe rechazarla.

### 6. Actualizar el menú común

En `pages/base.html`, dentro de la sección que se presenta a un usuario autenticado, puedes añadir:

~~~html
{% if perms.pages.add_resource %}
    <a href="{% url 'pages:resource-create' %}">Add resource</a>
{% endif %}
~~~

Conserva los enlaces `Dashboard` y `Review` del Laboratorio 8 y el formulario de logout mediante POST. No coloques un botón de eliminación que use GET ni sustituyas el formulario de salida por un enlace.

### 7. Pruebas manuales

| Caso | Acción | Resultado esperado |
|---|---|---|
| P01 | Abrir `/resources/` como visitante | 200 |
| P02 | Abrir el detalle de un recurso existente | 200 y datos relacionados |
| P03 | Abrir un detalle inexistente | 404 |
| P04 | Abrir creación sin autenticación | Redirección a login |
| P05 | Abrir creación como cuenta común | 403 |
| P06 | Abrir creación como revisor | 403 |
| P07 | Crear recurso como editor | Se guarda y se redirige |
| P08 | Crear con título de dos caracteres | Error, sin registro |
| P09 | Crear con categoría inexistente | Error, sin registro |
| P10 | Editar como cuenta común | 403 |
| P11 | Editar como editor | Cambios guardados sobre la misma PK |
| P12 | Enviar edición inválida | Se conserva el valor anterior |
| P13 | Abrir confirmación de borrado | El recurso sigue existiendo |
| P14 | Eliminar como cuenta común | 403 |
| P15 | Confirmar borrado como editor | Solo desaparece el recurso elegido |
| P16 | Visitar la URL del recurso eliminado | 404 |
| P17 | Recargar catálogo después de cada operación | Lista actualizada |
| P18 | Reiniciar Django | Los registros no eliminados persisten |
| P19 | Abrir `/resources/review/` como revisor | Lectura autorizada |
| P20 | Ejecutar las pruebas automatizadas | Base de pruebas aislada |

### 8. Conservar las pruebas previas

El Laboratorio 7 incorporó **10 pruebas** y el Laboratorio 8 agregó **18**. Mantén esas **28 pruebas** en `pages/tests.py`: el CRUD debe sumarse, no reemplazar lo que ya funciona.

Las importaciones necesarias (`TestCase`, `reverse`, `get_user_model`, `Group`, `Permission`, `ContentType`, `Category` y `Resource`) ya aparecen en el archivo ampliado en el Laboratorio 8. **No dupliques imports** si ya existen.

### 9. Pruebas de CRUD y permisos

Agrega al final de `pages/tests.py` la siguiente clase. Los datos de cada prueba son ficticios y se crean en la base de pruebas:

~~~python
class ResourceCrudTests(TestCase):
    @classmethod
    def setUpTestData(cls):
        cls.category = Category.objects.create(
            name="Programming",
            description="Programming resources",
        )
        cls.resource = Resource.objects.create(
            title="Python basics",
            description="Variables and functions",
            category=cls.category,
            available=True,
        )

        User = get_user_model()
        cls.reader = User.objects.create_user(
            username="crud_reader",
            password="ExampleTestPassword!2026",
        )
        cls.editor = User.objects.create_user(
            username="crud_editor",
            password="ExampleTestPassword!2026",
        )
        cls.reviewer = User.objects.create_user(
            username="crud_reviewer",
            password="ExampleTestPassword!2026",
        )

        content_type = ContentType.objects.get_for_model(Resource)
        all_permissions = Permission.objects.filter(
            content_type=content_type,
            codename__in=[
                "view_resource",
                "add_resource",
                "change_resource",
                "delete_resource",
            ],
        )
        editors = Group.objects.create(name="Catalog editors")
        editors.permissions.add(*all_permissions)
        cls.editor.groups.add(editors)

        review_permission = Permission.objects.get(
            content_type=content_type,
            codename="view_resource",
        )
        reviewers = Group.objects.create(name="Catalog reviewers")
        reviewers.permissions.add(review_permission)
        cls.reviewer.groups.add(reviewers)

    def resource_data(self, **overrides):
        values = {
            "title": "Database testing",
            "description": "Database integration practice",
            "resource_type": "Guide",
            "category": self.category.pk,
            "available": "on",
        }
        values.update(overrides)
        return values

    def test_resource_detail_is_public(self):
        url = reverse("pages:resource-detail", args=[self.resource.pk])

        response = self.client.get(url)

        self.assertEqual(response.status_code, 200)
        self.assertContains(response, "Python basics")
        self.assertContains(response, "Programming")

    def test_unknown_resource_detail_returns_404(self):
        url = reverse("pages:resource-detail", args=[999999])

        response = self.client.get(url)

        self.assertEqual(response.status_code, 404)

    def test_public_catalog_links_to_resource_detail(self):
        response = self.client.get(reverse("pages:resources"))
        detail_url = reverse(
            "pages:resource-detail", args=[self.resource.pk]
        )

        self.assertContains(response, f'href="{detail_url}"')

    def test_create_redirects_anonymous_user_to_login(self):
        url = reverse("pages:resource-create")

        response = self.client.get(url)

        expected = reverse("pages:login") + f"?next={url}"
        self.assertRedirects(response, expected)

    def test_create_denies_authenticated_reader(self):
        self.client.force_login(self.reader)

        response = self.client.get(reverse("pages:resource-create"))

        self.assertEqual(response.status_code, 403)

    def test_reviewer_cannot_create_resource(self):
        self.client.force_login(self.reviewer)

        response = self.client.get(reverse("pages:resource-create"))

        self.assertEqual(response.status_code, 403)

    def test_editor_can_open_create_form(self):
        self.client.force_login(self.editor)

        response = self.client.get(reverse("pages:resource-create"))

        self.assertEqual(response.status_code, 200)
        self.assertTemplateUsed(response, "pages/resource_form.html")

    def test_reader_cannot_create_resource_via_post(self):
        self.client.force_login(self.reader)

        response = self.client.post(
            reverse("pages:resource-create"),
            self.resource_data(),
        )

        self.assertEqual(response.status_code, 403)
        self.assertFalse(
            Resource.objects.filter(title="Database testing").exists()
        )

    def test_editor_can_create_resource(self):
        self.client.force_login(self.editor)

        response = self.client.post(
            reverse("pages:resource-create"),
            self.resource_data(),
        )

        self.assertRedirects(response, reverse("pages:resources"))
        self.assertTrue(
            Resource.objects.filter(title="Database testing").exists()
        )

    def test_create_rejects_short_title(self):
        self.client.force_login(self.editor)

        response = self.client.post(
            reverse("pages:resource-create"),
            self.resource_data(title="ab"),
        )

        self.assertEqual(response.status_code, 200)
        self.assertFormError(
            response.context["form"],
            "title",
            "Title must contain at least 3 characters.",
        )
        self.assertFalse(Resource.objects.filter(title="ab").exists())

    def test_create_rejects_unknown_category(self):
        self.client.force_login(self.editor)

        response = self.client.post(
            reverse("pages:resource-create"),
            self.resource_data(category=999999),
        )

        self.assertEqual(response.status_code, 200)
        self.assertIn("category", response.context["form"].errors)
        self.assertFalse(
            Resource.objects.filter(title="Database testing").exists()
        )

    def test_form_exposes_only_expected_fields(self):
        self.client.force_login(self.editor)

        response = self.client.get(reverse("pages:resource-create"))
        field_names = list(response.context["form"].fields)

        self.assertEqual(
            field_names,
            [
                "title",
                "description",
                "resource_type",
                "category",
                "available",
            ],
        )
        self.assertNotIn("created_at", field_names)

    def test_update_redirects_anonymous_user_to_login(self):
        url = reverse("pages:resource-update", args=[self.resource.pk])

        response = self.client.get(url)

        self.assertRedirects(
            response,
            reverse("pages:login") + f"?next={url}",
        )

    def test_update_denies_authenticated_reader(self):
        self.client.force_login(self.reader)
        url = reverse("pages:resource-update", args=[self.resource.pk])

        response = self.client.get(url)

        self.assertEqual(response.status_code, 403)

    def test_reader_cannot_update_resource_via_post(self):
        self.client.force_login(self.reader)
        url = reverse(
            "pages:resource-update", args=[self.resource.pk]
        )

        response = self.client.post(
            url,
            self.resource_data(title="Forbidden update"),
        )

        self.assertEqual(response.status_code, 403)
        self.resource.refresh_from_db()
        self.assertEqual(self.resource.title, "Python basics")

    def test_editor_can_open_update_form(self):
        self.client.force_login(self.editor)
        url = reverse("pages:resource-update", args=[self.resource.pk])

        response = self.client.get(url)

        self.assertEqual(response.status_code, 200)
        self.assertTemplateUsed(response, "pages/resource_form.html")
        self.assertContains(response, "Python basics")

    def test_editor_can_update_existing_resource(self):
        self.client.force_login(self.editor)
        old_id = self.resource.pk
        url = reverse("pages:resource-update", args=[old_id])

        response = self.client.post(
            url,
            self.resource_data(
                title="Updated Python resource",
                available="",
            ),
        )

        self.assertRedirects(response, reverse("pages:resources"))
        self.resource.refresh_from_db()
        self.assertEqual(self.resource.pk, old_id)
        self.assertEqual(self.resource.title, "Updated Python resource")
        self.assertFalse(self.resource.available)

    def test_invalid_update_keeps_existing_values(self):
        self.client.force_login(self.editor)
        old_title = self.resource.title
        url = reverse(
            "pages:resource-update", args=[self.resource.pk]
        )

        response = self.client.post(
            url,
            self.resource_data(title="ab"),
        )

        self.assertEqual(response.status_code, 200)
        self.resource.refresh_from_db()
        self.assertEqual(self.resource.title, old_title)

    def test_delete_redirects_anonymous_user_to_login(self):
        url = reverse(
            "pages:resource-delete", args=[self.resource.pk]
        )

        response = self.client.get(url)

        self.assertRedirects(
            response,
            reverse("pages:login") + f"?next={url}",
        )

    def test_delete_denies_authenticated_reader(self):
        self.client.force_login(self.reader)
        url = reverse(
            "pages:resource-delete", args=[self.resource.pk]
        )

        response = self.client.get(url)

        self.assertEqual(response.status_code, 403)

    def test_reviewer_cannot_delete_resource(self):
        self.client.force_login(self.reviewer)
        url = reverse(
            "pages:resource-delete", args=[self.resource.pk]
        )

        response = self.client.post(url)

        self.assertEqual(response.status_code, 403)
        self.assertTrue(
            Resource.objects.filter(pk=self.resource.pk).exists()
        )

    def test_delete_get_only_shows_confirmation(self):
        self.client.force_login(self.editor)
        url = reverse(
            "pages:resource-delete", args=[self.resource.pk]
        )

        response = self.client.get(url)

        self.assertEqual(response.status_code, 200)
        self.assertTemplateUsed(
            response, "pages/resource_confirm_delete.html"
        )
        self.assertTrue(
            Resource.objects.filter(pk=self.resource.pk).exists()
        )

    def test_delete_post_removes_resource(self):
        self.client.force_login(self.editor)
        url = reverse(
            "pages:resource-delete", args=[self.resource.pk]
        )

        response = self.client.post(url)

        self.assertRedirects(response, reverse("pages:resources"))
        self.assertFalse(
            Resource.objects.filter(pk=self.resource.pk).exists()
        )

    def test_unknown_resource_update_returns_404_for_editor(self):
        self.client.force_login(self.editor)
        url = reverse("pages:resource-update", args=[999999])

        response = self.client.get(url)

        self.assertEqual(response.status_code, 404)

    def test_unknown_resource_delete_returns_404_for_editor(self):
        self.client.force_login(self.editor)
        url = reverse("pages:resource-delete", args=[999999])

        response = self.client.get(url)

        self.assertEqual(response.status_code, 404)

    def test_reader_does_not_see_edit_or_delete_links(self):
        self.client.force_login(self.reader)
        url = reverse(
            "pages:resource-detail", args=[self.resource.pk]
        )

        response = self.client.get(url)

        self.assertNotContains(response, "Edit resource")
        self.assertNotContains(response, "Delete resource")

    def test_editor_can_manage_without_staff_status(self):
        self.client.force_login(self.editor)

        self.assertFalse(self.editor.is_staff)
        self.assertFalse(self.editor.is_superuser)
        self.assertTrue(self.editor.has_perm("pages.add_resource"))
        self.assertTrue(self.editor.has_perm("pages.change_resource"))
        self.assertTrue(self.editor.has_perm("pages.delete_resource"))

    def test_editor_can_still_open_review_page(self):
        self.client.force_login(self.editor)

        response = self.client.get(reverse("pages:resource-review"))

        self.assertEqual(response.status_code, 200)

    def test_public_catalog_reflects_new_resource(self):
        Resource.objects.create(
            title="New catalog resource",
            category=self.category,
        )

        response = self.client.get(reverse("pages:resources"))

        self.assertContains(response, "New catalog resource")

    def test_post_without_csrf_is_rejected(self):
        from django.test import Client

        csrf_client = Client(enforce_csrf_checks=True)
        csrf_client.force_login(self.editor)

        response = csrf_client.post(
            reverse("pages:resource-create"),
            self.resource_data(),
        )

        self.assertEqual(response.status_code, 403)
        self.assertFalse(
            Resource.objects.filter(title="Database testing").exists()
        )
~~~

### 10. Ejecutar las pruebas

La clase anterior agrega **27 pruebas** a las 28 de los laboratorios 7 y 8, para un total esperado de **55 pruebas** si conservaste las anteriores sin añadir ni eliminar otras.

Utiliza la configuración del Laboratorio 7:

~~~text
Development database: classroom_hub
Test database:        test_classroom_hub
~~~

La base `test_classroom_hub` es **descartable**; el ejecutor de pruebas de Django la crea, aplica migraciones y la destruye. No guardes allí datos importantes ni la sustituyas por la base de desarrollo.

Ejecuta:

~~~bash
python manage.py check
python manage.py makemigrations --check --dry-run
python manage.py test pages
~~~

Si las pruebas fallan porque no se puede crear la base, revisa los permisos del usuario SQL sobre `test_classroom_hub`. **No concedas permisos globales ni apuntes las pruebas a `classroom_hub`**.

Un conjunto de pruebas correcto debe detectar tanto funcionalidades ausentes como accesos no autorizados y errores de validación. No basta con probar que se puede crear un recurso: también hay que comprobar quién **no** puede hacerlo.

### 11. Depuración de integración

**Caso A — El formulario muestra `FieldError: Unknown field(s)`.** Revisa que `resource_type` exista en el modelo y en la migración aplicada del Laboratorio 7.

**Caso B — Después de guardar aparece `NoReverseMatch`.** Comprueba `pages:resources` y que utilizas `reverse_lazy()` en las vistas genéricas.

**Caso C — El editor recibe 403.** Verifica la cuenta, su grupo y los permisos reales. Si acabas de cambiar permisos en el shell, recarga la instancia del usuario para evitar cachés de permisos antiguos.

**Caso D — Una cuenta común consigue editar.** Verifica el permiso `pages.change_resource` en la clase y que no eliminaste los mixins.

**Caso E — La eliminación sucede con GET.** Investiga lógica propia que llame a `delete()` antes de recibir POST. El ejemplo correcto solo elimina al confirmar.

**Caso F — Una categoría no se puede eliminar desde Admin.** Es el efecto esperado de `models.PROTECT` si contiene recursos. El CRUD público no cambia esa regla.

**Caso G — Fallan las pruebas al crear la base.** Revisa `TEST.NAME`, el usuario SQL y los permisos. No alteres las credenciales de producción para ejecutar pruebas locales.

### 12. Reto adicional — Búsqueda sencilla de solo lectura

Cuando el CRUD funcione, agrega opcionalmente un campo de búsqueda GET al listado. Permite buscar por título utilizando `title__icontains` sin modificar registros. Mantén la búsqueda **pública y de solo lectura**; no conviertas un parámetro GET en una acción de eliminación o actualización.

Agrega pruebas para una búsqueda con resultados, otra sin resultados y un término vacío. Este ejercicio es opcional porque los filtros y consultas más avanzadas se profundizarán en otro laboratorio.

### 13. Preguntas para explicar tu solución

1. ¿Qué diferencia existe entre un `Form` y un `ModelForm`?
2. ¿Por qué conviene declarar explícitamente `Meta.fields`?
3. ¿Qué ocurre cuando `ResourceForm.is_valid()` devuelve `False`?
4. ¿Qué ventajas aporta `CreateView` frente a repetir manualmente cada parte del manejo de POST?
5. ¿Por qué `UpdateView` necesita recuperar el objeto existente?
6. ¿Por qué `DeleteView` muestra una confirmación por GET y elimina por POST?
7. ¿Qué ventaja aporta `reverse_lazy()` en atributos de vistas basadas en clases?
8. ¿Por qué una cuenta autenticada no puede editar sin `pages.change_resource`?
9. ¿Qué diferencia hay entre `Catalog reviewers` y `Catalog editors`?
10. ¿Por qué los enlaces condicionales no sustituyen los permisos de la vista?
11. ¿Qué sucede cuando el ID de un recurso no existe?
12. ¿Cómo compruebas que una actualización conserva la PK?
13. ¿Por qué la base de pruebas no puede contener datos importantes?
14. ¿Qué limitaciones quedan antes de exponer un CRUD real en Internet?

### 14. Lista de comprobación

- [ ] ClassroomHub conserva MySQL o MariaDB.
- [ ] Los modelos y migraciones de `Category` y `Resource` siguen funcionando.
- [ ] `pages/forms.py` define un `ResourceForm` con campos explícitos.
- [ ] Las validaciones impiden guardar un título demasiado corto.
- [ ] La lista y los detalles siguen siendo públicos.
- [ ] Crear exige `pages.add_resource`.
- [ ] Editar exige `pages.change_resource`.
- [ ] Eliminar exige `pages.delete_resource`.
- [ ] Un usuario anónimo es redirigido al login.
- [ ] Un usuario autenticado sin permiso recibe 403.
- [ ] `Catalog reviewers` mantiene acceso de lectura, no de edición.
- [ ] `Catalog editors` puede crear, modificar y eliminar recursos.
- [ ] El registro público no asigna grupos privilegiados.
- [ ] Crear y editar reutilizan una sola plantilla.
- [ ] GET de eliminación no modifica la base.
- [ ] POST de eliminación requiere confirmación y autorización.
- [ ] Los registros creados y modificados persisten al reiniciar.
- [ ] Las 28 pruebas anteriores se conservan.
- [ ] Las nuevas pruebas se ejecutan sobre `test_classroom_hub`.
- [ ] `.env`, contraseñas y datos sensibles no se versionan.
- [ ] El README permite reproducir y comprobar el CRUD.

### 15. Documentación y Git

Actualiza el `README.md` de tu proyecto con las nuevas rutas:

~~~text
GET  /resources/
GET  /resources/<pk>/
GET  /resources/new/
POST /resources/new/
GET  /resources/<pk>/edit/
POST /resources/<pk>/edit/
GET  /resources/<pk>/delete/
POST /resources/<pk>/delete/
~~~

Incluye instrucciones para crear categorías de ejemplo desde Django Admin y asignar el grupo `Catalog editors` a una cuenta de prueba desde una cuenta autorizada. Documenta que **los permisos no se asignan mediante el formulario de registro público**.

El código y las migraciones pueden compartirse, pero no las contraseñas reales ni una exportación de la base con datos personales. Si utilizas Git, registra únicamente cambios reales. Algunos mensajes posibles son:

~~~text
feat: add resource ModelForm and validation
feat: add public resource detail pages
feat: add permission-protected resource creation
feat: add resource update and delete views
test: cover CRUD permissions and validation
docs: explain resource management
~~~

No hace falta crear un informe adicional para demostrar los conceptos: el proyecto reproducible, las pruebas y la explicación de las decisiones permiten revisar el resultado.

---

## Cierre del laboratorio

ClassroomHub ahora puede mostrar recursos y ofrecer a cuentas autorizadas una interfaz propia para **crear, actualizar y eliminar** registros almacenados en MySQL o MariaDB. Las páginas públicas mantienen su acceso, mientras que el servidor aplica permisos distintos según la operación.

El avance principal no consiste solo en tener cuatro operaciones: ahora puedes explicar **por qué el formulario valida datos, la vista decide qué operación ejecutar, el modelo representa el registro y Django controla quién puede modificarlo**.

En laboratorios posteriores será posible ampliar el proyecto con validaciones de negocio, tratamiento de errores, carga de datos, consultas más complejas y nuevas integraciones, sin reconstruir la base de autenticación y persistencia.

### Referencias oficiales

- [Django 6.1 — Formularios](https://docs.djangoproject.com/en/6.1/topics/forms/)
- [Django 6.1 — ModelForm](https://docs.djangoproject.com/en/6.1/topics/forms/modelforms/)
- [Django 6.1 — Vistas genéricas de edición](https://docs.djangoproject.com/en/6.1/topics/class-based-views/generic-editing/)
- [Django 6.1 — Referencia de vistas de edición](https://docs.djangoproject.com/en/6.1/ref/class-based-views/generic-editing/)
- [Django 6.1 — Permisos, autenticación y mixins](https://docs.djangoproject.com/en/6.1/topics/auth/default/)
- [Django 6.1 — Protección CSRF](https://docs.djangoproject.com/en/6.1/howto/csrf/)
- [Django 6.1 — Pruebas](https://docs.djangoproject.com/en/6.1/topics/testing/overview/)
- [Django 6.1 — Validación de instancias de modelos](https://docs.djangoproject.com/en/6.1/ref/models/instances/#validating-objects)
- [Django 5.2 — Formularios](https://docs.djangoproject.com/en/5.2/topics/forms/)
