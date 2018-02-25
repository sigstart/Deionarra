# Deionarra

> This is what my eyes see, my Love, unfettered by the shackles of Time.

[Ghost](https://ghost.org) blog with SSL/TLS certificates from Let's Encrypt and a MySQL/MariaDB database, deployed using Docker Compose with named volumes for the Ghost and DB containers.

This is a simple Docker Compose configuration that will spin up containers running [Ghost](https://store.docker.com/images/ghost) and [MySQL](https://store.docker.com/images/mysql) (or [MariaDB](https://store.docker.com/images/mariadb)) using the official images from Docker.

It then uses Jason Wilder's (@jwilder) docker-gen for nginx to create a reverse proxy, along with the Let's Encrypt companion container from JrCs to enable TLS.

## Setup

Download the latest `nginx.tmpl` file from @jwilder's nginx-proxy project so that docker-gen can do its thing.

This should be re-run whenever you `docker-compose pull` to update.

```bash
curl https://raw.githubusercontent.com/jwilder/nginx-proxy/master/nginx.tmpl > nginx.tmpl
```

You can use it as-is for a local development environment, or create a `docker-compose.override.yml` file, overriding the following variables with your own values (or edit the variables in the main `docker-compose.yml` file):

```yaml
version: '3.1'

services:
  ghost:
    container_name: ghost
    ports:
      - 80:2368
    environment:
      url: https://example.com
      VIRTUAL_HOST: example.com
      LETSENCRYPT_HOST: example.com
      LETSENCRYPT_EMAIL: webmaster@example.com
      database__connection__password: makeitstrong
  db:
    container_name: db
    environment:
      MYSQL_ROOT_PASSWORD: makeitstrong
```

If you want to use Let's Encrypt's staging area rather than its live API, you can add the following snippet to the above overrides.

```yaml
letsencrypt-companion:
  container_name: letsencrypt-nginx-proxy-companion
  environment:
    ACME_CA_URI: https://acme-staging.api.letsencrypt.org/directory
```

To install and run Ghost on your server, you can then simply execute:

`docker-compose up -d`

## Updating

Updating Ghost (and the DBMS) should be as simple as running

`docker-compose pull`

Remember to download the latest version of the nginx-proxy template after every update.

`curl https://raw.githubusercontent.com/jwilder/nginx-proxy/master/nginx.tmpl > nginx.tmpl`

After both commands have been executed, you can re-up the project:

`docker-compose up -d`

(Ghost CLI does not work with this setup, but Docker seems to keep the Ghost image updated. The latest version is usually available as an image within a day of Ghost releasing it.)

## Lessons

1. Docker's MySQL and MariaDB containers do not like sharing a volume with Ghost, so I just have Compose create two volumes. It's probably better this way.

2. You can simply change the image of the `db` service from `mysql` to `mariadb` and everything appears to keep working, even if there is an existing database.

3. The value of `database__connection__host` can be either the name of the service (`db`), or the name of the image (`mimir`).

## Further down the rabbit hole

[Deionarra — Torment Wiki](http://torment.wikia.com/wiki/Deionarra)

## Acknowledgements

This project builds on the good work of several others before it, including:

 - [Web Proxy using Docker, NGINX and Let's Encrypt](https://github.com/evertramos/docker-compose-letsencrypt-nginx-proxy-companion) by @evertramos

 Which, in turn, builds on:

  - [nginx-proxy](https://github.com/jwilder/nginx-proxy) and [docker-gen](https://index.docker.io/u/jwilder/docker-gen/) by @jwilder
  - [docker-letsencrypt-nginx-proxy-companion](https://github.com/JrCs/docker-letsencrypt-nginx-proxy-companion) by @JrCs

The very early work of [Adrian Perez](https://adrianperez.org/advanced-deployment-of-ghost-in-2-minutes-with-docker/) using a similar approach, but with named volumes, deserves an honourable mention.
