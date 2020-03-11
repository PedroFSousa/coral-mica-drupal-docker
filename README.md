# Mica Drupal Docker

This repository contains the source code for building a [Drupal Mica](https://www.obiba.org/pages/products/mica/) Docker image based on the [original Mica Drupal image from OBiBa](https://github.com/obiba/docker-mica-drupal), modified to:
* download all the necessary dependencies at build time;
* support the use of Docker secrets for passwords;
* allow the disabling of anonymous access to variable statistics;
* set a custom site name;
* add a custom credit to the footer;
* automatically add and enable custom modules;
* replace any files in `/var/www/html/sites` with customised ones;
* run a custom `.sql` script on the Mica Drupal database.

## Building
After every commit, GitLab CI automatically builds the image and pushes it into INESC TEC's Docker Registry. The version tag of the image is determined from a string matching `##x.x.x##` on the commit message.

Alternatively, an image can be built locally by running:
`docker build -t mica-drupal-docker .`

## Setting Up
**Environment Variables**  
The following environment variables are available:

* `DRUPAL_ADMINISTRATOR_PASSWORD` is the password for the administrator account of the Drupal site;
* `MYSQL_PORT_3306_TCP_ADDR` is the address of the MySQL server where the Drupal data will be stored;
* `MYSQL_DATABASE` is the name of the Drupal database;
* `MYSQL_ROOT_PASSWORD` is the password for the MySQL root account of the MySQL server;
* `BASE_URL` is the base URL for the Drupal site;
* `SITE_NAME` is the name of the Drupal site;
* `FOOTER_CREDIT` is an HTML string that will be added to the footer of the Drupal site;
* `ALLOW_ANONYMOUS_STATS` if set to `no`, will disable access to pages with variable statistics for unauthenticated users;
* `MICA_PORT_8445_TCP_ADDR` and `MICA_PORT_8445_TCP_PORT` are the address and port of the Mica server;
* `MICA_ANONYMOUS_PASSWORD` is the password Drupal will use to query Mica using the `anonymous` account;
* `AGATE_PORT_8444_TCP_ADDR` and `AGATE_PORT_8444_TCP_PORT` are the address and port of the Agate server;

If you are using Docker Swarm, you can instead set the following variables to enable Docker secrets:
* `DRUPAL_ADMINISTRATOR_PASSWORD_FILE`
* `MYSQLDRUPAL_ROOT_PASSWORD_FILE`

**Volumes**  
Volumes can be mounted to different parts of the container:
* `/modules`: custom Drupal modules placed here will be copied to `/var/www/html/sites/` and enabled;
* `/sites`: this directory will be synchronised with `/var/www/html/sites` (overwriting files in the destination directory);
* `/customize.sql`: commands in this script will be executed in the Mica Drupal database when the container starts for the first time.
* `/customize.sh`: commands in this script will be executed when the container starts for the first time.

## Running

Create a `docker-compose.yml` file (examples bellow) and run `docker-compuse up`.

**Basic Example:**
```yml
version: '3.2'

services:
  mica-drupal:
    image: docker-registry.inesctec.pt/coral/coral-docker-images/coral-mica-drupal-docker:1.1.0
    container_name: mica-drupal
    environment:
    - DRUPAL_ADMINISTRATOR_PASSWORD=<INSERT_VALUE_HERE>
    - MYSQL_PORT_3306_TCP_ADDR=<INSERT_VALUE_HERE>
    - MYSQL_DATABASE=<INSERT_VALUE_HERE>
    - MYSQL_ROOT_PASSWORD=<INSERT_VALUE_HERE>
    - BASE_URL=<INSERT_VALUE_HERE>
    - SITE_NAME=<INSERT_VALUE_HERE>
    - FOOTER_CREDIT=<INSERT_VALUE_HERE>
    - ALLOW_ANONYMOUS_STATS=<INSERT_VALUE_HERE>
    - MICA_PORT_8445_TCP_ADDR=<INSERT_VALUE_HERE>
    - MICA_PORT_8445_TCP_PORT=<INSERT_VALUE_HERE>
    - MICA_ANONYMOUS_PASSWORD=<INSERT_VALUE_HERE>
    - AGATE_PORT_8444_TCP_ADDR=<INSERT_VALUE_HERE>
    - AGATE_PORT_8444_TCP_PORT=<INSERT_VALUE_HERE>
    volumes:
    - <PATH_TO_CUSTOM_MODULES>:/modules
    - <PATH_TO_MODIFIED_SITES_DIR>:/sites
    - <PATH_TO_SQL_SCRIPT>:/customize.sql
```
**Docker Swarm Example:**
```yml
version: '3.2'

services:
  mica-drupal:
    image: docker-registry.inesctec.pt/coral/coral-docker-images/coral-mica-drupal-docker:1.1.0
    container_name: mica-drupal
    environment:
    - DRUPAL_ADMINISTRATOR_PASSWORD_FILE=/run/secrets/DRUPAL_ADMINISTRATOR_PASSWORD
    - MYSQL_PORT_3306_TCP_ADDR=<INSERT_VALUE_HERE>
    - MYSQL_DATABASE=<INSERT_VALUE_HERE>
    - MYSQL_ROOT_PASSWORD_FILE=/run/secrets/MYSQL_ROOT_PASSWORD_FILE
    - BASE_URL=<INSERT_VALUE_HERE>
    - SITE_NAME=<INSERT_VALUE_HERE>
    - FOOTER_CREDIT=<INSERT_VALUE_HERE>
    - ALLOW_ANONYMOUS_STATS=<INSERT_VALUE_HERE>
    - MICA_PORT_8445_TCP_ADDR=<INSERT_VALUE_HERE>
    - MICA_PORT_8445_TCP_PORT=<INSERT_VALUE_HERE>
    - MICA_ANONYMOUS_PASSWORD=<INSERT_VALUE_HERE>
    - AGATE_PORT_8444_TCP_ADDR=<INSERT_VALUE_HERE>
    - AGATE_PORT_8444_TCP_PORT=<INSERT_VALUE_HERE>
    secrets:
    - DRUPAL_ADMINISTRATOR_PASSWORD
    - MYSQL_ROOT_PASSWORD
    volumes:
    - <PATH_TO_CUSTOM_MODULES>:/modules
    - <PATH_TO_MODIFIED_SITES_DIR>:/sites
    - <PATH_TO_SQL_SCRIPT>:/customize.sql

secrets:
  DRUPAL_ADMINISTRATOR_PASSWORD:
    external: true
  MYSQL_ROOT_PASSWORD:
    external: true
```

---
master: [![pipeline status](https://gitlab.inesctec.pt/coral/coral-docker-images/coral-mica-drupal-docker/badges/master/pipeline.svg)](https://gitlab.inesctec.pt/coral/coral-docker-images/coral-mica-drupal-docker/commits/master)

dev: &emsp;&ensp;[![pipeline status](https://gitlab.inesctec.pt/coral/coral-docker-images/coral-mica-drupal-docker/badges/dev/pipeline.svg)](https://gitlab.inesctec.pt/coral/coral-docker-images/coral-mica-drupal-docker/commits/dev)
