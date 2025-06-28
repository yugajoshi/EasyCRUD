FROM node:24-alpine
COPY . /opt/
WORKDIR /opt
RUN npm install
RUN npm run build
RUN apk update && apk add apache2
RUN rm -f /var/www/localhost/htdocs/*
RUN cp -r dist/* /var/www/localhost/htdocs/
EXPOSE 80
CMD ["/usr/sbin/httpd", "-D", "FOREGROUND"]
