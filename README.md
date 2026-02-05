
# Documentacion - examen

## configuracion en local 

# configuracion del docker-composer.local.yml
- he añadido estos datos, con la configuracion del .env
- 

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

- con el projecto levandado he probado una endpoint

- en la carpeta img, estan lo que se ha visto

 ## Configuracion en modo dev


- Primero parar el servico en local, que antes lo habias activado para 

- Configuracion del docker-compose.dev.yml

he añadido, la configuracion de postgre

- db-dev
- esta la configuracion de la base de datos
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

## Despliegue en render

