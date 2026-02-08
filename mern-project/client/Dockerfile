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
COPY public/ public/
COPY src/ src/
COPY package.json ./

RUN npm run build

# >-----< TEST STAGE >-----< #

FROM builder AS tester

RUN npm run test

# >-----< RUN STAGE >-----< #

FROM nginx:alpine-slim AS runner

COPY --from=builder /app/build/ /usr/share/nginx/html/
COPY server/nginx.conf /etc/nginx/conf.d/default.conf

HEALTHCHECK --interval=30s --timeout=5s --retries=3 \
    CMD wget --no-verbose --tries=1 --spider http://localhost/health || exit 1
