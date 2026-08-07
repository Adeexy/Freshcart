# Stage 1: Compile Vite Assets
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

# Stage 2: Serve Web Traffic Securely via Non-Root Nginx
FROM nginxinc/nginx-unprivileged:alpine-slim

# --- UPDATE THIS LINE TO MATCH YOUR NEW FILE NAME ---
# Inside your storefront/Dockerfile (Stage 2)
COPY nginx.conf /etc/nginx/conf.d/default.conf

COPY --from=builder /app/dist /usr/share/nginx/html

EXPOSE 8080
CMD ["nginx", "-g", "daemon off;"]
