FROM node:18

WORKDIR /app

COPY package.json package-lock.json ./
RUN npm install
RUN npm install -g pm2

COPY . .

# Database credentials
ENV DB_HOST=mysql-db
ENV DB_USERNAME=admin
ENV DB_PASSWORD=Devops123
ENV DB_PORT=3306

EXPOSE 80

CMD ["pm2-runtime", "start", "index.js", "--name", "backendapi"]
