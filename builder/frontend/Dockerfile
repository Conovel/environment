FROM node:20.16.0-slim

WORKDIR /app
COPY frontend/package.json ./
RUN npm install
COPY . .
CMD ["npm", "start"]

# WORKDIR /usr/src/app
# RUN chown -R node:node /usr/src/app