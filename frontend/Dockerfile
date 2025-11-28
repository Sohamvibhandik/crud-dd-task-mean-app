# frontend/Dockerfile
FROM node:18 AS build
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build --configuration=production

FROM nginx:stable-alpine
# copy built site
COPY --from=build /app/dist/angular-15-crud /usr/share/nginx/html
# copy custom nginx config (overrides default)
COPY nginx.conf /etc/nginx/conf.d/default.conf
