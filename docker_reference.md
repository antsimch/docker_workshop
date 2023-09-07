# Docker Reference

## Docker build
```
docker build -t <DOCKER_USERNAME>/<IMAGE_NAME>:<TAG> .
```

## Docker images
```
docker images
```

## Docker image inspect
```
docker image inspect <IMAGE_ID>
```

## Docker run
### -e to add environment variables
```
docker run -d -p <EXTERNAL_PORT>:<APP_INTERNAL_PORT> --name <APP_NAME> <DOCKER_USERNAME>/<IMAGE_NAME>:<TAG>
```
```
docker container run -d -p <EXTERNAL_PORT1>:<APP_INTERNAL_PORT1> -p <EXTERNAL_PORT2>:<APP_INTERNAL_PORT2> --name <APP_NAME> <DOCKER_USERNAME>/<IMAGE_NAME>:<TAG>
```

## Docker container list (all containers)
```
docker container ls -a
```

## Docker container list (only running containers)
```
docker container ls
```

## Docker container stop
```
docker container stop <IMAGE_ID / IMAGE_NAME>
```

## Docker container start
```
docker container start <IMAGE_ID / IMAGE_NAME>
```

## Docker enter the container
```
docker container exec -ti <IMAGE_ID / IMAGE_NAME> /bin/sh
```

## Docker exit the container
```
exit
```

## Docker kill container
```
docker container rm -f <IMAGE_ID / IMAGE_NAME>
```

## Pushing to docker
```
docker login
docker push <DOCKER_USERNAME>/<IMAGE_NAME>:<TAG>
```

## Docker logs
```
docker container logs <IMAGE_ID / IMAGE_NAME>
```

## Docker image push to github
github > settings > developer settings > personal access tokens > tokens(classic)

```
docker login ghcr.io -u<GH_USERNAME> -p<GH_TOKEN>

docker TAG <DOCKER_USERNAME>/<IMAGE_NAME>:<TAG> ghcr.io/<GH_USERNAME>/<IMAGE_NAME>:<TAG>

docker push ghcr.io/<GH_USERNAME>/<IMAGE_NAME>:<TAG>
```

## Docker caddy server from angular
### Caddy command
```
caddy file-server --listen: <PORT> --root .
```

## Angular Dockerfile
```
FROM node:20 AS angbuilder

WORKDIR /app

COPY angular.json .
COPY package.json .
COPY package-lock.json .
COPY tsconfig.json .
COPY tsconfig.app.json .
COPY src src

RUN npm i -g @angular/cli
RUN npm ci
RUN ng build

FROM caddy:2-alpine

WORKDIR /app

COPY --from=angbuilder /app/dist/<APP_NAME> <APP_NAME>

ENV PORT=8080

EXPOSE ${PORT}

ENTRYPOINT caddy file-server --listen: ${PORT} --root /app/<APP_NAME>
```

## Springboot Dockerfile
```
FROM maven:3-eclipse-temurin-20 AS builder

WORKDIR /app

COPY mvnw .
COPY pom.xml .
COPY src src

RUN mvn package -Dmaven.test.skip=true

FROM openjdk:20-slim

WORKDIR /app

COPY --from=builder /app/target/<JAR_FILE> <APP.JAR>

ENV PORT=3000

EXPOSE ${PORT}

ENTRYPOINT java -jar <APP.JAR>
```

## Springboot + Angular Dockerfile for same origin
```
FROM node:20 AS angbuilder

WORKDIR /app

COPY angular.json .
COPY package.json .
COPY package-lock.json .
COPY tsconfig.json .
COPY tsconfig.app.json .
COPY src src

RUN npm i -g @angular/cli
RUN npm ci
RUN ng build

FROM maven:3-eclipse-temurin-20 AS mvnbuilder

WORKDIR /app

COPY <server_dir>/src .
COPY <server_dir>/mvnw .
COPY <server_dir>/pom.xml .
COPY --from=angbuilder /app/dist/<APP_NAME> /app/src/main/resources/static/

RUN mvn clean package -Dmaven.test.skip=true

FROM openjdk:20-slim

WORKDIR /app

COPY --from=mvnbuilder /app/target/<JAR_FILE> <APP.JAR>

ENV PORT=3000

EXPOSE ${PORT}

ENTRYPOINT SERVER_PORT=${PORT} java -jar /app/<APP.JAR>

```