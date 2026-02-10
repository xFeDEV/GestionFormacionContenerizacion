# ---- Stage 1: Build ----
FROM node:22-alpine AS build

WORKDIR /app

# Copiar package.json y lock para caché de capas
COPY package.json package-lock.json ./

# Instalar dependencias
RUN npm ci

# Copiar el resto del código fuente
COPY . .

# Build arg para inyectar la URL del backend en build-time
ARG VITE_API_BASE_URL=http://localhost:8000
ENV VITE_API_BASE_URL=${VITE_API_BASE_URL}

# Compilar la aplicación
RUN npm run build

# ---- Stage 2: Serve con Nginx ----
FROM nginx:alpine

# Copiar configuración personalizada de Nginx
COPY nginx.conf /etc/nginx/conf.d/default.conf

# Copiar los archivos compilados desde el stage de build
COPY --from=build /app/dist /usr/share/nginx/html

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
