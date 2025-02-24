# Odoo Starter Docker #

## About ##

This is a simple starter configuration for running a self-hosted Odoo 18 using Docker.

## Features ##

 - Simplified installation process.
 - Database initialization scripts.
 - Customizable `res_users_data.xml` and `res_company_data.xml`.
 - Custom addons in `./odoo/extra-addons`.
 - PostGIS extenstion enabled by default.

## Setup ##

Create a network so both containers can talk to each other:

```
 docker network create odoo
```

Create a new environment by copying `env-example`:

```
 cp env-example .env
```

Adjust the values in the new file. This is an overview, make sure they make sense in your environment:

 - `POSTGRES_VERSION`: The `postgis` image tag to use.
 - `POSTGRES_CONTAINER_NAME`: The `postgres` container name. Connection from the `odoo` container must use this as the database host.
 - `POSTGRES_USER`: Default database admin user.
 - `POSTGRES_PASSWORD`: Admin user password.
 - `POSTGRES_DB`: The main database name.
 - `ODOO_VERSION`: The `odoo` image tag to use.
 - `ODOO_INIT_MODULES`: The list of modules to initialize (default: `base`).
 - `ODOO_USER`: The database user for Odoo database.
 - `ODOO_PASS`: The database password for Odoo database.
 - `ODOO_PORT`: The port to use by Odoo.

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

### Running Odoo ###

Once the process has finished you can start the server by running the following:

```
 docker compose up -d odoo
```

Open a new browser tab and navigate to [http://localhost:8069](http://localhost:8069). Login using the admin credentials (Default: `admin:starter`).
