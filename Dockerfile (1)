# ── DeployBoard — Dockerfile ─────────────────────────────────────────────────
# Works on Google Cloud Shell, GCP VMs, and any Linux VPS.
# Does NOT require package-lock.json (uses npm install, not npm ci).

FROM node:20-alpine

# Install git (for cloning repos in local/docker build mode)
RUN apk add --no-cache git wget

WORKDIR /app

# Copy package.json first (layer caching — only re-installs when dependencies change)
COPY package*.json ./
RUN npm install --only=production --no-audit && npm cache clean --force

# Copy all application files
COPY . .

# Create directories the app needs, with correct ownership
RUN addgroup -g 1001 -S deployboard && \
    adduser  -u 1001 -S deployboard -G deployboard && \
    mkdir -p /var/www/user-sites /tmp/deployboard-builds && \
    chown -R deployboard:deployboard /app /var/www/user-sites /tmp/deployboard-builds && \
    chmod 755 /var/www/user-sites /tmp/deployboard-builds

USER deployboard

EXPOSE 3001

# Health check — wget is installed above so this works on Alpine
HEALTHCHECK --interval=30s --timeout=5s --start-period=15s --retries=3 \
  CMD wget -qO- http://localhost:3001/api/health || exit 1

CMD ["node", "server.js"]
