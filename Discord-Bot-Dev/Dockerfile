# syntax=docker/dockerfile:1
FROM node:20-slim

# Enable corepack and install pnpm
RUN corepack enable && corepack prepare pnpm@10.26.1 --activate

WORKDIR /app

# Copy workspace manifest files first (better layer caching)
COPY package.json pnpm-workspace.yaml pnpm-lock.yaml tsconfig.base.json ./

# Copy all source packages
COPY lib/ ./lib/
COPY artifacts/api-server/ ./artifacts/api-server/
COPY artifacts/dashboard/ ./artifacts/dashboard/

# Install all dependencies (frozen = exact lockfile, no re-resolution)
RUN pnpm install --frozen-lockfile

# Build the dashboard as static files (served by Express in production)
# BASE_PATH=/ means all assets are rooted at /
RUN BASE_PATH=/ PORT=8080 NODE_ENV=production pnpm --filter @workspace/dashboard run build

# Build the API server + bot bundle
RUN pnpm --filter @workspace/api-server run build

EXPOSE 8080

ENV PORT=8080
ENV NODE_ENV=production

CMD ["node", "--enable-source-maps", "artifacts/api-server/dist/index.mjs"]
