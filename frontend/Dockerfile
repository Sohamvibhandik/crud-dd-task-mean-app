# frontend/Dockerfile
# 1) Builder stage: build Angular app
FROM node:18-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
# build into dist/angular-15-crud
RUN npm run build -- --configuration=production

# 2) Serve with nginx
FROM nginx:stable-alpine
# copy built files to nginx html dir (note the dist subfolder name)
COPY --from=builder /app/dist/angular-15-crud /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
