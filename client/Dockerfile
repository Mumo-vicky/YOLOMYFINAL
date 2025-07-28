# Stage 1: Build
FROM node:18-alpine AS builder
WORKDIR /app

# 1. Install dependencies
COPY package.json package-lock.json ./
RUN npm ci --omit=dev

# 2. Build with OpenSSL legacy provider
COPY . .
ENV NODE_OPTIONS="--openssl-legacy-provider"
RUN npm run build

# Stage 2: Production
FROM nginx:1.25-alpine-slim
RUN chown -R nginx:nginx /usr/share/nginx/html && \
    chmod -R 755 /usr/share/nginx/html
COPY --from=builder /app/build /usr/share/nginx/html
COPY nginx.conf /etc/nginx/conf.d/default.conf
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]