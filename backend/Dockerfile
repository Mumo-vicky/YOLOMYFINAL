# Install dependencies
FROM node:18-alpine AS deps

WORKDIR /usr/src/app

COPY package*.json ./

# Only install build tools if needed 
RUN if [ -f "package-lock.json" ]; then \
      apk add --no-cache python3 make g++; \
    fi
RUN npm ci --only=production 

# Copying source and build
FROM node:18-alpine AS builder
WORKDIR /usr/src/app
COPY --from=deps /usr/src/app/node_modules ./node_modules

# Copying only necessary files for production
COPY models ./models
COPY routes ./routes
COPY server.js .
COPY upload.js .

FROM node:18-alpine
WORKDIR /usr/src/app

COPY --from=deps /usr/src/app/node_modules ./node_modules

# Copy app files
COPY --from=builder /usr/src/app/models ./models
COPY --from=builder /usr/src/app/routes ./routes
COPY --from=builder /usr/src/app/server.js .
COPY --from=builder /usr/src/app/upload.js .

# Creating the upload directory
RUN mkdir -p ./public/images

ENV PORT=5000 \
    MONGODB_URI=mongodb://mongodb/yolomy \
    NODE_ENV=production \
    UPLOAD_DIR=/usr/src/app/public/images

EXPOSE 5000
CMD ["node", "server.js"]