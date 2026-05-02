# Autocarga PSR-4 con Composer - Luis Jiménez

Esta guía demuestra la implementación del estándar **PSR-4** usando **Composer Autoload**
para gestionar la carga automática de clases en PHP, eliminando por completo el uso de
`require` e `include` manuales.

## Estructura del Proyecto con el estándar PSR-4
Para el ejemplo propuesto, en base a la estructura PSR-4 creamos carpetas y subcarpetas que nos permitirán idividualizar e identificar secciones del código para trabajar. La función del autoload en este laboratorio es simple: en vez de estar escribiendo require para cada archivo PHP que necesites usar, Composer se encarga de encontrar y cargar las clases automáticamente cuando las necesitas.

El estandar PSR-4 define una forma automatizada de cargar clases en PHP mediante un mapeo entre namespaces y directorios del sistema de archivos.

<img width="563" height="286" alt="image" src="https://github.com/user-attachments/assets/40ee0d9a-1b46-443f-bbca-d79ac830f01a" />

---

## Flujo de Trabajo — Paso a Paso

A continuación se documenta el proceso seguido para construir este proyecto
desde cero, aplicando PSR-4.

### Paso 1 — Crear el proyecto y las carpetas

Se creó la carpeta raíz `Autocarga/` y dentro de ella la estructura de
directorios que respeta la jerarquía de namespaces definida por PSR-4:

<img width="247" height="233" alt="Captura de pantalla 2026-05-02 014603" src="https://github.com/user-attachments/assets/3991db15-e3d5-4cf4-9c3c-05221957ed58" />

La lógica es directa: si la clase va a pertenecer al namespace `Database\Model`,
debe vivir físicamente en la carpeta `src/Database/Model/`. PSR-4 exige que
esta correspondencia sea exacta, carácter por carácter.

---

### Paso 2 — Crear las clases PHP

<img width="430" height="256" alt="Captura de pantalla 2026-05-02 014620" src="https://github.com/user-attachments/assets/b7528782-f2ae-4f2d-9551-aa825aaea09d" />

<img width="430" height="256" alt="Captura de pantalla 2026-05-02 014703" src="https://github.com/user-attachments/assets/0f3a1f43-e1f2-434d-9776-b355729b55eb" />


Cada archivo declara su `namespace` en la primera línea después de `<?php`.
El namespace refleja exactamente la ruta de carpetas desde la raíz configurada.

<img width="422" height="62" alt="image" src="https://github.com/user-attachments/assets/0e1ae57f-24a5-4329-8828-ee81120d9c44" />

Si hacemos una impresión sin el uso del composer el resultado nos dará el mismo, pero nos realentiza el estar escribiendo require en cada clase para esperaer una impresión, mejor utilicemos autoload instalandolo en la terminal, así nos agilizaremos un poco más.

---

### Paso 3 — Configurar `composer.json`

Se creó el archivo `composer.json` en la raíz del proyecto con el siguiente
contenido:

<img width="488" height="294" alt="image" src="https://github.com/user-attachments/assets/9e22ecd2-8de1-45ef-ae07-f304dac067cb" />


#### ¿Qué hace este archivo específicamente?

Le dice a Composer dónde está cada clase según su namespace. App\ la busca en src/App/ y Database\ en src/Database/. Sin esto, tendrías que usar require para cada archivo manualmente. Así obtendremos laimpresión que queremos para este ejemplo.

---

### Paso 4 — Ejecutar `composer install`

```bash
composer install
```

Este comando lee el bloque `"autoload"` del `composer.json` y genera automáticamente la carpeta `vendor/` con todos los archivos internos
necesarios para que PHP encuentre cada clase.

<img width="1466" height="204" alt="Captura de pantalla 2026-05-02 014826" src="https://github.com/user-attachments/assets/5fb2a531-f22a-4c51-a156-d2820e3ec815" />

#### ¿Por qué se crea la carpeta `vendor/`?

`vendor/` es el directorio donde Composer almacena:

1. **Las dependencias de terceros**
2. **El autoloader generado** (`vendor/autoload.php`)

<img width="307" height="412" alt="Captura de pantalla 2026-05-02 014854" src="https://github.com/user-attachments/assets/08ccf2f8-284c-43c7-a5c0-ff88a4d10bb1" />

---

### Paso 5 — Pruebas con mi archivo `Prueba.php`

<img width="589" height="344" alt="Captura de pantalla 2026-05-02 014715" src="https://github.com/user-attachments/assets/97c525b7-5fcf-4311-8228-e1fc70d3839c" />

<img width="426" height="109" alt="Captura de pantalla 2026-05-02 014727" src="https://github.com/user-attachments/assets/086eb79c-0d53-4bc1-a305-c09ebd74bf71" />

Al ejecutarlo directamente, no nos saldrá la impresión pero al estructurarlo como se menció anteriormente se remplaza el require por el formato use. Despues de ejecutarlo con la estructura correcta si nos saldrá la impresión.

<img width="479" height="390" alt="Captura de pantalla 2026-05-02 015143" src="https://github.com/user-attachments/assets/3a6c4b38-38b4-460f-80d1-3a17dd8fb8e0" />

<img width="412" height="67" alt="Captura de pantalla 2026-05-02 015200" src="https://github.com/user-attachments/assets/5d1561dd-14d5-4fa4-ae49-4f66de96dd60" />

#### ¿Por qué ya no hay `require` para cada clase?

Por que la sentencia `use` no carga el archivo, simplemente crea un **alias corto**
dentro del archivo actual para no tener que escribir el namespace completo
cada vez.

### Paso 6 — Creación del archivo gitignore
Este último antes de subirlo a mi repositorio excluimos la carpeta vendor, con que finalidad? 
Demostrar que el composer.json está bien configurado si se llega clonar un repositorio como prueba, si el proyecto funciona correctamente después de regenerar el vendor/ desde cero

---

## Conclusiones Técnicas

### Mantenibilidad
Agregar nuevas clases al proyecto no requiere modificar ningún archivo de configuración global. Basta con respetar la convención de carpetas y declarar el namespace correcto. El autoloader de Composer resuelve todo en tiempo de ejecución.

### Eficiencia de Memoria — Lazy Loading
Composer solo carga en memoria las clases que realmente se instancian durante una petición. En un proyecto con 200 clases donde una petición específica solo usa 15, las otras 185 nunca se cargan, reduciendo el consumo de RAM y mejorando el tiempo de respuesta del servidor.

### Estandarización PSR-4
Seguir PSR-4 garantiza una buena estructuración con el ecosistema PHP completo. Frameworks como Laravel sigueneste mismo estándar. Esto permite integrar cualquier librería externa sin conflictos y facilita la incorporación de nuevos desarrolladores al equipo.

---

## Información del Estudiante

| Campo | Información |
|-------|-------------|
| Nombre | Luis Jiménez |
| Correo | [luis.jimenez6@utp.ac.pa](mailto:luis.jimenez6@utp.ac.pa) |
| Curso | Desarrollo de Software 7 |
| Fecha de Ejecución del Laboratorio | 01-05-26 |
| Instructor del Laboratorio | Irina Fong |
