FROM nginx:alpine

# Supprime la config par défaut
RUN rm -rf /usr/share/nginx/html/*

# Copie ton HTML + JS
COPY src/ /usr/share/nginx/html/

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
