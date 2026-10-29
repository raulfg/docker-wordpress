# Entorno de Desarrollo Local de WordPress con Docker (Español)

Este proyecto proporciona una configuración de Docker Compose para ejecutar un entorno de desarrollo local de WordPress de forma sencilla y portable.

## ✨ Características Principales

*   **Portable:** Todo el entorno, incluyendo los archivos de WordPress y la base de datos MariaDB, se almacena dentro de la carpeta del proyecto (`./www/` para WordPress, `./data/` para la base de datos). Esto facilita la creación de copias de seguridad, compartir el entorno o moverlo entre diferentes máquinas.
*   **Aislado:** Utiliza Docker para asegurar que el entorno de desarrollo no interfiera con otras configuraciones locales o del sistema.
*   **Fácil de Usar:** Inicia todos los servicios con un simple comando (`docker compose up -d`) y detenlos con (`docker compose down`).
*   **phpMyAdmin Incluido:** Accede fácilmente a la base de datos a través de phpMyAdmin para una gestión visual.
*   **Pre-configurado:** Un entorno de WordPress listo para usar con una configuración básica.

## 📋 Requisitos Previos

Asegúrate de tener instalados:
*   [Docker](https://www.docker.com/get-started)
*   [Docker Compose](https://docs.docker.com/compose/install/) (generalmente se incluye con Docker Desktop)

## 📁 Estructura del Proyecto

*   `docker-compose.yml`: Archivo principal de configuración de Docker Compose que define los servicios (WordPress, MariaDB, phpMyAdmin).
*   `./data/`: Contiene los archivos de la base de datos MariaDB. Esta carpeta es creada por Docker al iniciar los servicios y está ignorada por Git gracias al archivo `.gitignore`.
*   `./www/`: Contiene los archivos del núcleo de WordPress, temas, plugins y archivos subidos. Esta carpeta es creada por Docker y también está ignorada por Git.
*   `./apache-config.conf`: Archivo de configuración adicional para Apache (utilizado por los contenedores de WordPress y phpMyAdmin, por ejemplo, para `ServerName`).
*   `env.sample`: Plantilla comentada de las variables que puedes sobrescribir. Se versiona, y Docker Compose no la lee nunca: cópiala a `.env` para usarla.
*   `.env`: Tus propios valores, si creas uno. Docker Compose lo lee automáticamente. Está ignorado por Git, pero sí viaja dentro de la carpeta del proyecto cuando la entregas.
*   `.gitignore`: Especifica los archivos y carpetas que Git debe ignorar (como `./data/`, `./www/` y `.env`).

## 🚀 Uso

1.  **Clonar el Repositorio:**
    La forma más sencilla es clonar el repositorio. Esto creará un nuevo directorio llamado `docker-wordpress` con los archivos del proyecto:
    ```bash
    git clone https://github.com/raulfg/docker-wordpress.git
    cd docker-wordpress
    ```

    **Alternativa: Clonar en una carpeta específica**
    Si prefieres nombrar la carpeta de tu proyecto de manera diferente o clonar en un directorio existente:
    a. Crea la carpeta de proyecto deseada (por ejemplo, `mi-proyecto-wordpress`) y navega hacia ella:
       ```bash
       mkdir mi-proyecto-wordpress
       cd mi-proyecto-wordpress
       ```
    b. Luego, clona el contenido del repositorio directamente en esta carpeta usando un `.` al final del comando:
       ```bash
       git clone https://github.com/raulfg/docker-wordpress.git .
       ```

    Si no vas a clonar, simplemente asegúrate de tener `docker-compose.yml` y `apache-config.conf` en tu directorio de trabajo.

2.  **Iniciar los Servicios:**
    Navega al directorio raíz del proyecto (donde se encuentra el archivo `docker-compose.yml`) y ejecuta:
    ```bash
    docker compose up -d
    ```
    Esto descargará las imágenes de Docker necesarias (si es la primera vez) y creará e iniciará los contenedores en segundo plano (`-d`).
    Si prefieres ver los logs de los contenedores directamente en tu terminal (modo primer plano), puedes omitir el parámetro `-d`: `docker compose up`. Para detener los servicios cuando se ejecutan en modo primer plano, presiona `Ctrl+C` en la terminal. Ten en cuenta que esto solo detiene los contenedores; para eliminarlos, necesitarás ejecutar `docker compose down` después.

3.  **Acceder a los Servicios:**
    *   **WordPress:** Abre tu navegador y ve a `http://localhost` (o `http://localhost:80`).
        *   La primera vez, serás guiado a través del proceso de instalación de WordPress.
        *   Los valores de abajo son los **predeterminados**. Si fijas alguno en un archivo `.env`, usa los tuyos — mira [Variables que puedes sobrescribir al arrancar](#variables-que-puedes-sobrescribir-al-arrancar).
            *   Nombre de la base de datos: `wordpress`
            *   Usuario: `wordpress`
            *   Contraseña: `wordpress`
            *   Host de la base de datos: `db` (el nombre del servicio de la base de datos en `docker-compose.yml`)
            *   Prefijo de tabla: `dockwp_`
    *   **phpMyAdmin:** Abre tu navegador y ve a `http://localhost:8080`.
        *   Servidor: `db`
        *   Usuario: `root`
        *   Contraseña: `password` (el valor por defecto; es lo que valga `DB_ROOT_PASSWORD`)

4.  **Detener y Eliminar los Servicios:**
    Para detener y eliminar todos los contenedores, redes y volúmenes (no los mapeados localmente en `./data/` y `./www/`) asociados a este proyecto, ejecuta en el mismo directorio:
    ```bash
    docker compose down
    ```
    Este comando se utiliza tanto si iniciaste los servicios con `docker compose up -d` (modo segundo plano) como si los ejecutaste en modo primer plano y los detuviste con `Ctrl+C`. Asegura una detención y eliminación limpias de los recursos Docker creados por este proyecto. Los datos en `./data/` (base de datos) y `./www/` (archivos de WordPress) persistirán en tu sistema local.

## 🔧 Personalización

Puedes personalizar la configuración editando el archivo `docker-compose.yml`:

*   **Versión de WordPress/MariaDB/phpMyAdmin:** Cambia las etiquetas de las imágenes (ej. `wordpress:latest`, `mariadb:11`, `phpmyadmin`).

### Variables que puedes sobrescribir al arrancar

Los puertos, el prefijo de tablas y las credenciales de la base de datos están parametrizados, así que no necesitas editar `docker-compose.yml` para cambiarlos. Todos tienen un valor por defecto, y por eso un `docker compose up` a secas funciona siempre sin ninguna preparación:

| Variable | Por defecto | Qué controla |
| --- | --- | --- |
| `WP_PORT` | `80` | Puerto donde se publica WordPress |
| `PMA_PORT` | `8080` | Puerto donde se publica phpMyAdmin |
| `WP_PREFIX` | `dockwp_` | Prefijo de las tablas de WordPress |
| `DB_NAME` | `wordpress` | Nombre de la base de datos |
| `DB_USER` | `wordpress` | Usuario con el que conecta WordPress |
| `DB_PASSWORD` | `wordpress` | Contraseña de ese usuario |
| `DB_ROOT_PASSWORD` | `password` | Contraseña de root de MariaDB, también para entrar en phpMyAdmin |

Cada credencial se declara una sola vez y la reutilizan todos los servicios que la necesitan, así que `db`, `wordpress` y `phpmyadmin` ya no pueden descuadrarse entre sí. Cambiar la contraseña es una edición, no tres.

En `env.sample` tienes una plantilla comentada con todas ellas, en inglés y español. Cópiala para partir de ahí:

```bash
cp env.sample .env
```

Ese archivo se versiona y es inofensivo, porque **Docker Compose no lo lee nunca**. Compose solo recoge un archivo llamado exactamente `.env`, así que nada de lo que escribas en `env.sample` tiene efecto hasta que lo copies. Por eso en `.gitignore` solo está `.env`: la plantilla está hecha para compartirse, tus valores reales no.

**Para un solo arranque**, ponlas delante del comando:

```bash
WP_PORT=8081 PMA_PORT=8082 docker compose up -d
```

Es la solución habitual cuando otro proyecto ya está publicando el puerto `80`.

**Para toda una sesión de terminal**, expórtalas una vez:

```bash
export WP_PORT=8081
docker compose up -d
```

**De forma permanente, para un proyecto concreto**, crea un archivo `.env` junto a `docker-compose.yml`. Docker Compose lo lee automáticamente, así que quien use esa carpeta sigue escribiendo solo `docker compose up`:

```ini
# .env
WP_PORT=8081
WP_PREFIX=acme7x_
```

Esta última opción es la adecuada cuando preparas una carpeta para otra persona: fijas los valores una vez, y ella nunca tiene que pasar una variable ni editar un archivo.

Los tres métodos se combinan, y gana el más específico: una variable puesta delante del comando sobrescribe a la misma variable del `.env`, que a su vez sobrescribe al valor por defecto. Así puedes mantener un `.env` permanente para un proyecto y arrancarlo puntualmente en otro puerto sin tocar el archivo.

> ⚠️ **`WP_PREFIX` hay que decidirlo antes de la primera instalación.** El prefijo no queda congelado en `wp-config.php` —ese archivo lee la variable en tiempo de ejecución—, pero las tablas de la base de datos se llaman físicamente `<prefijo>posts`, `<prefijo>options`, etc., y el prefijo va además incrustado en claves como `<prefijo>user_roles` y `<prefijo>capabilities`. Cambiarlo en un sitio ya instalado hace que WordPress apunte a tablas que no existen. Cambiarlo después implica renombrar todas las tablas y parchear esas claves a mano.

> ⚠️ **`WP_PORT` conviene decidirlo también antes de instalar.** WordPress guarda su propia dirección en la base de datos durante la instalación, en las opciones `siteurl` y `home`. Si instalas en el puerto `80` y más tarde arrancas con `WP_PORT=8081`, el sitio te seguirá redirigiendo a `http://localhost`. No está roto, solo apunta a la dirección antigua. Elige el puerto antes de instalar, o corrige después esas dos opciones:
>
> ```bash
> docker compose exec db mariadb -uwordpress -pwordpress wordpress \
>   -e "UPDATE dockwp_options SET option_value='http://localhost:8081' WHERE option_name IN ('siteurl','home');"
> ```

> ⚠️ **`DB_NAME`, `DB_USER`, `DB_PASSWORD` y `DB_ROOT_PASSWORD` también hay que decidirlos antes del primer arranque.** MariaDB solo los lee mientras inicializa un `./data/` vacío. Una vez que ese directorio existe, la base de datos, el usuario y las contraseñas ya están guardados dentro, y cambiar las variables solo cambia lo que WordPress *intenta* usar. El resultado es un sitio que responde `Error establishing a database connection` con HTTP 500, mientras la base de datos sigue conservando el usuario original. Para cambiar credenciales en un proyecto en marcha, cámbialas dentro de la base de datos (o desde phpMyAdmin) y mantén las variables sincronizadas, o empieza de nuevo desde un `./data/` vacío.

> ⚠️ **¿Vienes de una copia antigua de este proyecto?** Las versiones anteriores usaban `mysql:5.7`. MariaDB no puede leer un directorio de datos creado por MySQL, así que un `./data/` existente no arrancará. Volca primero la base de datos con el stack antiguo, y luego arranca desde un `./data/` vacío e impórtala de vuelta:
>
> ```bash
> docker compose exec db mysqldump -uroot -ppassword --all-databases > backup.sql
> ```

### Nota sobre las credenciales y el archivo `.env`

Los valores por defecto son genéricos y fáciles de recordar, y es algo deliberado: este es un entorno local, y unas claves sencillas mantienen trivial la puesta en marcha. Se quedan como defaults dentro de `docker-compose.yml` para que el entorno arranque siempre sin ninguna preparación.

Si llegas a poner credenciales reales, ponlas en `.env`, nunca en `docker-compose.yml`: así se quedan fuera del repositorio, porque `.env` está en `.gitignore` mientras el archivo compose sí se versiona. Ten en cuenta que eso las mantiene fuera de Git, no fuera de la carpeta: el `.env` sigue viajando dentro de la carpeta del proyecto cuando la entregas, que es justo lo que hace funcionar una carpeta ya preparada. No es una caja fuerte de secretos. Si este entorno llega a ser alcanzable desde fuera de tu propia máquina, da por públicas todas sus credenciales y cámbialas.

---

## 🪟 Notas para Windows

Este entorno funciona en Windows, y quitar los `platform:` de `docker-compose.yml` lo hizo de hecho más portable: ahora Docker elige por su cuenta la arquitectura del host, así que el mismo archivo sirve en un Mac con Apple Silicon y en un Windows x86. Hay dos cosas que conviene saber antes de empezar.

### Mantén el proyecto dentro del sistema de archivos de WSL2

La base de datos vive en `./data/`, montada dentro del contenedor. Cuando esa carpeta está en una ruta de Windows como `C:\...`, la E/S de disco de MariaDB tiene que cruzar del contenedor Linux a NTFS y se convierte en el cuello de botella: instalar WordPress y navegar por el administrador se vuelven notablemente lentos. Está registrado en el [issue #1](https://github.com/raulfg/docker-wordpress/issues/1).

La solución no está en el archivo compose. Coloca la carpeta del proyecto dentro del sistema de archivos de la propia distribución WSL2 —accesible como `\\wsl$\...` desde el Explorador— en lugar de bajo `C:\`. El mismo `docker compose up`, con una E/S mucho mejor.

Si necesitas el proyecto en una ruta de Windows y la lentitud es inaceptable, pasar `./data/` a un volumen nombrado de Docker elimina el cuello de botella. El precio es la portabilidad sobre la que está construido este proyecto: la base de datos dejaría de viajar dentro de la carpeta.

### Finales de línea

`.gitattributes` fuerza LF al hacer checkout en cualquier plataforma, y no deberías sobrescribirlo. Git for Windows hace checkout en CRLF por defecto, y un retorno de carro final acaba dentro del valor de cada variable del `.env`: `WP_PORT=80\r` no es un puerto válido, y el mensaje de error no te dirá por qué.

### Comandos

Este README usa `cp`, que no existe en PowerShell ni en CMD. Usa `copy`:

```powershell
copy env.sample .env
```

---

## 🌱 De Semilla a Proyecto de Cliente

Este repositorio es una **semilla**. Su función es darte el stack más reciente cada vez que empiezas un desarrollo nuevo, y por eso las imágenes usan etiquetas flotantes (`wordpress:latest`, `mariadb:11`) en lugar de versiones fijadas.

### Empezar un desarrollo nuevo: haz pull primero

Una etiqueta es solo un nombre, no una promesa de novedad. Docker la resuelve contra el registry **únicamente cuando la imagen no está ya en tu caché local**, así que `docker compose up` por sí solo seguirá sirviendo tranquilamente la imagen que te bajaste hace meses. Esa es la peor de las dos opciones: ni reproducible como una versión fijada, ni actualizada como pretendías.

Por eso, cuando arranques un desarrollo nuevo desde esta semilla, refresca de forma explícita:

```bash
docker compose pull && docker compose up -d
```

Comprueba lo que realmente obtuviste con `docker compose exec wordpress php -v` y con la versión de WordPress en el escritorio de administración.

La opción `pull_policy: always` automatizaría esto, y aquí **no** se usa de forma deliberada. Convierte cada `up` en una dependencia dura de red: con el registry inalcanzable, el entorno se niega a arrancar **incluso cuando la imagen ya está en la caché local**. Como este entorno está pensado para entregarse como una carpeta autocontenida que alguien arranca con un `docker compose up` a secas, posiblemente sin conexión, no arrancar es muchísimo peor que ejecutar una imagen algo más antigua. La novedad es una decisión explícita que tomas con `pull`, no un requisito para arrancar.

### Convertirse en proyecto de cliente: congélalo

Un proyecto de cliente quiere lo contrario que una semilla: el mismo entorno que ayer, todos los días, hasta que tú decidas otra cosa. **En el momento en que una copia de este repositorio contiene datos reales de un cliente, congela el entorno.** Fija las imágenes:

```yaml
db:
  image: mariadb:11.8
wordpress:
  image: wordpress:php8.3-apache
```

### Qué toca y qué no toca una actualización de imagen

*   **No toca el núcleo de WordPress.** El núcleo vive en `./www/`, y el entrypoint de la imagen solo lo copia cuando el directorio no contiene ningún WordPress. Sobre una instalación existente no se copia nada, no se sobrescribe nada y no se dispara ninguna actualización de base de datos. El sitio se queda en su versión exacta, y el cliente sigue actualizando WordPress desde su panel como siempre.
*   **Sí cambia el runtime de PHP que hay debajo.** Los archivos siguen idénticos pero empiezan a ejecutarse sobre otra versión de PHP, que es la forma habitual en la que muere un plugin antiguo. Fija por tanto la versión de PHP (`wordpress:php8.3-apache`) y no la de WordPress, que la imagen no controla.
*   **Sí cambia la base de datos.** Una actualización de minor de MariaDB migra el directorio de datos de `./data/`, y volver a un minor anterior no está soportado. Haz un `mysqldump` antes de cambiar la imagen de base de datos.

---

## 🙏 Agradecimientos

Un agradecimiento especial a Oscar García ([@opobla](https://github.com/opobla)) por darme a conocer el poder de Docker.

---

¡Disfruta de tu entorno de desarrollo local de WordPress portable y fácil de gestionar!

---
---

# WordPress Docker Local Development Environment (English)

This project provides a Docker Compose setup to run a WordPress local development environment easily and portably.

## ✨ Key Features

*   **Portable:** The entire environment, including WordPress files and the MariaDB database, is stored within the project folder (`./www/` for WordPress, `./data/` for the database). This makes it easy to create backups, share the environment, or move it between different machines.
*   **Isolated:** Uses Docker to ensure the development environment does not interfere with other local or system configurations.
*   **Easy to Use:** Start all services with a simple command (`docker compose up -d`) and stop them with (`docker compose down`).
*   **phpMyAdmin Included:** Easily access the database through phpMyAdmin for visual management.
*   **Pre-configured:** A ready-to-use WordPress environment with a basic setup.

## 📋 Prerequisites

Ensure you have installed:
*   [Docker](https://www.docker.com/get-started)
*   [Docker Compose](https://docs.docker.com/compose/install/) (usually included with Docker Desktop)

## 📁 Project Structure

*   `docker-compose.yml`: Main Docker Compose configuration file that defines the services (WordPress, MariaDB, phpMyAdmin).
*   `./data/`: Contains the MariaDB database files. This folder is created by Docker when starting the services and is ignored by Git thanks to the `.gitignore` file.
*   `./www/`: Contains WordPress core files, themes, plugins, and uploaded files. This folder is created by Docker and is also ignored by Git.
*   `./apache-config.conf`: Additional configuration file for Apache (used by WordPress and phpMyAdmin containers, e.g., for `ServerName`).
*   `env.sample`: Commented template of the variables you can override. It is versioned, and Docker Compose never reads it — copy it to `.env` to use it.
*   `.env`: Your own values, if you create one. Docker Compose reads it automatically. It is ignored by Git, but it does travel inside the project folder when you hand that folder over.
*   `.gitignore`: Specifies files and folders that Git should ignore (like `./data/`, `./www/` and `.env`).

## 🚀 Usage

1.  **Clone the Repository:**
    The simplest way is to clone the repository. This will create a new directory named `docker-wordpress` with the project files:
    ```bash
    git clone https://github.com/raulfg/docker-wordpress.git
    cd docker-wordpress
    ```

    **Alternative: Clone into a specific folder**
    If you prefer to name your project folder differently or clone into an existing directory:
    a. Create your desired project folder (e.g., `my-wordpress-project`) and navigate into it:
       ```bash
       mkdir my-wordpress-project
       cd my-wordpress-project
       ```
    b. Then, clone the repository content directly into this folder using a `.` at the end of the command:
       ```bash
       git clone https://github.com/raulfg/docker-wordpress.git .
       ```

    If you are not cloning, simply ensure you have `docker-compose.yml` and `apache-config.conf` in your working directory.

2.  **Start the Services:**
    Navigate to the project's root directory (where the `docker-compose.yml` file is located) and run:
    ```bash
    docker compose up -d
    ```
    This will download the necessary Docker images (if it's the first time) and create and start the containers in the background (`-d`).
    If you prefer to see the container logs directly in your terminal (foreground mode), you can omit the `-d` flag: `docker compose up`. To stop the services when running in foreground mode, press `Ctrl+C` in the terminal. Note that this only stops the containers; to remove them, you'll still need to run `docker compose down` afterwards.

3.  **Access the Services:**
    *   **WordPress:** Open your browser and go to `http://localhost` (or `http://localhost:80`).
        *   The first time, you will be guided through the WordPress installation process.
        *   The values below are the **defaults**. If you set any of them in a `.env` file, use yours instead — see [Variables you can override at startup](#variables-you-can-override-at-startup).
            *   Database Name: `wordpress`
            *   User: `wordpress`
            *   Password: `wordpress`
            *   Database Host: `db` (the name of the database service in `docker-compose.yml`)
            *   Table Prefix: `dockwp_`
    *   **phpMyAdmin:** Open your browser and go to `http://localhost:8080`.
        *   Server: `db`
        *   User: `root`
        *   Password: `password` (the default; it is whatever `DB_ROOT_PASSWORD` is set to)

4.  **Stop and Remove the Services:**
    To stop and remove all containers, networks, and volumes (not the locally mapped ones in `./data/` and `./www/`) associated with this project, run in the same directory:
    ```bash
    docker compose down
    ```
    This command is used whether you started the services with `docker compose up -d` (detached mode) or if you ran them in foreground mode and stopped them with `Ctrl+C`. It ensures a clean shutdown and removal of the Docker resources created by this project. The data in `./data/` (database) and `./www/` (WordPress files) will persist on your local system.

## 🔧 Customization

You can customize the configuration by editing the `docker-compose.yml` file:

*   **WordPress/MariaDB/phpMyAdmin Versions:** Change the image tags (e.g., `wordpress:latest`, `mariadb:11`, `phpmyadmin`).

### Variables you can override at startup

The ports, the table prefix and the database credentials are all parameterized, so you do not have to edit `docker-compose.yml` to change them. Every one of them falls back to a default, which is why a bare `docker compose up` always works with no setup at all:

| Variable | Default | What it controls |
| --- | --- | --- |
| `WP_PORT` | `80` | Port where WordPress is published |
| `PMA_PORT` | `8080` | Port where phpMyAdmin is published |
| `WP_PREFIX` | `dockwp_` | WordPress database table prefix |
| `DB_NAME` | `wordpress` | Database name |
| `DB_USER` | `wordpress` | Database user WordPress connects with |
| `DB_PASSWORD` | `wordpress` | Password for that user |
| `DB_ROOT_PASSWORD` | `password` | MariaDB root password, also used to log into phpMyAdmin |

Each credential is declared once and reused by every service that needs it, so the `db`, `wordpress` and `phpmyadmin` services can no longer drift apart. Changing the password is one edit, not three.

A commented template listing all of them lives in `env.sample`, in English and Spanish. Copy it to start from it:

```bash
cp env.sample .env
```

That file is versioned and harmless, because **Docker Compose never reads it**. Compose only picks up a file named exactly `.env`, so nothing you write in `env.sample` has any effect until you copy it. That is also why only `.env` is listed in `.gitignore`: the template is meant to be shared, your actual values are not.

**For a single run**, put them in front of the command:

```bash
WP_PORT=8081 PMA_PORT=8082 docker compose up -d
```

This is the usual fix when another project is already publishing port `80`.

**For a whole terminal session**, export them once:

```bash
export WP_PORT=8081
docker compose up -d
```

**Permanently, for one project**, create a `.env` file next to `docker-compose.yml`. Docker Compose reads it automatically, so whoever uses that folder keeps typing just `docker compose up`:

```ini
# .env
WP_PORT=8081
WP_PREFIX=acme7x_
```

That last option is the right one when you are preparing a folder for someone else: you set the values once, and they never have to pass a variable or edit a file.

The three methods combine, and the more specific one wins: a variable placed in front of the command overrides the same variable in `.env`, which in turn overrides the default. So you can keep a permanent `.env` for a project and still start it on a different port just once, without touching the file.

> ⚠️ **`WP_PREFIX` has to be decided before the first installation.** The prefix is not frozen into `wp-config.php` — that file reads the variable at runtime — but the database tables are physically named `<prefix>posts`, `<prefix>options` and so on, and the prefix is also embedded in keys such as `<prefix>user_roles` and `<prefix>capabilities`. Changing it on a site that is already installed points WordPress at tables that do not exist. Changing it afterwards means renaming every table and patching those keys by hand.

> ⚠️ **`WP_PORT` is best decided before installing too.** WordPress stores its own address in the database during installation, in the `siteurl` and `home` options. Install on port `80` and later start with `WP_PORT=8081`, and the site will keep redirecting you back to `http://localhost`. It is not broken, just pointing at the old address. Either pick the port before installing, or fix the two options afterwards:
>
> ```bash
> docker compose exec db mariadb -uwordpress -pwordpress wordpress \
>   -e "UPDATE dockwp_options SET option_value='http://localhost:8081' WHERE option_name IN ('siteurl','home');"
> ```

> ⚠️ **`DB_NAME`, `DB_USER`, `DB_PASSWORD` and `DB_ROOT_PASSWORD` also have to be decided before the first start.** MariaDB only reads them while it initializes an empty `./data/`. Once that directory exists, the database, the user and the passwords are already stored inside it, and changing the variables only changes what WordPress *tries* to use. The result is a site that answers `Error establishing a database connection` with HTTP 500, while the database still holds the original user. To change credentials on a running project, change them inside the database (or in phpMyAdmin) and keep the variables in sync, or start over from an empty `./data/`.

> ⚠️ **Coming from an older copy of this project?** Earlier versions used `mysql:5.7`. MariaDB cannot read a data directory created by MySQL, so an existing `./data/` will not start. Dump the database with the old stack first, then start from an empty `./data/` and import it back:
>
> ```bash
> docker compose exec db mysqldump -uroot -ppassword --all-databases > backup.sql
> ```

### Note on credentials and the `.env` file

The defaults are generic, memorable values, and that is deliberate: this is a local environment, and simple keys keep the startup trivial. They stay as defaults inside `docker-compose.yml` so that the environment always boots with no setup.

If you do set real credentials, put them in `.env`, never in `docker-compose.yml` — that way they stay out of the repository, because `.env` is listed in `.gitignore` while the compose file is versioned. Note that this keeps them out of Git, not out of the folder: `.env` still travels inside the project folder when you hand it over, which is exactly what makes a prepared folder work. It is not a secrets vault. If this environment is ever reachable from outside your own machine, treat every credential in it as public and change it.

---

## 🪟 Notes for Windows

This environment runs on Windows, and dropping the `platform:` overrides from `docker-compose.yml` actually made it more portable: Docker now picks the host architecture on its own, so the same file works on an Apple Silicon Mac and on an x86 Windows machine. Two things are worth knowing before you start.

### Keep the project inside the WSL2 filesystem

The database lives in `./data/`, bind-mounted into the container. When that folder sits on a Windows path such as `C:\...`, disk I/O for MariaDB has to cross from the Linux container to NTFS and becomes the bottleneck: installing WordPress and browsing the admin get noticeably slow. This is tracked in [issue #1](https://github.com/raulfg/docker-wordpress/issues/1).

The fix is not in the compose file. Put the project folder inside the WSL2 distribution's own filesystem — reachable as `\\wsl$\...` from Explorer — instead of under `C:\`. Same `docker compose up`, much better I/O.

If you need the project on a Windows path and the slowness is unacceptable, moving `./data/` to a Docker named volume removes the bottleneck. The cost is the portability this project is built around: the database would no longer travel inside the folder.

### Line endings

`.gitattributes` forces LF on checkout on every platform, and you should not override it. Git for Windows checks out CRLF by default, and a trailing carriage return ends up inside the value of every variable in `.env`: `WP_PORT=80\r` is not a valid port, and the error message will not tell you why.

### Commands

This README uses `cp`, which does not exist in PowerShell or CMD. Use `copy` instead:

```powershell
copy env.sample .env
```

---

## 🌱 From Seed to Client Project

This repository is a **seed**. Its job is to hand you the newest stack every time you start a new development, which is why the images use floating tags (`wordpress:latest`, `mariadb:11`) instead of pinned versions.

### Starting a new development: pull first

A tag is only a name, not a promise of freshness. Docker resolves a tag against the registry **only when the image is not already in your local cache**, so `docker compose up` on its own will happily keep serving the image you pulled months ago. That is the worst of both worlds: neither reproducible like a pinned version nor current like you intended.

So when you start a new development from this seed, refresh explicitly:

```bash
docker compose pull && docker compose up -d
```

Check what you actually got with `docker compose exec wordpress php -v` and the WordPress version in the admin dashboard.

The `pull_policy: always` option would automate this, and it is deliberately **not** used here. It turns every single `up` into a hard network dependency: with an unreachable registry the stack refuses to start **even when the image is already in the local cache**. Since this environment is meant to be handed over as a self-contained folder that someone starts with a bare `docker compose up`, possibly offline, failing to boot is far worse than running a slightly older image. Freshness is an explicit decision you make with `pull`, not a precondition for booting.

### Becoming a client project: freeze it

A client project wants the opposite of a seed: the same environment as yesterday, every day, until you decide otherwise. **The moment a copy of this repository holds real client data, freeze the environment.** Pin the images:

```yaml
db:
  image: mariadb:11.8
wordpress:
  image: wordpress:php8.3-apache
```

### What an image update does and does not touch

*   **It does not touch the WordPress core.** The core lives in `./www/`, and the image entrypoint copies it only when the directory contains no WordPress at all. On an existing installation nothing is copied, nothing is overwritten, and no database upgrade is triggered. The site stays on its exact version, and the client keeps updating WordPress from the admin panel as always.
*   **It does change the PHP runtime underneath.** The files stay identical but start running on a different PHP version, which is the usual way a legacy plugin dies. So pin for the PHP version (`wordpress:php8.3-apache`) rather than for the WordPress version, which the image does not control anyway.
*   **It does change the database.** A MariaDB minor upgrade migrates the data directory in `./data/`, and returning to an earlier minor is not supported. Take a `mysqldump` before changing the database image.

---

## 🙏 Acknowledgements

Special thanks to Oscar García ([@opobla](https://github.com/opobla)) for introducing me to the power of Docker.

---

Enjoy your portable and easy-to-manage local WordPress development environment!
