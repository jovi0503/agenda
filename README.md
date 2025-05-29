# Laravel com postgress



## Passo a passo para rodar o projeto
Clone o projeto

Crie o Arquivo .env
```sh
cp .env.example .env
```


parte do DB no .env
```dosini
DB_CONNECTION=pgsql
DB_HOST=postgres
DB_PORT=5432
DB_DATABASE=laravel
DB_USERNAME=username
DB_PASSWORD=root
```
# Parte do .yml

```yml
services:
    # image project
    app:
        build:
            context: .
            dockerfile: Dockerfile
        restart: unless-stopped
        working_dir: /var/www/
        volumes:
            - ./:/var/www
        depends_on:
            - redis
            - postgres
        networks:
            - laravel

    # nginx
    nginx:
        image: nginx:alpine
        restart: unless-stopped
        ports:
            - "8000:80"
        volumes:
            - ./:/var/www
            - ./docker/nginx/:/etc/nginx/conf.d/
        networks:
            - laravel

    # PostgreSQL
    postgres:
        image: postgres:16
        platform: linux/x86_64
        restart: unless-stopped
        environment:
            POSTGRES_DB: ${DB_DATABASE:-laravel}
            POSTGRES_USER: ${DB_USERNAME:-username}
            POSTGRES_PASSWORD: ${DB_PASSWORD:-root}
        volumes:
            - ./.docker/postgres/data:/var/lib/postgresql/data
        ports:
            - "5432:5432"
        networks:
            - laravel

    # pgAdmin
    pgadmin:
        image: dpage/pgadmin4
        platform: linux/x86_64
        restart: unless-stopped
        ports:
            - "5050:80"
        environment:
            PGADMIN_DEFAULT_EMAIL: admin@example.com
            PGADMIN_DEFAULT_PASSWORD: admin123
        networks:
            - laravel
        depends_on:
            - postgres
'''


Suba os containers do projeto
```sh
docker-compose up -d
```


Acesse o container
```sh
docker-compose exec app bash
```


Instale as dependências do projeto
```sh
composer install
```


Gere a key do projeto Laravel
```sh
php artisan key:generate
```


Acesse o projeto
[http://localhost:8989](http://localhost:8989)

Adicione libpq-dev \ no Dockerfile - Primeiro run
