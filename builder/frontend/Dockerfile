FROM node:20.16.0-slim

RUN npm install -g http-server

WORKDIR /app
COPY . .

CMD ["http-server", ".", "-p", "8080"]

# WORKDIR /usr/src/app
# RUN chown -R node:node /usr/src/app