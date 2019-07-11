FROM ubuntu:16.04

WORKDIR /app

RUN apt-get update && apt-get install -y xz-utils
# RUN apk update && apk add xz

ADD https://nodejs.org/dist/v10.16.0/node-v10.16.0-linux-x64.tar.xz /app
RUN xz -d node-v10.16.0-linux-x64.tar.xz && \
 tar -C /usr --strip-components=1 -xf node-v10.16.0-linux-x64.tar && \
 rm node-v10.16.0-linux-x64.tar

COPY . /app
RUN npm install

CMD ["npm", "run", "serve"]



# docker exec -it <kontener> bash
