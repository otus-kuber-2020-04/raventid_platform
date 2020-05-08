ARG NGINX_VERSION
FROM nginx:${NGINX_VERSION:-latest}
COPY default.conf /etc/nginx/conf.d/
COPY nginx.conf /etc/nginx/
WORKDIR /app
EXPOSE 8000
USER 1001
CMD ["nginx", "-g", "daemon off;"]
