FROM node:20.16.0-slim

WORKDIR /app
COPY frontend/package.json frontend/package-lock.json ./
RUN rm -rf package-lock.json node_modules
RUN npm install
RUN npm install -g vite
COPY frontend ./
CMD ["sh", "-c", "vite --host --port ${PORT:-3000}"]