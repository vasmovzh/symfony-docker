# Dockerized Symfony
This is a complete stack for running Symfony application into [Docker](https://docs.docker.com/) container using [Docker Compose](https://docs.docker.com/compose/) tools

## Requirements
- Install [Docker](https://docs.docker.com/install/)
- Install [Docker Compose](https://docs.docker.com/compose/install/)

## Installation
1. First, clone this repository:
    ```bash
    $ git clone https://github.com/vasmovzh/symfony-docker.git
    # or
    $ git clone git@github.com:vasmovzh/symfony-docker.git
    ```
2. Then, place Your Symfony application into `app` folder
3. Configure domain in [app.conf](docker/nginx/app.conf) and don't forget to add this domain in your `hosts` file
    ```bash
    $ echo 127.0.0.1 specified.domain >> /etc/hosts
    ```
4. Next, create `.env` file from the existing `.env.dist` file, and adapt it according to application
    ```bash
    $ cp .env.dist .env
    ```
5. And configure specific variables `SF_APP_PATH` and `DOCKER_HOST_IP` with your values
6. Now build and run the containers
    ```bash
    $ docker-compose build
    $ docker-compose up -d
    ```
7. Then you could visit your application with your configured domain with `8080` port or just with `localhost:8080`. Database is available at port `5432`

## On the board
Have a look at the [docker-compose.yml](docker-compose.yml) file, here are the `docker-compose` built images:
- `db`: [postgres:12-alpine](https://hub.docker.com/_/postgres) container
- `php`: [php:7.3-fpm-alpine](https://hub.docker.com/_/php) container including the application volume mounted on
- `nginx`: [nginx:1.16-alpine](https://hub.docker.com/_/nginx) container including the application volume mounted on too

All images based on [alpine linux](https://alpinelinux.org/) with its [packages](https://pkgs.alpinelinux.org/packages)
## Use xdebug
Don't forget to configure your IDE with xdebug. For configuring PhpStorm see this [document](doc/xdebug/xdebug.md)
