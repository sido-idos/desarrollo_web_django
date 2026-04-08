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