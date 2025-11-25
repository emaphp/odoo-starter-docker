# Odoo Starter Docker #

## About ##

This is a simple starter configuration for running a self-hosted Odoo server using Docker.

## Features ##

 - Two step execution process.
 - `pgvector` extension enabled (allos *AI* and *vector search* features).
 - Additional database initialization scripts.
 - Customizable `res_users_data.xml` and `res_company_data.xml`.
 - Addons folder in `./odoo/extra-addons`.
 - Optional language and localization setup.
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
 - `ODOO_VERSION`: The `odoo` image/tag to use.
 - `ODOO_USER`: The database user for the Odoo database (default: `odoo`).
 - `ODOO_PASS`: The database password for the Odoo database (default: `pgsecret`).
 - `ODOO_INIT_MODULES`: The list of modules to initialize (default: `base`).
 - `ODOO_INIT_COUNTRY`: The ISO code of the country to use during database initialization. Only used by `odoo-init-db`.
 - `ODOO_INIT_LANG`: The language to load during setup. Only used by `odoo-init-modules`.
 - `ODOO_ADMIN_USERNAME`: The username for the main admin account. Only used by `odoo-init-db`.
 - `ODOO_ADMIN_PASSWORD`: The password for the main admin account. Only used by `odoo-init-db`.
 - `ODOO_PORT`: The port to expose for HTTP (default: `8069`).
 - `ODOO_RTC_PORT`: The port to expose for Real Time Communication (default: `8072`).

### Database ###

Start the database by running the following:

```
 docker compose up -d postgres
```

By default, database port is not exposed.

### Initialization ###

This repo offers two ways to setup your Odoo application. Both methods share the same Odoo configuration, which can be found under `odoo/config/odoo.conf`.

#### Fast Demo Setup ####

This initialization process is implemented as a single-run container:

```
 docker compose run --rm odoo-init-demo
```

This process will initialize the database with the demo data and activate the module list on `ODOO_INIT_MODULES`. Custom credentials can be applied by modifying `odoo/base/res_users_data.xml`. Company name can also be modified beforehand in `odoo/base/res_company_data.xml`.

#### Custom Setup ####

This is a two-step setup that generates a new database with custom admin credentials. Use this process if you want to customize the country and language from the start.

First, make sure you set the appropiate values for `ODOO_INIT_LANG` and `ODOO_INIT_COUNTRY` and run the following:

> Note: Warning. This will drop the Odoo database. Any extensions previously enabled must be re-enabled by hand.

```
 docker compose run --rm odoo-init-db
```

This will generate a new database from scratch. It will also create an admin account using the credentials available in `ODOO_ADMIN_USERNAME` and `ODOO_ADMIN_PASSWORD`.

The next process is to initialize the module list. Before this step you might want to provide additional localization modules. These need to be added to `ODOO_INIT_MODULES` (ex. `ODOO_INIT_MODULES=base,l10n_es`). Once ready, run the following:

```
 docker compose run --rm odoo-init-modules
```

### Running Odoo ###

Once the process has finished you can start the server by running the following:

```
 docker compose up -d odoo
```

Open a new browser tab and navigate to [http://localhost:8069](http://localhost:8069). Login using the admin credentials (Default: `admin:starter`).

### Running a shell ###

To start a Odoo Shell session run the following:

```
 docker compose run --rm -it odoo-shell
```

### Running psql ###

To start an instance of `psql` you can do the following:

```
 docker compose exec postgres /bin/psql -U pgadmin -d odoo -W
```

### PostGIS ###

This repo also includes an alternative database image based on PostGIS. A pre-defined composer file is also available. You only need to add the `-f` flag to the commands used previously:

```
 docker compose -f docker-compose.postgis.yml up -d postgres
 docker compose -f docker-compose.postgis.yml run --rm odoo-init-demo
 docker compose -f docker-compose.postgis.yml up -d odoo
```
