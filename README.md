# Deionarra

> This is what my eyes see, my love, unfettered by the shackles of Time.

Ghost blog with SSL/TLS certificates from Let's Encrypt and a MySQL/MariaDB database, deployed using Docker Compose with named volumes for the Ghost and DB containers.

This is a simple Docker Compose configuration that will spin up containers running [Ghost][https://store.docker.com/images/ghost] and [MySQL][https://store.docker.com/images/mysql] (or [MariaDB][https://store.docker.com/images/mariadb]) using the official images from Docker.

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
      LETSENCRYPT_HOST: example.com
      LETSENCRYPT_EMAIL: webmaster@example.com
      database__connection__password: makeitstrong
  db:
    container_name: db
    environment:
      MYSQL_ROOT_PASSWORD: makeitstrong
```

To install and run Ghost on your server, you can then simply execute:

`docker-compose up -d`

## Updating

Updating Ghost (and the DBMS) when should be as simple as running

`docker-compose pull`

and then re-upping with

`docker-compose up -d`

(Ghost CLI does not work with this setup, but Docker seems to keep the Ghost image updated. The latest version is usually available as an image within a day of Ghost releasing it.)

## Lessons

1. Docker's MySQL and MariaDB containers do not like sharing a volume with Ghost, so I just have Compose create two volumes. It's probably better this way.

2. You can simply change the image of the `db` service from `mysql` to `mariadb` and everything appears to keep working, even if there is an existing database.

3. The value of `database__connection__host` can be either the name of the service (`db`), or the name of the image (`mimir`).

## Further down the rabbit hole

[Deionarra — Torment Wiki](http://torment.wikia.com/wiki/Deionarra)
