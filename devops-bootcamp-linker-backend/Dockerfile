# --- Build Stage ---
# Use Node 20 Alpine image for building the app
FROM node:20-alpine AS builder

# Set working directory inside the container
WORKDIR /app

# Install build tools required for some Node modules and Prisma
RUN apk add --no-cache python3 build-base openssl

# Copy dependency definitions
COPY package*.json ./

# Install all dependencies (dev + prod) for build
RUN npm ci

# Copy all source code into the container
COPY . .

# Generate Prisma client for database access at runtime
RUN npx prisma generate

# Build the NestJS backend (output goes to /dist)
RUN npm run build

# --- Production Stage ---
# Use small Node 20 Alpine image for running the app
FROM node:20-alpine AS runner

# Set environment variables for production
ENV NODE_ENV=production \
    PORT=3001

# Set working directory
WORKDIR /app

# Install runtime dependencies only
RUN apk add --no-cache openssl curl netcat-openbsd

# Copy package files for installing production-only dependencies
COPY --from=builder --chown=node:node /app/package*.json ./

# Install only production dependencies
RUN npm ci --only=production && npm cache clean --force

# Copy built code, node_modules, and Prisma client from builder
COPY --from=builder --chown=node:node /app/dist ./dist
COPY --from=builder --chown=node:node /app/node_modules ./node_modules
COPY --from=builder --chown=node:node /app/prisma ./prisma/

# Copy entrypoint script and make it executable
COPY --from=builder --chown=node:node /app/entrypoint-script.sh ./entrypoint-script.sh
RUN chmod +x ./entrypoint-script.sh

# Run container as non-root user
USER node

# Expose port for the backend
EXPOSE 3001

# Command to start the application
CMD ["./entrypoint-script.sh"]