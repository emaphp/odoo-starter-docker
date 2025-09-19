# Odoo Starter Docker #

 > Since Odoo 19 is already approaching, this setup will start including `pgvector/pgvector` as default database image. PostGIS will be kept as an alternative though.

## About ##

This is a simple starter configuration for running a self-hosted Odoo 18 using Docker.

## Features ##

 - Simplified installation process.
 - Database initialization scripts.
 - Customizable `res_users_data.xml` and `res_company_data.xml`.
 - Custom addons in `./odoo/extra-addons`.
 - PostGIS extension enabled by default.
 - Additional language setup.

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

 - `POSTGRES_VERSION`: The `postgis` Docker image tag to use.
 - `POSTGRES_CONTAINER_NAME`: The `postgres` container name. Connection from the `odoo` container must use this as the database host.
 - `POSTGRES_USER`: Default database superadmin user.
 - `POSTGRES_PASSWORD`: Superadmin user password.
 - `POSTGRES_DB`: The main database name (default: `odoo`).
 - `ODOO_VERSION`: The `odoo` image tag to use.
 - `ODOO_USER`: The database user for Odoo database (default: `odoo`).
 - `ODOO_PASS`: The database password for Odoo database (default: `pgsecret`).
 - `ODOO_PORT`: The port to use by Odoo (default: `8069`).
 - `ODOO_INIT_MODULES`: The list of modules to initialize (default: `base`).
 - `ODOO_INIT_LANG`: The language to load during setup. Only used by `odoo-init-lang`.

### Database ###

Start the database by running the following:

```
 docker compose up -d postgres
```

By default, database port is not exposed.

### Initialization ###

The initialization process runs inside a container. A default configuration file located in `./odoo/config/odoo.conf` is used.

```
 docker compose run --rm odoo-init
```

This will setup the database and initialize the `base` module. You can modify `odoo/base/res_users_data.xml` to prevent using the default credentials for the `admin` user. The company name can also be modified beforehand in `odoo/base/res_company_data.xml`.

Alternatively, an `odoo-init-lang` container is also included. This process is similar to `odoo-init` but it will also try to setup the installation language by adding the `--load-language` option. The language to use must be specified in `ODOO_INIT_LANG`. This container also overrides the original language data with `odoo/base/res_lang_data.xml`.

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
