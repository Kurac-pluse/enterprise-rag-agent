# ==========================================
# 1. ビルドステージ (Build Stage)
# ==========================================
# Node.jsの公式軽量イメージを使用（Viteのビルド用）
FROM node:24-alpine AS build
WORKDIR /app

# キャッシュ活用のためにパッケージ定義ファイルを先にコピー
COPY frontend/package*.json ./
RUN npm ci

# ソースコードをコピーして本番用ビルドを実行
COPY frontend/ ./
RUN npm run build

# ==========================================
# 2. 実行・配信ステージ (Production Stage)
# ==========================================
# 本番配信用の軽量Webサーバー「Nginx」を採用
FROM nginx:stable-alpine

# ビルドステージで生成された「dist」フォルダ（静的ファイル群）をNginxの公開領域にコピー
COPY --from=build /app/dist /usr/share/nginx/html

# Nginxのデフォルトのポート80番を開放
EXPOSE 80

# Nginxをフォアグラウンドで起動
CMD ["nginx", "-g", "daemon off;"]
