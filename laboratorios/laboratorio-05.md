# Laboratorio 5 — Simulación completa de un proyecto Python con Scrum y GitHub

Este laboratorio muestra **un proyecto ficticio desde la idea hasta su entrega**. No es una colección de ejercicios: es una simulación explicada del proceso que sigue un equipo para planificar, construir, comprobar y presentar una aplicación de consola en Python mediante Scrum y GitHub.

El propósito principal es comprender **cómo se aplica Scrum**, no memorizar una lista de nombres. Las decisiones técnicas aparecen relacionadas con historias de usuario, incrementos funcionales, revisiones y cambios en el plan. Al final se incluye una versión ejecutable del producto simulado para contrastar lo narrado con código real.

> **Alcance del ejemplo.** Los nombres de integrantes, historias, números de Issues y Pull Requests y registros de seguimiento que aparecen en este documento son **simulados**. No describen actividad real en GitHub. En un repositorio auténtico, los números, las fechas y los autores se generan o registran mientras ocurre el trabajo; no deben inventarse posteriormente.

> **Convención de idioma.** Las explicaciones pedagógicas están en español. Los nombres de archivos, clases, funciones, variables, ramas, mensajes de commits y ejemplos de Issues y Pull Requests están en inglés, como en un proyecto de software con convenciones técnicas consistentes.

## Vista general de la simulación

| Elemento | Decisión del equipo simulado |
|---|---|
| Producto | **GearTrack**: gestión de recursos y préstamos |
| Tecnología | Python 3, biblioteca estándar y terminal |
| Trabajo | Tres sprints autogestionados |
| Responsabilidades | Product Owner, Scrum Master y Developers |
| Seguimiento | GitHub Issues, GitHub Projects, branches, commits y Pull Requests |
| Datos | Archivo JSON para conservar el estado |
| Reportes | Resumen de texto y exportación CSV |
| Cierre | Informe PDF, pitch grupal final, demostración y sustentación |

La aplicación permitirá registrar recursos (por ejemplo, teclados, libros o herramientas), consultar sus cantidades, registrar préstamos, devolver unidades y elaborar reportes. No tendrá interfaz gráfica, base de datos ni dependencias externas.

El caso ilustra cómo evolucionaría el producto durante los tres sprints:

| Sprint | Meta del incremento | Resultado esperado |
|---|---|---|
| Sprint 1 | Representar recursos y administrar un catálogo | Recursos registrados, validados y consultables en memoria |
| Sprint 2 | Gestionar préstamos y devoluciones | Préstamos disponibles, con un defecto de devolución detectado y trabajo pendiente visible |
| Sprint 3 | Resolver el pendiente, conservar datos y emitir reportes | Aplicación final con JSON, CSV, validación de entradas y pruebas |

Esta es **una distribución de ejemplo**. En un proyecto real, el equipo decide cómo organizar sus sprints para cumplir con la fecha límite establecida, sin falsear los registros para hacerlos coincidir artificialmente con un plan anterior.

---

## 1. De un problema concreto a un Product Goal

### 1.1. La necesidad

Un espacio compartido dispone de recursos que varias personas utilizan. Su registro se lleva de forma dispersa: resulta difícil saber cuántas unidades están disponibles, cuáles se prestaron y qué préstamos deben devolverse.

El equipo plantea una solución de consola. Antes de programar, diferencia el **problema** de la **solución**:

- **Problema:** no hay una forma confiable de conocer disponibilidad y préstamos.
- **Usuarios:** personas encargadas de administrar los recursos.
- **Solución propuesta:** registrar recursos, préstamos y devoluciones con información recuperable al reiniciar.
- **Valor esperado:** reducir errores de registro y facilitar la consulta de disponibilidad.

Una lista de funciones no equivale todavía a una visión del producto. El objetivo debe explicar qué cambio se pretende conseguir.

### 1.2. Product Goal

> **Product Goal:** Provide a reliable console-based tool to register shared resources, track loans and returns, and recover inventory data between sessions.

Este compromiso del Product Backlog expresa un resultado deseado y orienta su orden. Las historias de usuario son medios para alcanzarlo.

El equipo evita comenzar directamente con «crear `main.py`» porque ese archivo es una posible decisión técnica, no una necesidad de quien utilizará el producto.

### 1.3. El pitch corresponde al cierre

En esta simulación, el equipo no presenta el pitch como requisito para iniciar la programación. El **pitch elevator grupal ocurre en la presentación final**, cuando ya puede comunicar qué problema abordó y qué solución efectivamente construyó.

El orden previsto del cierre es: **pitch de un minuto → demostración funcional → sustentación técnica y de Scrum**. El pitch no se transcribe ni se incorpora al informe PDF.

---

## 2. Scrum explicado a través del caso

### 2.1. Qué es Scrum

Scrum es un marco ligero que permite generar valor mediante soluciones adaptativas a problemas complejos. Se basa en **empirismo** y **pensamiento Lean**. En vez de asumir que todas las decisiones iniciales serán correctas, el equipo construye un incremento, observa lo que ocurrió y ajusta el trabajo siguiente.

Sus tres pilares son:

| Pilar | Significado dentro de GearTrack |
|---|---|
| **Transparencia** | Las historias, impedimentos, defectos y resultados se registran de manera comprensible |
| **Inspección** | El equipo revisa el incremento, las pruebas y el avance hacia el objetivo |
| **Adaptación** | Reordena el trabajo o cambia la solución cuando la evidencia lo exige |

Los cinco valores de Scrum —**compromiso, enfoque, apertura, respeto y coraje**— se reflejan en conductas concretas. Informar que una devolución todavía falla, en vez de marcar la historia como terminada, es un ejemplo de apertura y coraje.

Scrum **no** equivale a crear un tablero, mover tarjetas o dividir un proyecto en tres partes sin revisión. GitHub es una herramienta de soporte; Scrum determina por qué se inspecciona y adapta el trabajo.

### 2.2. Scrum Team: responsabilidades y participación técnica

El equipo simulado tiene tres integrantes:

| Integrante | Responsabilidades de Scrum | Participación técnica |
|---|---|---|
| Alex | Product Owner + Developer | Reglas de recursos, revisión y pruebas |
| Sam | Scrum Master + Developer | Gestión de préstamos, correcciones e integración |
| Taylor | Developer | Persistencia, reportes y pruebas |

La guía Scrum define tres conjuntos de responsabilidades: **Product Owner, Scrum Master y Developers**. En este ejemplo educativo reducido, algunas personas asumen responsabilidades adicionales, pero **todos forman parte del equipo de desarrollo y participan en la construcción del producto**.

**Product Owner.** Se responsabiliza de maximizar el valor del producto y de gestionar eficazmente el Product Backlog. Por ejemplo, decide que «evitar préstamos cuando no hay unidades disponibles» tiene más valor inmediato que «ordenar los reportes por nombre».

**Scrum Master.** Se responsabiliza de establecer Scrum tal como lo describe su guía, ayudar al equipo a mejorar su efectividad y facilitar la eliminación de impedimentos. No es un jefe que distribuye cada línea de código.

**Developers.** Se responsabilizan de crear un incremento utilizable en cada sprint y de adaptar el plan para alcanzar el Sprint Goal. La programación, las pruebas y la calidad no son responsabilidad exclusiva de quien creó una historia o un commit.

**Importante:** la distribución del trabajo no divide el conocimiento. Cualquier integrante deberá poder explicar qué hace un archivo, por qué se utiliza una estructura de datos y cómo se relaciona una modificación con su historia de usuario.

### 2.3. Los cinco eventos de Scrum

El **Sprint** es el evento contenedor. En su interior se desarrollan los otros cuatro eventos:

| Evento | Propósito | Ejemplo en GearTrack |
|---|---|---|
| **Sprint** | Construir valor mediante un incremento y acercarse al Product Goal | Entregar una nueva capacidad verificable del sistema |
| **Sprint Planning** | Definir por qué importa el sprint, qué se abordará y cómo se trabajará | Seleccionar historias y proponer diseño, pruebas y colaboración |
| **Daily Scrum** | Inspeccionar el progreso hacia el Sprint Goal y ajustar el Sprint Backlog | Detectar que la devolución se registra dos veces y cambiar el plan |
| **Sprint Review** | Inspeccionar con las partes interesadas el resultado del sprint y adaptar el Product Backlog | Demostrar préstamos y registrar una mejora necesaria |
| **Sprint Retrospective** | Planear cómo mejorar calidad y efectividad | Acordar pruebas de casos repetidos antes de solicitar integración |

La Daily Scrum no es una reunión para reportarle al Scrum Master qué hizo cada persona. En una adaptación educativa puede utilizar un registro breve para conservar evidencia, pero lo importante es inspeccionar el progreso y ajustar el plan.

La Sprint Review no es un acto de aprobación exclusivamente técnico. Busca contrastar el incremento con las necesidades y obtener retroalimentación útil. En esta **simulación**, las observaciones de personas usuarias son parte ficticia del caso y se identifican como tales.

La Sprint Retrospective se concentra en **cómo trabaja el equipo**, no en volver a enumerar funcionalidades.

### 2.4. Artefactos y compromisos

Los tres artefactos de Scrum tienen un compromiso asociado:

| Artefacto | Compromiso | Pregunta que responde |
|---|---|---|
| **Product Backlog** | **Product Goal** | ¿Qué necesitamos mejorar para alcanzar el objetivo del producto? |
| **Sprint Backlog** | **Sprint Goal** | ¿Qué queremos lograr ahora y cómo avanzaremos? |
| **Increment** | **Definition of Done** | ¿Qué trabajo integrado cumple el estándar de calidad acordado? |

Un **Product Backlog** es una lista emergente y ordenada de lo que se necesita para mejorar el producto. No es un documento congelado.

El **Sprint Backlog** contiene el Sprint Goal, los elementos seleccionados del Product Backlog y un plan accionable para entregarlos. Los Developers lo adaptan conforme aprenden.

El **Increment** es una parte utilizable del producto, compatible con los incrementos anteriores. Un cambio sin integrar o que no cumple la Definition of Done **no es un incremento terminado**.

---

## 3. Preparación del proyecto y gestión en GitHub

### 3.1. Product Backlog inicial

El Product Owner presenta una propuesta y la conversa con el equipo. Para este simulacro se registran estas historias:

| Issue ficticio | Título en GitHub | Prioridad | Sprint inicialmente previsto |
|---|---|---|---|
| #11 | `Register resources` | Alta | Sprint 1 |
| #12 | `List resources and availability` | Alta | Sprint 1 |
| #13 | `Check out a resource` | Alta | Sprint 2 |
| #14 | `Return a borrowed resource` | Alta | Sprint 2 |
| #15 | `Persist inventory state in JSON` | Alta | Sprint 3 |
| #16 | `Export resource report to CSV` | Media | Sprint 3 |
| #17 | `Harden console input and add regression tests` | Alta | Sprint 3 |

Los identificadores `#11` a `#17` solo ilustran cómo se verían en GitHub. La plataforma asignará los números reales.

Las historias describen capacidades observables; actividades como «crear clase», «escribir prueba» o «corregir importación» son **tareas técnicas** vinculadas a una historia, no objetivos del usuario por sí mismas.

### 3.2. Ejemplo completo de un Issue

El equipo documenta la historia de préstamo de este modo:

~~~markdown
# Check out a resource

As a resource manager,
I want to register a loan for an available resource,
so that I can track who borrowed it and update availability.

## Acceptance criteria

- The resource must already exist.
- The borrower name cannot be blank.
- A loan is rejected when no units are available.
- A successful loan reduces available units by exactly one.
- Every accepted loan receives a unique identifier.
- A rejected loan does not change resources or loan history.

## Technical tasks

- [ ] Implement the Loan model.
- [ ] Implement the Inventory.checkout() operation.
- [ ] Handle invalid requests in the console.
- [ ] Add successful and rejected loan tests.
- [ ] Link the pull request and verification results.
~~~

**Por qué sirve cada elemento:**

- La **historia de usuario** conecta la funcionalidad con una necesidad.
- Los **criterios de aceptación** permiten comprobar qué debe cumplirse.
- Las **tareas técnicas** describen cómo se propone materializar la historia.
- La **relación con el PR y las pruebas** permite inspeccionar el resultado sin depender de la memoria del equipo.

El Issue debe existir antes de comenzar el trabajo relacionado. Si el conocimiento cambia, se actualiza el Issue dejando un registro real del cambio.

### 3.3. Definition of Done

Antes de desarrollar, el equipo acuerda esta Definition of Done:

~~~markdown
# Definition of Done

A backlog item is Done only when:

- Acceptance criteria have been verified.
- Code is understandable and follows the project structure.
- Relevant tests have passed.
- Changes are integrated into the main branch.
- Documentation reflects the delivered behavior.
- The issue links to the corresponding implementation and tests.
- Every team member can explain the technical decisions.
~~~

La Definition of Done no se reinventa para justificar cada historia. Es un compromiso compartido de calidad para el incremento.

Una historia puede tener código escrito y aun así permanecer pendiente: por ejemplo, si sus pruebas fallan o todavía no está integrada.

### 3.4. Repositorio y tablero

El equipo crea el repositorio ficticio `geartrack`, habilita Issues y utiliza GitHub Projects con los estados:

| Estado | Qué representa |
|---|---|
| `Backlog` | Trabajo identificado pero no seleccionado para el sprint |
| `Todo` | Trabajo seleccionado que todavía no comenzó |
| `In Progress` | Trabajo en construcción |
| `In Review` | Trabajo pendiente de revisión, pruebas o integración |
| `Done` | Trabajo que cumple la Definition of Done |

Se añade un campo **Sprint** para separar `Sprint 1`, `Sprint 2` y `Sprint 3`. El campo puede implementarse con iteraciones o con otro campo de proyecto apropiado. El equipo organiza su calendario de forma autónoma según la fecha de entrega.

La columna `Done` del tablero es una representación visual: **no sustituye** la Definition of Done.

### 3.5. Convenciones para enlazar el trabajo

Ejemplo del recorrido de una historia:

~~~text
Issue #13: Check out a resource
    |
    v
Technical tasks and acceptance criteria
    |
    v
Branch: feature/13-resource-checkout
    |
    v
Commit: feat: validate resource checkout
    |
    v
Pull request #21: feat: add resource checkout
    |
    v
Tests + review + merge into main
    |
    v
Issue #13: Done
~~~

Un Pull Request puede incluir en su descripción `Closes #13` para enlazar el trabajo con un Issue y cerrarlo automáticamente al fusionarlo en la rama predeterminada, cuando se cumplen las condiciones de GitHub. Para historias aún no terminadas, se utiliza una referencia sin cierre automático, como `Refs #14`.

No todos los commits necesitan resolver una historia completa; un commit debe explicar **un cambio real y comprensible**. Una rama permite aislar el trabajo hasta que pueda revisarse.

Comandos ilustrativos de una rama:

~~~bash
git switch -c feature/13-resource-checkout
git add resource.py inventory.py tests/test_inventory.py
git commit -m "feat: validate resource checkout"
git push -u origin feature/13-resource-checkout
~~~

Los archivos deben existir y contener cambios reales antes de ejecutar estos comandos. El ejemplo no representa un commit realizado en un repositorio auténtico.

---

## 4. Simulación del Sprint 1 — Primer incremento

### 4.1. Sprint Planning

El equipo acuerda:

> **Sprint Goal:** Build a usable in-memory catalog of resources with validated quantities.

Selecciona las historias `#11` y `#12` y prepara un Sprint Backlog:

| Tarea | Historia | Responsable principal | Estado inicial |
|---|---|---|---|
| Define resource fields and validation | #11 | Alex | Todo |
| Implement catalog registration | #11 | Sam | Todo |
| Implement resource listing | #12 | Taylor | Todo |
| Test duplicates and invalid quantities | #11, #12 | Equipo | Todo |

La persona responsable principal facilita coordinación; las demás también programan, revisan y prueban.

### 4.2. Primeras decisiones técnicas

Para representar un recurso se crea un objeto `Resource`, en lugar de llevar por separado listas de códigos, nombres y cantidades.

Un fragmento de la implementación inicial puede verse así:

~~~python
class Resource:
    def __init__(self, code, name, total_units):
        if not code.strip() or not name.strip():
            raise ValueError("Code and name are required.")
        if type(total_units) is not int or total_units < 0:
            raise ValueError("Total units must be a non-negative integer.")

        self.code = code.strip()
        self.name = name.strip()
        self.total_units = total_units
        self.available_units = total_units
~~~

Este fragmento demuestra el principio: cada instancia guarda su propio estado y valida datos antes de aceptarlos. La versión del anexo agrega categoría, consultas y serialización.

La clase `Inventory` conserva varios objetos `Resource` en un diccionario cuya clave es el código. Esa decisión responde a la necesidad de consultar recursos por un identificador único:

~~~python
class Inventory:
    def __init__(self):
        self._resources = {}

    def add_resource(self, resource):
        if resource.code in self._resources:
            raise ValueError("Resource code already exists.")
        self._resources[resource.code] = resource
~~~

La separación importa: `Resource` controla las reglas de cada recurso; `Inventory` controla la colección. La entrada de usuario pertenece a `main.py`, no al modelo.

### 4.3. Avance registrado en GitHub

El equipo mueve historias y tareas al estado real correspondiente. Un historial ilustrativo sería:

~~~text
Branch: feature/11-register-resources
Commit: feat: add resource validation
PR #19: feat: register resources
Linked issue: Closes #11

Branch: feature/12-list-resources
Commit: feat: list resources with availability
PR #20: feat: display resource catalog
Linked issue: Closes #12
~~~

Las referencias son simuladas. En un proyecto real, el equipo verificaría que el PR incorpore los archivos correctos, que la aplicación funcione y que los criterios de aceptación estén cumplidos antes de fusionarlo.

### 4.4. Daily Scrum: inspeccionar el avance

En el caso ficticio, el equipo detecta que los códigos duplicados todavía se aceptan en una versión de trabajo. Ajusta el Sprint Backlog: añade una comprobación de duplicados antes de considerar terminada la historia `#11`.

Registro de ejemplo:

~~~markdown
# Sprint 1 — Daily Scrum (SIMULATED)

Sprint Goal: Build a usable resource catalog.

Progress:
- Resource fields and validation are implemented.
- Listing works with multiple resources.

Impediment:
- Duplicate resource codes were accepted.

Adaptation:
- Add duplicate-code validation and a regression test
  before merging the registration pull request.
~~~

Es útil registrar **qué decisión cambió**, no solo una lista de actividades realizadas.

### 4.5. Sprint Review y Retrospective

En la Sprint Review el equipo demuestra que puede registrar varios recursos, consultar disponibilidad y rechazar un código duplicado. El incremento está integrado en `main` y satisface la Definition of Done.

En la Sprint Retrospective acuerda una mejora:

> **Improvement:** Write a failing-case test whenever a validation rule is added.

Esta mejora afectará el modo de trabajar del siguiente sprint. La Review inspeccionó el producto; la Retrospective examinó el proceso del equipo.

---

## 5. Simulación del Sprint 2 — Un problema que no debe ocultarse

### 5.1. Sprint Planning

El equipo plantea:

> **Sprint Goal:** Track loans and returns without losing consistency in available units.

Selecciona `#13` y `#14`. El Product Backlog prioriza estas historias porque no existe seguimiento de préstamos si solo se registra un catálogo.

Las tareas incluyen crear el modelo `Loan`, implementar `checkout()`, implementar `return_loan()`, probar rechazos y comprobar cambios repetidos de estado.

### 5.2. Registrar un préstamo

El sistema verifica que el recurso exista, que haya disponibilidad y que la persona esté identificada. Una operación aceptada crea un préstamo y reduce una unidad.

La regla crítica es:

~~~text
Before checkout:
  total_units = 3
  available_units = 3
  active_loans = 0

After one successful checkout:
  total_units = 3
  available_units = 2
  active_loans = 1
~~~

Si no hay unidades disponibles, **no** debe agregarse un préstamo ni modificarse el recurso.

Esta regla relaciona conceptos de los laboratorios anteriores: condiciones, objetos, colecciones y validaciones.

### 5.3. El defecto de la devolución

Una versión incompleta de `return_loan()` contiene este defecto:

~~~python
def return_loan(self, loan_id):
    loan = self._loans[loan_id]
    resource = self._resources[loan.resource_code]

    resource.checkin()
    loan.returned = True
~~~

Si la operación se ejecuta dos veces sobre el mismo préstamo, puede sumar disponibilidad dos veces o producir un error poco claro. Falta comprobar si el préstamo **ya fue devuelto** antes de modificar el estado.

Durante las pruebas, el equipo reproduce el problema. `#14` tiene código, pero **no cumple** su criterio de aceptación ni la Definition of Done.

El error no debe ocultarse moviendo `#14` a `Done`.

### 5.4. Daily Scrum y adaptación del plan

El seguimiento registrado podría ser:

~~~markdown
# Sprint 2 — Daily Scrum (SIMULATED)

Sprint Goal: Track loans and returns safely.

Progress:
- Checkout is implemented and tested.
- Return flow works for the first request.

Impediment:
- Repeated returns can produce inconsistent availability.

Decision:
- Keep Issue #14 In Progress.
- Add a test for repeated returns.
- Do not merge the return pull request as completed.
~~~

En la **Sprint Review**, el equipo demuestra lo terminado: `#13` permite registrar préstamos y rechaza operaciones sin disponibilidad. Explica que `#14` sigue pendiente por el defecto identificado.

En la **Sprint Retrospective**, acuerda:

> **Improvement:** Test repeated state-changing operations before review.

Al inspeccionar la situación, el Product Owner conserva `#14` en el Product Backlog para volver a considerarla durante la siguiente planificación. El equipo no declara un incremento ficticio ni cambia fechas o commits para aparentar cumplimiento.

---

## 6. Simulación del Sprint 3 — Completar el producto y conservar los datos

### 6.1. Sprint Planning y reordenación

Con la información aprendida, el equipo propone:

> **Sprint Goal:** Deliver a recoverable, verified loan-tracking application with useful reports.

El Sprint Backlog incluye `#14` (pendiente), `#15` (JSON), `#16` (CSV) y `#17` (errores y pruebas). La selección exige revisar su capacidad real y reducir alcance si hace falta; no autoriza ignorar la calidad.

### 6.2. Resolver el defecto antes de cerrar la historia

La devolución debe identificar un préstamo activo y comprobar que no se ha devuelto previamente:

~~~python
def return_loan(self, loan_id):
    loan = self._loans.get(loan_id)
    if loan is None:
        raise ValueError("Loan does not exist.")
    if loan.returned:
        raise ValueError("Loan has already been returned.")

    resource = self._resources[loan.resource_code]
    resource.checkin()
    loan.returned = True
~~~

Ahora se verifica tanto el caso correcto como el repetido. Solo después se revisa, integra y considera `#14` como terminada.

En GitHub podría existir:

~~~text
Branch: fix/14-prevent-double-return
Commit: fix: reject repeated loan returns
PR #23: fix: prevent returning a loan twice
Linked issue: Closes #14
~~~

El identificador de historia permanece igual. El hecho de trasladarla al sprint siguiente no obliga a inventar una historia nueva.

### 6.3. Persistencia JSON: una nueva responsabilidad

El Laboratorio 4 de Python introduce `pathlib` y `json`. Aquí esas herramientas se aplican para recuperar información al volver a iniciar la aplicación.

La responsabilidad se separa en `storage.py`:

~~~python
from pathlib import Path
import json

DATA_PATH = Path(__file__).resolve().parent / "data" / "state.json"

def load_inventory(path=DATA_PATH):
    # Read saved state, validate it and return an Inventory.
    ...

def save_inventory(inventory, path=DATA_PATH):
    # Serialize a verified inventory into JSON.
    ...
~~~

Este fragmento solo presenta la **interfaz**. La implementación completa está en el anexo.

El equipo acuerda estas reglas:

1. Si el archivo no existe, puede iniciarse un inventario vacío.
2. Si el JSON es válido, se reconstruyen recursos y préstamos.
3. Si el JSON está dañado o los datos son inconsistentes, se informa el error.
4. **No se sobrescribe automáticamente el archivo dañado por un inventario vacío.**
5. Solo se confirma el guardado después de que finaliza la escritura.

Estas decisiones son reglas de integridad de datos, no simples detalles de sintaxis.

### 6.4. Reportes CSV: datos derivados, no duplicados

Para la historia `#16`, el equipo elige un CSV con columnas:

~~~text
code,name,category,total_units,available_units,borrowed_units
~~~

Donde:

~~~python
borrowed_units = resource.total_units - resource.available_units
~~~

El archivo CSV es un **reporte exportado**, no la fuente principal del estado. El JSON conserva la información; el CSV presenta un resumen que puede regenerarse.

Se añade también un reporte en texto. Ambas implementaciones comparten la operación `generate(inventory)`, lo que permite explicar **herencia y polimorfismo** mediante dos formatos que tienen una necesidad real, sin crear jerarquías artificiales para administrar recursos.

### 6.5. Entradas seguras y pruebas de regresión

La historia `#17` considera entradas como `abc` cuando se espera un número, recursos inexistentes, nombres vacíos y archivos dañados.

Ejemplo de conversión:

~~~python
def read_integer(prompt):
    return int(input(prompt).strip())
~~~

La conversión puede provocar `ValueError`. La interfaz debe atender ese error, informar qué ocurrió y conservar el funcionamiento del menú. Las clases de negocio deben validar sus propias reglas, incluso cuando sean llamadas sin pasar por la interfaz.

Las pruebas automatizadas del anexo verifican, entre otros comportamientos, el rechazo de devoluciones duplicadas, la recuperación del JSON y la generación del CSV.

### 6.6. Sprint Review y Retrospective finales

En la Review final el equipo demuestra el flujo completo: crear recurso, prestar, devolver, cerrar la aplicación, reiniciar y verificar que el estado se conserva; después exporta el reporte CSV.

Una retroalimentación **simulada** solicita buscar préstamos por nombre de persona. El Product Owner registra esa idea como nueva entrada del Product Backlog, pero no declara que esté implementada.

La Retrospective identifica qué mejoró y qué debe conservarse:

~~~markdown
# Sprint 3 — Retrospective (SIMULATED)

What improved:
- Tests were added before merging changes.
- Data validation prevents inconsistent JSON state.
- Pull requests link implementation and verification.

What needs improvement:
- Review error messages with users earlier.

Follow-up:
- Add acceptance examples for invalid input to future stories.
~~~

El cierre no elimina trabajo pendiente ni convierte automáticamente nuevas ideas en funcionalidades del incremento.

---

## 7. Trazabilidad completa: del Product Backlog al código

### 7.1. Correspondencia entre artefactos de Scrum y GitHub

| Elemento | Evidencia que debería existir |
|---|---|
| Product Goal | Descripción estable y accesible del objetivo |
| Product Backlog | Issues ordenados por prioridad |
| Sprint Goal | Objetivo registrado para cada sprint |
| Sprint Backlog | Issues seleccionados, tareas y plan actualizado |
| Daily Scrum | Notas breves sobre avance, impedimentos y adaptación |
| Increment | Código integrado y ejecutable que cumple la Definition of Done |
| Sprint Review | Demostración y conclusiones, con ajustes al Product Backlog |
| Sprint Retrospective | Mejora del proceso acordada por el equipo |
| Verificación técnica | Pruebas relacionadas con historias y cambios |
| Trazabilidad de implementación | Branches, commits, Pull Requests y referencias cruzadas |

GitHub Projects **no implementa Scrum por sí solo**: visualiza el trabajo. La calidad del proceso depende de decisiones, revisiones y evidencias auténticas.

### 7.2. Ejemplo de Pull Request en inglés

~~~markdown
# fix: prevent returning a loan twice

## Related issue
Closes #14

## Why
A second return could incorrectly modify available units.

## Changes
- Reject already returned loans.
- Preserve inventory state when the request is invalid.
- Add a regression test for repeated returns.

## Verification
- Run: python -m unittest discover -s tests -v
- Confirm that a second return raises ValueError.
- Confirm that available units remain unchanged.

## Review notes
The fix does not change successful checkout behavior.
~~~

**Por qué es un buen PR:** identifica el problema, el cambio, la relación con la historia y la manera de verificarlo. No utiliza descripciones genéricas como «cambios finales».

### 7.3. Seguimiento y reporte de tareas

El registro de la simulación podría consolidarse así:

| Historia | Sprint planificado | Sprint de finalización | Estado final | Evidencia simulada |
|---|---|---|---|---|
| #11 Register resources | 1 | 1 | Done | PR #19 |
| #12 List resources and availability | 1 | 1 | Done | PR #20 |
| #13 Check out a resource | 2 | 2 | Done | PR #21 |
| #14 Return a borrowed resource | 2 | 3 | Done | PR #23 |
| #15 Persist inventory state in JSON | 3 | 3 | Done | PR #24 |
| #16 Export resource report to CSV | 3 | 3 | Done | PR #25 |
| #17 Harden console input and add regression tests | 3 | 3 | Done | PR #26 |

Los datos son inventados **para explicar el formato**, no para mostrar actividad real. En un proyecto verdadero, la columna de evidencias debe enlazar a registros existentes.

No hay que crear un commit por cada cambio de estado del tablero. Sí debe ser posible explicar qué cambio técnico respalda una historia que se reporta como terminada.

### 7.4. Señales de inconsistencia

| Situación | Por qué requiere revisión |
|---|---|
| Una historia está en Done pero no pasó sus pruebas | No cumple la Definition of Done |
| Un PR cierra un Issue cuya funcionalidad sigue fallando | La automatización de GitHub no demuestra calidad |
| Todo el historial aparece después de elaborar el informe | No permite verificar seguimiento progresivo |
| Un commit dice `feat: add persistence` pero solo cambia el README | El mensaje no describe el cambio |
| El PDF afirma que una historia terminó antes de que existiera su implementación | La cronología reportada contradice la evidencia |
| Una persona no puede explicar un cambio que figura bajo su autoría | Existe una brecha de comprensión técnica |

El historial Git es una evidencia útil, pero **no acredita por sí solo que el equipo practicó Scrum**. Por eso importan también las decisiones y los resultados de Review y Retrospective.

---

## 8. Informe final PDF: síntesis verificable

El informe resume los registros, sin sustituir ni copiar todo GitHub.

Una estructura apropiada incluye:

1. **Identificación:** nombre del producto, integrantes, responsabilidades y enlaces.
2. **Planificación:** Product Goal, Product Backlog resumido y Definition of Done.
3. **Seguimiento de cada sprint:** Sprint Goal, historias, responsables, avance, impedimentos, ajustes, Review y Retrospective.
4. **Resultados:** incrementos entregados, pruebas satisfactorias, pendientes y enlaces a evidencias.
5. **Conclusiones:** valor logrado, decisiones técnicas y mejoras necesarias.

Ejemplo de reporte consolidado **simulado**:

| Indicador | Resultado del ejemplo |
|---|---:|
| Sprints realizados | 3 |
| Historias planificadas | 7 |
| Historias terminadas | 7 |
| Historias trasladadas entre sprints | 1 |
| Defectos relevantes detectados | 1 |
| Defectos relevantes corregidos | 1 |

En un proyecto real estos datos deben obtenerse de Issues, tablero, PR y pruebas, no estimarse de memoria. La cantidad de commits no debe interpretarse como una medida directa de productividad.

El pitch elevator **no se incluye en el PDF**: corresponde a la presentación final.

---

## 9. Presentación final y dominio técnico

### 9.1. Pitch elevator grupal

El equipo inicia la presentación final con un pitch de un minuto:

> «Los recursos compartidos suelen prestarse sin un control claro de disponibilidad. GearTrack permite registrar recursos, prestar y devolver unidades y consultar el estado de forma sencilla desde la terminal. Además, guarda la información en JSON y exporta un reporte CSV. Así se reduce la pérdida de seguimiento y se facilita la administración del inventario».

El pitch presenta **valor y solución**, no enumera archivos ni commits. Se realiza de forma grupal, sin presentación individual adicional.

### 9.2. Demostración del producto

Después del pitch, el equipo muestra una ejecución del sistema:

~~~text
1. Register resource
2. List resources
3. Check out resource
4. Return loan
5. Show summary
6. Export CSV
0. Exit
~~~

Demuestra un préstamo válido, uno rechazado, una devolución, un intento de devolución repetida y la recuperación de datos después de reiniciar.

### 9.3. Sustentación técnica y de Scrum

Cualquier integrante puede recibir preguntas sobre cualquier parte:

- ¿Por qué `Inventory` utiliza diccionarios indexados por código?
- ¿Qué diferencia hay entre el estado del recurso y el estado del préstamo?
- ¿Por qué se valida el estado antes de devolver una unidad?
- ¿Qué ocurre si `state.json` contiene datos inválidos?
- ¿Qué relación hay entre `#14`, su prueba de regresión y el PR que la corrige?
- ¿Qué evidencia permite afirmar que el Sprint 2 no completó esa historia?
- ¿Por qué un Issue cerrado no garantiza que su incremento esté Done?
- ¿Qué cambiaría si el sistema debiera permitir varios préstamos simultáneos del mismo recurso?

**Usar IA no sustituye la comprensión.** Si una herramienta propone una función, un archivo, un comando o un cambio de diseño, el equipo sigue siendo responsable de probarlo, entenderlo, justificarlo y explicar sus consecuencias.

---

## 10. Cómo reproducir el proceso en otro proyecto

La simulación puede servir como referencia para cualquier proyecto pequeño de consola. Lo esencial no es copiar GearTrack, sino conservar este razonamiento:

1. Comprender un problema y formular un Product Goal.
2. Elaborar un Product Backlog con historias verificables.
3. Distribuir responsabilidades sin excluir a nadie del desarrollo.
4. Establecer una Definition of Done.
5. Organizar el trabajo en tres sprints autogestionados.
6. Crear y adaptar los Sprint Backlogs.
7. Mantener Issues, ramas, commits, PR, pruebas y decisiones relacionados.
8. Inspeccionar incrementos reales y registrar lo que no se termina.
9. Preparar el informe a partir de GitHub.
10. Presentar el pitch final y sustentar todo el proceso y el código.

La planificación pertenece al equipo, pero la evidencia debe existir mientras sucede el trabajo. Los ejemplos ficticios nunca deben utilizarse para aparentar fechas, autores o actividades reales.

### Relación con los laboratorios anteriores

| Laboratorio | Aplicación visible en GearTrack |
|---|---|
| [01 — Fundamentos de Python](laboratorio-01.md) | Variables, tipos básicos, operadores, conversiones, `input()` y salida de consola |
| [02 — Decisiones, ciclos y colecciones](laboratorio-02.md) | `if`, `while`, `for`, listas, tuplas, conjuntos, diccionarios y comprensiones |
| [03 — Funciones, módulos y POO](laboratorio-03.md) | Funciones, imports, clases, `__init__`, `self`, propiedades y colaboración de objetos |
| [04 — Excepciones, persistencia y POO](laboratorio-04.md) | `try/except`, `raise`, JSON, CSV, `pathlib`, composición, herencia y polimorfismo en reportes |

---

## Anexo A — Versión final ejecutable del producto simulado

El código siguiente **no representa commits históricos reales**. Es una versión final coherente que permite ejecutar, inspeccionar y probar el producto ficticio. Las secciones narrativas anteriores explican las decisiones que llevarían hasta este resultado.

La estructura es:

~~~text
geartrack/
├── main.py
├── resource.py
├── inventory.py
├── storage.py
├── reports.py
├── tests/
│   └── test_inventory.py
├── data/
│   └── state.json          # Created when saving
└── reports/
    └── resources.csv      # Created when exporting
~~~

Los archivos JSON y CSV se generan mediante la aplicación. No es necesario crearlos manualmente.

### A.1. `resource.py` — Objetos con estado y reglas

~~~python
CATEGORIES = ("Equipment", "Books", "Supplies")


class Resource:
    def __init__(self, code, name, category, total_units, available_units=None):
        if not isinstance(code, str) or not code.strip():
            raise ValueError("Resource code is required.")
        if not isinstance(name, str) or not name.strip():
            raise ValueError("Resource name is required.")
        if category not in CATEGORIES:
            raise ValueError("Unknown resource category.")
        if type(total_units) is not int or total_units < 0:
            raise ValueError("Total units must be a non-negative integer.")

        if available_units is None:
            available_units = total_units
        if (
            type(available_units) is not int
            or available_units < 0
            or available_units > total_units
        ):
            raise ValueError("Available units are invalid.")

        self._code = code.strip()
        self._name = name.strip()
        self._category = category
        self._total_units = total_units
        self._available_units = available_units

    @property
    def code(self):
        return self._code

    @property
    def name(self):
        return self._name

    @property
    def category(self):
        return self._category

    @property
    def total_units(self):
        return self._total_units

    @property
    def available_units(self):
        return self._available_units

    def checkout(self):
        if self.available_units == 0:
            raise ValueError("No units are available.")
        self._available_units -= 1

    def checkin(self):
        if self.available_units >= self.total_units:
            raise ValueError("All units are already available.")
        self._available_units += 1

    def to_dict(self):
        return {
            "code": self.code,
            "name": self.name,
            "category": self.category,
            "total_units": self.total_units,
            "available_units": self.available_units,
        }

    @classmethod
    def from_dict(cls, data):
        return cls(
            data["code"],
            data["name"],
            data["category"],
            data["total_units"],
            data["available_units"],
        )


class Loan:
    def __init__(self, loan_id, resource_code, borrower, returned=False):
        if type(loan_id) is not int or loan_id <= 0:
            raise ValueError("Loan ID must be a positive integer.")
        if not isinstance(resource_code, str) or not resource_code.strip():
            raise ValueError("Resource code is required.")
        if not isinstance(borrower, str) or not borrower.strip():
            raise ValueError("Borrower name is required.")
        if type(returned) is not bool:
            raise ValueError("Returned flag must be boolean.")

        self.loan_id = loan_id
        self.resource_code = resource_code.strip()
        self.borrower = borrower.strip()
        self._returned = returned

    @property
    def returned(self):
        return self._returned

    def mark_returned(self):
        if self._returned:
            raise ValueError("Loan has already been returned.")
        self._returned = True

    def to_dict(self):
        return {
            "loan_id": self.loan_id,
            "resource_code": self.resource_code,
            "borrower": self.borrower,
            "returned": self.returned,
        }

    @classmethod
    def from_dict(cls, data):
        return cls(
            data["loan_id"],
            data["resource_code"],
            data["borrower"],
            data["returned"],
        )
~~~

**Por qué se diseñó así:** cada objeto `Resource` representa un recurso con un estado propio, expone propiedades de solo lectura y modifica la disponibilidad únicamente mediante operaciones que validan las reglas. `Loan` representa una operación identificable; no reemplaza al recurso y controla la transición al estado devuelto mediante `mark_returned()`. La tupla `CATEGORIES` expresa un catálogo fijo de categorías para el ejemplo. Los métodos `to_dict()` y `from_dict()` convierten objetos a datos simples y los reconstruyen aplicando las validaciones del constructor.

### A.2. `inventory.py` — Colaboración entre clases y colección central

~~~python
from resource import Loan, Resource


class Inventory:
    def __init__(self):
        self._resources = {}
        self._loans = {}

    def add_resource(self, resource):
        if not isinstance(resource, Resource):
            raise TypeError("Expected a Resource instance.")
        if resource.code in self._resources:
            raise ValueError("Resource code already exists.")
        self._resources[resource.code] = resource

    def get_resource(self, code):
        if code not in self._resources:
            raise ValueError("Resource does not exist.")
        return self._resources[code]

    def list_resources(self):
        return sorted(self._resources.values(), key=lambda item: item.code)

    def list_loans(self):
        return sorted(self._loans.values(), key=lambda item: item.loan_id)

    def checkout(self, code, borrower):
        resource = self.get_resource(code)
        loan_id = max(self._loans, default=0) + 1
        loan = Loan(loan_id, code, borrower)
        resource.checkout()
        self._loans[loan.loan_id] = loan
        return loan

    def return_loan(self, loan_id):
        loan = self._loans.get(loan_id)
        if loan is None:
            raise ValueError("Loan does not exist.")
        if loan.returned:
            raise ValueError("Loan has already been returned.")

        resource = self._resources[loan.resource_code]
        resource.checkin()
        loan.mark_returned()

    def used_categories(self):
        return {resource.category for resource in self._resources.values()}

    def available_resources(self):
        return [
            resource
            for resource in self._resources.values()
            if resource.available_units > 0
        ]

    def units_by_category(self):
        result = {}
        for resource in self._resources.values():
            result[resource.category] = (
                result.get(resource.category, 0) + resource.total_units
            )
        return result

    def to_dict(self):
        return {
            "resources": [item.to_dict() for item in self.list_resources()],
            "loans": [item.to_dict() for item in self.list_loans()],
        }

    @classmethod
    def from_dict(cls, data):
        if not isinstance(data, dict):
            raise ValueError("Inventory data must be an object.")
        resources = data.get("resources")
        loans = data.get("loans")
        if not isinstance(resources, list) or not isinstance(loans, list):
            raise ValueError("Inventory collections must be lists.")

        inventory = cls()

        for entry in resources:
            inventory.add_resource(Resource.from_dict(entry))

        for entry in loans:
            loan = Loan.from_dict(entry)
            if loan.loan_id in inventory._loans:
                raise ValueError("Duplicate loan ID.")
            if loan.resource_code not in inventory._resources:
                raise ValueError("Loan references an unknown resource.")
            inventory._loans[loan.loan_id] = loan

        for resource in inventory._resources.values():
            active_loans = sum(
                1
                for loan in inventory._loans.values()
                if loan.resource_code == resource.code and not loan.returned
            )
            borrowed_units = resource.total_units - resource.available_units
            if active_loans != borrowed_units:
                raise ValueError("Inventory and active loans are inconsistent.")

        return inventory
~~~

**Por qué dos diccionarios:** el código del recurso y el identificador del préstamo son claves únicas, por lo que pueden consultarse directamente. Los métodos de consulta permiten crear listas derivadas; `used_categories()` emplea una comprensión de conjunto y `units_by_category()` construye un reporte agrupado.

**Por qué validar al recuperar:** leer un JSON sintácticamente válido no garantiza que los datos tengan sentido. `from_dict()` construye un inventario nuevo y comprueba la coherencia entre unidades prestadas y préstamos activos antes de devolverlo como estado utilizable. De esta forma no se acepta una carga parcial.

### A.3. `storage.py` — Guardado y recuperación con JSON

~~~python
import json
from pathlib import Path

from inventory import Inventory


DATA_PATH = Path(__file__).resolve().parent / "data" / "state.json"


def load_inventory(path=DATA_PATH):
    if not path.exists():
        return Inventory()

    try:
        with path.open("r", encoding="utf-8") as file:
            data = json.load(file)
        return Inventory.from_dict(data)
    except (OSError, ValueError, KeyError, TypeError) as error:
        raise RuntimeError("Cannot load inventory safely.") from error


def save_inventory(inventory, path=DATA_PATH):
    temporary_path = path.with_suffix(path.suffix + ".tmp")

    try:
        path.parent.mkdir(parents=True, exist_ok=True)
        with temporary_path.open("w", encoding="utf-8") as file:
            json.dump(
                inventory.to_dict(),
                file,
                ensure_ascii=False,
                indent=2,
            )
        temporary_path.replace(path)
    except OSError as error:
        raise RuntimeError("Cannot save inventory.") from error
~~~

**Por qué `pathlib`:** permite ubicar el almacenamiento con respecto al módulo, sin depender de la carpeta desde la que se ejecute el comando. **Por qué archivo temporal:** se prepara el contenido antes de sustituir el anterior; esto reduce el riesgo de dejar un JSON incompleto durante una escritura normal. No sustituye una solución integral de transacciones ni copias de seguridad.

Si un archivo existente está dañado, `load_inventory()` genera un error en lugar de asumir que debe borrarse. La interfaz del anexo detiene el inicio para no sobrescribir datos potencialmente recuperables.

### A.4. `reports.py` — CSV, herencia y polimorfismo

~~~python
import csv
from pathlib import Path


REPORT_PATH = Path(__file__).resolve().parent / "reports" / "resources.csv"


class BaseReport:
    def generate(self, inventory):
        raise NotImplementedError


class TextReport(BaseReport):
    def generate(self, inventory):
        resources = inventory.list_resources()
        total_units = sum(item.total_units for item in resources)
        available_units = sum(item.available_units for item in resources)

        lines = [
            f"Resources: {len(resources)}",
            f"Total units: {total_units}",
            f"Available units: {available_units}",
            f"Borrowed units: {total_units - available_units}",
            f"Categories: {len(inventory.used_categories())}",
        ]
        for category, count in sorted(inventory.units_by_category().items()):
            lines.append(f"{category}: {count} total units")
        return "\n".join(lines)


class CsvReport(BaseReport):
    def __init__(self, path=REPORT_PATH):
        self.path = path

    def generate(self, inventory):
        self.path.parent.mkdir(parents=True, exist_ok=True)

        with self.path.open("w", newline="", encoding="utf-8") as file:
            writer = csv.DictWriter(
                file,
                fieldnames=[
                    "code",
                    "name",
                    "category",
                    "total_units",
                    "available_units",
                    "borrowed_units",
                ],
            )
            writer.writeheader()
            for resource in inventory.list_resources():
                writer.writerow({
                    "code": resource.code,
                    "name": resource.name,
                    "category": resource.category,
                    "total_units": resource.total_units,
                    "available_units": resource.available_units,
                    "borrowed_units": (
                        resource.total_units - resource.available_units
                    ),
                })

        return self.path
~~~

`TextReport` y `CsvReport` especializan `BaseReport` y ofrecen la operación `generate(inventory)`. Este diseño muestra **herencia** y **sobrescritura**; usar cualquiera de los dos reportes mediante una interfaz común ilustra **polimorfismo**. Los retornos son distintos porque un formato produce texto y el otro un archivo; esa diferencia debe ser conocida por quien llama al método.

El inventario **utiliza** objetos `Resource` y `Loan`: es composición, no herencia. Se selecciona cada relación por su significado, no por cumplir una cuota de clases.

### A.5. `main.py` — Interacción y gestión de errores

~~~python
from inventory import Inventory
from reports import CsvReport, TextReport
from resource import Resource
from storage import load_inventory, save_inventory


def read_integer(prompt):
    return int(input(prompt).strip())


def save_changes(inventory):
    try:
        save_inventory(inventory)
        print("Changes saved.")
    except RuntimeError as error:
        print(f"Warning: {error}")
        print("Changes may exist only in memory.")


def show_menu():
    print("\n=== GEARTRACK ===")
    print("1. Register resource")
    print("2. List resources")
    print("3. Check out resource")
    print("4. Return loan")
    print("5. Show summary")
    print("6. Export CSV")
    print("0. Exit")


def main():
    try:
        inventory = load_inventory()
    except RuntimeError as error:
        print(error)
        print("Application stopped to protect the existing data.")
        return

    while True:
        show_menu()
        option = input("Option: ").strip()

        try:
            if option == "1":
                code = input("Code: ").strip()
                name = input("Name: ").strip()
                category = input("Category (Equipment/Books/Supplies): ").strip()
                quantity = read_integer("Total units: ")
                inventory.add_resource(Resource(code, name, category, quantity))
                save_changes(inventory)

            elif option == "2":
                for resource in inventory.list_resources():
                    print(
                        f"{resource.code} | {resource.name} | "
                        f"{resource.available_units}/{resource.total_units}"
                    )

            elif option == "3":
                code = input("Resource code: ").strip()
                borrower = input("Borrower: ").strip()
                loan = inventory.checkout(code, borrower)
                print(f"Loan registered: {loan.loan_id}")
                save_changes(inventory)

            elif option == "4":
                loan_id = read_integer("Loan ID: ")
                inventory.return_loan(loan_id)
                print("Loan returned.")
                save_changes(inventory)

            elif option == "5":
                report = TextReport()
                print(report.generate(inventory))

            elif option == "6":
                report = CsvReport()
                path = report.generate(inventory)
                print(f"Report exported: {path}")

            elif option == "0":
                print("Goodbye.")
                break

            else:
                print("Unknown option.")

        except (ValueError, KeyError, TypeError, RuntimeError, OSError) as error:
            print(f"Operation rejected: {error}")


if __name__ == "__main__":
    main()
~~~

La interfaz concentra `input()` y los mensajes. Las reglas de negocio siguen dentro de `Resource` e `Inventory`, de modo que las mismas operaciones pueden probarse sin utilizar el menú.

El programa guarda después de cada modificación válida. Si la escritura falla, informa que el estado en memoria puede diferir de lo almacenado; no asegura falsamente que todo está persistido.

### A.6. `tests/test_inventory.py` — Casos automatizados

~~~python
import json
import tempfile
import unittest
from pathlib import Path

from inventory import Inventory
from reports import CsvReport, TextReport
from resource import Resource
from storage import load_inventory, save_inventory


class InventoryTests(unittest.TestCase):
    def setUp(self):
        self.inventory = Inventory()
        self.inventory.add_resource(
            Resource("R001", "Keyboard", "Equipment", 2)
        )

    def test_register_resource(self):
        self.assertEqual(len(self.inventory.list_resources()), 1)

    def test_reject_duplicate_code(self):
        with self.assertRaises(ValueError):
            self.inventory.add_resource(
                Resource("R001", "Other", "Equipment", 1)
            )

    def test_checkout_changes_availability(self):
        loan = self.inventory.checkout("R001", "Alex")
        self.assertEqual(loan.loan_id, 1)
        self.assertEqual(self.inventory.get_resource("R001").available_units, 1)

    def test_reject_unavailable_checkout(self):
        self.inventory.checkout("R001", "Alex")
        self.inventory.checkout("R001", "Sam")
        with self.assertRaises(ValueError):
            self.inventory.checkout("R001", "Taylor")
        self.assertEqual(len(self.inventory.list_loans()), 2)

    def test_reject_repeated_return(self):
        loan = self.inventory.checkout("R001", "Alex")
        self.inventory.return_loan(loan.loan_id)
        with self.assertRaises(ValueError):
            self.inventory.return_loan(loan.loan_id)
        self.assertEqual(self.inventory.get_resource("R001").available_units, 2)

    def test_reject_invalid_category(self):
        with self.assertRaises(ValueError):
            Resource("R002", "Chair", "Unknown", 1)

    def test_categories_and_summary(self):
        self.inventory.add_resource(
            Resource("R002", "Book", "Books", 3)
        )
        self.assertEqual(self.inventory.used_categories(), {"Equipment", "Books"})
        self.assertEqual(
            self.inventory.units_by_category(),
            {"Equipment": 2, "Books": 3},
        )
        self.assertIn("Total units: 5", TextReport().generate(self.inventory))

    def test_json_round_trip(self):
        self.inventory.checkout("R001", "Alex")
        with tempfile.TemporaryDirectory() as directory:
            path = Path(directory) / "state.json"
            save_inventory(self.inventory, path)
            restored = load_inventory(path)
            self.assertEqual(
                restored.get_resource("R001").available_units, 1
            )
            self.assertEqual(len(restored.list_loans()), 1)

    def test_damaged_json_is_not_overwritten(self):
        with tempfile.TemporaryDirectory() as directory:
            path = Path(directory) / "state.json"
            path.write_text("{invalid", encoding="utf-8")
            with self.assertRaises(RuntimeError):
                load_inventory(path)
            self.assertEqual(path.read_text(encoding="utf-8"), "{invalid")

    def test_reject_inconsistent_saved_state(self):
        data = self.inventory.to_dict()
        data["resources"][0]["available_units"] = 0
        with self.assertRaises(ValueError):
            Inventory.from_dict(data)

    def test_csv_export(self):
        with tempfile.TemporaryDirectory() as directory:
            path = Path(directory) / "resources.csv"
            CsvReport(path).generate(self.inventory)
            csv_text = path.read_text(encoding="utf-8")
            self.assertIn("borrowed_units", csv_text)
            self.assertIn("R001,Keyboard,Equipment,2,2,0", csv_text)


if __name__ == "__main__":
    unittest.main()
~~~

Estas pruebas permiten verificar tanto resultados correctos como **rechazos que no deben alterar el estado**. Por ejemplo, `test_reject_repeated_return` protege la corrección descubierta durante el Sprint 2.

Las pruebas se incluyen como parte del proyecto y no como una colección desconectada de la simulación. Un equipo real debe relacionar sus pruebas con criterios de aceptación, Issues y Pull Requests.

### A.7. Ejecución y comprobación

Desde la carpeta `geartrack`:

~~~bash
python --version
python main.py
~~~

En algunos sistemas el ejecutable se llama `python3`. Para ejecutar las pruebas:

~~~bash
python -m unittest discover -s tests -v
~~~

La biblioteca `unittest` forma parte de Python; no requiere instalación adicional.

Una demostración posible registra `R001` con dos unidades, realiza un préstamo y consulta el reporte:

~~~text
Resources: 1
Total units: 2
Available units: 1
Borrowed units: 1
Categories: 1
Equipment: 2 total units
~~~

Al devolver el préstamo, las unidades disponibles vuelven a dos. Devolverlo por segunda vez debe producir un rechazo sin alterar la disponibilidad.

Después de reiniciar, la aplicación debe cargar el JSON conservado. Si el archivo se ha dañado intencionalmente en una **copia de prueba**, el inicio debe detenerse y preservar ese archivo para evitar su sobrescritura accidental.

---

## 11. Cierre de la simulación

La enseñanza principal no consiste en repetir «Planning, Daily, Review y Retrospective», sino en comprender **para qué ocurre cada decisión y qué evidencia permite comprobarla**.

GearTrack comenzó con un problema, expresó su Product Goal, priorizó historias, entregó incrementos y registró una falla realista sin ocultarla. Luego adaptó el plan, corrigió el error, verificó el producto y preparó su entrega a partir de los registros existentes.

GitHub hizo visible el desarrollo; Scrum ayudó al equipo a **inspeccionar, aprender y adaptar**. La aplicación Python permitió convertir esas decisiones en comportamiento verificable.

### Referencias

- [Scrum Guide oficial](https://scrumguides.org/scrum-guide.html)
- [GitHub Docs — About issues](https://docs.github.com/en/issues/tracking-your-work-with-issues/about-issues)
- [GitHub Docs — About Projects](https://docs.github.com/en/issues/planning-and-tracking-with-projects/learning-about-projects/about-projects)
- [GitHub Docs — Linking a pull request to an issue](https://docs.github.com/en/issues/tracking-your-work-with-issues/using-issues/linking-a-pull-request-to-an-issue)
- [Python — Classes](https://docs.python.org/3/tutorial/classes.html)
- [Python — Errors and exceptions](https://docs.python.org/3/tutorial/errors.html)
- [Python — json](https://docs.python.org/3/library/json.html)
- [Python — csv](https://docs.python.org/3/library/csv.html)
- [Python — pathlib](https://docs.python.org/3/library/pathlib.html)
- [Python — unittest](https://docs.python.org/3/library/unittest.html)
