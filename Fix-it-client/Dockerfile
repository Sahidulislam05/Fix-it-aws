
# ---------- Stage 1: base ----------
FROM node:24-alpine AS base

# ---------- Stage 2: dependencies ----------
FROM base AS deps

RUN apk add --no-cache libc6-compat
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm ci

# ---------- Stage 3: build ----------
FROM base AS builder
WORKDIR /app
COPY --from=deps /app/node_modules ./node_modules
COPY . .

# build-time এ দরকার হলে public env var (client bundle এ যায়, তাই secret না)
ARG NEXT_PUBLIC_API_URL
ENV NEXT_PUBLIC_API_URL=$NEXT_PUBLIC_API_URL

ENV NEXT_TELEMETRY_DISABLED=1
RUN npm run build

# ---------- Stage 4: production runner ----------
FROM base AS runner
WORKDIR /app
ENV NODE_ENV=production
ENV NEXT_TELEMETRY_DISABLED=1

# root user হিসেবে না চালিয়ে আলাদা non-root user বানানো হলো (security best practice)
RUN addgroup -g 1001 -S nodejs \
    && adduser -S nextjs -u 1001 -G nodejs

COPY --from=builder /app/public ./public
COPY --from=builder --chown=nextjs:nodejs /app/.next/standalone ./
COPY --from=builder --chown=nextjs:nodejs /app/.next/static ./.next/static

USER nextjs

EXPOSE 3000
ENV PORT=3000
ENV HOSTNAME="0.0.0.0"

CMD ["node", "server.js"]
