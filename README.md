# 🛍️ Practica Docker Final – TesloShop

Proyecto de práctica para implementación de aplicaciones con Docker, Docker Compose y buenas prácticas de contenedorización.

---

## 🏗️ Arquitectura

\`\`\`
┌─────────────────────────────────────────────────┐
│                   teslo-net                     │
│                                                 │
│  ┌─────────────┐     ┌─────────────┐            │
│  │   Frontend  │────▶│   Backend   │            │
│  │   Angular   │     │   NestJS    │            │
│  │  Puerto 80  │     │ Puerto 3000 │            │
│  └─────────────┘     └──────┬──────┘            │
│                             │                   │
│                      ┌──────▼──────┐            │
│                      │  Database   │            │
│                      │ PostgreSQL  │            │
│                      │ Puerto 5432 │            │
│                      └─────────────┘            │
└─────────────────────────────────────────────────┘
🧩 Servicios
🅰️ Frontend – Angular 19

Framework: Angular 19 con TailwindCSS y DaisyUI
Imagen: Multi-stage build con Nginx Alpine
Puerto: 80
Contenedor: `teslo-frontend`
⚙️ Backend – NestJS

Framework: NestJS con TypeORM
Base de datos: PostgreSQL via `pg`
Autenticación: JWT + Passport
Documentación: Swagger en `/api`
Puerto: 3000
Contenedor: `teslo-backend`

🐘 Base de datos – PostgreSQL 15

Imagen: `postgres:15-alpine`
Puerto: 5432
Volumen persistente: `postgres-data`
Contenedor: `teslo-db`


📁 Estructura del proyecto
```
practica-docker-final/
├── teslo-shop/                 # Backend NestJS
│   ├── src/
│   │   ├── auth/
│   │   ├── products/
│   │   ├── files/
│   │   ├── seed/
│   │   └── main.ts
│   ├── Dockerfile
│   └── package.json
├── angular-tesloshop/          # Frontend Angular
│   ├── src/
│   │   ├── app/
│   │   └── main.ts
│   ├── Dockerfile
│   └── package.json
├── docker-compose.yml
├── .env
└── README.md
```

⚙️ Variables de entorno
Crea un archivo `.env` en la raíz del proyecto con las siguientes variables:
```env
POSTGRES_USER=postgres
POSTGRES_PASSWORD=MySecretPassword123
POSTGRES_DB=TesloDB
POSTGRES_PUBLISH_PORT=5432
POSTGRES_CONTAINER_NAME=teslo-db
STAGE=dev
DB_HOST=db
DB_PORT=5432
DB_USERNAME=postgres
DB_PASSWORD=MySecretPassword123
DB_NAME=TesloDB
PORT=3000
BACKEND_PUBLISH_PORT=3000
BACKEND_CONTAINER_NAME=teslo-backend
HOST_API=http://localhost:3000/api
JWT_SECRET=MiJwtSecretSuperLargoYAleatorio123456
FRONTEND_PUBLISH_PORT=80
FRONTEND_CONTAINER_NAME=teslo-frontend
```

🚀 Pasos de ejecución
Requisitos previos

Docker Desktop instalado
Git instalado

1. Clonar el repositorio
```bash
git clone https://github.com/Juahzx002/practica-docker-final.git
cd practica-docker-final
```
2. Crear el archivo de variables de entorno
```bash
cp .env.template .env
Editar .env con tus valores
```
3. Construir y levantar los servicios
```bash
docker compose up --build -d
```
4. Verificar que los contenedores estén corriendo
```bash
docker compose ps
```
5. Ejecutar el seed (datos de prueba)
```bash
curl http://localhost:3000/api/seed
```
6. Acceder a la aplicación
ServicioURL🅰️ Frontendhttp://localhost:80⚙️ API Backendhttp://localhost:3000/api📖 Swagger Docshttp://localhost:3000/api/docs

🐳 Dockerfiles
Backend – Multi-stage build
```dockerfile
Stage 1: Builder
FROM node:18-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm install --legacy-peer-deps
COPY . .
RUN npm run build
Stage 2: Production
FROM node:18-alpine AS production
WORKDIR /app
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/dist ./dist
COPY package*.json ./
EXPOSE 3000
CMD ["node", "dist/main"]
```
Frontend – Multi-stage build con Nginx
```dockerfile
Stage 1: Builder
FROM node:18-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build
Stage 2: Production
FROM nginx:alpine AS production
COPY --from=builder /app/dist/teslo-shop/browser /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

🛑 Comandos útiles
```bash
Ver logs de un servicio
docker compose logs backend --tail=20
Detener todos los servicios
docker compose down
Detener y eliminar volúmenes
docker compose down -v
Reconstruir un servicio específico
docker compose up --build backend -d
Entrar a la base de datos
docker exec -it teslo-db psql -U postgres -d TesloDB
```

👨‍💻 Tecnologías utilizadas
Tecnología     | Versión | Uso 
Docker         |  Latest | Contenedorización
Docker Compose |    v2   | Orquestación
NestJS         |    8.x  | Backend API REST
Angular        |    19   | Frontend SPA
PostgreSQL     |    15   | Base de datos
TypeORM        |   0.3.x | ORM
Nginx          | Alpine  | Servidor web frontend
Node.js       | 18 Alpine |Runtime backend
READMEEOF