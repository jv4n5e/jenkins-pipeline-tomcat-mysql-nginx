FROM ubuntu:latest

LABEL version="1.0"
LABEL maintainer="juanse18_7@hotmail.com"

RUN apt-get update && apt-get upgrade -y
RUN apt-get install nginx -y

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]