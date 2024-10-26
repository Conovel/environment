FROM node:20.16.0-slim

WORKDIR /app
COPY frontend/package.json ./
RUN npm install
RUN npm install -g vite
CMD ["sh", "-c", "vite --host --port ${PORT:-3000}"]
