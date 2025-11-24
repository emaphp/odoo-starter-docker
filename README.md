# Odoo Starter Docker #

## About ##

This is a simple starter configuration for running a self-hosted Odoo server using Docker.

## Features ##

 - Two step execution process.
 - Using `pgvector` as default database (allos *AI* and *vector search* features).
 - Additional database initialization scripts.
 - Customizable `res_users_data.xml` and `res_company_data.xml`.
 - Addons folder in `./odoo/extra-addons`.
 - Optional language setup.
 - Version-specific branches.
 - Extras: Customized PostGIS database image.

## Setup ##

Create a network so containers can talk to each other:

```
 docker network create odoo_starter
```

Create a new environment by copying `env-example`:

```
 cp env-example .env
```

Adjust the values in the new file. Make sure they make sense in your environment. This is the full list of values:

 - `POSTGRES_VERSION`: The `pgvector` Docker image tag to use.
 - `POSTGRES_CONTAINER_NAME`: The `postgres` container name. Connection from the `odoo` container must use this as the database host.
 - `POSTGRES_USER`: Default database superadmin user.
 - `POSTGRES_PASSWORD`: Superadmin user password.
 - `POSTGRES_DB`: The main database name (default: `odoo`).
 - `ODOO_VERSION`: The `odoo` image tag to use.
 - `ODOO_USER`: The database user for Odoo database (default: `odoo`).
 - `ODOO_PASS`: The database password for Odoo database (default: `pgsecret`).
 - `ODOO_PORT`: The port to use by Odoo (default: `8069`).
 - `ODOO_INIT_MODULES`: The list of modules to initialize (default: `base`).
 - `ODOO_INIT_COUNTRY`: The ISO code of the country to load during setup. Only used by `odoo-init`.
 - `ODOO_INIT_LANG`: The language to load during setup. Only used by `odoo-init`.

### Database ###

Start the database by running the following:

```
 docker compose up -d postgres
```

By default, database port is not exposed.

### Initialization ###

The initialization process is implemented as a single-run container which you can run like this:

```
 docker compose run --rm odoo-init-demo
```

This will setup the demo database and initialize the `base` module. You'll find the default configuration file under `./odoo/config/odoo.conf`. You can modify `odoo/base/res_users_data.xml` to prevent using the default credentials for the `admin` user. The company name can also be modified beforehand in `odoo/base/res_company_data.xml`.

Alternatively, an `odoo-init` container is also included. This container will try to setup the localization language by adding the `--load-language` option. You can find the value for this option under `ODOO_INIT_LANG`. Keep in mind that for a full setup you might need to include additional localization modules, which need to be added to `ODOO_INIT_MODULES` (ex. `ODOO_INIT_MODULES=base,l10n_ES`). Running this container is basically the same:

```
 docker compose run --rm odoo-init
```

This container will not populate the database with the demo data.

### Running Odoo ###

Once the process has finished you can start the server by running the following:

```
 docker compose up -d odoo
```

Open a new browser tab and navigate to [http://localhost:8069](http://localhost:8069). Login using the admin credentials (Default: `admin:starter`).

### Running a shell ###

To run a Odoo Shell session run the following:

```
 docker compose run --rm -it odoo-shell
```

### PostGIS ###

This repo also includes an alternative database image based on PostGIS. A pre-defined composer file is also available. You only need to add the `-f` flag to the commands used previously:

```
 docker compose -f docker-compose.postgis.yml up -d postgres
 docker compose -f docker-compose.postgis.yml run --rm odoo-init
 docker compose -f docker-compose.postgis.yml up -d odoo
```
