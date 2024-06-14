# Products Launcher

## Cómo levantar el proyecto en desarrollo

1. Clonar el repositorio
2. Crear un .env basado en el .env.template
3. Ejecutar el comando `git submodule update --init --recursive`
4. Ejecutar el comando `docker compose up --build`

## Pasos para crear los Git Submodules

1. Crear un nuevo repositorio en GitHub
2. Clonar el repositorio en la máquina local
3. Añadir el submodule, donde `repository_url` es la url del repositorio y `directory_name` es el nombre de la carpeta donde quieres que se guarde el sub-módulo (no debe de existir en el proyecto)

```
git submodule add <repository_url> <directory_name>
```

4. Añadir los cambios al repositorio (git add, git commit, git push)
   Ej:

```
git add .
git commit -m "Add submodule"
git push
```

## Inicializar y actualizar Sub-módulos

Cuando alguien clona el repositorio por primera vez, debe de ejecutar el siguiente comando para inicializar y actualizar los sub-módulos

```
git submodule update --init --recursive
```

6. Para actualizar las referencias de los sub-módulos

```
git submodule update --remote
```

## Importante

Si se trabaja en el repositorio que tiene los sub-módulos, **primero actualizar y hacer push** en el sub-módulo y **después** en el repositorio principal.

Si se hace al revés, se perderán las referencias de los sub-módulos en el repositorio principal y tendremos que resolver conflictos.

## Cómo construir imágenes de docker para producción

1. Se necesita que cada microservicio tenga un archivo `dockerfile.prod` configurado previamente.

2. Luego es necesario configurar un archivo `docker-compose.prod.yml` como se muestra a continuación:

```
services:
  nats-server:
    image: nats:latest
    ports:
      - "8222:8222"

  client-gateway:
    build:
      context: ./client-gateway
      dockerfile: dockerfile.prod
    image: client-gateway-prod
    ports:
      - 3000:3000
    environment:
      - PORT=3000
      - NATS_SERVERS=nats://nats-server:4222
```

Es importante indicar la carpeta que en este caso es `./client-gateway` y luego el dockerfile que será `dockerfile.prod`

3. Seguidamente ejecutamos el siguiente comando estando ubicados donde se encuentra el archivo `docker-compose.prod.yml`

```
docker compose -f docker-compose.prod.yml build
```

**-f** Indica que el archivo a tomar será `docker-compose.prod.yml`

4. Una vez que se construye la imagen podemos usar el siguiente comando para levantar la red de contenedores

```
docker compose -f docker-compose.prod.yml up
```
