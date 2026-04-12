# -------- Build Stage -------
FROM node:20-alpine AS build

WORKDIR /app

COPY package.json package-lock.json* .

RUN npm ci --only=production && npm clean cache --force

# --------- Prod stage -------

FROM node:20-alpine AS production

RUN addgroup -S appgroup && adduser -S appuser -G approup

RUN apk --no-cache add dumb-init

WORKDIR /app

COPY --from=build /app/node-modules ./node-modules

COPY src/ ./src/

# Security: own files by non-root user
RUN chown -R appuser:appgroup /app

USER appuser

EXPOSE 5000

# Security: use dumb-init for proper signal handling (PID 1 problem)
ENTRYPOINT ["dumb-init", "--"]
CMD ["node", "src/index.js"]

