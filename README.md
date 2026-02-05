
# Documentacion - examen - alex1

# configuracion en local 

## configuracion del docker-composer.local.yml
- he añadido estos datos, con la configuracion del .env
- He usado los datos que tengo en el .evn, pero cambiando a pgsql

```
services:
  db:
    image: postgres:16
    container_name: postgres_example
    restart: always
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: 1234
      POSTGRES_DB: order_app
    ports:
      - "5432:5432"

```

- Luego he levando el contenedor en docker

- Con el projecto levandado, he probado un endpoint

- en la carpeta img-local, estan lo que se ha visto

 # Configuracion en modo dev


- Primero parar el servico en local, que antes lo habias activado

- Configuracion del docker-compose.dev.yml

- he añadido, la configuracion de postgre

- db-dev
- esta la configuracion de la base de datos
- repitiendo los mismo de antes
- app_dev
- Configuracion de la app de laravel
- he utilizado el dockerfile, en el contexto ya que tiene los comando para las migraciones
- las variables de abajo las he nombreado a como he creado el contenedor de arriba
- 
```
services:

  db-dev:
    image: postgres:16
    container_name: postgres_dev
    restart: always
    environment:
      POSTGRES_DB: order_app
      POSTGRES_USER: user
      POSTGRES_PASSWORD: 1234
    ports:
      - "5432:5432"


  app-dev:
    build:
      context: .
      dockerfile: Dockerfile
    container_name: laravel_dev
    depends_on:
      - db-dev
    ports:
      - "8080:8000"
    environment:
      #AQUI CAMBIAMOS HOST POR EL NOMBRE DEL CONTENEDOR DE LA BASE DE DATOS POSTGRES_DEV
      DB_CONNECTION: pgsql
      DB_HOST: db-dev
      DB_PORT: 5432
      DB_DATABASE: order_app
      DB_USERNAME: user
      DB_PASSWORD: 1234
      APP_KEY: ${APP_KEY}
      APP_ENV: local
      APP_DEBUG: "true"

```

## En la carpeta img-dev, estan las imagenes comprobadas


- Los contenedores se mantiene
- Puedo ver el endpoint desde la url
- He comprobado que en la base de datos al inciar, se hace las migraciones
- y inserta los datos
`

# Despliegue en render
## img-render estan las imagenes

- Como en modo dev habia utilizado el Dockerfile. con comando preparado para que ejecute las mnigraciones
```
# COGEMOS LA IMAGEN DE PHP
FROM php:8.2-fpm

# INSTALAMOS LAS DEPENDENCIAS
RUN apt-get update && apt-get install -y \
    git curl zip unzip libpng-dev libonig-dev libxml2-dev libzip-dev libicu-dev libpq-dev \
    nodejs npm \
    && docker-php-ext-install pdo pdo_pgsql mbstring zip exif pcntl gd intl \
    && apt-get clean && rm -rf /var/lib/apt/lists/* # 2. Instalar Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# DIRECTORIO DE TRABAJO
WORKDIR /var/www

# AQUÍ COPIAMOS ARCHIVOS
COPY . .

# DEPENDENCIAS DE PHP Y NODE QUE ES LO QUE UTILIZAMOS
RUN composer install --optimize-autoloader --no-interaction
RUN npm install && npm run build

# PERMISOS PARA EVITAR ERRORES FUTUROS CON EL TEMA PERMISOS
RUN chown -R www-data:www-data /var/www/storage /var/www/bootstrap/cache

# PUERTO
EXPOSE 8002

# COMANDO FINAL ; Esperamos 10 segundos, migra con datos de prueba y arranca
CMD sh -c "sleep 10 && php artisan migrate:fresh --seed --force && php artisan serve --host=0.0.0.0 --port=8002"

```

- Crear la base de datos en la nube con el servicio de render
- Usar los datos de la base de datos 
- He rellenado las variables de entorno con los datos de la base de datos
- APP_KEy importante

- Una vez desplegado en Render, he usado esta url para comprobar que ha funcionado
  https://app-pizza-daw-nzsg.onrender.com/api/pizza
- El resultado se puede ver en la carpeta de img-render



# Despligue en MySQL Y Render

## En la carpeta img-MySQL se puede ver los resultados
- Para esta configuracion he creado la base de datos en skysql
- Luego me he conectado a vsCode, para ver si ha funcionado
- Las variables de entorno, las he cambiando a las de la nueva base de datos

- No ha desplegado en mariadb, por problemas de drivers


