# Stage 1: Build & Compile TypeScript
FROM node:20-alpine AS builder
WORKDIR /app

COPY package*.json ./
RUN npm ci

COPY . .
RUN npm run build
RUN npm prune --production

# Stage 2: Production Execution Layer
FROM node:20-alpine AS runner
WORKDIR /app

RUN apk update && apk upgrade --no-cache

RUN addgroup -S appgroup && adduser -S appuser -G appgroup

COPY --from=builder /app/package*.json ./
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/dist ./dist

# npm itself is never invoked at runtime (CMD calls node directly) — remove
# it and its bundled deps, which is where every CVE from our scan lived.
RUN rm -rf /usr/local/lib/node_modules/npm \
    /usr/local/bin/npm /usr/local/bin/npx /usr/local/bin/corepack

USER appuser
EXPOSE 3000

ENV NODE_ENV=production
ENV PORT=3000

CMD ["node", "dist/index.js"]