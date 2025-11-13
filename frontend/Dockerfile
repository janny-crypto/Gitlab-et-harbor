# Build de l'app React
FROM node:18 AS build
WORKDIR /app
COPY src/ .
RUN npm install
RUN npm run build

# Serveur web statique
FROM harbor.host:8086/monprojet/nginx:alpine
COPY --from=build /app/build /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
