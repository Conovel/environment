FROM node:20.16.0-slim

WORKDIR /app

COPY frontend/package.json frontend/package-lock.json ./

# package-lock.jsonとnode_modulesを削除
RUN rm -rf package-lock.json node_modules

# npm installを再実行
RUN npm install

# Viteをグローバルにインストール
RUN npm install -g vite

COPY frontend ./

# 環境変数PORTを使用してViteを起動
CMD ["sh", "-c", "vite --host --port ${PORT:-3000}"]

# WORKDIR /usr/src/app
# RUN chown -R node:node /usr/src/app