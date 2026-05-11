# Migración de Base de Datos Relacional a NoSQL
### Persistencia de Información — Tercer Prototipo 2026A
**Universidad de Ibagué · Facultad de Ingeniería**

---

## Descripción

Aplicación desarrollada en Java que migra datos de una base de datos relacional **Oracle XE** a una base de datos NoSQL **MongoDB**. El sistema lee las tablas `FARMACIA`, `PROVEEDOR`, `MEDICAMENTO` y `VENTA`, y las transforma en tres colecciones de documentos, embebiendo las ventas dentro de cada farmacia.

---

## Estructura del proyecto

```
migrar/
├── src/
│   ├── main/java/com/migrar/
│   │   ├── Data/
│   │   │   ├── DatabaseConectionRelacional.java   # Conexión a Oracle
│   │   │   └── DatabaseConectionNoRelacional.java # Conexión a MongoDB
│   │   └── Migrar.java                            # Lógica de migración
│   └── test/java/com/migrar/
│       └── AppTest.java                           # Menú principal
├── pom.xml
└── Script.txt                                     # Script SQL (tablas + datos)
```

---

## Modelo de datos

### Base de datos relacional (Oracle)

| Tabla | Descripción |
|-------|-------------|
| `FARMACIA` | Información de cada farmacia (NIT como PK) |
| `PROVEEDOR` | Datos de los proveedores de medicamentos |
| `MEDICAMENTO` | Catálogo de medicamentos con FK a FARMACIA y PROVEEDOR |
| `VENTA` | Registro de ventas con FK a FARMACIA |

### Base de datos NoSQL (MongoDB)

| Colección | Descripción |
|-----------|-------------|
| `Farmacias` | Documento de farmacia con array de ventas embebidas |
| `Medicamentos` | Documentos de medicamentos con referencias |
| `Proveedores` | Documentos de proveedores |

Cada documento generado incluye un `_id` de tipo `ObjectId` nativo de MongoDB.

**Ejemplo de documento en `Farmacias`:**
```json
{
  "_id": ObjectId("..."),
  "nit": 800123456,
  "razonSocial": "Farmacia El Ahorro",
  "representanteLegal": "Andrés Pérez",
  "direccion": "Carrera 10 #5-20",
  "estado": "AC",
  "ventas": [
    {
      "_id": ObjectId("..."),
      "id": 101,
      "fechaVenta": "2026-05-10T08:30:00.125",
      "cantidad": 5,
      "total": 25000.0
    }
  ]
}
```

---

## Requisitos previos

- Java 8 o superior
- Apache Maven 3.x
- Oracle Database XE (con el esquema configurado)
- MongoDB Community Server (local en puerto `27017`)

---

## Configuración

### 1. Base de datos relacional (Oracle)

Ejecuta el archivo `Script.txt` en tu cliente SQL (SQL Developer, SQLPlus, etc.):

```sql
-- Crea las tablas e inserta los registros de prueba
@Script.txt
```

> El script es secuencial y no presenta errores al ejecutarse en lote.

### 2. Conexión a Oracle

Edita el archivo `DatabaseConectionRelacional.java` con tus credenciales:

```java
private static final String DB_URL = "jdbc:oracle:thin:@localhost:1521:XE";
private static final String USER   = "TU_USUARIO";
private static final String PASS   = "TU_CONTRASEÑA";
```

### 3. Conexión a MongoDB

Edita el archivo `DatabaseConectionNoRelacional.java` si tu MongoDB no corre en el puerto por defecto:

```java
private static final String CONNECTION_STRING = "mongodb://localhost:27017";
private static final String DATABASE_NAME     = "PER2026";
```

---

## Instalación y ejecución

### Compilar el proyecto con Maven

```bash
mvn clean compile
```

### Ejecutar la aplicación

```bash
mvn test
```

Al ejecutar verás el siguiente menú en consola:

```
==========================================
   SISTEMA DE MIGRACIÓN DE DATOS
==========================================
  1. Migrar datos
  2. Salir
==========================================
Seleccione una opción:
```

Selecciona `1` para iniciar la migración. Al finalizar verás:

```
==========================================
MIGRACIÓN FINALIZADA
Hora de inicio : 10:35:42
Hora de fin    : 10:35:43
Total registros procesados: 30
Tiempo total: 1.243 segundos
==========================================
```

---

## Dependencias (pom.xml)

| Dependencia | Versión | Uso |
|-------------|---------|-----|
| `mongodb-driver-sync` | 5.0.1 | Driver oficial de MongoDB para Java |
| `ojdbc8` | 19.3.0.0 | Driver JDBC de Oracle |
| `junit` | 4.13.2 | Framework de pruebas |

---

## Autores

Desarrollado como parte del **Tercer Prototipo 2026A** del curso Persistencia de Información.

**Universidad de Ibagué — Facultad de Ingeniería**
