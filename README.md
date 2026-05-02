# 📦 Autocarga PSR-4 con Composer

> **Desarrollo de Software VII** — Universidad Tecnológica  
> Facultad de Ingeniería en Sistemas Computacionales  
> Estándar aplicado: [PSR-4 Autoloader](https://www.php-fig.org/psr/psr-4/)

---

## 📖 ¿Qué es este proyecto?

Este proyecto demuestra la implementación del estándar **PSR-4** usando **Composer Autoload**
para gestionar la carga automática de clases en PHP, eliminando por completo el uso de
`require` e `include` manuales.

---

## 🗂️ Estructura del Proyecto

```
Autocarga/
│
├── src/                          ← Código fuente del proyecto
│   ├── App/
│   │   └── User.php              → Namespace: App\User
│   └── Database/
│       └── Model/
│           └── ProductModel.php  → Namespace: Database\Model\ProductModel
│
├── vendor/                       ← Generado por Composer (NO se versiona)
│   └── autoload.php              → Autoloader PSR-4 listo para usar
│
├── composer.json                 ← Configuración del proyecto y mapeo PSR-4
├── composer.lock                 ← Versiones exactas bloqueadas
├── .gitignore                    ← Excluye vendor/
└── Prueba.php                    ← Punto de entrada / demostración
```

### Mapa Namespace → Carpeta Física (Regla PSR-4)

| Namespace Prefix | Carpeta física    | Ejemplo completo                             |
|-----------------|-------------------|----------------------------------------------|
| `App\`          | `src/App/`        | `App\User` → `src/App/User.php`              |
| `Database\`     | `src/Database/`   | `Database\Model\ProductModel` → `src/Database/Model/ProductModel.php` |

> **Regla clave PSR-4:** cada segmento del namespace después del prefijo raíz
> debe corresponder a una subcarpeta. El nombre del archivo debe ser idéntico
> al nombre de la clase, con extensión `.php`.

---

## ⚙️ Guía de Instalación

### Requisitos previos

- PHP 8.0 o superior
- [Composer](https://getcomposer.org/) instalado globalmente

### Pasos para ejecutar

```bash
# 1. Clonar el repositorio
git clone https://github.com/tu-usuario/autocarga.git
cd autocarga

# 2. Generar el autoloader
composer install
# Si solo quieres regenerar el autoloader sin instalar paquetes nuevos:
composer dump-autoload

# 3. Ejecutar la demostración
php Prueba.php
```

### Salida esperada

```
Dave
123
```

---

## 🚶 Flujo de Trabajo — Paso a Paso

A continuación se documenta el proceso seguido para construir este proyecto
desde cero, aplicando PSR-4.

### Paso 1 — Crear el proyecto y las carpetas

Se creó la carpeta raíz `Autocarga/` y dentro de ella la estructura de
directorios que respeta la jerarquía de namespaces definida por PSR-4:

```bash
mkdir -p Autocarga/src/App
mkdir -p Autocarga/src/Database/Model
```

La lógica es directa: si la clase va a pertenecer al namespace `Database\Model`,
debe vivir físicamente en la carpeta `src/Database/Model/`. PSR-4 exige que
esta correspondencia sea exacta, carácter por carácter.

---

### Paso 2 — Crear las clases PHP

**`src/App/User.php`**

```php
<?php
namespace App;

class User {
    public function getName(): string
    {
        return "Dave";
    }
}
```

**`src/Database/Model/ProductModel.php`**

```php
<?php
namespace Database\Model;

class ProductModel {
    public function getId(): int
    {
        return 123;
    }
}
```

Cada archivo declara su `namespace` en la primera línea después de `<?php`.
El namespace refleja exactamente la ruta de carpetas desde la raíz configurada.

---

### Paso 3 — Configurar `composer.json`

Se creó el archivo `composer.json` en la raíz del proyecto con el siguiente
contenido:

```json
{
    "name": "estudiante/autocarga-psr4",
    "description": "Laboratorio de Autoload PSR-4 con Composer",
    "require": {
        "php": ">=8.0"
    },
    "autoload": {
        "psr-4": {
            "App\\":      "src/App/",
            "Database\\": "src/Database/"
        }
    }
}
```

#### ¿Qué hace cada parte?

| Sección | Función |
|---------|---------|
| `"name"` | Identificador del paquete en formato `vendor/proyecto` |
| `"require"` | Declara las dependencias del proyecto (aquí solo PHP 8.0+) |
| `"autoload"` | Le indica a Composer cómo resolver nombres de clases a archivos |
| `"psr-4"` | Especifica el estándar de mapeo a utilizar |
| `"App\\\\"` | Prefijo de namespace raíz → Composer buscará clases `App\*` en `src/App/` |
| `"Database\\\\"` | Prefijo de namespace raíz → Composer buscará clases `Database\*` en `src/Database/` |

> **¿Por qué dos entradas en `psr-4`?**  
> Porque el proyecto tiene dos jerarquías de namespaces independientes:
> `App` (para lógica de aplicación) y `Database` (para acceso a datos).
> Cada una necesita su propio mapeo hacia su carpeta física.

---

### Paso 4 — Ejecutar `composer dump-autoload`

```bash
composer dump-autoload
```

Este comando lee el bloque `"autoload"` del `composer.json` y genera
automáticamente la carpeta `vendor/` con todos los archivos internos
necesarios para que PHP encuentre cada clase.

#### ¿Por qué se crea la carpeta `vendor/`?

`vendor/` es el directorio donde Composer almacena:

1. **Las dependencias de terceros** (librerías instaladas). En este proyecto
   no hay ninguna, pero si agregáramos por ejemplo `"monolog/monolog"`,
   se descargaría aquí.

2. **El autoloader generado** (`vendor/autoload.php`): un archivo PHP que
   registra en el motor de PHP una función que, cada vez que se usa una clase
   desconocida, consulta los mapas PSR-4 definidos y carga el archivo correcto
   automáticamente — sin que el programador escriba ningún `require`.

> **Importante:** `vendor/` **no debe versionarse** en Git. Cada desarrollador
> que clone el proyecto la regenera con `composer install`. Por eso existe el
> `.gitignore` que la excluye.

---

### Paso 5 — Escribir `Prueba.php`

```php
<?php

require("vendor/autoload.php");  // ← única línea de carga

use App\User;
use Database\Model\ProductModel;

$user = new User();
echo $user->getName();   // imprime: Dave
echo "\n";

$product = new ProductModel();
echo $product->getId();  // imprime: 123
echo "\n";
```

#### ¿Por qué ya no hay `require` para cada clase?

**Antes (sin Composer):**
```php
require("src/App/User.php");
require("src/Database/Model/ProductModel.php");
// ...y así por cada clase que agregues
```

**Ahora (con PSR-4):**
```php
require("vendor/autoload.php");  // una vez, para siempre
```

La sentencia `use` no carga el archivo — simplemente crea un **alias corto**
dentro del archivo actual para no tener que escribir el namespace completo
cada vez. La carga real ocurre en el momento en que se hace `new User()`,
de forma transparente y automática. Esto se llama **Lazy Loading** (carga
bajo demanda): la clase solo ocupa memoria cuando realmente se necesita.

---

## 🔍 ¿Por qué establecer rutas con namespaces en un proyecto estructurado?

En proyectos reales con decenas o cientos de clases, los namespaces cumplen
tres funciones críticas:

**1. Evitan colisiones de nombres**  
Puedes tener `App\Model\User` y `Database\Model\User` sin que PHP se confunda,
porque el nombre completo (namespace + clase) es único.

**2. Expresan la arquitectura del proyecto**  
El namespace `Database\Model\ProductModel` comunica de inmediato que esa clase
pertenece a la capa de base de datos, específicamente al patrón Model. Es
documentación vivida dentro del código.

**3. Permiten escalar sin tocar archivos existentes**  
Para agregar una nueva clase `App\Services\PaymentService` basta con crear
`src/App/Services/PaymentService.php` con el namespace correcto. Composer la
encuentra automáticamente — no hay que modificar ningún archivo de configuración
ni agregar ningún `require`.

---

## 📊 Conclusiones Técnicas

### Mantenibilidad
Agregar nuevas clases al proyecto no requiere modificar ningún archivo de
configuración global. Basta con respetar la convención de carpetas y declarar
el namespace correcto. El autoloader de Composer resuelve todo en tiempo de
ejecución.

### Eficiencia de Memoria — Lazy Loading
Composer solo carga en memoria las clases que realmente se instancian durante
una petición. En un proyecto con 200 clases donde una petición específica
solo usa 15, las otras 185 nunca se cargan, reduciendo el consumo de RAM y
mejorando el tiempo de respuesta del servidor.

### Estandarización PSR-4
Seguir PSR-4 garantiza interoperabilidad con el ecosistema PHP completo.
Frameworks como Laravel y Symfony, y miles de paquetes en Packagist, siguen
este mismo estándar. Esto permite integrar cualquier librería externa sin
conflictos y facilita la incorporación de nuevos desarrolladores al equipo.

---

## 📄 Rúbrica Cubierta

| Criterio | Evidencia |
|----------|-----------|
| E.1 Evidencias | README con estructura, flujo de trabajo y código documentado |
| E.2 Ejecución | `php Prueba.php` imprime `Dave` y `123` sin errores |
| E.3 Higiene del repo | `vendor/` excluida mediante `.gitignore` |
