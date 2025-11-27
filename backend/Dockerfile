# backend/Dockerfile
FROM node:18-alpine

# Create app directory
WORKDIR /app

# Copy package.json & package-lock if exists first, install dependencies
COPY package*.json ./

# Use npm ci if package-lock.json exists; fallback to npm install
RUN if [ -f package-lock.json ]; then npm ci --only=production; else npm install --only=production; fi

# Copy app source
COPY . .

# Expose port that server.js listens on
EXPOSE 8080

# Use environment variable NODE_ENV
ENV NODE_ENV=production

# Start the app
CMD ["node", "server.js"]
