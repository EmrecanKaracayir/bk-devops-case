# >-----< BASE STAGE >-----< #

FROM node:25-alpine AS base

ENV CI=true

# >-----< INSTALL STAGE >-----< #

FROM base AS installer

WORKDIR /app/

COPY \
    package-lock.json \
    package.json ./

RUN npm clean-install

# >-----< BUILD STAGE >-----< #

FROM base AS builder

WORKDIR /app/

COPY --from=installer /app/node_modules/ node_modules/
COPY db/ db/
COPY routes/ routes/
COPY loadEnvironment.mjs \
    server.mjs \
    package.json ./

# >-----< TEST STAGE >-----< #

FROM builder AS tester

RUN npm run test

# >-----< RUN STAGE >-----< #

FROM node:25-alpine AS runner

EXPOSE 5050

ENV NODE_ENV=production

USER node

WORKDIR /app/

COPY --from=builder /app/node_modules/ node_modules/
COPY --from=builder /app/db/ db/
COPY --from=builder /app/routes/ routes/
COPY --from=builder /app/loadEnvironment.mjs ./
COPY --from=builder /app/server.mjs ./

HEALTHCHECK --interval=30s --timeout=5s --retries=3 \
    CMD wget --no-verbose --tries=1 --spider http://localhost:5050/healthcheck || exit 1

CMD ["node", "server.mjs"]
