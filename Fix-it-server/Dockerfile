# ---------- Stage 1: base ----------
FROM node:24-alpine AS base
WORKDIR /app

# ---------- Stage 2: dependencies (dev সহ, কারণ prisma CLI ও tsc build এর জন্য লাগবে) ----------
FROM base AS deps
COPY package.json package-lock.json ./
RUN npm ci

# ---------- Stage 3: build ----------
FROM base AS builder
COPY --from=deps /app/node_modules ./node_modules
COPY . .
RUN npx prisma generate
RUN npm run build

# ---------- Stage 4: production runner ----------
FROM base AS runner
ENV NODE_ENV=production

RUN addgroup -g 1001 -S nodejs \
    && adduser -S expressjs -u 1001 -G nodejs

COPY --from=builder --chown=expressjs:nodejs /app ./

USER expressjs

EXPOSE 5000
ENV PORT=5000


CMD ["sh", "-c", "npx prisma migrate deploy && node dist/server.js"]
