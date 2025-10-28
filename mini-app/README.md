# MiniApp

## Objetivo
Demostrar control de versiones con Git, ciclo de vida de contenedores con Docker, integración de una mini aplicación en un contenedor reproducible y documentación técnica clara.

## Estructura

Miniapp
-Github\workflows
ci.yml

-app
 -public
   index.html
   style.css

 index.js
 package.json

-.gitignore
-docker-compose.yml
-Dockerfile
-README.md

## Requisitos
- Git
- Docker (Engine) en ejecución
- docker-compose (opcional)
- Cuenta en GitHub, GitLab o Bitbucket para el repositorio remoto

## Instrucciones rápidas
1. Clonar (reemplazar URL):
```bash
git clone <URL_REPO>
cd tp-docker-miniapp

2. Construir imagen:
docker build -t tp-docker-miniapp:1.0 .

3. Ejecutar contenedor (modo detach):
docker run -d --name tp-mini -p 3000:3000 tp-docker-miniapp:1.0

4. Ejecutar con docker-compose (alternativa):
docker-compose up --build -d

5. Verificar
Abrir en el navegador: http://localhost:3000
API: GET http://localhost:3000/api/hello
Health: GET http://localhost:3000/healthz

6. Detener y limpiar:
docker stop tp-mini
docker rm tp-mini
docker rmi tp-docker-miniapp:1.0   # opcional
docker-compose down

## Comandos usados (registro)

docker build -t tp-docker-miniapp:1.0 .
docker run -d --name tp-mini -p 3000:3000 tp-docker-miniapp:1.0
docker ps -a
docker logs -f tp-mini
docker-compose up --build -d
docker-compose down

## Explicación del Dockerfile (resumen técnico)

Base: node:18-alpine por ligereza y compatibilidad con Node.js..
Multi-stage build: instalar dependencias en un primer stage y copiar solo lo necesario al stage final para reducir tamaño.
Usuario no root: se crea un usuario de baja-privilegio para ejecutar la app y mejorar la seguridad.
ENV / EXPOSE / CMD: NODE_ENV=production, EXPOSE 3000, arranque con node index.js.
HEALTHCHECK: verifica /healthz periódicamente para detectar contenedores no funcionales.

Fragmento esencial del Dockerfile:

FROM node:18-alpine AS deps
WORKDIR /app
COPY app/package.json app/package-lock.json* ./
RUN npm ci --only=production

FROM node:18-alpine
WORKDIR /app
RUN addgroup -S appgroup && adduser -S appuser -G appgroup
COPY --from=deps /app/node_modules ./node_modules
COPY app ./app
WORKDIR /app/app
ENV NODE_ENV=production
EXPOSE 3000
HEALTHCHECK --interval=10s --timeout=3s --retries=3 CMD wget -qO- http://localhost:3000/healthz || exit 1
USER appuser
CMD ["node", "index.js"]


## FLujo Git mínimo

-Comandos para inicializar y subir:

git init
git add .
git commit -m "chore: initial project files (app, Dockerfile, compose, README)"
git remote add origin <URL_REMOTE>
git branch -M main
git push -u origin main


Autor: Brian Ayvar
Repositorio: https://github.com/brianayvar-prog/tp-miniapp.git