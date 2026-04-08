# Acceso a datos

## Integración con bases de datos
---

- Django ofrece soporte nativo para varios motores de bases de datos.
- La configuración se realiza principalmente en el archivo settings.py del proyecto.

## Comparativa de Motores de Bases de Datos
---

### Soporte bases de datos relacionales SQL

1. Oracle:
    - Características: Robusto, escalable, con alta seguridad y funcionalidades empresariales avanzadas.
    - Ventajas: Ideal para entornos con altas demandas de rendimiento y seguridad, y aplicaciones corporativas de gran escala.
    - Recomendado: Grandes sistemas empresariales y aplicaciones críticas.
2. MySQL:
    - Características: Fácil de usar, con un rendimiento sólido y una amplia comunidad de soporte.
    - Ventajas: Buen balance entre eficiencia y facilidad de administración, ideal para la mayoría de las aplicaciones web.
    - Recomendado: Sitios web y aplicaciones que requieren rapidez y confiabilidad en operaciones comunes.
3. PosgreSQL:
    - Características: Conforme a SQL, altamente extensible y con soporte para operaciones complejas y datos no estructurados (como JSON).
    - Ventajas: Funciones avanzadas y robustez en el manejo de datos complejos, optimización de consultas y soporte a nivel empresarial.
    - Recomendado: Proyectos que demandan consultas avanzadas y una estructura de datos compleja.

    ```sql
        DATABASES = {
            'default': {
                'ENGINE': 'django.db.backends.postgresql',
                'NAME': 'nombre_base_de_datos',
                'USER': 'tu_usuario',
                'PASSWORD': 'tu_contraseña',
                'HOST': 'localhost',
                'PORT': '5432',
            }
        }
    ```
4. SQLite:
    - Características: Motor ligero sin necesidad de servidor, integrado em Python.
    - Ventajas: Instalación y configuración inmediatas, ideal para desarrollo, pruebas y aplicaciones de bajo tráfico.
    - Recomendado: Prototipos, desarrollo local y aplicaciones con necesidades mínimas de concurrencia.

### Soporte para Bases NoSQL

Python ofrece extensiones que permiten trabajar con bases NoSQL. Proyectos como Django permiten integrar MongoDB, aprovechando el ORM de Django para trabajar con datos no estructurados.

Usar MongoDB con Django:

1. Instalar Django:
```bash
pip install django
```
2. Configura el archivo settings.py:
```sql
    DATABASES = {
        'default': {
            'ENGINE': 'djongo',
            'NAME': 'nombre_base_de_datos',
        }
    }
```

### Paquetes de Instalación y Configuración

La interacción con la BD requiere la instalación de paquetes adicionales que actúan como puente entre Django y el motor de base de datos, los que varían según el motor.

- PostgreSQL:
    - Instala el paquete `psycopg2` con `pip install psycopg2`
    > Nota: Existen también versiones precompiladas (como `psycopg2-binary`) que pueden facilitar la instalación en algunos entornos.
- MySQL:
    - Instala el paquete `mysqlclient` con `pip install mysqlclient`
- SQLite:
    - No requiere instalación adicional, ya que se incluye con la distribución de Python
- Oracle:
    - Instala `cx_Oracle` con `pip install cx_Oracle`

## Django como ORM
---

- El ORM (Object-Relational Mapping) de Django permite interactuar con la BD usando objetos de Python, eliminando la necesidad de escribir consultas SQL manualmente. 
- Se crean "modelos" que definen la estructura de la tabla en la BD y permite generar automáticamente métodos para intreactuar con la BD.
- Ventajas:
    - Abstracción y Portabilidad: Cambio de motor de base de datos sin modificar el código de acceso a datos.
    - Seguridad: Minimiza riesgos de inyección SQL mediante la gestión segura de parámetros.
    - Simplicidad y Eficiencia: Permite definir modelos como clases de Python.
    - Optimización de Consultas: Soporta técnicas como lazy evaluation, prefetch_related y select_related para mejorar el rendimiento.

Ejemplo de definición de un modelo en Django:

```python
from django.db import models

class Producto(models.Model):
    nombre = models.CharField(max_length=100)
    precio = models.DecimalField(max_digits=8, decimal_places=2)
    stock = models.IntegerField()
    
    def __str__(self):
        return self.nombre
```

## Migraciones en Django
---

- Mecanismo que utiliza Django para aplicar de forma
controlada y reversible cambios en la estructura de la BD.
- Cada modificación genera un archivo de migración que documenta y ejecuta los cambios necesarios.
- Pasos clave en la migración:
    1. Crear migraciones ejecutando `python manage.py makemigrations` para detectar cambios y generar archivos de migración.
    2. Aplicar migraciones con `python manage.py migrate` actualizando la estructura de la BD.
    3. Revertir migraciones (si es necesario) con `python manage.py migrate <app_name> <migration_number>`.


## Sintaxis de Consultas en el ORM de Django
---

- Querysets Lazy: Las consultas se ejecutan solo cuando se requieren, optimizando el rendimiento.
- Encadenamiento de Filtros: Permite combinar múltiples condiciones de forma legible.
- Métodos de consulta Avanzados: Funciones como update(),
delete(), select_related() y prefetch_related() facilitan operaciones complejas.
- Ejemplos prácticos:
    - Obtener todos los registros:
    ```python
    productos = Producto.objects.all()
    ```
    - Filtrar registros (por ejemplo, productos con precio
    mayor a 100):
    ```python
    productos_caros = Producto.objects.filter(precio__gt=100)
    ```
    - Ordenar resultados:
    ```python 
    productos_ordenados = Producto.objects.order_by('nombre')
    ```
    - Consultas complejas con Q objects:
    ```python
    from django.db.models import Q
    
    productos_filtrados = Producto.objects.filter(Q(precio__gt=50) & Q(stock__gte=10))
    ```
## Ejecución de Queries SQL en Django
---

Django proporciona dos métodos principales para ejecución de consultas directas en SQL:
1. raw(): Permite ejecutar una consulta SQL personalizada y obtener instancias del modelo resultante.
Ejemplo:
```python
for producto in Producto.objects.raw(
    'SELECT * FROM miapp_producto WHERE precio > %s', [100]
    ):
print(producto.nombre)
```

2. cursor con connection.cursor(): Permite un mayor control sobre la ejecución de la consulta y la manipulación de resultados.
Ejemplo:
```python
from django.db import connection

with connection.cursor() as cursor:
    cursor.execute("SELECT COUNT(*) FROM miapp_producto")
    row = cursor.fetchone()
    print("Número de productos:", row[0])
```

## El ORM de Django
---

### ¿Qué es un Modelo?

Es la representación en código de una entidad que se
almacenará en la base de datos (tabla). Cada modelo se define como una clase que hereda de `models.Model` y sus atributos se traducen en columnas en la tabla correspondiente. El uso de modelos permite abstraer SQL y trabajar con objetos de Python.

### Definición de Campos y Tipos de Datos en el Modelo

Cada atributo del modelo se define mediante un campo, y cada campo determina el tipo de dato que se almacenará y las restricciones asociadas. 
Algunos de los campos más comunes son:

- CharField: Para almacenar cadenas de texto.
- IntegerField: Para almacenar números enteros.
- DecimalField: Para almacenar números decimales, definiendo parámetros como `max_digits` (número total de dígitos) y
`decimal_places` (dígitos decimales).
- DateField / DateTimeField: Para almacenar fechas o fechas con hora.
- EmailField: Para almacenar direcciones de correo
electrónico, con validación incorporada.

Ejemplo de un modelo Cliente con campo de nombre, edad y email:
```python
from django.db import models

class Cliente(models.Model):
    nombre = models.CharField(max_length=100)
    edad = models.IntegerField()
    email = models.EmailField(unique=True)
    
    def __str__(self):
        return self.nombre
```

### Opciones en los Campos

Algunas de las opciones más utilizadas son:

- **max_length:** Especifica la longitud máxima para campos de tipo CharField o TextField.
- **default:** Define un valor por defecto en caso de no proporcionar uno al crear el objeto.
- **null:** Permite que el campo acepte valores nulos.
- **blank:** Determina si el campo puede quedar vacío en formularios (a nivel de validación en la aplicación).
- **unique:** Asegura que cada valor en ese campo sea único en la tabla.
- **verbose_name:** Permite asignar un nombre legible para el campo, que se utiliza en la interfaz administrativa de Django.

## Manejo de Claves Primarias
---

### Llaves Primarias Simples

- Django crea automáticamente un campo `id` como llave primaria para cada modelo (por defecto), pero es posible definir un campo específico como llave primaria usando el parámetro `primary_key = True`.

```python
class Producto(models.Model):
    codigo = models.CharField(max_length=10, primary_key=True)
    nombre = models.CharField(max_length=100)
    precio = models.DecimalField(max_digits=8, decimal_places=2)
    
    def __str__(self):
        return self.nombre
```

### Llaves Primarias Compuestas (Columnas Múltiples)

- Django no soporta de forma nativa las llaves primarias compuestas, se puede simular este comportamiento utilizando la opción `unique_together` en la clase interna Meta del modelo asegurando que la combinación de dos o más
campos sea única en la tabla.

```python
class Inscripcion(models.Model):
    estudiante = models.ForeignKey('Estudiante', on_delete=models.CASCADE)
    curso = models.ForeignKey('Curso', on_delete=models.CASCADE)
    fecha_inscripcion = models.DateField()
    
    class Meta:
    unique_together = (('estudiante', 'curso'),)
```

## Operaciones CRUD en el Modelo
---

1. Create (Crear): Para insertar un nuevo registro en la base de datos, existen dos métodos principales: 
    1. save() de una instancia.
    ```python
    cliente = Cliente(nombre="Juan Pérez", edad=30, email="juan@example.com")
    cliente.save()
    ```
    2. create() del administrador de modelos.
    ```python
    cliente = Cliente.objects.create(nombre="Ana Gómez", edad=25,email="ana@example.com")
    ```

2. Read (Leer): Para obtener registros desde la base de datos, se pueden utilizar métodos del
administrador de modelos (objects).
    1. Obtener todos los registros:
    ```python
    clientes = Cliente.objects.all()
    ```
    2. Obtener un registro específico:
    ```python
    cliente = Cliente.objects.get(email="juan@example.com")
    ```
    3. Filtrar registros según criterios:
    ```python
    clientes_mayores = Cliente.objects.filter(edad__gt=25)
    ```

3. Update (Actualizar): Para modificar un registro existente, se debe primero obtener la instancia correspondiente, modificar los atributos deseados y luego llamar al método `save()`.

```python
cliente = Cliente.objects.get(email="juan@example.com")
cliente.edad = 31
cliente.save()
```

4. Delete (Borrar): Eliminar un registro es tan sencillo como obtener la instancia y llamar al método `delete()`.

```python
cliente = Cliente.objects.get(email="ana@example.com")
cliente.delete()
```

## Profundización y Buenas Prácticas
---

### Validación y Gestión de Errores
Es importante manejar excepciones durante las operaciones CRUD para garantizar la estabilidad de la aplicación. Por ejemplo, al usar `get()` es recomendable capturar la excepción `DoesNotExist` si no se encuentra el registro:

```python
from django.core.exceptions import ObjectDoesNotExist

try:
    cliente = Cliente.objects.get(email="noexiste@example.com")
except ObjectDoesNotExist:
    print("El cliente no existe.")
```

### Uso de Migraciones para Gestionar Cambios en el Modelo

Cada vez que se modifica un modelo, se debe generar una migración que actualice la estructura de la base de datos con:

```bash
python manage.py makemigrations
python manage.py migrate
```

El uso demigraciones asegura que los cambios se apliquen de forma controlada y que se pueda revertir a estados anteriores en caso de ser necesario.

### Optimización de Consultas

El ORM de Django permite optimizar consultas utilizando métodos como `select_related()` y `prefetch_related()`, que reducen el número de consultas a la base de datos y mejoran el rendimiento de la aplicación. Estos métodos son especialmente útiles en modelos con relaciones (ForeignKey, ManyToMany).

## Relaciones: Muchos a Uno
---

Las relaciones Muchos a Uno son fundamentales cuando se necesita representar una estructura jerárquica o de dependencia, donde múltiples registros de una entidad (modelo "hijo") se vinculan a un único registro de otra entidad (modelo "padre").
La ventaja principal de esta relación es que permite centralizar la información compartida en el modelo "padre", evitando redundancias y facilitando la gestión de la información compartida.

### Implementación en Django
Se utiliza el campo ForeignKey para establecer esta relación, donde se debe especificar el comportamiento ante la eliminación del registro padre a través del parámetro `on_delete`, según:

- **CASCADE:** Eliminar en cascada todos los registros hijos si se borra el registro padre.
- **PROTECT:** Impide la eliminación del registro padre si existen registros hijos asociados.
- **SET_NULL:** Establece el valor del campo en `NULL` en los registros hijos si el registro padre se elimina (requiere que el campo permita nulos).
- **SET_DEFAULT:** Asigna un valor por defecto.

Ejemplo: Supongamos que tenemos un sistema de gestión de inventario en el que cada producto pertenece a una categoría. La implementación sería la siguiente:

```python
from django.db import models

class Categoria(models.Model):
    nombre = models.CharField(max_length=100)
    descripcion = models.TextField(blank=True, null=True)
    def __str__(self):
        return self.nombre

class Producto(models.Model):
    nombre = models.CharField(max_length=100)
    precio = models.DecimalField(max_digits=8, decimal_places=2)
    descripcion = models.TextField(blank=True, null=True)
    categoria = models.ForeignKey(Categoria, on_delete=models.CASCADE)

    def __str__(self):
        return self.nombre
```

Análisis: Cada instancia de Producto se relaciona con una instancia de Categoria. Al usar `on_delete=models.CASCADE`, se garantiza que la eliminación de una categoría elimine todos los productos asociados, manteniendo la integridad de la base de datos.

### Buenas Prácticas y Optimización

- **select_related()**: Permite hacer una unión SQL (relaciones) y reducir el número de consultas.
```python
productos = Producto.objects.select_related('categoria').all()
```

- Se debe validar que la categoría exista antes de asignarla a un producto para evitar errores en la inserción de datos.

## Relaciones Muchos a Muchos

Se emplean cuando múltiples registros de un modelo pueden estar relacionados con múltiples registros de otro modelo donde no se impone una jerarquía estricta entre las entidades. Este tipo de relación es ideal para representar escenarios de colaboración o asociación, como estudiantes inscritos en múltiples cursos, autores que colaboran en varios libros o usuarios que siguen a otros usuarios en redes sociales.

### Implementación en Django
Se utiliza el campo `ManyToManyField` para definir relaciones Muchos a Muchos, donde Django se encarga de crear automáticamente una tabla intermedia que maneja las claves foráneas de ambas entidades.

```python
from django.db import models

class Curso(models.Model):
    titulo = models.CharField(max_length=100)
    descripcion = models.TextField()

    def __str__(self):
        return self.titulo

class Estudiante(models.Model):
    nombre = models.CharField(max_length=100)
    email = models.EmailField(unique=True)
    cursos = models.ManyToManyField(Curso)

    def __str__(self):
        return self.nombre
```


### Relaciones Muchos a Muchos con Entidad Intermedia
A veces es necesario almacenar información adicional sobre la relación, por ejemplo, en una aplicación de gestión de proyectos puede ser importante registrar la fecha de asignación o el rol del empleado en cada proyecto. En estos casos, se define un modelo intermedio y se especifica mediante el parámetro `through`.

```python
from django.db import models

class Empleado(models.Model):
    nombre = models.CharField(max_length=100)

    def __str__(self):
        return self.nombre

class Proyecto(models.Model):
    titulo = models.CharField(max_length=100)
    descripcion = models.TextField()

    def __str__(self):
        return self.titulo

class Asignacion(models.Model):
    empleado = models.ForeignKey(Empleado, on_delete=models.CASCADE)
    proyecto = models.ForeignKey(Proyecto, on_delete=models.CASCADE)
    fecha_asignacion = models.DateField()
    rol = models.CharField(max_length=50)

    class Meta:
        unique_together = (('empleado','proyecto'),)

    def __str__(self):
        return f"{self.empleado.nombre} en {self.proyecto.titulo} como {self.rol}"
```

La opción `unique_together` garantiza que un empleado no se asigne más de una vez al mismo proyecto.
Se utilizan claves foráneas para establecer la relación con Empleado y Proyecto.

### Optimización y Consideraciones

- **Prefetch_related():** Cuando se recuperan datos de una relación Muchos a Muchos, se recomienda utilizar `prefetch_related()` para minimizar las consultas SQL y obtener de forma eficiente los datos relacionados.
- Es fundamental definir correctamente las restricciones de unicidad y los índices en la tabla
intermedia para mantener la integridad referencial y mejorar el rendimiento en consultas complejas.

## Relaciones Uno a Uno

Cada registro de un modelo está relacionado de manera exclusiva con un único registro de otro modelo. Este tipo de relación es útil para extender un modelo sin modificar su estructura original.

### Implementación en Django
Se implementa con el campo `OneToOneField`.

```python
from django.db import models
from django.contrib.auth.models import User

class Perfil(models.Model):
    usuario = models.OneToOneField(User, on_delete=models.CASCADE)
    direccion = models.CharField(max_length=255, blank=True, null=True)
    telefono = models.CharField(max_length=15, blank=True, null=True)
    fecha_nacimiento = models.DateField(blank=True, null=True)
    bio = models.TextField(blank=True)

    def __str__(self):
        return f"Perfil de {self.usuario.username}"
```

Detalles:
- Cada usuario del sistema tendrá un perfil único.
- La eliminación del usuario también eliminará su perfil por el uso de `on_delete=models.CASCADE`.

### Consideraciones y Buenas Prácticas

- Las relaciones Uno a Uno son ideales para extender modelos de terceros (por ejemplo, el modelo User de
Django) sin alterar su estructura interna.
- Se puede acceder de forma directa al modelo relacionado, lo que simplifica la lógica de negocio y la presentación de datos en la interfaz de usuario.

## Estrategias de Optimización y Mantenimiento
---

### Optimización de Consultas

- **select_related():** Útil para relaciones Muchos a Uno y Uno a Uno ya que reduce el número de consultas haciendo una única unión SQL.
- **prefetch_related():** Ideal para relaciones Muchos a Muchos ya que permite traer todos los datos relacionados en consultas separadas pero optimizadas.

### Índices y Restricciones

- Asegurar de que los campos utilizados en relaciones (especialmente en tablas intermedias) estén indexados mejora significativamente el rendimiento en búsquedas y filtrados.
- Restricciones como `unique_together` o `unique=True` garantizan la integridad de los datos y evitan la duplicidad en las relaciones.

### Manejo del Borrado

- El parámetro `on_delete` es crítico para definir cómo se comportan las relaciones ante la eliminación de datos.

### Documentación y Evolución del Esquema

- Mantener documentación actualizada del diseño del modelo y las relaciones implementadas facilita el mantenimiento, la colaboración y la adaptación de la base de datos a nuevas necesidades.
- Se recomienda utilizar herramientas de diagramación y comentarios en el código para describir la
función de cada relación.

## Migraciones
---

- Una migración es un archivo que Django genera automáticamente para documentar y aplicar cambios en el esquema de la base de datos.
- Cada archivo de migración contiene una serie de operaciones (como crear, modificar o eliminar
tablas y campos) que se ejecutan para transformar la estructura de la base de datos de una versión a otra.

### Funciones y Propósito

- Las migraciones aseguran que la definición de los modelos en el código se refleje fielmente en la estructura física de la base de datos, evitando discrepancias que puedan provocar errores en tiempo de ejecución.
- Cada migración representa una versión del esquema, lo que permite a los desarrolladores retroceder o avanzar en el historial de cambios de forma controlada.
- Al automatizar la aplicación de cambios, se reducen los errores humanos que podrían ocurrir al modificar la base de datos manualmente.
- El sistema de migraciones permite probar y validar los cambios en entornos de desarrollo antes de implementarlos en producción.

### Problemas que Resuelve el Sistema de Migraciones

- Sin migraciones, cualquier cambio en el modelo requeriría la actualización manual de la base de datos, lo que conlleva a errores y discrepancias.
- Las migraciones automatizan este proceso, garantizando que el esquema de la base de datos se mantenga actualizado.
- Al incluir los archivos de migración en el control de versiones, se asegura que todos
trabajen con la misma estructura, facilitando la integración y evitando conflictos.
- Si un cambio resulta problemático, las migraciones permiten revertir a una versión anterior del esquema de manera sencilla y segura.
- Cada migración actúa como un registro histórico, lo que facilita la auditoría de cambios, la identificación de errores y la planificación de futuras actualizaciones.

### Comandos Fundamentales en la Gestión de Migraciones

1. **makemigrations:** Analiza los modelos de la aplicación y detecta cualquier cambio con respecto a las migraciones previamente aplicadas. Luego, genera archivos de migración que contienen las instrucciones para transformar el esquema de la base de datos.

Proceso:
    1. Realizar Cambios en el Modelo
    2. Ejecutar `python manage.py makemigrations`
    3. Generación del archivo de migración:
    4. Revisión del archivo:

2. **migrate:** Aplica las migraciones pendientes al esquema de la base de datos y ejecutar cada operación en el orden adecuado, actualizando el registro interno de migraciones aplicadas.

Proceso:
1. Aplicación de Migraciones con `python manage.py migrate`
2. Verificación del estado utilizando herramientas de administración de bases de datos o el panel de administración de Django.

### Migraciones en Entornos Colaborativos

Cuando se clona un repositorio o se integran cambios de otros desarrolladores, es probable que la base de datos local no esté actualizada, por lo que el comando `migrate` sincronizará el esquema con la última versión de los modelos.

**Caso Práctico 1:** Agregar un nuevo campo a un Modelo. Supongamos que tenemos un modelo básico para Cliente:

```python
from django.db import models

class Cliente(models.Model):
    nombre = models.CharField(max_length=100)
    email = models.EmailField(unique=True)
```

Decidimos agregar un nuevo campo, fecha_nacimiento, para almacenar la fecha de nacimiento del cliente:

```python
class Cliente(models.Model):
    nombre = models.CharField(max_length=100)
    email = models.EmailField(unique=True)
    fecha_nacimiento = models.DateField(null=True, blank=True)
```

Después de realizar el cambio, se ejecuta:

```bash
python manage.py makemigrations
```

El sistema genera un archivo de migración que contiene una operación como:

```python
migrations.AddField(
    model_name='cliente',
    name='fecha_nacimiento',
    field=models.DateField(null=True, blank=True),
),
```

Luego, al ejecutar:

```bash
python manage.py migrate
```

la columna fecha_nacimiento se añade a la tabla correspondiente en la base de datos.

**Caso Práctico 2:** Modificar el Tipo de Dato de un Campo, por ejemplo, cambiar un campo de CharField a TextField para permitir textos más largos. Tras modificar el modelo, `makemigrations` detectará el cambio
y generará una migración que, al aplicarse, ajustará el esquema de la base de datos sin perder la información existente. Si se detecta un problema tras aplicar una migración, se puede revertir a una
versión anterior utilizando:

```bash
python manage.py migrate <nombre_app> <número_de_migración>
```

## Buenas Prácticas en la Gestión de Migraciones
---

### Control de Versiones y Sincronización

- Asegúrate de subir todos los archivos de migración al sistema de control de versiones (por ejemplo, Git) para que todo el equipo esté sincronizado.
- Siempre revisa los archivos generados antes de aplicarlos, especialmente en entornos de producción, para garantizar que se reflejen correctamente los cambios deseados.

### Cambios Incrementales y Migraciones Atómicas

- Realiza cambios pequeños en lugar de grandes modificaciones acumuladas.
- Cada migración debe ejecutarse como una transacción completa; si falla una operación, toda la migración se revierte, asegurando la consistencia del esquema.

### Pruebas y Validación en Entornos de Desarrollo

- Aplica y prueba migraciones en entornos de desarrollo o *staging* antes de implementarlas en producción.
- Integra la ejecución de `makemigrations` y `migrate` en tus pipelines de integración y despliegue continuo para detectar errores de forma temprana.

### Documentación y Comunicación

- Aunque las migraciones son generadas automáticamente, documenta los cambios importantes y las razones detrás de ellos para facilitar la revisión y el mantenimiento a largo plazo.
- Mantén un historial claro y organizado de las migraciones para poder auditar y comprender la evolución del esquema de la base de datos.

## Aspectos Avanzados en Migraciones
---

### Gestión de Dependencias entre Migraciones

- Cada archivo de migración puede depender de migraciones previas, y Django gestiona estas dependencias automáticamente. En proyectos complejos con múltiples aplicaciones, es importante entender el orden de ejecución y, en ocasiones, ajustar manualmente las dependencias en el archivo de migración para evitar conflictos.

### Personalización de Operaciones de Migración

- Django permite personalizar las migraciones mediante operaciones personalizadas, como por ejemplo, crear una operación que modifique datos antes de aplicar un cambio en el esquema.

### Migraciones Squashing

- Django ofrece la opción de *"squashing"* (fusionar) migraciones, que permite consolidar varias migraciones en un solo archivo, simplificando el mantenimiento y mejora el rendimiento durante la inicialización de la base de datos en nuevos entornos.

### Uso de Opciones Avanzadas en migrate

* **--plan:** Ejecuta `python manage.py migrate --plan` para ver el plan de migración sin aplicarlo.
* **--fake:** Permite marcar una migración como aplicada sin ejecutar realmente las operaciones. Esto puede ser útil cuando se corrige manualmente el esquema de la base de datos y se desea sincronizar el
estado interno de migraciones sin cambiar el contenido de la base de datos.

### Integración con Herramientas de Gestión de Bases de Datos

Para grandes proyectos, es recomendable utilizar herramientas de gestión y
visualización de bases de datos que permitan inspeccionar el estado del
esquema antes y después de aplicar migraciones. Esto ayuda a detectar posibles
inconsistencias y optimizar el rendimiento.
pág 10
7. Estrategias para el
Mantenimiento a Largo Plazo
7.1 Monitoreo y Auditoría del Esquema
Realiza auditorías periódicas del esquema de la base de datos y revisa el historial
de migraciones para asegurarte de que el sistema sigue siendo coherente y
eficiente.
7.2 Capacitación del Equipo
Es importante que todo el equipo de desarrollo comprenda el funcionamiento del
sistema de migraciones y las mejores prácticas asociadas. Esto incluye la
resolución de conflictos, la aplicación de rollback y el manejo de migraciones
personalizadas.
7.3 Actualización Continua
A medida que Django evoluciona, el sistema de migraciones también puede
mejorar. Mantente actualizado con las nuevas versiones y funcionalidades, y
considera refactorizar las migraciones antiguas (por ejemplo, utilizando
squashing) para aprovechar las últimas mejoras y optimizaciones.
Evaluación de Criterios
●
●
4.1 Describir el Significado de una Migración y el Problema que
Resuelve:
Se evaluará la capacidad para explicar de manera clara cómo las
migraciones mantienen la sincronía entre el código y la base de datos, y
cómo ayudan a resolver problemas relacionados con la evolución del
esquema en entornos colaborativos y en producción.
4.2 Utilizando makemigrations para Organizar las Actualizaciones al
Esquema de Base de Datos:
Se analizará la correcta utilización del comando makemigrations, la
revisión y validación de los archivos generados, y la aplicación ordenada
de los cambios mediante migrate para garantizar que el proceso de
actualización del esquema sea seguro, eficiente y reproducible.
pág 11