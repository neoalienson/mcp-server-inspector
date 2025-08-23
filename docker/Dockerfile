# Multi-stage build for minimal image size
FROM node:22-alpine AS builder

# Create app directory
WORKDIR /app

# Copy package files for all components
COPY package*.json ./
COPY server/package*.json ./server/
COPY client/package*.json ./client/

# Install dependencies for all components, skipping prepare scripts
RUN npm install --ignore-scripts

# Copy source code for server and client (skip cli as requested)
COPY server/ ./server/
COPY client/ ./client/

# Build server
RUN cd server && npm run build

# Build client
RUN cd client && npm run build

# Production stage
FROM node:22-alpine

# Create app directory
WORKDIR /app

# Copy package files for production
COPY package*.json ./
COPY server/package*.json ./server/
COPY client/package*.json ./client/

# Install production dependencies only, skipping prepare scripts
RUN npm install --only=production --ignore-scripts

# Copy built assets from builder stage
COPY --from=builder /app/client/dist ./client/dist
COPY --from=builder /app/server/build ./server/build

# Copy necessary runtime files
COPY client/bin ./client/bin

# Expose port 6274 for the inspector UI
EXPOSE 6274

# Set default command to start the inspector, binding to all interfaces
ENTRYPOINT ["sh", "-c", "HOST=0.0.0.0 npm start"]
