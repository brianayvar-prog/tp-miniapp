# Stage 1: install deps
FROM node:18-alpine AS deps
WORKDIR /app
COPY app/package.json app/package-lock.json* ./
RUN npm ci --only=production

# Stage 2: final image
FROM node:18-alpine
WORKDIR /app

# Crear usuario no root
RUN addgroup -S appgroup && adduser -S appuser -G appgroup

# Copiar dependencias y código
COPY --from=deps /app/node_modules ./node_modules
COPY app ./app
WORKDIR /app/app

ENV NODE_ENV=production
EXPOSE 3000

HEALTHCHECK --interval=10s --timeout=3s --retries=3 CMD wget -qO- http://localhost:3000/healthz || exit 1

USER appuser
CMD ["node", "index.js"]
