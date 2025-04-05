FROM node:18
WORKDIR /app
COPY package*.json ./
RUN npm install -g @angular/cli && npm install
COPY . .
RUN ng build --configuration=production
FROM nginx:alpine
COPY --from=0 /app/dist/frontend-app /usr/share/nginx/html
