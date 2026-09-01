# ---------- Stage 1: base ----------
FROM node:24-alpine AS base

WORKDIR /app

<<<<<<< HEAD
=======

>>>>>>> 89e573972518c12b0a7d9f0b9de2e1eb304f323f
# ---------- Stage 2: dependencies ----------
FROM base AS deps

COPY package.json package-lock.json ./
<<<<<<< HEAD
RUN npm ci --ignore-scripts
=======

# postinstall (prisma generate) এখন চালাবে না
RUN npm ci --ignore-scripts

>>>>>>> 89e573972518c12b0a7d9f0b9de2e1eb304f323f

# ---------- Stage 3: build ----------
FROM base AS builder

COPY --from=deps /app/node_modules ./node_modules

COPY . .

# এখন prisma/schema.prisma already আছে
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

<<<<<<< HEAD
CMD ["sh", "-c", "npx prisma migrate deploy && node dist/src/server.js"]
=======
CMD ["sh", "-c", "npx prisma migrate deploy && node dist/src/server.js"]
>>>>>>> 89e573972518c12b0a7d9f0b9de2e1eb304f323f
